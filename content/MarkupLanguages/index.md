---
title: "Markup Languages"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

## HTML

### ConvertFrom-Html

``` powershell
function ConvertFrom-Html {
    param (
        [Parameter(ValueFromPipeline)]$url
    )
    $irm = Invoke-RestMethod $url
    $HTMLFile = New-Object -ComObject HTMLFile
    $Bytes = [System.Text.Encoding]::Unicode.GetBytes($irm)
    $HTMLFile.write($Bytes)
    ($HTMLFile.all | where {$_.tagname -eq "body"}).innerText
}

$apache_status = "http://192.168.3.102/server-status"
$apache_status | ConvertFrom-Html
```

### ConvertTo-Html

`Get-Process | select Name, CPU | ConvertTo-Html -As Table > "$home\desktop\proc-table.html"`
вывод в формате List (Format-List) или Table (Format-Table)

``` powershell
$servers = "ya.ru","ya.com","google.com"
$path = "$home\Desktop\Ping.html" 
$header = @"
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" lang="en" xml:lang="en">
<head>
<title>Отчет о статусе серверов</title>
<style type="text/css">
<!--
body {
background-color: #E0E0E0;
font-family: sans-serif
}
table, th, td {
background-color: white;
border-collapse:collapse;
border: 1px solid black;
padding: 5px
}
-->
</style>
"@
$body = @"
<h1>Ping status</h1>
<p>$(get-date -Format "dd.MM.yyyy hh:mm").</p>
"@
$results = foreach ($server in $servers) { 
    if (Test-Connection $server -Count 1 -ea 0 -Quiet) { 
        $status = "Up" 
    }
    else { 
        $status = "Down"
    }
    [PSCustomObject]@{
        Name = $server
        Status = $status
    }
}
$results | ConvertTo-Html -head $header -body $body | foreach {
    $_ -replace "<td>Down</td>","<td style='background-color:#FF8080'>Down</td>" -replace "<td>Up</td>","<td style='background-color:#5BCCF3'>Up</td>"
} | Out-File $path
Invoke-Item $path
```

### PSWriteHTML

``` powershell
Import-Module PSWriteHTML
(Get-Module PSWriteHTML).ExportedCommands
Get-Service | Out-GridHtml -FilePath ~\Desktop\Get-Service-Out-GridHtml.html
```

### HtmlReport

``` powershell
Import-Module HtmlReport
$topVM = ps | Sort PrivateMemorySize -Descending | Select -First 10 | %{,@(($_.ProcessName + " " + $_.Id), $_.PrivateMemorySize)}
$topCPU = ps | Sort CPU -Descending | Select -First 10 | %{,@(($_.ProcessName + " " + $_.Id), $_.CPU)}
New-Report -Title "Piggy Processes" -Input {
New-Chart Bar "Top VM Users" -input $topVm
New-Chart Column "Top CPU Overall" -input $topCPU
ps | Select ProcessName, Id, CPU, WorkingSet, *MemorySize | New-Table "All Processes"
} > ~\Desktop\Get-Process-HtmlReport.html
```

### HtmlAgilityPack

[Source](https://www.nuget.org/packages/HtmlAgilityPack)

``` powershell
# Загрузка библиотеки C#, которая позволяет парсить HTML-документы, выбирать элементы DOM и извлекать из них данные
Add-Type -Path "C:\Users\Lifailon\Downloads\HtmlAgilityPack\Net40\HtmlAgilityPack.dll"
$title = "новобранец"
$url = "http://fasts-torrent.net"
$ep = "engine/ajax/search_torrent.php?title=$title"
$html = Invoke-RestMethod "$url/$ep"
# Создание нового объекта HtmlDocument из HtmlAgilityPack, который будет использоваться для загрузки и обработки HTML-кода
$HtmlDocument = New-Object HtmlAgilityPack.HtmlDocument
# Загрузка HTML в созданный объект HtmlDocument
$HtmlDocument.LoadHtml($html)
$torrents = @()
# Использование XPath для выбора всех элементов <tr> (строк таблицы) в документе
$HtmlDocument.DocumentNode.SelectNodes("//tr") | ForEach-Object {
    # Для каждой строки таблицы выбираем классы, соответствующие названию раздачи, размеру и ссылке для скачивания.
    $titleNode = $_.SelectSingleNode(".//td[@class='torrent-title']")
    $sizeNode = $_.SelectSingleNode(".//td[@class='torrent-sp']")
    $downloadLinkNode = $_.SelectSingleNode(".//td[@class='torrent-d-btn']/a")
    # Проверяем, что все классы найдены
    if ($titleNode -ne $null -and $sizeNode -ne $null -and $downloadLinkNode -ne $null) {
        # Извлечение текста из классов
        $title = $titleNode.InnerText.Trim()
        $size = $sizeNode.InnerText.Trim()
        $downloadLink = $downloadLinkNode.Attributes["href"].Value
        $torrent = New-Object PSObject -Property @{
            Title = $title
            Size = $size
            DownloadLink = "$($url)$($downloadLink)"
        }
        $torrents += $torrent
    }
}
```

`$torrents`

``` powershell
Title                                                                    Size     DownloadLink
-----                                                                    ----     ------------
Новобранец (6 сезон: 1-3 серии из 13) (2024) WEBRip | RuDub              1,55 ГБ  http://fasts-torrent.net/download/449613/torrent/-6-1-3-13-2024-webrip-rudub/
Новобранец (5 сезон: 1-22 серии из 22) (2023) WEBRip 1080p | RuDub       54,15 ГБ http://fasts-torrent.net/download/433749/torrent/-5-1-22-22-2023-webrip-1080p-rudub/
Новобранец (5 сезон: 1-22 серии из 22) (2023) WEBRip 720p | RuDub        30,14 ГБ http://fasts-torrent.net/download/433750/torrent/-5-1-22-22-2023-webrip-720p-rudub/
Новобранец (5 сезон: 1-22 серии из 22) (2023) WEBRip | RuDub             11,55 ГБ http://fasts-torrent.net/download/433751/torrent/-5-1-22-22-2023-webrip-rudub/
Новобранец (4 сезон: 1-22 серии из 22) (2021) WEB-DL 720p | RG.Paravozik 21.33 Gb http://fasts-torrent.net/download/418618/torrent/-4-1-22-22-2021-web-dl-720p-rgparavozik/
Полицейский с половиной: Новобранец (2017) WEB-DLRip 720p| Чистый звук   3.41 Gb  http://fasts-torrent.net/download/254846/torrent/-2017-web-dlrip-720p-/
Полицейский с половиной: Новобранец (2017) WEB-DLRip | Чистый звук       1.37 Gb  http://fasts-torrent.net/download/254845/torrent/-2017-web-dlrip-/
Новобранец (2 сезон: 1-20 серии из 20) (2019) WEBRip | BaibaKo           11.28 Gb http://fasts-torrent.net/download/364669/torrent/-2-1-20-20-2019-webrip-baibako/
Новобранец (2 сезон: 1-20 серии из 20) (2019) WEBRip 1080p | Octopus     45.97 Gb http://fasts-torrent.net/download/364161/torrent/-2-1-20-20-2019-webrip-1080p-octopus/
Новобранец (2 сезон: 1-20 серии из 20) (2019) WEB-DLRip | LostFilm       11.95 Gb http://fasts-torrent.net/download/364668/torrent/-2-1-20-20-2019-web-dlrip-lostfilm/
```

## Markdown

### Convert Markdown to HTML

``` powershell
function ConvertFrom-MarkdownToHtml {
    param (
        [Parameter(Mandatory = $true,ValueFromPipeline = $true)]$Markdown
    )
    $html = $(Get-Content index.md -Raw | ConvertFrom-Markdown).html
    @"
    <!DOCTYPE html>
    <html>
    <head>
    </head>
    <body>
    $html
    </body>
    </html>
    "@
}
```

`Get-Content index.md -Raw | ConvertFrom-MarkdownToHtml | Out-File index.html`

### PSMarkdown

Install-Module PSMarkdown

### ConvertFrom-MarkdownV2

``` powershell
Function ConvertFrom-MarkdownV2 {
    [CmdletBinding()]
    [OutputType([PSObject])]
    Param (
        [Parameter(
            Mandatory = $true,
            Position = 0,
            ValueFromPipeline = $true
        )]
        $InputObject
    )
    Begin {
        $items = @()
    }
    Process {
        $mddata = $InputObject
        $data = $mddata | Where-Object {$_ -notmatch "--" }
        $items += $data
    }
    End {
       $object = $items -replace ' +', '' | ConvertFrom-Csv -Delimiter '|'
       $object 
    }
}
```

### ConvertTo-Markdown

``` powershell
Function ConvertTo-Markdown {
    [CmdletBinding()]
    [OutputType([string])]
    Param (
        [Parameter(
            Mandatory = $true,
            Position = 0,
            ValueFromPipeline = $true
        )]
        [PSObject[]]$InputObject
    )
    Begin {
        $items = @()
        $columns = @{}
    }
    Process {
        ForEach($item in $InputObject) {
            $items += $item
            $item.PSObject.Properties | ForEach-Object {
                if($null -ne $_.Value) {
                    if(-not $columns.ContainsKey($_.Name) -or $columns[$_.Name] -lt $_.Value.ToString().Length) {
                        $columns[$_.Name] = $_.Value.ToString().Length
                    }
                }
            }
        }
    }
    End {
        ForEach($key in $($columns.Keys)) {
            $columns[$key] = [Math]::Max($columns[$key], $key.Length)
        }
        $header = @()
        ForEach($key in $columns.Keys) {
            $header += ('{0,-' + $columns[$key] + '}') -f $key
        }
        $header -join ' | '
        $separator = @()
        ForEach($key in $columns.Keys) {
            $separator += '-' * $columns[$key]
        }
        $separator -join ' | '
        ForEach($item in $items) {
            $values = @()
            ForEach($key in $columns.Keys) {
                $values += ('{0,-' + $columns[$key] + '}') -f $item.($key)
            }
            $values -join ' | '
        }
    }
}
```

## XML

``` powershell
$xml = [xml](Get-Content $home\desktop\test.rdg)` прочитать содержимое XML-файла
$xml.load("$home\desktop\test.rdg")` открыть файл
$xml.RDCMan.file.group.properties.name` имена групп
$xml.RDCMan.file.group.server.properties` имена всех серверов
$xml.RDCMan.file.group[3].server.properties` список серверов в 4-й группе
($xml.RDCMan.file.group[3].server.properties | ? name -like ADIRK).Name = "New-Name"` изменить значение
$xml.RDCMan.file.group[3].server[0].properties.displayName = "New-displayName" 
$xml.RDCMan.file.group[3].server[1].RemoveAll()` удалить объект (2-й сервер в списке)
$xml.Save($file)` сохранить содержимое объекта в файла
```

`Get-Service | Export-Clixml -path $home\desktop\test.xml`
экспортировать объект PowerShell в XML\
`Import-Clixml -Path $home\desktop\test.xml` импортировать объект XML в
PowerShell\
`ConvertTo-Xml (Get-Service)`

### Get-CredToXML

``` powershell
function Get-CredToXML {
    param (
        $CredFile = "$home\Documents\cred.xml"
    )
    if (Test-Path $CredFile) {
        Import-Clixml -path $CredFile
    }
    elseif (!(Test-Path $CredFile)) {
        $Cred = Get-Credential -Message "Enter credential"
        if ($Cred -ne $null) {
        $Cred | Export-CliXml -Path $CredFile
        $Cred
    }
    else {
        return
    }
    }
}
```

`$Cred = Get-CredToXML`\
`$Login = $Cred.UserName`\
`$PasswordText = $Cred.GetNetworkCredential().password` получить пароль
в текстовом виде

### XmlWriter (Extensible Markup Language)

``` powershell
$XmlWriterSettings = New-Object System.Xml.XmlWriterSettings
$XmlWriterSettings.Indent = $true` включить отступы
$XmlWriterSettings.IndentChars = "    "` задать отступ

$XmlFilePath = "$home\desktop\test.xml"
$XmlObjectWriter = [System.XML.XmlWriter]::Create($XmlFilePath, $XmlWriterSettings)` создать документ
$XmlObjectWriter.WriteStartDocument()` начать запись в документ

$XmlObjectWriter.WriteComment("Comment")
$XmlObjectWriter.WriteStartElement("Root")` создать стартовый элемент, который содержит дочерние объекты
    $XmlObjectWriter.WriteStartElement("Configuration")` создать первый дочерний элемент для BaseSettings
        $XmlObjectWriter.WriteElementString("Language","RU")
        $XmlObjectWriter.WriteStartElement("Fonts")         # <Fonts>
            $XmlObjectWriter.WriteElementString("Name","Arial")
            $XmlObjectWriter.WriteElementString("Size","12")
        $XmlObjectWriter.WriteEndElement()                  # </Fonts>
    $XmlObjectWriter.WriteEndElement()` конечный элемент </Configuration>
$XmlObjectWriter.WriteEndElement()` конечный элемент </Root>

$XmlObjectWriter.WriteEndDocument()` завершить запись в документ
$XmlObjectWriter.Flush()
$XmlObjectWriter.Close()
```

### CreateElement

``` powershell
$xml = [xml](gc $home\desktop\test.xml)
$xml.Root.Configuration.Fonts
$NewElement = $xml.CreateElement("Fonts")` выбрать элемент куда добавить
$NewElement.set_InnerXML("<Name>Times New Roman</Name><Size>14</Size>")` Заполнить значениями дочерние элементы Fonts
$xml.Root.Configuration.AppendChild($NewElement)` добавить элемент новой строкой в Configuration (родитель Fonts)
$xml.Save("$home\desktop\test.xml")
```

## Excel

`Install-Module -Name ImportExcel`\
`$data | Export-Excel .\Data.xlsx`\
`$data = Import-Excel .\Data.xlsx`

`$data = ps`\
`$Chart = New-ExcelChartDefinition -XRange CPU -YRange WS -Title "Process" -NoLegend`\
`$data | Export-Excel .\ps.xlsx -AutoNameRange -ExcelChartDefinition $Chart -Show`

## CSV

`Get-Service | Select Name,DisplayName,Status,StartType | Export-Csv -path "$home\Desktop\Get-Service.csv" -Append -Encoding Default`
экспортировать в csv (-Encoding UTF8)\
`Import-Csv "$home\Desktop\Get-Service.csv" -Delimiter ","`
импортировать массив

``` powershell
$data = ConvertFrom-Csv @"
Region,State,Units,Price
West,Texas,927,923.71
$null,Tennessee,466,770.67
"@
```

`$systeminfo = systeminfo /FO csv | ConvertFrom-Csv` вывод работы
программы в CSV и конвертация в объект\
`$systeminfo."Полный объем физической памяти"`\
`$systeminfo."Доступная физическая память"`

## JSON

``` powershell
$log = '
{
  "log": {
    "level": 7
  }
}
' | ConvertFrom-Json

Get-Service | ConvertTo-Json

$OOKLA  = '
{
"result" : 
{"date":1683534970,"id":"14708271987","connection_icon":"wireless","download":33418,"upload":35442,"latency":15,"distance":50,"country_code":"RU","server_id":2707,"server_name":"Bryansk","sponsor_name":"DOM.RU","sponsor_url":null,"connection_mode":"multi","isp_name":"Resource Link","isp_rating":"4.0","test_rank":63,"test_grade":"B-","test_rating":4,"idle_latency":"17","download_latency":"116","upload_latency":"75","additional_servers":
[{"server_id":8191,"server_name":"Bryansk","sponsor_name":"SectorTelecom.ru"},{"server_id":46278,"server_name":"Fokino","sponsor_name":"Fokks - Promyshlennaya avtomatika Ltd."},{"server_id":18218,"server_name":"Bryansk","sponsor_name":"RIA-link Ltd."}],
"path":"result\u002F14708271987","hasSecondary":true
}
}
' | ConvertFrom-Json
$ookla.result
```

## YAML

``` powershell
Import-Module PSYaml` используется в Docker/Ansible
$netplan = "
network:` словарь по типу - ключ : значение с вложенными словарями
  ethernets:
    ens160:
      dhcp4: yes
      dhcp6: no
      nameservers:
        addresses:` [8.8.8.8, 1.1.1.1]` список данных (строк)
          - 8.8.8.8
          - 1.1.1.1
  version: 2
"
$network = ConvertFrom-Yaml $netplan
$network.Values.ethernets.ens160.nameservers

$DataType = "
int: !!int 10.1
flo: !!float 10.1
str: !!str string
bool: !!bool` boolean
"
```

## TOML

`Install-Module -Name PSToml -Scope CurrentUser` Устанавливаем модуль
для чтения toml (PSToml)\
`$toml = Get-Content "$home\Documents\Git\lifailon.github.io\hugo.toml"`
Читаем содержимое конфигурации Hugo в формате toml\
`$json = ConvertFrom-Toml $toml | ConvertTo-Json -Depth 3` Конвертируем
TOML в JSON\
`$json | Out-File "$home\Documents\Git\lifailon.github.io\hugo.json"`
Сохраняем конфигурационный файл в формате JSON

## ConvertFrom-String

``` powershell
'
log = 
{
   level = 4;
};
' | ConvertFrom-String` создает PSCustomObject (разбивает по пробелам, удаляет все пробелы и пустые строки)
```

## ConvertFrom-StringData

``` powershell
"
key1 = value1
key2 = value2
" | ConvertFrom-StringData # создает Hashtable
```

## Pandoc

``` powershell
$release_latest = Invoke-RestMethod "https://api.github.com/repos/jgm/pandoc/releases/latest"
$url = $($release_latest.assets | Where-Object name -match "windows-x86_64.zip").browser_download_url
Invoke-RestMethod $url -OutFile $home\Downloads\pandoc.zip
Expand-Archive -Path "$home\Downloads\pandoc.zip" -DestinationPath "$home\Downloads\"
$path = $(Get-ChildItem "$home\Downloads\pandoc-*\*.exe").FullName
Copy-Item -Path $path -Destination "C:\Windows\System32\pandoc.exe"
Remove-Item "$home\Downloads\pandoc*" -Force -Recurse
```

`pandoc -s README.md -o index.html` конвертация из Markdown в HTML\
`pandoc README.md -o index.html --css=styles.css` применить стили из
css\
`pandoc -s index.html -o README.md` конвертация из HTML в Markdown\
`pandoc -s README.md -o README.docx` конвертация в Word\
`pandoc -s README.md -o README.epub` конвертация в открытый формат
электронных версий книг\
`pandoc -s README.md -o README.pdf` конвертация в PDF (требуется
rsvg-convert)\
`pandoc input.md -f markdown+hard_line_breaks -o output.md` конвертация
из markdown документа, который не содержит обратный слэш в конце каждой
строки для переноса (), который их добавит

### Convert Excel to Markdown

``` powershell
Import-Module ImportExcel
Import-Excel -Path srv.xlsx | Export-Csv -Path $csvFilePath -NoTypeInformation -Encoding UTF8 # конвертация Excel в csv
pandoc -s -f csv -t markdown input.csv -o output.md # конвертация таблицу csv в markdown
```

## FFmpeg

``` powershell
$release_latest = Invoke-RestMethod "https://api.github.com/repos/BtbN/FFmpeg-Builds/releases/latest"
$url = $($release_latest.assets | Where-Object name -match "ffmpeg-master-latest-win64-gpl.zip").browser_download_url
Invoke-RestMethod $url -OutFile $home\Downloads\ffmpeg-master-latest-win64-gpl.zip
Expand-Archive -Path "$home\Downloads\ffmpeg-master-latest-win64-gpl.zip" -DestinationPath "$home\Downloads\"
Copy-Item -Path "$home\Downloads\ffmpeg-master-latest-win64-gpl\bin\ffmpeg.exe" -Destination "C:\Windows\System32\ffmpeg.exe"
Remove-Item "$home\Downloads\ffmpeg-*" -Force -Recurse
```

`ffmpeg -i input.mp4 output.gif` конвертировать mp4 в gif\
`ffmpeg -i input.mp4 -filter_complex "scale=1440:-1:flags=lanczos" output.gif`
изменить разрешение на выходе\
`ffmpeg -i input.mp4 -filter_complex "scale=1440:-1:flags=lanczos" -r 10 output.gif`
изменить количество кадров в секунду на выходе\
`ffmpeg -i input.mp4 -filter_complex "fps=5,scale=960:-1:flags=lanczos,split[s0][s1];[s0]palettegen=max_colors=32[p];[s1][p]paletteuse=dither=bayer" output.gif`
сжатие за счет цветовой политры\
`ffmpeg -i input.mp4 -ss 00:00:10 -frames:v 1 -q:v 1 output.jpg`
вытащить скриншот из видео на 10 секунде\
`ffmpeg -i input.mp4 -ss 00:00:05 -to 00:00:10 -c copy output.mp4`
вытащить кусок видео\
`ffmpeg -i "%d.jpeg" -framerate 2 -c:v libx264 -r 30 -pix_fmt yuv420p output.mp4`
создать видео из фото (1.jpeg, 2.jpeg и т.д.) с framerate (частотой
кадров) в создаваемом видео 2 кадра в секунду\
`ffmpeg -i "rtsp://admin:password@192.168.3.201:554" -rtsp_transport tcp -c:v copy -c:a aac -strict experimental output.mp4`
запись без перекодирования (copy) RTSP-потока с камеры видеонаблюдения
(+ аудио в кодеке AAC) в файл\
`ffmpeg -i "rtsp://admin:password@192.168.3.201:554" -rtsp_transport tcp -c:v copy -c:a aac -strict experimental -movflags +faststart+frag_keyframe+empty_moov output.mp4`
переместить метаданные в начало файла, что позволяет начать
воспроизведение файла в видеоплеере до его полной загрузки\
`ffmpeg -i "rtsp://admin:password@192.168.3.201:554" -rtsp_transport tcp -frames:v 1 -c:v mjpeg output.jpg`
сделать скриншот

## HandBrake

``` powershell
$url = "https://github.com/HandBrake/HandBrake/releases/download/1.8.0/HandBrakeCLI-1.8.0-win-x86_64.zip"
Invoke-RestMethod $url -OutFile $home\Downloads\HandBrakeCLI.zip
Expand-Archive -Path $home\Downloads\HandBrakeCLI.zip -OutputPath "$home\Downloads\"
Copy-Item -Path "$home\Downloads\HandBrakeCLI.exe" -Destination "C:\Windows\System32\HandBrakeCLI.exe"
Remove-Item "$home\Downloads\doc" -Force -Recurse
Remove-Item "$home\Downloads\HandBrakeCLI*"
```

`HandBrakeCLI -i input.mp4 -o output.mkv` конвертирует видео в формате
mp4 в формат mkv с использованием стандартных настроек HandBrake\
`HandBrakeCLI -i input.mp4 -o output.mkv -q 20` установить качество
видео 20, значения варьируются от 0 (максимальное качество) до 51
(минимальное качество), где 20 считается хорошим качеством для
большинства видео\
`HandBrakeCLI -i input.mp4 -o output.mkv -r 30` установить частоту
кадров на 30 fps\
`HandBrakeCLI -i input.mp4 -o output.mkv --maxWidth 1280 --maxHeight 720`
изменить размер на 1280х720\
`HandBrakeCLI -i input.mp4 -o output.mkv -b 1500` установить битрейт
видео 1500 кбит/с\
`HandBrakeCLI -i input.mp4 -o output.mkv -e x264` преобразовать видео с
использованием кодека x264\
`HandBrakeCLI -i input.mp4 -o output.mp4 --crop 0:200:0:0` обрезать
видео снизу на 200px (верх:низ:лево:право)\
`HandBrakeCLI -i input.mp4 -o output.mp4 --start-at duration:5 --stop-at duration:15`
обрезать видео (на выходе будет 15-секундное видео с 5 по 20 секунду)

## ImageMagick

Source: [ImageMagick](https://sourceforge.net/projects/imagemagick)

`magick identify -verbose PowerShell-Commands.png` извлечь метаданные
изображения\
`magick PowerShell-Commands.png output.jpg` конвертация формата
изображения\
`magick PowerShell-Commands.png -resize 800x600 output.jpg` изменить
размер (увеличить или уменьшить)\
`magick PowerShell-Commands.png -crop 400x300+100+50 output.jpg`
обрезать\
`magick PowerShell-Commands.png -rotate 90 output.jpg` повернуть
изображение\
`magick PowerShell-Commands.png -fill white -pointsize 24 -gravity center -annotate +0+0 "PowerShell" output.jpg`
наложить текст на изображение\
`magick PowerShell-Commands.png -brightness-contrast +20x+10 output.jpg`
изменить яркость и контрастность\
`magick convert -delay 100 1.png 2.png 3.png output.gif` создать gif из
изображений\
`magick convert image1.jpg image2.jpg -append output.jpg` вертикально
объединенить изображения

## YouTube

``` powershell
$release_latest = Invoke-RestMethod "https://api.github.com/repos/yt-dlp/yt-dlp/releases/latest"
$url = $($release_latest.assets | Where-Object name -match "yt-dlp.exe").browser_download_url
Invoke-RestMethod $url -OutFile "C:\Windows\System32\yt-dlp.exe"
```

`yt-dlp -F https://www.youtube.com/watch?v=gxplizjhqiw` отобразить
список всех доступных форматов\
`yt-dlp -J https://www.youtube.com/watch?v=gxplizjhqiw` вывести данные в
формате JSON\
`yt-dlp -J https://www.youtube.com/watch?v=gxplizjhqiw | jq -r .formats.[].format`
id - resolution (format_note)\
`yt-dlp -f 137 https://www.youtube.com/watch?v=gxplizjhqiw` загрузить
только видео в указанном формате по id\
`yt-dlp -f bestaudio https://www.youtube.com/watch?v=gxplizjhqiw`
загрузить только аудио\
`yt-dlp -f best https://www.youtube.com/watch?v=gxplizjhqiw` загрузить
видео с аудио в лучшем качестве\
`yt-dlp -f 'bestvideo[height<=1080]+bestaudio/best[height<=1080]' https://www.youtube.com/watch?v=gxplizjhqiw`
загрузить в указанном качестве\
`yt-dlp -r 2m https://www.youtube.com/watch?v=gxplizjhqiw` ограничить
скорость загрузки до 2 МБит/с

``` powershell
function Get-YouTube {
    param (
        $url
    )
    $result = yt-dlp -J $url
    $($result | ConvertFrom-Json).formats | 
    Where-Object filesize -ne $null | 
    Select-Object format_id,
    @{Name="FileSize"; 
        Expression={[string]([int]($_.filesize / 1024kb)).ToString("0.0")+" Mb"}
    },
    resolution,format_note,quality,fps,ext,language
}

$formats = Get-YouTube "https://www.youtube.com/watch?v=gxplizjhqiw"
$video = $($formats | Where-Object format_note -match 1080 | Where-Object ext -match mp4)[-1].format_id
$audio = $($formats | Where-Object resolution -match "audio" | Where-Object ext -match m4a)[-1].format_id
cd "$home\Downloads"
yt-dlp -f $video+$audio https://www.youtube.com/watch?v=gxplizjhqiw -o '%(title)s.%(ext)s'
```
