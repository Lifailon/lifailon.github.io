---
title: "Active Directory"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

## RSAT (Remote Server Administration Tools)

`DISM.exe /Online /add-capability /CapabilityName:Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0 /CapabilityName:Rsat.GroupPolicy.Management.Tools~~~~0.0.1.0` 
`Add-WindowsCapability –online –Name Rsat.Dns.Tools~~~~0.0.1.0` 
`Add-WindowsCapability -Online -Name Rsat.DHCP.Tools~~~~0.0.1.0` 
`Add-WindowsCapability –online –Name Rsat.FileServices.Tools~~~~0.0.1.0` 
`Add-WindowsCapability -Online -Name Rsat.WSUS.Tools~~~~0.0.1.0` 
`Add-WindowsCapability -Online -Name Rsat.CertificateServices.Tools~~~~0.0.1.0` 
`Add-WindowsCapability -Online -Name Rsat.RemoteDesktop.Services.Tools~~~~0.0.1.0` 
`Get-WindowsCapability -Name RSAT* -Online | Select-Object -Property DisplayName, State` отобразить список установленных компанентов

## Import-Module ActiveDirectory

`$Session = New-PSSession -ComputerName $srv` -Credential $cred 
`Export-PSsession -Session $Session -Module ActiveDirectory -OutputModule ActiveDirectory` экспортировать модуль из удаленной сесси (например, с DC) 
`Remove-PSSession -Session $Session` 
`Import-Module ActiveDirectory` 
`Get-Command -Module ActiveDirectory`

## ADSI (Active Directory Service Interface)

`$d0 = $env:userdnsdomain` 
`$d0 = $d0 -split "\."` 
`$d1 = $d0[0]` 
`$d2 = $d0[1]` 
`$group = [ADSI]"LDAP://OU=Domain Controllers,DC=$d1,DC=$d2"` 
`$group | select *`

`$Local_User = [ADSI]"WinNT://./Администратор,user"` 
`$Local_User | Get-Member` 
`$Local_User.Description` 
`$Local_User.LastLogin` время последней авторизации локального пользователя

## LDAP (Lightweight Directory Access Protocol)

`$ldapsearcher = New-Object System.DirectoryServices.DirectorySearcher` 
`$ldapsearcher.SearchRoot = "LDAP://OU=Domain Controllers,DC=$d1,DC=$d2"` 
`$ldapsearcher.Filter = "(objectclass=computer)"` 
`$dc = $ldapsearcher.FindAll().path`

`$usr = $env:username` cписок групп текущего пользователя 
`$ldapsearcher = New-Object System.DirectoryServices.DirectorySearcher` 
`$ldapsearcher.Filter = "(&(objectCategory=User)(samAccountName=$usr))"` 
`$usrfind = $ldapsearcher.FindOne()` 
`$groups = $usrfind.properties.memberof -replace "(,OU=.+)"` 
`$groups = $groups -replace "(CN=)"`

DC (Domain Component) - компонент доменного имени 
OU (Organizational Unit) - организационные подразделения (type), используются для упорядочения объектов 
Container - так же используется для упорядочения объектов, контейнеры в отличии от подраделений не могут быть переименованы, удалены, созданы или связаны с объектом групповой политики (Computers, Domain Controllers, Users) 
DN (Distinguished Name) — уникальное имя объекта и местоположение в лесу AD. В DN описывается содержимое атрибутов в дереве (путь навигации), требуемое для доступа к конкретной записи или ее поиска 
CN (Common Name) - общее имя

`(Get-ADObject (Get-ADRootDSE).DefaultNamingContext -Properties wellKnownObjects).wellKnownObjects` отобразить отобразить контейнеры по умолчанию 
`redircmp OU=Client Computers,DC=root,DC=domain,DC=local` изменить контейнер компьютеров по умолчанию 
`redirusr` изменить контейнер пользователей по умолчанию

## LAPS (Local Admin Password Management)

`Import-module AdmPwd.ps` импортировать модуль 
`Get-AdmPwdPassword -ComputerName NAME` посмотреть пароль 
`Reset-AdmPwdPassword -ComputerName NAME` изменить пароль 
`Get-ADComputer -Filter * -SearchBase "DC=$d1,DC=$d2" | Get-AdmPwdPassword -ComputerName {$_.Name} | select ComputerName,Password,ExpirationTimestamp | Out-GridView` 
`Get-ADComputer -Identity $srv | Get-AdmPwdPassword -ComputerName {$_.Name} | select ComputerName,Password,ExpirationTimestamp`

## Recycle Bin

Удаленные объекты хранятся в корзине AD в течении времени захоронения (определяется в атрибуте домена msDS-deletedObjectLifetime), заданном для леса. По умолчанию это 180 дней. Если данный срок прошел, объект все еще остается в контейнере Deleted Objects, но большинство его атрибутов и связей очищаются (Recycled Object). После истечения периода tombstoneLifetime (по умолчанию также 180 дней, но можно увеличить) объект полностью удаляется из AD автоматическим процессом очистки. 
`Get-ADForest domain.local` отобразить уровень работы леса 
`Set-ADForestMode -Identity domain.local -ForestMode Windows2008R2Forest -force` увеличить уровень работы леса 
`Enable-ADOptionalFeature –Identity "CN=Recycle Bin Feature,CN=Optional Features,CN=Directory Service,CN=Windows NT,CN=Services,CN=Configuration,DC=domain,DC=local" –Scope ForestOrConfigurationSet –Target "domain.local"` включить корзину 
`Get-ADOptionalFeature "Recycle Bin Feature" | select-object name,EnabledScopes` если значение EnabledScopes не пустое, значит в домене корзина Active Directory включена 
`Get-ADObject -Filter 'Name -like "*tnas*"' -IncludeDeletedObjects` найти удаленную (Deleted: True) УЗ (ObjectClass: user) в AD 
`Get-ADObject -Filter 'Name -like "*tnas*"' –IncludeDeletedObjects -Properties *| select-object Name, sAMAccountName, LastKnownParent, memberOf, IsDeleted | fl` проверить значение атрибута IsDeleted, контейнер, в котором находился пользователе перед удалением (LastKnownParent) и список групп, в которых он состоял 
`Get-ADObject –filter {Deleted -eq $True -and ObjectClass -eq "user"} –includeDeletedObjects` вывести список удаленных пользователей 
`Restore-ADObject -Identity "3dc33c7c-b912-4a19-b1b7-415c1395a34e"` восстановить по значению атрибута ObjectGUID 
`Get-ADObject -Filter 'SAMAccountName -eq "tnas-01"' –IncludeDeletedObjects | Restore-ADObject` восстановить по SAMAccountName 
`Get-ADObject -Filter {Deleted -eq $True -and ObjectClass -eq 'group' -and Name -like '*Allow*'} –IncludeDeletedObjects | Restore-ADObject –Verbose` восстановить группу или компьютер

## thumbnailPhoto

`$photo = [byte[]](Get-Content C:\Install\adm.jpg -Encoding byte)` преобразовать файл картинки в массив байтов (jpeg/bmp файл, размером фото до 100 Кб и разрешением 96?96) 
`Set-ADUser support4 -Replace @{thumbnailPhoto=$photo}` задать значение атрибута thumbnailPhoto

## ADDomainController

`Get-ADDomainController` выводит информацию о текущем контроллере домена (LogonServer), который используется данным компьютером для аутентификации (DC выбирается при загрузке в соответствии с топологией сайтов AD) 
`Get-ADDomainController -Discover -Service PrimaryDC` найти контроллер с ролью PDC в домене 
`Get-ADDomainController -Filter * | ft HostName,IPv4Address,Name,Site,OperatingSystem,IsGlobalCatalog` список все DC, принадлежность к сайту, версии ОС и GC

При загрузке ОС служба NetLogon делает DNS запрос со списком контроллеров домена (к SRV записи _ldap._tcp.dc._msdcs.domain_), DNS возвращает список DC в домене с записью Service Location (SRV). Клиент делает LDAP запрос к DC для определения сайта AD по своему IP адресу. Клиент через DNS запрашивает список контроллеров домена в сайте (в разделе _tcp.sitename._sites...).

USN (Update Sequence Numbers) - счетчик номера последовательного обновления, который существует у каждого объекта AD. При репликации контроллеры обмениваются значениями USN, объект с более низким USN будет при репликации перезаписан объектом с более высоким USN. Находится в свойствах - Object (включить View - Advanced Features). Каждый контроллер домена содержит отдельный счетчик USN, который начинает отсчет в момент запуска процесса Dcpromo и продолжает увеличивать значения в течение всего времени существования контроллера домена. Значение счетчика USN увеличивается каждый раз, когда на контроллере домена происходит транзакция, это операции создания, обновления или удаления объекта.

`Get-ADDomainController -Filter * | % {` отобразить USN объекта на всех DC в домене` 
`Get-ADUser -Server $_.HostName -Identity support4 -Properties uSNChanged | select SamAccountName,uSNChanged` 
`}`

`dcpromo /forceremoval` принудительно выполнит понижение в роли контроллера домена до уровня рядового сервера. После понижения роли выполняется удаление всех ссылок в домене на этот контроллер. Далее производит включение сервера в состав домена, и выполнение обратного процесса, т.е. повышение сервера до уровня контроллера домена.

## ADComputer

`nltest /DSGETDC:$env:userdnsdomain` узнать на каком DC аудентифицирован хост (Logon Server) 
`nltest /SC_RESET:$env:userdnsdomain\srv-dc2.$env:userdnsdomain` переключить компьютер на другой контроллер домена AD вручную (The command completed successfully) 
`Get-ADComputer –Identity $env:computername -Properties PasswordLastSet` время последней смены пароля на сервере 
`Test-ComputerSecureChannel –verbose` проверить доверительные отношения с доменом (соответствует ли локальный пароль компьютера паролю, хранящемуся в AD) 
`Reset-ComputerMachinePassword -Credential domain\admin` принудительно обновить пароль 
`Netdom ResetPWD /Server:dc-01 /UserD:domain\admin /PasswordD:*` сбросить хэш пароля компьютера в домене (перезагрузка не требуется) 
`Search-ADAccount -AccountDisabled -ComputersOnly | select Name,LastLogonDate,Enabled` отобразить все отключенные компьютеры

`Get-ADComputer -Filter * -Properties * | select name` список всех компьютеров в домене (Filter), вывести все свойства (Properties) 
`Get-ADComputer -Identity $srv -Properties * | ft Name,LastLogonDate,PasswordLastSet,ms-Mcs-AdmPwd -Autosize` конкретного компьютера в AD (Identity) 
`Get-ADComputer -SearchBase "OU=Domain Controllers,DC=$d1,DC=$d2" -Filter * -Properties * | ft Name, LastLogonDate, distinguishedName -Autosize` поиск в базе по DN (SearchBase)

`(Get-ADComputer -Filter {enabled -eq "true"}).count` получить общее количество активных (незаблокированных) компьютеров 
`(Get-ADComputer -Filter {enabled -eq "true" -and OperatingSystem -like "*Windows Server 2016*"}).count` кол-во активных копьютеров с ОС WS 2016

`Get-ADComputer -Filter * -Properties * | select @{Label="Ping Status"; Expression={` 
`$ping = ping -n 1 -w 50 $_.Name` 
`if ($ping -match "TTL") {"Online"} else {"Offline"}` 
`}},` 
`@{Label="Status"; Expression={` 
`if ($_.Enabled -eq "True") {$_.Enabled -replace "True","Active"} else {$_.Enabled -replace "False","Blocked"}` 
`}}, Name, IPv4Address, OperatingSystem, @{Label="UserOwner"; Expression={$_.ManagedBy -replace "(CN=|,.+)"}` 
`},Created | Out-GridView`

## ADUser

`Get-ADUser -Identity support4 -Properties *` список всех атрибутов 
`Get-ADUser -Identity support4 -Properties DistinguishedName, EmailAddress, Description` путь DN, email и описание 
`Get-ADUser -Filter {(Enabled -eq "True") -and (mail -ne "null")} -Properties mail | ft Name,mail` список активных пользователей и есть почтовый ящик 
`Get-ADUser -Filter {SamAccountName -like "*"} | Measure-Object` посчитать кол-во всех аккаунтов (Count) 
`Get-ADUser -Filter * -Properties WhenCreated | sort WhenCreated | ft Name, whenCreated` дата создания 
`Get-ADUser -Identity support4 -property LockedOut | select samaccountName,Name,Enabled,Lockedout` 
`Enabled=True` учетная запись включена - да 
`Lockedout=False` учетная запись заблокирована (например, политикой паролей) - нет 
`Get-ADUser -Identity support4 | Unlock-ADAccount` разблокировать учетную запись 
`Disable-ADAccount -Identity support4` отключить учетную запись 
`Enable-ADAccount -Identity support4` включить учетную запись 
`Search-ADAccount -LockedOut` найти все заблокированные учетные записи 
`Search-ADAccount -AccountDisabled | select Name,LastLogonDate,Enabled` отобразить все отключенные учетные записи с временем последнего входа

`Get-ADUser -Identity support4 -Properties PasswordLastSet,PasswordExpired,PasswordNeverExpires` 
`PasswordLastSet` время последней смены пароля 
`PasswordExpired=False` пароль истек - нет 
`PasswordNeverExpires=True` срок действия пароля не истекает - да 
`Set-ADAccountPassword support4 -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "password" -Force -Verbose)` изменить пароль учетной записи 
`Set-ADUser -Identity support4 -ChangePasswordAtLogon $True` смена пароля при следующем входе в систему

`$day = (Get-Date).adddays(-90)` 
`Get-ADUser -filter {(passwordlastset -le $day)} | ft` пользователи, которые не меняли свой пароль больше 90 дней

`$day = (Get-Date).adddays(-30)` 
`Get-ADUser -filter {(Created -ge $day)} -Property Created | select Name,Created` Новые пользователи за 30 дней

`$day = (Get-Date).adddays(-360)` 
`Get-ADUser -Filter {(LastLogonTimestamp -le $day)} -Property LastLogonTimestamp | select Name,SamAccountName,@{n='LastLogonTimestamp';e={[DateTime]::FromFileTime($_.LastLogonTimestamp)}} | sort -Descending LastLogonTimestamp` пользователи, которые не логинились больше 360 дней. Репликация атрибута LastLogonTimestamp составляет от 9 до 14 дней. 
`| Disable-ADAccount $_.SamAccountName` заблокировать 
`-WhatIf` отобразить вывод без применения изменений

## ADGroupMember

`(Get-ADUser -Identity support4 -Properties MemberOf).memberof` список групп в которых состоит пользователь 
`Get-ADGroupMember -Identity "Domain Admins" | Select Name,SamAccountName` список пользователей в группе 
`Add-ADGroupMember -Identity "Domain Admins" -Members support5` добавить в группу 
`Remove-ADGroupMember -Identity "Domain Admins" -Members support5 -force` удалить из группы 
`Get-ADGroup -filter * | where {!($_ | Get-ADGroupMember)} | Select Name` отобразить список пустых групп (-Not)

## ADReplication

`Get-Command -Module ActiveDirectory -Name *Replication*` список всех командлетов модуля 
`Get-ADReplicationFailure -Target dc-01` список ошибок репликации с партнерами 
`Get-ADReplicationFailure -Target $env:userdnsdomain -Scope Domain` 
`Get-ADReplicationPartnerMetadata -Target dc-01 | select Partner,LastReplicationAttempt,LastReplicationSuccess,LastReplicationResult,LastChangeUsn` время последней и время успешной репликации с партнерами 
`Get-ADReplicationUpToDatenessVectorTable -Target dc-01` Update Sequence Number (USN) увеличивается каждый раз, когда на контроллере домена происходит транзакция (операции создания, обновления или удаления объекта), при репликации DC обмениваются значениями USN, объект с более низким USN при репликации будет перезаписан высоким USN.

## repadmin

`repadmin /replsummary` отображает время последней репликации на всех DC по направлению (Source и Destination) и их состояние с учетом партнеров 
`repadmin /showrepl $srv` отображает всех партнеров по реплкации и их статус для всех разделов Naming Contexts (DC=ForestDnsZones, DC=DomainDnsZones, CN=Schema, CN=Configuration) 
`repadmin /replicate $srv2 $srv1 DC=domain,DC=local ` выполнить репликацию с $srv1 на $srv2 только указанный раздела домена 
`repadmin /SyncAll /AdeP` запустить межсайтовую исходящую репликацию всех разделов от текущего сервера со всеми партнерами по репликации 
`/A` выполнить для всех разделов NC 
`/d` в сообщениях идентифицировать серверы по DN (вместо GUID DNS - глобальным уникальным идентификаторам) 
`/e` межсайтовая синхронизация (по умолчанию синхронизирует только с DC текущего сайта) 
`/P` извещать об изменениях с этого сервера (по умолчанию: опрашивать об изменениях) 
`repadmin /Queue $srv` отображает кол-во запросов входящей репликации (очередь), которое необходимо обработать (причиной может быть большое кол-во партнеров или формирование 1000 объектов скриптом) 
`repadmin /showbackup *` узнать дату последнего Backup

`Error: 1722` сервер rpc недоступен (ошибка отката репликации). Проверить имя домена в настройках сетевого адаптера, первым должен идти адрес DNS-сервера другого контроллера домена, вторым свой адрес. 
`Get-Service -ComputerName $srv | select name,status | ? name -like "RpcSs"` 
`Get-Service -ComputerName $srv -Name RpcSs -RequiredServices` зависимые службы 
Зависимые службы RPC: 
"Служба сведений о подключенных сетях" - должен быть включен отложенный запуск. Если служба срабатывает до "службы списка сетей", может падать связь с доменом (netlogon) 
"Центр распространения ключей Kerberos" 
"DNS-сервер" 
`nslookup $srv` 
`tnc $srv -p 135` 
`repadmin /retry` повторить попытку привязки к целевому DC, если была ошибка 1722 или 1753 (RPC недоступен)

`repadmin /showrepl $srv` 
`Last attempt @ 2022-07-15 10:46:01 завершена с ошибкой, результат 8456 (0x2108)` при проверки showrepl этого партнера, его ошибка: 8457 (0x2109) 
`Last success @ 2022-07-11 02:29:46` последний успех 
Когда репликация автоматически отключена, ОС записывает в DSA - not writable одно из четырех значений: 
`Path: HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\NTDS\Parameters` 
`Dsa Not Writable` 
`##define DSA_WRITABLE_GEN 1` версия леса несовместима с ОС 
`##define DSA_WRITABLE_NO_SPACE 2` на диске, где размещена база данных Active Directory или файлы журналов (логи), недостаточно свободного места 
`##define DSA_WRITABLE_USNROLLBCK 4` откат USN произошел из-за неправильного отката базы данных Active Directory во времени (восстановление из снапшота) 
`##define DSA_WRITABLE_CORRUPT_UTDV 8` вектор актуальности поврежден на локальном контроллере домена

## dcdiag

`dcdiag /s:<DomainController> [/n:<NamingContext>] [[/u:<domain\user>] [/p:<password>]] [{/a|/e}{/q|/v}] [/f:<LogFile>] [/ferr:<ErrorLog>] [/test:<test>] [/fix]` 
`dcdiag /Test:replications /s:dc-01` отображает ошибки репликации 
`dcdiag /Test:DNS /e /v /q` тест DNS 
`/a` проверка всех серверов данного сайта 
`/e` проверка всех серверов предприятия 
`/q` выводить только сообщения об ошибках 
`/v` выводить подробную информацию 
`/fix` автоматически исправляет ошибки 
`/test:` 
`NetLogons` проверка наличие прав на выполнение репликации 
`Connectivity` проверяет регистрацию DNS для каждого контроллера домена, отправляет тестовый эхо-пакет на каждый контроллер домена и проверяет подключение по протоколам LDAP и RPC к каждому контроллеру домена 
`Services` проверяет работоспособность всех служб, необходимых для работы контроллера домена, на указанном контроллере домена 
`Systemlog` проверяет наличие ошибок в журналах контроллера домена 
`FRSEvent` проверяет ошибки репликации в работе службы репликации файлов, что может означать наличие проблем в репликации SYSVOL и, таким образом, целостности копий объектов групповых политик 
`FSMOCheck` не проверяет роли хозяев операций, а вместо этого запрашивает сервер глобального каталога, первичный контроллер домена, предпочтительный сервер времени, сервер времени и центр распространения ключей (контроллер домена может подключиться к KDC, PDC, серверу глобального каталога) 
`KnowsOfRoleHolders` пgроверяет возможность подключения контроллеров домена ко всем пяти хозяевам операций (ролями FSMO) 
`MachineAccount` проверяет правильность регистрации учетной записи целевого компьютера и правильность объявлений служб этого компьютера (корректность доверительных отношения с доменом). Если обнаружена ошибка, ее можно исправить с помощью утилиты dcdiag, указав параметры /fixmachineaccount или /recreatemachineaccount 
`Advertising` проверяет, правильно ли контроллер домена сообщает о себе и о своей роли хозяина операций. Этот тест завершиться неудачно, если служба NetLogon не запущена 
`CheckSDRefDom` проверяет правильность доменов ссылок дескрипторов безопасности для каждого раздела каталогов программ 
`CrossRefValidation` проверяет правильность перекрестных ссылок для доменов 
`RRSSysvol` проверяет состояние готовности для FRS SYSVOL 
`Intersite` проверяет наличие ошибок, которые могут помешать нормальной репликации между сайтами. Компания Microsoft предупреждает, что иногда результаты этого теста могут оказаться неточными 
`KCCEvent` проверяет безошибочность создания объектов соединений для репликации между сайтами 
`NCSecDesc` проверяет правильность разрешений для репликации в дескрипторах безопасности для заголовков контекста именования 
`ObjectsReplicated` проверяет правильность репликации агента сервера каталогов и объектов учетных записей компьютеров 
`OutboundSecureChannels` проверяется наличие безопасных каналов между всеми контроллерами домена в интересующем домене 
`Replications` проверяет возможность репликации между контроллерами домена и сообщает обо всех ошибках при репликации 
`RidManager` проверяет работоспособность и доступность хозяина относительных идентификаторов 
`VerifyEnterpriseReferences` проверяет действительность системных ссылок службы репликации файлов для всех объектов на всех контроллерах домена в лесу 
`VerifyReferences` проверяет действительность системных ссылок службы репликации файлов для всех объектов на указанном контроллере домена 
`VerifyReplicas` проверяет действительность всех разделов каталога приложения на всех серверах, принимающих участие в репликации

## ntdsutil

Перенос БД AD (ntds.dit): 
`Get-Acl C:\Windows\NTDS | Set-Acl D:\AD-DB` скопировать NTFS разрешения на новый каталог 
`Stop-Service -ComputerName dc -name NTDS` остановить службу Active Directory Domain Services 
`ntdsutil` запустить утилиту ntdsutil 
`activate instance NTDS` выбрать активный экземпляр базы AD 
`files` перейдем в контекст files, в котором возможно выполнение операция с файлами базы ntds.dit 
`move DB to D:\AD-DB\` перенести базу AD в новый каталог (предварительно нужно его создать) 
`info` проверить, что БД находится в новом каталоге 
`move logs to D:\AD-DB\` переместим в тот же каталог файлы с журналами транзакций 
`quit` 
`Start-Service -ComputerName dc -name NTDS`

Сброс пароля DSRM (режим восстановления служб каталогов):  
`ntdsutil` 
`set dsrm password` 
`reset password on server NULL` 
новый пароль 
подтверждение пароля 
`quit` 
`quit`

Синхронизировать с паролем УЗ в AD: 
`ntdsutil` 
`set dsrm password` 
`sync from domain account dsrmadmin` 
`quit` 
`quit`

Ошибка 0x00002e2 при загрузке ОС. 
Загрузиться в режиме восстанавления WinRE (Windows Recovery Environment) - Startup Settings - Restart - DSRM (Directory Services Restore Mode) 
`reagentc /boottore` shutdown /f /r /o /t 0 перезагрузка в режиме WinRE - ОС на базе WinPE (Windows Preinstallation Environment), образ winre.wim находится на скрытом разделе System Restore 
На контроллере домена единственная локальная учетная запись — администратор DSRM. Пароль создается при установке роли контроллера домена ADDS на сервере (SafeModeAdministratorPassword). 
`ntdsutil` 
`activate instance ntds` 
`Files` 
`Info` 
`integrity` проверить целостность БД 
Ошибка: Failed to open DIT for AD DS/LDS instance NTDS. Error -2147418113 
`mkdir c:\ntds_bak` 
`xcopy c:\Windows\NTDS\*.* c:\ntds_bak` backup содержимого каталога с БД 
`esentutl /g c:\windows\ntds\ntds.dit` проверим целостность файла 
Вывод: Integrity check completed. Database is CORRUPTED ошибка, база AD повреждена 
`esentutl /p c:\windows\ntds\ntds.dit` исправить ошибки 
Вывод: Operation completed successfully in xx seconds. нет ошибок 
`esentutl /g c:\windows\ntds\ntds.dit` проверим целостность файла 
Выполнить анализ семантики базы с помощью ntdsutil: 
`ntdsutil` 
`activate instance ntds` 
`semantic database analysis` 
`go` 
`go fixup` исправить семантические ошибки 
Сжать файл БД: 
`activate instance ntds` 
`files` 
`compact to C:\Windows\NTDS\TEMP` 
`copy C:\Windows\NTDS\TEMP\ntds.dit C:\Windows\NTDS\ntds.dit` заменить оригинальный файл ntds.dit 
`Del C:\Windows\NTDS\*.log` удалить все лог файлы из каталога NTDS

## GPO

`Get-Command -Module GroupPolicy` 
`Get-GPO -Domain domain.local -All | ft` 
`Get-GPO -Name LAPS` 
`[xml](Get-GPOReport LAPS -ReportType Xml)` 
`Get-GPPermission -Name LAPS -All` 
`Get-GPO LAPS | New-GPLink -Target "ou=servers,dc=domain,dc=local"` 
`Set-GPLink -Name LAPS -Target "ou=servers,dc=domain,dc=local" -LinkEnabled No` 
`Backup-GPO -Name LAPS -Path "$home\Desktop"` 
`Backup-GPO -All -Path "$home\Desktop"` 
`Restore-GPO -Name LAPS -Path C:\Backup\GPOs\`
