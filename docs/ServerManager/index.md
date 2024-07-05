---
title: "Server Manager"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

## Module

`Get-Command *WindowsFeature*` source module ServerManager 
`Get-WindowsFeature -ComputerName "localhost"` 
`Get-WindowsFeature | where Installed -eq $True` список установленных ролей и компонентов 
`Get-WindowsFeature | where FeatureType -eq "Role"` отсортировать по списку ролей 
`Install-WindowsFeature -Name DNS` установить роль 
`Get-Command *DNS*` 
`Get-DnsServerSetting -ALL` 
`Uninstall-WindowsFeature -Name DNS` удалить роль

## PSWA

`Install-WindowsFeature -Name WindowsPowerShellWebAccess -IncludeManagementTools` 
`Install-PswaWebApplication -UseTestCertificate` Создать веб-приложение /pswa 
`Add-PswaAuthorizationRule -UserGroupName "$domain\Domain Admins" -ComputerName * -ConfigurationName * -RuleName "For Admins"` добавить права авторизации

## WSB (Windows Server Backup)

При создании backup DC через WSB, создается копия состояния системы (System State), куда попадает база AD (NTDS.DIT), объекты групповых политик, содержимое каталога SYSVOL, реестр, метаданные IIS, база AD CS, и другие системные файлы и ресурсы. Резервная копия создается через службу теневого копирования VSS. 
`Get-WindowsFeature Windows-Server-Backup` проверить установлена ли роль 
`Add-Windowsfeature Windows-Server-Backup –Includeallsubfeature` установить роль
```PowerShell
$path="\\$srv\bak-dc\dc-03\"
[string]$TargetUNC=$path+(get-date -f 'yyyy-MM-dd')
if ((Test-Path -Path $path) -eq $true) {New-Item -Path $TargetUNC -ItemType directory} # если путь доступен, создать новую директорию по дате
$WBadmin_cmd = "wbadmin.exe START BACKUP -backupTarget:$TargetUNC -systemState -noverify -vssCopy -quiet"
# $WBadmin_cmd = "wbadmin start backup -backuptarget:$path -include:C:\Windows\NTDS\ntds.dit -quiet" # Backup DB NTDS
Invoke-Expression $WBadmin_cmd
```
## RDS

`Get-Command -Module RemoteDesktop` 
`Get-RDServer -ConnectionBroker $broker` список всех серверов в фермеы, указывается полное доменное имя при обращение к серверу с ролью RDCB 
`Get-RDRemoteDesktop -ConnectionBroker $broker` список коллекций 
`(Get-RDLicenseConfiguration -ConnectionBroker $broker | select *).LicenseServer` список серверов с ролью RDL 
`Get-RDUserSession -ConnectionBroker $broker` список всех активных пользователей 
`Disconnect-RDUser -HostServer $srv -UnifiedSessionID $id -Force` отключить сессию пользователя 
`Get-RDAvailableApp -ConnectionBroker $broker -CollectionName C03` список установленного ПО на серверах в коллекции 
`(Get-RDSessionCollectionConfiguration -ConnectionBroker $broker -CollectionName C03 | select *).CustomRdpProperty` use redirection server name:i:1 
`Get-RDConnectionBrokerHighAvailability`

# DNSServer

`Get-Command -Module DnsServer` 
`Show-DnsServerCache` отобразить весь кэш DNS-сервера 
`Show-DnsServerCache | where HostName -match ru` 
`Clear-DnsServerCache` 
`Get-DnsServerCache` 
`Get-DnsServerDiagnostics`
```PowerShell
$zone = icm $srv {Get-DnsServerZone} | select ZoneName,ZoneType,DynamicUpdate,ReplicationScope,SecureSecondaries,
DirectoryPartitionName | Out-GridView -Title "DNS Server: $srv" –PassThru
$zone_name = $zone.ZoneName
if ($zone_name -ne $null) {
    icm $srv {
        Get-DnsServerResourceRecord -ZoneName $using:zone_name | sort RecordType | select RecordType,HostName, @{
        Label="IPAddress"; Expression={$_.RecordData.IPv4Address.IPAddressToString}},TimeToLive,Timestamp
    } | select RecordType,HostName,IPAddress,TimeToLive,Timestamp | Out-GridView -Title "DNS Server: $srv"
}
```
`Sync-DnsServerZone –passthru` синхронизировать зоны с другими DC в домене 
`Remove-DnsServerZone -Name domain.local` удалить зону 
`Get-DnsServerResourceRecord -ZoneName domain.local -RRType A` вывести все А-записи в указанной зоне 
`Add-DnsServerResourceRecordA -Name new-host-name -IPv4Address 192.168.1.100 -ZoneName domain.local -TimeToLive 01:00:00 -CreatePtr` создать А-запись и PTR для нее 
`Remove-DnsServerResourceRecord -ZoneName domain.local -RRType A -Name new-host-name –Force` удалить А-запись
```PowerShell
$DNSServer = "DC-01"
$DNSFZone = "domain.com"
$DataFile = "C:\Scripts\DNS-Create-A-Records-from-File.csv"
# cat $DataFile
# "HostName;IP"
# "server-01;192.168.1.10"
$DNSRR = [WmiClass]"\\$DNSServer\root\MicrosoftDNS:MicrosoftDNS_ResourceRecord"
$ConvFile = $DataFile + "_unicode"
Get-Content $DataFile | Set-Content $ConvFile -Encoding Unicode
Import-CSV $ConvFile -Delimiter ";" | ForEach-Object {
    $FQDN = $_.HostName + "." + $DNSFZone
    $IP = $_.HostIP
    $TextA = "$FQDN IN A $IP"
    [Void]$DNSRR.CreateInstanceFromTextRepresentation($DNSServer,$DNSFZone,$TextA)
}
```
# DHCPServer

`Get-Command -Module DhcpServer`
```PowerShell
$mac = icm $srv -ScriptBlock {Get-DhcpServerv4Scope | Get-DhcpServerv4Lease} | select AddressState,
HostName,IPAddress,ClientId,DnsRegistration,DnsRR,ScopeId,ServerIP | Out-GridView -Title "HDCP Server: $srv" –PassThru
(New-Object -ComObject Wscript.Shell).Popup($mac.ClientId,0,$mac.HostName,64)
```
`Add-DhcpServerv4Reservation -ScopeId 192.168.1.0 -IPAddress 192.168.1.10 -ClientId 00-50-56-C0-00-08 -Description "new reservation"`

# DFS

`dfsutil /root:\\domain.sys\public /export:C:\export-dfs.txt` экспорт конфигурации namespace root 
`dfsutil /AddFtRoot /Server:\\$srv /Share:public` на новой машине предварительно создать корень на основе домена 
`dfsutil /root:\\domain.sys\public /import:C:\export-dfs.txt /<verify /set` Import (перед импортом данных в существующий корень DFS, утилита создает резервную копию конфигурации корня в текущем каталоге, из которого запускается утилита dfsutil) 
`/verify` выводит изменения, которые будут внесены в процессе импорта, без применения 
`/set` меняет целевое пространство имен путем полной перезаписи и замены на конфигурацию пространства имен из импортируемого файла 
`/merge` импортирует конфигурацию пространства имен в дополнение к существующей конфигурации для слияния, параметры из файла конфигурации будут иметь больший приоритет, чем существующие параметры пространства имен

`Export-DfsrClone` экспортирует клонированную базу данных репликации DFS и параметры конфигурации тома 
`Get-DfsrCloneState` получает состояние операции клонирования базы данных 
`Import-DfsrClone` импортирует клонированную базу данных репликации DFS и параметры конфигурации тома

`net use x: \\$srv1\public\*` примонтировать диск 
`Get-DfsrFileHash x:\* | Out-File C:\$srv1.txt` забрать hash всех файлов диска в файл (файлы с одинаковыми хешами всегда являются точными копиями друг друга) 
`net use x: /d` отмонтировать 
`net use x: \\$srv2\public\*` 
`Get-DfsrFileHash x:\* | Out-File C:\$srv2.txt` 
`net use x: /d` 
`Compare-Object -ReferenceObject (Get-Content C:\$srv1.txt) -DifferenceObject (Get-Content C:\$srv2.txt) -IncludeEqual` сравнить содержимое файлов

`Get-DfsrBacklog -DestinationComputerName "fs-06" -SourceComputerName "fs-05" -GroupName "folder-rep" -FolderName "folder" -Verbose` получает список ожидающих обновлений файлов между двумя партнерами репликации DFS 
`Get-DfsrConnection` отображает группы репликации, участников и статус 
`Get-DfsReplicatedFolder` отображает имя и полный путь к папкам реликации в системе DFS 
`Get-DfsrState -ComputerName fs-06 -Verbose` состояние репликации DFS для члена группы 
`Get-DfsReplicationGroup` отображает группы репликации и их статус 
`Add-DfsrConnection` создает соединение между членами группы репликации 
`Add-DfsrMember` добавляет компьютеры в группу репликации 
`ConvertFrom-DfsrGuid` преобразует идентификаторы GUID в понятные имена в заданной группы репликации 
`Get-DfsrConnectionSchedule` получает расписание соединений между членами группы репликации 
`Get-DfsrGroupSchedule` извлекает расписание группы репликации 
`Get-DfsrIdRecord` получает записи ID для реплицированных файлов или папок из базы данных репликации DFS 
`Get-DfsrMember` получает компьютеры в группе репликации 
`Get-DfsrMembership` получает параметры членства для членов групп репликации 
`Get-DfsrPreservedFiles` получает список файлов и папок, ранее сохраненных репликацией DFS 
`Get-DfsrServiceConfiguration` получает параметры службы репликации DFS для членов группы 
`Grant-DfsrDelegation` предоставляет разрешения участникам безопасности для группы репликации 
`Revoke-DfsrDelegation` отменяет разрешения участников безопасности для группы репликации 
`New-DfsReplicationGroup` создает группу репликации 
`New-DfsReplicatedFolder` создает реплицированную папку в группе репликации 
`Remove-DfsrConnection` удаляет соединение между членами группы репликации 
`Remove-DfsReplicatedFolder` удаляет реплицированную папку из группы репликации 
`Remove-DfsReplicationGroup` удаляет группу репликации 
`Remove-DfsrMember` удаляет компьютеры из группы репликации 
`Restore-DfsrPreservedFiles` восстанавливает файлы и папки, ранее сохраненные репликацией DFS 
`Set-DfsrConnection` изменяет параметры соединения между членами группы репликации 
`Set-DfsrConnectionSchedule` изменяет параметры расписания соединений между членами группы репликации 
`Set-DfsReplicatedFolder` изменяет настройки реплицированной папки 
`Set-DfsReplicationGroup` изменяет группу репликации 
`Set-DfsrGroupSchedule` изменяет расписание группы репликации 
`Set-DfsrMember` изменяет информацию о компьютере-участнике в группе репликации 
`Set-DfsrMembership` настраивает параметры членства для членов группы репликации 
`Set-DfsrServiceConfiguration` изменяет параметры службы репликации DFS 
`Sync-DfsReplicationGroup` синхронизирует репликацию между компьютерами независимо от расписания 
`Suspend-DfsReplicationGroup` приостанавливает репликацию между компьютерами независимо от расписания 
`Update-DfsrConfigurationFromAD` инициирует обновление службы репликации DFS 
`Write-DfsrHealthReport` создает отчет о работоспособности репликации DFS 
`Write-DfsrPropagationReport` создает отчеты для тестовых файлов распространения в группе репликации 
`Start-DfsrPropagationTest` создает тестовый файл распространения в реплицированной папке

# StorageReplica

`Install-WindowsFeature Storage-Replica –IncludeManagementTools -Restart` 
`Get-Command -Module StorageReplica` 
`Test-SRTopology` проверить соответствует ли сервер и канал связи технологии Storage Replica 
`New-SRPartnership -SourceComputerName srv-01 -SourceRGName srv-01-rep-group-01 -SourceVolumeName D: -SourceLogVolumeName L: -DestinationComputerName srv-02 -DestinationRGName srv-02-rep-group-01 -DestinationVolumeName D: -DestinationLogVolumeName L: -LogSizeInBytes 1GB` 
`Get-Counter -Counter "\Storage Replica Statistics(*)"` 
`Get-WinEvent -ProviderName Microsoft-Windows-StorageReplica -max 10` 
`Set-SRPartnership -ReplicationMode Asynchronous` переключить режим репликации на асинхронный 
`Set-SRPartnership -NewSourceComputerName srv-02 -SourceRGName srv-02-rep-group-01 -DestinationComputerName srv-01 -DestinationRGName srv-01-rep-group-01` изменить вручную направление репликации данных, переведя вторичную копию в онлайн режим (при выходе из строя основного сервера) 
`Get-SRGroup` информация о состояние группы реплизации 
`Get-SRPartnerShip` информация о направлении репликации 
`(Get-SRGroup).Replicas | Select-Object numofbytesremaining` проверить длину очереди копирования 
`Get-SRPartnership | Remove-SRPartnership` удалить реплизацию на основном сервере 
`Get-SRGroup | Remove-SRGroup` удалить реплизацию на обоих серверах

# WSUS

`Get-Hotfix | Sort-Object -Descending  InstalledOn` список установленных обновлений (информация из cimv2) 
`Get-Hotfix -Description "Security update"` 
`Get-CimInstance Win32_QuickFixEngineering` 
`Get-Service uhssvc` служба Microsoft Health Update Tools, которая отвечает за предоставление обновлений

## WindowsUpdate

`Get-Command -Module WindowsUpdate` 
`Get-WindowsUpdateLog` формирует отчет в $home\AppData\Local\Temp\WindowsUpdateLog в формате csv

## PSWindowsUpdate

`Install-Module -Name PSWindowsUpdate -Scope CurrentUser` 
`Import-Module PSWindowsUpdate` 
`Get-Command -Module PSWindowsUpdate` 
`Get-WindowsUpdate` список обновлений для скачать и установить с сервера WSUS или Microsoft Update 
`Get-WindowsUpdate -Download` загрузить все обновления 
`Get-WindowsUpdate –Install` установить все обновления 
`Install-WindowsUpdate -MicrosoftUpdate -AcceptAll -IgnoreReboot` установить все обновления без перезагрузки 
`Get-WindowsUpdate -KBArticleID KB2267602, KB4533002 -Install` 
`Get-WindowsUpdate -KBArticleID KB2538243 -Hide` скрыть обновления, что бы они никогда не устанавливались 
`Get-WindowsUpdate –IsHidden` отобразить скрытые обновления (Hide-WindowsUpdate) 
`Remove-WindowsUpdate -KBArticleID KB4011634 -NoRestart` удалить обновление 
`Uninstall-WindowsUpdate` удалить обновление 
`Add-WUServiceManager` регистрация сервера обновления (Windows Update Service Manager) 
`Enable-WURemoting` включить правила Windows Defender, разрешающие удаленное использование командлета PSWindowsUpdate 
`Get-WUApiVersion` версия Windows Update Agent 
`Get-WUHistory` список всех установленных обновлений (история обновлений) 
`Get-WUHistory | Where-Object {$_.Title -match "KB4517389"}` поиск обновления 
`Get-WULastResults` даты последнего поиска и установки обновлений 
`Get-WURebootStatus` проверить, нужна ли перезагрузка для применения конкретного обновления 
`Get-WUServiceManager` выводит источники обновлений 
`Get-WUInstallerStatus` статус службы Windows Installer 
`Remove-WUServiceManager` отключить Windows Update Service Manager

## UpdateServices

`Install-WindowsFeature -Name UpdateServices-RSAT` установить роль UpdateServices 
`$UpdateScope = New-Object Microsoft.UpdateServices.Administration.UpdateScope` 
`[enum]::GetValues([Microsoft.UpdateServices.Administration.ApprovedStates])` список утвержденных состояний 
`[enum]::GetValues([Microsoft.UpdateServices.Administration.UpdateInstallationStates])` статус установки (неизвестный, непригодный, не установлен, скачено, установлен, неуспешный, установлен и ожидает перезагрузки, все) 
`$UpdateScope.ApprovedStates = [Microsoft.UpdateServices.Administration.ApprovedStates]"NotApproved"` выставляем статус не утвержденных обновлений на сервере WSUS 
`$UpdateScope.IncludedInstallationStates = [Microsoft.UpdateServices.Administration.UpdateInstallationStates]"NotInstalled"` выставляем статус не установленных обновлений 
`$UpdateScope.IncludedInstallationStates = [Microsoft.UpdateServices.Administration.UpdateInstallationStates]"NotInstalled,Downloaded"` обновления загружены, но не установлены

## PoshWSUS

`Install-Module -Name PoshWSUS` 
`Get-Command -Module PoshWSUS` нужны права администратора 
`Add-PSWSUSClientToGroup` добавление клиента в группу 
`Approve-PSWSUSUpdate` утверждение обновления 
`Connect-PSWSUSDatabaseServer` подключение к серверу базы данных WSUS 
`Connect-PSWSUSServer` подключение к серверу WSUS 
`Deny-PSWSUSUpdate` отклонение обновления 
`Disconnect-PSWSUSServer` отключение от сервера WSUS 
`Export-PSWSUSMetaData` экспорт метаданных WSUS 
`Get-PoshWSUSSyncUpdateCategories` получение категорий обновлений для синхронизации 
`Get-PoshWSUSSyncUpdateClassifications` получение классификаций обновлений для синхронизации 
`Get-PSWSUSCategory` получение категории WSUS 
`Get-PSWSUSChildServer` получение дочернего сервера WSUS 
`Get-PSWSUSClassification` получение классификации WSUS 
`Get-PSWSUSClient` получение информации о клиенте 
`Get-PSWSUSClientGroupMembership` получение групповой принадлежности клиента 
`Get-PSWSUSClientPerUpdate` получение информации о клиентах по обновлениям 
`Get-PSWSUSClientsInGroup` получение клиентов в группе 
`Get-PSWSUSCommand` получение информации о командах WSUS 
`Get-PSWSUSConfig` получение конфигурации WSUS 
`Get-PSWSUSConfigEnabledUpdateLanguages` получение включенных языков обновлений 
`Get-PSWSUSConfigProxyServer` получение конфигурации прокси-сервера 
`Get-PSWSUSConfigSupportedUpdateLanguages` получение поддерживаемых языков обновлений 
`Get-PSWSUSConfigSyncSchedule` получение расписания синхронизации 
`Get-PSWSUSConfigSyncUpdateCategories` получение категорий обновлений для синхронизации 
`Get-PSWSUSConfigSyncUpdateClassifications` получение классификаций обновлений для синхронизации 
`Get-PSWSUSConfigUpdateFiles` получение конфигурации файлов обновлений 
`Get-PSWSUSConfigUpdateSource` получение источника обновлений 
`Get-PSWSUSConfiguration` получение полной конфигурации WSUS 
`Get-PSWSUSContentDownloadProgress` получение прогресса загрузки контента 
`Get-PSWSUSCurrentUserRole` получение роли текущего пользователя 
`Get-PSWSUSDatabaseConfig` получение конфигурации базы данных 
`Get-PSWSUSDownstreamServer` получение конфигурации нижестоящего сервера 
`Get-PSWSUSEmailConfig` получение конфигурации электронной почты 
`Get-PSWSUSEnabledUpdateLanguages` получение включенных языков обновлений 
`Get-PSWSUSEvent` получение событий WSUS 
`Get-PSWSUSGroup` получение информации о группе 
`Get-PSWSUSInstallableItem` получение информации об установленных элементах 
`Get-PSWSUSInstallApprovalRule` получение правил утверждения установки 
`Get-PSWSUSProxyServer` получение информации о прокси-сервере 
`Get-PSWSUSServer` получение информации о сервере WSUS 
`Get-PSWSUSStatus` получение статуса WSUS 
`Get-PSWSUSSubscription` получение подписки WSUS 
`Get-PSWSUSSupportedUpdateLanguages` получение поддерживаемых языков обновлений 
`Get-PSWSUSSyncEvent` получение событий синхронизации 
`Get-PSWSUSSyncHistory` получение истории синхронизаций 
`Get-PSWSUSSyncProgress` получение прогресса синхронизации 
`Get-PSWSUSSyncSchedule` получение расписания синхронизации 
`Get-PSWSUSUpdate` получение информации об обновлении 
`Get-PSWSUSUpdateApproval` получение информации об утверждении обновлений 
`Get-PSWSUSUpdateCategory` получение категории обновлений 
`Get-PSWSUSUpdateClassification` получение классификации обновлений 
`Get-PSWSUSUpdateFile` получение файлов обновлений 
`Get-PSWSUSUpdateFiles` получение списка файлов обновлений 
`Get-PSWSUSUpdatePerClient` получение информации об обновлениях по клиентам 
`Get-PSWSUSUpdateSource` получение источника обновлений 
`Get-PSWSUSUpdateSummary` получение сводки по обновлениям 
`Get-PSWSUSUpdateSummaryPerClient` получение сводки по обновлениям для каждого клиента 
`Get-PSWSUSUpdateSummaryPerGroup` получение сводки по обновлениям для каждой группы 
`Import-PSWSUSMetaData` импорт метаданных WSUS 
`New-PSWSUSComputerScope` создание области охвата компьютеров 
`New-PSWSUSGroup` создание новой группы 
`New-PSWSUSInstallApprovalRule` создание правила утверждения установки 
`New-PSWSUSUpdateScope` создание области охвата обновлений 
`Remove-PSWSUSClient` удаление клиента 
`Remove-PSWSUSClientFromGroup` удаление клиента из группы 
`Remove-PSWSUSGroup` удаление группы 
`Remove-PSWSUSInstallApprovalRule` удаление правила утверждения установки 
`Remove-PSWSUSUpdate` удаление обновления 
`Reset-PSWSUSContent` сброс контента WSUS 
`Resume-PSWSUSDownload` возобновление загрузки 
`Resume-PSWSUSUpdateDownload` возобновление загрузки обновлений 
`Set-PoshWsusClassification` установка классификации WSUS 
`Set-PoshWSUSProduct` установка продукта WSUS 
`Set-PSWSUSConfigEnabledUpdateLanguages` установка включенных языков обновлений 
`Set-PSWSUSConfigProduct` установка продукта конфигурации WSUS 
`Set-PSWsusConfigProxyServer` установка прокси-сервера конфигурации WSUS 
`Set-PSWSUSConfigSyncSchedule` установка расписания синхронизации 
`Set-PSWSUSConfigTargetingMode` установка режима таргетирования 
`Set-PSWSUSConfigUpdateClassification` установка классификации обновлений 
`Set-PSWSUSConfigUpdateFiles` установка файлов обновлений 
`Set-PSWSUSConfigUpdateSource` установка источника обновлений 
`Set-PSWSUSEmailConfig` установка конфигурации электронной почты 
`Set-PSWSUSEnabledUpdateLanguages` установка включенных языков обновлений 
`Set-PSWSUSInstallApprovalRule` установка правила утверждения установки 
`Set-PSWSUSProxyServer` установка прокси-сервера 
`Set-PSWSUSSyncSchedule` установка расписания синхронизации 
`Set-PSWSUSTargetingMode` установка режима таргетирования 
`Set-PSWSUSUpdateFiles` установка файлов обновлений 
`Set-PSWSUSUpdateSource` установка источника обновлений 
`Start-PSWSUSCleanup` запуск очистки WSUS 
`Start-PSWSUSInstallApprovalRule` запуск правила утверждения установки 
`Start-PSWSUSSync` запуск синхронизации 
`Stop-PSWSUSDownload` остановка загрузки 
`Stop-PSWSUSSync` остановка синхронизации 
`Stop-PSWSUSUpdateDownload` остановка загрузки обновлений 
`Test-PSWSUSDatabaseServerConnection` тестирование подключения к серверу базы данных
