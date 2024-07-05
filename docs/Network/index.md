---
title: "Network"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

### ping

`Test-Connection -Count 1 $srv1, $srv2` отправить icmp-пакет двум хостам 
`Test-Connection $srv -ErrorAction SilentlyContinue` не выводить ошибок, если хост не отвечает 
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
`Test-PingNetwork -Network 192.168.3.0` 
`Test-PingNetwork -Network 192.168.3.0 -Timeout 1000`

`Get-CimInstance -Class Win32_PingStatus -Filter "Address='127.0.0.1'"` 
`Get-CimInstance -Class Win32_PingStatus -Filter "Address='127.0.0.1'" | Format-Table -Property Address,ResponseTime,StatusCode -Autosize` 0 - успех 
`'127.0.0.1','8.8.8.8' | ForEach-Object -Process {Get-CimInstance -Class Win32_PingStatus -Filter ("Address='$_'") | Select-Object -Property Address,ResponseTime,StatusCode}` 
`$ips = 1..254 | ForEach-Object -Process {'192.168.1.' + $_}` сформировать массив из ip-адресов подсети

### dhcp

`Get-CimInstance -Class Win32_NetworkAdapterConfiguration -Filter "DHCPEnabled=$true"` отобразить адаптеры с включенным DHCP 
`$wql = 'SELECT * from Win32_NetworkAdapterConfiguration WHERE IPEnabled=True and DHCPEnabled=False'` 
`Invoke-CimMethod -MethodName ReleaseDHCPLease -Query $wql` включение DHCP на всех адаптерах 
`Invoke-CimMethod -ClassName Win32_NetworkAdapterConfiguration -MethodName ReleaseDHCPLeaseAll` отменить аренду адресов DHCP на всех адаптерах 
`Invoke-CimMethod -ClassName Win32_NetworkAdapterConfiguration -MethodName RenewDHCPLeaseAll` обновить аренду адресов DHCP на всех адаптерах

### port

`tnc $srv -p 5985` 
`tnc $srv -CommonTCPPort WINRM` HTTP,RDP,SMB 
`tnc ya.ru –TraceRoute -Hops 2` TTL=2 
`tnc ya.ru -DiagnoseRouting` маршрутизация до хоста, куда (DestinationPrefix: 0.0.0.0/0) через (NextHop: 192.168.1.254)

### netstat

`netstat -anop tcp` -n/-f/-b 
`Get-NetTCPConnection -State Established,Listen | ? LocalPort -Match 3389` 
`Get-NetTCPConnection -State Established,Listen | ? RemotePort -Match 22` 
`Get-NetUDPEndpoint | ? LocalPort -Match 514` netstat -ap udp`

### nslookup

`nslookup ya.ru 1.1.1.1` с указанием DNS сервера 
`nslookup -type=any ya.ru` указать тип записи 
`Resolve-DnsName ya.ru -Type MX` ALL,ANY,A,NS,SRV,CNAME,PTR,TXT(spf) 
`[System.Net.Dns]::GetHostEntry("ya.ru")`

### ipconfig

`Get-NetIPConfiguration` 
`Get-NetIPConfiguration -InterfaceIndex 14 -Detailed`

### Adapter

`Get-NetAdapter` 
`Set-NetIPInterface -InterfaceIndex 14 -Dhcp Disabled` отключить DHCP 
`Get-NetAdapter -InterfaceIndex 14 | New-NetIPAddress –IPAddress 192.168.3.99 -DefaultGateway 192.168.3.1 -PrefixLength 24` задать/добавить статический IP-адрес 
`Set-NetIPAddress -InterfaceIndex 14 -IPAddress 192.168.3.98` изменить IP-адреас на адаптере 
`Remove-NetIPAddress -InterfaceIndex 14 -IPAddress 192.168.3.99` удалить IP-адрес на адаптере 
`Set-NetIPInterface -InterfaceIndex 14 -Dhcp Enabled` включить DHCP

### DNSClient

`Get-DNSClientServerAddress` список интерфейсов и настроенные на них адреса DNS сервера 
`Set-DNSClientServerAddress -InterfaceIndex 14 -ServerAddresses 8.8.8.8` изменить адрес DNS сервера на указанного интерфейсе

### DNSCache

`Get-DnsClientCache` отобразить кэшированные записи клиента DNS 
`Clear-DnsClientCache` очистить кэш

### Binding

`Get-NetAdapterBinding -Name Ethernet -IncludeHidden -AllBindings` 
`Get-NetAdapterBinding -Name "Беспроводная сеть" -DisplayName "IP версии 6 (TCP/IPv6)" | Set-NetAdapterBinding -Enabled $false` отключить IPv6 на адаптере

### TCPSetting

`Get-NetTCPSetting` 
`Set-NetTCPSetting -SettingName DatacenterCustom,Datacenter -CongestionProvider DCTCP` настраивает провайдера управления перегрузкой (Congestion Control Provider) на DCTCP (Data Center TCP) для профилей TCP с именами DatacenterCustom и Datacenter 
`Set-NetTCPSetting -SettingName DatacenterCustom,Datacenter -CwndRestart True` включает функцию перезапуска окна перегрузки (Congestion Window Restart, CwndRestart) для указанных профилей TCP. Это означает, что после периода идле (когда нет передачи данных) TCP окно перегрузки будет сбрасываться 
`Set-NetTCPSetting -SettingName DatacenterCustom,Datacenter -ForceWS Disabled` отключает принудительное масштабирование окна (Forced Window Scaling) для указанных профилей TCP. Масштабирование окна — это механизм, который позволяет увеличивать размер окна перегрузки TCP, чтобы улучшить производительность передачи данных по сети с высокой пропускной способностью и большой задержкой

### hostname

`$env:computername` 
`hostname.exe` 
`(Get-CIMInstance CIM_ComputerSystem).Name` 
`(New-Object -ComObject WScript.Network).ComputerName` 
`[System.Environment]::MachineName` 
`[System.Net.Dns]::GetHostName()`

### arp

`ipconfig /all | Select-String "физ"` grep 
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
`Get-ARP -search 192.168.3.100` 
`Get-ARP -search 192.168.3.100 -proxy dc-01`

### Network Adapter Statistics

`netstat -se` 
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
### iPerf

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

`Invoke-RestMethod "https://github.com/Lifailon/iPerf-GUI/raw/rsa/iPerf-GUI-Install.exe" -OutFile "$home\Downloads\iPerf-GUI-Install.exe"` скачать установочную версию собранную с помощью WinRAR 
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
`docker build -t iperf3-alpine-server .` 
`docker run -d -p 5201:5201 --name iperf3-alpine-server iperf3-alpine-server`

### Server

`iperf3 -s` запуск сервера 
`iperf3 -s -D` запустить сервер в фоновом режиме как службу (--daemon) 
`Get-NetTCPConnection -State Established,Listen | ? LocalPort -Match 5201` проверить, что порт сервера слушает 
`Get-Process -Id $(Get-NetTCPConnection -State Established,Listen | ? LocalPort -Match 5201).OwningProcess` получить процесс по порту 
`Get-Process iperf3 | Stop-Process` остановить процесс 
`iperf3 -s -D --logfile "$home\Documents\iperf3\iperf3.log"` перенаправить вывод в лог файл 
`iperf3 -s -p 5211` указать порт, на котором будет слушать сервер или отправлять запросы клиент 
`iperf3 -s -p 5211 -f M` изменить формат выводимых данных (измерять в байтах а не в битах, доступные значения: K,M,G,T) 
`iperf3 -s -p 5211 -f M -J` вывод в формате json 
`iperf3 -s -p 5211 -f M -V` вывод подробной информации

### Client

`iperf3 -c 192.168.3.100 -p 5211` подключение к серверу (по умолчанию проверяется отдача на сервер с клиента) 
`iperf3 -c 192.168.3.100 -p 5211 -R` обратный тест, проверка скачивания с сервера (--reverse, сервер отправляет данные клиенту) 
`iperf3 -c 192.168.3.100 -p 5211 -R -P 2` количество одновременных потоков ([SUM] - суммарная скорость нескольки потоков) 
`iperf3 -c 192.168.3.100 -p 5211 -R -4` использовать только IPv4 
`iperf3 -c 192.168.3.100 -p 5211 -R -u` использовать UDP вместо TCP 
`iperf3 -c 192.168.3.100 -p 5211 -R -u -b 2mb` установить битрейт в 2.00 Mbits/sec для UDP (по умолчанию 1 Мбит/сек, для TCP не ограничено) 
`iperf3 -c 192.168.3.100 -p 5211 -R -t 30` время одного теста в секундах (по умолчанию 10 секунд) 
`iperf3 -c 192.168.3.100 -p 5211 -R -n 1gb` указать объем данных для проверки (применяется вместо времени -t) 
`iperf3 -c 192.168.3.100 -p 5211 -R --get-server-output` вывести вывод сервера на клиенте

### Output

`sender` upload (скорость передачи на удаленный сервер) 
`receiver` download (скорость скачивания с удаленного сервера) 
`Interval` общее время сканирования 
`Transfer` кол-во переданных и полученных МБайт 
`Bandwidth` скорость передачи (измеряется в Мбит/c)

### PS-iPerf

`Install-Module ps-iperf -Repository NuGet` 
`Import-Module PS-iPerf` 
`Start-iPerfServer -Port 5211` запустить сервер 
`Get-iPerfServer` статус работы сервера 
`Stop-iPerfServer` остановить сервер 
`Connect-iPerfServer -Server 192.168.3.100 -Port 5211 -MBytes 500 -Download` подключиться к серверу и скачать 500 МБайт 
`$SpeedTest = Connect-iPerfServer -Server 192.168.3.100 -Port 5211 -MBytes 500 -LogWrite` передать 500 МБайт на сервер (вести запись в лог-файл) 
`$SpeedTest.Intervals` метрики измерений 
`Get-iPerfLog` прочитать лог-файл

### Firewall
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
`New-NetFirewallRule -Profile Any -DisplayName "Open Port 135 RPC" -Direction Inbound -Protocol TCP -LocalPort 135` открыть in-порт 
`Get-NetFirewallRule | where DisplayName -match kms | select *` найти правило по имени 
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

`Install-Module Firewall-Manager` 
`Export-FirewallRules -Name * -CSVFile $home\documents\fw.csv` -Inbound -Outbound -Enabled -Disabled -Allow -Block (фильтр правил для экспорта) 
`Import-FirewallRules -CSVFile $home\documents\fw.csv`

### RDP

`Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"` отобразить номер текущего RDP порта 
`Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value "3390"` изменить RDP-порт 
`$(Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\" -Name "fDenyTSConnections").fDenyTSConnections` если 0, то включен 
`Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\" -Name "fDenyTSConnections" -Value 0` включить RDP 
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f` 
`(gcim -Class Win32_TerminalServiceSetting -Namespace root\CIMV2\TerminalServices).SetAllowTSConnections(0)` включить RDP (для Windows Server) 
`Get-Service TermService | Restart-Service -Force` перезапустить rdp-службу 
`New-NetFirewallRule -Profile Any -DisplayName "RDP 3390" -Direction Inbound -Protocol TCP -LocalPort 3390` открыть RDP-порт

### IPBan

`auditpol /get /category:*` отобразить все политики аудита 
`auditpol /get /category:Вход/выход` отобразить локальные политики аудита для Входа и Выхода из системы 
`auditpol /set /subcategory:"Вход в систему" /success:enable /failure:enable` включить локальные политики - Аудит входа в систему 
`auditpol /set /subcategory:"Выход из системы" /success:enable /failure:enable`

`$url = $($(Invoke-RestMethod https://api.github.com/repos/DigitalRuby/IPBan/releases/latest).assets | Where-Object name -match ".+win.+x64.+").browser_download_url` получить ссылку для загрузки последней версии 
`$version = $(Invoke-RestMethod https://api.github.com/repos/DigitalRuby/IPBan/releases/latest).tag_name` получить номер последней версии 
`$path = "$home\Documents\ipban-$version"` путь для установки 
`Invoke-RestMethod $url -OutFile "$home\Downloads\IPBan-$version.zip"` скачать дистрибутив 
`Expand-Archive "$home\Downloads\ipban-$version.zip" -DestinationPath $path` разархивировать в путь для установки 
`Remove-Item "$home\Downloads\ipban-$version.zip"` удалить дистрибутив 
`sc create IPBan type=own start=delayed-auto binPath="$path\DigitalRuby.IPBan.exe" DisplayName=IPBan` создать службу 
`Get-Service IPBan` статус службы 
`$conf = $(Get-Content "$path\ipban.config")` читаем конфигурацию 
`$conf = $conf -replace '<add key="Whitelist" value=""/>','<add key="Whitelist" value="192.168.3.0/24"/>'` добавить в белый лист домашнюю сеть для исключения 
`$conf = $conf -replace '<add key="ProcessInternalIPAddresses" value="false"/>','<add key="ProcessInternalIPAddresses" value="true"/>'` включить обработку локальных (внутренних) ip-адресов 
`$conf = $conf -replace '<add key="FailedLoginAttemptsBeforeBanUserNameWhitelist" value="20"/>','<add key="FailedLoginAttemptsBeforeBanUserNameWhitelist" value="5"/>'` указать количество попыток подключения до блокировки 
`$conf = $conf -replace '<add key="ExpireTime" value="01:00:00:00"/>','<add key="ExpireTime" value="00:01:00:00"/>'` задать время блокировки 1 час 
`$conf > "$path\ipban.config"` обновить конфигурацию 
`Get-Service IPBan | Start-Service` запустить службу
```
Get-NetFirewallRule | Where-Object DisplayName -Match "IPBan" | ForEach-Object {
    $Name = $_.DisplayName
    Get-NetFirewallAddressFilter -AssociatedNetFirewallRule $_ | Select-Object @{Name="Name"; Expression={$Name}},LocalIP,RemoteIP
} # отобразить область применения правил Брандмауэра для IPBan
```
`Get-Content -Wait "$path\logfile.txt"` читать лог 
`Get-Service IPBan | Stop-Service` остановить службу 
`sc delete IPBan` удалить службу

### shutdown

`shutdown /r /o` перезагрузка в безопасный режим 
`shutdown /s /t 600 /c "Power off after 10 minutes"` выключение 
`shutdown /s /f` принудительное закрытие приложений 
`shutdown /a` отмена 
`shutdown /r /t 0 /m \\192.168.3.100` 
`Restart-Computer -ComputerName 192.168.3.100 -Protocol WSMan` через WinRM 
`Restart-Computer –ComputerName 192.168.3.100 –Force` через WMI 
`Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\PolicyManager\default\Start\HideShutDown" -Name "value" -Value 1` скрыть кнопку выключения 
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
`Test-NetUDPConnection -ComputerName 127.0.0.1 -PortServer 5201` 
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
`Start-TCPServer -Port 5201` 
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
`Send-WOL -Mac "D8-BB-C1-70-A3-4E"` 
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

`[System.Net.WebClient] | Get-Member` 
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

### OpenVPN

`Invoke-WebRequest -Uri https://swupdate.openvpn.org/community/releases/OpenVPN-2.6.5-I001-amd64.msi -OutFile $home\Downloads\OpenVPN-2.6.5.msi` 
`Start-Process $home\Downloads\OpenVPN-2.6.5.msi -ArgumentList '/quiet /SELECT_OPENSSL_UTILITIES=1' -Wait` 
`msiexec /i $home\Downloads\OpenVPN-2.6.5.msi ADDLOCAL=EasyRSA /passive /quiet # установить отдельный компонент EasyRSA Certificate Management Scripts` 
`# msiexec /i $home\Downloads\OpenVPN-2.6.5.msi ADDLOCAL=OpenVPN.Service,Drivers,Drivers.Wintun,OpenVPN,OpenVPN.GUI,OpenVPN.GUI.OnLogon,EasyRSA /passive` выборочная установка 
`# Invoke-WebRequest -Uri https://github.com/OpenVPN/easy-rsa/releases/download/v3.1.5/EasyRSA-3.1.5-win64.zip -OutFile $home\Downloads\EasyRSA-3.1.5.zip` скачать отдельный пакет EasyRSA 
`rm $home\Downloads\OpenVPN-2.6.5.msi`

`cd "C:\Program Files\OpenVPN\easy-rsa"` 
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
`.\EasyRSA-Start.bat` среда EasyRSA Shell 
`easyrsa init-pki` инициализация PKI, создает директорию: C:\Program Files\OpenVPN\easy-rsa\pki и читает переменные файла \easy-rsa\vars 
`easyrsa build-ca` генерация корневого CA с указанием пароля и произвольное имя сервера (\pki\ca.crt и \pki\private\ca.key) 
`easyrsa gen-req server nopass` генерация запроса сертификата и ключ для сервера OpenVPN - yes (\pki\reqs\server.req и \pki\private\server.key) 
`easyrsa sign-req server server` подписать запрос на выпуск сертификата сервера с помощью CA - yes (\pki\issued\server.crt) 
`easyrsa gen-dh` создать ключ Диффи-Хеллмана (\pki\dh.pem) 
`easyrsa gen-req client1` nopass` генерация запроса сертификата и ключ для клиента OpenVPN (\pki\reqs\client1.req и \pki\private\client1.key)` 
`easyrsa sign-req client client1` подписать запрос на выпуск сертификата клиента с помощью CA - yes (\pki\issued\client1.crt) 
`easyrsa revoke client1` отозвать сертификат пользователя 
`openssl rsa -in "C:\Program Files\OpenVPN\easy-rsa\pki\private\client1.key" -out "C:\Program Files\OpenVPN\easy-rsa\pki\private\client1_nopass.key"` снять защиту паролем для ключа (BEGIN ENCRYPTED PRIVATE KEY -> BEGIN PRIVATE KEY) 
`exit` 
`cd "C:\Program Files\OpenVPN\bin"` 
`.\openvpn --genkey secret ta.key` генерация ключа tls-auth (\bin\ta.key) 
`Move-Item "C:\Program Files\OpenVPN\bin\ta.key" "C:\Program Files\OpenVPN\easy-rsa\pki\"`

### server.ovpn

`# Copy-Item "C:\Program Files\OpenVPN\sample-config\server.ovpn" "C:\Program Files\OpenVPN\config-auto\server.ovpn"` 
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
`New-NetFirewallRule -DisplayName "AllowOpenVPN-In" -Direction Inbound -Protocol UDP –LocalPort 1194 -Action Allow` на сервере 
`New-NetFirewallRule -DisplayName "AllowOpenVPN-Out" -Direction Outbound -Protocol UDP –LocalPort 1194 -Action Allow` на клиенте 
`Get-Service *openvpn* | Restart-Service`

### client.ovpn

`# Copy-Item "C:\Program Files\OpenVPN\sample-config\client.ovpn" "C:\Program Files\OpenVPN\config-auto\client.ovpn"` 
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

`iwr -Uri https://openvpn.net/downloads/openvpn-connect-v3-windows.msi -OutFile "$home\downloads\OpenVPN-Connect-3.msi"` 
Передать конфигурацию и ключи: 
`client.ovpn` 
`ca.crt` 
`dh.pem` 
`ta.key` 
`client1.crt` 
`client1.key`

### Route

`Get-Service RemoteAccess | Stop-Service` 
`Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" -Name "IPEnableRouter" -Value 1` включает IP маршрутизацию 
`(Get-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters").IPEnableRouter` 
`Get-NetIPInterface | select ifIndex,InterfaceAlias,AddressFamily,ConnectionState,Forwarding | ft` отобразить сетевые интерфейсы 
`Set-NetIPInterface -ifIndex 13 -Forwarding Enabled` включить переадресацию на интерфейсе

`sysctl net.ipv4.ip_forward=1` 
`echo "sysctl net.ipv4.ip_forward = 1" >> /etc/sysctl.conf`

`Get-NetRoute` 
`New-NetRoute -DestinationPrefix "192.168.3.0/24" -NextHop "192.168.4.1" -InterfaceIndex 8` 
`route -p add 192.168.3.0 mask 255.255.255.0 192.168.4.1 metric 1` 
`route -p change 192.168.3.0 mask 255.255.255.0 192.168.4.1 metric 2` 
`route -p add 192.168.3.0 mask 255.255.255.0 192.168.4.1 metric 1 if 7` указать номер сетевого интерфейса на который необходимо посылать пакет (Wintun Userspace Tunnel) 
`route print -4` 
`route delete 192.168.3.0`

`tracert 192.168.3.101` с 192.168.4.6
```
1    17 ms     *       22 ms  192.168.4.1
2    12 ms    13 ms    14 ms  192.168.3.101
```
`route add -net 192.168.4.0 netmask 255.255.255.0 gw 192.168.3.100` 
`route -e`

`traceroute 192.168.4.6` с 192.168.3.101
```
1  192.168.3.100 (192.168.3.100)  0.148 ms  0.110 ms  0.106 ms
2  192.168.4.6 (192.168.4.6)  14.573 ms * *
```
`ping 192.168.3.101 -t` с 192.168.4.6 
`tcpdump -n -i ens33 icmp` на 192.168.3.101
```
14:36:34.533771 IP 192.168.4.6 > 192.168.3.101: ICMP echo request, id 1, seq 2962, length 40 # отправил запрос
14:36:34.533806 IP 192.168.3.101 > 192.168.4.6: ICMP echo reply, id 1, seq 2962, length 40 # отправил ответ
```
### NAT

`Get-Command -Module NetNat` 
`New-NetNat -Name LocalNat -InternalIPInterfaceAddressPrefix "192.168.3.0/24"` 
`Add-NetNatStaticMapping -NatName LocalNat -Protocol TCP -ExternalIPAddress 0.0.0.0 -ExternalPort 80 -InternalIPAddress 192.168.3.102 -InternalPort 80` 
`Remove-NetNatStaticMapping -StaticMappingID 0` 
`Remove-NetNat -Name LocalNat`

### WireGuard

`Invoke-WebRequest "https://download.wireguard.com/windows-client/wireguard-amd64-0.5.3.msi" -OutFile "$home\Downloads\WireGuard-Client-0.5.3.msi"` 
`msiexec.exe /i "$home\Downloads\WireGuard-Client-0.5.3.msi" DO_NOT_LAUNCH=1 /qn` 
`Invoke-WebRequest "http://www.wiresock.net/downloads/wiresock-vpn-gateway-x64-1.1.4.1.msi" -OutFile "$home\Downloads\WireSock-VPN-Gateway-1.1.4.1.msi"` 
`msiexec.exe /i "http://www.wiresock.net/downloads/wiresock-vpn-gateway-x64-1.1.4.1.msi" /qn` 
`$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")` 
`wg-quick-config -add -start` 
`26.115.154.67:8181` 
`192.168.21.4/24` 
`Successfully saved client configuration: C:\ProgramData\NT KERNEL\WireSock VPN Gateway\wsclient_1.conf` 
`Successfully saved server configuration: C:\ProgramData\NT KERNEL\WireSock VPN Gateway\wiresock.conf` 
`get-service *wire*` 
`wg show` 
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
### VpnClient

`Get-Command -Module VpnClient` 
`Add-VpnConnection -Name "vpn-failon" -ServerAddress "26.115.154.67" -TunnelType L2TP -L2tpPsk "123098" -EncryptionLevel "Required" -AuthenticationMethod MSChapv2 -RememberCredential -AllUserConnection –PassThru -Force` 
`-TunnelType PPTP/L2TP/SSTP/IKEv2/Automatic` 
`-L2tpPsk` использовать общий ключ для аутентификации (без параметра, для L2TP аутентификации используется сертификат) 
`-AuthenticationMethod Pap/Chap/MSChapv2/Eap/MachineCertificate` 
`-EncryptionLevel NoEncryption/Optional/Required/Maximum/Custom` 
`-SplitTunneling` заворачивать весь трафик через VPN-туннель (включение Use default gateway on remote network в настройках параметра VPN адаптера) 
`-UseWinlogonCredential` использовать учетные данные текущего пользователя для аутентификации на VPN сервере 
`-RememberCredential` разрешить сохранять учетные данные для VPN подключения (учетная запись и пароль сохраняются в диспетчер учетных данных Windows после первого успешного подключения) 
`-DnsSuffix domain.local` 
`-AllUserConnection` разрешить использовать VPN подключение для всех пользователей компьютера (сохраняется в конфигурационный файл: C:\ProgramData\Microsoft\Network\Connections\Pbk\rasphone.pbk)

`Install-Module -Name VPNCredentialsHelper` модуль для сохранения логина и пароля в Windows Credential Manager для VPN подключения 
`Set-VpnConnectionUsernamePassword -connectionname vpn-failon -username user1 -password password`

`rasdial "vpn-failon"` подключиться 
`Get-VpnConnection -AllUserConnection | select *` список VPN подключения, доступных для всех пользователей, найстройки и текущий статус подключения (ConnectionStatus) 
`Add-VpnConnectionRoute -ConnectionName vpn-failon -DestinationPrefix 192.168.3.0/24 –PassThru` динамически добавить в таблицу маршрутизации маршрут, который будет активен при подключении к VPN 
`Remove-VpnConnection -Name vpn-failon -AllUserConnection -Force` удалить

`Set-VpnConnection -Name "vpn-failon" -SplitTunneling $True` включить раздельное тунеллирование 
`Add-VpnConnectionRoute -ConnectionName "vpn-failon" -DestinationPrefix 172.22.22.0/24` настроить маршрутизацию к указанной подсети через VPN-соединение 
`(Get-VpnConnection -ConnectionName "vpn-failon").routes` отобразить таблицу маршрутизации для указанного соединения 
`Remove-VpnConnectionRoute -ConnectionName "vpn-failon" -DestinationPrefix "172.22.23.0/24"`

### ProxyClient

`$user = "lifailon"` 
`$pass = "Proxy"` 
`$SecureString = ConvertTo-SecureString $pass -AsPlainText -Force` 
`$Credential = New-Object System.Management.Automation.PSCredential($user, $SecureString)` 
`[System.Net.Http.HttpClient]::DefaultProxy = New-Object System.Net.WebProxy("http://192.168.3.100:9090")` 
`[System.Net.Http.HttpClient]::DefaultProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials` 
`[System.Net.Http.HttpClient]::DefaultProxy.Credentials = $Credential` 
`Invoke-RestMethod http://ifconfig.me/ip` узнать внешний ip-адрес (по умолчанию в текущей сессии подключения будут происходить через заданный прокси сервер) 
`Invoke-RestMethod https://kinozal.tv/rss.xml`

### netsh

### Reverse Proxy

`netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=192.168.3.108` настраивает входящее подключение на 8080 порту и переадресует трафик на 80 порт указанного хоста 
`netsh interface portproxy show all` отобразить список всех настроек 
`netsh interface portproxy delete v4tov4 listenport=8080 listenaddress=0.0.0.0` удалить переадресацию

### Wlan

`netsh wlan show profile` список сохраненны профилей Wi-Fi и паролей 
`netsh wlan show interfaces` хар-ки текущей сети (MAC, speed) 
`netsh wlan show profile SSID-Name-Network key=clear` очистить пароль 
`netsh wlan show networks` список видемых сетей 
`netsh wlan disconnect` отключиться от Wi-Fi 
`netsh wlan connect name="SSID-Name-Network"` подключиться 
`netsh wlan show drivers` драйвер Wi-Fi 
`netsh wlan set hostednetwork mode=allow ssid="WiFi-Test" key="password"` создание точки доступа Wi-Fi (SoftAP)

### Firewall

`netsh advfirewall set allprofiles state off` отключить fw 
`netsh advfirewall reset` сбросить настройки 
`netsh advfirewall firewall add rule name="Open Remote Desktop" protocol=TCP dir=in localport=3389 action=allow` открыть порт 3389 
`netsh advfirewall firewall add rule name="All ICMP V4" dir=in action=allow protocol=icmpv4` открыть icmp

### OpenSSH

`Get-WindowsCapability -Online | ? Name -like 'OpenSSH.Client*'` 
`Add-WindowsCapability -Online -Name OpenSSH.Client*` 
`dism /Online /Add-Capability /CapabilityName:OpenSSH.Client~~~~0.0.1.0` 
`iwr https://github.com/PowerShell/Win32-OpenSSH/releases/download/v9.2.2.0p1-Beta/OpenSSH-Win64-v9.2.2.0.msi -OutFile $home\Downloads\OpenSSH-Win64-v9.2.2.0.msi` скачать 
`msiexec /i $home\Downloads\OpenSSH-Win64-v9.2.2.0.msi` установить msi пакет 
`Set-Service sshd -StartupType Automatic` 
`Get-NetTCPConnection | where LocalPort -eq 22` 
`New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22` 
`Get-NetFirewallRule -Name *ssh*` 
`Start-Process notepad++ C:\Programdata\ssh\sshd_config` конфигурационный файл 
`GSSAPIAuthentication yes` включить Kerberos аутентификацию (через AD) 
`SyslogFacility LOCAL0` включить локальное ведение журнала в файл (C:\ProgramData\ssh\logs\sshd.log) 
`LogLevel INFO` 
`Restart-Service sshd` 
`ssh -K $srv` выполнить Kerberos аутентификацию 
`ssh Lifailon@192.168.3.99 -p 22` 
`pwsh -command Get-Service` 
`ssh -L 3101:192.168.3.101:22 -R 3101:192.168.3.101:22 lifailon@192.168.3.101 -p 22` SSH Tunnel lifailon@localhost:3101 -> 192.168.3.101:3101

### PSRemoting over SSH

`Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0` установка OpenSSH Server 
`Get-WindowsCapability -Online | ? Name -like 'OpenSSH.Ser*'` 
`iex "& { $(irm https://aka.ms/install-powershell.ps1) } -UseMSI"` установка PowerShell Core последней версии (требуется на клиентской стороне) 
`Set-Service -Name sshd -StartupType "Automatic"` 
`Start-Service sshd` 
`Get-NetTCPConnection -State Listen|where {$_.localport -eq '22'}` 
`Enable-NetFirewallRule -Name *OpenSSH-Server*` 
`New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "C:\Program Files\PowerShell\7\pwsh.exe" -PropertyType String –Force` изменить интерпритатор по умолчанию на pwsh 
`notepad $Env:ProgramData\ssh\sshd_config`

PasswordAuthentication yes 
Subsystem powershell c:/progra~1/powershell/7/pwsh.exe -sshs -NoLogo # запуск интерпретатора pwsh для удаленных SSH подключений

`Restart-Service sshd`

`$session = New-PSSession -HostName 192.168.3.100 -Port 2121 -UserName lifailon -SSHTransport` 
`Invoke-Command -Session $session -ScriptBlock {Get-Service}`

### WinRM

`Enter-PSSession -ComputerName $srv` подключиться к PowerShell сессии через PSRemoting. Подключение возможно только по FQDN-имени 
`Invoke-Command $srv -ScriptBlock {Get-ComputerInfo}` выполнение команды через PSRemoting 
`$session = New-PSSession $srv` открыть сессию 
`Get-PSSession` отобразить активные сессии 
`icm -Session $session {$srv = $using:srv}` передать переменную текущей сессии ($using) в удаленную 
`Disconnect-PSSession $session` закрыть сессию 
`Remove-PSSession $session` удалить сессию 
`Import-Module -Name ActiveDirectory -PSSession $srv` импортировать модуль с удаленного компьютера в локальную сессию

### Windows Remote Management Configuration

`winrm quickconfig -quiet` изменит запуск службы WinRM на автоматический, задаст стандартные настройки WinRM и добавить исключения для портов в fw 
`Enable-PSRemoting –Force` включить PowerShell Remoting, работает только для доменного и частного сетевых профилей Windows 
`Enable-PSRemoting -SkipNetworkProfileCheck -Force` для настройки компьютера в общей (public) сети (работает с версии powershell 6)

`$NetProfiles = Get-NetConnectionProfile` отобразить профили сетевых подключений 
`Set-NetConnectionProfile -InterfaceIndex $NetProfiles[1].InterfaceIndex -NetworkCategory Private` изменить тип сети для профиля (DomainAuthenticated/Public) 
`(Get-CimInstance -ClassName Win32_ComputerSystem).PartOfDomain` проверить, что компьютер добавлен в домен AD 
`Get-Service WinRM | Set-Service -StartupType AutomaticDelayedStart` отложенный запуск 
`Get-Service -Name winrm -RequiredServices` статус зависимых служб 
`New-NetFirewallRule -Profile Any -DisplayName "WinRM HTTP" -Direction Inbound -Protocol TCP -LocalPort 5985,5986` 
`Test-NetConnection $srv -port 5895` проверить порт 
`Test-WSMan $srv -ErrorAction Ignore` проверить работу WinRM на удаленном компьютере (игнорировать вывод ошибок для скрипта) или локально (localhost)

`$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\LocalMachine\My -DnsName "$env:computername" -FriendlyName "WinRM HTTPS Certificate" -NotAfter (Get-Date).AddYears(5)` создать самоподписанный сертификат 
`$Thumbprint = $Cert.Thumbprint` забрать отпечаток 
`New-Item -Path WSMan:\Localhost\Listener -Transport HTTPS -Address * -CertificateThumbprint $Thumbprint -Name WinRM_HTTPS_Listener -Force` создать прослушиватель 
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
`winrm get winrm/config` отобразить всю конфигурацию (Client/Service) 
`winrm get winrm/config/service/auth` конфигурация авторизации на сервере 
`winrm enumerate winrm/config/listener` текущая конфигурация прослушивателей WinRM (отображает отпечаток сертификата для HTTPS 5986) 
`Get-ChildItem -Path Cert:\LocalMachine\My\ | where Thumbprint -eq D9356FB774EE0E6206B7D5B59B99102CA5B17BDA | select *` информация о сертификате

`ls WSMan:\localhost\Client` конфигурацию клиента 
`ls WSMan:\localhost\Service` конфигурация сервера 
`ls WSMan:\localhost\Service\auth` список всех конфигураций аутентификации WinRM сервера 
`Set-Item -path WSMan:\localhost\Service\auth\basic -value $true` разрешить локальную аутентификацию к текущему серверу 
`ls HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\WSMAN` настройки в реестре (например, для включения аудентификации в \Service\auth_basic = 1) 
`Set-Item WSMan:\localhost\Client\TrustedHosts -Value 192.168.* -Force` добавить доверенные хосты в конфигурацию на клиенте, чтобы работала Negotiate аутентификация через NTLM 
`Set-Item WSMan:\localhost\Client\TrustedHosts -Value 192.168.3.100 -Concatenate -Force` добавить второй компьютер 
`ls WSMan:\localhost\Client\TrustedHosts` 
`Set-Item WSMan:\localhost\Client\AllowUnencrypted $true` включить передача незашифрованных данных конфигурации клиента 
`Set-Item WSMan:\localhost\Service\AllowUnencrypted $true` включить передача незашифрованных данных конфигурации сервера (необходимо быть в private сети)

`Get-PSSessionConfiguration` проверить, включен ли PSremoting и вывести список пользователей и групп, которым разрешено подключаться через WinRM 
`Set-PSSessionConfiguration -Name Microsoft.PowerShell -ShowSecurityDescriptorUI` назначить права доступа через дескриптор безопасности текущей сессии (до перезагруки) 
`(Get-PSSessionConfiguration -Name "Microsoft.PowerShell").SecurityDescriptorSDDL` получить настройки дескриптора в формате SDDL 
`Set-PSSessionConfiguration -Name Microsoft.PowerShell -SecurityDescriptorSDDL $SDDL` применить настройки дескриптора на другом компьютере без использования GUI 

`New-LocalUser "WinRM-Writer" -Password (ConvertTo-SecureString -AsPlainText "123098")` создать пользователя 
`Add-LocalGroupMember -Group "Remote Management Users" -Member "WinRM-Writer"` добавить пользователя WinRM-Writer в локальную группу доступа "Пользователи удаленного управления" 
`cmdkey /add:192.168.3.99 /user:WinRM-Writer /pass:123098` сохранить пароль в CredentialManager
`cmdkey /list` 
`Import-Module CredentialManager` 
`Add-Type -AssemblyName System.Web` 
`New-StoredCredential -Target 192.168.3.99 -UserName WinRM-Writer -Password 123098 -Comment WinRM` сохранить пароль в CredentialManager (из PS5) 
`Get-StoredCredential -AsCredentialObject` 
`$cred = Get-StoredCredential -Target 192.168.3.99` 
`Enter-PSSession -ComputerName 192.168.3.99 -Credential $cred -Authentication Negotiate` 
`Enter-PSSession -ComputerName 192.168.3.99 -Credential $cred -Authentication Basic -Port 5985` работает при отключении allowunencrypted на стороне сервера и клиента 
`winrs -r:http://192.168.3.100:5985/wsman -u:WinRM-Writer -p:123098 ipconfig` передать команду через winrs (-?) 
`winrs -r:https://192.168.3.100:5985/wsman -u:WinRM-Writer -p:123098 -ssl ipconfig` через https 
`pwsh -Command "Install-Module -Name PSWSMan"` установить модуль для использования в Linux системе

### Kerberos

`.\CheckMaxTokenSize.ps1 -Principals login -OSEmulation $true -Details $true` узнать размер токена пользователя в домене 
`Get-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\Kerberos\Parameters | select maxtokensize` максимальный размер токена на сервере 
`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\HTTP\Parameters` изменить размера, если заголовок пакета аутентификации превышает 16 Кб (из за большого кол-ва групп) 
`MaxFieldLength увеличить до 0000ffff (65535)` 
`MaxRequestBytes увеличить до 0000ffff (65535)`

### Console-Download

`Install-Module Console-Download -Repository NuGet` устаовить модуль из менеджера пакетов NuGet 
`Invoke-Expression $(Invoke-RestMethod "https://raw.githubusercontent.com/Lifailon/Console-Download/rsa/module/Console-Download/Console-Download.psm1")` или импортировать модуль из GitHub репозитория в текущую сессию PowerShell 
`Invoke-Download -Url "https://github.com/PowerShell/PowerShell/releases/download/v7.4.2/PowerShell-7.4.2-win-x64.zip"` загрузить файл в один поток с отображением скорости загрузки в реальном времен (путь загрузки файла по умолчанию: $home\downloads) 
`Invoke-Download -Url "https://github.com/PowerShell/PowerShell/releases/download/v7.4.2/PowerShell-7.4.2-win-x64.zip" -Thread 3` загрузить один и тотже файл в 3 потока (создается 3 файла, доступно от 1 до 20 потоков)
```PowerShell
$urls = @(
    "https://github.com/PowerShell/PowerShell/releases/download/v7.4.2/PowerShell-7.4.2-win-x64.zip",
    "https://github.com/Lifailon/helperd/releases/download/0.0.1/Helper-Desktop-Setup-0.0.1.exe"
)
Invoke-Download $urls # загрузить параллельно 2 файла
```
`$urls = Get-Content "$home\Desktop\links.txt"` 
`Invoke-Download $urls` передайте список URL-адресов из файла для загрузки файлов, эквивалентному числу url

`$urls = Get-LookingGlassList` отобразить актуальный список конечных точек Looking Glass через Looking.House (выдает 3 ссылки на загрузку файлов по 10, 100 и 1000 мбайт для каждого региона) 
`$usaNy = $urls | Where-Object region -like *USA*New*York*` отфильтровать список по региону и городу 
`$url = $usaNy[0].url100mb` забрать первую ссылку с файлов на 100 МБайт 
`Invoke-Download $url` начать загрузку файла 
`Invoke-Download $url -Thread 3` начать загрузку 3-х одинаковых файлов

### PSDomainTest

`Install-Module PSDomainTest -Repository NuGet -Scope CurrentUser` 
`Get-DomainTest -Domain github.com -Warning` протестировать домен и DNS записи на ошибки (вывести только ошибки) через ZoneMaster (https://github.com/zonemaster/zonemaster) 
`Get-DomainTest -Domain github.com -Warning -json` вывод в формате json 
`Get-DomainTest -Domain github.com -html | Out-File .\result.html` получить отчет в формате HTML-таблицы с фильтрацией по столбцам

## Check-Host
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
`Install-Module CheckHost` установить модуль работы с Check-Host (https://check-host.net) через api 
`Get-CheckHost -List` список хостов (node) разных регионов (40) 
`Get-CheckHost -Server google.com -Type ping -Count 5` использовать 5 любых хостов для 4 пингов с каждого до указанного url (google) 
`Get-CheckHost -Server google.com -Type dns -Count 5` проверить DNS (возвращает А-запись или диапазон с ip-адресом) 
`Get-CheckHost -Server google.com:443 -Type http -Count 5` проверить доступность порта 
`Get-CheckHost -Server google.com:443 -Type tcp -Count 5` проверить доступность TCP или UDP порта

### pSyslog

`Install-Module pSyslog -Repository NuGet` 
`Start-pSyslog -Port 514` запустить сервер на порту 514 (по умолчанию) 
`Start-pSyslog -RotationSize 500` указать размер файла локального журнала для его ротации (обрезания) в КБ 
`Get-pSyslog -Status | Format-List` отобразить статус работы 
`Get-pSyslog` вывести журнал сообщений в реальном времени 
`Stop-pSyslog` остановить сервер 
`Send-pSyslog -Content "Test" -Server 192.168.3.99` отправить сообщение на Syslog-сервер 
`Send-pSyslog -Content "Test" -Server 192.168.3.99 -Type Informational -PortServer 514 -PortClient 55514` 
`(Get-Service -Name WinRM).Status | Send-pSyslog -Server 192.168.3.102 -Tag Service[WinRM]` 
`Send-pSyslog -Content "test" -Server 192.168.3.99 -PortServer 514 -Base64` использовать шифрование при отправки сообщения (расшифровка работает только для сервера pSyslog) 
`Start-UDPRelay -inPort 515 -outIP 192.168.3.102 -outPort 514` запустить сервер в режиме UDP-Relay, который слушает на порту 515 и переадресует сообщения на Syslog сервер 192.168.3.102 с портом 514 
`Send-pSyslog -Server 192.168.3.99 -PortServer 515 -Content $(Get-Date)` отправить сообщение на сервере UDP-Relay 
`Show-pSyslog -Type Warning -Count` отобразить метрики (количество ошибок) 
`Show-pSyslog -Type Alert -Count` 
`Show-pSyslog -Type Critical -Count` 
`Show-pSyslog -Type Error -Count` 
`Show-pSyslog -Type Emergency -Count` 
`Show-pSyslog -Type Informational -Count` 
`Show-pSyslog -LogFile 05-06 | Out-GridView` прочитать локальный журнал логирования и вывести в GridView для фильтрации сообщений 
`Show-pSyslog -Count` отобразить количество сообщений локального журнала 
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
