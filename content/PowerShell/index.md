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

# Network

### ping

`Test-Connection -Count 1 $srv1, $srv2` отправить icmp-пакет двум хостам \
`Test-Connection $srv -ErrorAction SilentlyContinue` не выводить ошибок, если хост не отвечает \
`Test-Connection -Source $srv1 -ComputerName $srv2` пинг с удаленного компьютера
```PowerShell
function Test-PingNetwork {
param (
    [Parameter(Mandatory,ValueFromPipeline)][string[]]$Network,
    [ValidateRange(100,10000)][int]$Timeout = 100
)
$ping = New-Object System.Net.NetworkInformation.Ping
$Network  = $Network -replace "0$"
$net = @()
foreach ($r in @(1..254)) {
    $net += "$network$r"
}
foreach ($n in $net) {
    $ping.Send($n, $timeout) | select @{Name="Address"; Expression={$n -replace ".+\."}}, Status
}
}
```
`Test-PingNetwork -Network 192.168.3.0` \
`Test-PingNetwork -Network 192.168.3.0 -Timeout 1000`

`Get-CimInstance -Class Win32_PingStatus -Filter "Address='127.0.0.1'"` \
`Get-CimInstance -Class Win32_PingStatus -Filter "Address='127.0.0.1'" | Format-Table -Property Address,ResponseTime,StatusCode -Autosize` 0 - успех \
`'127.0.0.1','8.8.8.8' | ForEach-Object -Process {Get-CimInstance -Class Win32_PingStatus -Filter ("Address='$_'") | Select-Object -Property Address,ResponseTime,StatusCode}` \
`$ips = 1..254 | ForEach-Object -Process {'192.168.1.' + $_}` сформировать массив из ip-адресов подсети

### dhcp

`Get-CimInstance -Class Win32_NetworkAdapterConfiguration -Filter "DHCPEnabled=$true"` отобразить адаптеры с включенным DHCP \
`$wql = 'SELECT * from Win32_NetworkAdapterConfiguration WHERE IPEnabled=True and DHCPEnabled=False'` \
`Invoke-CimMethod -MethodName ReleaseDHCPLease -Query $wql` включение DHCP на всех адаптерах \
`Invoke-CimMethod -ClassName Win32_NetworkAdapterConfiguration -MethodName ReleaseDHCPLeaseAll` отменить аренду адресов DHCP на всех адаптерах \
`Invoke-CimMethod -ClassName Win32_NetworkAdapterConfiguration -MethodName RenewDHCPLeaseAll` обновить аренду адресов DHCP на всех адаптерах

### port

`tnc $srv -p 5985` \
`tnc $srv -CommonTCPPort WINRM` HTTP,RDP,SMB \
`tnc ya.ru –TraceRoute -Hops 2` TTL=2 \
`tnc ya.ru -DiagnoseRouting` маршрутизация до хоста, куда (DestinationPrefix: 0.0.0.0/0) через (NextHop: 192.168.1.254)

### netstat

`netstat -anop tcp` -n/-f/-b \
`Get-NetTCPConnection -State Established,Listen | ? LocalPort -Match 3389` \
`Get-NetTCPConnection -State Established,Listen | ? RemotePort -Match 22` \
`Get-NetUDPEndpoint | ? LocalPort -Match 514` netstat -ap udp`

### nslookup

`nslookup ya.ru 1.1.1.1` с указанием DNS сервера \
`nslookup -type=any ya.ru` указать тип записи \
`Resolve-DnsName ya.ru -Type MX` ALL,ANY,A,NS,SRV,CNAME,PTR,TXT(spf) \
`[System.Net.Dns]::GetHostEntry("ya.ru")`

### ipconfig

`Get-NetIPConfiguration` \
`Get-NetIPConfiguration -InterfaceIndex 14 -Detailed`

### Adapter

`Get-NetAdapter` \
`Set-NetIPInterface -InterfaceIndex 14 -Dhcp Disabled` отключить DHCP \
`Get-NetAdapter -InterfaceIndex 14 | New-NetIPAddress –IPAddress 192.168.3.99 -DefaultGateway 192.168.3.1 -PrefixLength 24` задать/добавить статический IP-адрес \
`Set-NetIPAddress -InterfaceIndex 14 -IPAddress 192.168.3.98` изменить IP-адреас на адаптере \
`Remove-NetIPAddress -InterfaceIndex 14 -IPAddress 192.168.3.99` удалить IP-адрес на адаптере \
`Set-NetIPInterface -InterfaceIndex 14 -Dhcp Enabled` включить DHCP

### DNSClient

`Get-DNSClientServerAddress` список интерфейсов и настроенные на них адреса DNS сервера \
`Set-DNSClientServerAddress -InterfaceIndex 14 -ServerAddresses 8.8.8.8` изменить адрес DNS сервера на указанного интерфейсе

### DNSCache

`Get-DnsClientCache` отобразить кэшированные записи клиента DNS \
`Clear-DnsClientCache` очистить кэш

### Binding

`Get-NetAdapterBinding -Name Ethernet -IncludeHidden -AllBindings` \
`Get-NetAdapterBinding -Name "Беспроводная сеть" -DisplayName "IP версии 6 (TCP/IPv6)" | Set-NetAdapterBinding -Enabled $false` отключить IPv6 на адаптере

### TCPSetting

`Get-NetTCPSetting` \
`Set-NetTCPSetting -SettingName DatacenterCustom,Datacenter -CongestionProvider DCTCP` настраивает провайдера управления перегрузкой (Congestion Control Provider) на DCTCP (Data Center TCP) для профилей TCP с именами DatacenterCustom и Datacenter \
`Set-NetTCPSetting -SettingName DatacenterCustom,Datacenter -CwndRestart True` включает функцию перезапуска окна перегрузки (Congestion Window Restart, CwndRestart) для указанных профилей TCP. Это означает, что после периода идле (когда нет передачи данных) TCP окно перегрузки будет сбрасываться \
`Set-NetTCPSetting -SettingName DatacenterCustom,Datacenter -ForceWS Disabled` отключает принудительное масштабирование окна (Forced Window Scaling) для указанных профилей TCP. Масштабирование окна — это механизм, который позволяет увеличивать размер окна перегрузки TCP, чтобы улучшить производительность передачи данных по сети с высокой пропускной способностью и большой задержкой

### hostname

`$env:computername` \
`hostname.exe` \
`(Get-CIMInstance CIM_ComputerSystem).Name` \
`(New-Object -ComObject WScript.Network).ComputerName` \
`[System.Environment]::MachineName` \
`[System.Net.Dns]::GetHostName()`

### arp

`ipconfig /all | Select-String "физ"` grep \
`Get-NetNeighbor -AddressFamily IPv4`
```PowerShell
function Get-ARP {
    Param (
        $proxy,
        $search
    )
    if (!$proxy) {
        $arp = arp -a
    }
    if ($proxy) {
        $arp = icm $proxy { arp -a }
    }
    $mac = $arp[3..260]
    $mac = $mac -replace "^\s\s"
    $mac = $mac -replace "\s{1,50}", " "
    $mac_coll = New-Object System.Collections.Generic.List[System.Object]
    foreach ($m in $mac) {
        $smac = $m -split " "
        $mac_coll.Add([PSCustomObject]@{
                IP   = $smac[0];
                MAC  = $smac[1];
                Type = $smac[2]
            })
    }
    if ($search) {
        if ($search -NotMatch "\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}") {
            #$ns = nslookup $search
            #$ns = $ns[-2]
            #$global:ns = $ns -replace "Address:\s{1,10}"
            $rdns = Resolve-DnsName $search -ErrorAction Ignore
            $ns = $rdns.IPAddress
            if ($ns -eq $null) {
                return
            }
        }
        else {
            $ns = $search
        }
        $mac_coll = $mac_coll | ? ip -Match $ns
    }
    $mac_coll
}
```
`Get-ARP -search 192.168.3.100` \
`Get-ARP -search 192.168.3.100 -proxy dc-01`

### Network Adapter Statistics

`netstat -se` \
`Get-NetAdapterStatistics`

### SpeedTest
```PowerShell
function Get-SpeedTestOokla {
    param (
        $Server = 3682,
        [switch]$List
    )
    $path = "$env:TEMP\speedtest.exe"
    $testPath = Test-Path "$env:TEMP\speedtest.exe"
    if ($testPath -eq $false) {
        Invoke-RestMethod https://install.speedtest.net/app/cli/ookla-speedtest-1.2.0-win64.zip -OutFile "$env:TEMP\speedtest.zip"
        Expand-Archive "$env:TEMP\speedtest.zip" -DestinationPath $env:TEMP
    }
    if ($List) {
        $(& $path -L -f json | ConvertFrom-Json).servers
    } else {
        $test = & $path -s $Server -f json
        $Collections = New-Object System.Collections.Generic.List[System.Object]
        $Collections.Add(
            [PSCustomObject]@{
                Date = $($test | ConvertFrom-Json).timestamp
                Url = $($test | ConvertFrom-Json).result.url
                Download = [double]::Round($($($test | ConvertFrom-Json).download.bandwidth / 1mb * 8), 2)
                Upload = [double]::Round($($($test | ConvertFrom-Json).upload.bandwidth / 1mb * 8), 2)
                Ping = $($test | ConvertFrom-Json).ping.latency
                Internal_IP = $($test | ConvertFrom-Json).interface.internalIp
                External_IP = $($test | ConvertFrom-Json).interface.externalIp
                Server = $($test | ConvertFrom-Json).server
            }
        )
        $Collections
    }
}
```
# iPerf

### Install
```PowerShell
$url = $($(Invoke-RestMethod https://api.github.com/repos/ar51an/iperf3-win-builds/releases/latest).assets | Where-Object name -match "win64.zip").browser_download_url
Invoke-RestMethod $url -OutFile $home\Downloads\iperf.zip
New-Item "$home\Documents\iperf3" -Type Directory | Out-Null
Expand-Archive -Path "$home\Downloads\iperf.zip" -OutputPath "$home\Documents\iperf3"
Remove-Item "$home\Downloads\iperf*" -Force -Recurse
```
`& "$home\Documents\iperf3\iperf3.exe" -h`

### Env-Update-Exec-Path
```PowerShell
$EnvPath = [Environment]::GetEnvironmentVariable("Path", [EnvironmentVariableTarget]::Machine)
$EnvPath -split ";"
$iperfPath = "$home\Documents\iperf3\"
$EnvAddPath = $EnvPath + ";" + $iperfPath
[Environment]::SetEnvironmentVariable("Path", $EnvAddPath, [EnvironmentVariableTarget]::Machine)
$([Environment]::GetEnvironmentVariable("Path", [EnvironmentVariableTarget]::Machine)) -split ";"
```
`iperf3 -h`

### iPerf-GUI

`Invoke-RestMethod "https://github.com/Lifailon/iPerf-GUI/raw/rsa/iPerf-GUI-Install.exe" -OutFile "$home\Downloads\iPerf-GUI-Install.exe"` скачать установочную версию собранную с помощью WinRAR \
`Start-Process -FilePath "$home\Downloads\iPerf-GUI-Install.exe" -ArgumentList "/S" -NoNewWindow -Wait` установить в тихом режиме

### iPerf-Docker
```PowerShell
echo '
FROM alpine:latest
RUN apk update && apk add --no-cache iperf3
ENV PORT=5201
EXPOSE $PORT
CMD ["sh", "-c", "iperf3 -s -p $PORT"]
' > Dockerfile
```
`docker build -t iperf3-alpine-server .` \
`docker run -d -p 5201:5201 --name iperf3-alpine-server iperf3-alpine-server`

### Server

`iperf3 -s` запуск сервера \
`iperf3 -s -D` запустить сервер в фоновом режиме как службу (--daemon) \
`Get-NetTCPConnection -State Established,Listen | ? LocalPort -Match 5201` проверить, что порт сервера слушает \
`Get-Process -Id $(Get-NetTCPConnection -State Established,Listen | ? LocalPort -Match 5201).OwningProcess` получить процесс по порту \
`Get-Process iperf3 | Stop-Process` остановить процесс \
`iperf3 -s -D --logfile "$home\Documents\iperf3\iperf3.log"` перенаправить вывод в лог файл \
`iperf3 -s -p 5211` указать порт, на котором будет слушать сервер или отправлять запросы клиент \
`iperf3 -s -p 5211 -f M` изменить формат выводимых данных (измерять в байтах а не в битах, доступные значения: K,M,G,T) \
`iperf3 -s -p 5211 -f M -J` вывод в формате json \
`iperf3 -s -p 5211 -f M -V` вывод подробной информации

### Client

`iperf3 -c 192.168.3.100 -p 5211` подключение к серверу (по умолчанию проверяется отдача на сервер с клиента) \
`iperf3 -c 192.168.3.100 -p 5211 -R` обратный тест, проверка скачивания с сервера (--reverse, сервер отправляет данные клиенту) \
`iperf3 -c 192.168.3.100 -p 5211 -R -P 2` количество одновременных потоков ([SUM] - суммарная скорость нескольки потоков) \
`iperf3 -c 192.168.3.100 -p 5211 -R -4` использовать только IPv4 \
`iperf3 -c 192.168.3.100 -p 5211 -R -u` использовать UDP вместо TCP \
`iperf3 -c 192.168.3.100 -p 5211 -R -u -b 2mb` установить битрейт в 2.00 Mbits/sec для UDP (по умолчанию 1 Мбит/сек, для TCP не ограничено) \
`iperf3 -c 192.168.3.100 -p 5211 -R -t 30` время одного теста в секундах (по умолчанию 10 секунд) \
`iperf3 -c 192.168.3.100 -p 5211 -R -n 1gb` указать объем данных для проверки (применяется вместо времени -t) \
`iperf3 -c 192.168.3.100 -p 5211 -R --get-server-output` вывести вывод сервера на клиенте

### Output

`sender` upload (скорость передачи на удаленный сервер) \
`receiver` download (скорость скачивания с удаленного сервера) \
`Interval` общее время сканирования \
`Transfer` кол-во переданных и полученных МБайт \
`Bandwidth` скорость передачи (измеряется в Мбит/c)

### PS-iPerf

`Install-Module ps-iperf -Repository NuGet` \
`Import-Module PS-iPerf` \
`Start-iPerfServer -Port 5211` запустить сервер \
`Get-iPerfServer` статус работы сервера \
`Stop-iPerfServer` остановить сервер \
`Connect-iPerfServer -Server 192.168.3.100 -Port 5211 -MBytes 500 -Download` подключиться к серверу и скачать 500 МБайт \
`$SpeedTest = Connect-iPerfServer -Server 192.168.3.100 -Port 5211 -MBytes 500 -LogWrite` передать 500 МБайт на сервер (вести запись в лог-файл) \
`$SpeedTest.Intervals` метрики измерений \
`Get-iPerfLog` прочитать лог-файл

# RDP

`Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"` отобразить номер текущего RDP порта \
`Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value "3390"` изменить RDP-порт \
`$(Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\" -Name "fDenyTSConnections").fDenyTSConnections` если 0, то включен \
`Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\" -Name "fDenyTSConnections" -Value 0` включить RDP \
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f` \
`(gcim -Class Win32_TerminalServiceSetting -Namespace root\CIMV2\TerminalServices).SetAllowTSConnections(0)` включить RDP (для Windows Server) \
`Get-Service TermService | Restart-Service -Force` перезапустить rdp-службу \
`New-NetFirewallRule -Profile Any -DisplayName "RDP 3390" -Direction Inbound -Protocol TCP -LocalPort 3390` открыть RDP-порт

### IPBan

`auditpol /get /category:*` отобразить все политики аудита \
`auditpol /get /category:Вход/выход` отобразить локальные политики аудита для Входа и Выхода из системы \
`auditpol /set /subcategory:"Вход в систему" /success:enable /failure:enable` включить локальные политики - Аудит входа в систему \
`auditpol /set /subcategory:"Выход из системы" /success:enable /failure:enable`

`$url = $($(Invoke-RestMethod https://api.github.com/repos/DigitalRuby/IPBan/releases/latest).assets | Where-Object name -match ".+win.+x64.+").browser_download_url` получить ссылку для загрузки последней версии \
`$version = $(Invoke-RestMethod https://api.github.com/repos/DigitalRuby/IPBan/releases/latest).tag_name` получить номер последней версии \
`$path = "$home\Documents\ipban-$version"` путь для установки \
`Invoke-RestMethod $url -OutFile "$home\Downloads\IPBan-$version.zip"` скачать дистрибутив \
`Expand-Archive "$home\Downloads\ipban-$version.zip" -DestinationPath $path` разархивировать в путь для установки \
`Remove-Item "$home\Downloads\ipban-$version.zip"` удалить дистрибутив \
`sc create IPBan type=own start=delayed-auto binPath="$path\DigitalRuby.IPBan.exe" DisplayName=IPBan` создать службу \
`Get-Service IPBan` статус службы \
`$conf = $(Get-Content "$path\ipban.config")` читаем конфигурацию \
`$conf = $conf -replace '<add key="Whitelist" value=""/>','<add key="Whitelist" value="192.168.3.0/24"/>'` добавить в белый лист домашнюю сеть для исключения \
`$conf = $conf -replace '<add key="ProcessInternalIPAddresses" value="false"/>','<add key="ProcessInternalIPAddresses" value="true"/>'` включить обработку локальных (внутренних) ip-адресов \
`$conf = $conf -replace '<add key="FailedLoginAttemptsBeforeBanUserNameWhitelist" value="20"/>','<add key="FailedLoginAttemptsBeforeBanUserNameWhitelist" value="5"/>'` указать количество попыток подключения до блокировки \
`$conf = $conf -replace '<add key="ExpireTime" value="01:00:00:00"/>','<add key="ExpireTime" value="00:01:00:00"/>'` задать время блокировки 1 час \
`$conf > "$path\ipban.config"` обновить конфигурацию \
`Get-Service IPBan | Start-Service` запустить службу
```
Get-NetFirewallRule | Where-Object DisplayName -Match "IPBan" | ForEach-Object {
    $Name = $_.DisplayName
    Get-NetFirewallAddressFilter -AssociatedNetFirewallRule $_ | Select-Object @{Name="Name"; Expression={$Name}},LocalIP,RemoteIP
} # отобразить область применения правил Брандмауэра для IPBan
```
`Get-Content -Wait "$path\logfile.txt"` читать лог \
`Get-Service IPBan | Stop-Service` остановить службу \
`sc delete IPBan` удалить службу

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
# SMTP
```PowerShell
function Send-SMTP {
param (
    [Parameter(Mandatory = $True)]$mess
)
    $srv_smtp = "smtp.yandex.ru" 
    $port = "587"
    $from = "login1@yandex.ru" 
    $to = "login2@yandex.ru" 
    $user = "login1"
    $pass = "password"
    $subject = "Service status on Host: $hostname"
    $Message = New-Object System.Net.Mail.MailMessage
    $Message.From = $from
    $Message.To.Add($to) 
    $Message.Subject = $subject 
    $Message.IsBodyHTML = $true 
    $Message.Body = "<h1> $mess </h1>"
    $smtp = New-Object Net.Mail.SmtpClient($srv_smtp, $port)
    $smtp.EnableSSL = $true 
    $smtp.Credentials = New-Object System.Net.NetworkCredential($user, $pass);
    $smtp.Send($Message) 
}
```
`Send-SMTP $(Get-Service)`

# REST API

`$url = "https://habr.com/ru/rss/users/Lifailon/publications/articles/?fl=ru"` RSS лента публикаций на Habr \
`Invoke-RestMethod $url` \
`$iwr = Invoke-WebRequest -Uri $url` \
`$iwr | Get-Member` \
`$iwr.Content` \
`$iwr.StatusCode -eq 200` \
`$iwr.Headers` \
`$iwr.ParsedHtml | Select lastModified` \
`$iwr.Links | fl title,innerText,href` \
`$iwr.Images.src`

### Methods

**GET** - Read \
**POST** - Create \
**PATCH** - Partial update/modify \
**PUT** - Update/replace \
**DELETE** - Remove

### Download Image
```PowerShell
function Download-Image {
param (
    [Parameter(Mandatory = $True)]$url
)
    $folder = $url -replace "http.+://" -replace "/","-" -replace "-$"
    $path = "$home\Pictures\$folder"
    if (Test-Path $path) {
        Remove-Item $path -Recurse -Force
        New-Item -ItemType Directory $path > $null
    } else {
        New-Item -ItemType Directory $path > $null
    }
    $irm = Invoke-WebRequest -Uri $url
    foreach ($img in $irm.Images.src) {
        $name = $img -replace ".+/"
        Start-Job {
            Invoke-WebRequest $using:img -OutFile "$using:path\$using:name"
        } > $null
    }
    while ($True){
        $status_job = (Get-Job).State[-1]
        if ($status_job -like "Completed"){
        Get-Job | Remove-Job -Force
        break
    }}
    $count_all = $irm.Images.src.Count
    $count_down = (Get-Item $path\*).count
    "Downloaded $count_down of $count_all files to $path"
}
```
`Download-Image -url https://losst.pro/`

### Token
```PowerShell
https://veeam-11:9419/swagger/ui/index.html
$Header = @{
    "x-api-version" = "1.0-rev2"
}
$Body = @{
    "grant_type" = "password"
    "username" = "$login"
    "password" = "$password"
}
$vpost = iwr "https://veeam-11:9419/api/oauth2/token" -Method POST -Headers $Header -Body $Body -SkipCertificateCheck
$vtoken = (($vpost.Content) -split '"')[3]
```
### GET
```PowerShell
$token = $vtoken | ConvertTo-SecureString -AsPlainText –Force
$vjob = iwr "https://veeam-11:9419/api/v1/jobs" -Method GET -Headers $Header -Authentication Bearer -Token $token -SkipCertificateCheck

$Header = @{
    "x-api-version" = "1.0-rev1"
    "Authorization" = "Bearer $vtoken"
}
$vjob = iwr "https://veeam-11:9419/api/v1/jobs" -Method GET -Headers $Header -SkipCertificateCheck
$vjob = $vjob.Content | ConvertFrom-Json

$vjob = Invoke-RestMethod "https://veeam-11:9419/api/v1/jobs" -Method GET -Headers $Header -SkipCertificateCheck
$vjob.data.virtualMachines.includes.inventoryObject
```
### Cookie

Получить hash торрент файла на сайте Кинозал
```PowerShell
function Get-KinozalTorrentHash {
    param (
        [Parameter(Mandatory = $True)][string]$id,
        [Parameter(Mandatory = $True)][string]$cookies
    )
    $url = "https://kinozal.tv/get_srv_details.php?id=$($id)&action=2"
    $cookies = "uid=...+"
    $headers = @{
        "User-Agent" = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36"
        "Cookie" = $cookies
    }
    $result = Invoke-RestMethod -Uri $url -Headers $headers -Method Get
    $result -match "Инфо хеш: (.+)</li><li>Размер" | Out-Null
    return $Matches[1]
}
```
`$id = 1656552` \
`$cookies = "uid=..."` получить cookie в браузере на вкладке сеть из загловка запросов после авторизации на сайте \
`Get-KinozalTorrentHash $id $cookies`

# Pode
```PowerShell
Start-PodeServer {
    Add-PodeEndpoint -Address localhost -Port "8080" -Protocol "HTTP"
    ### Get info endpoints
    Add-PodeRoute -Path "/" -Method "GET" -ScriptBlock {
        Write-PodeJsonResponse -Value @{
        "service"="/api/service";
        "process"="/api/process"
        }
    }
    ### GET
    Add-PodeRoute -Path "/api/service" -Method "GET" -ScriptBlock {
        Write-PodeJsonResponse -Value $(
            Get-Service | Select-Object Name,@{
                Name="Status"; Expression={[string]$_.Status}
            },@{
                Name="StartType"; Expression={[string]$_.StartType}
            } | ConvertTo-Json
        )
    }
    Add-PodeRoute -Path "/api/process" -Method "GET" -ScriptBlock {
        Write-PodeJsonResponse -Value $(
            Get-Process | Sort-Object -Descending CPU | Select-Object -First 15 ProcessName,
            @{Name="ProcessorTime"; Expression={$_.TotalProcessorTime -replace "\.\d+$"}},
            @{Name="Memory"; Expression={[string]([int]($_.WS / 1024kb))+"MB"}},
            @{Label="RunTime"; Expression={((Get-Date) - $_.StartTime) -replace "\.\d+$"}}
        )
    }
    Add-PodeRoute -Path "/api/process-html" -Method "GET" -ScriptBlock {
        Write-PodeHtmlResponse -Value (
            Get-Process | Sort-Object -Descending CPU | Select-Object -First 15 ProcessName,
            @{Name="ProcessorTime"; Expression={$_.TotalProcessorTime -replace "\.\d+$"}},
            @{Name="Memory"; Expression={[string]([int]($_.WS / 1024kb))+"MB"}},
            @{Label="RunTime"; Expression={((Get-Date) - $_.StartTime) -replace "\.\d+$"}} # Auto ConvertTo-Html
        )
    }
    ### POST
    Add-PodeRoute -Path "/api/service" -Method "POST" -ScriptBlock {
        # https://pode.readthedocs.io/en/latest/Tutorials/WebEvent/
        # $WebEvent | Out-Default
        $Value = $WebEvent.Data["ServiceName"]
        $Status = (Get-Service -Name $Value).Status
        Write-PodeJsonResponse -Value @{
            "Name"="$Value";
            "Status"="$Status";
        }
    }
}
```
`irm http://localhost:8080/api/service -Method Get` \
`irm http://localhost:8080/api/process -Method Get` \
`http://localhost:8080/api/process-html` использовать браузер \
`irm http://localhost:8080/api/service -Method Post -Body @{"ServiceName" = "AnyDesk"}`

# Selenium

`Invoke-Expression(New-Object Net.WebClient).DownloadString("https://raw.githubusercontent.com/Lifailon/Deploy-Selenium/rsa/Deploy-Selenium-Drivers.ps1")` установка всех драйверов и Chromium соответствующий версии для драйвера
```
$path = "$home\Documents\Selenium\"
$log = "$path\ChromeDriver.log"
$ChromeDriver = "$path\ChromeDriver.exe"
$WebDriver = "$path\WebDriver.dll"
$SupportDriver = "$path\WebDriver.Support.dll"
$Chromium = (Get-ChildItem $path -Recurse | Where-Object Name -like chrome.exe).FullName
Add-Type -Path $WebDriver
Add-Type -Path $SupportDriver
try {
    $ChromeOptions = New-Object OpenQA.Selenium.Chrome.ChromeOptions # создаем объект с настройками запуска браузера
    $ChromeOptions.BinaryLocation = $Chromium # передаем путь до исполняемого файла, который отвечает за запуск браузера
    $ChromeOptions.AddArgument("start-maximized") # добавляем аргумент, который позволяет запустить браузер на весь экран
    #$ChromeOptions.AddArgument("start-minimized") # запускаем браузер в окне
    #$ChromeOptions.AddArgument("window-size=400,800") # запускаем браузер с заданными размерам окна в пикселях
    $ChromeOptions.AcceptInsecureCertificates = $True # игнорировать предупреждение на сайтах с не валидным сертификатом
    #$ChromeOptions.AddArgument("headless") # скрывать окно браузера при запуске
    $ChromeDriverService = [OpenQA.Selenium.Chrome.ChromeDriverService]::CreateDefaultService($ChromeDriver) # создаем объект настроек службы драйвера
    $ChromeDriverService.HideCommandPromptWindow = $True # отключаем весь вывод логирования драйвера в консоль (этот вывод нельзя перенаправить)
    $ChromeDriverService.LogPath = $log # указать путь до файла с журналом
    $ChromeDriverService.EnableAppendLog = $True # не перезаписывать журнал при каждом новом запуске
    #$ChromeDriverService.EnableVerboseLogging = $True # кроме INFO и ошибок, записывать DEBUG сообщения
    $Selenium = New-Object OpenQA.Selenium.Chrome.ChromeDriver($ChromeDriverService, $ChromeOptions) # инициализируем запуск с указанными настройками

    $Selenium.Navigate().GoToUrl("https://google.com") # переходим по указанной ссылке в браузере
    #$Selenium.Manage().Window.Minimize() # свернуть окно браузера после запуска и перехода по нужному url (что бы считать страницу корректно)
    # Ищем поле для ввода текста:
    $Search = $Selenium.FindElements([OpenQA.Selenium.By]::Id('APjFqb'))
    $Search = $Selenium.FindElements([OpenQA.Selenium.By]::XPath('//*[@id="APjFqb"]'))
    $Search = $Selenium.FindElements([OpenQA.Selenium.By]::Name('q'))
    $Search = $Selenium.FindElements([OpenQA.Selenium.By]::XPath('//*[@name="q"]'))
    $Search = $Selenium.FindElements([OpenQA.Selenium.By]::ClassName('gLFyf'))
    $Search = $Selenium.FindElements([OpenQA.Selenium.By]::CssSelector('[jsname="yZiJbe"]'))
    $Search = $Selenium.FindElements([OpenQA.Selenium.By]::TagName('textarea')) | Where-Object ComputedAccessibleRole -eq combobox
    $Search.SendKeys("calculator online") # передаем текст выбранному элементу
    $Search.SendKeys([OpenQA.Selenium.Keys]::Enter) # нажимаем Enter для вызова функции поиска

    Start-Sleep 1
    $div = $Selenium.FindElements([OpenQA.Selenium.By]::TagName("div"))
    $2 = $div | Where-Object {($_.ComputedAccessibleRole -eq "button") -and ($_.ComputedAccessibleLabel -eq "2")}
    $2.Click()
    $2.Click()
    $plus = $div | Where-Object {($_.ComputedAccessibleRole -eq "button") -and ($_.Text -eq "+")}
    $plus.Click()
    $3 = $Selenium.FindElement([OpenQA.Selenium.By]::CssSelector('[jsname="KN1kY"]'))
    $3.Click()
    $3.Click()
    $sum = $Selenium.FindElement([OpenQA.Selenium.By]::CssSelector('[jsname="Pt8tGc"]'))
    $sum.Click()
    $result = $Selenium.FindElement([OpenQA.Selenium.By]::CssSelector('[jsname="VssY5c"]')).Text
    Write-Host "Result: $result" -ForegroundColor Green
}
finally {
    $Selenium.Close()
    $Selenium.Quit()
}
```
### Selenium modules
```PowerShell
Invoke-RestMethod https://raw.githubusercontent.com/Lifailon/Selenium-Modules/rsa/Modules/Get-GPT/Get-GPT.psm1 | Out-File -FilePath "$(New-Item -Path "$($($Env:PSModulePath -split ";")[0])\Get-GPT" -ItemType Directory -Force)\Get-GPT.psm1" -Force
```
`Get-GPT "Исполняй роль калькулятора. Посчитай сумму чисел: 22+33"`
```PowerShell
Invoke-RestMethod https://raw.githubusercontent.com/Lifailon/Selenium-Modules/rsa/Modules/Get-Translation/Get-Translation.psm1 | Out-File -FilePath "$(New-Item -Path "$($($Env:PSModulePath -split ";")[0])\Get-Translation" -ItemType Directory -Force)\Get-Translation.psm1" -Force
```
`Get-Translation -Provider DeepL -Text "I translating the text"` \
`Get-Translation -Provider DeepL -Text "Я перевожу текст"` \
`Get-Translation -Provider Google -Text "I translating the text"` \
`Get-Translation -Provider Google -Text "Я перевожу текст" -Language en`
```PowerShell
Invoke-RestMethod https://raw.githubusercontent.com/Lifailon/Selenium-Modules/rsa/Modules/Get-SpeedTest/Get-SpeedTest.psm1 | Out-File -FilePath "$(New-Item -Path "$($($Env:PSModulePath -split ";")[0])\Get-SpeedTest" -ItemType Directory -Force)\Get-SpeedTest.psm1" -Force
```
`Get-SpeedTest -Provider Libre` \
`Get-SpeedTest -Provider Open` \
`Get-SpeedTest -Provider Ookla`

# IE

`$ie.document.IHTMLDocument3_getElementsByTagName("input")  | select name` получить имена всех Input Box \
`$ie.document.IHTMLDocument3_getElementsByTagName("button") | select innerText` получить имена всех Button \
`$ie.Document.documentElement.innerHTML` прочитать сырой Web Content (<input name="login" tabindex="100" class="input__control input__input" id="uniq32005644019429136" spellcheck="false" placeholder="Логин") \
`$All_Elements = $ie.document.IHTMLDocument3_getElementsByTagName("*")` забрать все элементы \
`$Go_Button = $All_Elements | ? innerText -like "go"` поиск элемента по имени \
`$Go_Button | select ie9_tagName` получить TagName (SPAN) для быстрого дальнейшего поиска \
`$SPAN_Elements = $ie.document.IHTMLDocument3_getElementsByTagName("SPAN")`
```PowerShell
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
# Sockets

### UDP-Socket

[Source](https://cloudbrothers.info/en/test-udp-connection-powershell/)
```PowerShell
function Start-UDPServer {
    param(
        $Port = 5201
    )
    $RemoteComputer = New-Object System.Net.IPEndPoint([System.Net.IPAddress]::Any, 0)
    do {
        $UdpObject = New-Object System.Net.Sockets.UdpClient($Port)
        $ReceiveBytes = $UdpObject.Receive([ref]$RemoteComputer)
        $UdpObject.Close()
        $ASCIIEncoding = New-Object System.Text.ASCIIEncoding
        [string]$ReturnString = $ASCIIEncoding.GetString($ReceiveBytes)
        [PSCustomObject]@{
            LocalDateTime = $(Get-Date -UFormat "%Y-%m-%d %T")
            ClientIP      = $RemoteComputer.address.ToString()
            ClientPort    = $RemoteComputer.Port.ToString()
            Message       = $ReturnString
        }
    } while (1)
}
```
`Start-UDPServer -Port 5201`

### Test-NetUDPConnection
```PowerShell
function Test-NetUDPConnection {
    param(
        [string]$ComputerName = "127.0.0.1",
        [int32]$PortServer = 5201,
        [int32]$PortClient = 5211,
        $Message
    )
    begin {
        $UdpObject = New-Object system.Net.Sockets.Udpclient($PortClient)
        $UdpObject.Connect($ComputerName, $PortServer)
    }
    process {
        $ASCIIEncoding = New-Object System.Text.ASCIIEncoding
        if (!$Message) { $Message = Get-Date -UFormat "%Y-%m-%d %T" }
        $Bytes = $ASCIIEncoding.GetBytes($Message)
        [void]$UdpObject.Send($Bytes, $Bytes.length)
    }
    end {
        $UdpObject.Close()
    }
}
```
`Test-NetUDPConnection -ComputerName 127.0.0.1 -PortServer 5201` \
`Test-NetUDPConnection -ComputerName 127.0.0.1 -PortServer 514 -Message "<30>May 31 00:00:00 HostName multipathd[784]: Test message"`

### TCP-Socket
```PowerShell
function Start-TCPServer {
    param(
        $Port = 5201
    )
    do {
        $TcpObject = New-Object System.Net.Sockets.TcpListener($port)
        $ReceiveBytes = $TcpObject.Start()
        $ReceiveBytes = $TcpObject.AcceptTcpClient()
        $TcpObject.Stop()
        $ReceiveBytes.Client.RemoteEndPoint | select Address, Port
    } while (1)
}
```
`Start-TCPServer -Port 5201` \
`Test-NetConnection -ComputerName 127.0.0.1 -Port 5201`

### WakeOnLan

Broadcast package consisting of 6 byte filled "0xFF" and then 96 byte where the mac address is repeated 16 times
```PowerShell
function Send-WOL {
    param (
        [Parameter(Mandatory = $True)]$Mac,
        $IP,
        [int]$Port = 9
    )
    $Mac = $Mac.replace(":", "-")
    if (!$IP) { $IP = [System.Net.IPAddress]::Broadcast }
    $SynchronizationChain = [byte[]](, 0xFF * 6)
    $ByteMac = $Mac.Split("-") | % { [byte]("0x" + $_) }
    $Package = $SynchronizationChain + ($ByteMac * 16)
    $UdpClient = New-Object System.Net.Sockets.UdpClient
    $UdpClient.Connect($IP, $port)
    $UdpClient.Send($Package, $Package.Length)
    $UdpClient.Close()
}
```
`Send-WOL -Mac "D8-BB-C1-70-A3-4E"` \
`Send-WOL -Mac "D8-BB-C1-70-A3-4E" -IP 192.168.3.100`

### HTTPListener
```PowerShell
$httpListener = New-Object System.Net.HttpListener
$httpListener.Prefixes.Add("http://+:8888/")
$httpListener.Start()
while (!([console]::KeyAvailable)) {
    $info = Get-Service | select name, status | ConvertTo-HTML
    $context = $httpListener.GetContext()
    $context.Response.StatusCode = 200
    $context.Response.ContentType = 'text/HTML'
    $WebContent = $info
    $EncodingWebContent = [Text.Encoding]::UTF8.GetBytes($WebContent)
    $context.Response.OutputStream.Write($EncodingWebContent , 0, $EncodingWebContent.Length)
    $context.Response.Close()
    Get-NetTcpConnection -LocalPort 8888
(Get-Date).datetime
}
$httpListener.Close()
```
### WebClient

`[System.Net.WebClient] | Get-Member` \
`(New-Object Net.WebClient).DownloadString("https://raw.githubusercontent.com/Lifailon/PowerShell-Commands/rsa/README.md")`

### HttpClient
```PowerShell
$url = "https://github.com/PowerShell/PowerShell/releases/download/v7.4.2/PowerShell-7.4.2-win-x64.zip"
$path = "$home\Downloads\$(Split-Path -Path $url -Leaf)"
$httpClient = [System.Net.Http.HttpClient]::new()
# Выполнение GET-запроса для загрузки файла (считывая заголовки ответа)
$response = $httpClient.GetAsync($url, [System.Net.Http.HttpCompletionOption]::ResponseHeadersRead).Result
# Получение потока содержимого из заголовка ответа
$stream = $response.Content.ReadAsStreamAsync().Result
# Открытие файла для записи
$fileStream = [System.IO.File]::OpenWrite($path)
try {
    # Создание буфера размером 81920 байт (80 КБ) для чтения данных из потока
    $buffer = New-Object byte[] 81920
    # Чтение данных из потока и запись их в файл
    while (($bytesRead = $stream.Read($buffer, 0, $buffer.Length)) -ne 0) {
        $fileStream.Write($buffer, 0, $bytesRead)
    }
}
finally {
    # Освобождение ресурсов, связанных с потоками
    $stream.Dispose()
    $fileStream.Dispose()
}
```
### Certificate
```PowerShell
function Get-WebCertificate ($srv) {
    $iwr = iwr $srv
    $status_code = $iwr.StatusCode
    $status = $iwr.BaseResponse.StatusCode
    $info = $iwr.BaseResponse.Server
    $spm = [System.Net.ServicePointManager]::FindServicePoint($srv)
    $date_end = $spm.Certificate.GetExpirationDateString()
    $cert_name = ($spm.Certificate.Subject) -replace "CN="
    $cert_owner = ((($spm.Certificate.Issuer) -split ", ") | where { $_ -match "O=" }) -replace "O="
    $Collections = New-Object System.Collections.Generic.List[System.Object]
    $Collections.Add([PSCustomObject]@{
            Host        = $srv;
            Server      = $info;
            Status      = $status;
            StatusCode  = $status_code;
            Certificate = $cert_name;
            Issued      = $cert_owner;
            End         = $date_end
        })
    $Collections
}
```
`Get-WebCertificate https://google.com`

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

# pki

`New-SelfSignedCertificate -CertStoreLocation Cert:\LocalMachine\My -DnsName "$env:computername" -FriendlyName "Test Certificate" -NotAfter (Get-Date).AddYears(5)` создать самоподписанный сертификат (в LocalMachine\My - Сертификаты компьютера\Личное) с сроком действия 5 лет

`Get-ChildItem -Path Cert:\CurrentUser\Root\` список всех установленных сертификатов в хранилище Доверенные корневые ЦС Текущего пользователя \
`Get-ChildItem -Path Cert:\CurrentUser\My\` список самозаверяющих сертификатов в Личное хранилище Текущего пользователя \
`Get-ChildItem -Path Cert:\LocalMachine\My\` список самозаверяющих сертификатов в Личное хранилище Локального компьютера \
`Get-ChildItem -Path Cert:\LocalMachine\My\ | select NotBefore,NotAfter,Thumbprint,Subject` срок действия сертификата \
`Get-ChildItem -Path Cert:\LocalMachine\My\ | where Thumbprint -eq D9356FB774EE0E6206B7D5B59B99102CA5B17BDA` поиск сертификат по отпечатку

`Get-ChildItem -Path $env:APPDATA\Microsoft\SystemCertificates\My\Certificates\` сертификаты в файловой системе, каждый файл соответствует сертификату, установленному в личном хранилище текущего пользователя \
`Get-ChildItem -Path $env:APPDATA\Microsoft\SystemCertificates\My\Keys\` ссылки на объекты закрытых ключей, созданных поставщиком хранилища ключей (KSP) \
`Get-ChildItem -Path HKCU:\Software\Microsoft\SystemCertificates\CA\Certificates | ft -AutoSize` список сертификатов в реестре вошедшего в систему пользователя

`$cert = (Get-ChildItem -Path Cert:\CurrentUser\My\)[1]` выбрать сертификат \
`$cert | Remove-Item` удалить сертификат

`Export-Certificate -FilePath $home\Desktop\certificate.cer -Cert $cert` экспортировать сертификат \
`$cert.HasPrivateKey` проверить наличие закрытого ключа \
`$pass = "password" | ConvertTo-SecureString -AsPlainText -Force` создать пароль для шифрования закрытого ключа \
`Export-PfxCertificate -FilePath $home\Desktop\certificate.pfx -Password $pass -Cert $certificate` экспортировать сертификат с закрытым ключем

`Import-Certificate -FilePath $home\Desktop\certificate.cer -CertStoreLocation Cert:\CurrentUser\My` импортировать сертификат \
`Import-PfxCertificate -Exportable -Password $pass -CertStoreLocation Cert:\CurrentUser\My -FilePath $home\Desktop\certificate.pfx`

# OpenSSL
```PowerShell
Invoke-WebRequest -Uri https://slproweb.com/download/Win64OpenSSL_Light-3_1_1.msi -OutFile $home\Downloads\OpenSSL-Light-3.1.1.msi
Start-Process $home\Downloads\OpenSSL-Light-3.1.1.msi -ArgumentList '/quiet' -Wait` установить msi пакет в тихом режиме (запуск от имени Администратора)
rm $home\Downloads\OpenSSL-Light-3.1.1.msi
cd "C:\Program Files\OpenSSL-Win64\bin"
```
- Изменить пароль для PFX \
`openssl pkcs12 -in "C:\Cert\domain.ru.pfx" -out "C:\Cert\domain.ru.pem" -nodes` экспортируем имеющийся сертификат и закрытый ключ в .pem-файл без пароля с указанием текущего пароля \
`openssl pkcs12 -export -in "C:\Cert\domain.ru.pem" -out "C:\Cert\domain.ru_password.pfx" -nodes` конвертируем .pem обратно в .pfx c указанием нового пароля

- Конвертация из закрытого и открытого ключа PEM в PFX \
`openssl pkcs12 -export -in "C:\tmp\vpn\vpn.itproblog.ru-crt.pem" -inkey "C:\tmp\vpn\vpn.itproblog.ru-key.pem" -out "C:\tmp\vpn\vpn.iiproblog.ru.pfx"` \
in – путь до файла с открытым ключом \
inkey – путь до файла с закрытым ключом \
out – путь до файла, в который будет конвертирован сертификат (pfx)

- Конвертация PFX в CRT \
`openssl pkcs12 -in "C:\OpenSSL-Win64\bin\_.domain.ru.pfx" -clcerts -out "C:\OpenSSL-Win64\bin\_.domain.ru.crt"` указывается текущий и 2 раза новый пароль PEM pass phrase (файл содержит EGIN CERTIFICATE и BEGIN ENCRYPTED PRIVATE KEY) \
`openssl pkcs12 -in "C:\OpenSSL-Win64\bin\_.domain.ru.pfx" -clcerts -nokeys -out "C:\OpenSSL-Win64\bin\_.domain.ru.crt"` без ключа, получить открытую часть (файл содержит только EGIN CERTIFICATE)

- Конвертация PFX в KEY \
`openssl pkcs12 -in "C:\OpenSSL-Win64\bin\_.domain.ru.pfx" -nocerts -out "C:\OpenSSL-Win64\bin\_.domain.ru.key"` файл содержит только BEGIN ENCRYPTED PRIVATE KEY

- Снять пароль к закрытого ключа .key \
`openssl rsa -in "C:\OpenSSL-Win64\bin\_.domain.ru.key" -out "C:\OpenSSL-Win64\bin\_.domain.ru-decrypted.key"`

- CRT и KEY в PFX: \
`openssl pkcs12 -inkey certificate.key -in certificate.crt -export -out certificate.pfx`

# OpenVPN

`Invoke-WebRequest -Uri https://swupdate.openvpn.org/community/releases/OpenVPN-2.6.5-I001-amd64.msi -OutFile $home\Downloads\OpenVPN-2.6.5.msi` \
`Start-Process $home\Downloads\OpenVPN-2.6.5.msi -ArgumentList '/quiet /SELECT_OPENSSL_UTILITIES=1' -Wait` \
`msiexec /i $home\Downloads\OpenVPN-2.6.5.msi ADDLOCAL=EasyRSA /passive /quiet # установить отдельный компонент EasyRSA Certificate Management Scripts` \
`# msiexec /i $home\Downloads\OpenVPN-2.6.5.msi ADDLOCAL=OpenVPN.Service,Drivers,Drivers.Wintun,OpenVPN,OpenVPN.GUI,OpenVPN.GUI.OnLogon,EasyRSA /passive` выборочная установка \
`# Invoke-WebRequest -Uri https://github.com/OpenVPN/easy-rsa/releases/download/v3.1.5/EasyRSA-3.1.5-win64.zip -OutFile $home\Downloads\EasyRSA-3.1.5.zip` скачать отдельный пакет EasyRSA \
`rm $home\Downloads\OpenVPN-2.6.5.msi`

`cd "C:\Program Files\OpenVPN\easy-rsa"` \
`Copy-Item vars.example vars` файл конфигурации для EasyRSA
```
set_var EASYRSA_TEMP_DIR "$EASYRSA_PKI"
set_var EASYRSA_REQ_COUNTRY "RU"
set_var EASYRSA_REQ_PROVINCE "MSK"
set_var EASYRSA_REQ_CITY "MSK"
set_var EASYRSA_REQ_ORG "FAILON.NET"
set_var EASYRSA_REQ_EMAIL "lifailon@domain.ru"
set_var EASYRSA_REQ_OU "IT"
#set_var EASYRSA_KEY_SIZE 2048
#set_var EASYRSA_CA_EXPIRE 3650
#set_var EASYRSA_CERT_EXPIRE 825
```
`.\EasyRSA-Start.bat` среда EasyRSA Shell \
`easyrsa init-pki` инициализация PKI, создает директорию: C:\Program Files\OpenVPN\easy-rsa\pki и читает переменные файла \easy-rsa\vars \
`easyrsa build-ca` генерация корневого CA с указанием пароля и произвольное имя сервера (\pki\ca.crt и \pki\private\ca.key) \
`easyrsa gen-req server nopass` генерация запроса сертификата и ключ для сервера OpenVPN - yes (\pki\reqs\server.req и \pki\private\server.key) \
`easyrsa sign-req server server` подписать запрос на выпуск сертификата сервера с помощью CA - yes (\pki\issued\server.crt) \
`easyrsa gen-dh` создать ключ Диффи-Хеллмана (\pki\dh.pem) \
`easyrsa gen-req client1` nopass` генерация запроса сертификата и ключ для клиента OpenVPN (\pki\reqs\client1.req и \pki\private\client1.key)` \
`easyrsa sign-req client client1` подписать запрос на выпуск сертификата клиента с помощью CA - yes (\pki\issued\client1.crt) \
`easyrsa revoke client1` отозвать сертификат пользователя \
`openssl rsa -in "C:\Program Files\OpenVPN\easy-rsa\pki\private\client1.key" -out "C:\Program Files\OpenVPN\easy-rsa\pki\private\client1_nopass.key"` снять защиту паролем для ключа (BEGIN ENCRYPTED PRIVATE KEY -> BEGIN PRIVATE KEY) \
`exit` \
`cd "C:\Program Files\OpenVPN\bin"` \
`.\openvpn --genkey secret ta.key` генерация ключа tls-auth (\bin\ta.key) \
`Move-Item "C:\Program Files\OpenVPN\bin\ta.key" "C:\Program Files\OpenVPN\easy-rsa\pki\"`

### server ovpn

`# Copy-Item "C:\Program Files\OpenVPN\sample-config\server.ovpn" "C:\Program Files\OpenVPN\config-auto\server.ovpn"` \
`New-Item -ItemType File -Path "C:\Program Files\OpenVPN\config-auto\server.ovpn"`
```
port 1194
proto udp
# Что именно инкапсулировать в туннеле (ethernet фреймы - tap или ip пакеты - tun)
dev tun
ca "C:\\Program Files\\OpenVPN\\easy-rsa\\pki\\ca.crt"
cert "C:\\Program Files\\OpenVPN\\easy-rsa\\pki\\issued\\server.crt"
key "C:\\Program Files\\OpenVPN\\easy-rsa\\pki\\private\\server.key"
dh "C:\\Program Files\\OpenVPN\\easy-rsa\\pki\\dh.pem"
server 192.168.4.0 255.255.255.0
# Хранит список сопоставления ip для клиентов, что бы назначить тот же адрес при перезапуске сервера
# ifconfig-pool-persist "C:\\Program Files\\OpenVPN\\dhcp-client-list.txt"
# Разрешить клиентам подключаться под одним ключом
# duplicate-cn
# max-clients 30
# Разрешить обмен трафиком между клиентами
client-to-client
# compress
tls-auth "C:\\Program Files\\OpenVPN\\easy-rsa\\pki\\ta.key" 0
cipher AES-256-GCM
keepalive 20 60
# Не перечитавать файлы ключей при перезапуске туннеля
persist-key
# Оставляет без изменения устройства tun/tap при перезапуске OpenVPN
persist-tun
status "C:\\Program Files\\OpenVPN\\log\\status.log"
log "C:\\Program Files\\OpenVPN\\log\\openvpn.log"
verb 3
mute 20
windows-driver wintun
# Открыть доступ к подсети за сервером
push "route 192.168.3.0 255.255.255.0"
push "route 192.168.4.0 255.255.255.0"
# Завернуть все запросы клиента (в том числе Интернет трафик) на OpenVPN сервер
# push "redirect-gateway def1"
# push "dhcp-option DNS 192.168.3.101"
# push "dhcp-option DOMAIN failon.net"
```
`New-NetFirewallRule -DisplayName "AllowOpenVPN-In" -Direction Inbound -Protocol UDP –LocalPort 1194 -Action Allow` на сервере \
`New-NetFirewallRule -DisplayName "AllowOpenVPN-Out" -Direction Outbound -Protocol UDP –LocalPort 1194 -Action Allow` на клиенте \
`Get-Service *openvpn* | Restart-Service`

### client ovpn

`# Copy-Item "C:\Program Files\OpenVPN\sample-config\client.ovpn" "C:\Program Files\OpenVPN\config-auto\client.ovpn"` \
`New-Item -ItemType File -Path "C:\Program Files\OpenVPN\config-auto\client.ovpn"`
```
client
dev tun
proto udp
remote 26.115.154.67 1194
resolv-retry infinite
nobind
persist-key
persist-tun
ca ca.crt
cert client1.crt
key client1.key
remote-cert-tls server
tls-auth ta.key 1
cipher AES-256-GCM
connect-retry-max 25
# Использовать драйвер wintun и полный путь до сертификатов при использовании openvpn gui
windows-driver wintun
verb 3
```
### Client

`iwr -Uri https://openvpn.net/downloads/openvpn-connect-v3-windows.msi -OutFile "$home\downloads\OpenVPN-Connect-3.msi"` \
Передать конфигурацию и ключи: \
`client.ovpn` \
`ca.crt` \
`dh.pem` \
`ta.key` \
`client1.crt` \
`client1.key`

# Route

`Get-Service RemoteAccess | Stop-Service` \
`Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" -Name "IPEnableRouter" -Value 1` включает IP маршрутизацию \
`(Get-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters").IPEnableRouter` \
`Get-NetIPInterface | select ifIndex,InterfaceAlias,AddressFamily,ConnectionState,Forwarding | ft` отобразить сетевые интерфейсы \
`Set-NetIPInterface -ifIndex 13 -Forwarding Enabled` включить переадресацию на интерфейсе

`sysctl net.ipv4.ip_forward=1` \
`echo "sysctl net.ipv4.ip_forward = 1" >> /etc/sysctl.conf`

`Get-NetRoute` \
`New-NetRoute -DestinationPrefix "192.168.3.0/24" -NextHop "192.168.4.1" -InterfaceIndex 8` \
`route -p add 192.168.3.0 mask 255.255.255.0 192.168.4.1 metric 1` \
`route -p change 192.168.3.0 mask 255.255.255.0 192.168.4.1 metric 2` \
`route -p add 192.168.3.0 mask 255.255.255.0 192.168.4.1 metric 1 if 7` указать номер сетевого интерфейса на который необходимо посылать пакет (Wintun Userspace Tunnel) \
`route print -4` \
`route delete 192.168.3.0`

`tracert 192.168.3.101` с 192.168.4.6
```
1    17 ms     *       22 ms  192.168.4.1
2    12 ms    13 ms    14 ms  192.168.3.101
```
`route add -net 192.168.4.0 netmask 255.255.255.0 gw 192.168.3.100` \
`route -e`

`traceroute 192.168.4.6` с 192.168.3.101
```
1  192.168.3.100 (192.168.3.100)  0.148 ms  0.110 ms  0.106 ms
2  192.168.4.6 (192.168.4.6)  14.573 ms * *
```
`ping 192.168.3.101 -t` с 192.168.4.6 \
`tcpdump -n -i ens33 icmp` на 192.168.3.101
```
14:36:34.533771 IP 192.168.4.6 > 192.168.3.101: ICMP echo request, id 1, seq 2962, length 40 # отправил запрос
14:36:34.533806 IP 192.168.3.101 > 192.168.4.6: ICMP echo reply, id 1, seq 2962, length 40 # отправил ответ
```
# NAT

`Get-Command -Module NetNat` \
`New-NetNat -Name LocalNat -InternalIPInterfaceAddressPrefix "192.168.3.0/24"` \
`Add-NetNatStaticMapping -NatName LocalNat -Protocol TCP -ExternalIPAddress 0.0.0.0 -ExternalPort 80 -InternalIPAddress 192.168.3.102 -InternalPort 80` \
`Remove-NetNatStaticMapping -StaticMappingID 0` \
`Remove-NetNat -Name LocalNat`

# WireGuard

`Invoke-WebRequest "https://download.wireguard.com/windows-client/wireguard-amd64-0.5.3.msi" -OutFile "$home\Downloads\WireGuard-Client-0.5.3.msi"` \
`msiexec.exe /i "$home\Downloads\WireGuard-Client-0.5.3.msi" DO_NOT_LAUNCH=1 /qn` \
`Invoke-WebRequest "http://www.wiresock.net/downloads/wiresock-vpn-gateway-x64-1.1.4.1.msi" -OutFile "$home\Downloads\WireSock-VPN-Gateway-1.1.4.1.msi"` \
`msiexec.exe /i "http://www.wiresock.net/downloads/wiresock-vpn-gateway-x64-1.1.4.1.msi" /qn` \
`$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")` \
`wg-quick-config -add -start` \
`26.115.154.67:8181` \
`192.168.21.4/24` \
`Successfully saved client configuration: C:\ProgramData\NT KERNEL\WireSock VPN Gateway\wsclient_1.conf` \
`Successfully saved server configuration: C:\ProgramData\NT KERNEL\WireSock VPN Gateway\wiresock.conf` \
`get-service *wire*` \
`wg show` \
`wg-quick-config -add -restart` add client

wiresock.conf
```
[Interface]
PrivateKey = gCHC0g2JPwr6sXPiaOL4/KTkMyjN9TculrJUA/GORV8=
Address = 192.168.21.5/24
ListenPort = 8181

[Peer]
PublicKey = NoSxjew2RCHiUzI6mlahjd4I+0EcLsoYom/H01z91yU=
AllowedIPs = 192.168.21.6/32
```
wsclient_1.conf (добавить маршруты для клиента в AllowedIPs)
```
[Interface]
PrivateKey = yIpRQRmaGrrk9Y+49E8JhEpFmKzSeecvUAdeNgf1hUM=
Address = 192.168.21.6/24
DNS = 8.8.8.8, 1.1.1.1
MTU = 1420

[Peer]
PublicKey = Fp7674VSYeGj8CYt6RCKR7Qz1y/IKUXCw8ImOFhX3hk=
AllowedIPs = 192.168.21.0/24, 192.168.3.0/24
Endpoint = 26.115.154.67:8181
PersistentKeepalive = 25
```
# VpnClient

`Get-Command -Module VpnClient` \
`Add-VpnConnection -Name "vpn-failon" -ServerAddress "26.115.154.67" -TunnelType L2TP -L2tpPsk "123098" -EncryptionLevel "Required" -AuthenticationMethod MSChapv2 -RememberCredential -AllUserConnection –PassThru -Force` \
`-TunnelType PPTP/L2TP/SSTP/IKEv2/Automatic` \
`-L2tpPsk` использовать общий ключ для аутентификации (без параметра, для L2TP аутентификации используется сертификат) \
`-AuthenticationMethod Pap/Chap/MSChapv2/Eap/MachineCertificate` \
`-EncryptionLevel NoEncryption/Optional/Required/Maximum/Custom` \
`-SplitTunneling` заворачивать весь трафик через VPN-туннель (включение Use default gateway on remote network в настройках параметра VPN адаптера) \
`-UseWinlogonCredential` использовать учетные данные текущего пользователя для аутентификации на VPN сервере \
`-RememberCredential` разрешить сохранять учетные данные для VPN подключения (учетная запись и пароль сохраняются в диспетчер учетных данных Windows после первого успешного подключения) \
`-DnsSuffix domain.local` \
`-AllUserConnection` разрешить использовать VPN подключение для всех пользователей компьютера (сохраняется в конфигурационный файл: C:\ProgramData\Microsoft\Network\Connections\Pbk\rasphone.pbk)

`Install-Module -Name VPNCredentialsHelper` модуль для сохранения логина и пароля в Windows Credential Manager для VPN подключения \
`Set-VpnConnectionUsernamePassword -connectionname vpn-failon -username user1 -password password`

`rasdial "vpn-failon"` подключиться \
`Get-VpnConnection -AllUserConnection | select *` список VPN подключения, доступных для всех пользователей, найстройки и текущий статус подключения (ConnectionStatus) \
`Add-VpnConnectionRoute -ConnectionName vpn-failon -DestinationPrefix 192.168.3.0/24 –PassThru` динамически добавить в таблицу маршрутизации маршрут, который будет активен при подключении к VPN \
`Remove-VpnConnection -Name vpn-failon -AllUserConnection -Force` удалить

`Set-VpnConnection -Name "vpn-failon" -SplitTunneling $True` включить раздельное тунеллирование \
`Add-VpnConnectionRoute -ConnectionName "vpn-failon" -DestinationPrefix 172.22.22.0/24` настроить маршрутизацию к указанной подсети через VPN-соединение \
`(Get-VpnConnection -ConnectionName "vpn-failon").routes` отобразить таблицу маршрутизации для указанного соединения \
`Remove-VpnConnectionRoute -ConnectionName "vpn-failon" -DestinationPrefix "172.22.23.0/24"`

# ProxyClient

`$user = "lifailon"` \
`$pass = "Proxy"` \
`$SecureString = ConvertTo-SecureString $pass -AsPlainText -Force` \
`$Credential = New-Object System.Management.Automation.PSCredential($user, $SecureString)` \
`[System.Net.Http.HttpClient]::DefaultProxy = New-Object System.Net.WebProxy("http://192.168.3.100:9090")` \
`[System.Net.Http.HttpClient]::DefaultProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials` \
`[System.Net.Http.HttpClient]::DefaultProxy.Credentials = $Credential` \
`Invoke-RestMethod http://ifconfig.me/ip` узнать внешний ip-адрес (по умолчанию в текущей сессии подключения будут происходить через заданный прокси сервер) \
`Invoke-RestMethod https://kinozal.tv/rss.xml`

# netsh

### Reverse Proxy

`netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=192.168.3.108` настраивает входящее подключение на 8080 порту и переадресует трафик на 80 порт указанного хоста \
`netsh interface portproxy show all` отобразить список всех настроек \
`netsh interface portproxy delete v4tov4 listenport=8080 listenaddress=0.0.0.0` удалить переадресацию

### Wlan

`netsh wlan show profile` список сохраненны профилей Wi-Fi и паролей \
`netsh wlan show interfaces` хар-ки текущей сети (MAC, speed) \
`netsh wlan show profile SSID-Name-Network key=clear` очистить пароль \
`netsh wlan show networks` список видемых сетей \
`netsh wlan disconnect` отключиться от Wi-Fi \
`netsh wlan connect name="SSID-Name-Network"` подключиться \
`netsh wlan show drivers` драйвер Wi-Fi \
`netsh wlan set hostednetwork mode=allow ssid="WiFi-Test" key="password"` создание точки доступа Wi-Fi (SoftAP)

### Firewall

`netsh advfirewall set allprofiles state off` отключить fw \
`netsh advfirewall reset` сбросить настройки \
`netsh advfirewall firewall add rule name="Open Remote Desktop" protocol=TCP dir=in localport=3389 action=allow` открыть порт 3389 \
`netsh advfirewall firewall add rule name="All ICMP V4" dir=in action=allow protocol=icmpv4` открыть icmp

# OpenSSH

`Get-WindowsCapability -Online | ? Name -like 'OpenSSH.Client*'` \
`Add-WindowsCapability -Online -Name OpenSSH.Client*` \
`dism /Online /Add-Capability /CapabilityName:OpenSSH.Client~~~~0.0.1.0` \
`iwr https://github.com/PowerShell/Win32-OpenSSH/releases/download/v9.2.2.0p1-Beta/OpenSSH-Win64-v9.2.2.0.msi -OutFile $home\Downloads\OpenSSH-Win64-v9.2.2.0.msi` скачать \
`msiexec /i $home\Downloads\OpenSSH-Win64-v9.2.2.0.msi` установить msi пакет \
`Set-Service sshd -StartupType Automatic` \
`Get-NetTCPConnection | where LocalPort -eq 22` \
`New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22` \
`Get-NetFirewallRule -Name *ssh*` \
`Start-Process notepad++ C:\Programdata\ssh\sshd_config` конфигурационный файл \
`GSSAPIAuthentication yes` включить Kerberos аутентификацию (через AD) \
`SyslogFacility LOCAL0` включить локальное ведение журнала в файл (C:\ProgramData\ssh\logs\sshd.log) \
`LogLevel INFO` \
`Restart-Service sshd` \
`ssh -K $srv` выполнить Kerberos аутентификацию \
`ssh Lifailon@192.168.3.99 -p 22` \
`pwsh -command Get-Service` \
`ssh -L 3101:192.168.3.101:22 -R 3101:192.168.3.101:22 lifailon@192.168.3.101 -p 22` SSH Tunnel lifailon@localhost:3101 -> 192.168.3.101:3101

### PSRemoting over SSH

`Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0` установка OpenSSH Server \
`Get-WindowsCapability -Online | ? Name -like 'OpenSSH.Ser*'` \
`iex "& { $(irm https://aka.ms/install-powershell.ps1) } -UseMSI"` установка PowerShell Core последней версии (требуется на клиентской стороне) \
`Set-Service -Name sshd -StartupType "Automatic"` \
`Start-Service sshd` \
`Get-NetTCPConnection -State Listen|where {$_.localport -eq '22'}` \
`Enable-NetFirewallRule -Name *OpenSSH-Server*` \
`New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "C:\Program Files\PowerShell\7\pwsh.exe" -PropertyType String –Force` изменить интерпритатор по умолчанию на pwsh \
`notepad $Env:ProgramData\ssh\sshd_config`

PasswordAuthentication yes \
Subsystem powershell c:/progra~1/powershell/7/pwsh.exe -sshs -NoLogo # запуск интерпретатора pwsh для удаленных SSH подключений

`Restart-Service sshd`

`$session = New-PSSession -HostName 192.168.3.100 -Port 2121 -UserName lifailon -SSHTransport` \
`Invoke-Command -Session $session -ScriptBlock {Get-Service}`

# WinRM

`Enter-PSSession -ComputerName $srv` подключиться к PowerShell сессии через PSRemoting. Подключение возможно только по FQDN-имени \
`Invoke-Command $srv -ScriptBlock {Get-ComputerInfo}` выполнение команды через PSRemoting \
`$session = New-PSSession $srv` открыть сессию \
`Get-PSSession` отобразить активные сессии \
`icm -Session $session {$srv = $using:srv}` передать переменную текущей сессии ($using) в удаленную \
`Disconnect-PSSession $session` закрыть сессию \
`Remove-PSSession $session` удалить сессию \
`Import-Module -Name ActiveDirectory -PSSession $srv` импортировать модуль с удаленного компьютера в локальную сессию

### Windows Remote Management Configuration

`winrm quickconfig -quiet` изменит запуск службы WinRM на автоматический, задаст стандартные настройки WinRM и добавить исключения для портов в fw \
`Enable-PSRemoting –Force` включить PowerShell Remoting, работает только для доменного и частного сетевых профилей Windows \
`Enable-PSRemoting -SkipNetworkProfileCheck -Force` для настройки компьютера в общей (public) сети (работает с версии powershell 6)

`$NetProfiles = Get-NetConnectionProfile` отобразить профили сетевых подключений \
`Set-NetConnectionProfile -InterfaceIndex $NetProfiles[1].InterfaceIndex -NetworkCategory Private` изменить тип сети для профиля (DomainAuthenticated/Public) \
`(Get-CimInstance -ClassName Win32_ComputerSystem).PartOfDomain` проверить, что компьютер добавлен в домен AD \
`Get-Service WinRM | Set-Service -StartupType AutomaticDelayedStart` отложенный запуск \
`Get-Service -Name winrm -RequiredServices` статус зависимых служб \
`New-NetFirewallRule -Profile Any -DisplayName "WinRM HTTP" -Direction Inbound -Protocol TCP -LocalPort 5985,5986` \
`Test-NetConnection $srv -port 5895` проверить порт \
`Test-WSMan $srv -ErrorAction Ignore` проверить работу WinRM на удаленном компьютере (игнорировать вывод ошибок для скрипта) или локально (localhost)

`$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\LocalMachine\My -DnsName "$env:computername" -FriendlyName "WinRM HTTPS Certificate" -NotAfter (Get-Date).AddYears(5)` создать самоподписанный сертификат \
`$Thumbprint = $Cert.Thumbprint` забрать отпечаток \
`New-Item -Path WSMan:\Localhost\Listener -Transport HTTPS -Address * -CertificateThumbprint $Thumbprint -Name WinRM_HTTPS_Listener -Force` создать прослушиватель \
`New-NetFirewallRule -DisplayName 'WinRM HTTPS' -Profile Domain,Private -Direction Inbound -Action Allow -Protocol TCP -LocalPort 5986` открыть порт в fw
```
$selector_set = @{
    Address = "*"
    Transport = "HTTPS"
}
$value_set = @{
    CertificateThumbprint = "66ABFDA044D8C85135048186E2FDC0DBE6125163"
}
New-WSManInstance -ResourceURI "winrm/config/Listener" -SelectorSet $selector_set -ValueSet $value_set
```
`winrm get winrm/config` отобразить всю конфигурацию (Client/Service) \
`winrm get winrm/config/service/auth` конфигурация авторизации на сервере \
`winrm enumerate winrm/config/listener` текущая конфигурация прослушивателей WinRM (отображает отпечаток сертификата для HTTPS 5986) \
`Get-ChildItem -Path Cert:\LocalMachine\My\ | where Thumbprint -eq D9356FB774EE0E6206B7D5B59B99102CA5B17BDA | select *` информация о сертификате

`ls WSMan:\localhost\Client` конфигурацию клиента \
`ls WSMan:\localhost\Service` конфигурация сервера \
`ls WSMan:\localhost\Service\auth` список всех конфигураций аутентификации WinRM сервера \
`Set-Item -path WSMan:\localhost\Service\auth\basic -value $true` разрешить локальную аутентификацию к текущему серверу \
`ls HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\WSMAN` настройки в реестре (например, для включения аудентификации в \Service\auth_basic = 1) \
`Set-Item WSMan:\localhost\Client\TrustedHosts -Value 192.168.* -Force` добавить доверенные хосты в конфигурацию на клиенте, чтобы работала Negotiate аутентификация через NTLM \
`Set-Item WSMan:\localhost\Client\TrustedHosts -Value 192.168.3.100 -Concatenate -Force` добавить второй компьютер \
`ls WSMan:\localhost\Client\TrustedHosts` \
`Set-Item WSMan:\localhost\Client\AllowUnencrypted $true` включить передача незашифрованных данных конфигурации клиента \
`Set-Item WSMan:\localhost\Service\AllowUnencrypted $true` включить передача незашифрованных данных конфигурации сервера (необходимо быть в private сети)

`Get-PSSessionConfiguration` проверить, включен ли PSremoting и вывести список пользователей и групп, которым разрешено подключаться через WinRM \
`Set-PSSessionConfiguration -Name Microsoft.PowerShell -ShowSecurityDescriptorUI` назначить права доступа через дескриптор безопасности текущей сессии (до перезагруки) \
`(Get-PSSessionConfiguration -Name "Microsoft.PowerShell").SecurityDescriptorSDDL` получить настройки дескриптора в формате SDDL \
`Set-PSSessionConfiguration -Name Microsoft.PowerShell -SecurityDescriptorSDDL $SDDL` применить настройки дескриптора на другом компьютере без использования GUI \

`New-LocalUser "WinRM-Writer" -Password (ConvertTo-SecureString -AsPlainText "123098")` создать пользователя \
`Add-LocalGroupMember -Group "Remote Management Users" -Member "WinRM-Writer"` добавить пользователя WinRM-Writer в локальную группу доступа "Пользователи удаленного управления" \
`cmdkey /add:192.168.3.99 /user:WinRM-Writer /pass:123098` сохранить пароль в CredentialManager
`cmdkey /list` \
`Import-Module CredentialManager` \
`Add-Type -AssemblyName System.Web` \
`New-StoredCredential -Target 192.168.3.99 -UserName WinRM-Writer -Password 123098 -Comment WinRM` сохранить пароль в CredentialManager (из PS5) \
`Get-StoredCredential -AsCredentialObject` \
`$cred = Get-StoredCredential -Target 192.168.3.99` \
`Enter-PSSession -ComputerName 192.168.3.99 -Credential $cred -Authentication Negotiate` \
`Enter-PSSession -ComputerName 192.168.3.99 -Credential $cred -Authentication Basic -Port 5985` работает при отключении allowunencrypted на стороне сервера и клиента \
`winrs -r:http://192.168.3.100:5985/wsman -u:WinRM-Writer -p:123098 ipconfig` передать команду через winrs (-?) \
`winrs -r:https://192.168.3.100:5985/wsman -u:WinRM-Writer -p:123098 -ssl ipconfig` через https \
`pwsh -Command "Install-Module -Name PSWSMan"` установить модуль для использования в Linux системе

### Kerberos

`.\CheckMaxTokenSize.ps1 -Principals login -OSEmulation $true -Details $true` узнать размер токена пользователя в домене \
`Get-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\Kerberos\Parameters | select maxtokensize` максимальный размер токена на сервере \
`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\HTTP\Parameters` изменить размера, если заголовок пакета аутентификации превышает 16 Кб (из за большого кол-ва групп) \
`MaxFieldLength увеличить до 0000ffff (65535)` \
`MaxRequestBytes увеличить до 0000ffff (65535)`

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
# Atlassian

### Bitbucket
```PowerShell
$url = "https://github.com/AtlassianPS/BitbucketPS/archive/refs/heads/master.zip"
Invoke-RestMethod $url -OutFile $home\Downloads\BitbucketPS.zip
Expand-Archive -Path "$home\Downloads\BitbucketPS.zip" -OutputPath "$home\Downloads"
Copy-Item -Path "$home\Downloads\BitbucketPS-master\*" -Destination "$($env:PSModulePath.Split(";")[0])\PSBitBucket" -Recurse
Remove-Item "$home\Downloads\Bitbucket*" -Recurse -Force
```
`Import-Module PSBitBucket` \
`Get-Command -Module PSBitBucket` \
`Set-BitBucketConfigServer -Url $url -User username -Password password` установить конфигурацию сервера BitBucket \
`Get-BitBucketConfigServer` получить текущую конфигурацию сервера BitBucket \
`Get-Repositories` получить список всех репозиториев для текущей конфигурации сервера BitBucket \
`Get-ProjectKey` получить ключ проекта BitBucket \
`Get-BranchList -Repository pSyslog` список всех веток в репозитории \
`Get-Branch -Repository pSyslog -Branch main` получить информацию о конкретной ветке репозитория \
`Get-CommitMessage -Repository pSyslog -CommitHash $hash` получить сообщение коммита по его хэшу \
`Get-Commits -Repository pSyslog -Limit 10` список последних 10 коммитов в репозитории \
`Get-CommitsForBranch -Repository pSyslog -Branch main` список коммитов для конкретной ветки в репозитории

### Jira

`Install-Module JiraPS -Scope CurrentUser -Repository PSGallery -AllowClobber -Force` \
`Get-Command -Module JiraPS` \
`Get-JiraServerInfo` информация о сервере \
`Add-JiraFilterPermission` добавить разрешения для фильтра \
`Add-JiraGroupMember` добавить участника в группу \
`Add-JiraIssueAttachment` добавить вложения к задаче \
`Add-JiraIssueComment` добавить комментария к задаче \
`Add-JiraIssueLink` добавить ссылки на задачу \
`Add-JiraIssueWatcher` добавить наблюдателя к задаче \
`Add-JiraIssueWorklog` добавить рабочего журнала к задаче \
`Find-JiraFilter` поиск фильтра \
`Format-Jira` форматирование данных Jira \
`Get-JiraComponent` получение компонента проекта \
`Get-JiraConfigServer` получение конфигурации сервера Jira \
`Get-JiraField` получение поля Jira \
`Get-JiraFilter` получение фильтра \
`Get-JiraFilterPermission` получение разрешения фильтра \
`Get-JiraGroup` получение группы \
`Get-JiraGroupMember` получение участников группы \
`Get-JiraIssue` получение задачи \
`Get-JiraIssueAttachment` получение вложения задачи \
`Get-JiraIssueAttachmentFile` получение файла вложения задачи \
`Get-JiraIssueComment` получение комментария задачи \
`Get-JiraIssueCreateMetadata` получение метаданных создания задачи \
`Get-JiraIssueEditMetadata` получение метаданных редактирования задачи \
`Get-JiraIssueLink` получение ссылки задачи \
`Get-JiraIssueLinkType` получение типа ссылки задачи \
`Get-JiraIssueType` получение типа задачи \
`Get-JiraIssueWatcher` получение наблюдателя задачи \
`Get-JiraIssueWorklog` получение рабочего журнала задачи \
`Get-JiraPriority` получение приоритета задачи \
`Get-JiraProject` получение проекта \
`Get-JiraRemoteLink` получение удаленной ссылки \
`Get-JiraServerInformation` получение информации о сервере Jira \
`Get-JiraSession` получение сессии \
`Get-JiraUser` получение пользователя \
`Get-JiraVersion` получение версии проекта \
`Invoke-JiraIssueTransition` выполнение перехода задачи \
`Invoke-JiraMethod` выполнение метода Jira \
`Move-JiraVersion` перемещение версии проекта \
`New-JiraFilter` создание нового фильтра \
`New-JiraGroup` создание новой группы \
`New-JiraIssue` создание новой задачи \
`New-JiraSession` создание новой сессии \
`New-JiraUser` создание нового пользователя \
`New-JiraVersion` создание новой версии проекта \
`Remove-JiraFilter` удаление фильтра \
`Remove-JiraFilterPermission` удаление разрешения фильтра \
`Remove-JiraGroup` удаление группы \
`Remove-JiraGroupMember` удаление участника группы \
`Remove-JiraIssue` удаление задачи \
`Remove-JiraIssueAttachment` удаление вложения задачи \
`Remove-JiraIssueLink` удаление ссылки задачи \
`Remove-JiraIssueWatcher` удаление наблюдателя задачи \
`Remove-JiraRemoteLink` удаление удаленной ссылки \
`Remove-JiraSession` удаление сессии \
`Remove-JiraUser` удаление пользователя \
`Remove-JiraVersion` удаление версии проекта \
`Set-JiraConfigServer` установка конфигурации сервера Jira \
`Set-JiraFilter` установка фильтра \
`Set-JiraIssue` установка задачи \
`Set-JiraIssueLabel` установка метки задачи \
`Set-JiraUser` установка пользователя \
`Set-JiraVersion` установка версии проекта

### Confluence

`Install-Module ConfluencePS -Scope CurrentUser -Repository PSGallery -AllowClobber -Force` \
`Get-Command -Module ConfluencePS` \
`Add-ConfluenceAttachment` добавить вложения к странице \
`Add-ConfluenceLabel` добавить метки к странице \
`ConvertTo-ConfluenceStorageFormat` конвертация содержимого в формат хранения Confluence \
`ConvertTo-ConfluenceTable` конвертация данных в таблицу Confluence \
`Get-ConfluenceAttachment` получение вложения страницы \
`Get-ConfluenceAttachmentFile` получение файла вложения страницы \
`Get-ConfluenceChildPage` получение дочерних страниц \
`Get-ConfluenceLabel` получение меток страницы \
`Get-ConfluencePage` получение информации о странице \
`Get-ConfluenceSpace` получение информации о пространстве \
`Invoke-ConfluenceMethod` выполнение метода Confluence \
`New-ConfluencePage` создание новой страницы \
`New-ConfluenceSpace` создание нового пространства \
`Remove-ConfluenceAttachment` удаление вложения страницы \
`Remove-ConfluenceLabel` удаление метки со страницы \
`Remove-ConfluencePage` удаление страницы \
`Remove-ConfluenceSpace` удаление пространства \
`Set-ConfluenceAttachment` установка вложения страницы \
`Set-ConfluenceInfo` установка информации о странице \
`Set-ConfluenceLabel` установка метки страницы \
`Set-ConfluencePage` установка страницы

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
# PSAppDeployToolkit

### Install-DeployToolkit
```PowerShell
$githubRepository = "psappdeploytoolkit/psappdeploytoolkit"
$filenamePatternMatch = "PSAppDeployToolkit*.zip"
$psadtReleaseUri = "https://api.github.com/repos/$githubRepository/releases/latest"
$psadtDownloadUri = ((Invoke-RestMethod -Method GET -Uri $psadtReleaseUri).assets | Where-Object name -like $filenamePatternMatch ).browser_download_url
$zipExtractionPath = Join-Path $env:USERPROFILE "Downloads" "PSAppDeployToolkit"
$zipTempDownloadPath = Join-Path -Path $([System.IO.Path]::GetTempPath()) -ChildPath $(Split-Path -Path $psadtDownloadUri -Leaf)
## Download to a temporary folder
Invoke-WebRequest -Uri $psadtDownloadUri -Out $zipTempDownloadPath
## Remove any Zone.Identifier alternate data streams to unblock the file (if required)
Unblock-File -Path $zipTempDownloadPath
New-Item -Type Directory $zipExtractionPath
Expand-Archive -Path $zipTempDownloadPath -OutputPath $zipExtractionPath -Force
Write-Host ("File: {0} extracted to Path: {1}" -f $psadtDownloadUri, $zipExtractionPath) -ForegroundColor Yellow
Remove-Item $zipTempDownloadPath
```
### Deploy-Notepad-Plus-Plus

`$url_notepad = "https://github.com/notepad-plus-plus/notepad-plus-plus/releases/download/v8.6.6/npp.8.6.6.Installer.x64.exe"` \
`Invoke-RestMethod $url_notepad -OutFile "$home\Downloads\PSAppDeployToolkit\Toolkit\Files\npp.8.6.6.Installer.x64.exe"`
```PowerShell
'# Подключаем модуль PSAppDeployToolkit
Import-Module "$PSScriptRoot\AppDeployToolkit\AppDeployToolkitMain.ps1"
# Название приложения
$AppName = "Notepad++"
# Версия приложения
$AppVersion = "8.6.6"
# Путь к установщику Notepad++
$InstallerPath = "$PSScriptRoot\Files\npp.$AppVersion.Installer.x64.exe"
# Проверка существования установщика
If (-not (Test-Path $InstallerPath)) {
    Write-Host "Установщик Notepad++ не найден: $InstallerPath"
    Exit-Script -ExitCode 1
}
# Настройки установки Notepad++
$InstallerArguments = "/S /D=$ProgramFiles\Notepad++"
Function Install-Application {
    # Выводим сообщение о начале установки
    Show-InstallationWelcome -CloseApps "iexplore" -CheckDiskSpace -PersistPrompt
    # Запускаем установку
    Execute-Process -Path $InstallerPath -Parameters $InstallerArguments -WindowStyle Hidden -IgnoreExitCodes "3010"
    # Выводим сообщение об успешной установке
    Show-InstallationPrompt -Message "Установка $AppName завершена." -ButtonRightText "Закрыть" -Icon Information -NoWait
    # Завершаем процесс установки
    Exit-Script -ExitCode $AppDependentExitCode
}
Install-Application' | Out-File "$home\Downloads\PSAppDeployToolkit\Toolkit\Deploy-Application.ps1" -Encoding unicode
```
`powershell -File "$home\Downloads\PSAppDeployToolkit\Toolkit\Deploy-Application.ps1"`

### Uninstall-Notepad-Plus-Plus
```PowerShell
'Import-Module "$PSScriptRoot\AppDeployToolkit\AppDeployToolkitMain.ps1"
$AppName = "Notepad++"
$UninstallerPath = "C:\Program Files\Notepad++\uninstall.exe"
If (-not (Test-Path $UninstallerPath)) {
    Write-Host "Деинсталлятор Notepad++ не найден: $UninstallerPath"
    Exit-Script -ExitCode 1
}
Function Uninstall-Application {
    Show-InstallationWelcome -CloseApps "iexplore" -CheckDiskSpace -PersistPrompt
    Execute-Process -Path $UninstallerPath -Parameters "/S" -WindowStyle Hidden -IgnoreExitCodes "3010"
    Show-InstallationPrompt -Message "Программа $AppName удалена." -ButtonRightText "Закрыть" -Icon Information -NoWait
    Exit-Script -ExitCode $AppDependentExitCode
}
Uninstall-Application' | Out-File "$home\Downloads\PSAppDeployToolkit\Toolkit\Deploy-Application.ps1" -Encoding unicode
```
`powershell -File "$home\Downloads\PSAppDeployToolkit\Toolkit\Deploy-Application.ps1"`

### Deploy-WinSCP
```PowerShell
$PSAppDeployToolkit = "$home\Downloads\PSAppDeployToolkit\"
$version = "6.3.3"
$url_winscp = "https://cdn.winscp.net/files/WinSCP-$version.msi?secure=P2HLWGKaMDigpDQw-H9BgA==,1716466173"
$WinSCP_Template = Get-Content "$PSAppDeployToolkit\Examples\WinSCP\Deploy-Application.ps1" # читаем пример конфигурации для WinSCP
$WinSCP_Template_Latest = $WinSCP_Template -replace "6.3.2","$version" # обновляем версию на актуальную
$WinSCP_Template_Latest > "$PSAppDeployToolkit\Toolkit\Deploy-Application.ps1" # заменяем скрипт развертывания 
Invoke-RestMethod $url_winscp -OutFile "$PSAppDeployToolkit\Toolkit\Files\WinSCP-$version.msi" # загружаем msi-пакет
powershell -File "$PSAppDeployToolkit\Toolkit\Deploy-Application.ps1" # запускаем установку
```
# DSC

`Import-Module PSDesiredStateConfiguration` \
`Get-Command -Module PSDesiredStateConfiguration` \
`(Get-Module PSDesiredStateConfiguration).ExportedCommands` \
`Get-DscLocalConfigurationManager`

`Get-DscResource` \
`Get-DscResource -Name File -Syntax` [синтаксис](https://learn.microsoft.com/ru-ru/powershell/dsc/reference/resources/windows/fileresource?view=dsc-1.1)

`Ensure = Present` настройка должна быть включена (каталог должен присутствовать, процесс должен быть запущен, если нет – создать, запустить) \
`Ensure = Absent` настройка должна быть выключена (каталога быть не должно, процесс не должен быть запущен, если нет – удалить, остановить)
```PowerShell
Configuration TestConfiguraion
{
    Ctrl+Space
}

Configuration DSConfigurationProxy 
{
    Node vproxy-01 
    {
        File CreateDir
        {
            Ensure = "Present"
            Type = "Directory"
            DestinationPath = "C:\Temp"
        }
        Service StopW32time
        {
            Name = "w32time"
            State = "Stopped"` Running
        }
    WindowsProcess RunCalc
        {
            Ensure = "Present"
            Path = "C:\WINDOWS\system32\calc.exe"
            Arguments = ""
        }
        Registry RegSettings
        {
            Ensure = "Present"
            Key = "HKEY_LOCAL_MACHINE\SOFTWARE\MySoft"
            ValueName = "TestName"
            ValueData = "TestValue"
            ValueType = "String"
        }
#		WindowsFeature IIS
#       {
#            Ensure = "Present"
#            Name = "Web-Server"
#       }
    }
}
```
`$Path = (DSConfigurationProxy).DirectoryName` \
`Test-DscConfiguration -Path $Path | select *` ResourcesInDesiredState - уже настроено, ResourcesNotInDesiredState - не настроено (не соответствует) \
`Start-DscConfiguration -Path $Path` \
`Get-Job` \
`$srv = "vproxy-01"` \
`Get-Service -ComputerName $srv | ? name -match w32time # Start-Service` \
`icm $srv {Get-Process | ? ProcessName -match calc} | ft # Stop-Process -Force` \
`icm $srv {ls C:\ | ? name -match Temp} | ft` rm`
```PowerShell
Configuration InstallPowerShellCore {
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    Node localhost {
        Script InstallPowerShellCore {
            GetScript = {
                return @{
                    GetScript = $GetScript
                }
            }
            SetScript = {
        [string]$url = $(Invoke-RestMethod https://api.github.com/repos/PowerShell/PowerShell/releases/latest).assets.browser_download_url -match "win-x64.zip"
                $downloadPath = "$home\Downloads\PowerShell.zip"
                $installPath = "$env:ProgramFiles\PowerShell\7"
                Invoke-WebRequest -Uri $url -OutFile $downloadPath
                Expand-Archive -Path $downloadPath -DestinationPath $installPath -Force
            }
            TestScript = {
                return Test-Path "$env:ProgramFiles\PowerShell\7\pwsh.exe"
            }
        }
    }
}
```
`$Path = (InstallPowerShellCore).DirectoryName` \
`Test-DscConfiguration -Path $Path` \
`Start-DscConfiguration -Path $path -Wait -Verbose` \
`Get-Job`

# GigaChat

[Developers chat](https://developers.sber.ru/gigachat/login)

- Установка сертификатов:

`Invoke-WebRequest "https://gu-st.ru/content/lending/russian_trusted_root_ca_pem.crt" -OutFile "$home\Downloads\russian_trusted_root_ca.cer"` скачать сертификат минцифры \
`Invoke-WebRequest "https://gu-st.ru/content/lending/russian_trusted_sub_ca_pem.crt" -OutFile "$home\Downloads\russian_trusted_sub_ca.cer"` \
`Import-Certificate -FilePath "$home\Downloads\russian_trusted_root_ca.cer" -CertStoreLocation "Cert:\CurrentUser\Root"` установить сертификат минцифры \
`Import-Certificate -FilePath "$home\Downloads\russian_trusted_sub_ca.cer" -CertStoreLocation "Cert:\CurrentUser\CA"`

- 2. Авторизация по Sber ID и генерация новых авторизационных данных для получения токена: [Developers](https://developers.sber.ru/studio) (время жизни 30 минут)

- 3. Формирование авторизационных данных в формате Base64 из Client ID и Client Secret:
```PowerShell
$Client_ID     = "7e6d2f9f-825e-49b7-98f4-62fbb7506427" # [System.Guid]::Parse("7e6d2f9f-825e-49b7-98f4-62fbb7506427")
$Client_Secret = "c35113ee-6757-47ba-9853-ea1d0d9db1ef" # [System.Guid]::Parse("c35113ee-6757-47ba-9853-ea1d0d9db1ef")
$Client_Join   = $Client_ID+":"+$Client_Secret # объединяем два UUID в одну строку, разделяя их символом ':'
$Bytes         = [System.Text.Encoding]::UTF8.GetBytes($Client_Join) # преобразуем строку в массив байт
$Cred_Base64   = [Convert]::ToBase64String($Bytes) # кодируем байты в строку Base64
```
- 4. Получение токена:

`$Cred_Base64   = "N2U2ZDJmOWYtODI1ZS00OWI3LTk4ZjQtNjJmYmI3NTA2NDI3OmIyYzgwZmZmLTEzOGUtNDg1Mi05MjgwLWE2MGI4NTc0YTM2MQ=="` \
`$UUID = [System.Guid]::NewGuid()` генерируем UUID для журналирования входящих вызовов и разбора инцидентов
```PowerShell
$url = "https://ngw.devices.sberbank.ru:9443/api/v2/oauth"
$headers = @{
    "Authorization" = "Basic $Cred_Base64"
    "RqUID" = "$UUID"
    "Content-Type" = "application/x-www-form-urlencoded"
}
$body = @{
    scope = "GIGACHAT_API_PERS"
}
$GIGA_TOKEN = $(Invoke-RestMethod -Uri $url -Method POST -Headers $headers -Body $body).access_token
```
- 5. Параметры:
```PowerShell
[string]$content = "Посчитай сумму чисел: 22+33"
[string]$role = "user" # роль автора сообщения (user/assistant/system)
[float]$temperature = 0.7 # температура выборки в диапазоне от 0 до 2. Чем выше значение, тем более случайным будет ответ модели.
[float]$top_p = 0.1 # используется как альтернатива temperature и изменяется в диапазоне от 0 до 1. Задает вероятностную массу токенов, которые должна учитывать модель. Так, если передать значение 0.1, модель будет учитывать только токены, чья вероятностная масса входит в верхние 10%.
[int64]$n = 1 # количество вариантов ответов (1..4), которые нужно сгенерировать для каждого входного сообщения
[int64]$max_tokens = 512 # максимальное количество токенов, которые будут использованы для создания ответов
[boolean]$stream = $false # передавать сообщения по частям в потоке
```
- 6. Составление запросов:
```PowerShell
$url = "https://gigachat.devices.sberbank.ru/api/v1/chat/completions"
$headers = @{
    "Authorization" = "Bearer $GIGA_TOKEN"
    "Content-Type" = "application/json"
}

$(Invoke-RestMethod -Uri "https://gigachat.devices.sberbank.ru/api/v1/models" -Headers $headers).data # список доступных моделей

$body = @{
    model = "GigaChat:latest"
    messages = @(
        @{
            role = $role
            content = $content
        }
    )
    temperature = $temperature
  n = $n
  max_tokens = $max_tokens
  stream = $stream
} | ConvertTo-Json
$Request = Invoke-RestMethod -Method POST -Uri $url -Headers $headers -Body $body
$Request.choices.message.content
```
## Curl

- Установка сертификатов в Ubuntu:

`wget https://gu-st.ru/content/lending/russian_trusted_root_ca_pem.crt` \
`wget https://gu-st.ru/content/lending/russian_trusted_sub_ca_pem.crt` \
`mkdir /usr/local/share/ca-certificates/russian_trusted` \
`cp russian_trusted_root_ca_pem.crt russian_trusted_sub_ca_pem.crt /usr/local/share/ca-certificates/russian_trusted` \
`update-ca-certificates -v` \
`wget -qS --spider --max-redirect=0 https://www.sberbank.ru`

- Получение токена:
- 
```Bash
Cred_Base64="N2U2ZDJmOWYtODI1ZS00OWI3LTk4ZjQtNjJmYmI3NTA2NDI3OmIyYzgwZmZmLTEzOGUtNDg1Mi05MjgwLWE2MGI4NTc0YTM2MQ=="
UUID=$(uuidgen)
GIGA_TOKEN=$(curl -s --location --request POST "https://ngw.devices.sberbank.ru:9443/api/v2/oauth" \
--header "Authorization: Basic $Cred_Base64" \
--header "RqUID: $UUID" \
--header "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode 'scope=GIGACHAT_API_PERS' | jq -r .access_token)
```
`curl -s --location "https://gigachat.devices.sberbank.ru/api/v1/models" --header "Authorization: Bearer $GIGA_TOKEN" | jq .` для проверки

- Составление запроса:
- 
```Bash
request=$(curl -s https://gigachat.devices.sberbank.ru/api/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $GIGA_TOKEN" \
  -d '{
  "model": "GigaChat:latest",
  "messages": [
        {
            "role": "user",
            "content": "Когда уже ИИ захватит этот мир?"
        }
    ],
  "temperature": 0.7
}')
echo $request | jq -r .choices[].message.content
```

# YandexGPT

- Получить OAuth-Token:

[Create AIM Token](https://cloud.yandex.ru/ru/docs/iam/operations/iam-token/create) время жизни IAM-токена не больше 12 часов \
`yandexPassportOauthToken="y0_AgAAAAAGaLFLAATuwQAAAAD3xtRLQE4hvlazQ5euKO43XXXXXXXXXXX"` для bash \
`$yandexPassportOauthToken = "y0_AgAAAAAGaLFLAATuwQAAAAD3xtRLQE4hvlazQ5euKO43XXXXXXXXXXX"` для PowerShell

- Обменять OAuth-Token на IAM-Token:

`IAM_TOKEN=$(curl -s -d "{\"yandexPassportOauthToken\":\"$yandexPassportOauthToken\"}" "https://iam.api.cloud.yandex.net/iam/v1/tokens" | jq -r .iamToken)` \
`$IAM_TOKEN = $(Invoke-RestMethod -Method POST -Uri "https://iam.api.cloud.yandex.net/iam/v1/tokens" -Body $(@{yandexPassportOauthToken = "$yandexPassportOauthToken"} | ConvertTo-Json -Compress)).iamToken`

- Получить FOLDER_ID:


```Bash
CLOUD_ID=$(curl -s -H "Authorization: Bearer $IAM_TOKEN" https://resource-manager.api.cloud.yandex.net/resource-manager/v1/clouds | jq -r .clouds[].id) # получить cloud id
curl -s --request GET -H "Authorization: Bearer $IAM_TOKEN" https://resource-manager.api.cloud.yandex.net/resource-manager/v1/folders -d "{\"cloudId\": \"$CLOUD_ID\"}" # получить список директорий в облаке
curl -s --request POST -H "Authorization: Bearer $IAM_TOKEN" https://resource-manager.api.cloud.yandex.net/resource-manager/v1/folders -d "{\"cloudId\": \"$CLOUD_ID\", \"name\": \"test\"}" # создать директорию в облаке
FOLDER_ID=$(curl -s --request GET -H "Authorization: Bearer $IAM_TOKEN" https://resource-manager.api.cloud.yandex.net/resource-manager/v1/folders -d '{"cloudId": "b1gf9n6heihqj0pt5piu"}' | jq -r '.folders[] | select(.name == "test") | .id') # забрать id директории
```

```PowerShell
$CLOUD_ID = $(Invoke-RestMethod -Method Get -Uri "https://resource-manager.api.cloud.yandex.net/resource-manager/v1/clouds" -Headers @{"Authorization"="Bearer $IAM_TOKEN"; "Content-Type"="application/json"}).clouds.id
$FOLDER_ID = $(Invoke-RestMethod -Method Get -Uri "https://resource-manager.api.cloud.yandex.net/resource-manager/v1/folders" -Headers @{"Authorization"="Bearer $IAM_TOKEN"; "Content-Type"="application/json"} -Body (@{"cloudId"= $CLOUD_ID} | ConvertTo-Json)).folders | Where-Object name -eq test | Select-Object -ExpandProperty id
```

- Составление запроса:

```Bash
model="gpt://$FOLDER_ID/yandexgpt/latest" # https://cloud.yandex.ru/ru/docs/yandexgpt/concepts/models
body=$(cat <<EOF
{
  "modelUri": "$model",
  "completionOptions": {
    "stream": false,
    "temperature": 0.6,
    "maxTokens": 2000
  },
  "messages": [
    {
      "role": "user",
      "text": "Посчитай сумму 22+33"
    }
  ]
}
EOF)
curl --request POST \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $IAM_TOKEN" \
  -H "x-folder-id: $FOLDER_ID" \
  -d "$body" \
  "https://llm.api.cloud.yandex.net/foundationModels/v1/completion"
```
```PowerShell
$model = "gpt://$FOLDER_ID/yandexgpt/latest"
$body = @"
{
  "modelUri": "$model",
  "completionOptions": {
    "stream": false,
    "temperature": 0.6,
    "maxTokens": 2000
  },
  "messages": [
    {
      "role": "user",
      "text": "Посчитай сумму 22+33"
    }
  ]
}
"@
Invoke-RestMethod -Method POST -Uri "https://llm.api.cloud.yandex.net/foundationModels/v1/completion" -Headers @{"Content-Type"="application/json"; "Authorization"="Bearer $IAM_TOKEN"; "x-folder-id"="$FOLDER_ID"} -Body $body
```

# SuperAGI

[Source](https://github.com/TransformerOptimus/SuperAGI) \
[Playground generate](https://models.superagi.com/playground/generate) \
[API Doc (exaples)](https://documenter.getpostman.com/view/30119783/2s9YR3cFJG)
```Bash
SUPERAGI_API_KEY="31f72164129XXXXX"
prompt="посчитай сумму 22+33, дай только ответ без лишнего текста"
request=$(curl -s -X POST 'https://api.superagi.com/v1/generate/65437cbf227a4018516ad1ce' \
-H 'Content-Type: application/json' \
-H "Authorization: Bearer $SUPERAGI_API_KEY" \
-d '{
  "prompt": ["$prompt"],
  "max_tokens": 500,
  "temperature": 0.9,
  "top_p": 0.15,
  "repetition_penalty": 0,
  "best_of": 1.05,
  "top_k": 50,
  "stream": false
}')
echo $request | sed "s/data: //" | jq -r .choices[].text
```
```PowerShell
$SUPERAGI_API_KEY = "31f72164129XXXXX"
$prompt = "посчитай сумму 22+33, дай только ответ без лишнего текста"
$request = Invoke-RestMethod -Method Post -Uri 'https://api.superagi.com/v1/generate/65437cbf227a4018516ad1ce' -Headers @{
    'Content-Type' = 'application/json'
    'Authorization' = "Bearer $SUPERAGI_API_KEY"
} -Body (@{
    prompt = @($prompt)
    max_tokens = 500
    temperature = 0.9
    top_p = 0.15
    repetition_penalty = 0
    best_of = 1.05
    top_k = 50
    stream = $false
} | ConvertTo-Json)
$($request -replace "^data: " | ConvertFrom-Json).choices.text
```

# Replicate

[API curl examples](https://replicate.com/stability-ai/stable-diffusion/examples?input=http)
```Bash
REPLICATE_API_TOKEN="r8_STyeUNXiGonkLfxE1FSKaqll26lXXXXXXXXXX"
prompt="Жираф в полоску зебры"
request=$(curl -s -X POST \
  -H "Authorization: Token $REPLICATE_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d $'{
    "version": "ac732df83cea7fff18b8472768c88ad041fa750ff7682a21affe81863cbe77e4",
    "input": {
      "prompt": "$prompt"
    }
  }' \
  https://api.replicate.com/v1/predictions)
request_url=$(echo $request | jq -r .urls.get)
response_status=$(curl -s -H "Authorization: Token $REPLICATE_API_TOKEN" $request_url | jq -r .status)
while [[ $response_status != succeeded ]]; do
    response_status=$(curl -s -H "Authorization: Token $REPLICATE_API_TOKEN" $request_url | jq -r .status)
done
curl -s -H "Authorization: Token $REPLICATE_API_TOKEN" $request_url | jq -r .output[]
```
```PowerShell
$REPLICATE_API_TOKEN = "r8_STyeUNXiGonkLfxE1FSKaqll26lXXXXXXXXXX"
$prompt = "Жираф в полоску зебры"
$body = @{
   version = "ac732df83cea7fff18b8472768c88ad041fa750ff7682a21affe81863cbe77e4"
   input = @{
       prompt = $prompt
   }
} | ConvertTo-Json
$headers = @{
   "Authorization" = "Token $REPLICATE_API_TOKEN"
   "Content-Type" = "application/json"
}
$request = Invoke-RestMethod -Uri "https://api.replicate.com/v1/predictions" -Method POST -Body $body -Headers $headers
$response = Invoke-RestMethod $($request.urls.get) -Headers @{"Authorization" = "Token $REPLICATE_API_TOKEN"}
while ($response.status -ne "succeeded") {
    $response = Invoke-RestMethod $($request.urls.get) -Headers @{"Authorization" = "Token $REPLICATE_API_TOKEN"}
}
$response.output
```

# Google-Filter

`https://www.google.com/search?q=the+rookie+2018+imdb` формат url-запроса поиска с пробелами \
`https://www.google.com/search?q=the+rookie+2018+site:imdb.com` поиск по сайту \
`https://www.google.com/search?q=the+rookie+intitle:index.of+"last modified"+(mkv|avi)` искать страницы, на которых указано "last modified" (последние изменения), заголовок страницы через расширенный оператор поиска (все перечисленные слова должны встречаться в заголовке) содержит слово "index.of" (указывает на директорию на веб-сервере, которая содержит список файлов) и искать файлы с расширениями .mkv или (|) .avi \
`https://www.google.com/search?q=the+rookie+2018+filetype:torrent` \
`инструкция gopro hero 11 filetype:pdf` искать сразу документ (на странице .pdf или загрузка) \
`"действия/глаголы, утвержденные для использования в командлетах"` искать по фразе целиком, без разбиения на отдельные слова \
`"ягуар скорость -животное -xe -xj"` узнаем скорость Ягуара, исключаем животное и модели автомобиля \
`"intitle:лучшие фильмы 2023"` запрос ищет страницы, заголовки (title HTML документа) которых содержат слова "лучшие", "фильмы" и "2023" (все слова должны быть в заголовке) \
`"allintitle:лучшие фильмы 2023"` запрос ищет страницы, заголовки (title HTML документа) которых содержат слова "лучшие", "фильмы" или "2023" (одно из) \
`"intext:telegram бот powershell"` поиск страниц, содержащих указанное ключевое слово в тексте страницы (а не только в заголовке) \
`"inurl:lifailon"` поиск страниц, в URL которых содержится указанное ключевое слово \
`intitle:index.of "game of thrones" mkv daterange:2010..2015` фильтрация по дате изменения, оператор позволяет задать диапазон дат в формате YYYYMMDD..YYYYMMDD \
`intitle:index.of "game of thrones" mkv after:2015` ограничить результаты поиска файлов, измененных до (before) или после (after) указанной даты \
`intitle:index.of "game of thrones" mkv from:2010 to:2015` фильтрация по диапазону дат \
`https://www.google.com/search?q=the-rookie-2018+site:imdb.com&btnI` редирект на первый url

# Google-API

### Google-Translate
```PowerShell
$Key = "<TOKEN_API>" # получить токен: https://console.cloud.google.com/apis/credentials
$Text = "You can see in the right corner how long each translation request takes (this does not depend on the amount of text being transferred)."
$LanguageTarget = "RU"
$LanguageSource = "EN"
$url = "https://translation.googleapis.com/language/translate/v2?key=$key"
$Header = @{
    "Content-Type" = "application/json"
}
$Body = @{
    "q" = "$Text"
    "target" = "$LanguageTarget"
    "source" = "$LanguageSource"
} | ConvertTo-Json
$WebClient = New-Object System.Net.WebClient
foreach ($key in $Header.Keys) {
    $WebClient.Headers.Add($key, $Header[$key])
}
$Response = $WebClient.UploadString($url, "POST", $Body) | ConvertFrom-Json
$Response.data.translations.translatedText
```
### Google-Search
```PowerShell
$Key = "<TOKEN_API>" # получить токен: https://developers.google.com/custom-search/v1/overview?hl=ru (пользовательский поиск JSON API предоставляет 100 поисковых запросов в день бесплатно)
$cx = "35c78340f49eb474a" # создать поисковую систему https://programmablesearchengine.google.com/controlpanel/all
$Query = "как создать бота discord"
$Lang = "ru"
$Num = 10
$Start = 0
$response = Invoke-RestMethod "https://www.googleapis.com/customsearch/v1?q=$Query&key=$Key&cx=$cx&lr=lang_$Lang&num=$Num&$start=$Start"
$response.items | Select-Object title,snippet,displayLink,link | Format-List
```
# RapidAPI

[Google-Search72](https://rapidapi.com/ru/neoscrap-net/api/google-search72)
```PowerShell
$Key = "<TOKEN_API>"
$headers=@{}
$headers.Add("X-RapidAPI-Key", "$Key")
$headers.Add("X-RapidAPI-Host", "google-search72.p.rapidapi.com")
$query = "как создать бота discord"
$response = Invoke-RestMethod "https://google-search72.p.rapidapi.com/search?q=$query%20gitgub&gl=us&lr=lang_ru&num=20&start=0" -Method GET -Headers $headers
$response.items | Select-Object title,snippet,displayLink,link | Format-List
```
### IMDb

[IMDb8](https://rapidapi.com/apidojo/api/imdb8)
```PowerShell
$key = "<TOKEN_API>" # 500 запросов в месяц
$query="Break"
$headers=@{}
$headers.Add("X-RapidAPI-Key", "$key")
$headers.Add("X-RapidAPI-Host", "imdb8.p.rapidapi.com")
$response = Invoke-RestMethod "https://imdb8.p.rapidapi.com/title/find?q=$query" -Method GET -Headers $headers
$response.results | select title,titletype,year,runningTimeInMinutes,id | Format-Table
"https://www.imdb.com$($response.results.id[0])"
$response.results.principals # актеры
$response.results.image
```
### MoviesDatabase

[MoviesDatabase](https://rapidapi.com/SAdrian/api/moviesdatabase)
```PowerShell
$key = "<TOKEN_API>"
$imdb_id = "tt0455275"
$headers=@{}
$headers.Add("X-RapidAPI-Key", "$key")
$headers.Add("X-RapidAPI-Host", "moviesdatabase.p.rapidapi.com")
$response = Invoke-RestMethod "https://moviesdatabase.p.rapidapi.com/titles/$imdb_id" -Method GET -Headers $headers
$response.results
```
# TMDB

[Developer TMDB](https://developer.themoviedb.org/reference/intro/getting-started)
```PowerShell
$TOKEN = "548e444e7812575caa0a7eXXXXXXXXXX"
$Endpoint = "search/tv" # поиск сериала (tv) и фильма (movie) по названию
$Query = "зимородок"
$url = $("https://api.themoviedb.org/3/$Endpoint"+"?api_key=$TOKEN&query=$Query")
$(Invoke-RestMethod -Uri $url -Method Get).results
$id = $(Invoke-RestMethod -Uri $url -Method Get).results.id # забрать id сериала (210865) https://www.themoviedb.org/tv/210865

$Endpoint = "tv/$id" # получение информации о сериале по его ID
$url = $("https://api.themoviedb.org/3/$Endpoint"+"?api_key=$TOKEN")
$(Invoke-RestMethod -Uri $url -Method Get) # список сезонов (.seasons), количество эпизодов (.seasons.episode_count)

(Invoke-RestMethod -Uri "https://api.themoviedb.org/3/tv/$id/season/2?api_key=$Token" -Method Get).episodes # вывести 2 сезон
Invoke-RestMethod -Uri "https://api.themoviedb.org/3/tv/$id/season/2/episode/8?api_key=$Token" -Method Get # вывести 8 эпизод
```
# OMDb

Получение API ключа по [email](https://www.omdbapi.com)

`$API_KEY = "XXXXXXXX"` \
`$IMDb_ID = "tt7587890"` \
`curl -s "https://omdbapi.com/?apikey=$($API_KEY)&i=$($IMDb_ID)" | jq .` \
`curl -s "https://omdbapi.com/?apikey=$($API_KEY)&i=$($IMDb_ID)" | ConvertFrom-Json` \
`Invoke-RestMethod "https://omdbapi.com/?apikey=$($API_KEY)&s=The Rookie"` \
`Invoke-RestMethod "https://omdbapi.com/?apikey=$($API_KEY)&t=The Rookie"` поиск по Title \
`Invoke-RestMethod "https://omdbapi.com/?apikey=$($API_KEY)&t=The Rookie&y=1990"` поиск по Title и году выхода \
`Invoke-RestMethod "https://omdbapi.com/?apikey=$($API_KEY)&t=The Rookie&type=movie"` поиск только фильма (movie) или сериала (series) \
`$(Invoke-RestMethod "https://omdbapi.com/?apikey=$($API_KEY)&s=The Rookie").Search` поиск всех совпадений (фильмы и сериалы)

# ivi

[ivi api doc](https://ask.ivi.ru/knowledge-bases/10/articles/51697-dokumentatsiya-dlya-api-ivi)

`Invoke-RestMethod https://api.ivi.ru/mobileapi/categories` список категорий и жанров (genres/meta_genres) \
`Invoke-RestMethod https://api.ivi.ru/mobileapi/collections` подборки

`(Invoke-RestMethod "https://api.ivi.ru/mobileapi/search/v7/?query=zimorodok").result.seasons.number` кол-во сезонов \
`(Invoke-RestMethod "https://api.ivi.ru/mobileapi/search/v7/?query=zimorodok").result.seasons[1].episode_count` кол-во серий во втором сезоне \
`(Invoke-RestMethod "https://api.ivi.ru/mobileapi/search/v7/?query=zimorodok").result.seasons[1].ivi_release_info.date_interval_min` дата выхода следующей серии \
`(Invoke-RestMethod "https://api.ivi.ru/mobileapi/search/v7/?query=zimorodok").result.kp_rating` рейтинг в Кинопоиск (8.04)

`$id = (Invoke-RestMethod "https://api.ivi.ru/mobileapi/search/v7/?query=zimorodok").result.kp_id` получить id в Кинопоиск (5106881) \
`id=$(curl -s https://api.ivi.ru/mobileapi/search/v7/?query=zimorodok | jq .result[].kp_id)` получить id в Кинопоиск

# Kinopoisk

```Bash
id=5106881
get=$(curl -s https://www.kinopoisk.ru/film/$id/episodes/)
printf "%s\n" "${get[@]}" | grep -A 1 "Сезон 2" | grep "эпизодов" | sed -r "s/^.+\: //" # количество эпиздовод во втором сезоне
```

[Получить токен](https://t.me/kinopoiskdev_bot) \
[Документация по API в формате OpenAPI](https://kinopoisk.dev/documentation)

`GET /v1.4/movie/{id}` поиск по id
```PowerShell
$id = 5106881
$API_KEY = "ZYMNJJA-0J8MNPN-PB4N7R7-XXXXXXX"

$Header = @{
    "accept" = "application/json"
    "X-API-KEY" = "$API_KEY"
}
$irm = Invoke-RestMethod "https://api.kinopoisk.dev/v1.4/movie/$id" -Method GET -Headers $Header
$irm.rating.kp # рейтинг в Кинопоиск (8,079)
$irm.seasonsInfo # количество сезонов и эпизодов в них
```
```Bash
id=5106881
API_KEY="ZYMNJJA-0J8MNPN-PB4N7R7-XXXXXXX"
get=$(curl -s -X GET \
  "https://api.kinopoisk.dev/v1.4/movie/$id" \
  -H "accept: application/json" \
  -H "X-API-KEY: $API_KEY")
echo $get | jq .rating.kp # рейтинг в Кинопоиск (8,079)
echo $get | jq .seasonsInfo[1].episodesCount # количество эпизодов во втором [1] сезоне (6)
```
`GET /v1.4/movie/search`
```Bash
query="zimorodok"
page=1 # кол-во страниц для выборки
limit=1 # кол-во элементов на странице
curl -s -X GET \
  "https://api.kinopoisk.dev/v1.4/movie/search?page=$page&limit=$limit&query=$query" \
  -H "accept: application/json" \
  -H "X-API-KEY: $API_KEY" | jq .

limit=5
request=$(curl -s -X GET \
  "https://api.kinopoisk.dev/v1.4/movie/search?page=$page&limit=$limit&query=%D0%B7%D0%B8%D0%BC%D0%BE%D1%80%D0%BE%D0%B4%D0%BE%D0%BA" \
  -H "accept: application/json" \
  -H "X-API-KEY: $API_KEY" | jq .)
echo $request | jq '.docs[] | select(.year == 2022)' # отфильтровать вывод по году выхода
```
```PowerShell
$API_KEY = "ZYMNJJA-0J8MNPN-PB4N7R7-XXXXXXX"
$page = 1
$limit = 5
$query = "%D0%B7%D0%B8%D0%BC%D0%BE%D1%80%D0%BE%D0%B4%D0%BE%D0%BA"
$request = Invoke-RestMethod -Uri "https://api.kinopoisk.dev/v1.4/movie/search?page=$page&limit=$limit&query=$query" -Headers @{"accept"="application/json"; "X-API-KEY"="$API_KEY"}
$request.docs | Where-Object year -eq 2022
```
### UrlCode
```PowerShell
function Get-PercentEncode ($str) {
   $bytes = [System.Text.Encoding]::UTF8.GetBytes($str)
   ($bytes | ForEach-Object { "{0:X2}" -f $_ }) -join '%' -replace "^","%"
}
Get-PercentEncode "зимородок"
```
```PowerShell
function Get-UrlEncode($str) {
   [System.Web.HttpUtility]::UrlEncode($str)
}
UrlEncode "зимородок"
```
```Bash
percent-encode() {
  str=$1
    echo -n "$1" | iconv -t utf8 | od -An -tx1 | tr ' ' % | tr -d '\n'
}
percent-encode "зимородок"
```
```PowerShell
function Get-UrlDecode($encoded) {
    [System.Uri]::UnescapeDataString($encoded)
}
Get-UrlDecode "%D0%B7%D0%B8%D0%BC%D0%BE%D1%80%D0%BE%D0%B4%D0%BE%D0%BA"
```
```Bash
percent-decode() {
    encoded=$1
    local url_encoded="${1//+/ }"
    printf '%b' "${url_encoded//%/\\x}"
}
percent-decode "%D0%B7%D0%B8%D0%BC%D0%BE%D1%80%D0%BE%D0%B4%D0%BE%D0%BA"
```
### KinopoiskApiUnofficial

Бесплатно 500 запросов в сутки. [Swagger documentation](https://kinopoiskapiunofficial.tech/documentation/api)
```PowerShell
API_KEY="828ec96a-f45d-4e3d-84b1-XXXXXXXXXXXX"
$headers = @{
    "accept" = "application/json"
    "X-API-KEY" = "$API_KEY"
}
Invoke-RestMethod -Uri 'https://kinopoiskapiunofficial.tech/api/v2.2/films/1142153' -Headers $headers
```
`curl -s "https://kinopoiskapiunofficial.tech/api/v2.2/films/1142153" -H "accept: application/json" -H "X-API-KEY: $API_KEY" | jq .`

### Kinobox

`$url = "https://www.kinopoisk.ru/film/694051"` \
`$kp_id = $url -replace ".+/"` \
`https://kinomix.web.app/#694051` \
`curl -s -X GET "https://kinobox.tv/api/players/main?kinopoisk=$kp_id" -H "accept: application/json"` поиск по id Кинопоиск \
`curl -s -X GET "https://kinobox.tv/api/players/main?imdb=tt2293640" -H "accept: application/json"` поиск по id IMDb \
`curl -s -X GET "https://kinobox.tv/api/players/main?title=minions" -H "accept: application/json"` поиск основных плееров по названию \
`curl -s -X GET "https://kinobox.tv/api/players/all?title=minions" -H "accept: application/json"` поиск всех плееров \
`curl -s -X GET "https://kinobox.tv/api/popular/films" -H "accept: application/json"` популярные фильмы \
`curl -s -X GET "https://kinobox.tv/api/popular/series" -H "accept: application/json"` популярные сериалы

# VideoCDN

[API](https://github.com/notssh/videocdn-api) \
[Source](https://github.com/API-Movies/videocdn) \
[API JSON](https://api-movies.github.io/videocdn/index.json)
```PowerShell
$kp_id = 5106881
$token = "YfTWH2p3Mai7ziqDoGjS3yXXXXXXXXXX"
$ep = "tv-series"
$(Invoke-RestMethod $("https://videocdn.tv/api/$ep"+"?api_token=$token&field=kinopoisk_id&query=$kp_id")).data.episodes | Where-Object season_num -eq 2 | Select-Object @{Name="Episode"; Expression={$_.num}}, @{Name="Voice"; Expression={$_.media.translation.title}} # отфильтровать серии по второму сезону и отобразить все озвучки к сериям
```
```Bash
kp_id=5106881
token="YfTWH2p3Mai7ziqDoGjS3yXXXXXXXXXX"
ep="tv-series"
curl -s "https://videocdn.tv/api/$ep?api_token=$token&field=kinopoisk_id&query=$kp_id" | jq ".data[].episodes | length" # количество серий
curl -s "https://videocdn.tv/api/$ep?api_token=$token&field=kinopoisk_id&query=$kp_id" | jq ".data[].episodes[] | select(.season_num == 2) | {episode: .ru_title, voice: .media[].translation.title}" # отфильтровать параметры вывода
```
# Telegram

@BotFather (https://t.me/BotFather) /newbot

Format: `https://api.telegram.org/bot<token>/<endpoint>`

[getupdates](https://core.telegram.org/bots/api#getupdates)
```PowerShell
function Get-FromTelegram {
    param (
        $Token = "687...:AAF...",
        [switch]$Date,
        [switch]$Last,
        [switch]$ChatID
    )
    $endpoint = "getUpdates"
    $url      = "https://api.telegram.org/bot$Token/$endpoint"
    $result   = Invoke-RestMethod -Uri $url
    if ($Date) {
        $Collections = New-Object System.Collections.Generic.List[System.Object]
        foreach ($r in $($result.result)) {
            $EpochTime = [DateTime]"1/1/1970"
            $TimeZone = Get-TimeZone
            $UTCTime = $EpochTime.AddSeconds($r.message.date)
            $d = $UTCTime.AddMinutes($TimeZone.BaseUtcOffset.TotalMinutes)
            $Collections.Add([PSCustomObject]@{
                Message = $r.message.text;
                Date    = $d
            })
        }
        $Collections
    }
    else {
        if ($Last) {
            $result.result.message.text[-1]
        }
        elseif ($ChatID) {
            $Collections = New-Object System.Collections.Generic.List[System.Object]
            foreach ($r in $($result.result)) {
                $Collections.Add([PSCustomObject]@{
                    Message = $r.message.text;
                    UserName = $r.message.chat.username;
                    ChatID = $r.message.chat.id;
                    ChatType = $r.message.chat.type
                })
            }
            $Collections
        }
        else {
            $result.result.message.text
        }
    }
}
```
`Get-FromTelegram` \
`Get-FromTelegram -Last` \
`Get-FromTelegram -Date` \
`Get-FromTelegram -ChatID`

[sendmessage](https://core.telegram.org/bots/api#sendmessage)
```PowerShell
function Send-ToTelegram {
param (
    [Parameter(Mandatory = $True)]$Text,
    $Token    = "687...:AAF...",
    $Chat     = "125468108",
    $Keyboard
)
    $endpoint = "sendMessage"
    $url      = "https://api.telegram.org/bot$Token/$endpoint"
    $Body = @{
        chat_id = $Chat
        text    = $Text
    }
    if ($keyboard -ne $null) {
        $Body += @{reply_markup = $keyboard}
    }
    Invoke-RestMethod -Uri $url -Body $Body
}
```
`Send-ToTelegram -Text "Send test from powershell"`
```PowerShell
$LastDate = (Get-FromTelegram -date)[-1].Date
while ($true) {
    $LastMessage  = (Get-FromTelegram -date)[-1]
    Start-Sleep 1
    $LastDateTest = $LastMessage.Date
    if (($LastMessage.Message -match "/Service") -and ($LastDate -ne $LastDateTest)) {
        $ServiceName = $($LastMessage.Message -split " ")[-1]
        $Result = $(Get-Service $ServiceName -ErrorAction Ignore).Status
        if ($Result) {
            Send-ToTelegram -Text $Result
        } else {
            Send-ToTelegram -Text "Service not found"
        }
        $LastDate = $LastDateTest
    }
}
```
`/Service vpnagent` \
`/Service WinRM` \
`/Service test`

### Button
```PowerShell
$keyboard = '{
    "inline_keyboard":[[
        {"text":"Uptime","callback_data":"/Uptime"},
        {"text":"Test","callback_data":"/Test"}
    ]]
}'
Send-ToTelegram -Text "Test buttons" -Keyboard $keyboard
$request = (Invoke-RestMethod -Uri "https://api.telegram.org/bot$Token/getUpdates").result.callback_query
$request.data # прочитать callback_data нажатой кнопки
$request.message.date
```
### Send-ToTelegramFile

https://core.telegram.org/bots/api#senddocument
```PowerShell
function Send-ToTelegramFile {
    param (
        [Parameter(Mandatory = $true)][string]$Path,
        [Parameter(Mandatory = $true)][string]$Token,
        [Parameter(Mandatory = $true)][string]$Chat,
        $Keyboard
    )
    $endpoint = "senddocument"
    $url      = "https://api.telegram.org/bot$Token/$endpoint"
    $multipartContent = [System.Net.Http.MultipartFormDataContent]::new()
    $fileStream = [System.IO.FileStream]::new($Path, [System.IO.FileMode]::Open)
    $fileContent = [System.Net.Http.StreamContent]::new($fileStream)
    $fileHeader = [System.Net.Http.Headers.ContentDispositionHeaderValue]::new("form-data")
    $fileHeader.Name = "document"
    $fileHeader.FileName = [System.IO.Path]::GetFileName($Path)
    $fileContent.Headers.ContentDisposition = $fileHeader
    $multipartContent.Add($fileContent, "document")
    if ($Keyboard) {
        $keyboardContent = [System.Net.Http.StringContent]::new($Keyboard)
        $keyboardContent.Headers.ContentType.MediaType = "application/json"
        $multipartContent.Add($keyboardContent, "reply_markup")
    }
    $chatContent = [System.Net.Http.StringContent]::new($Chat)
    $multipartContent.Add($chatContent, "chat_id")
    $response = Invoke-RestMethod -Uri $url -Method Post -Body $multipartContent -ContentType "multipart/form-data"
    $fileStream.Dispose()
    return $response
}
```
`Send-ToTelegramFile -Path "C:\Users\Lifailon\Documents\lake.jpg" -Token "7777777777:AAF..." -Chat "7777777777"`

# Discord

[Developers](https://discord.com/developers/applications)

Создаем Applications (General Information). В Bot привязываем к Application и копируем токен авторизации. В OAuth2 - URL Generator выбираем bot и права Administrator и копируем созданный URL для добавления на канал. Переходим по url и добавляем бота на сервер. Получаем ID канала на сервере (текстовые каналы, правой кнопкой мыши копируем ссылку и забираем последний id в url).

### Send to Discord
```Bash
DISCORD_TOKEN="MTE5NzE1NjM0NTM3NjQxMTcyOQ.XXXXXX.EzBF6RA9Kx_MSuhLW5elH1U-XXXXXXXXXXXXXX"
DISCORD_CHANNEL_ID="119403124XXXXXXXXXX"
TEXT="test from bash"
URL="https://discordapp.com/api/channels/$DISCORD_CHANNEL_ID/messages"
curl -s -X POST $URL \
  -H "Authorization: Bot $DISCORD_TOKEN" \
  -H "Content-Type: application/json" \
  -d "{\"content\": \"$TEXT\"}"
```
```PowerShell
$DISCORD_TOKEN = "MTE5NzE1NjM0NTM3NjQxMTcyOQ.XXXXXX.EzBF6RA9Kx_MSuhLW5elH1U-XXXXXXXXXXXXXX"
$DISCORD_CHANNEL_ID = "119403124XXXXXXXXXX"
$TEXT = "test from PowerShell"
$URL = "https://discordapp.com/api/channels/$DISCORD_CHANNEL_ID/messages"
$Body = @{
    content = $TEXT
} | ConvertTo-Json
curl -s $URL -X POST -H "Authorization: Bot $DISCORD_TOKEN" -H "Content-Type: application/json" -d $Body
```
### Read from Discord
```Bash
curl -s -X GET $URL \
  -H "Authorization: Bot $DISCORD_TOKEN" \
  -H "Content-Type: application/json" | jq -r .[0].content
```
```PowerShell
$messages = (curl -s -X GET $URL -H "Authorization: Bot $DISCORD_TOKEN" -H "Content-Type: application/json" | ConvertFrom-Json)
$messages | Select-Object content,timestamp,{$_.author.username}
```
### HttpClient
```PowerShell
$DISCORD_TOKEN = "MTE5NzE1NjM0NTM3NjQxMTcyOQ.XXXXXX.EzBF6RA9Kx_MSuhLW5elH1U-XXXXXXXXXXXXXX"
$DISCORD_CHANNEL_ID = "119403124XXXXXXXXXX"
$URL = "https://discordapp.com/api/channels/$DISCORD_CHANNEL_ID/messages"
$HttpClient = New-Object System.Net.Http.HttpClient
$HttpClient.DefaultRequestHeaders.Authorization = "Bot $DISCORD_TOKEN"
$response = $HttpClient.GetAsync($URL).Result
$messages = $response.Content.ReadAsStringAsync().Result
($messages | ConvertFrom-Json).content
```
### Button
```Bash
curl -X POST $URL \
  -H "Content-Type: application/json" \
  -H "Authorization: Bot $DISCORD_TOKEN" \
  -d '
  {
    "content": "Test text for button",
    "components": [
      {
        "type": 1,
        "components": [
          {
            "type": 2,
            "label": "Button",
            "style": 1,
            "custom_id": "button_click"
          }
        ]
      }
    ]
  }'
```
### Discord Net Webhook

```PowerShell
Add-Type -Path $(ls "$home\Documents\Discord.NET\*.dll").FullName
# https://discordapp.com/api/webhooks/<webhook_id>/<webhook_token> (Настроить канал - Интеграция)
$webhookId = 1197577280000000000
$webhookToken = "rs8AA-XXXXXXXXXXX_Vk5RUI4A6HuSGhpCCTepq25duwCwLXasfv6u23a7XXXXXXXXXX"
$messageContent = "Test dotNET"
$client = New-Object Discord.Webhook.DiscordWebhookClient($webhookId, $webhookToken)
$client.SendMessageAsync($messageContent).Wait()
```

### Discord Net WebSocket

```PowerShell
$DiscordAssemblies = $(ls "$home\Documents\Discord.NET\*.dll").FullName
foreach ($assembly in $DiscordAssemblies) {
    Add-Type -Path $assembly
}
$DISCORD_TOKEN = "MTE5NzE1NjM0NTM3NjQxMTcyOQ.XXXXXX.EzBF6RA9Kx_MSuhLW5elH1U-XXXXXXXXXXXXXX"
$Client = New-Object Discord.WebSocket.DiscordSocketClient
$Client.Add_MessageReceived({
    param($message)
    if ($message.Author.Id -ne $Client.CurrentUser.Id) {
        Write-Host ("Received message from " + $message.Author.Username + ": " + $message.Content)
        if ($message.Content.Contains("ping")) {
            $message.Channel.SendMessageAsync("pong").GetAwaiter().GetResult()
        }
    }
})
$Client.LoginAsync([Discord.TokenType]::Bot, $DISCORD_TOKEN).GetAwaiter().GetResult()
#$Client.StartAsync().Wait()
$Client.StartAsync().GetAwaiter().GetResult()
$Client.ConnectionState

[console]::ReadKey($true)
$Client.LogoutAsync().GetAwaiter().GetResult()
$Client.Dispose()
```

# Torrent

### Jackett

[Source](https://github.com/Jackett/Jackett)

`mkdir /jackett` \
`docker-compose.yml`
```yaml
---
services:
  jackett:
    image: lscr.io/linuxserver/jackett:latest
    container_name: jackett
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /jackett/data:/config
      - /jackett/blackhole:/downloads
    ports:
      - 9117:9117
    restart: unless-stopped
```
`docker-compose up -d jackett` \
`docker exec -it jackett /bin/bash` доступ к оболочке во время работы контейнера \
`docker logs -f jackett` мониторинг журналов контейнера

`/jackett/data/Jackett/ServerConfig.json` место хранения конфигурации сервера \
`/jackett/data/Jackett/Indexers/*.json` место хранения конфигурации индексаторов

`$API_KEY = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"` \
`Invoke-RestMethod "http://127.0.0.1:9117/api/v2.0/indexers/rutor/results/torznab/api?apikey=$API_KEY"` Прочитать RSS ленту RuTor \
`$query = "the+rookie"` \
`Invoke-RestMethod "http://127.0.0.1:9117/api/v2.0/indexers/rutor/results/torznab/api?apikey=$API_KEY&t=search&cat=&q=$query"` поиск в RuTor \
`Invoke-RestMethod "http://127.0.0.1:9117/api/v2.0/indexers/kinozal/results/torznab/api?apikey=$API_KEY&t=search&q=$query"` поиск в кинозал \
`Invoke-RestMethod "http://127.0.0.1:9117/api/v2.0/indexers/kinozal/results/torznab/api?apikey=$API_KEY&t=search&q=$query&cat=5000"` отфильтровать вывод по сериалам (Capabilities: 5000) \
`Invoke-RestMethod "http://127.0.0.1:9117/api/v2.0/indexers/all/results/torznab/api?apikey=$API_KEY&t=search&q=riverdale"` поиск во всех индексаторах \
`$(Invoke-RestMethod "http://127.0.0.1:9117/api/v2.0/indexers/all/results/torznab/api?apikey=$API_KEY&t=indexers&configured=true").indexers.indexer` cписок всех настроенных индексаторов (трекеров)

### Torrent-API-py

[Source](https://github.com/Ryuk-me/Torrent-Api-py) \
[Documentation](https://torrent-api-py-nx0x.onrender.com/docs#/default/health_route_health_get)
```
git clone https://github.com/Ryuk-me/Torrent-Api-py
cd Torrent-Api-py
pip install virtualenv
py -3 -m venv api-py
# Активировать виртуальную среду для Windows
.\api-py\Scripts\activate
# Активировать виртуальную среду для Linux
# $ source api-py/bin/activate
# Установить зависимости и запустить
pip install -r requirements.txt
python main.py
# Proxy: https://github.com/dperson/torproxy
# export HTTP_PROXY="http://proxy-host:proxy-port"
```
`$srv = "http://localhost:8009"` local \
`$srv = "https://torrent-api-py-nx0x.onrender.com"` public \
`Invoke-RestMethod $srv/api/v1/sites` список доступных трекеров \
`Invoke-RestMethod "$srv/api/v1/search/?site=torlock&query=the+rookie&limit=0&page=1"` поиск в выбранном трекере \
`Invoke-RestMethod "$srv/api/v1/all/search?query=the+rookie&limit=0"` поиск по названию во всех трекерах

### Plex

`$API_TOKEN = "XXXXXXXXXXXXXXXXXXXX"`
```
$headers = @{
    "X-Plex-Token" = $API_TOKEN
    "accept" = "application/json"
}
```
`$(Invoke-RestMethod -Headers $headers -Uri http://localhost:32400/servers).MediaContainer.Server` версия сервера \
`Invoke-RestMethod -Headers $headers -Uri http://localhost:32400/diagnostics/logs -OutFile log.zip` выгруить лог с сервера \
`$(Invoke-RestMethod -Headers $headers -Uri http://localhost:32400/library/sections).MediaContainer.Directory` список секций добавленных на сервер \
`$section_key = $(Invoke-RestMethod -Headers $headers -Uri http://localhost:32400/library/sections).MediaContainer.Directory.key[0]` \
`Invoke-RestMethod -Headers $headers -Uri http://localhost:32400/library/sections/$section_key/refresh` синхронизация указанной секции в Plex по ключу \
`$(Invoke-RestMethod -Headers $headers -Uri http://localhost:32400/library/sections/2/folder).MediaContainer.Metadata` получить список директорий и файлов в корне выбранной секции \
`$(Invoke-RestMethod -Headers $headers -Uri http://localhost:32400/library/sections/2/folder?parent=204).MediaContainer.Metadata` получить список всех файлов в указанной директории через ключ (MediaContainer.Metadata.key) конечной точки

### Jellyfin

[Source](https://github.com/jellyfin/jellyfin) \
[API Docs](https://api.jellyfin.org)

`$API_TOKEN "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"` \
`Invoke-RestMethod -Headers @{"X-Emby-Token" = $API_TOKEN} http://localhost:8096/Users` список пользователей и их id \
`$Users = Invoke-RestMethod -Headers @{"X-Emby-Token" = $API_TOKEN} http://localhost:8096/Users` \
`$UserId = $($Users | Where-Object Name -match "Lifailon").Id` забрать id пользователя \
`Invoke-RestMethod -Headers @{"X-Emby-Token" = $API_TOKEN} http://localhost:8096/System/Info` информация о системе \
`$(Invoke-RestMethod -Headers @{"X-Emby-Token" = $API_TOKEN} http://localhost:8096/Items).Items` список добавленных объектов директорий \
`$ItemId = $(Invoke-RestMethod -Headers @{"X-Emby-Token" = $API_TOKEN} http://localhost:8096/Items).Items[-1].Id` забрать id директории \
`$Data = $(Invoke-RestMethod -Headers @{"X-Emby-Token" = $API_TOKEN} "http://localhost:8096/Users/$UserId/Items?ParentId=$ItemId").Items` получить содержимое корневой директории по Id из Items \
`$TvId = $($data | Where-Object Name -match "Rookie").Id` найти сериал или фильм по имени и забрать его Id \
`$(Invoke-RestMethod -Headers @{"X-Emby-Token" = $API_TOKEN} "http://localhost:8096/Users/$UserId/Items?ParentId=$TvId").Items` получить содержимое дочерней директории по Id ее родительской директории

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