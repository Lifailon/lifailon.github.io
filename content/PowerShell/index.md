+++
title = "PowerShell Syntax"
[extra]
toc = true
toc_sidebar = true
+++

<p align="center">
    <a href="https://github.com/Lifailon/PS-Commands"><img title="PS-Commands Logo"src="PS-Commands-Logo.png"></a>
</p>

<p align="center">
    Синтаксис и работа с модулями в <b>PowerShell</b>.
</p>

---

<p align="center">
<a href="https://github.com/Lifailon/PS-Commands"><img title="PowerShell Cheat Sheet RU"src="PowerShell-Cheat-Sheet-RU.jpg"></a>
</p>

---

# Help

`Get-Verb` действия/глаголы, утвержденные для использования в командлетах \
`Get-Command *Language*` поиск команды по имени \
`(Get-Command Get-Language).Module` узнать к какому модулю принадлежит команда \
`Get-Command Get-Content | fl Module,DLL` узнать принадлежность команды к модулю и dll \
`Get-Command -Module LanguagePackManagement` отобразить список команд указанного модуля \
`(Get-Module LanguagePackManagement).ExportedCommands.Values` отобразить список команд указанного модуля \
`Get-Language | Get-Member` отобразить список методов команды (действия), объекты вывода и Event (события объектов: Click) \
`(Get-Help Get-Service).Aliases` узнать псевдонимом команды \
`Get-Alias gsv` узнать имя команды по псевдониму \
`Get-Help Get-Service` синтаксис \
`Get-Help Get-Service -Parameter *` описание всех параметров \
`Get-Help Get-Service -Online` \
`Get-Help Get-Service -ShowWindow` описание параметров в GUI с фильтрацией \
`Show-Command` вывести список команд в GUI \
`Show-Command Get-Service` список параметров команды в GUI \
`Invoke-Expression` iex принимает текст для выполнения в консоли как команды \
`$PSVersionTable` текущая версия PowerShell \
`Set-ExecutionPolicy Unrestricted` \
`Get-ExecutionPolicy` \
`$Metadata = New-Object System.Management.Automation.CommandMetaData (Get-Command Get-Service)` получить информацию о командлете \
`[System.Management.Automation.ProxyCommand]::Create($Metadata)` исходный код функции

# Object

### Variable

`$var = Read-Host "Enter"` ручной ввод \
`$pass = Read-Host "Enter Password" -AsSecureString` скрывать набор \
`$global:path = "\\path"` задать глобальную переменную, например в функции \
`$using:srv` использовать переменную текущей сесси в Invoke-сессии \
`Get-Variable` отобразить все переменные \
`ls variable:/` отобразить все переменные \
`Get-Variable *srv*` найти переменную по имени \
`Get-Variable -Scope Global` отобразить все глобальные переменные \
`Get-Variable Error` последняя команда с ошибкой \
`Remove-Variable -Name *` очистить все переменные \
`$LASTEXITCODE` содержит код вывода последней запущенной программы, например ping. Если код возврата положительный (True), то $LastExitCode = 0

### ENV

`Get-ChildItem Env:` отобразить все переменные окружения \
`$env:PSModulePath` директории импорта модулей \
`$env:userprofile` \
`$env:computername` \
`$env:username` \
`$env:userdnsdomain` \
`$env:logonserver` \
`([DirectoryServices.ActiveDirectory.Forest]::GetCurrentForest()).Name` \
`[Environment]::GetFolderPath('ApplicationData')`

### History

`Get-History` история команд текущей сессии \
`(Get-History)[-1].Duration.TotalSeconds` время выполнения последней команды \
`(Get-PSReadLineOption).HistorySavePath` путь к сохраненному файлу с 4096 последних команд (из модуля PSReadLine) \
`Get-Content (Get-PSReadlineOption).HistorySavePath | Select-String Get` поиск по содержимому файла (GREP) \
`Set-PSReadlineOption -MaximumHistoryCount 10000` изменить количество сохраняемых команд в файл \
`Get-PSReadLineOption | select MaximumHistoryCount` \
`Set-PSReadlineOption -HistorySaveStyle SaveNothing` отключить ведение журнала \
`F2` переключиться с InlineView на ListView

### Clipboard

`Set-Clipboard $srv` скопировать в буфер обмена \
`Get-Clipboard` вставить

### Write-Host
```PowerShell
Write-Host -BackgroundColor Green "Test:" -NoNewline # изменить цвет фона и запретить перенос строки
Write-Host " True" -ForegroundColor Green # данная строка будет печататься продолжая предыдущую с новыми параметрами цвета (фон по умолчанию, изменяем цвет текста)
```
`Write-Error "False"` \
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

`$srv = @("server-01", "server-02")`  создать массив \
`$srv += @("server-03")` добавить в массив новый элемент \
`$srv.Count` отобразить кол-во элементов в массиве \
`Out-String` построчный вывод

### Index

`$srv[0]` вывести первое значение элемента массива \
`$srv[0] = Name` замена элемента в массиве \
`$srv[0].Length` узнать кол-во символов первого значения в массиве \
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
`$hashtable.Keys` список всех ключей \
`$hashtable["User"]` получить значение (Values) по ключу \
`$hashtable["User"] = "Test"` изменить \
`$hashtable.Remove("User")` удалить ключ

### Collections
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

`$CustomObject | Add-Member –MemberType NoteProperty –Name Arr –Value @(1,2,3)` добавить свойство/стобец \
`$CustomObject.Arr = @(1,3,5)` изменить содержимое \
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
`$Class = New-Object -TypeName CustomClass` \
`$Class.User = $env:username` \
`$Class.Server = $env:computername` \
`$Class.Start(1)`

### Pipeline

`$CustomObject | Add-Member -MemberType NoteProperty -Name "Type" -Value "user" -Force` добавление объкта вывода NoteProperty \
`$CustomObject | Add-Member -MemberType NoteProperty -Name "User" -Value "admin" -Force` изменеие содержимого для сущности объекта User \
`ping $srv | Out-Null` перенаправить результат вывода в Out-Null

### Select-Object

`Get-Process | Select-Object -Property *` отобразить все доступные объекты вывода \
`Get-Process | select -Unique "Name"` удалить повторяющиеся значения в массиве \
`Get-Process | select -ExpandProperty ProcessName` преобразовать из объекта-коллекции в массив (вывести содержимое без наименовая столбца) \
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

`$(ipconfig | Select-String IPv4) -replace ".+: " | Where-Object {$_ -match "^172."}` узнать только IP \
`$Current_IP = Get-Content $RDCMan_RDG_PATH | Select-String $RDCMan_Display_Name -Context 0,1` получить две строки \
`$Current_IP = $Current_IP.Context.DisplayPostContext[0] -replace ".+<name>|<\/name>"` забрать только вторую строку и удалить тэги

### Format

`Get-Process | fl ProcessName, StartTime` \
`Get-Process | ft ProcessName, StartTime -Autosize` автоматическая группировка размера столбцов

### Measure-Object

`Get-Process | Measure | select Count` кол-во объектов \
`Get-Process | Measure -Line -Word -Character` кол-во строк, слов и Char объектов \
`Get-Process | Measure-Object PM -sum | Select-Object Count,@{Name="MEM_MB"; Expression={[int]($_.Sum/1mb)}}` кол-во процессов и общий объем занятой памяти в МБайт

### Compare-Object

`Compare-Object -ReferenceObject (Get-Content -Path .\file1.txt) -DifferenceObject (Get-Content -Path .\file2.txt)` сравнение двух файлов \
`$group1 = Get-ADGroupMember -Identity "Domain Admins"` \
`$group2 = Get-ADGroupMember -Identity "Enterprise Admins"` \
`Compare-Object -ReferenceObject $group1 -DifferenceObject $group2 -IncludeEqual` сравнение друх объектов
`==` нет изменений \
`<=` есть изменения в $group1 \
`=>` есть изменения в $group2

### Where-Object

`Get-Process | Where-Object {$_.ProcessName -match "zabbix"}` фильтрация/поиск процессов по имени свойства объекта \
`Get-Process | where CPU -gt 10 | Sort-Object -Descending CPU` вывести объекты, где значения CPU больше 10 \
`Get-Process | where WS -gt 200MB` отобразить процессы где WS выше 200МБ \
`Get-Service | where Name -match "zabbix"` поиск службы \
`Get-Service -ComputerName $srv | Where {$_.Name -match "WinRM"} | Restart-Service` перезапустить службу на удаленном компьютере \
`(Get-Service).DisplayName` вывести значения свойства массива \
`netstat -an | where {$_ -match 443}` \
`netstat -an | ?{$_ -match 443}` \
`(netstat -an) -match 443`

### Sort-Object

`Get-Process | Sort-Object -Descending CPU | ft` обратная (-Descending) сортировка по CPU \
`Get-Process | Sort-Object -Descending cpu,ws` сортировка по двум свойствам \
`$path[-1..-10]` обратная сборка массива без сортировки \
`$arr = @(1..20); $arr[$($arr.Count - 1)..0]` пересобрать массив с конца

### Last and First

`Get-Process | Sort-Object -Descending CPU | select -First 10` вывести первых 10 объектов \
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

`$srv.Count` кол-во элементов в массиве \
`$srv.Length` содержит количество символом строки переменной [string] или количество значений (строк) объекта \
`$srv.Chars(2)` отобразить 3-й символ в строке \
`$srv[2]` отобразить 3-ю строку в массиве

### Method

`$srv = "127.0.0.1"` \
`$srv.Insert(0,"https://")` добавить значение перед первым символом \
`$srv.Substring(4)` удалить (из всего массива) первые 4 символа \
`$srv.Remove(3)` удалить из всего массива все после 3 символа \
`$string = "123"` создать строку \
`$int = [convert]::ToInt32($string)` преобразовать строку в тип данных число \
`[string]::Concat($text,$num)` объеденить переменные в одну строку \
`[string]::Join(":",$text,$num)` объеденить используя разделитель \
`[string]::Compare($text,$num,$true)` выдает 0 при совпадении или 1/-1 при несовпадении, $true (без учета регистра) или $false (с учетом регистра) \
`[string]::Equals($text,$num)` производит сравнение двух строк и выдает $true при их совпадении или $false при несовпадении \
`[string]::IsNullOrEmpty($text)` проверяет наличие строки, если строка пуста $true, если нет $false \
`[string]::IsNullOrWhiteSpace($text2)` проверяет на наличие только символов пробел, табуляция или символ новой строки

# Error

`$Error` выводит все ошибки текущего сеанса \
`$Error[0].InvocationInfo` развернутый отчет об ошибке \
`$Error.clear()` \
`$LASTEXITCODE` результат выполнения последней команды (0 - успех) \
`exit 1` код завершения, который возвращается $LASTEXITCODE

### ExecutionStatus
```PowerShell
$(Get-History)[-1] | Select-Object @{
    Name="RunTime"; Expression={$_.EndExecutionTime - $_.StartExecutionTime}
},ExecutionStatus,CommandLine # посчитать время работы последней [-1] (или Select-Object -Last 1) выполненной команды и ее узнать статус
```
### Measure-Command

`$(Measure-Command {ping ya.ru}).TotalSeconds` получить время выполнения в секундах

# DateTime

`[DateTime]::UtcNow` время в формате UTC 0 \
`$(Get-Date).AddHours(-3)` вычесть три часа из текущего времени \
`$Date = $(Get-Date -Format "dd/MM/yyyy HH:mm:ss")` изменить формат отображения времени \
`$Date = Get-Date -f "dd/MM/yyyy"` получаем тип данных [string] $($Date.GetType().Name) \
`$Date = "19.05.2024"` \
`[DateTime]$Date = Get-Date "$Date"` преобразовать строку подходящую под формат даты в тип данных [DateTime] \
`$BeforeDate = Get-Date "12.05.2024"` \
`[int32]$days=$($Date - $BeforeDate).Days` посчитать разницу в днях \
`"5/7/07" -as [DateTime]` преобразовать входные данные в тип данных [DateTime]

# TimeSpan

`New-TimeSpan -Start $(Get-Date) -End $($(Get-Date).AddMinutes(+1))` получить разницу во времени \
`$TimeZone = (Get-TimeZone).BaseUtcOffset.TotalMinutes` получить разницу в минутах от текущего часового пояса относительно UTC 0 \
`$UnixTime  = (New-TimeSpan -Start (Get-Date "01/01/1970") -End ((Get-Date).AddMinutes(-$tz))).TotalSeconds` вычесть минуты для получения UTC 0 \
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

`$start_time = Get-Date` зафиксировать время до выполнения команды \
`$end_time = Get-Date` зафиксировать время по завершению \
`$time = $end_time - $start_time` высчитать время работы скрипта \
`$min = $time.minutes` \
`$sec = $time.seconds` \
`Write-Host "$min минут $sec секунд"`

`$timer = [System.Diagnostics.Stopwatch]::StartNew()` запустить таймер \
`$timer.IsRunning` статус работы таймера \
`$timer.Elapsed.TotalSeconds` отобразить время с момента запуска (в секундах) \
`$timer.Stop()` остановить таймер

# Regex
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
- Якори

`^` или `\A` определяет начало строки. $url -replace '^','https:'` добавить в начало; \
`$` или `\Z` обозначают конец строки. $ip -replace "\d{1,3}$","0" \
`(?=text)` поиск слова слева. Пишем слева на право от искомого (ищет только целые словосочетания) "Server:\s(.{1,30})\s(?=$username)" \
`(?<=text)` поиск слова справа. $in_time -replace ".+(?<=Last)"` удалить все до слова Last \
`(?!text)` не совпадает со словом слева \
`(?<!text)` не совпадает со словом справа

`$test = "string"` \
`$test -replace ".{1}$"` удалить любое кол-во символов в конце строки \
`$test -replace "^.{1}"` удалить любое кол-во символов в начале строки \

- Группы захвата

`$date = '12.31.2021'` \
`$date -replace '^(\d{2}).(\d{2})','$2.$1'` поменять местами \
`$1` содержимое первой группы в скобках \
`$2` содержимое второй группы

`-replace "1","2"` замена элементов в индексах массива (везде где присутствует 1, заменить на 2), для удаления используется только первое значение \
`-split " "` преобразовать строку в массив, разделителем указан пробел, которой удаляется ($url.Split("/")[-1]) \
`-join " "` преобразовать массив (коллекцию) в единую строку (string), добавить разделителем пробел

`@(1,2,3) -contains 3` проверить, что элемент справа содержится в массиве слева \
`@(1,2) -notcontains 3` проверить, что элемент справа не содержится в массиве слева

`-like *txt*` поиск по маскам wildcard, выводит значение на экран \
`-match txt` поиска по шаблонам, проверка на соответствие содержимого текста \
`-match "zabbix|rpc"` условия, для поиска по нескольким словам \
`-NotMatch` проверка на отсутствие вхождения \

- Matches

`"num: 777" -match "num: ([0-9]+)" | Out-Null` \
`$Matches[1]` выводим только номер

`$ip = "192.168.10.1"` \
`$ip -match "(\.\d{1,3})\.\d{1,2}"` True \
`$Matches` отобразить все подходящие переменные последнего поиска, которые входят и не входят в группы ()

`$String = "09/14/2017 12:00:27 - mtbill_post_201709141058.txt 7577_Delivered: OK"` \
`$String -Match ".*(?=\.txt)" | Out-Null` \
`$Matches[0][-4..-1] -Join ""`

`$string.Substring($string.IndexOf(".txt")-4, 4)` 2-й вариант (IndexOf)

- Форматирование (.NET method format)

`[string]::Format("{1} {0}","Index0","Index1")` \
`"{1} {0}" -f "Index0","Index1"` \
`"{0:###-##-##}" -f 1234567` записать число в другом формате (#) \
`"{0:0000}" -f 123` вывести число в формате не меньше 4 знаков (0123) \
`"{0:P0}" -f (220/1000)` посчитать в процентах (P) \
`"{0:P}" -f (512MB/1GB)` сколько % составляет 512Мб от 1Гб \
`"{0:0.0%}" -f 0.123` умножить на 100%
```PowerShell
$gp = Get-Process | sort cpu -Descending | select -First 10
foreach ($p in $gp) {
    "{0} - {1:N2}" -f $p.processname, $p.cpu # округлить
}
```
- Условный оператор
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
Если условие if () является истенным ($True), выполнить действие в {} \
Если условие if () является ложным ($False), выполнить действие не обязательного оператора else \
Условие Elseif идёт после условия if для проверки дополнительных условий перед выполнение оператора else. Оператор, который первый вернет $True, отменит выполнение следующих дополнительных условий \
Если передать переменную в условие без оператора, то будет проверяться наличие значения у переменной на $True/$False \
```PowerShell
if ($(Test-NetConnection $srv -Port 80).TcpTestSucceeded) {
    "Opened port"
} else {
    "Closed port"
}
```
- Логические операторы сравнения

`-eq` равно (equal) \
`-ceq` учитывать регистр \
`-ne` не равно (not equal) \
`-cne` не равно учитывая регистр \
`-gt` больше (greater) \
`-ge` больше или равно \
`-lt` меньше (less) \
`-le` меньше или равно \
`-in` проверить на наличие (5 -in @(1,2,3,4,5)) \
`-NOT` логическое НЕТ !(Test-Path $path) \
`-and` логическое И \
`-or` логическое ИЛИ \
```PowerShell
if ((($1 -eq 1) -and ($2 -eq 2)) -or ($1 -ne 3)) {
    $true
} else {
    $false
} # два условия: (если $1 = 1 И $2 = 2) ИЛИ $1 не равно 3 вернуть $true
```
- Pipeline Operators

`Write-Output "First" && Write-Output "Second"` две успешные команды выполняются \
`Write-Error "Bad" && Write-Output "Second"` первая команда завершается ошибкой, из-за чего вторая команда не выполняется \
`Write-Error "Bad" || Write-Output "Second"` первая команда завершается ошибкой, поэтому выполняется вторая команда \
`Write-Output "First" || Write-Output "Second"` первая команда выполнена успешно, поэтому вторая команда не выполняется

- Invocation Operator

`$addr = "8.8.8.8"` \
`$ping = "ping"` \
`& $ping $addr` запускает текст как команду

`& $ping $addr &` запустить команду в фоне \
`(Get-Job)[-1] | Receive-Job -Keep`

# DataType

`$srv.GetType()` узнать тип данных \
`$srv -is [string]` проверка на соответствие типа данных \
`$srv -isnot [System.Object]` проверка на несоответствие \
`[Object]` массив (BaseType:System.Array) \
`[DateTime]` формат времени (BaseType:System.ValueType) \
`[Bool]/[Boolean]` логическое значение ($True/$False) или 1/0 (1 бит) наличие/отсуствие напряжения \
`[Byte]` 8-битное (1 байт) целое число без знака (0..255) \
`[Int16]` 16-битное знаковое целое число от -32767 до 32767 (тип данных WORD 0..65535) \
`[Int]` 32-битное (4 байта) знаковое целое число от –2147483648 до 2147483647 (DWORD) \
`[Int64]` 64-битное от -9223372036854775808 до 9223372036854775808 (LWORD) \
`[Decimal]` 128-битное десятичное значение от –79228162514264337593543950335 до 79228162514264337593543950335 \
`[Single]` число с плавающей запятой (32-разрядное) \
`[Double]` число с плавающей запятой с двойной точностью (64-разрядное) \
`[String]` неизменяемая строка символов Юникода фиксированной длины (BaseType:System.Object)

### Math

`[math] | Get-Member -Static` \
`[math]::Pow(2,4)` 2 в 4 степени \
`[math]::Truncate(1.8)` грубое округление, удаляет дробную часть \
`[math]::Ceiling(1.8)` округляет число в большую сторону до ближайшего целого значения \
`[math]::Floor(-1.8)` округляет число в меньшую сторону \
`[math]::Min(33,22)` возвращает наименьшее значение двух значений \
`[math]::Max(33,22)` возвращает наибольшее значение двух значений

### Round

`[double]::Round(87.5, 0)` 88 (нечетное), в .NET по умолчанию используется округление в средней точке ToEven, где *.5 значения округляются до ближайшего четного целого числа \
`[double]::Round(88.5, 0)` 88 (четное) \
`[double]::Round(88.5, 0, 1)` 89 (округлять в большую сторону) \
`[double]::Round(1234.56789, 2)` округлить до 2 символов после запятой

### ToString

`(4164539/1MB).ToString("0.00")` разделить на дважды на 1024/1024 и округлить до 3,97

### Char

`[Char]` cимвол Юникода (16-разрядный) \
`$char = $srv.ToCharArray()` разбить строку [string] на массив [System.Array] из букв

# Function

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

### psm1 (module file and parameters)
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
`Get-Function -Text Text1` \
`Get-Function -Text Text2 -Provider Test2 -Number 3`

### psd1 (module description file)
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
# Bit
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

`Get-Process pwsh | fl ProcessorAffinity` привязка процесса к ядрам, представляет из себя битовую маску (bitmask), где каждому биту соответствует ядро процессора. Если для ядра отмечено сходство (affinity), то бит выставляется в 1, если нет — то в 0. Например, если выбраны все 16 ядер, то это 1111 1111 1111 1111 или 65535. \
`$(Get-Process pwsh).ProcessorAffinity = 15` 0000000000001111 присвоить 4 первых ядра \
`$(Get-Process pwsh).ProcessorAffinity = 61440` 1111000000000000 присвоить 4 последних ядра \
`$(Get-Process pwsh).ProcessorAffinity = (ConvertFrom-Bit 1111000000000000)`

# Cycle

### Foreach

`$list = 100..110` создать массив из цифр от 100 до 110 \
`foreach ($srv in $list) {ping 192.168.3.$srv -n 1 -w 50}` $srv хранит текущий элемент из $list и повторяет команду до последнего элемента в массиве \
`$foreach.Current` текущий элемент в цикле \
`$foreach.Reset()` обнуляет итерацию, перебор начнется заново, что приводит к бесконечному циклу \
`$foreach.MoveNext()` переход к следующему элементу в цикле

### ForEach-Object
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
`% ` передать цикл через конвеер (ForEach-Object) \
`$_` переменная цикла и конвеера ($PSItem) \
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
# Files

`Get-Content $home/desktop\test.txt -Wait` аналог tail \
`Test-Path $path` проверить доступность пути \
`Get-FileHash -Algorithm SHA256 "$path"` узнать хэш файла по алгоритму sha256 \
`Get-ChildItem $path -Filter *.txt -Recurse` отобразить содержимое каталога (Alias: ls/gci/dir) и дочерних каталогов (-Recurse) и отфильтровать вывод \
`Get-Location` отобразить текущие месторасположение (Alias: pwd/gl) \
`Set-Location $path` перемещение по каталогам (Alias: cd/sl) \
`Invoke-Item $path` открыть файл (Alias: ii/start) \
`Get-ItemProperty $env:userprofile\Documents\dns-list.txt | select FullName,Directory,Name,BaseName,Extension` свойтсва файла \
`Get-ItemProperty -Path $path\* | select FullName,CreationTime,LastWriteTime` свойства файлов содержимого директории, дата их создания и последнего изменения \
`New-Item -Path "C:\test\" -ItemType "Directory"` создать директорию (Alias: mkdir/md) \
`New-Item -Path "C:\test\file.txt" -ItemType "File" -Value "Добавить текст в файл"` создать файл \
`"test" > "C:\test\file.txt"` заменить содержимое \
`"test" >> "C:\test\file.txt"` добавить строку в файл \
`New-Item -Path "C:\test\test\file.txt" -Force` ключ используется для создания отсутствующих в пути директорий или перезаписи файла если он уже существует \
`Move-Item` перемещение объектов (Alias: mv/move) \
`Remove-Item "$path\" -Recurse` удаление всех файлов внутри каталога, без запроса подверждения (Alias: rm/del) \
`Remove-Item $path -Recurse -Include "*.txt","*.temp" -Exclude "log.txt"` удалить все файлы с расширением txt и temp ([Array]), кроме log.txt \
`Rename-Item "C:\test\*.*" "*.jpg"` переименовать файлы по маске (Alias: ren) \
`Copy-Item` копирование файлов и каталогов (Alias: cp/copy) \
`Copy-Item -Path "\\server-01\test" -Destination "C:\" -Recurse` копировать директорию с ее содержимым (-Recurse) \
`Copy-Item -Path "C:\*.txt" -Destination "C:\test\"` знак '\' в конце Destination используется для переноса папки внутрь указанной, отсутствие, что это новое имя директории \
`Copy-Item -Path "C:\*" -Destination "C:\test\" -Include '*.txt','*.jpg'` копировать объекты с указанным расширением (Include) \
`Copy-Item -Path "C:\*" -Destination "C:\test\" -Exclude '*.jpeg'` копировать объекты, за исключением файлов с расширением (Exclude) \
`$log = Copy-Item "C:\*.txt" "C:\test\" -PassThru` вывести результат копирования (логирование) в переменную, можно забирать строки с помощью индексов $log[0].FullName \
`Unblock-File "script.ps1"` разблокирует файлы скриптов PowerShell скачанных из Интернета, чтобы их можно было запустить, даже если политика выполнения PowerShell в режиме RemoteSigned

### Clear-env-Temp-14-days
```PowerShell
$ls = Get-Item $env:TEMP\*.tmp # считать все файлы с указанным расширением
$date = (Get-Date).AddDays(-14)
foreach ($l in $ls) {
    if ($l.LastWriteTime -le $date) {
        $l.FullName
        Remove-Item $l.FullName -Recurse
    }
}
```
### System IO File

`$file = [System.IO.File]::Create("$home\desktop\test.txt")` создать файл \
`$file.Close()` закрыть файл \
`[System.IO.File]::ReadAllLines("$home\desktop\test.txt")` прочитать файл \
`$file = New-Object System.IO.StreamReader("$home\desktop\test.txt")` файл будет занят процессом PowerShell \
`$file | Get-Member` \
`$file.ReadLine()` построчный вывод \
`$file.ReadToEnd()` прочитать файл целиком

### Read and Write Bytes

`$file = [io.file]::ReadAllBytes("$home\desktop\powershell.jpg")` метод открывает двоичный файл, считывает его в массив байт и закрывает файл \
`[io.file]::WriteAllBytes("$home\desktop\tloztotk-2.jpg",$file)` сохранить байты в файл (можно использовать для выгрузки двоичных файлов из БД)

# Archive

### Microsoft PowerShell Archive

`Compress-Archive -Path $srcPath -DestinationPath "$($srcPath).zip" -CompressionLevel Optimal` архивировать (по исходному пути и названию с добавлением расширения) \
`Expand-Archive -Path $zip` разархивировать \
`Expand-Archive -Path $zip -DestinationPath $dstPath` указать путь извлечения \
`Expand-Archive -Path $zip -OutputPath $dstPath`

### System IO Compression FileSystem
```PowerShell
function Expand-ArchiveFile {
    param (
        # Путь к архиву
        $Path,
        # Путь, куда извлечь файл
        $DestinationPath,
        # Имя файла, который нужно извлечь
        $FileName
    )
    Add-Type -AssemblyName System.IO.Compression.FileSystem
    try {
        # Открыть архив для чтения
        $zipArchive = [System.IO.Compression.ZipFile]::OpenRead($Path)
        # Найти файл в архиве
        $fileEntry = $zipArchive.Entries | Where-Object { $_.FullName -eq $fileName }
        if ($fileEntry) {
            # Создание потока для чтения содержимого файла
            $stream = $fileEntry.Open()
            # Создание файла и запись в него данных из потока
            $DestinationPathFileName = "$DestinationPath\$FileName"
            $fileStream = [System.IO.File]::Create($DestinationPathFileName)
            $stream.CopyTo($fileStream)
            # Закрытие потоков
            $stream.Close()
            $fileStream.Close()
        } else {
            Write-Output "Файл $fileName не найден в архиве"
        }
    } catch {
        Write-Error "Ошибка при извлечении файла из архива"
    } finally {
        # Закрыть архив
        $zipArchive.Dispose()
    }
}
```
### WinRAR
```PowerShell
function Expand-ArchivePassword {
    param (
        $Path,
        $Password
    )
    $winrar =  "C:\Program Files\WinRAR\WinRAR.exe"
    & $winrar x -p"$Password" $Path
}
```
`cd "$home\Downloads"` \
`Expand-ArchivePassword archive.rar qwe123`

# Handles

`$url = "https://download.sysinternals.com/files/Handle.zip"` \
`Invoke-RestMethod $url -OutFile "$env:TEMP\handle.zip"` \
`Expand-ArchiveFile -Path "$env:TEMP\handle.zip" -DestinationPath "$home\Documents" -FileName "handle.exe"` извлекаем выбранный файл из архива \
`Remove-Item "$env:TEMP\handle.zip"` \
`$handle = "$home\Documents\handle.exe"` \
`$test = New-Object System.IO.StreamReader("$home\desktop\test.txt")` занять файл текущим процессом pwsh ($pid) \
`$SearchProcess = & $handle "C:\Users\Lifailon\Desktop\test.txt" -nobanner -u -v | ConvertFrom-Csv` вывести список дескрипторов по пути к файлу (имя процесса, его PID и пользователь который запустил) \
`Stop-Process $SearchProcess.PID` завершить процесс, который удерживал файл

# Console-Menu
```PowerShell
# Импортируем модуль PS-Menu в текущую сессию из репозитория GitHub
$module = "https://raw.githubusercontent.com/chrisseroka/ps-menu/master/ps-menu.psm1"
Invoke-Expression $(Invoke-RestMethod $module)
```
Пример навигации по директориям в системе используя меню:
```PowerShell
function ls-menu {
  param (
    $startDir = "C:\"
  )
  clear
  # Проверяем, что мы не находимся в root директории (исключить возврат назад)
  if ([System.IO.Path]::GetPathRoot($startDir) -eq $startDir) {
    $select = menu @(
      @($(Get-ChildItem $startDir).name)
    )
  }
  else {
    $select = menu @(
      @("..")+@($(Get-ChildItem $startDir).name)
    )
  }
  # Если выбрали возврат назад, то забираем только путь у стартовой директории
  if ($select -eq "..") {
    $backPath = [System.IO.Path]::GetDirectoryName($startDir)
    ls-menu $backPath
  }
  else {
    # Проверяем, что выбрали директорию
    if ($(Test-Path "$startDir\$select" -PathType Container)) {
      # Если выбрали директорию, к стартовому пути добавляем выбранное имя директории
      ls-menu "$startDir\$select"
    }
    else {
      ls-menu $startDir
    }
  }
}
```
`ls-menu` \
`ls-menu $home` \
`ls-menu "D:\"`

# Credential

`$Cred = Get-Credential` сохраняет креды в переменные `$Cred.Username` и `$Cred.Password` \
`$Cred.GetNetworkCredential().password` извлечь пароль \
`cmdkey /generic:"TERMSRV/$srv" /user:"$username" /pass:"$password"` добавить указанные креды аудентификации на на терминальный сервер для подключения без пароля \
`mstsc /admin /v:$srv` авторизоваться \
`cmdkey /delete:"TERMSRV/$srv"` удалить добавленные креды аудентификации из системы \
`rundll32.exe keymgr.dll,KRShowKeyMgr` хранилище Stored User Names and Password \
`Get-Service VaultSvc` служба для работы Credential Manager \
`Install-Module CredentialManager` установить модуль управления Credential Manager к хранилищу PasswordVault из PowerShell \
`[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]'Tls11,Tls12'` для устаноки модуля \
`Get-StoredCredential` получить учетные данные из хранилища Windows Vault \
`Get-StrongPassword` генератор пароля \
`New-StoredCredential -UserName test -Password "123456"` добавить учетную запись \
`Remove-StoredCredential` удалить учетную запись \
`$Cred = Get-StoredCredential | where {$_.username -match "admin"}` \
`$pass = $cred.password` \
`$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($pass)` \
`[System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)`

### Out-Gridview
`Get-Service -cn $srv | Out-GridView -Title "Service $srv" -OutputMode Single –PassThru | Restart-Service` перезапустить выбранную службу

### Out-File
`Read-Host –AsSecureString | ConvertFrom-SecureString | Out-File "$env:userprofile\desktop\password.txt"` писать в файл. Преобразовать пароль в формат SecureString с использованием шифрования Windows Data Protection API (DPAPI)

### Get-Content
`$password = gc "$env:userprofile\desktop\password.txt" | ConvertTo-SecureString` читать хэш пароля из файла с помощью ключей, хранящихся в профиле текущего пользователя, который невозможно прочитать на другом копьютере

### AES Key
`$AESKey = New-Object Byte[] 32` \
`[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey)` \
`$AESKey | Out-File "C:\password.key"` \
`$Cred.Password | ConvertFrom-SecureString -Key (Get-Content "C:\password.key") | Set-Content "C:\password.txt"` сохранить пароль в файл используя внешний ключ \
`$pass = Get-Content "C:\password.txt" | ConvertTo-SecureString -Key (Get-Content "\\Server\Share\password.key")` расшифровать пароль на втором компьютере

# WinEvent

`Get-WinEvent -ListLog *` отобразить все доступные журналы логов \
`Get-WinEvent -ListLog * | where RecordCount -ne 0 | where RecordCount -ne $null | sort -Descending RecordCount` отобразить не пустые журналы с сортировкой по кол-ву записей \
`Get-WinEvent -ListProvider * | ft` отобразить всех провайдеров приложений \
`Get-WinEvent -ListProvider GroupPolicy` найти в какой журнал LogLinks {Application} пишутся логи приложения \
`Get-WinEvent -ListProvider *smb*` \
`Get-WinEvent -ListLog * | where logname -match SMB | sort -Descending RecordCount` найти все журналы по имени \
`Get-WinEvent -LogName "Microsoft-Windows-SmbClient/Connectivity"` \
`Get-WinEvent -ListProvider *firewall*`

### XPath

`Get-WinEvent -FilterHashtable @{LogName="Security";ID=4624}` найти логи по ID в журнале Security \
`Get-WinEvent -FilterHashtable @{LogName="System";Level=2}` найти все записи ошибки (1 - критический, 3 - предупреждение, 4 - сведения) \
`Get-WinEvent -FilterHashtable @{LogName="System";Level=2;ProviderName="Service Control Manager"}` отфильтровать по имени провайдера

`([xml](Get-WinEvent -FilterHashtable @{LogName="Security";ID=4688} -MaxEvents 1).ToXml()).Event.EventData.Data` отобразить все свойства, хранимые в EventData (Message) \
`Get-WinEvent -FilterHashtable @{logname="security";ID=4688} -MaxEvents 1 | select timecreated,{$_.Properties[5].value}` отфильтровать время события и имя запущенного процесса
```
$query = '
<QueryList>
    <Query Id="0" Path="Security">
        <Select Path="Security">
        *[System[EventID=4688]] and 
            *[EventData[Data[@Name="NewProcessName"]="C:\Windows\System32\autochk.exe" or Data[@Name="NewProcessName"]="C:\Windows\System32\services.exe"]]
        </Select>
    </Query>
</QueryList>
'

Get-WinEvent -LogName Security -FilterXPath $query
```
### Reboot
```
$query = '
<QueryList>
    <Query Id="0" Path="System">
        <Select Path="System">
        *[
      System[
      EventID=41 or
      EventID=1074 or
      EventID=1076 or
      EventID=6005 or
      EventID=6006 or
      EventID=6008 or
      EventID=6009 or
      EventID=6013
      ]
      ]
        </Select>
    </Query>
</QueryList>
'
Get-WinEvent -LogName System -FilterXPath $query

41  ` Система была перезагружена без корректного завершения работы.
1074` Система была корректного выключена пользователем или процессом.
1076` Следует за Event ID 6008 и означает, что первый пользователь (с правом выключения системы) подключившийся к серверу после неожиданной перезагрузки или выключения, указал причину этого события.
6005` Запуск "Журнала событий Windows" (Event Log). Указывает на включение системы.
6006` Остановка «Журнала событий Windows». Указывает на выключение системы.
6008` Предыдущее выключение системы было неожиданным.
6009` Версия операционной системы, зафиксированная при загрузке системы.
6013` Время работы системы (system uptime) в секундах.
```
### Logon
```PowerShell
$srv = "localhost"
$FilterXPath = '<QueryList><Query Id="0"><Select>*[System[EventID=21]]</Select></Query></QueryList>'
$RDPAuths = Get-WinEvent -ComputerName $srv -LogName "Microsoft-Windows-TerminalServices-LocalSessionManager/Operational" -FilterXPath $FilterXPath
[xml[]]$xml = $RDPAuths | Foreach {$_.ToXml()}
$EventData = Foreach ($event in $xml.Event) {
    New-Object PSObject -Property @{
        "Connection Time" = (Get-Date ($event.System.TimeCreated.SystemTime) -Format 'yyyy-MM-dd hh:mm K')
        "User Name" = $event.UserData.EventXML.User
        "User ID" = $event.UserData.EventXML.SessionID
        "User Address" = $event.UserData.EventXML.Address
        "Event ID" = $event.System.EventID
    }
}
$EventData | ft
```
### EventLog

`Get-EventLog -List` отобразить все корневые журналы логов и их размер \
`Clear-EventLog Application` очистить логи указанного журнала \
`Get-EventLog -LogName Security -InstanceId 4624` найти логи по ID в журнале Security

# Firewall
```PowerShell
$days = 5
$obj = @()
$fw = Get-WinEvent "Microsoft-Windows-Windows Firewall With Advanced Security/Firewall"
foreach ($temp_fw in $fw) {
if ($temp_fw.id -eq 2097) { # 2004
    $type = "Added Rule"
}
elseif ($temp_fw.id -eq 2006) {
    $type = "Deleted Rule"
}
$port = $temp_fw.Properties[7] | select -ExpandProperty value
$name = $temp_fw.Properties[1] | select -ExpandProperty value
$obj += [PSCustomObject]@{
    Time = $temp_fw.TimeCreated;
    Type = $type;
    Port = $port;
    Name = $name}
}
$obj | Where-Object time -gt (Get-Date).AddDays(-$days)
```
`New-NetFirewallRule -Profile Any -DisplayName "Open Port 135 RPC" -Direction Inbound -Protocol TCP -LocalPort 135` открыть in-порт \
`Get-NetFirewallRule | where DisplayName -match kms | select *` найти правило по имени \
`Get-NetFirewallPortFilter | where LocalPort -like 80` найти действующие правило по номеру порта
```PowerShell
Get-NetFirewallRule -Enabled True -Direction Inbound | select -Property DisplayName,
@{Name='Protocol';Expression={($_ | Get-NetFirewallPortFilter).Protocol}},
@{Name='LocalPort';Expression={($_ | Get-NetFirewallPortFilter).LocalPort}},
@{Name='RemotePort';Expression={($_ | Get-NetFirewallPortFilter).RemotePort}},
@{Name='RemoteAddress';Expression={($_ | Get-NetFirewallAddressFilter).RemoteAddress}},
Enabled,Profile
```
### Firewall-Manager

`Install-Module Firewall-Manager` \
`Export-FirewallRules -Name * -CSVFile $home\documents\fw.csv` -Inbound -Outbound -Enabled -Disabled -Allow -Block (фильтр правил для экспорта) \
`Import-FirewallRules -CSVFile $home\documents\fw.csv`

# Defender

`Import-Module Defender` \
`Get-Command -Module Defender` \
`Get-MpComputerStatus` \
`(Get-MpComputerStatus).AntivirusEnabled` статус работы антивируса

`$session = NewCimSession -ComputerName hostname` подключиться к удаленному компьютеру, используется WinRM \
`Get-MpComputerStatus -CimSession $session | fl fullscan*` узнать дату последнего сканирования на удаленном компьютере

`Get-MpPreference` настройки \
`(Get-MpPreference).ScanPurgeItemsAfterDelay` время хранения записей журнала защитника в днях \
`Set-MpPreference -ScanPurgeItemsAfterDelay 30` изменить время хранения \
`ls "C:\ProgramData\Microsoft\Windows Defender\Scans\History"` \
`Get-MpPreference | select disable*` отобразить статус всех видов проверок/сканирований \
`Set-MpPreference -DisableRealtimeMonitoring $true` отключить защиту Defender в реальном времени (использовать только ручное сканирование) \
`Set-MpPreference -DisableRemovableDriveScanning $false` включить сканирование USB накопителей \
`Get-MpPreference | select excl*` отобразить список всех исключений \
`(Get-MpPreference).ExclusionPath` \
`Add-MpPreference -ExclusionPath C:\install` добавить директорию в исключение \
`Remove-MpPreference -ExclusionPath C:\install` удалить из исключения \
`New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows Defender" -Name DisableAntiSpyware -Value 1 -PropertyType DWORD -Force` полностью отключить Windows Defender

`Set-MpPreference -SignatureDefinitionUpdateFileSharesSources \\FileShare1\Updates` для обновления из сетевой папки нужно предварительно скачать файлы с сигнатурами баз с сайта https://www.microsoft.com/security/portal/definitions/adl.aspx и поместить в сетевой каталог
`Update-MpSignature -UpdateSource FileShares` изменить источник обновлений (MicrosoftUpdateServer – сервера обновлений MS в интернете, InternalDefinitionUpdateServer — внутренний WSUS сервер) \
`Update-MpSignature` обновить сигнатуры

`Start-MpScan -ScanType QuickScan` быстрая проверка или FullScan \
`Start-MpScan -ScanType FullScan -AsJob` \
`Set-MpPreference -RemediationScheduleDay 1-7` выбрать дни, начиная с воскресенья или 0 каждый день, 8 - сбросить \
`Set-MpPreference -ScanScheduleQuickScanTime 14:00:00` \
`Start-MpScan -ScanType CustomScan -ScanPath "C:\Program Files"` сканировать выбранную директорию

`Get-MpThreat` история угроз и тип угрозы (ThreatName: HackTool/Trojan) \
`Get-MpThreatCatalog` список известных видов угроз \
`Get-MpThreatDetection` история защиты (активных и прошлые) и ID угрозы \
`Get-MpThreat -ThreatID 2147760253`

`ls "C:\ProgramData\Microsoft\Windows Defender\Quarantine\"` директория хранения файлов в карантине \
`cd "C:\Program Files\Windows Defender\"` \
`.\MpCmdRun.exe -restore -name $ThreatName` восстановить файл из карантина \
`.\MpCmdRun.exe -restore -filepath $path_file`

# DISM

`Get-Command -Module Dism -Name *Driver*` \
`Export-WindowsDriver -Online -Destination C:\Users\Lifailon\Documents\Drivers\` извлечение драйверов из текущей системы (C:\Windows\System32\DriverStore\FileRepository\), выгружает список файлов, которые необходимы для установки драйвера (dll,sys,exe) в соответствии со списком файлов, указанных в секции [CopyFiles] inf-файла драйвера. \
`Export-WindowsDriver -Path C:\win_image -Destination C:\drivers` извлечь драйвера из офлайн образа Windows, смонтированного в каталог c:\win_image \
`$BackupDrivers = Export-WindowsDriver -Online -Destination C:\Drivers` \
`$BackupDrivers | ft Driver,ClassName,ProviderName,Date,Version,ClassDescription` список драйверов в объектном представлении \
`$BackupDrivers | where classname -match printer` \
`pnputil.exe /add-driver C:\drivers\*.inf /subdirs /install` установить все (параметр subdirs) драйвера из указанной папки (включая вложенные)

`sfc /scannow` проверить целостность системных файлов с помощью утилиты SFC (System File Checker), в случае поиска ошибок, попробует восстановить их оригинальные копии из хранилища системных компонентов Windows (каталог C:\Windows\WinSxS). Вывод работы логируется в C:\Windows\Logs\CBS с тегом SR \
`Get-ComputerInfo | select *` подробная информация о системе (WindowsVersion,WindowsEditionId,*Bios*) \
`Get-WindowsImage -ImagePath E:\sources\install.wim` список доступных версий в образе \
`Repair-WindowsImage -Online –ScanHealth` \
`Repair-WindowsImage -Online -RestoreHealth` восстановление хранилища системных компонентов \
`Repair-WindowsImage -Online -RestoreHealth -Source E:\sources\install.wim:3 –LimitAccess` восстановление в оффлайн режиме из образа по номеру индекса

# Scheduled

`$Trigger = New-ScheduledTaskTrigger -At 01:00am -Daily` 1:00 ночи \
`$Trigger = New-ScheduledTaskTrigger –AtLogon` запуск при входе пользователя в систему \
`$Trigger = New-ScheduledTaskTrigger -AtStartup` при запуске системы \
`$User = "NT AUTHORITY\SYSTEM"` \
`$Action = New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "$home\Documents\DNS-Change-Tray-1.3.ps1"` \
`$Action = New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "-NoProfile -NoLogo -NonInteractive -ExecutionPolicy Unrestricted -WindowStyle Hidden -File $home\Documents\DNS-Change-Tray-1.3.ps1"` \
`Register-ScheduledTask -TaskName "DNS-Change-Tray-Startup" -Trigger $Trigger -User $User -Action $Action -RunLevel Highest –Force`

`Get-ScheduledTask | ? state -ne Disabled` список всех активных заданий \
`Start-ScheduledTask DNS-Change-Tray-Startup` запустить задание немедленно \
`Get-ScheduledTask DNS-Change-Tray-Startup | Disable-ScheduledTask` отключить задание \
`Get-ScheduledTask DNS-Change-Tray-Startup | Enable-ScheduledTask` включить задание \
`Unregister-ScheduledTask DNS-Change-Tray-Startup` удалить задание \
`Export-ScheduledTask DNS-Change-Tray-Startup | Out-File $home\Desktop\Task-Export-Startup.xml` экспортировать задание в xml \
`Register-ScheduledTask -Xml (Get-Content $home\Desktop\Task-Export-Startup.xml | Out-String) -TaskName "DNS-Change-Tray-Startup"`

# shutdown

`shutdown /r /o` перезагрузка в безопасный режим \
`shutdown /s /t 600 /c "Power off after 10 minutes"` выключение \
`shutdown /s /f` принудительное закрытие приложений \
`shutdown /a` отмена \
`shutdown /r /t 0 /m \\192.168.3.100` \
`Restart-Computer -ComputerName 192.168.3.100 -Protocol WSMan` через WinRM \
`Restart-Computer –ComputerName 192.168.3.100 –Force` через WMI \
`Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\PolicyManager\default\Start\HideShutDown" -Name "value" -Value 1` скрыть кнопку выключения \
`Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\PolicyManager\default\Start\HideRestart" -Name "value" -Value 1` скрыть кнопку перезагрузки
```PowerShell
function Start-Shutdown {
    <#
    .SYNOPSIS
    Module for shutdown and restart the computer at a specified time
    .DESCRIPTION
    Example:
    # Start-Shutdown -Time "18:00"
    # Start-Shutdown -Restart -Time "18:00"
    # Start-Shutdown -Cancel
    .LINK
    https://github.com/Lifailon/PS-Commands
    #>
    param(
        [string]$Time,
        [switch]$Restart,
        [switch]$Cancel
    )
    if ($Time) {
        $currentDateTime = Get-Date
        $shutdownTime = Get-Date $Time
        if ($shutdownTime -lt $currentDateTime) {
            $shutdownTime = $shutdownTime.AddDays(1)
        }
        $timeUntilShutdown = $shutdownTime - $currentDateTime
        $secondsUntilShutdown = [math]::Round($timeUntilShutdown.TotalSeconds)
    }
    if ($Cancel) {
        Start-Process -FilePath "shutdown.exe" -ArgumentList "/a"
    } elseif ($Restart) {
        Write-Host "The computer will restart after $($timeUntilShutdown.Hours) hours and $($timeUntilShutdown.Minutes) minutes."
        Start-Process -FilePath "shutdown.exe" -ArgumentList "/r", "/f", "/t", "$secondsUntilShutdown"
    } else {
        Write-Host "The computer will shutdown after $($timeUntilShutdown.Hours) hours and $($timeUntilShutdown.Minutes) minutes."
        Start-Process -FilePath "shutdown.exe" -ArgumentList "/s", "/f", "/t", "$secondsUntilShutdown"
    }
}
```
# LocalAccounts

`Get-Command -Module Microsoft.PowerShell.LocalAccounts` \
`Get-LocalUser` список пользователей \
`Get-LocalGroup` список групп \
`New-LocalUser "1C" -Password $Password -FullName "1C Domain"` создать пользователя \
`Set-LocalUser -Password $Password 1C` изменить пароль \
`Add-LocalGroupMember -Group "Administrators" -Member "1C"` добавить в группу Администраторов \
`Get-LocalGroupMember "Administrators"` члены группы
```PowerShell
@("vproxy-01","vproxy-02","vproxy-03") | %{
    icm $_ {Add-LocalGroupMember -Group "Administrators" -Member "support4"}
    icm $_ {Get-LocalGroupMember "Administrators"}
}
```

# PS2EXE

`Install-Module ps2exe -Repository PSGallery` \
`Get-Module -ListAvailable` список всех модулей \
`-noConsole` использовать GUI, без окна консоли powershell \
`-noOutput` выполнение в фоне \
`-noError` без вывода ошибок \
`-requireAdmin` при запуске запросить права администратора \
`-credentialGUI` вывод диалогового окна для ввода учетных данных \
`Invoke-ps2exe -inputFile "$home\Desktop\WinEvent-Viewer-1.1.ps1" -outputFile "$home\Desktop\WEV-1.1.exe" -iconFile "$home\Desktop\log_48px.ico" -title "WinEvent-Viewer" -noConsole -noOutput -noError`

# NSSM

`$powershell_Path = (Get-Command powershell).Source` \
`$NSSM_Path = (Get-Command "C:\WinPerf-Agent\NSSM-2.24.exe").Source` \
`$Script_Path = "C:\WinPerf-Agent\WinPerf-Agent-1.1.ps1"` \
`$Service_Name = "WinPerf-Agent"` \
`& $NSSM_Path install $Service_Name $powershell_Path -ExecutionPolicy Bypass -NoProfile -f $Script_Path` создать Service \
`& $NSSM_Path start $Service_Name` запустить \
`& $NSSM_Path status $Service_Name` статус \
`$Service_Name | Restart-Service` перезапустить \
`$Service_Name | Get-Service` статус \
`$Service_Name | Stop-Service` остановить \
`& $NSSM_Path set $Service_Name description "Check performance CPU and report email"` изменить описание \
`& $NSSM_Path remove $Service_Name` удалить

# Jobs

`Get-Job` получение списка задач \
`Start-Job` запуск процесса \
`Stop-Job` остановка процесса \
`Suspend-Job` приостановка работы процесса \
`Resume-Job` восстановление работы процесса \
`Wait-Job` ожидание вывода команды \
`Receive-Job` получение результатов выполненного процесса \
`Remove-Job` удалить задачу
```PowerShell
function Start-PingJob ($Network) {
    $RNetwork = $Network -replace "\.\d{1,3}$","."
    foreach ($4 in 1..254) {
        $ip = $RNetwork+$4
        # Создаем задания, забираем 3-ю строку вывода и добавляем к выводу ip-адрес
        (Start-Job {"$using:ip : "+(ping -n 1 -w 50 $using:ip)[2]}) | Out-Null
    }
    while ($True) {
        $status_job = $(Get-Job).State[-1] # забираем статус последнего задания (задания выполняются по очереди сверху вниз)
        if ($status_job -like "Completed") { # проверяем задание на выполнение
            $ping_out = Get-Job | Receive-Job # если выполнено, забираем вывод всех заданий
            Get-Job | Remove-Job -Force # удаляем задания
            break # завершаем цикл
        }
    }
    $ping_out
}
```
`Start-PingJob -Network 192.168.3.0` \
`$(Measure-Command {Start-PingJob -Network 192.168.3.0}).TotalSeconds` 60 Seconds

### ThreadJob

`Install-Module -Name ThreadJob` \
`Get-Module ThreadJob -list` \
`Start-ThreadJob {ping ya.ru} | Out-Null` создать фоновую задачу \
`Get-Job | Receive-Job -Keep` отобразить и не удалять вывод \
`(Get-Job).HasMoreData` если False, то вывод команы удален \
`(Get-Job)[-1].Output` отобразить вывод последней задачи
```PowerShell
function Start-PingThreadJob ($Network) {
    $RNetwork = $Network -replace "\.\d{1,3}$","."
    foreach ($4 in 1..254) {
        $ip = $RNetwork+$4
        $(Start-ThreadJob {
            "$using:ip : " + $(ping -n 1 -w 50 $using:ip)[2]
        }) | Out-Null
    }
    while ($True) {
        $status_job = $(Get-Job).State[-1]
        if ($status_job -like "Completed") {
            $ping_out = Get-Job | Receive-Job
            Get-Job | Remove-Job -Force
            break
        }
    }
    $ping_out
}
```
`Start-PingThreadJob -Network 192.168.3.0` \
`$(Measure-Command {Start-PingThread -Network 192.168.3.0}).TotalSeconds` 24 Seconds

### PoshRSJob

Install-Module -Name PoshRSJob
```PowerShell
function Start-PingRSJob ($Network) {
    $RNetwork = $Network -replace "\.\d{1,3}$","."
    foreach ($4 in 1..254) {
        $ip = $RNetwork+$4
        $(Start-RSJob {
            "$using:ip : " + $(ping -n 1 -w 50 $using:ip)[2]
        }) | Out-Null
    }
    while ($True) {
        $status_job = $(Get-RSJob).State -notcontains "Running" # проверяем, что массив не содержит активных заданий
        if ($status_job) {
            $ping_out = Get-RSJob | Receive-RSJob
            Get-RSJob | Remove-RSJob
            break
        }
    }
    $ping_out
}
```
`Start-PingRSJob -Network 192.168.3.0` \
`$(Measure-Command {Start-PingRSJob -Network 192.168.3.0}).TotalSeconds` 10 Seconds

### Invoke-Parallel
```PowerShell
# Import function from GitHub to current session
$module = "https://raw.githubusercontent.com/RamblingCookieMonster/Invoke-Parallel/master/Invoke-Parallel/Invoke-Parallel.ps1"
Invoke-Expression $(Invoke-RestMethod $module)
```
Get-Help Invoke-Parallel -Full
```PowerShell
function Start-PingInvokeParallel ($Network) {
    $RNetwork = $Network -replace "\.\d{1,3}$","."
    1..254 | ForEach-Object {$srvList += @($RNetwork+$_)}
    Invoke-Parallel -InputObject $srvList -ScriptBlock {
        "$_ : " + $(ping -n 1 -w 50 $_)[2]
    }
}
```
`Start-PingInvokeParallel -Network 192.168.3.0` \
`$(Get-History)[-1].Duration.TotalSeconds` 7 seconds
```PowerShell
$array_main  = 1..10 | ForEach-Object {"192.168.3.$_"}
Invoke-Parallel -InputObject $(0..$($array_main.Count-1)) -ScriptBlock {
    Foreach ($n in 1..100) {
        Start-Sleep -Milliseconds 100
        Write-Progress -Activity $($array_main[$_]) -PercentComplete $n -id $_
    }
} -ImportVariables
```
### ForEach-Object-Parallel
```PowerShell
function Start-PingParallel ($Network) {
    $RNetwork = $Network -replace "\.\d{1,3}$","."
    1..254 | ForEach-Object -Parallel {
        "$using:RNetwork.$_ : " + $(ping -n 1 -w 50 "$using:RNetwork$_")[2]
    } -ThrottleLimit 254
}
```
`Start-PingParallel -Network 192.168.3.0` \
`$(Get-History)[-1].Duration.TotalSeconds` 2 seconds
```PowerShell
function Start-TestConnectParallel (
        $Network,
        [switch]$Csv
    ) {
    $RNetwork = $Network -replace "\.\d{1,3}$","."
    if ($csv) {
        "Address,Status,Latency"
    }
    1..254 | ForEach-Object -Parallel {
        $test = Test-Connection "$using:RNetwork$_" -Count 1 -TimeoutSeconds 1
        if ($using:csv) {
            "$($using:RNetwork)$_,$($test.Status),$($test.Latency)"
        } else {
            $test
        }
    } -ThrottleLimit 254
}
```
`Start-TestConnectParallel -Network 192.168.3.0 -Csv | ConvertFrom-Csv` \
`$(Get-History)[-1].Duration.TotalSeconds` 3 seconds
```PowerShell
$array_main  = 1..10 | ForEach-Object {"192.168.3.$_"}
$(0..$($array_main.Count-1)) | ForEach-Object -Parallel {
    Foreach ($n in 1..100) {
        Start-Sleep -Milliseconds 100
        $array_temp = $using:array_main
        Write-Progress -Activity $($array_temp[$_]) -PercentComplete $n -id $_
    }
} -ThrottleLimit $($array_main.Count)
```

# COM

`$wshell = New-Object -ComObject Wscript.Shell` \
`$wshell | Get-Member` \
`$link = $wshell.CreateShortcut("$Home\Desktop\Yandex.lnk")` создать ярлык \
`$link | Get-Member` \
`$link.TargetPath = "https://yandex.ru"` куда ссылается (метод TargetPath объекта $link где хранится объект CreateShortcut) \
`$link.Save()` сохранить

`Set-WinUserLanguageList -LanguageList en-us,ru -Force` изменить языковую раскладку клавиатуры

### Wscript Shell SendKeys

`(New-Object -ComObject Wscript.shell).SendKeys([char]173)` включить/выключить звук \
`$wshell.Exec("notepad.exe")` запустить приложение \
`$wshell.AppActivate("Блокнот")` развернуть запущенное приложение

`$wshell.SendKeys("Login")` текст \
`$wshell.SendKeys("{A 5}")` напечатать букву 5 раз подряд \
`$wshell.SendKeys("%{TAB}")` ALT+TAB \
`$wshell.SendKeys("^")` CTRL \
`$wshell.SendKeys("%")` ALT \
`$wshell.SendKeys("+")` SHIFT \
`$wshell.SendKeys("{DOWN}")` вниз \
`$wshell.SendKeys("{UP}")` вверх \
`$wshell.SendKeys("{LEFT}")` влево \
`$wshell.SendKeys("{RIGHT}")` вправо \
`$wshell.SendKeys("{PGUP}")` PAGE UP \
`$wshell.SendKeys("{PGDN}")` PAGE DOWN \
`$wshell.SendKeys("{BACKSPACE}")` BACKSPACE/BKSP/BS \
`$wshell.SendKeys("{DEL}")` DEL/DELETE \
`$wshell.SendKeys("{INS}")` INS/INSERT \
`$wshell.SendKeys("{PRTSC}")` PRINT SCREEN \
`$wshell.SendKeys("{ENTER}")` \
`$wshell.SendKeys("{ESC}")` \
`$wshell.SendKeys("{TAB}")` \
`$wshell.SendKeys("{END}")` \
`$wshell.SendKeys("{HOME}")` \
`$wshell.SendKeys("{BREAK}")` \
`$wshell.SendKeys("{SCROLLLOCK}")` \
`$wshell.SendKeys("{CAPSLOCK}")` \
`$wshell.SendKeys("{NUMLOCK}")` \
`$wshell.SendKeys("{F1}")` \
`$wshell.SendKeys("{F12}")` \
`$wshell.SendKeys("{+}{^}{%}{~}{(}{)}{[}{]}{{}{}}")`
```PowerShell
function Get-AltTab {
    (New-Object -ComObject wscript.shell).SendKeys("%{Tab}")
    Start-Sleep $(Get-Random -Minimum 30 -Maximum 180)
    Get-AltTab
}
```
`Get-AltTab`

### Wscript Shell Popup

`$wshell = New-Object -ComObject Wscript.Shell` \
`$output = $wshell.Popup("Выберите действие?",0,"Заголовок",4)` \
`if ($output -eq 6) {"yes"} elseif ($output -eq 7) {"no"} else {"no good"}`
```
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
```
### WScript Network

`$wshell = New-Object -ComObject WScript.Network` \
`$wshell | Get-Member` \
`$wshell.UserName` \
`$wshell.ComputerName` \
`$wshell.UserDomain`

### Shell Application

`$wshell = New-Object -ComObject Shell.Application` \
`$wshell | Get-Member` \
`$wshell.Explore("C:\")` \
`$wshell.Windows() | Get-Member` получить доступ к открытым в проводнике или браузере Internet Explorer окон

`$shell = New-Object -Com Shell.Application` \
`$RecycleBin = $shell.Namespace(10)` \
`$RecycleBin.Items()`

### Outlook

`$Outlook = New-Object -ComObject Outlook.Application` \
`$Outlook | Get-Member` \
`$Outlook.Version`
```PowerShell
$Outlook = New-Object -ComObject Outlook.Application
$Namespace = $Outlook.GetNamespace("MAPI")
$Folder = $namespace.GetDefaultFolder(4)` исходящие
$Folder = $namespace.GetDefaultFolder(6)` входящие
$Explorer = $Folder.GetExplorer()
$Explorer.Display()	
$Outlook.Quit()
```
### Microsoft Update

`(New-Object -com 'Microsoft.Update.AutoUpdate').Settings` \
`(New-Object -com 'Microsoft.Update.AutoUpdate').Results` \
`(New-Timespan -Start ((New-Object -com 'Microsoft.Update.AutoUpdate').Results|Select -ExpandProperty LastInstallationSuccessDate) -End (Get-Date)).hours` кол-во часов, прошедших с последней даты установки обновления безопасности в Windows.

# dotNET

`[System.Diagnostics.EventLog] | select Assembly,Module` \
`$EventLog = [System.Diagnostics.EventLog]::new("Application")` \
`$EventLog = New-Object -TypeName System.Diagnostics.EventLog -ArgumentList Application,192.168.3.100` \
`$EventLog | Get-Member -MemberType Method` \
`$EventLog.MaximumKilobytes` максимальный размер журнала \
`$EventLog.Entries` просмотреть журнал \
`$EventLog.Clear()` очистить журнал

`Join-Path C: Install Test` \
`[System.IO.Path]::Combine("C:", "Install", "Test")`

### Match

`[System.Math] | Get-Member -Static -MemberType Methods` \
`[System.Math]::Max(2,7)` \
`[System.Math]::Min(2,7)` \
`[System.Math]::Floor(3.9)` \
`[System.Math]::Truncate(3.9)`

### Generate Password

`Add-Type -AssemblyName System.Web` \
`[System.Web.Security.Membership]::GeneratePassword(10,2)`

### Sound Player
```PowerShell
$CriticalSound = New-Object System.Media.SoundPlayer
$CriticalSound.SoundLocation = "C:\WINDOWS\Media\Windows Critical Stop.wav"
$CriticalSound.Play()

$GoodSound = New-Object System.Media.SoundPlayer
$GoodSound.SoundLocation = "C:\WINDOWS\Media\tada.wav"
$GoodSound.Play()
```
### Static Class

`[System.Environment] | Get-Member -Static` \
`[System.Environment]::OSVersion` \
`[System.Environment]::Version` \
`[System.Environment]::MachineName` \
`[System.Environment]::UserName`

`[System.Diagnostics.Process] | Get-Member -Static` \
`[System.Diagnostics.Process]::Start('notepad.exe')`

### Clicker
```PowerShell
$cSource = @'
using System;
using System.Drawing;
using System.Runtime.InteropServices;
using System.Windows.Forms;
public class Clicker
{
//https://msdn.microsoft.com/en-us/library/windows/desktop/ms646270(v=vs.85).aspx
[StructLayout(LayoutKind.Sequential)]
struct INPUT
{ 
    public int        type; // 0 = INPUT_MOUSE,
                            // 1 = INPUT_KEYBOARD
                            // 2 = INPUT_HARDWARE
    public MOUSEINPUT mi;
}
//https://msdn.microsoft.com/en-us/library/windows/desktop/ms646273(v=vs.85).aspx
[StructLayout(LayoutKind.Sequential)]
struct MOUSEINPUT
{
    public int    dx ;
    public int    dy ;
    public int    mouseData ;
    public int    dwFlags;
    public int    time;
    public IntPtr dwExtraInfo;
}
//This covers most use cases although complex mice may have additional buttons
//There are additional constants you can use for those cases, see the msdn page
const int MOUSEEVENTF_MOVED      = 0x0001 ;
const int MOUSEEVENTF_LEFTDOWN   = 0x0002 ;
const int MOUSEEVENTF_LEFTUP     = 0x0004 ;
const int MOUSEEVENTF_RIGHTDOWN  = 0x0008 ;
const int MOUSEEVENTF_RIGHTUP    = 0x0010 ;
const int MOUSEEVENTF_MIDDLEDOWN = 0x0020 ;
const int MOUSEEVENTF_MIDDLEUP   = 0x0040 ;
const int MOUSEEVENTF_WHEEL      = 0x0080 ;
const int MOUSEEVENTF_XDOWN      = 0x0100 ;
const int MOUSEEVENTF_XUP        = 0x0200 ;
const int MOUSEEVENTF_ABSOLUTE   = 0x8000 ;
const int screen_length          = 0x10000 ;
//https://msdn.microsoft.com/en-us/library/windows/desktop/ms646310(v=vs.85).aspx
[System.Runtime.InteropServices.DllImport("user32.dll")]
extern static uint SendInput(uint nInputs, INPUT[] pInputs, int cbSize);
public static void LeftClickAtPoint(int x, int y)
{
    //Move the mouse
    INPUT[] input = new INPUT[3];
    input[0].mi.dx = x*(65535/System.Windows.Forms.Screen.PrimaryScreen.Bounds.Width);
    input[0].mi.dy = y*(65535/System.Windows.Forms.Screen.PrimaryScreen.Bounds.Height);
    input[0].mi.dwFlags = MOUSEEVENTF_MOVED | MOUSEEVENTF_ABSOLUTE;
    //Left mouse button down
    input[1].mi.dwFlags = MOUSEEVENTF_LEFTDOWN;
    //Left mouse button up
    input[2].mi.dwFlags = MOUSEEVENTF_LEFTUP;
    SendInput(3, input, Marshal.SizeOf(input[0]));
}
}
'@
```
`Add-Type -TypeDefinition $cSource -ReferencedAssemblies System.Windows.Forms,System.Drawing` \
`[Clicker]::LeftClickAtPoint(1900,1070)`

### Audio
```PowerShell
Add-Type -Language CsharpVersion3 -TypeDefinition @"
using System.Runtime.InteropServices;
[Guid("5CDF2C82-841E-4546-9722-0CF74078229A"), InterfaceType(ComInterfaceType.InterfaceIsIUnknown)]
interface IAudioEndpointVolume {
// f(), g(), ... are unused COM method slots. Define these if you care
int f(); int g(); int h(); int i();
int SetMasterVolumeLevelScalar(float fLevel, System.Guid pguidEventContext);
int j();
int GetMasterVolumeLevelScalar(out float pfLevel);
int k(); int l(); int m(); int n();
int SetMute([MarshalAs(UnmanagedType.Bool)] bool bMute, System.Guid pguidEventContext);
int GetMute(out bool pbMute);
}
[Guid("D666063F-1587-4E43-81F1-B948E807363F"), InterfaceType(ComInterfaceType.InterfaceIsIUnknown)]
interface IMMDevice {
int Activate(ref System.Guid id, int clsCtx, int activationParams, out IAudioEndpointVolume aev);
}
[Guid("A95664D2-9614-4F35-A746-DE8DB63617E6"), InterfaceType(ComInterfaceType.InterfaceIsIUnknown)]
interface IMMDeviceEnumerator {
int f(); // Unused
int GetDefaultAudioEndpoint(int dataFlow, int role, out IMMDevice endpoint);
}
[ComImport, Guid("BCDE0395-E52F-467C-8E3D-C4579291692E")] class MMDeviceEnumeratorComObject { }
public class Audio {
static IAudioEndpointVolume Vol() {
var enumerator = new MMDeviceEnumeratorComObject() as IMMDeviceEnumerator;
IMMDevice dev = null;
Marshal.ThrowExceptionForHR(enumerator.GetDefaultAudioEndpoint(/*eRender*/ 0, /*eMultimedia*/ 1, out dev));
IAudioEndpointVolume epv = null;
var epvid = typeof(IAudioEndpointVolume).GUID;
Marshal.ThrowExceptionForHR(dev.Activate(ref epvid, /*CLSCTX_ALL*/ 23, 0, out epv));
return epv;
}
public static float Volume {
get {float v = -1; Marshal.ThrowExceptionForHR(Vol().GetMasterVolumeLevelScalar(out v)); return v;}
set {Marshal.ThrowExceptionForHR(Vol().SetMasterVolumeLevelScalar(value, System.Guid.Empty));}
}
public static bool Mute {
get { bool mute; Marshal.ThrowExceptionForHR(Vol().GetMute(out mute)); return mute; }
set { Marshal.ThrowExceptionForHR(Vol().SetMute(value, System.Guid.Empty)); }
}
}
"@
```
`[Audio]::Volume = 0.50` \
`[Audio]::Mute = $true`

### NetSessionEnum

[Function](https://learn.microsoft.com/ru-ru/windows/win32/api/lmshare/nf-lmshare-netsessionenum?redirectedfrom=MSDN) \
[Source](https://fuzzysecurity.com/tutorials/24.html)
```PowerShell
function Invoke-NetSessionEnum {
param (
[Parameter(Mandatory = $True)][string]$HostName
)
Add-Type -TypeDefinition @"
using System;
using System.Diagnostics;
using System.Runtime.InteropServices;
[StructLayout(LayoutKind.Sequential)]
public struct SESSION_INFO_10
{
    [MarshalAs(UnmanagedType.LPWStr)]public string OriginatingHost;
    [MarshalAs(UnmanagedType.LPWStr)]public string DomainUser;
    public uint SessionTime;
    public uint IdleTime;
}
public static class Netapi32
{
[DllImport("Netapi32.dll", SetLastError=true)]
    public static extern int NetSessionEnum(
        [In,MarshalAs(UnmanagedType.LPWStr)] string ServerName,
        [In,MarshalAs(UnmanagedType.LPWStr)] string UncClientName,
        [In,MarshalAs(UnmanagedType.LPWStr)] string UserName,
        Int32 Level,
        out IntPtr bufptr,
        int prefmaxlen,
        ref Int32 entriesread,
        ref Int32 totalentries,
        ref Int32 resume_handle);
         
[DllImport("Netapi32.dll", SetLastError=true)]
    public static extern int NetApiBufferFree(
        IntPtr Buffer);
}
"@
# Create SessionInfo10 Struct
$SessionInfo10 = New-Object SESSION_INFO_10
$SessionInfo10StructSize = [System.Runtime.InteropServices.Marshal]::SizeOf($SessionInfo10)` Grab size to loop bufptr
$SessionInfo10 = $SessionInfo10.GetType()` Hacky, but we need this ;))
# NetSessionEnum params
$OutBuffPtr = [IntPtr]::Zero` Struct output buffer
$EntriesRead = $TotalEntries = $ResumeHandle = 0` Counters & ResumeHandle
$CallResult = [Netapi32]::NetSessionEnum($HostName, "", "", 10, [ref]$OutBuffPtr, -1, [ref]$EntriesRead, [ref]$TotalEntries, [ref]$ResumeHandle)
if ($CallResult -ne 0){
echo "Mmm something went wrong!`nError Code: $CallResult"
}
else {
if ([System.IntPtr]::Size -eq 4) {
echo "`nNetapi32::NetSessionEnum Buffer Offset  --> 0x$("{0:X8}" -f $OutBuffPtr.ToInt32())"
}
else {
echo "`nNetapi32::NetSessionEnum Buffer Offset  --> 0x$("{0:X16}" -f $OutBuffPtr.ToInt64())"
}
echo "Result-set contains $EntriesRead session(s)!"
# Change buffer offset to int
$BufferOffset = $OutBuffPtr.ToInt64()
# Loop buffer entries and cast pointers as SessionInfo10
for ($Count = 0; ($Count -lt $EntriesRead); $Count++){
$NewIntPtr = New-Object System.Intptr -ArgumentList $BufferOffset
$Info = [system.runtime.interopservices.marshal]::PtrToStructure($NewIntPtr,[type]$SessionInfo10)
$Info
$BufferOffset = $BufferOffset + $SessionInfo10StructSize
}
echo "`nCalling NetApiBufferFree, no memleaks here!"
[Netapi32]::NetApiBufferFree($OutBuffPtr) |Out-Null
}
}
```
`Invoke-NetSessionEnum localhost`

### CopyFile

[Function](https://learn.microsoft.com/ru-ru/windows/win32/api/winbase/nf-winbase-copyfile) \
[Source](https://devblogs.microsoft.com/scripting/use-powershell-to-interact-with-the-windows-api-part-1/)
```PowerShell
$MethodDefinition = @"
[DllImport("kernel32.dll", CharSet = CharSet.Unicode)]
public static extern bool CopyFile(string lpExistingFileName, string lpNewFileName, bool bFailIfExists);
"@
$Kernel32 = Add-Type -MemberDefinition $MethodDefinition -Name "Kernel32" -Namespace "Win32" -PassThru
$Kernel32::CopyFile("$($Env:SystemRoot)\System32\calc.exe", "$($Env:USERPROFILE)\Desktop\calc.exe", $False) 
```
### ShowWindowAsync

[Function](https://learn.microsoft.com/ru-ru/windows/win32/api/winuser/nf-winuser-showwindowasync)
```PowerShell
$Signature = @"
[DllImport("user32.dll")]public static extern bool ShowWindowAsync(IntPtr hWnd, int nCmdShow);
"@
$ShowWindowAsync = Add-Type -MemberDefinition $Signature -Name "Win32ShowWindowAsync" -Namespace Win32Functions -PassThru
$ShowWindowAsync | Get-Member -Static
$ShowWindowAsync::ShowWindowAsync((Get-Process -Id $pid).MainWindowHandle, 2)
$ShowWindowAsync::ShowWindowAsync((Get-Process -Id $Pid).MainWindowHandle, 3)
$ShowWindowAsync::ShowWindowAsync((Get-Process -Id $Pid).MainWindowHandle, 4)
```
### GetAsyncKeyState

[Function](https://learn.microsoft.com/ru-ru/windows/win32/api/winuser/nf-winuser-getasynckeystate)

`Add-Type -AssemblyName System.Windows.Forms` \
`[int][System.Windows.Forms.Keys]::F1` определить номер [Int] клавиши по ее названию \
`65..90 | % {"{0} = {1}" -f $_, [System.Windows.Forms.Keys]$_}` порядковый номер букв (A..Z)
```PowerShell
function Get-ControlKey {
$key = 112
$Signature = @'
[DllImport("user32.dll", CharSet=CharSet.Auto, ExactSpelling=true)] 
public static extern short GetAsyncKeyState(int virtualKeyCode); 
'@
Add-Type -MemberDefinition $Signature -Name Keyboard -Namespace PsOneApi
[bool]([PsOneApi.Keyboard]::GetAsyncKeyState($key) -eq -32767)
}

Write-Warning 'Press F1 to exit'
while ($true) {
Write-Host '.' -NoNewline
if (Get-ControlKey) {
break
}
Start-Sleep -Seconds 0.5
}
```
# Console API

[Source](https://powershell.one/tricks/input-devices/detect-key-press)

`[Console] | Get-Member -Static` \
`[Console]::BackgroundColor = "Blue"` \
`[Console]::OutputEncoding` используемая кодировка в текущей сессии \
`[Console]::OutputEncoding = [System.Text.Encoding]::GetEncoding("utf-8")` изменить кодировку для отображения кириллицы \
`[Console]::outputEncoding = [System.Text.Encoding]::GetEncoding("cp866")` для ISE \
`[Console]::OutputEncoding = [System.Text.Encoding]::GetEncoding("windows-1251")` для ps2exe \
`Get-Service | Out-File $home\Desktop\Service.txt -Encoding oem` > \
`Get-Service | Out-File $home\Desktop\Service.txt -Append` >>
```PowerShell
do {
    if ([Console]::KeyAvailable) {
        $keyInfo = [Console]::ReadKey($true)
        break
    }
    Write-Host "." -NoNewline
    Start-Sleep 1
} while ($true)
Write-Host
$keyInfo

function Get-KeyPress {
    param (
        [Parameter(Mandatory)][ConsoleKey]$Key,
        [System.ConsoleModifiers]$ModifierKey = 0
    )
    if ([Console]::KeyAvailable) {
        $pressedKey = [Console]::ReadKey($true)
        $isPressedKey = $key -eq $pressedKey.Key
        if ($isPressedKey) {
            $pressedKey.Modifiers -eq $ModifierKey
        }
        else {
            [Console]::Beep(1800, 200)
            $false
        }
    }
}

Write-Warning 'Press Ctrl+Shift+Q to exit'
do {
    Write-Host "." -NoNewline
    $pressed = Get-KeyPress -Key Q -ModifierKey 'Control,Shift'
    if ($pressed) { break }
    Start-Sleep 1
} while ($true)
```
# Drawing

[API](https://learn.microsoft.com/en-us/dotnet/api/system.drawing?view=net-7.0&redirectedfrom=MSDN)
```PowerShell
Add-Type -AssemblyName System.Drawing
$Width = 800
$Height = 400
$image = New-Object System.Drawing.Bitmap($Width,$Height)
$graphic = [System.Drawing.Graphics]::FromImage($image)
$background_color = [System.Drawing.Brushes]::Blue # задать цвет фона (синий)
$graphic.FillRectangle($background_color, 0, 0, $image.Width, $image.Height)
$text_color = [System.Drawing.Brushes]::White # задать цвет текста (белый)
$font = New-Object System.Drawing.Font("Arial", 20, [System.Drawing.FontStyle]::Bold) # задать шрифт
$text = "PowerShell" # указать текст
$text_position = New-Object System.Drawing.RectangleF(320, 180, 300, 100)  # задать положение текста (x, y, width, height)
$graphic.DrawString($text, $font, $text_color, $text_position) # нанести текст на изображение
$image.Save("$home\desktop\powershell_image.bmp", [System.Drawing.Imaging.ImageFormat]::Bmp) # сохранить изображение
$image.Dispose() # освобождение ресурсов
```
`$path = "$home\desktop\powershell_image.bmp"` \
`Invoke-Item $path`
```PowerShell
$src_image = [System.Drawing.Image]::FromFile($path)
$Width = 400
$Height = 200
$dst_image = New-Object System.Drawing.Bitmap -ArgumentList $src_image, $Width, $Height # изменить размер изображения
$dst_image.Save("$home\desktop\powershell_image_resize.bmp", [System.Drawing.Imaging.ImageFormat]::Bmp)

$rotated_image = $src_image.Clone() # создать копию исходного изображения
$rotated_image.RotateFlip([System.Drawing.RotateFlipType]::Rotate180FlipNone) # перевернуть изображение на 180 градусов
$rotated_image.Save("$home\desktop\powershell_image_rotated.bmp", [System.Drawing.Imaging.ImageFormat]::Bmp)
$src_image.Dispose() # закрыть (отпустить) исходный файл
```
# ObjectEvent
```PowerShell
$Timer = New-Object System.Timers.Timer
$Timer.Interval = 1000
Register-ObjectEvent -InputObject $Timer -EventName Elapsed -SourceIdentifier Timer.Output -Action {
$Random = Get-Random -Min 0 -Max 100
Write-Host $Random 
}
$Timer.Enabled = $True
```
`$Timer.Enabled = $False` остановить \
`$Timer | Get-Member -MemberType Event` отобразить список всех событий объекта \
`Get-EventSubscriber` список зарегистрированных подписок на события в текущей сессии \
`Unregister-Event -SourceIdentifier Timer.Output` удаляет регистрацию подписки на событие по имени события (EventName) или все * \
`-Forward` перенаправляет события из удаленного сеанса (New-PSSession) в локальный сеанс \
`-SupportEvent` не выводит результат регистрации события на экран (и Get-EventSubscriber и Get-Job)
```
Register-EngineEvent -SourceIdentifier PowerShell.Exiting -Action {
$date = Get-Date -f hh:mm:ss
(New-Object -ComObject Wscript.Shell).Popup("PowerShell Exit: $date",0,"Action",64)
}
```

# Base64

### UTF8

`$loginPassword = "login:password"` \
`$Base64 = [Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($loginPassword))` закодировать логин и пароль в строку Base64 \
`[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($Base64))` преобразовать в байты и обратно декодировать в исходную строку с помощью UTF-8 кодировки

### Unicode
```PowerShell
$text = "password"
$byte = [System.Text.Encoding]::Unicode.GetBytes($text) # преобразует строку $text в последовательность байтов, используя кодировку Unicode
$base64 = [System.Convert]::ToBase64String($byte) # байты конвертируются в строку Base64 с помощью метода ToBase64String
$decode_base64 = [System.Convert]::FromBase64String($base64) # декодировать строку Base64 обратно в последовательность байтов с помощью метода FromBase64String
$decode_string = [System.Text.Encoding]::Unicode.GetString($decode_base64) # закодированные байты преобразуются обратно в строку с использованием кодировки Unicode с помощью метода GetString
```
### Image
```PowerShell
$path_image = "$home\Documents\1200x800.jpg"
$BBase64 = [System.Convert]::ToBase64String((Get-Content $path_image -Encoding Byte))
Add-Type -assembly System.Drawing
$Image = [System.Drawing.Bitmap]::FromStream([IO.MemoryStream][Convert]::FromBase64String($BBase64))
$Image.Save("$home\Desktop\1200x800.jpg")
```
# Excel
```PowerShell
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
### Excel Application Open
```PowerShell
$path = "$home\Desktop\Services-to-Excel.xlsx"
$Excel = New-Object -ComObject Excel.Application
$Excel.Visible = $false
$ExcelWorkBook = $excel.Workbooks.Open($path)` открыть xlsx-файл
$ExcelWorkBook.Sheets | select Name,Index` отобразить листы
$ExcelWorkSheet = $ExcelWorkBook.Sheets.Item(1)` открыть лист по номеру Index
1..100 | %{$ExcelWorkSheet.Range("A$_").Text}` прочитать значение из столбца А строки c 1 по 100
$Excel.Quit()
```
### ImportExcel

`Install-Module -Name ImportExcel` \
`$data | Export-Excel .\Data.xlsx` \
`$data = Import-Excel .\Data.xlsx`

`$data = ps` \
`$Chart = New-ExcelChartDefinition -XRange CPU -YRange WS -Title "Process" -NoLegend` \
`$data | Export-Excel .\ps.xlsx -AutoNameRange -ExcelChartDefinition $Chart -Show`

# CSV

`Get-Service | Select Name,DisplayName,Status,StartType | Export-Csv -path "$home\Desktop\Get-Service.csv" -Append -Encoding Default` экспортировать в csv (-Encoding UTF8) \
`Import-Csv "$home\Desktop\Get-Service.csv" -Delimiter ","` импортировать массив
```PowerShell
$data = ConvertFrom-Csv @"
Region,State,Units,Price
West,Texas,927,923.71
$null,Tennessee,466,770.67
"@
```
`$systeminfo = systeminfo /FO csv | ConvertFrom-Csv` вывод работы программы в CSV и конвертация в объект \
`$systeminfo."Полный объем физической памяти"` \
`$systeminfo."Доступная физическая память"`

### ConvertFrom-String
```PowerShell
'
log = 
{
   level = 4;
};
' | ConvertFrom-String` создает PSCustomObject (разбивает по пробелам, удаляет все пробелы и пустые строки)
```
### ConvertFrom-StringData
```PowerShell
"
key1 = value1
key2 = value2
" | ConvertFrom-StringData # создает Hashtable
```
# XML
```PowerShell
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
`Get-Service | Export-Clixml -path $home\desktop\test.xml` экспортировать объект PowerShell в XML \
`Import-Clixml -Path $home\desktop\test.xml` импортировать объект XML в PowerShell \
`ConvertTo-Xml (Get-Service)`

### Get-CredToXML
```PowerShell
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
`$Cred = Get-CredToXML` \
`$Login = $Cred.UserName` \
`$PasswordText = $Cred.GetNetworkCredential().password` получить пароль в текстовом виде

### XmlWriter (Extensible Markup Language)
```PowerShell
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
        $XmlObjectWriter.WriteStartElement("Fonts")   		# <Fonts>
            $XmlObjectWriter.WriteElementString("Name","Arial")
            $XmlObjectWriter.WriteElementString("Size","12")
        $XmlObjectWriter.WriteEndElement()               	# </Fonts>
    $XmlObjectWriter.WriteEndElement()` конечный элемент </Configuration>
$XmlObjectWriter.WriteEndElement()` конечный элемент </Root>

$XmlObjectWriter.WriteEndDocument()` завершить запись в документ
$XmlObjectWriter.Flush()
$XmlObjectWriter.Close()
```
### CreateElement
```PowerShell
$xml = [xml](gc $home\desktop\test.xml)
$xml.Root.Configuration.Fonts
$NewElement = $xml.CreateElement("Fonts")` выбрать элемент куда добавить
$NewElement.set_InnerXML("<Name>Times New Roman</Name><Size>14</Size>")` Заполнить значениями дочерние элементы Fonts
$xml.Root.Configuration.AppendChild($NewElement)` добавить элемент новой строкой в Configuration (родитель Fonts)
$xml.Save("$home\desktop\test.xml")
```
# JSON
```PowerShell
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
# YAML
```PowerShell
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
# TOML

`Install-Module -Name PSToml -Scope CurrentUser` Устанавливаем модуль для чтения toml (PSToml) \
`$toml = Get-Content "$home\Documents\Git\lifailon.github.io\hugo.toml"` Читаем содержимое конфигурации Hugo в формате toml \
`$json = ConvertFrom-Toml $toml | ConvertTo-Json -Depth 3` Конвертируем TOML в JSON \
`$json | Out-File "$home\Documents\Git\lifailon.github.io\hugo.json"` Сохраняем конфигурационный файл в формате JSON

# Markdown

### Convert Markdown to HTML
```PowerShell
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
```PowerShell
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
# ConvertTo-Markdown
```PowerShell
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
# HTML

### ConvertFrom-Html
```PowerShell
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

`Get-Process | select Name, CPU | ConvertTo-Html -As Table > "$home\desktop\proc-table.html"` вывод в формате List (Format-List) или Table (Format-Table)
```PowerShell
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
```PowerShell
Import-Module PSWriteHTML
(Get-Module PSWriteHTML).ExportedCommands
Get-Service | Out-GridHtml -FilePath ~\Desktop\Get-Service-Out-GridHtml.html
```
### HtmlReport
```PowerShell
Import-Module HtmlReport
$topVM = ps | Sort PrivateMemorySize -Descending | Select -First 10 | %{,@(($_.ProcessName + " " + $_.Id), $_.PrivateMemorySize)}
$topCPU = ps | Sort CPU -Descending | Select -First 10 | %{,@(($_.ProcessName + " " + $_.Id), $_.CPU)}
New-Report -Title "Piggy Processes" -Input {
New-Chart Bar "Top VM Users" -input $topVm
New-Chart Column "Top CPU Overall" -input $topCPU
ps | Select ProcessName, Id, CPU, WorkingSet, *MemorySize | New-Table "All Processes"
} > ~\Desktop\Get-Process-HtmlReport.html
```
# HtmlAgilityPack

[Source](https://www.nuget.org/packages/HtmlAgilityPack)
```PowerShell
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
```PowerShell
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

# WMI

### Windows Management Instrumentation

`Get-WmiObjec -ComputerName localhost -Namespace root -class "__NAMESPACE" | select name,__namespace` отобразить дочернии Namespace (логические иерархические группы) \
`Get-WmiObject -List` отобразить все классы пространства имен "root\cimv2" (по умолчанию), свойства (описывают конфигурацию и текущее состояние управляемого ресурса) и их методы (какие действия позволяет выполнить над этим ресурсом) \
`Get-WmiObject -List | Where-Object {$_.name -match "video"}` поиск класса по имени, его свойств и методов \
`Get-WmiObject -ComputerName localhost -Class Win32_VideoController` отобразить содержимое свойств класса

`gwmi -List | where name -match "service" | ft -auto` если в таблице присутствуют Methods, то можно взаимодействовать {StartService, StopService} \
`gwmi -Class win32_service | select *` отобразить список всех служб и всех их свойств \
`Get-CimInstance Win32_service` обращается на прямую к "root\cimv2" \
`gwmi win32_service -Filter "name='Zabbix Agent'"` отфильтровать вывод по имени \
`(gwmi win32_service -Filter "name='Zabbix Agent'").State` отобразить конкретное свойство \
`gwmi win32_service -Filter "State = 'Running'"` отфильтровать запущенные службы \
`gwmi win32_service -Filter "StartMode = 'Auto'"` отфильтровать службы по методу запуска \
`gwmi -Query 'select * from win32_service where startmode="Auto"'` WQL-запрос (WMI Query Language) \
`gwmi win32_service | Get-Member -MemberType Method` отобразить все методы взаимодействия с описание применения (Delete, StartService) \
`(gwmi win32_service -Filter 'name="Zabbix Agent"').Delete()` удалить службу \
`(gwmi win32_service -Filter 'name="MSSQL$MSSQLE"').StartService()` запустить службу

`Get-CimInstance -ComputerName $srv Win32_OperatingSystem | select LastBootUpTime` время последнего включения \
`gwmi -ComputerName $srv -Class Win32_OperatingSystem | select LocalDateTime,LastBootUpTime` текущее время и время последнего включения \
`gwmi Win32_OperatingSystem | Get-Member -MemberType Method` методы reboot и shutdown \
`(gwmi Win32_OperatingSystem -EnableAllPrivileges).Reboot()` используется с ключем повышения привелегий \
`(gwmi Win32_OperatingSystem -EnableAllPrivileges).Win32Shutdown(0)` завершение сеанса пользователя
```PowerShell
$system = Get-WmiObject -Class Win32_OperatingSystem
$InstallDate = [Management.ManagementDateTimeconverter]::ToDateTime($system.installdate)` Получаем дату установки ОС
$AfterInstallDays = ((Get-Date) — $Installdate).Days` Вычисляем время, прошедшее с момента установки
$ShortInstallDate = "{0:yyyy-MM-dd HH:MM}" -f ($InstallDate)
"Система установлена: $ShortInstallDate (Прошло $AfterInstalldays дней)"
```
`(Get-WmiObject win32_battery).estimatedChargeRemaining` заряд батареи в процентах \
`gwmi Win32_UserAccount` доменные пользователи \
`(gwmi Win32_SystemUsers).PartComponent` \
`Get-CimInstance -ClassName Win32_LogonSession` \
`Get-CimInstance -ClassName Win32_BIOS`

`gwmi -list -Namespace root\CIMV2\Terminalservices` \
`(gwmi -Class Win32_TerminalServiceSetting -Namespace root\CIMV2\TerminalServices).AllowTSConnections` \
`(gwmi -Class Win32_TerminalServiceSetting -Namespace root\CIMV2\TerminalServices).SetAllowTSConnections(1)` включить RDP
```
$srv = "localhost"
gwmi Win32_logicalDisk -ComputerName $srv | where {$_.Size -ne $null} | select @{
Label="Value"; Expression={$_.DeviceID}}, @{Label="AllSize"; Expression={
[string]([int]($_.Size/1Gb))+" GB"}},@{Label="FreeSize"; Expression={
[string]([int]($_.FreeSpace/1Gb))+" GB"}}, @{Label="Free%"; Expression={
[string]([int]($_.FreeSpace/$_.Size*100))+" %"}}
```

### Network Level Authentication

`(gwmi -class "Win32_TSGeneralSetting" -Namespace root\cimv2\Terminalservices -Filter "TerminalName='RDP-tcp'").UserAuthenticationRequired` \
`(gwmi -class "Win32_TSGeneralSetting" -Namespace root\cimv2\Terminalservices -Filter "TerminalName='RDP-tcp'").SetUserAuthenticationRequired(1)` включить NLA \
`Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name SecurityLayer` отобразить значение (2) \
`Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name UserAuthentication` отобразить значение (1) \
`Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name SecurityLayer -Value 0` изменить значение \
`Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name UserAuthentication -Value 0` \
`REG ADD HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\CredSSP\Parameters /v AllowEncryptionOracle /t REG_DWORD /d 2` отключить на клиентском компьютере проверку версии CredSSP, если на целевом комьютере-сервере не установлены обновления KB4512509 от мая 2018 года

# Regedit

`Get-PSDrive` список всех доступных дисков/разделов, их размер и веток реестра \
`cd HKLM:\` HKEY_LOCAL_MACHINE \
`cd HKCU:\` HKEY_CURRENT_USER \
`Get-Item` получить информацию о ветке реестра \
`New-Item` создать новый раздел реестра \
`Remove-Item` удалить ветку реестра \
`Get-ItemProperty` получить значение ключей/параметров реестра (это свойства ветки реестра, аналогично свойствам файла) \
`Set-ItemProperty` изменить название или значение параметра реестра \
`New-ItemProperty` создать параметр реестра \
`Remove-ItemProperty` удалить параметр

`Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* | Select DisplayName` список установленных программ \
`Get-Item HKCU:\SOFTWARE\Microsoft\Office\16.0\Outlook\Profiles\Outlook\9375CFF0413111d3B88A00104B2A6676\00000002` посмотреть содержимое Items \
`(Get-ItemProperty HKCU:\SOFTWARE\Microsoft\Office\16.0\Outlook\Profiles\Outlook\9375CFF0413111d3B88A00104B2A6676\00000002)."New Signature"` отобразить значение (Value) свойства (Property) Items \
`$reg_path = "HKCU:\SOFTWARE\Microsoft\Office\16.0\Outlook\Profiles\Outlook\9375CFF0413111d3B88A00104B2A6676\00000002"` \
`$sig_name = "auto"` \
`Set-ItemProperty -Path $reg_path -Name "New Signature" -Value $sig_name` изменить или добавить в корне ветки (Path) свойство (Name) со значением (Value) \
`Set-ItemProperty -Path $reg_path -Name "Reply-Forward Signature" -Value $sig_name`
```
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\taskmgr.exe]
"Debugger"="\"C:\\Windows\\System32\\Taskmgr.exe\""
```
# Performance

`lodctr /R` пересоздать счетчиков производительности из системного хранилища архивов (так же исправляет счетчики для CIM, например, для cpu Win32_PerfFormattedData_PerfOS_Processor и iops Win32_PerfFormattedData_PerfDisk_PhysicalDisk) \
`(Get-Counter -ListSet *).CounterSetName` вывести список всех доступных счетчиков производительности в системе \
`(Get-Counter -ListSet *memory*).Counter` поиск по wildcard-имени во всех счетчиках (включая дочернии) \
`Get-Counter "\Memory\Available MBytes"` объем свободной оперативной памяти \
`Get-Counter -cn $srv "\LogicalDisk(*)\% Free Space"` % свободного места на всех разделах дисков \
`(Get-Counter "\Process(*)\ID Process").CounterSamples` \
`Get-Counter "\Processor(_Total)\% Processor Time" –ComputerName $srv -MaxSamples 5 -SampleInterval 2` 5 проверок каждые 2 секунды \
`Get-Counter "\Процессор(_Total)\% загруженности процессора" -Continuous` непрерывно \
`(Get-Counter "\Процессор(*)\% загруженности процессора").CounterSamples`

`(Get-Counter -ListSet *интерфейс*).Counter` найти все счетчики \
`Get-Counter "\Сетевой интерфейс(*)\Всего байт/с"` отобразить все адаптеры (выбрать действующий по трафику)
```PowerShell
$WARNING = 25
$CRITICAL = 50
$TransferRate = ((Get-Counter "\\huawei-mb-x-pro\сетевой интерфейс(intel[r] wi-fi 6e ax211 160mhz)\всего байт/с"
).countersamples | select -ExpandProperty CookedValue)*8
$NetworkUtilisation = [math]::round($TransferRate/1000000000*100,2)
if ($NetworkUtilisation -gt $CRITICAL){
Write-Output "CRITICAL: $($NetworkUtilisation) % Network utilisation, $($TransferRate.ToString('N0')) b/s"   
#exit 2		
}
if ($NetworkUtilisation -gt $WARNING){
Write-Output "WARNING: $($NetworkUtilisation) % Network utilisation, $($TransferRate.ToString('N0')) b/s"
#exit 1
}
Write-Output "OK: $($NetworkUtilisation) % Network utilisation, $($TransferRate.ToString('N0')) b/s"   
#exit 0
```
# SNMP

### Setup SNMP Service

`Install-WindowsFeature SNMP-Service,SNMP-WMI-Provider -IncludeManagementTools` установить роль SNMP и WMI провайдер через Server Manager \
`Get-WindowsFeature SNMP*` \
`Add-WindowsCapability -Online -Name SNMP.Client~~~~0.0.1.0` установить компонент Feature On Demand для Windows 10/11` \
`Get-Service SNMP*` \
`Get-NetFirewallrule -DisplayName *snmp* | ft` \
`Get-NetFirewallrule -DisplayName *snmp* | Enable-NetFirewallRule`

### Setting SNMP Service via Regedit

Agent: \
`New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\services\SNMP\Parameters\RFC1156Agent" -Name "sysContact" -Value "lifailon-user"` создать (New) или изменить (Set) \
`New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\services\SNMP\Parameters\RFC1156Agent" -Name "sysLocation" -Value "plex-server"`

Security: \
`New-Item -Path "HKLM:\SYSTEM\CurrentControlSet\services\SNMP\Parameters\TrapConfiguration\public"` создать новый community string \
`New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\SNMP\Parameters\ValidCommunities" -Name "public" -Value 16` назначить права на public \
`1 — NONE` \
`2 — NOTIFY` позволяет получать SNMP ловушки \
`4 — READ ONLY` позволяет получать данные с устройства \
`8 — READ WRITE` позволяет получать данные и изменять конфигурацию устройства \
`16 — READ CREATE` позволяет читать данные, изменять и создавать объекты \
`New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\SNMP\Parameters\PermittedManagers" -Name "1" -Value "192.168.3.99"` от кого разрешено принимать запросы \
`Get-Service SNMP | Restart-Service`

### snmpwalk

`snmpwalk -v 2c -c public 192.168.3.100` \
`snmpwalk -v 2c -c public -O e 192.168.3.100`

### SNMP Modules

`Install-Module -Name SNMP` \
`Get-SnmpData -IP 192.168.3.100 -OID 1.3.6.1.2.1.1.4.0 -UDPport 161 -Community public` \
`(Get-SnmpData -IP 192.168.3.100 -OID 1.3.6.1.2.1.1.4.0).Data` \
`Invoke-SnmpWalk -IP 192.168.3.100 -OID 1.3.6.1.2.1.1` пройтись по дереву OID \
`Invoke-SnmpWalk -IP 192.168.3.100 -OID 1.3.6.1.2.1.25.6.3.1.2` список установленного ПО \
`Invoke-SnmpWalk -IP 192.168.3.100 -OID 1.3.6.1.2.1.25.2.3.1` список разделов и памяти (C: D: Virtual Memory и Physical Memory) \
`Set-SnmpData` изменение данных на удаленном устройстве

`Install-Module -Name SNMPv3` \
`Invoke-SNMPv3Get` получение данных по одному OID \
`Invoke-SNMPv3Set` изменение данных \
`Invoke-SNMPv3Walk` обход по дереву OID \
`Invoke-SNMPv3Walk -UserName lifailon -Target 192.168.3.100 -AuthSecret password -PrivSecret password -OID 1.3.6.1.2.1.1 -AuthType MD5 -PrivType AES128`

### Lextm SharpSnmpLib

[Синтаксис](https://learn.microsoft.com/ru-ru/powershell/dsc/reference/resources/windows/fileresource?view=dsc-1.1) \
[Download lib](https://api.nuget.org/v3-flatcontainer/lextm.sharpsnmplib/12.5.2/lextm.sharpsnmplib.12.5.2.nupkg)

`Add-Type -LiteralPath "$home\Desktop\lextm.sharpsnmplib-12.5.2\net471\SharpSnmpLib.dll"`
```PowerShell
$port = 161
$OID = "1.3.6.1.2.1.1.4.0"
$variableList = New-Object Collections.Generic.List[Lextm.SharpSnmpLib.Variable]
$variableList.Add([Lextm.SharpSnmpLib.Variable]::new([Lextm.SharpSnmpLib.ObjectIdentifier]::new($OID)))
$timeout = 3000
[Net.IPAddress]$ip = "192.168.3.100"
$endpoint = New-Object Net.IpEndPoint $ip, $port
$Community = "public"
[Lextm.SharpSnmpLib.VersionCode]$Version = "V2"

$message = [Lextm.SharpSnmpLib.Messaging.Messenger]::Get(
$Version,
$endpoint,
$Community,
$variableList,
$TimeOut
)
$message.Data.ToString()
```
### Walk
```PowerShell
[Lextm.SharpSnmpLib.ObjectIdentifier]$OID = "1.3.6.1.2.1.1" # дерево или конечный OID
$WalkMode = [Lextm.SharpSnmpLib.Messaging.WalkMode]::WithinSubtree # режим обхода по дереву
$results = New-Object Collections.Generic.List[Lextm.SharpSnmpLib.Variable]
$message = [Lextm.SharpSnmpLib.Messaging.Messenger]::Walk(
  $Version,
  $endpoint,
  $Community,
  $OID,
  $results,
  $TimeOut,
  $WalkMode
)
$results

$results2 = @()
foreach ($d in $results) {
$results2 +=[PSCustomObject]@{'ID'=$d.id.ToString();'Data'=$d.Data.ToString()} # перекодировать вывод построчно в строку
}
$results2
```

# PackageManagement

`Import-Module PackageManagement` импортировать модуль \
`Get-Module PackageManagement` информация о модуле \
`Get-Command -Module PackageManagement` отобразить все командлеты модуля \
`Get-Package` отобразить все установленные пакеты PowerShellGallery \
`Get-Package -ProviderName msi,Programs` список установленных программ
`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` включить использование протокол TLS 1.2 (если не отключены протоколы TLS 1.0 и 1.1) \
`Get-PackageSource` источники установки пакетов \
`Get-PackageProvider` отобразить список провайдеров менеджеров пакетов \
`Get-PackageProvider | Where-Object Name -Match nuget` \
`Find-PackageProvider` отображение всех доступных менеджеров пакетов \
`Find-PackageProvider nuget` \
`Install-PackageProvider NuGet -Force` установить менеджер пакетов nuget \
`Install-PackageProvider PSGallery -Force` установить источник \
`Install-PackageProvider Chocolatey -Force` \
`Set-PackageSource nuget -Trusted` разрешить установку пакетов из указанного источника \
`Register-PSRepository -Name "NuGet" -SourceLocation "https://www.nuget.org/api/v2" -InstallationPolicy Trusted` зарегестрировать менеджер пакетов используя url (работает для Power Shell Core) \
`Set-PackageSource -Name NuGet -Trusted` изменить источник по умолчанию \
`Find-Package PSEverything` поиск пакетов по имени во всех менеджерах \
`Find-Package PSEverything -Provider NuGet` поиск пакета у выбранного провайдера \
`Install-Module PSEverything -Scope CurrentUser` установить модуль для текущего пользователя \
`Install-Package -Name VeeamLogParser -ProviderName PSGallery -scope CurrentUser` \
`Get-Command *Veeam*` \
`Import-Module -Name VeeamLogParser` загрузить модуль \
`Get-Module VeeamLogParser | select -ExpandProperty ExportedCommands` отобразить список функций

### winget

[Source](https://github.com/microsoft/winget-cli)
[Web](https://winget.run)

`winget list` список установленных пакетов \
`winget search VLC` найти пакет \
`winget show VideoLAN.VLC` информация о пакете \
`winget show VideoLAN.VLC --versions` список доступных версий в репозитории \
`winget install VideoLAN.VLC` установить пакет \
`winget uninstall VideoLAN.VLC` удалить пакет \
`winget download jqlang.jq` загрузкить пакет (https://github.com/jqlang/jq/releases/download/jq-1.7/jq-windows-amd64.exe) \
`winget install jqlang.jq` добавляет в переменную среду и псевдоним командной строки jq \
`winget uninstall jqlang.jq`

### jqlang
```PowerShell
[uri]$url = $($(irm https://api.github.com/repos/jqlang/jq/releases/latest).assets.browser_download_url -match "windows-amd64").ToString() # получить версию latest на GitHub
irm $url -OutFile "C:\Windows\System32\jq.exe" # загрузить jq.exe
```
### Scoop

`Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` \
`irm get.scoop.sh | iex` установка \
`scoop help` \
`scoop search jq` \
`scoop info jq` \
`(scoop info jq).version` \
`scoop cat jq` \
`scoop download jq` C:\Users\lifailon\scoop\cache \
`scoop install jq` C:\Users\lifailon\scoop\apps\jq\1.7 \
`scoop list` \
`(scoop list).version` \
`scoop uninstall jq`

### Chocolatey
```PowerShell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```
`choco -v` \
`choco -help` \
`choco list` \
`choco install adobereader`

# NuGet

`Invoke-RestMethod https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -OutFile "$home\Documents\nuget.exe"` \
`Invoke-Expression "$home\Documents\nuget.exe search Selenium.WebDriver"` \
`Invoke-Expression "$home\Documents\nuget.exe install Selenium.WebDriver"` \
`Get-Item $home\Documents\*Selenium*`

`& "$home\Documents\nuget.exe" list console-translate` \
`$nuget_api_key = "<API-KEY>"` \
`$source = "https://api.nuget.org/v3/index.json"` \
`$Name = "Console-Translate"` \
`$path = "$home\Documents\$Name"` \
`New-Item -Type Directory $path` \
`Copy-Item "$home\Documents\Git\$Name\$Name\0.2\*" "$path\"` \
`Copy-Item "$home\Documents\Git\$Name\LICENSE" "$path\"` \
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
`Set-Location $path` \
`& "$home\Documents\nuget.exe" pack "$path\$Name.nuspec"` \
`& "$home\Documents\nuget.exe" push "$path\$Name.0.2.2.nupkg" -ApiKey $nuget_api_key -Source $source` \
`& "$home\Documents\nuget.exe" push "$path\$Name.0.2.2.nupkg" -ApiKey $nuget_api_key -Source $source -SkipDuplicate`

`Install-Package Console-Translate -Source nuget.org` \
`Get-Package Console-Translate | select *`

`Register-PSRepository -Name "NuGet" -SourceLocation "https://www.nuget.org/api/v2" -InstallationPolicy Trusted` \
`Get-PSRepository` \
`Find-Module -Name Console-Translate` \
`Install-Module Console-Translate -Repository NuGet`

`& "$home\Documents\nuget.exe" delete Console-Translate 0.2.0 -Source https://api.nuget.org/v3/index.json -ApiKey $nuget_api_key -NoPrompt`

# Modules

### Get-Query

`Install-Module Get-Query -Repository NuGet` установить модуль \
`Get-Help Get-Query` \
`Get-Query localhost` отобразить всех авторизованных пользователей, их статус и время работы (по умолчанию localhost) \
`Get-Query 192.168.1.1.1 -proc` список всех пользовательских процессов (по умолчанию -user *) \
`Get-Query 192.168.1.1.1 -proc -user username` список процессов указанного пользователя

### Console-Translate

`Install-Module Console-Translate -Repository NuGet` \
`Get-Translate "Module for text translation"` \
`Get-Translate "Модуль для перевода текста"` \
`Get-Translate "Привет world" -LanguageSelected` т.к. больше латинских символов (на 1), то перевод будет произведен на английский язык \
`Get-Translate "Hello друг" -LanguageSelected` перевод на русский язык \
`Get-Translate -Text "Модуль для перевода текста" -LanguageSource ru -LanguageTarget tr` \
`Get-Translate -Provider MyMemory -Text "Hello World" -Alternatives` выбрать провайдер перевода и добавить альтернативные варианты вывода \
`Get-DeepLX "Get select" ru` \
`Start-DeepLX -Job` запустить сервер в режиме процесса \
`Start-DeepLX -Status` \
`Get-DeepLX -Server 192.168.3.99 -Text "Module for text translation" ru` \
`Stop-DeepLX` \
`Get-LanguageCode` получение кодов языков по стандарту ISO-639-1

### Console-Download

`Install-Module Console-Download -Repository NuGet` устаовить модуль из менеджера пакетов NuGet \
`Invoke-Expression $(Invoke-RestMethod "https://raw.githubusercontent.com/Lifailon/Console-Download/rsa/module/Console-Download/Console-Download.psm1")` или импортировать модуль из GitHub репозитория в текущую сессию PowerShell \
`Invoke-Download -Url "https://github.com/PowerShell/PowerShell/releases/download/v7.4.2/PowerShell-7.4.2-win-x64.zip"` загрузить файл в один поток с отображением скорости загрузки в реальном времен (путь загрузки файла по умолчанию: $home\downloads) \
`Invoke-Download -Url "https://github.com/PowerShell/PowerShell/releases/download/v7.4.2/PowerShell-7.4.2-win-x64.zip" -Thread 3` загрузить один и тотже файл в 3 потока (создается 3 файла, доступно от 1 до 20 потоков)
```PowerShell
$urls = @(
    "https://github.com/PowerShell/PowerShell/releases/download/v7.4.2/PowerShell-7.4.2-win-x64.zip",
    "https://github.com/Lifailon/helperd/releases/download/0.0.1/Helper-Desktop-Setup-0.0.1.exe"
)
Invoke-Download $urls # загрузить параллельно 2 файла
```
`$urls = Get-Content "$home\Desktop\links.txt"` \
`Invoke-Download $urls` передайте список URL-адресов из файла для загрузки файлов, эквивалентному числу url

`$urls = Get-LookingGlassList` отобразить актуальный список конечных точек Looking Glass через Looking.House (выдает 3 ссылки на загрузку файлов по 10, 100 и 1000 мбайт для каждого региона) \
`$usaNy = $urls | Where-Object region -like *USA*New*York*` отфильтровать список по региону и городу \
`$url = $usaNy[0].url100mb` забрать первую ссылку с файлов на 100 МБайт \
`Invoke-Download $url` начать загрузку файла \
`Invoke-Download $url -Thread 3` начать загрузку 3-х одинаковых файлов

### PSEverything

`Install-Module PSEverything -Repository NuGet` \
`Find-Everything pingui` найти все файлы в системе (на всех локальных дисках) с именем *pingui* через dll csharp версии Everything (по умолчанию) \
`Find-Everything pingui-0.1.py | Format-List` \
`Find-Everything pingui -es` использовать cli версию Everything для поиска (при первом использовании версии, необходимо дождаться автоматической установки файлов зависимостей) \
`Find-Everything pingui-0.1 -ComputerName localhost` поиск на удаленном компьютере через REST API, если запущен HTTP-сервер Everything

### HardwareMonitor

`Install-Module HardwareMonitor -Repository NuGet -Scope AllUsers` \
`Install-LibreHardwareMonitor` установить и запустить LibreHardwareMonitor в систему (https://github.com/LibreHardwareMonitor/LibreHardwareMonitor) \
`Install-OpenHardwareMonitor` установить OpenHardwareMonitor (https://github.com/openhardwaremonitor/openhardwaremonitor) \
`Get-Sensor | Where-Object {($_.SensorName -match "Temperature") -or ($_.SensorType -match "Temperature")} | Format-Table` использовать LibreHardwareMonitor и WMI/CIM (по умолчанию) и отфильтровать вывод по наименованию датчикам или типу сенсора для вывода датчиков температуры \
`Get-Sensor -Open` использовать OpenHardwareMonitor \
`Get-Sensor -ComputerName 192.168.3.99 -Port 8086 | Format-Table` вывести датчики системы через REST API \
`Get-Sensor -ComputerName 192.168.3.99 -Port 8085 -User hardware -Password monitor | Where-Object Value -notmatch "^0,0" | Format-Table` использовать авторизацию и отфильтровать вывод не пустых датчиков \
`Get-Sensor -Library | Where-Object Value -ne 0 | Format-Table` использовать динамическую библиотеку (dll) через .NET

### CrystalDisk-Cli

`Install-Module CrystalDisk-Cli -Repository NuGet` \
`Get-DiskInfoSettings` отобразить настройки программы Crystal-DiskInfo (https://github.com/hiyohiyo/CrystalDiskInfo) \
`Get-DiskInfoSettings -AutoRefresh 5` изминить время сканирования на 5 минут \
`Get-DiskInfo -List` отобразить список дисков \
`Get-DiskInfo` получить результаты последнего сканирования (статус, температура и т.п.) \
`Get-DiskInfo -Report | Select-Object Name,Date,HealthStatus,Temperature` получить актуальный отчет (запустить сканирование и дождаться результатов)

### PS-Pi-Hole
```PowerShell
$path_psm = ($env:PSModulePath.Split(";")[0])+"\Invoke-Pi-Hole\Invoke-Pi-Hole.psm1"
if (!(Test-Path $path_psm)) {
    New-Item $path_psm -ItemType File -Force
}
irm https://raw.githubusercontent.com/Lifailon/PS-Pi-Hole/rsa/Invoke-Pi-Hole/Invoke-Pi-Hole.psm1 | Out-File $path_psm -Force
```
`sudo cat /etc/pihole/setupVars.conf | grep WEBPASSWORD` получить токен доступа \
`$Server = "192.168.1.253"` \
`$Token = "5af9bd44aebce0af6206fc8ad4c3750b6bf2dd38fa59bba84ea9570e16a05d0f"` \
`Invoke-Pi-Hole -Versions -Server $Server -Token $Token` получить текущую версию ядра (backend) и веб (frontend) на сервере а также последнюю доступную версию для обновления \
`Invoke-Pi-Hole -Releases -Server $Server -Token $Token` узнать последнюю доступную версию в репозитории на GitHub \
`Invoke-Pi-Hole -QueryLog -Server $Server -Token $Token` отобразить полный журнал запросов (клиент, домен назначения, тип записи, статус время запроса и адрес пересылки forward dns - куда ушел запрос) \
`Invoke-Pi-Hole -AdList -Server $Server -Token $Token` получить списки блокировак используемых на сервере (дата обновления, количество доменов и url-источника) \
`Invoke-Pi-Hole -Status -Server $Server -Token $Token` статус работы режима блокировки \
`Invoke-Pi-Hole -Enable -Server $Server -Token $Token` включить блокировку \
`Invoke-Pi-Hole -Disable -Server $Server -Token $Token` отключить блокировку \
`Invoke-Pi-Hole -Stats -Server $Server -Token $Token` подключиться к серверу Pi-Hole для получения статистики (метрики: количество доменов для блокировки, количество запросов и блокировок за сегодня и т.д.) \
`Invoke-Pi-Hole -QueryTypes -Server $Server -Token $Token` статистика запросов по типу записей относительно 100% \
`Invoke-Pi-Hole -TopClient -Server $Server -Token $Token` список самых активных клиентов (ip/name и количество запросов проходящих через сервер) \
`Invoke-Pi-Hole -TopPermittedDomains -Count 100 -Server $Server -Token $Token` список самых посещяемых доменов и количество запросов \
`Invoke-Pi-Hole -LastBlockedDomain -Server $Server -Token $Token` адрес последнего заблокированного домена \
`Invoke-Pi-Hole -ForwardServer -Server $Server -Token $Token` список серверов для пересылки, которым обычно выступает DNS-сервер стоящий за Pi-Hole в локальной сети, например AD \
`Invoke-Pi-Hole -Data -Server $Server -Token $Token` количество запросов за каждые 10 минут в течение последних 24 часов

### Check-Host
```PowerShell
$path = $(($env:PSModulePath -split ";")[0]) + "\Get-CheckHost"
if (Test-Path $path) {
    Remove-Item $path -Force -Recurse
    New-Item -ItemType Directory -Path $path
} else {
    New-Item -ItemType Directory -Path $path
}
Invoke-RestMethod "https://raw.githubusercontent.com/Lifailon/Check-Host/rsa/Get-CheckHost/Get-CheckHost.psm1" -OutFile "$path\Get-CheckHost.psm1"
```
`Install-Module CheckHost` установить модуль работы с Check-Host (https://check-host.net) через api \
`Get-CheckHost -List` список хостов (node) разных регионов (40) \
`Get-CheckHost -Server google.com -Type ping -Count 5` использовать 5 любых хостов для 4 пингов с каждого до указанного url (google) \
`Get-CheckHost -Server google.com -Type dns -Count 5` проверить DNS (возвращает А-запись или диапазон с ip-адресом) \
`Get-CheckHost -Server google.com:443 -Type http -Count 5` проверить доступность порта \
`Get-CheckHost -Server google.com:443 -Type tcp -Count 5` проверить доступность TCP или UDP порта

### PSDomainTest

`Install-Module PSDomainTest -Repository NuGet -Scope CurrentUser` \
`Get-DomainTest -Domain github.com -Warning` протестировать домен и DNS записи на ошибки (вывести только ошибки) через ZoneMaster (https://github.com/zonemaster/zonemaster) \
`Get-DomainTest -Domain github.com -Warning -json` вывод в формате json \
`Get-DomainTest -Domain github.com -html | Out-File .\result.html` получить отчет в формате HTML-таблицы с фильтрацией по столбцам

### WinAPI

`Install-Module ps.win.api -Repository NuGet -AllowClobber` \
`Import-Module ps.win.api` \
`Get-Command -Module ps.win.api` \
`Start-WinAPI` запустить сервер \
`Test-WinAPI` статус сервера \
`Stop-WinAPI` остановить сервер \
`Read-WinAPI` отобразить лог в реальном времени \
`Get-Hardware` вывести сводную информацию о системе с использованием потоков (фоновых заданий) \
`Get-DiskPhysical` отобразить список физических дисков, их размер, интерфейс и статус \
`Get-DiskPhysical -ComputerName 192.168.3.100 -Port 8443 -User rest -Pass api` получить информацию с удаленного сервер, на котором запущен сервер WinAPI (доступно для всех функций модуля) \
`Get-DiskLogical` список логических дисков (включая виртуальные диски), их файловая система, общий и используемый размер в гб и процентах \
`Get-DiskPartition` список разделов физических дисков (отобразит скрытые разделы, загрузочный и порядок назначения байт) \
`Get-Smart` статус работы всех дисков и текущая температура \
`Get-IOps` количество операций ввода/вывода дисковой подсистемы \
`Get-Files` подробная информация о файлах (добавляет дату создания, изменения, доступа, количество дочерних файлов и директорий) \
`Find-Process` поиск пути к исполняемому файлу по имени остановленного (не запущенного) процесса в директориях \
`Get-ProcessPerformance` информация о процессах (Get-Process) в человеко-читаемом формате \
`Get-CPU` список все ядер и нагрузка на каждое ядро и на все сразу (суммарное процессорное время, привилегированное и пользовательское время процессора) \
`Get-MemorySize` размер оперативной, виртуальной памяти, суммарное потребление памяти процессов и путь к файлу подкачки \
`Get-MemorySlots` список всех слотов оперативной памяти \
`Get-NetInterfaceStat` статистика активного сетевого интерфейса за время с момента загрузки системы (количество пакетов и ГБ) \
`Get-NetInterfaceStat -Current` текущая статистика активного сетевого интерфейса (количество пакетов и МБ/c) \
`Get-NetIpConfig` конфигурация всех сетевых интерфейсов (ip и mac-адрес, статус DHCP сервера, дата аренды) \
`Get-NetStat` развернутая статистика сетевых интерфейсов (из Get-NetTCPConnection), добавляет имя процесса, имя удаленного хоста (через nslookup), время работы процесса (не процессорное время) и путь к исполняемому файлу \
`Get-Performance` информация из счетчиков (Get-Counter) человеко-читаемом формате \
`Get-VideoCard` информация о всех видео-картах (наименование, частота и объем видео-памяти) \
`Get-Software` список установленного програмного обеспечения \
`Get-WinUpdate` список обновлений Windows (дата установки и источник) \
`Get-Driver` список установленных драйверов (имя, провайдер, версия и дата установки)

### pSyslog

`Install-Module pSyslog -Repository NuGet` \
`Start-pSyslog -Port 514` запустить сервер на порту 514 (по умолчанию) \
`Start-pSyslog -RotationSize 500` указать размер файла локального журнала для его ротации (обрезания) в КБ \
`Get-pSyslog -Status | Format-List` отобразить статус работы \
`Get-pSyslog` вывести журнал сообщений в реальном времени \
`Stop-pSyslog` остановить сервер \
`Send-pSyslog -Content "Test" -Server 192.168.3.99` отправить сообщение на Syslog-сервер \
`Send-pSyslog -Content "Test" -Server 192.168.3.99 -Type Informational -PortServer 514 -PortClient 55514` \
`(Get-Service -Name WinRM).Status | Send-pSyslog -Server 192.168.3.102 -Tag Service[WinRM]` \
`Send-pSyslog -Content "test" -Server 192.168.3.99 -PortServer 514 -Base64` использовать шифрование при отправки сообщения (расшифровка работает только для сервера pSyslog) \
`Start-UDPRelay -inPort 515 -outIP 192.168.3.102 -outPort 514` запустить сервер в режиме UDP-Relay, который слушает на порту 515 и переадресует сообщения на Syslog сервер 192.168.3.102 с портом 514 \
`Send-pSyslog -Server 192.168.3.99 -PortServer 515 -Content $(Get-Date)` отправить сообщение на сервере UDP-Relay \
`Show-pSyslog -Type Warning -Count` отобразить метрики (количество ошибок) \
`Show-pSyslog -Type Alert -Count` \
`Show-pSyslog -Type Critical -Count` \
`Show-pSyslog -Type Error -Count` \
`Show-pSyslog -Type Emergency -Count` \
`Show-pSyslog -Type Informational -Count` \
`Show-pSyslog -LogFile 05-06 | Out-GridView` прочитать локальный журнал логирования и вывести в GridView для фильтрации сообщений \
`Show-pSyslog -Count` отобразить количество сообщений локального журнала \
`Show-pSyslog -Count -LogFile 10-06` выбрать журнал по дате

### Syslog source message
```PowerShell
Add-Type -TypeDefinition @"
public enum Syslog_Facility {
    kern,     // 0  kernel (core) messages
    user,     // 1  user level messages
    mail,     // 2  mail system
    daemon,   // 3  system daemons
    auth,     // 4  security/authorization messages (login/su)
    syslog,   // 5  syslog daemon
    lpr,      // 6  line printer subsystem (creating jobs and send to spool for print by using lpd)
    news,     // 7  network news subsystem (USENET)
    uucp,     // 8  Unix-to-Unix Copy subsystem
    cron,     // 9  scheduling daemon
    authpriv, // 10 security/authorization private messages
    ftp,      // 11 FTP daemon
    ntp,      // 12 NTP daemon
    security, // 13 security log audit
    console,  // 14 console log alert
    clock,    // 15 clock subsystem
    local0,   // 16 local use
    local1,   // 17
    local2,   // 18
    local3,   // 19
    local4,   // 20
    local5,   // 21
    local6,   // 22
    local7    // 23
}
"@
```
### Syslog type message
```PowerShell
Add-Type -TypeDefinition @"
public enum Syslog_Severity {
    Emergency,     // 0 emerg
    Alert,         // 1 alert
    Critical,      // 2 crit
    Error,         // 3 err
    Warning,       // 4 warning
    Notice,        // 5 notice
    Informational, // 6 info
    Debug          // 7 debug
}
"@
```

# Pester

[Source](https://github.com/pester/Pester)

`Install-Module -Name Pester -Repository PSGallery -Force -AllowClobber` \
`Import-Module Pester` \
`$(Get-Module Pester -ListAvailable).Version`

`.Tests.ps1`
```PowerShell
function Add-Numbers {
    param (
        [int]$a,
        [int]$b
    )
    $a + $b
}
Describe "Add-Numbers" {
    Context "При сложении двух чисел" {
        It "Должна вернуться правильная сумма" {
            $result = Add-Numbers -a 3 -b 4
            $result | Should -Be 7
        }
    }
    Context "При сложении двух чисел" {
        It "Должна вернуться ошибка (5+0 -ne 4)" {
            $result = Add-Numbers -a 5 -b 0
            $result | Should -Be 4
        }
    }
}

function Get-RunningProcess {
    return Get-Process | Select-Object -ExpandProperty Name
}
Describe "Get-RunningProcess" {
    Context "При наличии запущенных процессов" {
        It "Должен возвращать список имен процессов" {
            $result = Get-RunningProcess
            $result | Should -Contain "svchost"
            $result | Should -Contain "explorer"
        }
    }
    Context "Когда нет запущенных процессов" {
        It "Должен возвращать пустой список" {
            # Замокать функцию Get-Process, чтобы она всегда возвращала пустой список процессов
            Mock Get-Process { return @() }
            $result = Get-RunningProcess
            $result | Should -BeEmpty
        }
    }
}
```

# oh-my-posh

[Install](https://ohmyposh.dev/docs/installation/windows)

`winget install JanDeDobbeleer.OhMyPosh -s winget` \
`choco install oh-my-posh -y` \
`scoop install https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest/download/oh-my-posh.json` \
`Set-ExecutionPolicy Bypass -Scope Process -Force; Invoke-Expression ((New-Object System.Net.WebClient).DownloadString('https://ohmyposh.dev/install.ps1'))`

[Themes](https://ohmyposh.dev/docs/themes)

`Get-PoshThemes` отобразить список всех тем \
`oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH/di4am0nd.omp.json" | Invoke-Expression` применить (использовать) тему в текущей сессии \
`oh-my-posh init pwsh --config "https://raw.githubusercontent.com/JanDeDobbeleer/oh-my-posh/main/themes/cert.omp.json" | Invoke-Expression` считать тему из репозитория \
`New-Item -Path $PROFILE -Type File -Force` создайт файл профилья PowerShell \
`'oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH/di4am0nd.omp.json" | Invoke-Expression' > $PROFILE` сохранить тему профиля (загружать тему при запуске PowerShell)

### themes-performance

`Install-Module themes-performance -Repository NuGet` установить модуль с темами \
`Set-PoshTheme -Theme System-Sensors` использовать тему с датчиками из LibreHardwareMonitor \
`Set-PoshTheme -Theme System-Sensors -Save` загрузить тему из репозитория на локальный компьютер и сохранить тему в профиле \
`Set-PoshTheme -Theme System-Performance` использовать тему с датчиками системы, получаемыми из системы WMI/CIM (заряд батареи ноутбука | загрузка CPU в % | использование оперативной памяти | скорость активного сетевого интерфейса) \
`Set-PoshTheme -Theme System-Performance -Save` \
`Set-PoshTheme -Theme Pwsh-Process-Performance` время работы текущего процесса pwsh (процессорное время), количество работающих/общее (статус успех/ошибка) фоновых заданий, Working Set текущего процесса и всех процессов PowerShell в системе \
`Set-PoshTheme -Theme Pwsh-Process-Performance -Save`

# Windows-Terminal 

### Terminal-Icons

`Install-Module -Name Terminal-Icons -Repository PSGallery` \
`scoop bucket add extras` \
`scoop install terminal-icons`

`notepad $PROFILE` \
`Import-Module -Name Terminal-Icons`

Использует шрифты, которые необходимо установить и настроить в параметрах профиля PowerShell: [Nerd Fonts](https://github.com/ryanoasis/nerd-fonts) \
[Список шрифтов](https://www.nerdfonts.com/font-downloads) \
Скачать и установить шрифт похожий на Cascadia Code - [CaskaydiaCove](https://github.com/ryanoasis/nerd-fonts/releases/download/v3.1.1/CascadiaCode.zip)

Установить шрифт в конфигурацию Windows Terminal для PowerShell Core:
```json
"profiles": 
{
    "defaults": 
    {
        "colorScheme": "One Half Dark",
        "experimental.retroTerminalEffect": true,
        "font": 
        {
            "size": 10.0
        },
        "useAtlasEngine": true
    },
    "list": 
    [
        // PowerShell Core
        {
            "font": 
            {
                "face": "CaskaydiaMono Nerd Font" // устанавливаем шрифт для работы Terminal-Icons
            },
            "guid": "{574e775e-4f2a-5b96-ac1e-a2962a402336}",
            "hidden": false,
            "name": "PowerShell Core",
            "source": "Windows.Terminal.PowershellCore"
        },
        // WSL (Ubuntu)
        {
            "guid": "{2c4de342-38b7-51cf-b940-2309a097f518}",
            "hidden": false,
            "name": "WSL",
            "source": "Windows.Terminal.Wsl"
        },
        // ssh
        {
            "commandline": "ssh lifailon@192.168.1.100 -p 22",
            "guid": "{a3ec86f6-2bc1-59dd-814d-2a0d935af5f8}",
            "icon": "🐧",
            "name": "devops-01"
        }
    ]
}
```
### Custom Actions

Custom actions: https://learn.microsoft.com/ru-ru/windows/terminal/customize-settings/actions \
Escape-последовательности: https://learn.microsoft.com/ru-ru/cpp/c-language/escape-sequences?view=msvc-170
```json
"actions": 
[
    {
        "command": 
        {
            "action": "copy",
            "singleLine": false
        },
        "keys": "ctrl+c"
    },
    {
        "command": "paste",
        // Сохраняем классическую вставку интерпритатора, не заставляя выполнять код построчно
        "keys": "ctrl+shift+v" // default: ctrl+v
    },
    {
        "command": "find",
        "keys": "ctrl+f" // default: ctrl+shift+f
    },
    {
        "command": 
        {
            "action": "splitPane",
            "split": "left", // default: auto
            "splitMode": "duplicate"
        },
        "keys": "ctrl+shift+d" // default: alt+shift+d
    },
    // Очистить строку
    {
        "command": {
            "action": "sendInput",
            "input": "\u0001\u001b[3~"
        },
        "keys": "ctrl+k"
    },
    // Очистить терминал
    {
        "command": {
            "action": "sendInput",
            "input": "\u0001\u001b[3~clear\r"
        },
        "keys": "ctrl+l"
    },
    // Вставить шаблон модуля для перевода текста через Google Translate
    {
        "command": {
            "action": "sendInput",
            "input": "\u0001\u001b[3~Get-Translate -Alternatives -Provider Google ''\u001b[D"
        },
        "keys": "ctrl+g"
    },
    // Вставить шаблон модуля для перевода текста через MyMemory
    {
        "command": {
            "action": "sendInput",
            "input": "\u0001\u001b[3~Get-Translate -Alternatives -Provider MyMemory ''\u001b[D"
        },
        "keys": "ctrl+q"
    },
    // Быстрый перевод текста из буфера обмена
    {
        "command": {
            "action": "sendInput",
            "input": "\u0001\u001b[3~Get-Translate -Alternatives -Provider MyMemory -Text $(Get-Clipboard)\u001b[D\r"
        },
        "keys": "ctrl+shift+q"
    },
    // Быстрый пинг dns google
    {
        "command": {
            "action": "sendInput",
            "input": "\u0001\u001b[3~ping 8.8.8.8 -t\r"
        },
        "keys": "ctrl+p"
    }
]
```
# Pandoc
```PowerShell
$release_latest = Invoke-RestMethod "https://api.github.com/repos/jgm/pandoc/releases/latest"
$url = $($release_latest.assets | Where-Object name -match "windows-x86_64.zip").browser_download_url
Invoke-RestMethod $url -OutFile $home\Downloads\pandoc.zip
Expand-Archive -Path "$home\Downloads\pandoc.zip" -DestinationPath "$home\Downloads\"
$path = $(Get-ChildItem "$home\Downloads\pandoc-*\*.exe").FullName
Copy-Item -Path $path -Destination "C:\Windows\System32\pandoc.exe"
Remove-Item "$home\Downloads\pandoc*" -Force -Recurse
```
`pandoc -s README.md -o index.html` конвертация из Markdown в HTML \
`pandoc README.md -o index.html --css=styles.css` применить стили из css \
`pandoc -s index.html -o README.md` конвертация из HTML в Markdown \
`pandoc -s README.md -o README.docx` конвертация в Word \
`pandoc -s README.md -o README.epub` конвертация в открытый формат электронных версий книг \
`pandoc -s README.md -o README.pdf` конвертация в PDF (требуется rsvg-convert) \
`pandoc input.md -f markdown+hard_line_breaks -o output.md` конвертация из markdown документа, который не содержит обратный слэш в конце каждой строки для переноса (\), который их добавит

### Convert Excel to Markdown
```PowerShell
Import-Module ImportExcel
Import-Excel -Path srv.xlsx | Export-Csv -Path $csvFilePath -NoTypeInformation -Encoding UTF8 # конвертация Excel в csv
pandoc -s -f csv -t markdown input.csv -o output.md # конвертация таблицу csv в markdown
```
# FFmpeg
```PowerShell
$release_latest = Invoke-RestMethod "https://api.github.com/repos/BtbN/FFmpeg-Builds/releases/latest"
$url = $($release_latest.assets | Where-Object name -match "ffmpeg-master-latest-win64-gpl.zip").browser_download_url
Invoke-RestMethod $url -OutFile $home\Downloads\ffmpeg-master-latest-win64-gpl.zip
Expand-Archive -Path "$home\Downloads\ffmpeg-master-latest-win64-gpl.zip" -DestinationPath "$home\Downloads\"
Copy-Item -Path "$home\Downloads\ffmpeg-master-latest-win64-gpl\bin\ffmpeg.exe" -Destination "C:\Windows\System32\ffmpeg.exe"
Remove-Item "$home\Downloads\ffmpeg-*" -Force -Recurse
```
`ffmpeg -i input.mp4 output.gif` конвертировать mp4 в gif \
`ffmpeg -i input.mp4 -filter_complex "scale=1440:-1:flags=lanczos" output.gif` изменить разрешение на выходе \
`ffmpeg -i input.mp4 -filter_complex "scale=1440:-1:flags=lanczos" -r 10 output.gif` изменить количество кадров в секунду на выходе \
`ffmpeg -i input.mp4 -filter_complex "fps=5,scale=960:-1:flags=lanczos,split[s0][s1];[s0]palettegen=max_colors=32[p];[s1][p]paletteuse=dither=bayer" output.gif` сжатие за счет цветовой политры \
`ffmpeg -i input.mp4 -ss 00:00:10 -frames:v 1 -q:v 1 output.jpg` вытащить скриншот из видео на 10 секунде \
`ffmpeg -i input.mp4 -ss 00:00:05 -to 00:00:10 -c copy output.mp4` вытащить кусок видео \
`ffmpeg -i "%d.jpeg" -framerate 2 -c:v libx264 -r 30 -pix_fmt yuv420p output.mp4` создать видео из фото (1.jpeg, 2.jpeg и т.д.) с framerate (частотой кадров) в создаваемом видео 2 кадра в секунду \
`ffmpeg -i "rtsp://admin:password@192.168.3.201:554" -rtsp_transport tcp -c:v copy -c:a aac -strict experimental output.mp4` запись без перекодирования (copy) RTSP-потока с камеры видеонаблюдения (+ аудио в кодеке AAC) в файл \
`ffmpeg -i "rtsp://admin:password@192.168.3.201:554" -rtsp_transport tcp -c:v copy -c:a aac -strict experimental -movflags +faststart+frag_keyframe+empty_moov output.mp4` переместить метаданные в начало файла, что позволяет начать воспроизведение файла в видеоплеере до его полной загрузки \
`ffmpeg -i "rtsp://admin:password@192.168.3.201:554" -rtsp_transport tcp -frames:v 1 -c:v mjpeg output.jpg` сделать скриншот \
`ffmpeg -i input.mp4 -vf "pad=width=iw:height=ih+100:x=0:y=100:color=black" -c:a copy output.mp4` width=iw: (ширина видео остается как у исходного файла), height=ih+100 (высота видео увеличивается на 100 пикселей), x=0 (горизонтальное смещение установлено в 0), y=100 (вертикальное смещение установлено в 100 пикселей вниз, чтобы добавить черное пространство сверху), color=black (цвет добавленного пространства — черный)

# HandBrake
```PowerShell
$url = "https://github.com/HandBrake/HandBrake/releases/download/1.8.0/HandBrakeCLI-1.8.0-win-x86_64.zip"
Invoke-RestMethod $url -OutFile $home\Downloads\HandBrakeCLI.zip
Expand-Archive -Path $home\Downloads\HandBrakeCLI.zip -OutputPath "$home\Downloads\"
Copy-Item -Path "$home\Downloads\HandBrakeCLI.exe" -Destination "C:\Windows\System32\HandBrakeCLI.exe"
Remove-Item "$home\Downloads\doc" -Force -Recurse
Remove-Item "$home\Downloads\HandBrakeCLI*"
```
`HandBrakeCLI -i input.mp4 -o output.mkv` конвертирует видео в формате mp4 в формат mkv с использованием стандартных настроек HandBrake \
`HandBrakeCLI -i input.mp4 -o output.mkv -q 20` установить качество видео 20, значения варьируются от 0 (максимальное качество) до 51 (минимальное качество), где 20 считается хорошим качеством для большинства видео \
`HandBrakeCLI -i input.mp4 -o output.mkv -r 30` установить частоту кадров на 30 fps \
`HandBrakeCLI -i input.mp4 -o output.mkv --maxWidth 1280 --maxHeight 720` изменить размер на 1280х720 \
`HandBrakeCLI -i input.mp4 -o output.mkv -b 1500` установить битрейт видео 1500 кбит/с \
`HandBrakeCLI -i input.mp4 -o output.mkv -e x264` преобразовать видео с использованием кодека x264 \
`HandBrakeCLI -i input.mp4 -o output.mp4 --crop 0:200:0:0` обрезать видео снизу на 200px (верх:низ:лево:право) \
`HandBrakeCLI -i input.mp4 -o output.mp4 --start-at duration:5 --stop-at duration:15` обрезать видео (на выходе будет 15-секундное видео с 5 по 20 секунду)

# ImageMagick

Source: [ImageMagick](https://sourceforge.net/projects/imagemagick)

`magick identify -verbose PowerShell-Commands.png` извлечь метаданные изображения \
`magick PowerShell-Commands.png output.jpg` конвертация формата изображения \
`magick PowerShell-Commands.png -resize 800x600 output.jpg` изменить размер (увеличить или уменьшить) \
`magick PowerShell-Commands.png -crop 400x300+100+50 output.jpg` обрезать \
`magick PowerShell-Commands.png -rotate 90 output.jpg` повернуть изображение \
`magick PowerShell-Commands.png -fill white -pointsize 24 -gravity center -annotate +0+0 "PowerShell" output.jpg` наложить текст на изображение \
`magick PowerShell-Commands.png -brightness-contrast +20x+10 output.jpg` изменить яркость и контрастность \
`magick convert -delay 100 1.png 2.png 3.png output.gif` создать gif из изображений \
`magick convert image1.jpg image2.jpg -append output.jpg` вертикально объединенить изображения

# YouTube
```PowerShell
$release_latest = Invoke-RestMethod "https://api.github.com/repos/yt-dlp/yt-dlp/releases/latest"
$url = $($release_latest.assets | Where-Object name -match "yt-dlp.exe").browser_download_url
Invoke-RestMethod $url -OutFile "C:\Windows\System32\yt-dlp.exe"
```
`yt-dlp -F https://www.youtube.com/watch?v=gxplizjhqiw` отобразить список всех доступных форматов \
`yt-dlp -J https://www.youtube.com/watch?v=gxplizjhqiw` вывести данные в формате JSON \
`yt-dlp -J https://www.youtube.com/watch?v=gxplizjhqiw | jq -r .formats.[].format` id - resolution (format_note) \
`yt-dlp -f 137 https://www.youtube.com/watch?v=gxplizjhqiw` загрузить только видео в указанном формате по id \
`yt-dlp -f bestaudio https://www.youtube.com/watch?v=gxplizjhqiw` загрузить только аудио \
`yt-dlp -f best https://www.youtube.com/watch?v=gxplizjhqiw` загрузить видео с аудио в лучшем качестве \
`yt-dlp -f 'bestvideo[height<=1080]+bestaudio/best[height<=1080]' https://www.youtube.com/watch?v=gxplizjhqiw` загрузить в указанном качестве \
`yt-dlp -r 2m https://www.youtube.com/watch?v=gxplizjhqiw` ограничить скорость загрузки до 2 МБит/с
```PowerShell
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