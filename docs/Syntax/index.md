---
title: "Syntax"
author: "Lifailon"
date: "2024-03-14T04:00:00+03:00"
---

### Cheat Sheet

<p align="center">
<a href="https://github.com/Lifailon/PS-Commands"><img title="PowerShell Cheat Sheet RU"src="PowerShell-Cheat-Sheet-RU.jpg"></a>
</p>

### Help

`Get-Verb` действия/глаголы, утвержденные для использования в командлетах 
`Get-Command *Language*` поиск команды по имени 
`(Get-Command Get-Language).Module` узнать к какому модулю принадлежит команда 
`Get-Command Get-Content | fl Module,DLL` узнать принадлежность команды к модулю и dll 
`Get-Command -Module LanguagePackManagement` отобразить список команд указанного модуля 
`(Get-Module LanguagePackManagement).ExportedCommands.Values` отобразить список команд указанного модуля 
`Get-Language | Get-Member` отобразить список методов команды (действия), объекты вывода и Event (события объектов: Click) 
`(Get-Help Get-Service).Aliases` узнать псевдонимом команды 
`Get-Alias gsv` узнать имя команды по псевдониму 
`Get-Help Get-Service` синтаксис 
`Get-Help Get-Service -Parameter *` описание всех параметров 
`Get-Help Get-Service -Online` 
`Get-Help Get-Service -ShowWindow` описание параметров в GUI с фильтрацией 
`Show-Command` вывести список команд в GUI 
`Show-Command Get-Service` список параметров команды в GUI 
`Invoke-Expression` iex принимает текст для выполнения в консоли как команды 
`$PSVersionTable` текущая версия PowerShell 
`Set-ExecutionPolicy Unrestricted` 
`Get-ExecutionPolicy` 
`$Metadata = New-Object System.Management.Automation.CommandMetaData (Get-Command Get-Service)` получить информацию о командлете 
`[System.Management.Automation.ProxyCommand]::Create($Metadata)` исходный код функции

## Object

### Variable

`$var = Read-Host "Enter"` ручной ввод 
`$pass = Read-Host "Enter Password" -AsSecureString` скрывать набор 
`$global:path = "\\path"` задать глобальную переменную, например в функции 
`$using:srv` использовать переменную текущей сесси в Invoke-сессии 
`Get-Variable` отобразить все переменные 
`ls variable:/` отобразить все переменные 
`Get-Variable *srv*` найти переменную по имени 
`Get-Variable -Scope Global` отобразить все глобальные переменные 
`Get-Variable Error` последняя команда с ошибкой 
`Remove-Variable -Name *` очистить все переменные 
`$LASTEXITCODE` содержит код вывода последней запущенной программы, например ping. Если код возврата положительный (True), то $LastExitCode = 0

### ENV

`Get-ChildItem Env:` отобразить все переменные окружения 
`$env:PSModulePath` директории импорта модулей 
`$env:userprofile` 
`$env:computername` 
`$env:username` 
`$env:userdnsdomain` 
`$env:logonserver` 
`([DirectoryServices.ActiveDirectory.Forest]::GetCurrentForest()).Name` 
`[Environment]::GetFolderPath('ApplicationData')`

### History

`Get-History` история команд текущей сессии 
`(Get-History)[-1].Duration.TotalSeconds` время выполнения последней команды 
`(Get-PSReadLineOption).HistorySavePath` путь к сохраненному файлу с 4096 последних команд (из модуля PSReadLine) 
`Get-Content (Get-PSReadlineOption).HistorySavePath | Select-String Get` поиск по содержимому файла (GREP) 
`Set-PSReadlineOption -MaximumHistoryCount 10000` изменить количество сохраняемых команд в файл 
`Get-PSReadLineOption | select MaximumHistoryCount` 
`Set-PSReadlineOption -HistorySaveStyle SaveNothing` отключить ведение журнала 
`F2` переключиться с InlineView на ListView

### Clipboard

`Set-Clipboard $srv` скопировать в буфер обмена 
`Get-Clipboard` вставить

### Write-Host
```PowerShell
Write-Host -BackgroundColor Green "Test:" -NoNewline # изменить цвет фона и запретить перенос строки
Write-Host " True" -ForegroundColor Green # данная строка будет печататься продолжая предыдущую с новыми параметрами цвета (фон по умолчанию, изменяем цвет текста)
```
`Write-Error "False"` 
`Write-Warning "False"`

### Write-Progress
```PowerShell
foreach ($n in 1..100) {
    Write-Progress -Activity "Test Progress" -PercentComplete $n
    Start-Sleep -Milliseconds 100
}
```
### for
```PowerShell
for ($i = 0; $i -le 100; $i+=10) {
    Write-Progress -Activity "Test Progress" -PercentComplete $i
    Start-Sleep -Seconds 1
}
```
### Array

`$srv = @("server-01", "server-02")`  создать массив 
`$srv += @("server-03")` добавить в массив новый элемент 
`$srv.Count` отобразить кол-во элементов в массиве 
`Out-String` построчный вывод

### Index

`$srv[0]` вывести первое значение элемента массива 
`$srv[0] = Name` замена элемента в массиве 
`$srv[0].Length` узнать кол-во символов первого значения в массиве 
`$srv[10..100]` срез
```PowerShell
$array = "a","b","c","d"
$num = 0
foreach ($a in $array) {
    $num += 1
    $index = [array]::IndexOf($array, $a) # узнать номер индекса по зачению
    $array[$index] = $num # пересобрать исходный массив
}
```
### HashTable
```PowerShell
$hashtable = @{ # Создать (инициализировать)
    "User" = $env:USERNAME; 
    "Server" = $env:COMPUTERNAME
}

$hashtable += @{ # Добавить ключи
    "Profile" = $PROFILE;
    "PowerShell_Home_Dir" = $PSHOME
}
```
`$hashtable.Keys` список всех ключей 
`$hashtable["User"]` получить значение (Values) по ключу 
`$hashtable["User"] = "Test"` изменить 
`$hashtable.Remove("User")` удалить ключ

### Collections/List
```PowerShell
$Collections = New-Object System.Collections.Generic.List[System.Object]
$Collections.Add([PSCustomObject]@{
    User = $env:username;
    Server = $env:computername
})
```
### PSCustomObject
```PowerShell
$CustomObject = [PSCustomObject][ordered]@{
    User = $env:username;
    Server = $env:computername
}
```
### Add and Remove Property

`$CustomObject | Add-Member –MemberType NoteProperty –Name Arr –Value @(1,2,3)` добавить свойство/стобец 
`$CustomObject.Arr = @(1,3,5)` изменить содержимое 
`$CustomObject.PsObject.Properties.Remove('User')` удалить Property

### Add Method
```PowerShell
$ScriptBlock = {Get-Service}
$CustomObject | Add-Member -Name "TestMethod" -MemberType ScriptMethod -Value $ScriptBlock
$CustomObject | Get-Member
$CustomObject.TestMethod()
```
### Class
```PowerShell
Class CustomClass {
    [string]$User
    [string]$Server
    Start([bool]$Param1) {
        If ($Param1) {
        Write-Host "Start Function"
        }
    }
}
```
`$Class = New-Object -TypeName CustomClass` 
`$Class.User = $env:username` 
`$Class.Server = $env:computername` 
`$Class.Start(1)`

### Pipeline

`$CustomObject | Add-Member -MemberType NoteProperty -Name "Type" -Value "user" -Force` добавление объкта вывода NoteProperty 
`$CustomObject | Add-Member -MemberType NoteProperty -Name "User" -Value "admin" -Force` изменеие содержимого для сущности объекта User 
`ping $srv | Out-Null` перенаправить результат вывода в Out-Null

### Select-Object

`Get-Process | Select-Object -Property *` отобразить все доступные объекты вывода 
`Get-Process | select -Unique "Name"` удалить повторяющиеся значения в массиве 
`Get-Process | select -ExpandProperty ProcessName` преобразовать из объекта-коллекции в массив (вывести содержимое без наименовая столбца) 
`(Get-Process | ? Name -match iperf).Modules` список используемых модулей процессом

### Expression
```PowerShell
Get-Process | Sort-Object -Descending CPU | select -first 10 ProcessName, # сортировка по CPU, вывести первых 10 значений (-first)
@{Name="ProcessorTime";
    Expression={$_.TotalProcessorTime -replace "\.\d+$"} # затрачено процессорного времени в минутах
},
@{Name="Memory"; 
    Expression={[string]([int]($_.WS / 1024kb))+"MB"} # делим байты на КБ (1mb)
},
@{Label="RunTime"; 
    Expression={((Get-Date) - $_.StartTime) -replace "\.\d+$"} # вычесть из текущего времени - время запуска, и удалить milisec
}
```
### Select-String

`$(ipconfig | Select-String IPv4) -replace ".+: " | Where-Object {$_ -match "^172."}` узнать только IP 
`$Current_IP = Get-Content $RDCMan_RDG_PATH | Select-String $RDCMan_Display_Name -Context 0,1` получить две строки 
`$Current_IP = $Current_IP.Context.DisplayPostContext[0] -replace ".+<name>|<\/name>"` забрать только вторую строку и удалить тэги

### Format-List/Format-Table

`Get-Process | fl ProcessName, StartTime` 
`Get-Process | ft ProcessName, StartTime -Autosize` автоматическая группировка размера столбцов

### Measure-Object

`Get-Process | Measure | select Count` кол-во объектов 
`Get-Process | Measure -Line -Word -Character` кол-во строк, слов и Char объектов 
`Get-Process | Measure-Object PM -sum | Select-Object Count,@{Name="MEM_MB"; Expression={[int]($_.Sum/1mb)}}` кол-во процессов и общий объем занятой памяти в МБайт

### Compare-Object

`Compare-Object -ReferenceObject (Get-Content -Path .\file1.txt) -DifferenceObject (Get-Content -Path .\file2.txt)` сравнение двух файлов 
`$group1 = Get-ADGroupMember -Identity "Domain Admins"` 
`$group2 = Get-ADGroupMember -Identity "Enterprise Admins"` 
`Compare-Object -ReferenceObject $group1 -DifferenceObject $group2 -IncludeEqual` сравнение друх объектов
`==` нет изменений 
`<=` есть изменения в $group1 
`=>` есть изменения в $group2

### Where-Object (?)

`Get-Process | Where-Object {$_.ProcessName -match "zabbix"}` фильтрация/поиск процессов по имени свойства объекта 
`Get-Process | where CPU -gt 10 | Sort-Object -Descending CPU` вывести объекты, где значения CPU больше 10 
`Get-Process | where WS -gt 200MB` отобразить процессы где WS выше 200МБ 
`Get-Service | where Name -match "zabbix"` поиск службы 
`Get-Service -ComputerName $srv | Where {$_.Name -match "WinRM"} | Restart-Service` перезапустить службу на удаленном компьютере 
`(Get-Service).DisplayName` вывести значения свойства массива 
`netstat -an | where {$_ -match 443}` 
`netstat -an | ?{$_ -match 443}` 
`(netstat -an) -match 443`

### Sort-Object

`Get-Process | Sort-Object -Descending CPU | ft` обратная (-Descending) сортировка по CPU 
`Get-Process | Sort-Object -Descending cpu,ws` сортировка по двум свойствам 
`$path[-1..-10]` обратная сборка массива без сортировки 
`$arr = @(1..20); $arr[$($arr.Count - 1)..0]` пересобрать массив с конца

### Last/First

`Get-Process | Sort-Object -Descending CPU | select -First 10` вывести первых 10 объектов 
`Get-Process | Sort-Object -Descending CPU | select -Last 10` вывести последних 10 объектов

### Group-Object
```PowerShell
$Groups = Get-CimInstance -Class Win32_PnPSignedDriver |
Select-Object DriverProviderName, FriendlyName, Description, DriverVersion, DriverDate |
Group-Object DriverProviderName, FriendlyName, Description, DriverVersion, DriverDate
$(foreach ($Group in $Groups) {
    $Group.Group[0]
}) | Format-Table
```
### Property

`$srv.Count` кол-во элементов в массиве 
`$srv.Length` содержит количество символом строки переменной [string] или количество значений (строк) объекта 
`$srv.Chars(2)` отобразить 3-й символ в строке 
`$srv[2]` отобразить 3-ю строку в массиве

### Method

`$srv = "127.0.0.1"` 
`$srv.Insert(0,"https://")` добавить значение перед первым символом 
`$srv.Substring(4)` удалить (из всего массива) первые 4 символа 
`$srv.Remove(3)` удалить из всего массива все после 3 символа 
`$string = "123"` создать строку 
`$int = [convert]::ToInt32($string)` преобразовать строку в тип данных число 
`[string]::Concat($text,$num)` объеденить переменные в одну строку 
`[string]::Join(":",$text,$num)` объеденить используя разделитель 
`[string]::Compare($text,$num,$true)` выдает 0 при совпадении или 1/-1 при несовпадении, $true (без учета регистра) или $false (с учетом регистра) 
`[string]::Equals($text,$num)` производит сравнение двух строк и выдает $true при их совпадении или $false при несовпадении 
`[string]::IsNullOrEmpty($text)` проверяет наличие строки, если строка пуста $true, если нет $false 
`[string]::IsNullOrWhiteSpace($text2)` проверяет на наличие только символов пробел, табуляция или символ новой строки

## Error

`$Error` выводит все ошибки текущего сеанса 
`$Error[0].InvocationInfo` развернутый отчет об ошибке 
`$Error.clear()` 
`$LASTEXITCODE` результат выполнения последней команды (0 - успех) 
`exit 1` код завершения, который возвращается $LASTEXITCODE

### ExecutionStatus
```PowerShell
$(Get-History)[-1] | Select-Object @{
    Name="RunTime"; Expression={$_.EndExecutionTime - $_.StartExecutionTime}
},ExecutionStatus,CommandLine # посчитать время работы последней [-1] (или Select-Object -Last 1) выполненной команды и ее узнать статус
```
### Measure-Command

`$(Measure-Command {ping ya.ru}).TotalSeconds` получить время выполнения в секундах

## DateTime

`[DateTime]::UtcNow` время в формате UTC 0 
`$(Get-Date).AddHours(-3)` вычесть три часа из текущего времени 
`$Date = $(Get-Date -Format "dd/MM/yyyy HH:mm:ss")` изменить формат отображения времени 
`$Date = Get-Date -f "dd/MM/yyyy"` получаем тип данных [string] $($Date.GetType().Name) 
`$Date = "19.05.2024"` 
`[DateTime]$Date = Get-Date "$Date"` преобразовать строку подходящую под формат даты в тип данных [DateTime] 
`$BeforeDate = Get-Date "12.05.2024"` 
`[int32]$days=$($Date - $BeforeDate).Days` посчитать разницу в днях 
`"5/7/07" -as [DateTime]` преобразовать входные данные в тип данных [DateTime]

## TimeSpan

`New-TimeSpan -Start $(Get-Date) -End $($(Get-Date).AddMinutes(+1))` получить разницу во времени 
`$TimeZone = (Get-TimeZone).BaseUtcOffset.TotalMinutes` получить разницу в минутах от текущего часового пояса относительно UTC 0 
`$UnixTime  = (New-TimeSpan -Start (Get-Date "01/01/1970") -End ((Get-Date).AddMinutes(-$tz))).TotalSeconds` вычесть минуты для получения UTC 0 
`$TimeStamp = ([string]$UnixTime -replace "\..+") + "000000000"` получить текущий TimeStamp

### Format
```
HH   # Часы в 24-часовом формате (00 до 23)
hh   # Часы в 12-часовом формате (01 до 12)
mm   # Минуты (00 до 59)
ss   # Секунды (00 до 59)
tt   # Десигнатор (AM/PM)
fff  # Миллисекунды (000 до 999)
d    # День месяца без ведущего нуля (1-31)
dd   # День месяца с ведущим нулём (01-31)
ddd  # Сокращённое название дня недели (например, "Пн")
dddd # Полное название дня недели (например, "Понедельник")
M    # Номер месяца без ведущего нуля (1-12)
MM   # Номер месяца с ведущим нулём (01-12)
MMM  # Сокращённое название месяца (например, "Янв")
MMMM # Полное название месяца (например, "Январь")
y    # Год без века (0-99)
yy   # Год без века с ведущим нулём (00-99)
yyyy # Год с веком (например, 2024)
g    # Период или эра (например, "н.э.")
```
### Timer

`$start_time = Get-Date` зафиксировать время до выполнения команды 
`$end_time = Get-Date` зафиксировать время по завершению 
`$time = $end_time - $start_time` высчитать время работы скрипта 
`$min = $time.minutes` 
`$sec = $time.seconds` 
`Write-Host "$min минут $sec секунд"`

`$timer = [System.Diagnostics.Stopwatch]::StartNew()` запустить таймер 
`$timer.IsRunning` статус работы таймера 
`$timer.Elapsed.TotalSeconds` отобразить время с момента запуска (в секундах) 
`$timer.Stop()` остановить таймер

## Regex
```
.       # Обозначает любой символ, кроме новой строки
\       # Экранирует любой специальный символ (метасимвол). Используется, если нужно указать конкретный символ, вместо специального ({ } [ ] / \ + * . $ ^ | ?)
\A (^)  # Начало строки
\Z ($)  # Конец строки
\n      # Новая строка
\s      # Пробел (эквивалент " "), табуляция, перенос строки
\S      # Не пробел
\d      # Число от 0 до 9 (20-07-2022 эквивалент: "\d\d-\d\d-\d\d\d\d")
\D      # Обозначает любой символ, кроме числа (цифры). Удаления всех символов, кроме цифр: [int]$("123 test" -replace "\D")
\w      # Любая буква латиницы, цифра, или знак подчёркивания (от "a" до "z" и от "A" до "Z" или число от 0 до 9)
\W      # Не латиница, не цифра, не подчёркивание
\b      # Граница слова. Применяется когда нужно выделить, что искомые символы являются словом, а не частью другого слова
\B      # Не граница слова
\A      # Начало текста
\Z      # Конец текста
+       # Повторяется 1 и более раз (\s+)
|       # Или. Соединяет несколько вариантов
()      # В круглые скобки заключаются все комбинации с "или" и поиск начала и конца строк
[]      # поиск совпадения любой буквы, например, [A-z0-9] от A до z и цифры от 0 до 9 ("192.168.1.1" -match "192.1[6-7][0-9]")
[^ ]    # Исключает из поиска символы указанные в квадратных скобках
{ }     # Квантификатор в фигурных скобках, указывает количество повторений символа слева на право (от 1 до 25 раз)
\d{2}   # Найти две цифры
\d{2,4} # Найти две или четыре
{4,}    # Найти четыре и более
```
### Якори

`^` или `\A` определяет начало строки. $url -replace '^','https:'` добавить в начало; 
`$` или `\Z` обозначают конец строки. $ip -replace "\d{1,3}$","0" 
`(?=text)` поиск слова слева. Пишем слева на право от искомого (ищет только целые словосочетания) "Server:\s(.{1,30})\s(?=$username)" 
`(?<=text)` поиск слова справа. $in_time -replace ".+(?<=Last)"` удалить все до слова Last 
`(?!text)` не совпадает со словом слева 
`(?<!text)` не совпадает со словом справа

`$test = "string"` 
`$test -replace ".{1}$"` удалить любое кол-во символов в конце строки 
`$test -replace "^.{1}"` удалить любое кол-во символов в начале строки 

### Группы захвата

`$date = '12.31.2021'` 
`$date -replace '^(\d{2}).(\d{2})','$2.$1'` поменять местами 
`$1` содержимое первой группы в скобках 
`$2` содержимое второй группы

`-replace "1","2"` замена элементов в индексах массива (везде где присутствует 1, заменить на 2), для удаления используется только первое значение 
`-split " "` преобразовать строку в массив, разделителем указан пробел, которой удаляется ($url.Split("/")[-1]) 
`-join " "` преобразовать массив (коллекцию) в единую строку (string), добавить разделителем пробел

`@(1,2,3) -contains 3` проверить, что элемент справа содержится в массиве слева 
`@(1,2) -notcontains 3` проверить, что элемент справа не содержится в массиве слева

`-like *txt*` поиск по маскам wildcard, выводит значение на экран 
`-match txt` поиска по шаблонам, проверка на соответствие содержимого текста 
`-match "zabbix|rpc"` условия, для поиска по нескольким словам 
`-NotMatch` проверка на отсутствие вхождения 

### Matches

`"num: 777" -match "num: ([0-9]+)" | Out-Null` 
`$Matches[1]` выводим только номер

`$ip = "192.168.10.1"` 
`$ip -match "(\.\d{1,3})\.\d{1,2}"` True 
`$Matches` отобразить все подходящие переменные последнего поиска, которые входят и не входят в группы ()

`$String = "09/14/2017 12:00:27 - mtbill_post_201709141058.txt 7577_Delivered: OK"` 
`$String -Match ".*(?=\.txt)" | Out-Null` 
`$Matches[0][-4..-1] -Join ""`

`$string.Substring($string.IndexOf(".txt")-4, 4)` 2-й вариант (IndexOf)

### Форматирование (.NET method format)

`[string]::Format("{1} {0}","Index0","Index1")` 
`"{1} {0}" -f "Index0","Index1"` 
`"{0:###-###-###}" -f 1234567` записать число в другом формате (#) 
`"{0:0000}" -f 123` вывести число в формате не меньше 4 знаков (0123) 
`"{0:P0}" -f (220/1000)` посчитать в процентах (P) 
`"{0:P}" -f (512MB/1GB)` сколько % составляет 512Мб от 1Гб 
`"{0:0.0%}" -f 0.123` умножить на 100%
```PowerShell
$gp = Get-Process | sort cpu -Descending | select -First 10
foreach ($p in $gp) {
    "{0} - {1:N2}" -f $p.processname, $p.cpu # округлить
}
```
### Условный оператор
```PowerShell
$rh = Read-Host
if ($rh -eq 1) {
    ipconfig
} elseif (
    $rh -eq 2
) {
    getmac
} else {
    hostname
}
```
Если условие if () является истенным ($True), выполнить действие в {} 
Если условие if () является ложным ($False), выполнить действие не обязательного оператора else 
Условие Elseif идёт после условия if для проверки дополнительных условий перед выполнение оператора else. Оператор, который первый вернет $True, отменит выполнение следующих дополнительных условий 
Если передать переменную в условие без оператора, то будет проверяться наличие значения у переменной на $True/$False 
```PowerShell
if ($(Test-NetConnection $srv -Port 80).TcpTestSucceeded) {
    "Opened port"
} else {
    "Closed port"
}
```
### Логические операторы сравнения

`-eq` равно (equal) 
`-ceq` учитывать регистр 
`-ne` не равно (not equal) 
`-cne` не равно учитывая регистр 
`-gt` больше (greater) 
`-ge` больше или равно 
`-lt` меньше (less) 
`-le` меньше или равно 
`-in` проверить на наличие (5 -in @(1,2,3,4,5)) 
`-NOT` логическое НЕТ !(Test-Path $path) 
`-and` логическое И 
`-or` логическое ИЛИ 
```PowerShell
if ((($1 -eq 1) -and ($2 -eq 2)) -or ($1 -ne 3)) {
    $true
} else {
    $false
} # два условия: (если $1 = 1 И $2 = 2) ИЛИ $1 не равно 3 вернуть $true
```
### Pipeline Operators

`Write-Output "First" && Write-Output "Second"` две успешные команды выполняются 
`Write-Error "Bad" && Write-Output "Second"` первая команда завершается ошибкой, из-за чего вторая команда не выполняется 
`Write-Error "Bad" || Write-Output "Second"` первая команда завершается ошибкой, поэтому выполняется вторая команда 
`Write-Output "First" || Write-Output "Second"` первая команда выполнена успешно, поэтому вторая команда не выполняется

### Invocation Operator

`$addr = "8.8.8.8"` 
`$ping = "ping"` 
`& $ping $addr` запускает текст как команду

`& $ping $addr &` запустить команду в фоне 
`(Get-Job)[-1] | Receive-Job -Keep`

## DataType

`$srv.GetType()` узнать тип данных 
`$srv -is [string]` проверка на соответствие типа данных 
`$srv -isnot [System.Object]` проверка на несоответствие 
`[Object]` массив (BaseType:System.Array) 
`[DateTime]` формат времени (BaseType:System.ValueType) 
`[Bool]/[Boolean]` логическое значение ($True/$False) или 1/0 (1 бит) наличие/отсуствие напряжения 
`[Byte]` 8-битное (1 байт) целое число без знака (0..255) 
`[Int16]` 16-битное знаковое целое число от -32767 до 32767 (тип данных WORD 0..65535) 
`[Int]` 32-битное (4 байта) знаковое целое число от –2147483648 до 2147483647 (DWORD) 
`[Int64]` 64-битное от -9223372036854775808 до 9223372036854775808 (LWORD) 
`[Decimal]` 128-битное десятичное значение от –79228162514264337593543950335 до 79228162514264337593543950335 
`[Single]` число с плавающей запятой (32-разрядное) 
`[Double]` число с плавающей запятой с двойной точностью (64-разрядное) 
`[String]` неизменяемая строка символов Юникода фиксированной длины (BaseType:System.Object)

### Math

`[math] | Get-Member -Static` 
`[math]::Pow(2,4)` 2 в 4 степени 
`[math]::Truncate(1.8)` грубое округление, удаляет дробную часть 
`[math]::Ceiling(1.8)` округляет число в большую сторону до ближайшего целого значения 
`[math]::Floor(-1.8)` округляет число в меньшую сторону 
`[math]::Min(33,22)` возвращает наименьшее значение двух значений 
`[math]::Max(33,22)` возвращает наибольшее значение двух значений

### Round

`[double]::Round(87.5, 0)` 88 (нечетное), в .NET по умолчанию используется округление в средней точке ToEven, где *.5 значения округляются до ближайшего четного целого числа 
`[double]::Round(88.5, 0)` 88 (четное) 
`[double]::Round(88.5, 0, 1)` 89 (округлять в большую сторону) 
`[double]::Round(1234.56789, 2)` округлить до 2 символов после запятой

### ToString

`(4164539/1MB).ToString("0.00")` разделить на дважды на 1024/1024 и округлить до 3,97

### Char

`[Char]` cимвол Юникода (16-разрядный) 
`$char = $srv.ToCharArray()` разбить строку [string] на массив [System.Array] из букв

### Switch function
```PowerShell
$MMM = Get-Date -UFormat "%m"
switch($MMM) {
    "01" {$Month = 'Jan'}
    "02" {$Month = 'Feb'}
    "03" {$Month = 'Mar'}
    "04" {$Month = 'Apr'}
    "05" {$Month = 'May'}
    "06" {$Month = 'Jun'}
    "07" {$Month = 'Jul'}
    "08" {$Month = 'Aug'}
    "09" {$Month = 'Sep'}
    "10" {$Month = 'Oct'}
    "11" {$Month = 'Nov'}
    "12" {$Month = 'Dec'}
}
```
### Switch param
```PowerShell
Function fun-switch (
    [switch]$param
) {
    If ($param) {"yes"} else {"no"}
}
```
`fun-switch -param`

## Bit
```
Двоичное    Десятичное
1           1
10          2
11          3
100         4
101         5
110         6
111         7
1000        8
1001        9
1010        10
1011        11
1100        12
1101        13
1110        14
1111        15
1 0000      16

Двоичное    Десятичное  Номер разряда
1           1           0
10          2           1
100         4           2
1000        8           3
1 0000      16          4
10 0000     32          5
100 0000    64          6
1000 0000   128         7
1 0000 0000 256         8

Из двоичного => десятичное (1-й вариант по таблице)
1001 0011 = 1000 0000 + 1 0000 + 10 + 1 = 128 + 16 + 2 + 1 = 147

2-й вариант
7654 3210 (разряды двоичного выражения) = (1*2^7)+(0*2^6)+(0*2^5)+(1*2^4)+(0*2^3)+(0*2^2)+(1*2^1)+(1*2^0) = 147
[math]::Pow(2,7) + [math]::Pow(2,4) + [math]::Pow(2,1) + [math]::Pow(2,0) = 147` исключить 0 и сложить степень

Из десятичного => двоичное (1-й вариант по таблице)
347 вычесть ближайшие 256 = 91 (+ 1 0000 0000 забрать двоичный остаток)
91  - 64  = 27 ближайшее 16 (+ 100 0000)
27  - 16  = 11 ближайшее 8 (+ 1 0000)
11  - 8   = 3  ближайшее 2 (+ 1000)
3   - 2   = 1 (+ 10)
1   - 1   = 0 (+ 1)
1 0101 1011

2-й вариант
Последовательное деления числа на 2, предворительно забирая остаток для получения четного числа в меньшую сторону
347 - 346 = остаток 1, (347-1)/2 = 173
173 - 172 = остаток 1, (172-1)/2 = 86
86  - 86  = остаток 0, 86/2 = 43
43  - 42  = остаток 1, (43-1)/2 = 21
21  - 20  = остаток 1, (21-1)/2 = 10
10  - 10  = остаток 0, 10/2 = 5
5   - 4   = остаток 1, (5-1)/2 = 2
2   - 2   = остаток 0, 2/2 = 1
1   - 2   = остаток 1, (1-1)/2 = 0
Результат деления записывается снизу вверх
```
### Bit Convertor
```PowerShell
function ConvertTo-Bit {
    param (
        [Int]$int
    )
    [array]$bits = @()
    $test = $true
    while ($test -eq $true) {
        if (($int/2).GetType() -match [double]) {
            $int = ($int-1)/2
            [array]$bits += 1
        }
        elseif (($int/2).GetType() -match [int]) {
            $int = $int/2
            [array]$bits += 0
        }
        if ($int -eq 0) {
            $test = $false
        }
    }
    $bits =  $bits[-1..-999]
    ([string]($bits)) -replace "\s"
}
```
`ConvertTo-Bit 347`
```PowerShell
function ConvertFrom-Bit {
    param (
        $bit
    )
    [int]$int = 0
    $bits = $bit.ToString().ToCharArray()
    $index = ($bits.Count)-1
    foreach ($b in $bits) {
        if ($b -notlike 0) {
            $int += [math]::Pow(2,$index)
        }
    $index -= 1
    }
    $int
}
```
`ConvertFrom-Bit 10010011`

`Get-Process pwsh | fl ProcessorAffinity` привязка процесса к ядрам, представляет из себя битовую маску (bitmask), где каждому биту соответствует ядро процессора. Если для ядра отмечено сходство (affinity), то бит выставляется в 1, если нет — то в 0. Например, если выбраны все 16 ядер, то это 1111 1111 1111 1111 или 65535. 
`$(Get-Process pwsh).ProcessorAffinity = 15` 0000000000001111 присвоить 4 первых ядра 
`$(Get-Process pwsh).ProcessorAffinity = 61440` 1111000000000000 присвоить 4 последних ядра 
`$(Get-Process pwsh).ProcessorAffinity = (ConvertFrom-Bit 1111000000000000)`

## Cycle

### Foreach

`$list = 100..110` создать массив из цифр от 100 до 110 
`foreach ($srv in $list) {ping 192.168.3.$srv -n 1 -w 50}` $srv хранит текущий элемент из $list и повторяет команду до последнего элемента в массиве 
`$foreach.Current` текущий элемент в цикле 
`$foreach.Reset()` обнуляет итерацию, перебор начнется заново, что приводит к бесконечному циклу 
`$foreach.MoveNext()` переход к следующему элементу в цикле

### ForEach-Object (%)
```PowerShell
100..110 | %{
    ping -n 1 -w 50 192.168.3.$_ > $null
    if ($LastExitCode -eq 0) {
        Write-Host "192.168.3.$_" -ForegroundColor green
    } else {
    Write-Host "192.168.3.$_"-ForegroundColor Red
    }
}
```
`% ` передать цикл через конвеер (ForEach-Object) 
`$_` переменная цикла и конвеера ($PSItem) 
`gwmi Win32_QuickFixEngineering | where {$_.InstalledOn.ToString() -match "2022"} | %{($_.HotFixID.Substring(2))}` gwmi создает массив, вывод команды передается where для поиска подходящих под критерии объектов. По конвееру передается в цикл для удаления первых (2) символов методом Substring из всех объектов HotFixID.

### While
```PowerShell
$srv = "yandex.ru"
$out2 = "Есть пинг"
$out3 = "Нет пинга"
$out = $false # предварительно сбросить переменную, While проверяет условие до запуска цикла
While ($out -eq $false) { # пока условие является $true, цикл будет повторяться
    $out = ping -n 1 -w 50 $srv
    if ($out -match "ttl") {$out = $true; $out2} else {$out = $false; $out3; sleep 1}
}

while ($True) { # запустить бесконечный цикл
    $result = ping yandex.ru -n 1 -w 50
    if ($result -match "TTL") { # условие, при котором будет выполнен break
        Write-Host "Сайт доступен"
        break # остановит цикл
    } else {
        Write-Host "Сайт недоступен"; sleep 1
    }
}
```
### Try-Catch-Finally
```PowerShell
Try {$out = pping 192.168.3.1}
Catch {Write-Warning "$($error[0])"} # выводит в случае ошибки (вместо ошибки)
finally {$out = "End"} # выполняется в конце в любом случае
```
### Out-Gridview
`Get-Service -cn $srv | Out-GridView -Title "Service $srv" -OutputMode Single –PassThru | Restart-Service` перезапустить выбранную службу

### Out-File
`Read-Host –AsSecureString | ConvertFrom-SecureString | Out-File "$env:userprofile\desktop\password.txt"` писать в файл. Преобразовать пароль в формат SecureString с использованием шифрования Windows Data Protection API (DPAPI)

### Get-Content (gc/cat/type)
`$password = gc "$env:userprofile\desktop\password.txt" | ConvertTo-SecureString` читать хэш пароля из файла с помощью ключей, хранящихся в профиле текущего пользователя, который невозможно прочитать на другом копьютере

### AES Key
`$AESKey = New-Object Byte[] 32` 
`[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey)` 
`$AESKey | Out-File "C:\password.key"` 
`$Cred.Password | ConvertFrom-SecureString -Key (Get-Content "C:\password.key") | Set-Content "C:\password.txt"` сохранить пароль в файл используя внешний ключ 
`$pass = Get-Content "C:\password.txt" | ConvertTo-SecureString -Key (Get-Content "\\Server\Share\password.key")` расшифровать пароль на втором компьютере
