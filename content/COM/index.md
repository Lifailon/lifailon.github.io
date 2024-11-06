---
title: "Component Object Model"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

## Shell

`$wshell = New-Object -ComObject Wscript.Shell`\
`$wshell | Get-Member`\
`$link = $wshell.CreateShortcut("$Home\Desktop\Yandex.lnk")` создать
ярлык\
`$link | Get-Member`\
`$link.TargetPath = "https://yandex.ru"` куда ссылается (метод
TargetPath объекта $link где хранится объект CreateShortcut)
`$link.Save()\` сохранить

`Set-WinUserLanguageList -LanguageList en-us,ru -Force` изменить
языковую раскладку клавиатуры

## SendKeys

`(New-Object -ComObject Wscript.shell).SendKeys([char]173)`
включить/выключить звук\
`$wshell.Exec("notepad.exe")` запустить приложение\
`$wshell.AppActivate("Блокнот")` развернуть запущенное приложение

`$wshell.SendKeys("Login")` текст\
`$wshell.SendKeys("{A 5}")` напечатать букву 5 раз подряд\
`$wshell.SendKeys("%{TAB}")` ALT+TAB\
`$wshell.SendKeys("^")` CTRL\
`$wshell.SendKeys("%")` ALT\
`$wshell.SendKeys("+")` SHIFT\
`$wshell.SendKeys("{DOWN}")` вниз\
`$wshell.SendKeys("{UP}")` вверх\
`$wshell.SendKeys("{LEFT}")` влево\
`$wshell.SendKeys("{RIGHT}")` вправо\
`$wshell.SendKeys("{PGUP}")` PAGE UP\
`$wshell.SendKeys("{PGDN}")` PAGE DOWN\
`$wshell.SendKeys("{BACKSPACE}")` BACKSPACE/BKSP/BS\
`$wshell.SendKeys("{DEL}")` DEL/DELETE\
`$wshell.SendKeys("{INS}")` INS/INSERT\
`$wshell.SendKeys("{PRTSC}")` PRINT SCREEN\
`$wshell.SendKeys("{ENTER}")`\
`$wshell.SendKeys("{ESC}")`\
`$wshell.SendKeys("{TAB}")`\
`$wshell.SendKeys("{END}")`\
`$wshell.SendKeys("{HOME}")`\
`$wshell.SendKeys("{BREAK}")`\
`$wshell.SendKeys("{SCROLLLOCK}")`\
`$wshell.SendKeys("{CAPSLOCK}")`\
`$wshell.SendKeys("{NUMLOCK}")`\
`$wshell.SendKeys("{F1}")`\
`$wshell.SendKeys("{F12}")`\
`$wshell.SendKeys("{+}{^}{%}{~}{(}{)}{[}{]}{{}{}}")`

``` powershell
function Get-AltTab {
    (New-Object -ComObject wscript.shell).SendKeys("%{Tab}")
    Start-Sleep $(Get-Random -Minimum 30 -Maximum 180)
    Get-AltTab
}
```

`Get-AltTab`

## Popup

`$wshell = New-Object -ComObject Wscript.Shell`\
`$output = $wshell.Popup("Выберите действие?",0,"Заголовок",4)`\
`if ($output -eq 6) {"yes"} elseif ($output -eq 7) {"no"} else {"no good"}`

    Type:
    0 ОК
    1 ОК и Отмена
    2 Стоп, Повтор, Пропустить
    3 Да, Нет, Отмена
    4 Да и Нет
    5 Повтор и Отмена
    16 Stop
    32 Question
    48 Exclamation
    64 Information

    Output:
    -1 Timeout
    1 ОК
    2 Отмена
    3 Стоп
    4 Повтор
    5 Пропустить
    6 Да
    7 Нет

## Internet Explorer

`$ie.document.IHTMLDocument3_getElementsByTagName("input")  | select name`
получить имена всех Input Box\
`$ie.document.IHTMLDocument3_getElementsByTagName("button") | select innerText`
получить имена всех Button\
`$ie.Document.documentElement.innerHTML` прочитать сырой Web Content
(\<input name="login" tabindex="100" class="input\_\_control
input\_\_input" id="uniq32005644019429136" spellcheck="false"
placeholder="Логин")\
`$All_Elements = $ie.document.IHTMLDocument3_getElementsByTagName("*")`
забрать все элементы\
`$Go_Button = $All_Elements | ? innerText -like "go"` поиск элемента по
имени\
`$Go_Button | select ie9_tagName` получить TagName (SPAN) для быстрого
дальнейшего поиска\
`$SPAN_Elements = $ie.document.IHTMLDocument3_getElementsByTagName("SPAN")`

``` powershell
$ie = New-Object -ComObject InternetExplorer.Application
$ie.navigate("https://yandex.ru")
$ie.visible = $true
$ie.document.IHTMLDocument3_getElementByID("login").value = "Login"
$ie.document.IHTMLDocument3_getElementByID("passwd").value = "Password"
$Button_Auth = ($ie.document.IHTMLDocument3_getElementsByTagName("button")) | ? innerText -match "Войти"
$Button_Auth.Click()
$Result = $ie.Document.documentElement.innerHTML
$ie.Quit()
```

## Excel

``` powershell
$path = "$home\Desktop\Services-to-Excel.xlsx"
$Excel = New-Object -ComObject Excel.Application
$Excel.Visible = $false` отключить открытие GUI
$ExcelWorkBook = $Excel.Workbooks.Add()` Создать книгу
$ExcelWorkSheet = $ExcelWorkBook.Worksheets.Item(1)` Создать лист
$ExcelWorkSheet.Name = "Services"` задать имя листа
$ExcelWorkSheet.Cells.Item(1,1) = "Name service"
# Задать имена столбцов:
$ExcelWorkSheet.Cells.Item(1,2) = "Description"
$ExcelWorkSheet.Cells.Item(1,3) = "Status"
$ExcelWorkSheet.Cells.Item(1,4) = "Startup type"
$ExcelWorkSheet.Rows.Item(1).Font.Bold = $true` выделить жирным шрифтом
$ExcelWorkSheet.Rows.Item(1).Font.size=14
# Задать ширину колонок:
$ExcelWorkSheet.Columns.Item(1).ColumnWidth=30
$ExcelWorkSheet.Columns.Item(2).ColumnWidth=80
$ExcelWorkSheet.Columns.Item(3).ColumnWidth=15
$ExcelWorkSheet.Columns.Item(4).ColumnWidth=25
$services =  Get-Service
$counter = 2` задать начальный номер строки для записи
foreach ($service in $services) {
    $status = $service.Status
    if ($status -eq 1) {
        $status_type = "Stopped"
    } elseif ($status -eq 4) {
        $status_type = "Running"
    }
    $Start = $service.StartType
    if ($Start -eq 1) {
        $start_type = "Delayed start"
    } elseif ($Start -eq 2) {
        $start_type = "Automatic"
    } elseif ($Start -eq 3) {
        $start_type = "Manually"
    } elseif ($Start -eq 4) {
        $start_type = "Disabled"
    }
    $ExcelWorkSheet.Columns.Item(1).Rows.Item($counter) = $service.Name
    $ExcelWorkSheet.Columns.Item(2).Rows.Item($counter) = $service.DisplayName
    $ExcelWorkSheet.Columns.Item(3).Rows.Item($counter) = $status_type
    $ExcelWorkSheet.Columns.Item(4).Rows.Item($counter) = $start_type
    if ($status_type -eq "Running") {
        $ExcelWorkSheet.Columns.Item(3).Rows.Item($counter).Font.Bold = $true
    }
    # +1 увеличить для счетчика строки Rows
    $counter++
}
$ExcelWorkBook.SaveAs($path)
$ExcelWorkBook.close($true)
$Excel.Quit()
```

## Excel.Application.Open

``` powershell
$path = "$home\Desktop\Services-to-Excel.xlsx"
$Excel = New-Object -ComObject Excel.Application
$Excel.Visible = $false
$ExcelWorkBook = $excel.Workbooks.Open($path)` открыть xlsx-файл
$ExcelWorkBook.Sheets | select Name,Index` отобразить листы
$ExcelWorkSheet = $ExcelWorkBook.Sheets.Item(1)` открыть лист по номеру Index
1..100 | %{$ExcelWorkSheet.Range("A$_").Text}` прочитать значение из столбца А строки c 1 по 100
$Excel.Quit()
```

## Network

`$wshell = New-Object -ComObject WScript.Network`\
`$wshell | Get-Member`\
`$wshell.UserName`\
`$wshell.ComputerName`\
`$wshell.UserDomain`

## Application

`$wshell = New-Object -ComObject Shell.Application`\
`$wshell | Get-Member`\
`$wshell.Explore("C:\")`\
`$wshell.Windows() | Get-Member` получить доступ к открытым в проводнике
или браузере Internet Explorer окон

`$shell = New-Object -Com Shell.Application`\
`$RecycleBin = $shell.Namespace(10)`\
`$RecycleBin.Items()`

## Outlook

`$Outlook = New-Object -ComObject Outlook.Application`\
`$Outlook | Get-Member`\
`$Outlook.Version`

``` powershell
$Outlook = New-Object -ComObject Outlook.Application
$Namespace = $Outlook.GetNamespace("MAPI")
$Folder = $namespace.GetDefaultFolder(4)` исходящие
$Folder = $namespace.GetDefaultFolder(6)` входящие
$Explorer = $Folder.GetExplorer()
$Explorer.Display() 
$Outlook.Quit()
```

## Update

`(New-Object -com 'Microsoft.Update.AutoUpdate').Settings`\
`(New-Object -com 'Microsoft.Update.AutoUpdate').Results`\
`(New-Timespan -Start ((New-Object -com 'Microsoft.Update.AutoUpdate').Results|Select -ExpandProperty LastInstallationSuccessDate) -End (Get-Date)).hours`
кол-во часов, прошедших с последней даты установки обновления
безопасности в Windows.
