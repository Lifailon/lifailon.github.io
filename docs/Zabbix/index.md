---
title: "Zabbix"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

## Zabbix Agent Deploy
```PowerShell
$url = "https://cdn.zabbix.com/zabbix/binaries/stable/6.4/6.4.5/zabbix_agent2-6.4.5-windows-amd64-static.zip"
$path = "$home\Downloads\zabbix-agent2-6.4.5.zip"
$WebClient = New-Object System.Net.WebClient
$WebClient.DownloadFile($url, $path)` скачать файл
Expand-Archive $path -DestinationPath "C:\zabbix-agent2-6.4.5\"` разархивировать
Remove-Item $path` удалить архив
New-NetFirewallRule -DisplayName "Zabbix-Agent" -Profile Any -Direction Inbound -Action Allow -Protocol TCP -LocalPort 10050,10051` открыть порты в FW

$Zabbix_Server = "192.168.3.102"
$conf = "C:\zabbix-agent2-6.4.5\conf\zabbix_agent2.conf"
$cat = cat $conf
$rep = $cat -replace "Server=.+","Server=$Zabbix_Server"
$rep | Select-String Server=
$rep > $conf

$exe = "C:\zabbix-agent2-6.4.5\bin\zabbix_agent2.exe"
.$exe --config $conf --install` установить службу
Get-Service *Zabbix*Agent* | Start-Service` запустить службу
#.$exe --config $conf --uninstall` удалить службу
```
## zabbix_sender

Создать host - задать произвольное имя (powershell-host) и добавить в группу 
Создать Items: 
Name: Service Count 
Type: Zabbix trapper 
Key: service.count 
Type of Information: Numeric
```PowerShell
$path = "C:\zabbix-agent2-6.4.5\bin"
$scount = (Get-Service).Count
.$path\zabbix_sender.exe -z 192.168.3.102 -s "powershell-host" -k service.count -o $scount
```
## zabbix_get

`apt install zabbix-get` 
`nano /etc/zabbix/zabbix_agentd.conf` 
`Server=127.0.0.1,192.168.3.102,192.168.3.99` добавить сервера для получения данных zabbix_get с агента (как их запрашивает сервер)

`.$path\zabbix_get -s 192.168.3.101 -p 10050 -k agent.version` проверить версию агента 
`.$path\zabbix_get -s 192.168.3.101 -p 10050 -k agent.ping` 1 - ok 
`.$path\zabbix_get -s 192.168.3.101 -p 10050 -k net.if.discovery` список сетевых интерфейсов 
`.$path\zabbix_get -s 192.168.3.101 -p 10050 -k net.if.in["ens33"]` 
`.$path\zabbix_get -s 192.168.3.101 -p 10050 -k net.if.out["ens33"]`

## UserParameter

`UserParameter=process.count,powershell -Command "(Get-Process).Count"` 
`UserParameter=process.vm[*],powershell -Command "(Get-Process $1).ws"`

Test: 
`C:\zabbix-agent2-6.4.5\bin\zabbix_get.exe -s 127.0.0.1 -p 10050 -k process.count` 
`C:\zabbix-agent2-6.4.5\bin\zabbix_get.exe -s 127.0.0.1 -p 10050 -k process.vm[zabbix_agent2] `
`C:\zabbix-agent2-6.4.5\bin\zabbix_get.exe -s 127.0.0.1 -p 10050 -k process.vm[powershell]`

Создать новые Items: 
key: `process.count` 
key: `process.vm[zabbix_agent2]`

## Include

- Добавить параметр Include для включения конфигурационных файлов подключаемых плагинов
`'Include=.\zabbix_agent2.d\plugins.d\*.conf' >> C:\zabbix-agent2-6.4.5\conf\zabbix_agent2.conf`

- Создать конфигурационный файл с пользовательскими параметрами в каталоге, путь к которому указан в zabbix_agentd.conf 
`'UserParameter=Get-Query-Param[*],powershell.exe -noprofile -executionpolicy bypass -File C:\zabbix-agent2-6.4.5\conf\zabbix_agent2.d\scripts\User-Sessions\Get-Query-Param.ps1 $1' > C:\zabbix-agent2-6.4.5\conf\zabbix_agent2.d\plugins.d\User-Sessions.conf`

- Поместить скрипт Get-Query-Param.ps1 в каталог, путь к которому указан в User-Sessions.conf. Скрипт содержим пользовательские параметры, которые он принимает от Zabbix сервера для передачи их в функции скрипта.
```PowerShell
Param([string]$select)
if ($select -eq "ACTIVEUSER") {
(Get-Query | where status -match "Active").User
}
if ($select -eq "INACTIVEUSER") {
(Get-Query | where status -match "Disconnect").User
}
if ($select -eq "ACTIVECOUNT") {
(Get-Query | where status -match "Active").Status.Count
}
if ($select -eq "INACTIVECOUNT") {
(Get-Query | where status -match "Disconnect").Status.Count
}
```
- Проверить работу скрипта:

`$path = "C:\zabbix-agent2-6.4.5\conf\zabbix_agent2.d\scripts\User-Sessions"` 
`.$path\Get-Query-Param.ps1 ACTIVEUSER` 
`.$path\Get-Query-Param.ps1 INACTIVEUSER` 
`.$path\Get-Query-Param.ps1 ACTIVECOUNT` 
`.$path\Get-Query-Param.ps1 INACTIVECOUNT`

- Создать Items с ключами:

`Get-Query-Param[ACTIVEUSER]` Type: Text 
`Get-Query-Param[INACTIVEUSER]` Type: Text 
`Get-Query-Param[ACTIVECOUNT]` Type: Int 
`Get-Query-Param[INACTIVECOUNT]` Type: Int

- Макросы:

`{$ACTIVEMAX} = 16` 
`{$ACTIVEMIN} = 0`

- Триггеры:

`last(/Windows-User-Sessions/Get-Query-Param[ACTIVECOUNT])>{$ACTIVEMAX}` 
`min(/Windows-User-Sessions/Get-Query-Param[ACTIVECOUNT],24h)={$ACTIVEMIN}`

## zabbix_agent2.conf
```
# Агент может работать в пассивном (сервер забирает сам информацию) и активном режиме (агент сам отправляет):
Server=192.168.3.102
ServerActive=192.168.3.102
# Требуется указать hostname для ServerActive:
Hostname=huawei-book-01
# Если не указано, используется для генерации имени хоста (игнорируется, если имя хоста определено):
# HostnameItem=system.hostname
# Как часто обновляется список активных проверок, в секундах (Range: 60-3600):
RefreshActiveChecks=120
# IP-адрес источника для исходящих соединений:
# SourceIP=
# Агент будет слушать на этом порту соединения с сервером (Range: 1024-32767):
# ListenPort=10050
# Список IP-адресов, которые агент должен прослушивать через запятую
# ListenIP=0.0.0.0
# Агент будет прослушивать этот порт для запросов статуса HTTP (Range: 1024-32767):
# StatusPort=
ControlSocket=\\.\pipe\agent.sock
# Куда вести журнал (file/syslog/console):
LogType=file
LogFile=C:\zabbix-agent2-6.4.5\zabbix_agent2.log
# Размер лога от 0-1024 MB (0 - отключить автоматическую ротацию логов)
LogFileSize=100
# Уровень логирования. 4 - для отладки (выдает много информации)
DebugLevel=4
```
## API Token

[Documentation](https://www.zabbix.com/documentation/current/en/manual/api/reference)

`$ip = "192.168.3.102"` 
`$url = "http://$ip/zabbix/api_jsonrpc.php"`
```PowerShell
$data = @{
    "jsonrpc"="2.0";
    "method"="user.login";
    "params"=@{
        "username"="Admin";` в версии до 6.4 параметр "user"
        "password"="zabbix";
    };
    "id"=1;
}
$token = (Invoke-RestMethod -Method POST -Uri $url -Body ($data | ConvertTo-Json) -ContentType "application/json").Result
```
`$token = "2eefd25fdf1590ebcdb7978b5bcea1fff755c65b255da8cbd723181b639bb789"` сгенерировать токен в UI (http://192.168.3.102/zabbix/zabbix.php?action=token.list)

## user.get
```PowerShell
$data = @{
    "jsonrpc"="2.0";
    "method"="user.get";
    "params"=@{
    };
    "auth"=$token;
    "id"=1;
}
$users = (Invoke-RestMethod -Method POST -Uri $url -Body ($data | ConvertTo-Json) -ContentType "application/json").Result
```
## problem.get
```PowerShell
$data = @{
    "jsonrpc"="2.0";
    "method"="problem.get";
    "params"=@{
    };
    "auth"=$token;
    "id"=1;
}
(Invoke-RestMethod -Method POST -Uri $url -Body ($data | ConvertTo-Json) -ContentType "application/json").Result
```
## host.get

Получить список всех хостов (имя и id)

[Endpoint host](https://www.zabbix.com/documentation/current/en/manual/api/reference/host)

host.create - creating new hosts 
host.delete - deleting hosts 
host.get - retrieving hosts 
host.massadd - adding related objects to hosts 
host.massremove - removing related objects from hosts 
host.massupdate - replacing or removing related objects from hosts 
host.update - updating hosts
```PowerShell
$data = @{
    "jsonrpc"="2.0";
    "method"="host.get";
    "params"=@{
        "output"=@(` отфильтровать вывод
            "hostid";
            "host";
        );
    };
    "id"=2;
    "auth"=$token;
}
$hosts = (Invoke-RestMethod -Method POST -Uri $url -Body ($data | ConvertTo-Json) -ContentType "application/json").Result
$host_id = $hosts[3].hostid` забрать id хоста по индексу
```
## item.get

Получить id элементов данных по наименованию ключа для конкретного хоста
```PowerShell
$data = @{
    "jsonrpc"="2.0";
    "method"="item.get";
    "params"=@{
        "hostids"=@($host_id);` отфильтровать по хосту
    };
    "auth"=$token;
    "id"=1;
}
$items = (Invoke-RestMethod -Method POST -Uri $url -Body ($data | ConvertTo-Json) -ContentType "application/json").Result
$items_id = ($items | where key_ -match system.uptime).itemid` забрать id элемента данных
```
## history.get

Получить всю историю элемента данных по его id
```PowerShell
$data = @{
    "jsonrpc"="2.0";
    "method"="history.get";
    "params"=@{
        "hostids"=@($host_id); ` фильтрация по хосту
        "itemids"=@($items_id);` фильтрация по элементу данных
    };
    "auth"=$token;
    "id"=1;
}
$items_data_uptime = (Invoke-RestMethod -Method POST -Uri $url -Body ($data | ConvertTo-Json) -ContentType "application/json").Result` получить все данные по ключу у конкретного хоста
```
## Convert Secconds To TimeSpan and DateTime

`$sec = $items_data_uptime.value`
```PowerShell
function ConvertSecondsTo-TimeSpan {
    param (
        $insec
    )
    $TimeSpan = [TimeSpan]::fromseconds($insec)
    "{0:dd' day 'hh\:mm\:ss}" -f $TimeSpan
}
```
`$UpTime = ConvertSecondsTo-TimeSpan $sec[-1]`

## Convert From Unix Time

`$time = $items_data_uptime.clock`
```PowerShell
function ConvertFrom-UnixTime {
    param (
        $intime
    )
    $EpochTime = [DateTime]"1/1/1970"
    $TimeZone = Get-TimeZone
    $UTCTime = $EpochTime.AddSeconds($intime)
    $UTCTime.AddMinutes($TimeZone.BaseUtcOffset.TotalMinutes)
}
```
`$GetDataTime = ConvertFrom-UnixTime $time[-1]`

`($hosts | where hostid -eq $host_id).host` получить имя хоста 
`$UpTime` последнее полученное значение времени работы хоста 
`$GetDataTime` время последнего полученного значения

# SNMP

## Setup SNMP Service

`Install-WindowsFeature SNMP-Service,SNMP-WMI-Provider -IncludeManagementTools` установить роль SNMP и WMI провайдер через Server Manager 
`Get-WindowsFeature SNMP*` 
`Add-WindowsCapability -Online -Name SNMP.Client~~~~0.0.1.0` установить компонент Feature On Demand для Windows 10/11` 
`Get-Service SNMP*` 
`Get-NetFirewallrule -DisplayName *snmp* | ft` 
`Get-NetFirewallrule -DisplayName *snmp* | Enable-NetFirewallRule`

## Setting SNMP Service via Regedit

Agent: 
`New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\services\SNMP\Parameters\RFC1156Agent" -Name "sysContact" -Value "lifailon-user"` создать (New) или изменить (Set) 
`New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\services\SNMP\Parameters\RFC1156Agent" -Name "sysLocation" -Value "plex-server"`

Security: 
`New-Item -Path "HKLM:\SYSTEM\CurrentControlSet\services\SNMP\Parameters\TrapConfiguration\public"` создать новый community string 
`New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\SNMP\Parameters\ValidCommunities" -Name "public" -Value 16` назначить права на public 
`1 — NONE` 
`2 — NOTIFY` позволяет получать SNMP ловушки 
`4 — READ ONLY` позволяет получать данные с устройства 
`8 — READ WRITE` позволяет получать данные и изменять конфигурацию устройства 
`16 — READ CREATE` позволяет читать данные, изменять и создавать объекты 
`New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\SNMP\Parameters\PermittedManagers" -Name "1" -Value "192.168.3.99"` от кого разрешено принимать запросы 
`Get-Service SNMP | Restart-Service`

## snmpwalk

`snmpwalk -v 2c -c public 192.168.3.100` 
`snmpwalk -v 2c -c public -O e 192.168.3.100`

## SNMP Modules

`Install-Module -Name SNMP` 
`Get-SnmpData -IP 192.168.3.100 -OID 1.3.6.1.2.1.1.4.0 -UDPport 161 -Community public` 
`(Get-SnmpData -IP 192.168.3.100 -OID 1.3.6.1.2.1.1.4.0).Data` 
`Invoke-SnmpWalk -IP 192.168.3.100 -OID 1.3.6.1.2.1.1` пройтись по дереву OID 
`Invoke-SnmpWalk -IP 192.168.3.100 -OID 1.3.6.1.2.1.25.6.3.1.2` список установленного ПО 
`Invoke-SnmpWalk -IP 192.168.3.100 -OID 1.3.6.1.2.1.25.2.3.1` список разделов и памяти (C: D: Virtual Memory и Physical Memory) 
`Set-SnmpData` изменение данных на удаленном устройстве

`Install-Module -Name SNMPv3` 
`Invoke-SNMPv3Get` получение данных по одному OID 
`Invoke-SNMPv3Set` изменение данных 
`Invoke-SNMPv3Walk` обход по дереву OID 
`Invoke-SNMPv3Walk -UserName lifailon -Target 192.168.3.100 -AuthSecret password -PrivSecret password -OID 1.3.6.1.2.1.1 -AuthType MD5 -PrivType AES128`

## Lextm.SharpSnmpLib

[Синтаксис](https://learn.microsoft.com/ru-ru/powershell/dsc/reference/resources/windows/fileresource?view=dsc-1.1) 
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
## Walk
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