---
title: "Modules"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

## psm1

Синтаксис модуля и его параметров
```PowerShell
function Get-Function {
    <#
    .SYNOPSIS
    Описание
    .DESCRIPTION
    Описание
    .LINK
    https://github.com/Lifailon/PS-Commands
    #>
    param (
        [Parameter(Mandatory,ValueFromPipeline)][string]$Text,
        [ValidateSet("Test1","Test2")][string]$Provider = "Test1",
        [ValidateRange(1,3)][int]$Number = 2
    )
    Write-Host Param Text: $Text
    Write-Host Param Provider: $Provider
    Write-Host Param Number: $Number
}
```
`Get-Function -Text Text1` 
`Get-Function -Text Text2 -Provider Test2 -Number 3`

## psd1

Описание модуля
```PowerShell
@{
    RootModule        = "Get-Function.psm1"
    ModuleVersion     = "0.1"
    Author            = "Lifailon"
    CompanyName       = "Open Source Community"
    Copyright         = "Apache-2.0"
    Description       = "Function example"
    PowerShellVersion = "7.2"
    PrivateData       = @{
        PSData = @{
            Tags         = @("Function","Example")
            ProjectUri   = "https://github.com/Lifailon/PS-Commands"
            LicenseUri   = "https://github.com/Lifailon/Console-Translate/blob/rsa/LICENSE"
            ReleaseNotes = "Second release"
        }
    }
}
```

# PackageManagement

`Import-Module PackageManagement` импортировать модуль 
`Get-Module PackageManagement` информация о модуле 
`Get-Command -Module PackageManagement` отобразить все командлеты модуля 
`Get-Package` отобразить все установленные пакеты PowerShellGallery 
`Get-Package -ProviderName msi,Programs` список установленных программ
`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` включить использование протокол TLS 1.2 (если не отключены протоколы TLS 1.0 и 1.1) 
`Get-PackageSource` источники установки пакетов 
`Get-PackageProvider` отобразить список провайдеров менеджеров пакетов 
`Get-PackageProvider | Where-Object Name -Match nuget` 
`Find-PackageProvider` отображение всех доступных менеджеров пакетов 
`Find-PackageProvider nuget` 
`Install-PackageProvider NuGet -Force` установить менеджер пакетов nuget 
`Install-PackageProvider PSGallery -Force` установить источник 
`Install-PackageProvider Chocolatey -Force` 
`Set-PackageSource nuget -Trusted` разрешить установку пакетов из указанного источника 
`Register-PSRepository -Name "NuGet" -SourceLocation "https://www.nuget.org/api/v2" -InstallationPolicy Trusted` зарегестрировать менеджер пакетов используя url (работает для Power Shell Core) 
`Set-PackageSource -Name NuGet -Trusted` изменить источник по умолчанию 
`Find-Package PSEverything` поиск пакетов по имени во всех менеджерах 
`Find-Package PSEverything -Provider NuGet` поиск пакета у выбранного провайдера 
`Install-Module PSEverything -Scope CurrentUser` установить модуль для текущего пользователя 
`Install-Package -Name VeeamLogParser -ProviderName PSGallery -scope CurrentUser` 
`Get-Command *Veeam*` 
`Import-Module -Name VeeamLogParser` загрузить модуль 
`Get-Module VeeamLogParser | select -ExpandProperty ExportedCommands` отобразить список функций

## winget

[Source](https://github.com/microsoft/winget-cli)
[Web](https://winget.run)

`winget list` список установленных пакетов 
`winget search VLC` найти пакет 
`winget show VideoLAN.VLC` информация о пакете 
`winget show VideoLAN.VLC --versions` список доступных версий в репозитории 
`winget install VideoLAN.VLC` установить пакет 
`winget uninstall VideoLAN.VLC` удалить пакет 
`winget download jqlang.jq` загрузкить пакет (https://github.com/jqlang/jq/releases/download/jq-1.7/jq-windows-amd64.exe) 
`winget install jqlang.jq` добавляет в переменную среду и псевдоним командной строки jq 
`winget uninstall jqlang.jq`

### jqlang
```PowerShell
[uri]$url = $($(irm https://api.github.com/repos/jqlang/jq/releases/latest).assets.browser_download_url -match "windows-amd64").ToString() # получить версию latest на GitHub
irm $url -OutFile "C:\Windows\System32\jq.exe" # загрузить jq.exe
```
## Scoop

`Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` 
`irm get.scoop.sh | iex` установка 
`scoop help` 
`scoop search jq` 
`scoop info jq` 
`(scoop info jq).version` 
`scoop cat jq` 
`scoop download jq` C:\Users\lifailon\scoop\cache 
`scoop install jq` C:\Users\lifailon\scoop\apps\jq\1.7 
`scoop list` 
`(scoop list).version` 
`scoop uninstall jq`

## Chocolatey
```PowerShell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```
`choco -v` 
`choco -help` 
`choco list` 
`choco install adobereader`

## NuGet

`Invoke-RestMethod https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -OutFile "$home\Documents\nuget.exe"` 
`Invoke-Expression "$home\Documents\nuget.exe search Selenium.WebDriver"` 
`Invoke-Expression "$home\Documents\nuget.exe install Selenium.WebDriver"` 
`Get-Item $home\Documents\*Selenium*`

`& "$home\Documents\nuget.exe" list console-translate` 
`$nuget_api_key = "<API-KEY>"` 
`$source = "https://api.nuget.org/v3/index.json"` 
`$Name = "Console-Translate"` 
`$path = "$home\Documents\$Name"` 
`New-Item -Type Directory $path` 
`Copy-Item "$home\Documents\Git\$Name\$Name\0.2\*" "$path\"` 
`Copy-Item "$home\Documents\Git\$Name\LICENSE" "$path\"` 
`Copy-Item "$home\Documents\Git\$Name\README.md" "$path\"`
```xml
'<?xml version="1.0"?>
<package >
  <metadata>
    <id>Console-Translate</id>
    <version>0.2.2</version>
    <authors>Lifailon</authors>
    <owners>Lifailon</owners>
    <description>Cross-platform client for translating text in the console, uses API Google (edded public free token), MyMemory and DeepLX (no token required)</description>
    <tags>PowerShell, Module, Translate, api</tags>
    <repository type="git" url="https://github.com/Lifailon/Console-Translate" />
    <projectUrl>https://github.com/Lifailon/Console-Translate</projectUrl>
    <licenseUrl>https://github.com/Lifailon/Console-Translate/blob/rsa/LICENSE</licenseUrl>
    <contentFiles>
      <files include="Console-Translate.psm1" buildAction="Content" />
      <files include="Console-Translate.psd1" buildAction="Content" />
      <files include="lang-iso-639-1.csv" buildAction="Content" />
      <files include="README.md" buildAction="Content" />
      <files include="LICENSE" buildAction="Content" />
    </contentFiles>
  </metadata>
</package>' > "$path\$Name.nuspec"
```
`Set-Location $path` 
`& "$home\Documents\nuget.exe" pack "$path\$Name.nuspec"` 
`& "$home\Documents\nuget.exe" push "$path\$Name.0.2.2.nupkg" -ApiKey $nuget_api_key -Source $source` 
`& "$home\Documents\nuget.exe" push "$path\$Name.0.2.2.nupkg" -ApiKey $nuget_api_key -Source $source -SkipDuplicate`

`Install-Package Console-Translate -Source nuget.org` 
`Get-Package Console-Translate | select *`

`Register-PSRepository -Name "NuGet" -SourceLocation "https://www.nuget.org/api/v2" -InstallationPolicy Trusted` 
`Get-PSRepository` 
`Find-Module -Name Console-Translate` 
`Install-Module Console-Translate -Repository NuGet`

`& "$home\Documents\nuget.exe" delete Console-Translate 0.2.0 -Source https://api.nuget.org/v3/index.json -ApiKey $nuget_api_key -NoPrompt`

# PS2EXE

`Install-Module ps2exe -Repository PSGallery` 
`Get-Module -ListAvailable` список всех модулей 
`-noConsole` использовать GUI, без окна консоли powershell 
`-noOutput` выполнение в фоне 
`-noError` без вывода ошибок 
`-requireAdmin` при запуске запросить права администратора 
`-credentialGUI` вывод диалогового окна для ввода учетных данных 
`Invoke-ps2exe -inputFile "$home\Desktop\WinEvent-Viewer-1.1.ps1" -outputFile "$home\Desktop\WEV-1.1.exe" -iconFile "$home\Desktop\log_48px.ico" -title "WinEvent-Viewer" -noConsole -noOutput -noError`

# NSSM

`$powershell_Path = (Get-Command powershell).Source` 
`$NSSM_Path = (Get-Command "C:\WinPerf-Agent\NSSM-2.24.exe").Source` 
`$Script_Path = "C:\WinPerf-Agent\WinPerf-Agent-1.1.ps1"` 
`$Service_Name = "WinPerf-Agent"` 
`& $NSSM_Path install $Service_Name $powershell_Path -ExecutionPolicy Bypass -NoProfile -f $Script_Path` создать Service 
`& $NSSM_Path start $Service_Name` запустить 
`& $NSSM_Path status $Service_Name` статус 
`$Service_Name | Restart-Service` перезапустить 
`$Service_Name | Get-Service` статус 
`$Service_Name | Stop-Service` остановить 
`& $NSSM_Path set $Service_Name description "Check performance CPU and report email"` изменить описание 
`& $NSSM_Path remove $Service_Name` удалить

# WinAPI

`Install-Module ps.win.api -Repository NuGet -AllowClobber` 
`Import-Module ps.win.api` 
`Get-Command -Module ps.win.api` 
`Start-WinAPI` запустить сервер 
`Test-WinAPI` статус сервера 
`Stop-WinAPI` остановить сервер 
`Read-WinAPI` отобразить лог в реальном времени 
`Get-Hardware` вывести сводную информацию о системе с использованием потоков (фоновых заданий) 
`Get-DiskPhysical` отобразить список физических дисков, их размер, интерфейс и статус 
`Get-DiskPhysical -ComputerName 192.168.3.100 -Port 8443 -User rest -Pass api` получить информацию с удаленного сервер, на котором запущен сервер WinAPI (доступно для всех функций модуля) 
`Get-DiskLogical` список логических дисков (включая виртуальные диски), их файловая система, общий и используемый размер в гб и процентах 
`Get-DiskPartition` список разделов физических дисков (отобразит скрытые разделы, загрузочный и порядок назначения байт) 
`Get-Smart` статус работы всех дисков и текущая температура 
`Get-IOps` количество операций ввода/вывода дисковой подсистемы 
`Get-Files` подробная информация о файлах (добавляет дату создания, изменения, доступа, количество дочерних файлов и директорий) 
`Find-Process` поиск пути к исполняемому файлу по имени остановленного (не запущенного) процесса в директориях 
`Get-ProcessPerformance` информация о процессах (Get-Process) в человеко-читаемом формате 
`Get-CPU` список все ядер и нагрузка на каждое ядро и на все сразу (суммарное процессорное время, привилегированное и пользовательское время процессора) 
`Get-MemorySize` размер оперативной, виртуальной памяти, суммарное потребление памяти процессов и путь к файлу подкачки 
`Get-MemorySlots` список всех слотов оперативной памяти 
`Get-NetInterfaceStat` статистика активного сетевого интерфейса за время с момента загрузки системы (количество пакетов и ГБ) 
`Get-NetInterfaceStat -Current` текущая статистика активного сетевого интерфейса (количество пакетов и МБ/c) 
`Get-NetIpConfig` конфигурация всех сетевых интерфейсов (ip и mac-адрес, статус DHCP сервера, дата аренды) 
`Get-NetStat` развернутая статистика сетевых интерфейсов (из Get-NetTCPConnection), добавляет имя процесса, имя удаленного хоста (через nslookup), время работы процесса (не процессорное время) и путь к исполняемому файлу 
`Get-Performance` информация из счетчиков (Get-Counter) человеко-читаемом формате 
`Get-VideoCard` информация о всех видео-картах (наименование, частота и объем видео-памяти) 
`Get-Software` список установленного програмного обеспечения 
`Get-WinUpdate` список обновлений Windows (дата установки и источник) 
`Get-Driver` список установленных драйверов (имя, провайдер, версия и дата установки)

# Get-Query

`Install-Module Get-Query -Repository NuGet` установить модуль 
`Get-Help Get-Query` 
`Get-Query localhost` отобразить всех авторизованных пользователей, их статус и время работы (по умолчанию localhost) 
`Get-Query 192.168.1.1.1 -proc` список всех пользовательских процессов (по умолчанию -user *) 
`Get-Query 192.168.1.1.1 -proc -user username` список процессов указанного пользователя

# Console-Translate

`Install-Module Console-Translate -Repository NuGet` 
`Get-Translate "Module for text translation"` 
`Get-Translate "Модуль для перевода текста"` 
`Get-Translate "Привет world" -LanguageSelected` т.к. больше латинских символов (на 1), то перевод будет произведен на английский язык 
`Get-Translate "Hello друг" -LanguageSelected` перевод на русский язык 
`Get-Translate -Text "Модуль для перевода текста" -LanguageSource ru -LanguageTarget tr` 
`Get-Translate -Provider MyMemory -Text "Hello World" -Alternatives` выбрать провайдер перевода и добавить альтернативные варианты вывода 
`Get-DeepLX "Get select" ru` 
`Start-DeepLX -Job` запустить сервер в режиме процесса 
`Start-DeepLX -Status` 
`Get-DeepLX -Server 192.168.3.99 -Text "Module for text translation" ru` 
`Stop-DeepLX` 
`Get-LanguageCode` получение кодов языков по стандарту ISO-639-1

# HardwareMonitor

`Install-Module HardwareMonitor -Repository NuGet -Scope AllUsers` 
`Install-LibreHardwareMonitor` установить и запустить LibreHardwareMonitor в систему (https://github.com/LibreHardwareMonitor/LibreHardwareMonitor) 
`Install-OpenHardwareMonitor` установить OpenHardwareMonitor (https://github.com/openhardwaremonitor/openhardwaremonitor) 
`Get-Sensor | Where-Object {($_.SensorName -match "Temperature") -or ($_.SensorType -match "Temperature")} | Format-Table` использовать LibreHardwareMonitor и WMI/CIM (по умолчанию) и отфильтровать вывод по наименованию датчикам или типу сенсора для вывода датчиков температуры 
`Get-Sensor -Open` использовать OpenHardwareMonitor 
`Get-Sensor -ComputerName 192.168.3.99 -Port 8086 | Format-Table` вывести датчики системы через REST API 
`Get-Sensor -ComputerName 192.168.3.99 -Port 8085 -User hardware -Password monitor | Where-Object Value -notmatch "^0,0" | Format-Table` использовать авторизацию и отфильтровать вывод не пустых датчиков 
`Get-Sensor -Library | Where-Object Value -ne 0 | Format-Table` использовать динамическую библиотеку (dll) через .NET

# CrystalDisk-Cli

`Install-Module CrystalDisk-Cli -Repository NuGet` 
`Get-DiskInfoSettings` отобразить настройки программы Crystal-DiskInfo (https://github.com/hiyohiyo/CrystalDiskInfo) 
`Get-DiskInfoSettings -AutoRefresh 5` изминить время сканирования на 5 минут 
`Get-DiskInfo -List` отобразить список дисков 
`Get-DiskInfo` получить результаты последнего сканирования (статус, температура и т.п.) 
`Get-DiskInfo -Report | Select-Object Name,Date,HealthStatus,Temperature` получить актуальный отчет (запустить сканирование и дождаться результатов)

# PS-Pi-Hole
```PowerShell
$path_psm = ($env:PSModulePath.Split(";")[0])+"\Invoke-Pi-Hole\Invoke-Pi-Hole.psm1"
if (!(Test-Path $path_psm)) {
    New-Item $path_psm -ItemType File -Force
}
irm https://raw.githubusercontent.com/Lifailon/PS-Pi-Hole/rsa/Invoke-Pi-Hole/Invoke-Pi-Hole.psm1 | Out-File $path_psm -Force
```
`sudo cat /etc/pihole/setupVars.conf | grep WEBPASSWORD` получить токен доступа 
`$Server = "192.168.1.253"` 
`$Token = "5af9bd44aebce0af6206fc8ad4c3750b6bf2dd38fa59bba84ea9570e16a05d0f"` 
`Invoke-Pi-Hole -Versions -Server $Server -Token $Token` получить текущую версию ядра (backend) и веб (frontend) на сервере а также последнюю доступную версию для обновления 
`Invoke-Pi-Hole -Releases -Server $Server -Token $Token` узнать последнюю доступную версию в репозитории на GitHub 
`Invoke-Pi-Hole -QueryLog -Server $Server -Token $Token` отобразить полный журнал запросов (клиент, домен назначения, тип записи, статус время запроса и адрес пересылки forward dns - куда ушел запрос) 
`Invoke-Pi-Hole -AdList -Server $Server -Token $Token` получить списки блокировак используемых на сервере (дата обновления, количество доменов и url-источника) 
`Invoke-Pi-Hole -Status -Server $Server -Token $Token` статус работы режима блокировки 
`Invoke-Pi-Hole -Enable -Server $Server -Token $Token` включить блокировку 
`Invoke-Pi-Hole -Disable -Server $Server -Token $Token` отключить блокировку 
`Invoke-Pi-Hole -Stats -Server $Server -Token $Token` подключиться к серверу Pi-Hole для получения статистики (метрики: количество доменов для блокировки, количество запросов и блокировок за сегодня и т.д.) 
`Invoke-Pi-Hole -QueryTypes -Server $Server -Token $Token` статистика запросов по типу записей относительно 100% 
`Invoke-Pi-Hole -TopClient -Server $Server -Token $Token` список самых активных клиентов (ip/name и количество запросов проходящих через сервер) 
`Invoke-Pi-Hole -TopPermittedDomains -Count 100 -Server $Server -Token $Token` список самых посещяемых доменов и количество запросов 
`Invoke-Pi-Hole -LastBlockedDomain -Server $Server -Token $Token` адрес последнего заблокированного домена 
`Invoke-Pi-Hole -ForwardServer -Server $Server -Token $Token` список серверов для пересылки, которым обычно выступает DNS-сервер стоящий за Pi-Hole в локальной сети, например AD 
`Invoke-Pi-Hole -Data -Server $Server -Token $Token` количество запросов за каждые 10 минут в течение последних 24 часов
