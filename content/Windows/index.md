---
title: "Operating System"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

## Windows Event

`Get-WinEvent -ListLog *` отобразить все доступные журналы логов\
`Get-WinEvent -ListLog * | where RecordCount -ne 0 | where RecordCount -ne $null | sort -Descending RecordCount`
отобразить не пустые журналы с сортировкой по кол-ву записей\
`Get-WinEvent -ListProvider * | ft` отобразить всех провайдеров
приложений\
`Get-WinEvent -ListProvider GroupPolicy` найти в какой журнал LogLinks
{Application} пишутся логи приложения\
`Get-WinEvent -ListProvider *smb*`\
`Get-WinEvent -ListLog * | where logname -match SMB | sort -Descending RecordCount`
найти все журналы по имени\
`Get-WinEvent -LogName "Microsoft-Windows-SmbClient/Connectivity"`\
`Get-WinEvent -ListProvider *firewall*`

### Filter XPath/Hashtable

`Get-WinEvent -FilterHashtable @{LogName="Security";ID=4624}` найти логи
по ID в журнале Security\
`Get-WinEvent -FilterHashtable @{LogName="System";Level=2}` найти все
записи ошибки (1 - критический, 3 - предупреждение, 4 - сведения)\
`Get-WinEvent -FilterHashtable @{LogName="System";Level=2;ProviderName="Service Control Manager"}`
отфильтровать по имени провайдера

`([xml](Get-WinEvent -FilterHashtable @{LogName="Security";ID=4688} -MaxEvents 1).ToXml()).Event.EventData.Data`
отобразить все свойства, хранимые в EventData (Message)\
`Get-WinEvent -FilterHashtable @{logname="security";ID=4688} -MaxEvents 1 | select timecreated,{$_.Properties[5].value}`
отфильтровать время события и имя запущенного процесса

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

### Reboot

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

### Logon

``` powershell
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

`Get-EventLog -List` отобразить все корневые журналы логов и их размер\
`Clear-EventLog Application` очистить логи указанного журнала\
`Get-EventLog -LogName Security -InstanceId 4624` найти логи по ID в
журнале Security

## Performance

`lodctr /R` пересоздать счетчиков производительности из системного
хранилища архивов (так же исправляет счетчики для CIM, например, для cpu
Win32_PerfFormattedData_PerfOS_Processor и iops
Win32_PerfFormattedData_PerfDisk_PhysicalDisk)\
`(Get-Counter -ListSet *).CounterSetName` вывести список всех доступных
счетчиков производительности в системе\
`(Get-Counter -ListSet *memory*).Counter` поиск по wildcard-имени во
всех счетчиках (включая дочернии)\
`Get-Counter "\Memory\Available MBytes"` объем свободной оперативной
памяти\
`Get-Counter -cn $srv "\LogicalDisk(*)\% Free Space"` % свободного места
на всех разделах дисков\
`(Get-Counter "\Process(*)\ID Process").CounterSamples`\
`Get-Counter "\Processor(_Total)\% Processor Time" –ComputerName $srv -MaxSamples 5 -SampleInterval 2`
5 проверок каждые 2 секунды\
`Get-Counter "\Процессор(_Total)\% загруженности процессора" -Continuous`
непрерывно\
`(Get-Counter "\Процессор(*)\% загруженности процессора").CounterSamples`

`(Get-Counter -ListSet *интерфейс*).Counter` найти все счетчики\
`Get-Counter "\Сетевой интерфейс(*)\Всего байт/с"` отобразить все
адаптеры (выбрать действующий по трафику)

``` powershell
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

## Defender

`Import-Module Defender`\
`Get-Command -Module Defender`\
`Get-MpComputerStatus`\
`(Get-MpComputerStatus).AntivirusEnabled` статус работы антивируса

`$session = NewCimSession -ComputerName hostname` подключиться к
удаленному компьютеру, используется WinRM\
`Get-MpComputerStatus -CimSession $session | fl fullscan*` узнать дату
последнего сканирования на удаленном компьютере

`Get-MpPreference` настройки\
`(Get-MpPreference).ScanPurgeItemsAfterDelay` время хранения записей
журнала защитника в днях\
`Set-MpPreference -ScanPurgeItemsAfterDelay 30` изменить время хранения\
`ls "C:\ProgramData\Microsoft\Windows Defender\Scans\History"`\
`Get-MpPreference | select disable*` отобразить статус всех видов
проверок/сканирований\
`Set-MpPreference -DisableRealtimeMonitoring $true` отключить защиту
Defender в реальном времени (использовать только ручное сканирование)\
`Set-MpPreference -DisableRemovableDriveScanning $false` включить
сканирование USB накопителей\
`Get-MpPreference | select excl*` отобразить список всех исключений\
`(Get-MpPreference).ExclusionPath`\
`Add-MpPreference -ExclusionPath C:\install` добавить директорию в
исключение\
`Remove-MpPreference -ExclusionPath C:\install` удалить из исключения\
`New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows Defender" -Name DisableAntiSpyware -Value 1 -PropertyType DWORD -Force`
полностью отключить Windows Defender

`Set-MpPreference -SignatureDefinitionUpdateFileSharesSources \\FileShare1\Updates`
для обновления из сетевой папки нужно предварительно скачать файлы с
сигнатурами баз с сайта
https://www.microsoft.com/security/portal/definitions/adl.aspx и
поместить в сетевой каталог\
`Update-MpSignature -UpdateSource FileShares` изменить источник
обновлений (MicrosoftUpdateServer -- сервера обновлений MS в интернете,
InternalDefinitionUpdateServer --- внутренний WSUS сервер)\
`Update-MpSignature` обновить сигнатуры

`Start-MpScan -ScanType QuickScan` быстрая проверка или FullScan\
`Start-MpScan -ScanType FullScan -AsJob`\
`Set-MpPreference -RemediationScheduleDay 1-7` выбрать дни, начиная с
воскресенья или 0 каждый день, 8 - сбросить\
`Set-MpPreference -ScanScheduleQuickScanTime 14:00:00`\
`Start-MpScan -ScanType CustomScan -ScanPath "C:\Program Files"`
сканировать выбранную директорию

`Get-MpThreat` история угроз и тип угрозы (ThreatName: HackTool/Trojan)\
`Get-MpThreatCatalog` список известных видов угроз\
`Get-MpThreatDetection` история защиты (активных и прошлые) и ID угрозы\
`Get-MpThreat -ThreatID 2147760253`

`ls "C:\ProgramData\Microsoft\Windows Defender\Quarantine\"` директория
хранения файлов в карантине\
`cd "C:\Program Files\Windows Defender\"`\
`.\MpCmdRun.exe -restore -name $ThreatName` восстановить файл из
карантина\
`.\MpCmdRun.exe -restore -filepath $path_file`

## DISM

`Get-Command -Module Dism -Name *Driver*`\
`Export-WindowsDriver -Online -Destination C:\Users\Lifailon\Documents\Drivers\`
извлечение драйверов из текущей системы
(C:`\Windows`{=tex}`\System32`{=tex}`\DriverStore`{=tex}`\FileRepository`{=tex}),
выгружает список файлов, которые необходимы для установки драйвера
(dll,sys,exe) в соответствии со списком файлов, указанных в секции
\[CopyFiles\] inf-файла драйвера.\
`Export-WindowsDriver -Path C:\win_image -Destination C:\drivers`
извлечь драйвера из офлайн образа Windows, смонтированного в каталог
c:`\win`{=tex}\_image\
`$BackupDrivers = Export-WindowsDriver -Online -Destination C:\Drivers`\
`$BackupDrivers | ft Driver,ClassName,ProviderName,Date,Version,ClassDescription`
список драйверов в объектном представлении\
`$BackupDrivers | where classname -match printer`\
`pnputil.exe /add-driver C:\drivers\*.inf /subdirs /install` установить
все (параметр subdirs) драйвера из указанной папки (включая вложенные)

`sfc /scannow` проверить целостность системных файлов с помощью утилиты
SFC (System File Checker), в случае поиска ошибок, попробует
восстановить их оригинальные копии из хранилища системных компонентов
Windows (каталог C:`\Windows`{=tex}`\WinSxS`{=tex}). Вывод работы
логируется в C:`\Windows`{=tex}`\Logs`{=tex}`\CBS `{=tex}с тегом SR\
`Get-ComputerInfo | select *` подробная информация о системе
(WindowsVersion,WindowsEditionId,*Bios*)\
`Get-WindowsImage -ImagePath E:\sources\install.wim` список доступных
версий в образе\
`Repair-WindowsImage -Online –ScanHealth`\
`Repair-WindowsImage -Online -RestoreHealth` восстановление хранилища
системных компонентов\
`Repair-WindowsImage -Online -RestoreHealth -Source E:\sources\install.wim:3 –LimitAccess`
восстановление в оффлайн режиме из образа по номеру индекса

## Scheduled

`$Trigger = New-ScheduledTaskTrigger -At 01:00am -Daily` 1:00 ночи\
`$Trigger = New-ScheduledTaskTrigger –AtLogon` запуск при входе
пользователя в систему\
`$Trigger = New-ScheduledTaskTrigger -AtStartup` при запуске системы\
`$User = "NT AUTHORITY\SYSTEM"`\
`$Action = New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "$home\Documents\DNS-Change-Tray-1.3.ps1"`\
`$Action = New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "-NoProfile -NoLogo -NonInteractive -ExecutionPolicy Unrestricted -WindowStyle Hidden -File $home\Documents\DNS-Change-Tray-1.3.ps1"`\
`Register-ScheduledTask -TaskName "DNS-Change-Tray-Startup" -Trigger $Trigger -User $User -Action $Action -RunLevel Highest –Force`

`Get-ScheduledTask | ? state -ne Disabled` список всех активных заданий\
`Start-ScheduledTask DNS-Change-Tray-Startup` запустить задание
немедленно\
`Get-ScheduledTask DNS-Change-Tray-Startup | Disable-ScheduledTask`
отключить задание\
`Get-ScheduledTask DNS-Change-Tray-Startup | Enable-ScheduledTask`
включить задание\
`Unregister-ScheduledTask DNS-Change-Tray-Startup` удалить задание\
`Export-ScheduledTask DNS-Change-Tray-Startup | Out-File $home\Desktop\Task-Export-Startup.xml`
экспортировать задание в xml\
`Register-ScheduledTask -Xml (Get-Content $home\Desktop\Task-Export-Startup.xml | Out-String) -TaskName "DNS-Change-Tray-Startup"`

## LocalAccounts

`Get-Command -Module Microsoft.PowerShell.LocalAccounts`\
`Get-LocalUser` список пользователей\
`Get-LocalGroup` список групп\
`New-LocalUser "1C" -Password $Password -FullName "1C Domain"` создать
пользователя\
`Set-LocalUser -Password $Password 1C` изменить пароль\
`Add-LocalGroupMember -Group "Administrators" -Member "1C"` добавить в
группу Администраторов\
`Get-LocalGroupMember "Administrators"` члены группы

``` powershell
@("vproxy-01","vproxy-02","vproxy-03") | %{
    icm $_ {Add-LocalGroupMember -Group "Administrators" -Member "support4"}
    icm $_ {Get-LocalGroupMember "Administrators"}
}
```

## Credential

`$Cred = Get-Credential` сохраняет креды в переменные `$Cred.Username` и
`$Cred.Password`\
`$Cred.GetNetworkCredential().password` извлечь пароль\
`cmdkey /generic:"TERMSRV/$srv" /user:"$username" /pass:"$password"`
добавить указанные креды аудентификации на на терминальный сервер для
подключения без пароля\
`mstsc /admin /v:$srv` авторизоваться\
`cmdkey /delete:"TERMSRV/$srv"` удалить добавленные креды аудентификации
из системы\
`rundll32.exe keymgr.dll,KRShowKeyMgr` хранилище Stored User Names and
Password\
`Get-Service VaultSvc` служба для работы Credential Manager\
`Install-Module CredentialManager` установить модуль управления
Credential Manager к хранилищу PasswordVault из PowerShell\
`[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]'Tls11,Tls12'`
для устаноки модуля\
`Get-StoredCredential` получить учетные данные из хранилища Windows
Vault\
`Get-StrongPassword` генератор пароля\
`New-StoredCredential -UserName test -Password "123456"` добавить
учетную запись\
`Remove-StoredCredential` удалить учетную запись\
`$Cred = Get-StoredCredential | where {$_.username -match "admin"}`\
`$pass = $cred.password`\
`$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($pass)`\
`[System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)`

## Regedit

`Get-PSDrive` список всех доступных дисков/разделов, их размер и веток
реестра\
`cd HKLM:\` HKEY_LOCAL_MACHINE\
`cd HKCU:\` HKEY_CURRENT_USER\
`Get-Item` получить информацию о ветке реестра\
`New-Item` создать новый раздел реестра\
`Remove-Item` удалить ветку реестра\
`Get-ItemProperty` получить значение ключей/параметров реестра (это
свойства ветки реестра, аналогично свойствам файла)\
`Set-ItemProperty` изменить название или значение параметра реестра\
`New-ItemProperty` создать параметр реестра\
`Remove-ItemProperty` удалить параметр

`Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* | Select DisplayName`
список установленных программ\
`Get-Item HKCU:\SOFTWARE\Microsoft\Office\16.0\Outlook\Profiles\Outlook\9375CFF0413111d3B88A00104B2A6676\00000002`
посмотреть содержимое Items\
`(Get-ItemProperty HKCU:\SOFTWARE\Microsoft\Office\16.0\Outlook\Profiles\Outlook\9375CFF0413111d3B88A00104B2A6676\00000002)."New Signature"`
отобразить значение (Value) свойства (Property) Items\
`$reg_path = "HKCU:\SOFTWARE\Microsoft\Office\16.0\Outlook\Profiles\Outlook\9375CFF0413111d3B88A00104B2A6676\00000002"`\
`$sig_name = "auto"`\
`Set-ItemProperty -Path $reg_path -Name "New Signature" -Value $sig_name`
изменить или добавить в корне ветки (Path) свойство (Name) со значением
(Value)\
`Set-ItemProperty -Path $reg_path -Name "Reply-Forward Signature" -Value $sig_name`

    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\taskmgr.exe]
    "Debugger"="\"C:\\Windows\\System32\\Taskmgr.exe\""

## pki

`New-SelfSignedCertificate -CertStoreLocation Cert:\LocalMachine\My -DnsName "$env:computername" -FriendlyName "Test Certificate" -NotAfter (Get-Date).AddYears(5)`
создать самоподписанный сертификат (в LocalMachine`\My `{=tex}-
Сертификаты компьютера`\Личное`{=tex}) с сроком действия 5 лет

`Get-ChildItem -Path Cert:\CurrentUser\Root\` список всех установленных
сертификатов в хранилище Доверенные корневые ЦС Текущего пользователя\
`Get-ChildItem -Path Cert:\CurrentUser\My\` список самозаверяющих
сертификатов в Личное хранилище Текущего пользователя\
`Get-ChildItem -Path Cert:\LocalMachine\My\` список самозаверяющих
сертификатов в Личное хранилище Локального компьютера\
`Get-ChildItem -Path Cert:\LocalMachine\My\ | select NotBefore,NotAfter,Thumbprint,Subject`
срок действия сертификата\
`Get-ChildItem -Path Cert:\LocalMachine\My\ | where Thumbprint -eq D9356FB774EE0E6206B7D5B59B99102CA5B17BDA`
поиск сертификат по отпечатку

`Get-ChildItem -Path $env:APPDATA\Microsoft\SystemCertificates\My\Certificates\`
сертификаты в файловой системе, каждый файл соответствует сертификату,
установленному в личном хранилище текущего пользователя\
`Get-ChildItem -Path $env:APPDATA\Microsoft\SystemCertificates\My\Keys\`
ссылки на объекты закрытых ключей, созданных поставщиком хранилища
ключей (KSP)\
`Get-ChildItem -Path HKCU:\Software\Microsoft\SystemCertificates\CA\Certificates | ft -AutoSize`
список сертификатов в реестре вошедшего в систему пользователя

`$cert = (Get-ChildItem -Path Cert:\CurrentUser\My\)[1]` выбрать
сертификат\
`$cert | Remove-Item` удалить сертификат

`Export-Certificate -FilePath $home\Desktop\certificate.cer -Cert $cert`
экспортировать сертификат\
`$cert.HasPrivateKey` проверить наличие закрытого ключа\
`$pass = "password" | ConvertTo-SecureString -AsPlainText -Force`
создать пароль для шифрования закрытого ключа\
`Export-PfxCertificate -FilePath $home\Desktop\certificate.pfx -Password $pass -Cert $certificate`
экспортировать сертификат с закрытым ключем

`Import-Certificate -FilePath $home\Desktop\certificate.cer -CertStoreLocation Cert:\CurrentUser\My`
импортировать сертификат\
`Import-PfxCertificate -Exportable -Password $pass -CertStoreLocation Cert:\CurrentUser\My -FilePath $home\Desktop\certificate.pfx`

## OpenSSL

``` powershell
Invoke-WebRequest -Uri https://slproweb.com/download/Win64OpenSSL_Light-3_1_1.msi -OutFile $home\Downloads\OpenSSL-Light-3.1.1.msi
Start-Process $home\Downloads\OpenSSL-Light-3.1.1.msi -ArgumentList '/quiet' -Wait` установить msi пакет в тихом режиме (запуск от имени Администратора)
rm $home\Downloads\OpenSSL-Light-3.1.1.msi
cd "C:\Program Files\OpenSSL-Win64\bin"
```

-   Изменить пароль для PFX\
    `openssl pkcs12 -in "C:\Cert\domain.ru.pfx" -out "C:\Cert\domain.ru.pem" -nodes`
    экспортируем имеющийся сертификат и закрытый ключ в .pem-файл без
    пароля с указанием текущего пароля\
    `openssl pkcs12 -export -in "C:\Cert\domain.ru.pem" -out "C:\Cert\domain.ru_password.pfx" -nodes`
    конвертируем .pem обратно в .pfx c указанием нового пароля

-   Конвертация из закрытого и открытого ключа PEM в PFX\
    `openssl pkcs12 -export -in "C:\tmp\vpn\vpn.itproblog.ru-crt.pem" -inkey "C:\tmp\vpn\vpn.itproblog.ru-key.pem" -out "C:\tmp\vpn\vpn.iiproblog.ru.pfx"`\
    in -- путь до файла с открытым ключом\
    inkey -- путь до файла с закрытым ключом\
    out -- путь до файла, в который будет конвертирован сертификат (pfx)

-   Конвертация PFX в CRT\
    `openssl pkcs12 -in "C:\OpenSSL-Win64\bin\_.domain.ru.pfx" -clcerts -out "C:\OpenSSL-Win64\bin\_.domain.ru.crt"`
    указывается текущий и 2 раза новый пароль PEM pass phrase (файл
    содержит EGIN CERTIFICATE и BEGIN ENCRYPTED PRIVATE KEY)\
    `openssl pkcs12 -in "C:\OpenSSL-Win64\bin\_.domain.ru.pfx" -clcerts -nokeys -out "C:\OpenSSL-Win64\bin\_.domain.ru.crt"`
    без ключа, получить открытую часть (файл содержит только EGIN
    CERTIFICATE)

-   Конвертация PFX в KEY\
    `openssl pkcs12 -in "C:\OpenSSL-Win64\bin\_.domain.ru.pfx" -nocerts -out "C:\OpenSSL-Win64\bin\_.domain.ru.key"`
    файл содержит только BEGIN ENCRYPTED PRIVATE KEY

-   Снять пароль к закрытого ключа .key\
    `openssl rsa -in "C:\OpenSSL-Win64\bin\_.domain.ru.key" -out "C:\OpenSSL-Win64\bin\_.domain.ru-decrypted.key"`

-   CRT и KEY в PFX:\
    `openssl pkcs12 -inkey certificate.key -in certificate.crt -export -out certificate.pfx`

## WMI

### WMI/CIM (Windows Management Instrumentation/Common Information Model)

`Get-WmiObjec -ComputerName localhost -Namespace root -class "__NAMESPACE" | select name,__namespace`
отобразить дочернии Namespace (логические иерархические группы)\
`Get-WmiObject -List` отобразить все классы пространства имен
"root`\cimv2`{=tex}" (по умолчанию), свойства (описывают конфигурацию и
текущее состояние управляемого ресурса) и их методы (какие действия
позволяет выполнить над этим ресурсом)\
`Get-WmiObject -List | Where-Object {$_.name -match "video"}` поиск
класса по имени, его свойств и методов\
`Get-WmiObject -ComputerName localhost -Class Win32_VideoController`
отобразить содержимое свойств класса

`gwmi -List | where name -match "service" | ft -auto` если в таблице
присутствуют Methods, то можно взаимодействовать {StartService,
StopService}\
`gwmi -Class win32_service | select *` отобразить список всех служб и
всех их свойств\
`Get-CimInstance Win32_service` обращается на прямую к
"root`\cimv2`{=tex}"\
`gwmi win32_service -Filter "name='Zabbix Agent'"` отфильтровать вывод
по имени\
`(gwmi win32_service -Filter "name='Zabbix Agent'").State` отобразить
конкретное свойство\
`gwmi win32_service -Filter "State = 'Running'"` отфильтровать
запущенные службы\
`gwmi win32_service -Filter "StartMode = 'Auto'"` отфильтровать службы
по методу запуска\
`gwmi -Query 'select * from win32_service where startmode="Auto"'`
WQL-запрос (WMI Query Language)\
`gwmi win32_service | Get-Member -MemberType Method` отобразить все
методы взаимодействия с описание применения (Delete, StartService)\
`(gwmi win32_service -Filter 'name="Zabbix Agent"').Delete()` удалить
службу\
`(gwmi win32_service -Filter 'name="MSSQL$MSSQLE"').StartService()`
запустить службу

`Get-CimInstance -ComputerName $srv Win32_OperatingSystem | select LastBootUpTime`
время последнего включения\
`gwmi -ComputerName $srv -Class Win32_OperatingSystem | select LocalDateTime,LastBootUpTime`
текущее время и время последнего включения\
`gwmi Win32_OperatingSystem | Get-Member -MemberType Method` методы
reboot и shutdown\
`(gwmi Win32_OperatingSystem -EnableAllPrivileges).Reboot()`
используется с ключем повышения привелегий\
`(gwmi Win32_OperatingSystem -EnableAllPrivileges).Win32Shutdown(0)`
завершение сеанса пользователя

``` powershell
$system = Get-WmiObject -Class Win32_OperatingSystem
$InstallDate = [Management.ManagementDateTimeconverter]::ToDateTime($system.installdate)` Получаем дату установки ОС
$AfterInstallDays = ((Get-Date) — $Installdate).Days` Вычисляем время, прошедшее с момента установки
$ShortInstallDate = "{0:yyyy-MM-dd HH:MM}" -f ($InstallDate)
"Система установлена: $ShortInstallDate (Прошло $AfterInstalldays дней)"
```

`(Get-WmiObject win32_battery).estimatedChargeRemaining` заряд батареи в
процентах\
`gwmi Win32_UserAccount` доменные пользователи\
`(gwmi Win32_SystemUsers).PartComponent`\
`Get-CimInstance -ClassName Win32_LogonSession`\
`Get-CimInstance -ClassName Win32_BIOS`

`gwmi -list -Namespace root\CIMV2\Terminalservices`\
`(gwmi -Class Win32_TerminalServiceSetting -Namespace root\CIMV2\TerminalServices).AllowTSConnections`\
`(gwmi -Class Win32_TerminalServiceSetting -Namespace root\CIMV2\TerminalServices).SetAllowTSConnections(1)`
включить RDP

    $srv = "localhost"
    gwmi Win32_logicalDisk -ComputerName $srv | where {$_.Size -ne $null} | select @{
    Label="Value"; Expression={$_.DeviceID}}, @{Label="AllSize"; Expression={
    [string]([int]($_.Size/1Gb))+" GB"}},@{Label="FreeSize"; Expression={
    [string]([int]($_.FreeSpace/1Gb))+" GB"}}, @{Label="Free%"; Expression={
    [string]([int]($_.FreeSpace/$_.Size*100))+" %"}}

### NLA (Network Level Authentication)

`(gwmi -class "Win32_TSGeneralSetting" -Namespace root\cimv2\Terminalservices -Filter "TerminalName='RDP-tcp'").UserAuthenticationRequired`\
`(gwmi -class "Win32_TSGeneralSetting" -Namespace root\cimv2\Terminalservices -Filter "TerminalName='RDP-tcp'").SetUserAuthenticationRequired(1)`
включить NLA\
`Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name SecurityLayer`
отобразить значение (2)\
`Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name UserAuthentication`
отобразить значение (1)\
`Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name SecurityLayer -Value 0`
изменить значение\
`Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name UserAuthentication -Value 0`\
`REG ADD HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\CredSSP\Parameters /v AllowEncryptionOracle /t REG_DWORD /d 2`
отключить на клиентском компьютере проверку версии CredSSP, если на
целевом комьютере-сервере не установлены обновления KB4512509 от мая
2018 года

## oh-my-posh

[Install](https://ohmyposh.dev/docs/installation/windows)

`winget install JanDeDobbeleer.OhMyPosh -s winget`\
`choco install oh-my-posh -y`\
`scoop install https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest/download/oh-my-posh.json`\
`Set-ExecutionPolicy Bypass -Scope Process -Force; Invoke-Expression ((New-Object System.Net.WebClient).DownloadString('https://ohmyposh.dev/install.ps1'))`

[Themes](https://ohmyposh.dev/docs/themes)

`Get-PoshThemes` отобразить список всех тем\
`oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH/di4am0nd.omp.json" | Invoke-Expression`
применить (использовать) тему в текущей сессии\
`oh-my-posh init pwsh --config "https://raw.githubusercontent.com/JanDeDobbeleer/oh-my-posh/main/themes/cert.omp.json" | Invoke-Expression`
считать тему из репозитория\
`New-Item -Path $PROFILE -Type File -Force` создайт файл профилья
PowerShell\
`'oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH/di4am0nd.omp.json" | Invoke-Expression' > $PROFILE`
сохранить тему профиля (загружать тему при запуске PowerShell)

### themes-performance

`Install-Module themes-performance -Repository NuGet` установить модуль
с темами\
`Set-PoshTheme -Theme System-Sensors` использовать тему с датчиками из
LibreHardwareMonitor\
`Set-PoshTheme -Theme System-Sensors -Save` загрузить тему из
репозитория на локальный компьютер и сохранить тему в профиле\
`Set-PoshTheme -Theme System-Performance` использовать тему с датчиками
системы, получаемыми из системы WMI/CIM (заряд батареи ноутбука \|
загрузка CPU в % \| использование оперативной памяти \| скорость
активного сетевого интерфейса)\
`Set-PoshTheme -Theme System-Performance -Save`\
`Set-PoshTheme -Theme Pwsh-Process-Performance` время работы текущего
процесса pwsh (процессорное время), количество работающих/общее (статус
успех/ошибка) фоновых заданий, Working Set текущего процесса и всех
процессов PowerShell в системе\
`Set-PoshTheme -Theme Pwsh-Process-Performance -Save`

## Windows-Terminal

### Terminal-Icons

`Install-Module -Name Terminal-Icons -Repository PSGallery`\
`scoop bucket add extras`\
`scoop install terminal-icons`

`notepad $PROFILE`\
`Import-Module -Name Terminal-Icons`

Использует шрифты, которые необходимо установить и настроить в
параметрах профиля PowerShell: [Nerd
Fonts](https://github.com/ryanoasis/nerd-fonts)\
[Список шрифтов](https://www.nerdfonts.com/font-downloads)\
Скачать и установить шрифт похожий на Cascadia Code -
[CaskaydiaCove](https://github.com/ryanoasis/nerd-fonts/releases/download/v3.1.1/CascadiaCode.zip)

Установить шрифт в конфигурацию Windows Terminal для PowerShell Core:

``` json
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

Custom actions:
https://learn.microsoft.com/ru-ru/windows/terminal/customize-settings/actions\
Escape-последовательности:
https://learn.microsoft.com/ru-ru/cpp/c-language/escape-sequences?view=msvc-170

``` json
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
