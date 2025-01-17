+++
title = "Windows Server"
[extra]
toc = true
toc_sidebar = true
+++

Заметки системного администрирования через `PowerShell`.

---

# Active Directory

## RSAT (Remote Server Administration Tools)

`DISM.exe /Online /add-capability /CapabilityName:Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0 /CapabilityName:Rsat.GroupPolicy.Management.Tools~~~~0.0.1.0` \
`Add-WindowsCapability –online –Name Rsat.Dns.Tools~~~~0.0.1.0` \
`Add-WindowsCapability -Online -Name Rsat.DHCP.Tools~~~~0.0.1.0` \
`Add-WindowsCapability –online –Name Rsat.FileServices.Tools~~~~0.0.1.0` \
`Add-WindowsCapability -Online -Name Rsat.WSUS.Tools~~~~0.0.1.0` \
`Add-WindowsCapability -Online -Name Rsat.CertificateServices.Tools~~~~0.0.1.0` \
`Add-WindowsCapability -Online -Name Rsat.RemoteDesktop.Services.Tools~~~~0.0.1.0` \
`Get-WindowsCapability -Name RSAT* -Online | Select-Object -Property DisplayName, State` отобразить список установленных компанентов

## Import-Module ActiveDirectory

`$Session = New-PSSession -ComputerName $srv` -Credential $cred \
`Export-PSsession -Session $Session -Module ActiveDirectory -OutputModule ActiveDirectory` экспортировать модуль из удаленной сесси (например, с DC) \
`Remove-PSSession -Session $Session` \
`Import-Module ActiveDirectory` \
`Get-Command -Module ActiveDirectory`

## ADSI (Active Directory Service Interface)

`$d0 = $env:userdnsdomain` \
`$d0 = $d0 -split "\."` \
`$d1 = $d0[0]` \
`$d2 = $d0[1]` \
`$group = [ADSI]"LDAP://OU=Domain Controllers,DC=$d1,DC=$d2"` \
`$group | select *`

`$Local_User = [ADSI]"WinNT://./Администратор,user"` \
`$Local_User | Get-Member` \
`$Local_User.Description` \
`$Local_User.LastLogin` время последней авторизации локального пользователя

## LDAP (Lightweight Directory Access Protocol)

`$ldapsearcher = New-Object System.DirectoryServices.DirectorySearcher` \
`$ldapsearcher.SearchRoot = "LDAP://OU=Domain Controllers,DC=$d1,DC=$d2"` \
`$ldapsearcher.Filter = "(objectclass=computer)"` \
`$dc = $ldapsearcher.FindAll().path`

`$usr = $env:username` cписок групп текущего пользователя \
`$ldapsearcher = New-Object System.DirectoryServices.DirectorySearcher` \
`$ldapsearcher.Filter = "(&(objectCategory=User)(samAccountName=$usr))"` \
`$usrfind = $ldapsearcher.FindOne()` \
`$groups = $usrfind.properties.memberof -replace "(,OU=.+)"` \
`$groups = $groups -replace "(CN=)"`

DC (Domain Component) - компонент доменного имени \
OU (Organizational Unit) - организационные подразделения (type), используются для упорядочения объектов \
Container - так же используется для упорядочения объектов, контейнеры в отличии от подраделений не могут быть переименованы, удалены, созданы или связаны с объектом групповой политики (Computers, Domain Controllers, Users) \
DN (Distinguished Name) — уникальное имя объекта и местоположение в лесу AD. В DN описывается содержимое атрибутов в дереве (путь навигации), требуемое для доступа к конкретной записи или ее поиска \
CN (Common Name) - общее имя

`(Get-ADObject (Get-ADRootDSE).DefaultNamingContext -Properties wellKnownObjects).wellKnownObjects` отобразить отобразить контейнеры по умолчанию \
`redircmp OU=Client Computers,DC=root,DC=domain,DC=local` изменить контейнер компьютеров по умолчанию \
`redirusr` изменить контейнер пользователей по умолчанию

## LAPS (Local Admin Password Management)

`Import-module AdmPwd.ps` импортировать модуль \
`Get-AdmPwdPassword -ComputerName NAME` посмотреть пароль \
`Reset-AdmPwdPassword -ComputerName NAME` изменить пароль \
`Get-ADComputer -Filter * -SearchBase "DC=$d1,DC=$d2" | Get-AdmPwdPassword -ComputerName {$_.Name} | select ComputerName,Password,ExpirationTimestamp | Out-GridView` \
`Get-ADComputer -Identity $srv | Get-AdmPwdPassword -ComputerName {$_.Name} | select ComputerName,Password,ExpirationTimestamp`

## Recycle Bin

Удаленные объекты хранятся в корзине AD в течении времени захоронения (определяется в атрибуте домена msDS-deletedObjectLifetime), заданном для леса. По умолчанию это 180 дней. Если данный срок прошел, объект все еще остается в контейнере Deleted Objects, но большинство его атрибутов и связей очищаются (Recycled Object). После истечения периода tombstoneLifetime (по умолчанию также 180 дней, но можно увеличить) объект полностью удаляется из AD автоматическим процессом очистки. \
`Get-ADForest domain.local` отобразить уровень работы леса \
`Set-ADForestMode -Identity domain.local -ForestMode Windows2008R2Forest -force` увеличить уровень работы леса \
`Enable-ADOptionalFeature –Identity "CN=Recycle Bin Feature,CN=Optional Features,CN=Directory Service,CN=Windows NT,CN=Services,CN=Configuration,DC=domain,DC=local" –Scope ForestOrConfigurationSet –Target "domain.local"` включить корзину \
`Get-ADOptionalFeature "Recycle Bin Feature" | select-object name,EnabledScopes` если значение EnabledScopes не пустое, значит в домене корзина Active Directory включена \
`Get-ADObject -Filter 'Name -like "*tnas*"' -IncludeDeletedObjects` найти удаленную (Deleted: True) УЗ (ObjectClass: user) в AD \
`Get-ADObject -Filter 'Name -like "*tnas*"' –IncludeDeletedObjects -Properties *| select-object Name, sAMAccountName, LastKnownParent, memberOf, IsDeleted | fl` проверить значение атрибута IsDeleted, контейнер, в котором находился пользователе перед удалением (LastKnownParent) и список групп, в которых он состоял \
`Get-ADObject –filter {Deleted -eq $True -and ObjectClass -eq "user"} –includeDeletedObjects` вывести список удаленных пользователей \
`Restore-ADObject -Identity "3dc33c7c-b912-4a19-b1b7-415c1395a34e"` восстановить по значению атрибута ObjectGUID \
`Get-ADObject -Filter 'SAMAccountName -eq "tnas-01"' –IncludeDeletedObjects | Restore-ADObject` восстановить по SAMAccountName \
`Get-ADObject -Filter {Deleted -eq $True -and ObjectClass -eq 'group' -and Name -like '*Allow*'} –IncludeDeletedObjects | Restore-ADObject –Verbose` восстановить группу или компьютер

## thumbnailPhoto

`$photo = [byte[]](Get-Content C:\Install\adm.jpg -Encoding byte)` преобразовать файл картинки в массив байтов (jpeg/bmp файл, размером фото до 100 Кб и разрешением 96?96) \
`Set-ADUser support4 -Replace @{thumbnailPhoto=$photo}` задать значение атрибута thumbnailPhoto

## ADDomainController

`Get-ADDomainController` выводит информацию о текущем контроллере домена (LogonServer), который используется данным компьютером для аутентификации (DC выбирается при загрузке в соответствии с топологией сайтов AD) \
`Get-ADDomainController -Discover -Service PrimaryDC` найти контроллер с ролью PDC в домене \
`Get-ADDomainController -Filter * | ft HostName,IPv4Address,Name,Site,OperatingSystem,IsGlobalCatalog` список все DC, принадлежность к сайту, версии ОС и GC

При загрузке ОС служба NetLogon делает DNS запрос со списком контроллеров домена (к SRV записи _ldap._tcp.dc._msdcs.domain_), DNS возвращает список DC в домене с записью Service Location (SRV). Клиент делает LDAP запрос к DC для определения сайта AD по своему IP адресу. Клиент через DNS запрашивает список контроллеров домена в сайте (в разделе _tcp.sitename._sites...).

USN (Update Sequence Numbers) - счетчик номера последовательного обновления, который существует у каждого объекта AD. При репликации контроллеры обмениваются значениями USN, объект с более низким USN будет при репликации перезаписан объектом с более высоким USN. Находится в свойствах - Object (включить View - Advanced Features). Каждый контроллер домена содержит отдельный счетчик USN, который начинает отсчет в момент запуска процесса Dcpromo и продолжает увеличивать значения в течение всего времени существования контроллера домена. Значение счетчика USN увеличивается каждый раз, когда на контроллере домена происходит транзакция, это операции создания, обновления или удаления объекта.

`Get-ADDomainController -Filter * | % {` отобразить USN объекта на всех DC в домене` \
`Get-ADUser -Server $_.HostName -Identity support4 -Properties uSNChanged | select SamAccountName,uSNChanged` \
`}`

`dcpromo /forceremoval` принудительно выполнит понижение в роли контроллера домена до уровня рядового сервера. После понижения роли выполняется удаление всех ссылок в домене на этот контроллер. Далее производит включение сервера в состав домена, и выполнение обратного процесса, т.е. повышение сервера до уровня контроллера домена.

## ADComputer

`nltest /DSGETDC:$env:userdnsdomain` узнать на каком DC аудентифицирован хост (Logon Server) \
`nltest /SC_RESET:$env:userdnsdomain\srv-dc2.$env:userdnsdomain` переключить компьютер на другой контроллер домена AD вручную (The command completed successfully) \
`Get-ADComputer –Identity $env:computername -Properties PasswordLastSet` время последней смены пароля на сервере \
`Test-ComputerSecureChannel –verbose` проверить доверительные отношения с доменом (соответствует ли локальный пароль компьютера паролю, хранящемуся в AD) \
`Reset-ComputerMachinePassword -Credential domain\admin` принудительно обновить пароль \
`Netdom ResetPWD /Server:dc-01 /UserD:domain\admin /PasswordD:*` сбросить хэш пароля компьютера в домене (перезагрузка не требуется) \
`Search-ADAccount -AccountDisabled -ComputersOnly | select Name,LastLogonDate,Enabled` отобразить все отключенные компьютеры

`Get-ADComputer -Filter * -Properties * | select name` список всех компьютеров в домене (Filter), вывести все свойства (Properties) \
`Get-ADComputer -Identity $srv -Properties * | ft Name,LastLogonDate,PasswordLastSet,ms-Mcs-AdmPwd -Autosize` конкретного компьютера в AD (Identity) \
`Get-ADComputer -SearchBase "OU=Domain Controllers,DC=$d1,DC=$d2" -Filter * -Properties * | ft Name, LastLogonDate, distinguishedName -Autosize` поиск в базе по DN (SearchBase)

`(Get-ADComputer -Filter {enabled -eq "true"}).count` получить общее количество активных (незаблокированных) компьютеров \
`(Get-ADComputer -Filter {enabled -eq "true" -and OperatingSystem -like "*Windows Server 2016*"}).count` кол-во активных копьютеров с ОС WS 2016

`Get-ADComputer -Filter * -Properties * | select @{Label="Ping Status"; Expression={` \
`$ping = ping -n 1 -w 50 $_.Name` \
`if ($ping -match "TTL") {"Online"} else {"Offline"}` \
`}},` \
`@{Label="Status"; Expression={` \
`if ($_.Enabled -eq "True") {$_.Enabled -replace "True","Active"} else {$_.Enabled -replace "False","Blocked"}` \
`}}, Name, IPv4Address, OperatingSystem, @{Label="UserOwner"; Expression={$_.ManagedBy -replace "(CN=|,.+)"}` \
`},Created | Out-GridView`

## ADUser

`Get-ADUser -Identity support4 -Properties *` список всех атрибутов \
`Get-ADUser -Identity support4 -Properties DistinguishedName, EmailAddress, Description` путь DN, email и описание \
`Get-ADUser -Filter {(Enabled -eq "True") -and (mail -ne "null")} -Properties mail | ft Name,mail` список активных пользователей и есть почтовый ящик \
`Get-ADUser -Filter {SamAccountName -like "*"} | Measure-Object` посчитать кол-во всех аккаунтов (Count) \
`Get-ADUser -Filter * -Properties WhenCreated | sort WhenCreated | ft Name, whenCreated` дата создания \
`Get-ADUser -Identity support4 -property LockedOut | select samaccountName,Name,Enabled,Lockedout` \
`Enabled=True` учетная запись включена - да \
`Lockedout=False` учетная запись заблокирована (например, политикой паролей) - нет \
`Get-ADUser -Identity support4 | Unlock-ADAccount` разблокировать учетную запись \
`Disable-ADAccount -Identity support4` отключить учетную запись \
`Enable-ADAccount -Identity support4` включить учетную запись \
`Search-ADAccount -LockedOut` найти все заблокированные учетные записи \
`Search-ADAccount -AccountDisabled | select Name,LastLogonDate,Enabled` отобразить все отключенные учетные записи с временем последнего входа

`Get-ADUser -Identity support4 -Properties PasswordLastSet,PasswordExpired,PasswordNeverExpires` \
`PasswordLastSet` время последней смены пароля \
`PasswordExpired=False` пароль истек - нет \
`PasswordNeverExpires=True` срок действия пароля не истекает - да \
`Set-ADAccountPassword support4 -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "password" -Force -Verbose)` изменить пароль учетной записи \
`Set-ADUser -Identity support4 -ChangePasswordAtLogon $True` смена пароля при следующем входе в систему

`$day = (Get-Date).adddays(-90)` \
`Get-ADUser -filter {(passwordlastset -le $day)} | ft` пользователи, которые не меняли свой пароль больше 90 дней

`$day = (Get-Date).adddays(-30)` \
`Get-ADUser -filter {(Created -ge $day)} -Property Created | select Name,Created` Новые пользователи за 30 дней

`$day = (Get-Date).adddays(-360)` \
`Get-ADUser -Filter {(LastLogonTimestamp -le $day)} -Property LastLogonTimestamp | select Name,SamAccountName,@{n='LastLogonTimestamp';e={[DateTime]::FromFileTime($_.LastLogonTimestamp)}} | sort -Descending LastLogonTimestamp` пользователи, которые не логинились больше 360 дней. Репликация атрибута LastLogonTimestamp составляет от 9 до 14 дней. \
`| Disable-ADAccount $_.SamAccountName` заблокировать \
`-WhatIf` отобразить вывод без применения изменений

## ADGroupMember

`(Get-ADUser -Identity support4 -Properties MemberOf).memberof` список групп в которых состоит пользователь \
`Get-ADGroupMember -Identity "Domain Admins" | Select Name,SamAccountName` список пользователей в группе \
`Add-ADGroupMember -Identity "Domain Admins" -Members support5` добавить в группу \
`Remove-ADGroupMember -Identity "Domain Admins" -Members support5 -force` удалить из группы \
`Get-ADGroup -filter * | where {!($_ | Get-ADGroupMember)} | Select Name` отобразить список пустых групп (-Not)

## ADReplication

`Get-Command -Module ActiveDirectory -Name *Replication*` список всех командлетов модуля \
`Get-ADReplicationFailure -Target dc-01` список ошибок репликации с партнерами \
`Get-ADReplicationFailure -Target $env:userdnsdomain -Scope Domain` \
`Get-ADReplicationPartnerMetadata -Target dc-01 | select Partner,LastReplicationAttempt,LastReplicationSuccess,LastReplicationResult,LastChangeUsn` время последней и время успешной репликации с партнерами \
`Get-ADReplicationUpToDatenessVectorTable -Target dc-01` Update Sequence Number (USN) увеличивается каждый раз, когда на контроллере домена происходит транзакция (операции создания, обновления или удаления объекта), при репликации DC обмениваются значениями USN, объект с более низким USN при репликации будет перезаписан высоким USN.

# repadmin

`repadmin /replsummary` отображает время последней репликации на всех DC по направлению (Source и Destination) и их состояние с учетом партнеров \
`repadmin /showrepl $srv` отображает всех партнеров по реплкации и их статус для всех разделов Naming Contexts (DC=ForestDnsZones, DC=DomainDnsZones, CN=Schema, CN=Configuration) \
`repadmin /replicate $srv2 $srv1 DC=domain,DC=local ` выполнить репликацию с $srv1 на $srv2 только указанный раздела домена \
`repadmin /SyncAll /AdeP` запустить межсайтовую исходящую репликацию всех разделов от текущего сервера со всеми партнерами по репликации \
`/A` выполнить для всех разделов NC \
`/d` в сообщениях идентифицировать серверы по DN (вместо GUID DNS - глобальным уникальным идентификаторам) \
`/e` межсайтовая синхронизация (по умолчанию синхронизирует только с DC текущего сайта) \
`/P` извещать об изменениях с этого сервера (по умолчанию: опрашивать об изменениях) \
`repadmin /Queue $srv` отображает кол-во запросов входящей репликации (очередь), которое необходимо обработать (причиной может быть большое кол-во партнеров или формирование 1000 объектов скриптом) \
`repadmin /showbackup *` узнать дату последнего Backup

`Error: 1722` сервер rpc недоступен (ошибка отката репликации). Проверить имя домена в настройках сетевого адаптера, первым должен идти адрес DNS-сервера другого контроллера домена, вторым свой адрес. \
`Get-Service -ComputerName $srv | select name,status | ? name -like "RpcSs"` \
`Get-Service -ComputerName $srv -Name RpcSs -RequiredServices` зависимые службы \
Зависимые службы RPC: \
"Служба сведений о подключенных сетях" - должен быть включен отложенный запуск. Если служба срабатывает до "службы списка сетей", может падать связь с доменом (netlogon) \
"Центр распространения ключей Kerberos" \
"DNS-сервер" \
`nslookup $srv` \
`tnc $srv -p 135` \
`repadmin /retry` повторить попытку привязки к целевому DC, если была ошибка 1722 или 1753 (RPC недоступен)

`repadmin /showrepl $srv` \
`Last attempt @ 2022-07-15 10:46:01 завершена с ошибкой, результат 8456 (0x2108)` при проверки showrepl этого партнера, его ошибка: 8457 (0x2109) \
`Last success @ 2022-07-11 02:29:46` последний успех \
Когда репликация автоматически отключена, ОС записывает в DSA - not writable одно из четырех значений: \
`Path: HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\NTDS\Parameters` \
`Dsa Not Writable` \
`#define DSA_WRITABLE_GEN 1` версия леса несовместима с ОС \
`#define DSA_WRITABLE_NO_SPACE 2` на диске, где размещена база данных Active Directory или файлы журналов (логи), недостаточно свободного места \
`#define DSA_WRITABLE_USNROLLBCK 4` откат USN произошел из-за неправильного отката базы данных Active Directory во времени (восстановление из снапшота) \
`#define DSA_WRITABLE_CORRUPT_UTDV 8` вектор актуальности поврежден на локальном контроллере домена

# dcdiag

`dcdiag /s:<DomainController> [/n:<NamingContext>] [[/u:<domain\user>] [/p:<password>]] [{/a|/e}{/q|/v}] [/f:<LogFile>] [/ferr:<ErrorLog>] [/test:<test>] [/fix]` \
`dcdiag /Test:replications /s:dc-01` отображает ошибки репликации \
`dcdiag /Test:DNS /e /v /q` тест DNS \
`/a` проверка всех серверов данного сайта \
`/e` проверка всех серверов предприятия \
`/q` выводить только сообщения об ошибках \
`/v` выводить подробную информацию \
`/fix` автоматически исправляет ошибки \
`/test:` \
`NetLogons` проверка наличие прав на выполнение репликации \
`Connectivity` проверяет регистрацию DNS для каждого контроллера домена, отправляет тестовый эхо-пакет на каждый контроллер домена и проверяет подключение по протоколам LDAP и RPC к каждому контроллеру домена \
`Services` проверяет работоспособность всех служб, необходимых для работы контроллера домена, на указанном контроллере домена \
`Systemlog` проверяет наличие ошибок в журналах контроллера домена \
`FRSEvent` проверяет ошибки репликации в работе службы репликации файлов, что может означать наличие проблем в репликации SYSVOL и, таким образом, целостности копий объектов групповых политик \
`FSMOCheck` не проверяет роли хозяев операций, а вместо этого запрашивает сервер глобального каталога, первичный контроллер домена, предпочтительный сервер времени, сервер времени и центр распространения ключей (контроллер домена может подключиться к KDC, PDC, серверу глобального каталога) \
`KnowsOfRoleHolders` пgроверяет возможность подключения контроллеров домена ко всем пяти хозяевам операций (ролями FSMO) \
`MachineAccount` проверяет правильность регистрации учетной записи целевого компьютера и правильность объявлений служб этого компьютера (корректность доверительных отношения с доменом). Если обнаружена ошибка, ее можно исправить с помощью утилиты dcdiag, указав параметры /fixmachineaccount или /recreatemachineaccount \
`Advertising` проверяет, правильно ли контроллер домена сообщает о себе и о своей роли хозяина операций. Этот тест завершиться неудачно, если служба NetLogon не запущена \
`CheckSDRefDom` проверяет правильность доменов ссылок дескрипторов безопасности для каждого раздела каталогов программ \
`CrossRefValidation` проверяет правильность перекрестных ссылок для доменов \
`RRSSysvol` проверяет состояние готовности для FRS SYSVOL \
`Intersite` проверяет наличие ошибок, которые могут помешать нормальной репликации между сайтами. Компания Microsoft предупреждает, что иногда результаты этого теста могут оказаться неточными \
`KCCEvent` проверяет безошибочность создания объектов соединений для репликации между сайтами \
`NCSecDesc` проверяет правильность разрешений для репликации в дескрипторах безопасности для заголовков контекста именования \
`ObjectsReplicated` проверяет правильность репликации агента сервера каталогов и объектов учетных записей компьютеров \
`OutboundSecureChannels` проверяется наличие безопасных каналов между всеми контроллерами домена в интересующем домене \
`Replications` проверяет возможность репликации между контроллерами домена и сообщает обо всех ошибках при репликации \
`RidManager` проверяет работоспособность и доступность хозяина относительных идентификаторов \
`VerifyEnterpriseReferences` проверяет действительность системных ссылок службы репликации файлов для всех объектов на всех контроллерах домена в лесу \
`VerifyReferences` проверяет действительность системных ссылок службы репликации файлов для всех объектов на указанном контроллере домена \
`VerifyReplicas` проверяет действительность всех разделов каталога приложения на всех серверах, принимающих участие в репликации

# ntdsutil

Перенос БД AD (ntds.dit): \
`Get-Acl C:\Windows\NTDS | Set-Acl D:\AD-DB` скопировать NTFS разрешения на новый каталог \
`Stop-Service -ComputerName dc -name NTDS` остановить службу Active Directory Domain Services \
`ntdsutil` запустить утилиту ntdsutil \
`activate instance NTDS` выбрать активный экземпляр базы AD \
`files` перейдем в контекст files, в котором возможно выполнение операция с файлами базы ntds.dit \
`move DB to D:\AD-DB\` перенести базу AD в новый каталог (предварительно нужно его создать) \
`info` проверить, что БД находится в новом каталоге \
`move logs to D:\AD-DB\` переместим в тот же каталог файлы с журналами транзакций \
`quit` \
`Start-Service -ComputerName dc -name NTDS`

Сброс пароля DSRM (режим восстановления служб каталогов):  \
`ntdsutil` \
`set dsrm password` \
`reset password on server NULL` \
новый пароль \
подтверждение пароля \
`quit` \
`quit`

Синхронизировать с паролем УЗ в AD: \
`ntdsutil` \
`set dsrm password` \
`sync from domain account dsrmadmin` \
`quit` \
`quit`

Ошибка 0x00002e2 при загрузке ОС. \
Загрузиться в режиме восстанавления WinRE (Windows Recovery Environment) - Startup Settings - Restart - DSRM (Directory Services Restore Mode) \
`reagentc /boottore` shutdown /f /r /o /t 0 перезагрузка в режиме WinRE - ОС на базе WinPE (Windows Preinstallation Environment), образ winre.wim находится на скрытом разделе System Restore \
На контроллере домена единственная локальная учетная запись — администратор DSRM. Пароль создается при установке роли контроллера домена ADDS на сервере (SafeModeAdministratorPassword). \
`ntdsutil` \
`activate instance ntds` \
`Files` \
`Info` \
`integrity` проверить целостность БД \
Ошибка: Failed to open DIT for AD DS/LDS instance NTDS. Error -2147418113 \
`mkdir c:\ntds_bak` \
`xcopy c:\Windows\NTDS\*.* c:\ntds_bak` backup содержимого каталога с БД \
`esentutl /g c:\windows\ntds\ntds.dit` проверим целостность файла \
Вывод: Integrity check completed. Database is CORRUPTED ошибка, база AD повреждена \
`esentutl /p c:\windows\ntds\ntds.dit` исправить ошибки \
Вывод: Operation completed successfully in xx seconds. нет ошибок \
`esentutl /g c:\windows\ntds\ntds.dit` проверим целостность файла \
Выполнить анализ семантики базы с помощью ntdsutil: \
`ntdsutil` \
`activate instance ntds` \
`semantic database analysis` \
`go` \
`go fixup` исправить семантические ошибки \
Сжать файл БД: \
`activate instance ntds` \
`files` \
`compact to C:\Windows\NTDS\TEMP` \
`copy C:\Windows\NTDS\TEMP\ntds.dit C:\Windows\NTDS\ntds.dit` заменить оригинальный файл ntds.dit \
`Del C:\Windows\NTDS\*.log` удалить все лог файлы из каталога NTDS

# GPO

`Get-Command -Module GroupPolicy` \
`Get-GPO -Domain domain.local -All | ft` \
`Get-GPO -Name LAPS` \
`[xml](Get-GPOReport LAPS -ReportType Xml)` \
`Get-GPPermission -Name LAPS -All` \
`Get-GPO LAPS | New-GPLink -Target "ou=servers,dc=domain,dc=local"` \
`Set-GPLink -Name LAPS -Target "ou=servers,dc=domain,dc=local" -LinkEnabled No` \
`Backup-GPO -Name LAPS -Path "$home\Desktop"` \
`Backup-GPO -All -Path "$home\Desktop"` \
`Restore-GPO -Name LAPS -Path C:\Backup\GPOs\`

# MS Exchange

`$srv_cas = "exchange-cas"` \
`$session_exchange = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri http://$srv_cas/PowerShell/` -Credential $Cred -Authentication Kerberos \
`Get-PSSession` \
`Import-PSSession $session_exchange -DisableNameChecking` импортировать в текущую сессию

`Get-ExchangeServer | select name,serverrole,admindisplayversion,Edition,OriginatingServer,WhenCreated,WhenChanged,DataPath | ft` список всех серверов

`Get-ImapSettings` настройки IMAP \
`Get-ExchangeCertificate` список сертификатов \
`Get-ExchangeCertificate -Thumbprint "5CEC8544D4743BC279E5FEA1679F79F5BD0C2B3A" | Enable-ExchangeCertificate -Services  IMAP, POP, IIS, SMTP` \
`iisreset` \
`Get-ClientAccessService | fl identity, *uri*` настройки службы автообнаружения в Exchange 2016 \
`Get-ClientAccessService -Identity $srv | Set-ClientAccessService -AutoDiscoverServiceInternalUri https://mail.domain.ru/Autodiscover/Autodiscover.xml` изменить на внешний адрес \
`Get-OutlookAnywhere` OA позволяет клиентам Outlook подключаться к своим почтовым ящикам за пределами локальной сети (без использования VPN) \
`Get-WebServicesVirtualDirectory` \
`Get-OwaVirtualDirectory` \
`Get-ActiveSyncVirtualDirectory` \
`Get-OabVirtualDirectory` виртуальная директория автономной адресной книги \
`Get-OabVirtualDirectory -Server $srv | Set-OabVirtualDirectory -InternalUrl "https://mail.domain.ru/OAB" -ExternalUrl "https://mail.domain.ru/OAB"`

## Roles

MS (Mailbox) - сервер с БД почтовых ящиков и общих папок, отвечает только за их размещение и не выполняет маршрутизацию никаких сообщений. \
CAS (Client Access Server) - обработка клиентских подключений к почтовым ящикам, которые создаются клиентами Outlook Web Access (HTTP для Outlook Web App), Outlook Anywhere, ActiveSync (для мобильных устройств), интернет протоколы POP3 и IMAP4, MAPI для клиентов Microsoft Outlook. \
Hub Transort - ответвечает за маршрутизацию сообщений интернета и инфраструктурой Exchange, а также между серверами Exchange. Сообщения всегда маршрутизируются с помощью роли транспортного сервера-концентратора, даже если почтовые ящики источника и назначения находятся в одной базе данных почтовых ящиков. \
Relay - роль пограничного транспортного сервера (шлюз SMTP в периметре сети).

SCP (Service Connection Point) - запись прописывается в AD, при создание сервера CAS. Outlook запрашивает SCP, выбирает те, которые находятся в одном сайте с ним и по параметру WhenCreated – по дате создания, выбирая самый старый. \
Autodiscover. Outlook выбирает в качестве сервера Client Access тот, который прописан в атрибуте RPCClientAccessServer базы данных пользователя. Сведения о базе данных и сервере mailbox, на котором она лежит, берутся из AD.

## MessageTrackingLog

`Get-MessageTrackingLog -ResultSize Unlimited | select Timestamp,Sender,Recipients,RecipientCount,MessageSubject,Source,EventID,ClientHostname,ServerHostname,ConnectorId, @{Name="MessageSize"; Expression={[string]([int]($_.TotalBytes / 1024))+" KB"}},@{Name="MessageLatency"; Expression={$_.MessageLatency -replace "\.\d+$"}}` \
`Get-MessageTrackingLog -Start (Get-Date).AddHours(-24) -ResultSize Unlimited | where {[string]$_.recipients -like "*@yandex.ru"}` вывести сообщения за последние 24 часа, где получателем был указанный домен \
-Start "04/01/2023 09:00:00" -End "04/01/2023 18:00:00" - поиск по указанному промежутку времени \
-MessageSubject "Тест" - поиск по теме письма \
-Recipients "support4@domain.ru" - поиск по получателю \
-Sender - поиск по отправителю \
-EventID – поиск по коду события сервера (RECEIVE, SEND, FAIL, DSN, DELIVER, BADMAIL, RESOLVE, EXPAND, REDIRECT, TRANSFER, SUBMIT, POISONMESSAGE, DEFER) \
-Server – поиск на определенном транспортном сервере \
-messageID – трекинг письма по его ID

## Mailbox

`Get-Mailbox -Database "it2"` список почтовых серверов в базе данных \
`Get-Mailbox -resultsize unlimited | ? Emailaddresses -like "support4" | format-list name,emailaddresses,database,servername` какую БД, сервер и smtp-адреса использует почтовый ящик \
`Get-Mailbox -Database $db_name -Archive` отобразить архивные почтовые ящики

`Get-MailboxFolderStatistics -Identity "support4" -FolderScope All | select Name,ItemsInFolder,FolderSize` отобразить кол-во писем и размер в каждой папке \
`Get-MailboxStatistics "support4" | select DisplayName,LastLoggedOnUserAccount,LastLogonTime,LastLogoffTime,ItemCount,TotalItemSize,DeletedItemCount,TotalDeletedItemSize,Database,ServerName` общее кол-во писем, их размер, время последнего входа и выхода, имя сервера и БД \
`Get-Mailbox -Server s2 | Get-MailboxStatistics | where {$_.Lastlogontime -lt (get-date).AddDays(-30)} | Sort Lastlogontime -desc | ft displayname,Lastlogontime,totalitemsize` ящики, которые не использовались 30 и более дней

`Enable-Mailbox -Identity support9 -Database test_base` создать почтовый ящик для существующего пользователя в AD \
`New-Mailbox -Name $login -UserPrincipalName "$login@$domain" -Database $select_db -OrganizationalUnit $path -Password (ConvertTo-SecureString -String "$password" -AsPlainText -Force)` создать новый почтовый ящик без привязки к пользователю AD \
`Get-MailboxDatabase -Database $db_name | Remove-MailboxDatabase` удалить БД

`Set-MailBox "support4" -PrimarySmtpAddress support24@domain.ru -EmailAddressPolicyEnabled $false` добавить и изменить основной SMTP-адрес электронной почты для пользователя \
`Set-Mailbox -Identity "support4" -DeliverToMailboxAndForward $true -ForwardingSMTPAddress "username@outlook.com"` включить переадресацию почты (электронная почта попадает в почтовый ящик пользователя support4 и одновременно пересылается по адресу username@outlook.com)

## MoveRequest

`Get-Mailbox -Database $db_in | New-MoveRequest -TargetDatabase $db_out` переместить все почтовые ящики из одной БД в другую \
`New-MoveRequest -Identity $db_in -TargetDatabase $db_out` переместить один почтовый ящик \
`Get-MoveRequest | Suspend-MoveRequest` остановить запросы перемещения \
`Get-MoveRequest | Remove-MoveRequest` удалить запросы на перемещение \
`Get-MoveRequest | Get-MoveRequestStatistics` статус перемещения

Status: \
Cleanup - нужно подождать \
Queued - в очереди \
InProgress - в процессе \
Percent Complete - процент выполнения \
CompletionInProgress - завершение процесса \
Completed - завершено

`Remove-MoveRequest -Identity $db_name` завершить процесс перемещения (убрать статус перемещения с почтового ящика и очистить список перемещений) \
`Get-MailboxDatabase | Select Name, MailboxRetention` после перемещения ящиков, размер базы не изменится, полное удаление из базы произойдет, как пройдет количество дней, выставленное в параметре MailboxRetention \
`Set-MailboxDatabase -MailboxRetention '0.00:00:00' -Identity $db_name` изменить значение

## Archive

`Enable-Mailbox -Identity $name -Archive` включить архив для пользователя \
`Get-Mailbox $name | New-MoveReques –ArchiveOnly –ArchiveTargetDatabase DBArch` переместить архивный почтовый ящик в другую БД \
`Get-Mailbox $name | fl Name,Database,ArchiveDatabase` место расположения БД пользователя и БД его архива \
`Disable-Mailbox -Identity $name -Archive` отключить архив \
`Connect-Mailbox -Identity "8734c04e-981e-4ccf-a547-1c1ac7ebf3e2" -Archive -User $name -Database it2` подключение архива пользователя к указанному почтовому ящику \
`Get-Mailbox $name | Set-Mailbox -ArchiveQuota 20GB -ArchiveWarningQuota 19GB` настроить квоты хранения архива

## Quota

`Get-Mailbox -Identity $mailbox | fl IssueWarningQuota, ProhibitSendQuota, ProhibitSendReceiveQuota, UseDatabaseQuotaDefaults` отобразить квоты почтового ящика \
IssueWarningQuota — квота, при достижении которой Exchange отправит уведомление \
ProhibitSendQuota — при достижении будет запрещена отправка \
ProhibitSendReceiveQuota — при достижении будет запрещена отправка и получение \
UseDatabaseQuotaDefaults — используется ли квота БД или false - индвидиуальные \
`Set-Mailbox -Identity $mailbox -UseDatabaseQuotaDefaults $false -IssueWarningQuota "3 GB" -ProhibitSendQuota "4 GB" -ProhibitSendReceiveQuota "5 GB"` задать квоту для пользователя

`Get-MailboxDatabase $db_name | fl Name, *Quota` отобразить квоты наложенные на БД \
`Set-MailboxDatabase $db -ProhibitSendReceiveQuota "5 GB" -ProhibitSendQuota "4 GB" -IssueWarningQuota "3 GB"` настроить квоты на БД

## Database

`Get-MailboxDatabase -Status | select ServerName,Name,DatabaseSize` список и размер всех БД на всех MX-серверах \
`New-MailboxDatabase -Name it_2022 -EdbFilePath E:\Bases\it_2022\it_2022.edb -LogFolderPath G:\Logs\it_2022 -OfflineAddressBook "Default Offline Address List" -server exch-mx-01` создать БД \
`Restart-Service MSExchangeIS` \
`Get-Service | Where {$_ -match "exchange"} | Restart-Service` \
`Get-MailboxDatabase -Server exch-01` список баз данных на MX-сервере \
`New-MoveRequest -Identity "support4" -TargetDatabase it_2022` переместить почтовый ящик в новую БД \
`Move-Databasepath $db_name –EdbFilepath "F:\DB\$db_name\$db_name.edb" –LogFolderpath "E:\DB\$db_name\logs\"` переместить БД и транзакционные логи на другой диск \
`Set-MailboxDatabase -CircularLoggingEnabled $true -Identity $db_name` включить циклическое ведение журнала (Circular Logging), где последовательно пишутся 4 файла логов по 5 МБ, после чего первый лог-файл перезаписывается \
`Set-MailboxDatabase -CircularLoggingEnabled $false -Identity $db_name` отключить циклическое ведение журнала \
`Get-MailboxDatabase -Server "exch-mx-01" -Status | select EdbFilePath,LogFolderPath,LogFilePrefix` путь к БД, логам, имя текущего актуального лог-файла

## MailboxRepairRequest

`New-MailboxRepairRequest -Database it2 -CorruptionType ProvisionedFolder, SearchFolder, AggregateCounts, Folderview` запустить последовательный тест (в конкретный момент времени не доступен один почтовый ящик) и исправление ошибок на прикладном уровне \
`Get-MailboxRepairRequest -Database it2` прогресс выполнения \
Позволяет исправить: \
ProvisionedFolder – нарушения логической структуры папок \
SearchFolder – ошибки в папках поиска \
AggregateCounts – проверка и исправление информации о количестве элементов в папках и их размере \
FolderView – неверное содержимое, отображаемое представлениями папок

## eseutil

При отправке/получении любого письма Exchange сначала вносит информацию в транзакционный лог, и только потом сохраняет элемент непосредственно в базу данных. Размер одного лог файла - 1 Мб. Есть три способа урезания логов: DAG, Backup на базе Volume Shadow Copy, Circular Logging.

Ручное удаление журналов транзакций: \
`cd E:\MS_Exchange_2010\MailBox\Reg_v1_MailBoxes\` перейти в каталог с логами \
`ls E*.chk` узнать имя файла, в котором находится информация из контрольной точки фиксации журналов \
`eseutil /mk .\E18.chk` узнать последний файл журнала, действия из которого были занесены в БД Exchange \
`Checkpoint: (0x561299,8,16)` 561299 имя файла, который был последним зафиксирован (его информация уже в базе данных) \
Находим в проводнике файл E0500561299.txt, можно удалять все файлы журналов, которые старше найденного файла

Восстановление БД (если две копии БД с ошибкой): \
`Get-MailboxDatabaseCopyStatus -Identity db_name\* | Format-List Name,Status,ContentIndexState` \
Status            : FailedAndSuspended \
ContentIndexState : Failed \
Status            : Dismounted \
ContentIndexState : Failed

`Get-MailboxDatabase -Server exch-mx-01 -Status | fl Name,EdbFilePath,LogFolderPath` проверить расположение базы и транзакционных логов \
LogFolderPath - директория логов \
E18 - имя транкзакционного лога (из него читаются остальные логи) \
`dismount-Database db_name` отмантировать БД \
`eseutil /mh D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb` проверить базу \
State: Dirty Shutdown - несогласованное состояние, означает, что часть транзакций не перенесена в базу, например, после того, как была осуществлена аварийная перезагрузка сервера. \
`eseutil /ml E:\MS_Exchange_2010\MailBox\db_name\E18` проверка целостности транзакционных логи, если есть логи транзакций и они не испорчены, то можно восстановить из них, из файла E18 считываются все логи, должен быть статус - ОК

Soft Recovery (мягкое восстановление) - необходимо перевести базу в состояние корректного отключения (Clear shutdown) путем записи недостающих файлов журналов транзакций в БД. \
`eseutil /R E18 /l E:\MS_Exchange_2010\MailBox\db_name\ /d D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb` \
`eseutil /R E18 /a /i /l E:\MS_Exchange_2010\MailBox\db_name\ /d D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb` если с логами что-то не так, можно попробовать восстановить базу игнорируя ошибку в логах \
`eseutil /mk D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb` cостоянии файла контрольных точек \
`eseutil /g D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb` проверка целостности БД \
`eseutil /k D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb` проверка контрольных сумм базы (CRC)

Hard Recovery - если логи содержат ошибки и база не восстанавливается, то восстанавливаем базу без логов. \
`eseutil /p D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb` \
/p - удалит поврежденные страницы, эта информация будет удалена из БД и восстановит целостность \
`esetuil /d D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb` выполнить дефрагментацию (если был потерян большой объем данных, то может сильно снизиться производительность) \
После выполнения команд необходимо вручную удалить все файлы с расширением log в папке MDBDATA, перед попыткой смонтировать базу данных. \
`isinteg -s "db_name.edb" -test alltests` проверьте целостность базы данных \
`isinteg -s "server_name" -fix -test -alltests` если проверка будет провалена. Выполнять команду до тех пор, пока у всех ошибок не станет статус 0 или статус не перестанет меняться, иногда необходимо 3 прохода для достижения результата. \
`eseutil /mh D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb | Select-String -Pattern "State:","Log Required:"` проверить статус \
State: Clear shutdown - успешный статус \
`Log Required` требуются ли файлы журналов, необходимые базе, чтобы перейти в согласованное состояние. Если база размонтирована корректно, то это значение будет равняться 0. \
`mount-Database -force db_name` примонтировать БД \
`Get-MailboxDatabase –Status db_name | fl Mounted` статус БД \
`New-MailboxRepairRequest -Database db_name -CorruptionType SearchFolder,AggregateCounts,ProvisionedFolder,FolderView` восстановление логической целостности данных \
После этого восстановить Index. \
Если индексы не восстанавливаются, но БД монтируется, то перенести почтовые ящики в новую БД.

Восстановление БД из Backup:

1-й вариант:
1. Отмантировать текущую БД и удалить или переименовать директорию с файлами текущей БД.
3. Восстановить в ту же директорию из Backup базу с логами.
4. Запустить мягкое восстановление БД (Soft Recovery).
5. Примониторвать.

2-й вариант:
1. Отмантировать и удалить текущую БД.
2. Восстановить БД с логами из Backup в любое место.
3. Запустить мягкое восстановление БД (Soft Recovery).
4. Создать новую БД.
5. Создать Recovery Database и смонтировать в нее восстановленную из бэкапа БД, скопировать из неё почтовые ящики в новую БД и переключить на них пользователей.
6. Если использовать Dial Tone Recovery, то так же перенести из временной БД промежуточные данные почтовых ящиков.

3-й вариант:
1. Восстановить целостность Soft Repair или Hard Recovery.
2. Создать новую БД. Указывать в свойствах: «база может быть перезаписана при восстановлении».
3. Если база была только что оздана и еще не была подмонтирована, то эта папка будет пуста, туда перемещаем базу из Backup, которая была обработана ESEUTIL вместе со всеми файлами. Указать имя .edb такое же, которое было при создании новой базы.
4. Монтируем базу.
5. Перенацеливаем ящики со старой (Mailbox_DB_02), неисправной базы, на новую базу (Mailbox_DB_02_02):
`Get-Mailbox -Database Mailbox_DB_02 | where {$_.ObjectClass -NotMatch '(SystemAttendantMailbox|ExOleDbSystemMailbox)'} | Set-Mailbox -Database Mailbox_DB_02_02`
6. Восстановление логической целостности данных:
`New-MailboxRepairRequest -Database "Mailbox_DB_02_02" -CorruptionType ProvisionedFolder, SearchFolder, AggregateCounts, Folderview`

## Dial Tone Recovery

`Get-Mailbox -Database "MailboxDB" | Set-Mailbox -Database "TempDB"` перенацелить ящики с одной БД (нерабочей) на другую (пустую) \
`Get-Mailbox -Database TempDB` отобразить почтовые ящики в БД TempDB \
`Restart-Service MSExchangeIS` перезапустить службу Mailbox Information Store (банка данных), иначе пользователи будут по-прежнему пытаться подключиться к старой БД \
`iisreset` \
`Get-Mailbox -Database "TempDB" | Set-Mailbox -Database "MailboxDB"` после восстановления старой БД, нужно переключить пользователей с временной БД обратно \
После этого сделать слияние с временной БД с помощью Recovery.

## Recovery database (RDB)

`New-MailboxDatabase –Recovery –Name RecoveryDB –Server $exch_mx –EdbFilePath "D:\TempDB\TempDB.edb" -LogFolderPath "D:\TempDB"` для переноса новых писем из временной БД в основную необходим только сам файл TempDB.edb со статусом Clean Shutdown, из нее необходимо создать служебную БД (ключ -Recovery) \
`Mount-Database "D:\TempDB\TempDB.edb"` примонтировать БД \
`Get-MailboxStatistics -Database RecoveryDB` \
`New-MailboxRestoreRequest –SourceDatabase RecoveryDB –SourceStoreMailbox support –TargetMailbox support` скопировать данные почтового ящика с DisplayName: support из RecoveryDB в почтовый ящик с псевдонимом support существующей базы. По умолчанию ищет в почтовой базе совпадающие LegacyExchangeDN либо проверяет совпадение адреса X500, если нужно восстановить данные в другой ящик, нужно указывать ключ -AllowLegacyDNMisMatch \
`New-MailboxRestoreRequest –SourceDatabase RecoveryDB –SourceStoreMailbox support –TargetMailbox support –TargetRootFolder "Restore"` скопировать письма в отдельную папку в ящике назначения (создается автоматически), возможно восстановить содержимое конкретной папки -IncludeFolders "#Inbox#" \
`Get-MailboxRestoreRequest | Get-MailboxRestoreRequestStatistics` статус запроса восстановления \
`Get-MailboxRestoreRequestStatistics -Identity support` \
`Get-MailboxRestoreRequest -Status Completed | Remove-MailboxRestoreRequest` удалить все успешные запросы

## Transport

`Get-TransportServer $srv_cas | select MaxConcurrentMailboxDeliveries,MaxConcurrentMailboxSubmissions,MaxConnectionRatePerMinute,MaxOutboundConnections,MaxPerDomainOutboundConnections,PickupDirectoryMaxMessagesPerMinute` настройки пропускной способности транспортного сервера \
MaxConcurrentMailboxDeliveries — максимальное количество одновременных потоков, которое может открыть сервер для отправки писем. \
MaxConcurrentMailboxSubmissions — максимальное количество одновременных потоков, которое может открыть сервер для получения писем. \
MaxConnectionRatePerMinute — максимальное возможная скорость открытия входящих соединений в минуту. \
MaxOutboundConnections — максимальное возможное количество соединений, которое может открыть Exchange для отправки. \
MaxPerDomainOutboundConnections — максимальное возможное количество исходящих соединений, которое может открыть Exchange для одного удаленного домена. \
PickupDirectoryMaxMessagesPerMinute — скорость внутренней обработки сообщений в минуту (распределение писем по папкам). \
`Set-TransportServer exchange-cas -MaxConcurrentMailboxDeliveries 21 -MaxConcurrentMailboxSubmissions 21 -MaxConnectionRatePerMinute 1201 -MaxOutboundConnections 1001 -MaxPerDomainOutboundConnections 21 -PickupDirectoryMaxMessagesPerMinute 101` изменить значения

`Get-TransportConfig | select MaxSendSize, MaxReceiveSize` ограничение размера сообщения на уровне траспорта (наименьший приоритет, после коннектора и почтового ящика). \
`New-TransportRule -Name AttachmentLimit -AttachmentSizeOver 15MB -RejectMessageReasonText "Sorry, messages with attachments over 15 MB are not accepted"` создать транспортное правило для проверки размера вложения

## Connector

`Get-ReceiveConnector | select Name,MaxMessageSize,RemoteIPRanges,WhenChanged` ограничения размера сообщения на уровне коннектора (приоритет ниже, чем у почтового ящика) \
`Set-ReceiveConnector ((Get-ReceiveConnector).Identity)[-1] -MaxMessageSize 30Mb` изменить размер у последнего коннектора в списке (приоритет выше, чем у траспорта) \
`Get-Mailbox "support4" | select MaxSendSize, MaxReceiveSize` наивысший приоритет \
`Set-Mailbox "support4" -MaxSendSize 30MB -MaxReceiveSize 30MB` изменить размер

`Set-SendConnector -Identity "ConnectorName" -Port 26` изменить порт коннектора отправки \
`Get-SendConnector "proxmox" | select port`

`Get-ReceiveConnector | select Name,MaxRecipientsPerMessage` по умолчанию Exchange принимает ограниченное количество адресатов в одном письме (200) \
`Set-ReceiveConnector ((Get-ReceiveConnector).Identity)[-1] -MaxRecipientsPerMessage 50` изменить значение \
`Set-ReceiveConnector ((Get-ReceiveConnector).Identity)[-1] -MessageRateLimit 1000` задать лимит обработки сообщений в минуту для коннектора

`Get-OfflineAddressbook | Update-OfflineAddressbook` обновить OAB \
`Get-ClientAccessServer | Update-FileDistributionService`

## PST

`New-MailboxExportRequest -Mailbox $name -filepath "\\$srv\pst\$name.PST" # -ContentFilter {(Received -lt "01/01/2021")} -Priority Highest/Lower # -IsArchive` выполнить экспорт из архива пользователя \
`New-MailboxExportRequest -Mailbox $name -IncludeFolders "#Inbox#" -FilePath "\\$srv\pst\$name.PST"` только папку входящие \
`New-MailboxImportRequest -Mailbox $name "\\$srv\pst\$name.PST"` импорт из PST \
`Get-MailboxExportRequest` статус запросов \
`Get-MailboxExportRequest -Status Completed | Remove-MailboxExportRequest` удалить успешно завершенные запросы \
`Remove-MailboxExportRequest -RequestQueue MBXDB01 -RequestGuid 25e0eaf2-6cc2-4353-b83e-5cb7b72d441f` отменить экспорт

## DistributionGroup

`Get-DistributionGroup` список групп рассылки \
`Get-DistributionGroupMember "!_Офис"` список пользователей в группе \
`Add-DistributionGroupMember -Identity "!_Офис" -Member "$name@$domain"` добавить в группу рассылки \
`Remove-DistributionGroupMember -Identity "!_Офис" -Member "$name@$domain"` \
`New-DistributionGroup -Name "!_Тест" -Members "$name@$domain"` создать группу \
`Set-DistributionGroup -Identity "support4" -HiddenFromAddressListsEnabled $true (или Set-Mailbox)` скрыть из списка адресов Exchange

## Search

`Search-Mailbox -Identity "support4" -SearchQuery 'Тема:"Mikrotik DOWN"'` поиск писем по теме \
`Search-Mailbox -Identity "support4" -SearchQuery 'Subject:"Mikrotik DOWN"'`\
`Search-Mailbox -Identity "support4" -SearchQuery 'attachment -like:"*.rar"'`\
`Search-Mailbox -Identity "support4" -SearchQuery "отправлено: < 01/01/2020" -DeleteContent -Force` удаление писем по дате

Формат даты в зависимости от региональных настроек сервера: \
`20/07/2018` \
`07/20/2018` \
`20-Jul-2018` \
`20/July/2018`

## AuditLog

`Get-AdminAuditLogConfig` настройки аудита \
`Set-Mailbox -Identity "support4" -AuditOwner HardDelete` добавить логирование HardDelete писем \
`Set-mailbox -identity "support4" -AuditlogAgelimit 120` указать время хранения \
`Get-mailbox -identity "support4" | Format-list Audit*` данные аудита \
`Search-MailboxAuditLog -Identity "support4" -LogonTypes Delegate -ShowDetails -Start "2022-02-22 18:00" -End "2022-03-22 18:00"` просмотр логов \
`Search-AdminAuditLog -StartDate "02/20/2022" | ft CmdLetName,Caller,RunDate,ObjectModified -Autosize` поиск событий истории выполненых команд в журнале аудита Exchange

## Test

`Test-ServiceHealth` проверить доступность ролей сервера: почтовых ящиков, клиентского доступа, единой системы обмена сообщениями, траспортного сервера \
`$mx_srv_list | %{Test-MapiConnectivity -Server $_}` проверка подключения MX-серверов к БД \
`Test-MAPIConnectivity -Database $db` проверка возможности логина в базу \
`Test-MAPIConnectivity –Identity $user@$domain` проверка возможности логина в почтовый ящик \
`Test-ComputerSecureChannel` проверка работы службы AD \
`Test-MailFlow` результат тестового потока почты

## Queue

`Get-TransportServer | %{Get-Queue -Server $_.Name}` отобразить очереди на всех транспортных серверах \
`Get-Queue -Identity EXCHANGE-CAS\155530 | Format-List` подробная информация об очереди \
`Get-Queue -Identity EXCHANGE-CAS\155530 | Get-Message -ResultSize Unlimited | Select FromAddress,Recipients` отобразить список отправителей (FromAddress) и список получателей в очереди (Recipients) \
`Get-Message -Queue EXCHANGE-CAS\155530` отобразить индентификатор сообщений в конкретной очереди (сервер\очередь\идентификатор письма) \
`Resume-Message EXCHANGE-CAS\155530\444010` повторить отправку письма из очереди \
`Retry-Queue -Filter {Status -eq "Retry"}` принудительно повторить отправку всех сообщений c статусом "Повторить" \
`Get-Queue -Identity EXCHANGE-CAS\155530 | Get-Message -ResultSize unlimited | Remove-Message -WithNDR $False` очистить очередь \
`Get-transportserver EXCHANGE-CAS | Select MessageExpirationTimeout` отобразить время жизни сообщений в очереди (по умолчанию, 2 дня)

Error Exchange 452 4.3.1 Insufficient system resources - окончание свободного места на диске, на котором находятся очереди службы Exchange Hub Transport, за мониторинг отвечает компонент доступных ресурсов Back Pressure, который в том числе отслеживает свободное место на диске \
Порог Medium (90%) — перестать принимать по SMTP почту от внешних отправителей (почта от MAPI клиентов при этом обрабатывается) \
Порог High (99%) — обработка потока почты полностью прекращается \
Решение: очистить, например логи IIS (C:\inetpub\logs\LogFiles\W3SVC1), увеличить размер диска, отключить мониторинг Back Pressure (плохой вариант) или перенести транспортные очередь на другой диск достаточного объёма.

`Get-Service | ? name -like "MSExchangeTransport" | Stop-Service` остановить служу очереди \
`Rename-Item "C:\Program Files\Microsoft\Exchange Server\V15\TransportRoles\data\Queue" "C:\Program Files\Microsoft\Exchange Server\V15\TransportRoles\data\Queue_old"` очистить базу очереди \
`C:\Program Files\Microsoft\Exchange Server\V15\Bin\EdgeTransport.exe.config` конфигурационный файл, который содержит путь к бд с очередью (блок <appSettings> ключи <add key="QueueDatabasePath" value="$new_path" /> и QueueDatabaseLoggingPath) \
Для переноса БД, необходимо переместить существующие файлы базы данных Mail.que и Trn.chk (контрольные точки для отслеживания записи в логах) из исходного местоположения в новое. Переместите существующие файлы журнала транзакций Trn.log, Trntmp.log, Trn nnnn.log , Trnres00001.jrs, Trnres00002.jrs и Temp.edb из старого расположения в новое. tmp.edb — временный файл для проверки схемы самой базы, перености не нужно. \
После запуска службы транспорта удалить старую базу данных очереди и файлы журнала транзакций из старого расположения.

## Defrag

`Get-MailboxDatabase -Status | ft Name, DatabaseSize, AvailableNewMailboxSpace` \
DatabaseSize - текущий размер базы \
AvailableNewMailboxSpace - объём пустых страниц, пространство, которое можно освободить при дефрагментации \
(DatabaseSize — AvailableNewMailboxSpace) x 1,1 - необходимо дополнительно иметь свободного места не менее 110% от текущего размера базы (без учета пустых страниц) \
`cd $path` \
`Dismount-Database "$path\$db_name"` отмонтировать БД \
`eseutil /d "$path\$db_name.edb"` \
`Mount-Database "$path\$db"` примонтировать БД

## DAG (Database Availability Group)

`Install-WindowsFeature -Name Failover-Clustering -ComputerName EXCH-MX-01` основывается на технологии Windows Server Failover Cluster \
`New-DatabaseAvailabilityGroup -Name dag-01 -WitnessServer fs-05 -WitnessDirectory C:\witness_exchange1` создать группу с указанием файлового свидетеля для кворума \
Quorum - это процесс голосования, в котором для принятия решения нужно иметь большинство голосов, что бы сделать текущую копию базы данных активной. \
WitnessDirectory — используется для хранения данных файлового ресурса-свидетеля. \
`Set-DatabaseAvailabilityGroup dag-01 –DatabaseAvailabilityGroupIPAdress $ip` изменить ip-адрес группы \
`Get-DatabaseAvailabilityGroup` список всех групп \
`Get-DatabaseAvailabilityGroup -Identity dag-01` \
`Add-DatabaseAvailabilityGroupServer -Identity dag-01 -MailboxServer EXCH-MX-01` добавить первый сервер (все БД на серверах в DAG должны храниться по одинаковому пути) \
`Add-MailboxDatabaseCopy -Identity db_name -MailboxServer EXCH-MX-04` добавить копию БД \
`Get-MailboxDatabaseCopyStatus -Identity db_name\* | select Name,Status,LastInspectedLogTime` статус и время последнего копирования журнала транзакий

Status: \
Mounted - рабочая база \
Suspended - приостановлено копирование \
Healthy - рабочая пассивная копия \
ServiceDown - недоступна (выключен сервер) \
Dismounted - отмонтирована \
FailedAndSuspended - ошибка и приостановка копирования \
Resynchronizing - процесс синхронизация, где будет постепенно уменьшаться длина очереди \
CopyQueue Length - длина репликационной очереди копирования (0 - значит все изменения из активной базы реплицированы в пассивную копию)

`Resume-MailboxDatabaseCopy -Identity db_name\EXCH-MX-04` возобновить (Resume) или запустить копирование бд на EXCH-MX-04 (из статуса Suspended в Healthy) \
`Suspend-MailboxDatabaseCopy -Identity db_name\EXCH-MX-04` остановить копирование (в статус Suspended) \
`Update-MailboxDatabaseCopy -Identity db_name\EXCH-MX-04 -DeleteExistingFiles` обновить копию БД (сделать Full Backup) \
`Set-MailboxDatabaseCopy -Identity db_name\EXCH-MX-04 -ActivationPreference 1` изменить приоритет для активации копий БД (какую использовать, 1 – самое высокое значение) \
`Move-ActiveMailboxDatabase db_name -ActivateOnServer EXCH-MX-04 -MountDialOverride:None -Confirm:$false` включить копию БД в DAG (переключиться на активную копию) \
`Remove-MailboxDatabaseCopy -Identity db_name\EXCH-MX-04 -Confirm:$False` удалить копии пассивной базы в DAG-группе (у БД должно быть отключено ведение циклического журнала) \
`Remove-DatabaseAvailabilityGroupServer -Identity dag-01 -MailboxServer EXCH-MX-04 -ConfigurationOnly` удалить MX сервер из группы DAG \
`Import-Module FailoverClusters` \
`Get-ClusterNode EXCH-MX-04 | Remove-ClusterNode -Force` удалить отказавший узел из Windows Failover Cluster

`Get-DatabaseAvailabilityGroup | Get-DatabaseAvailabilityGroupHealth` мониторинг

## Index

`Get-MailboxDatabaseCopyStatus * | select name,status,ContentIndexState,ContentIndexErrorMessage,ActiveDatabaseCopy,LatestCopyBackupTime,CopyQueueLength` узнать состояние работы индксов БД и текст ошибки, на каком сервере активная копия БД, дата последней копии и текущая очередь \
`Get-MailboxDatabaseCopyStatus -Identity $db_name\* | Format-List Name,ContentIndexState` отобразить список всех копий конкретной БД на всех серверах, и статус их индексов, если у второго сервера статус Healthy, можно восстановить из него \
`Get-MailboxDatabaseCopyStatus -Identity $db_name\EXCH-MX-04 | Update-MailboxDatabaseCopy -SourceServer EXCH-MX-01 -CatalogOnly` восстановить БД из копии \
`cd %PROGRAMFILES%\Microsoft\Exchange Server\V14\Scripts` или v15 для Exchange 2016 \
`.\ResetSearchIndex.ps1 $db_name` скрипт восстановления индекса

`Get-MailboxDatabaseCopyStatus * | where {$_.ContentIndexState -eq "Failed" -or $_.ContentIndexState -eq "FailedAndSuspended"}` отобразить у какой БД произошел сбой работы (FailedAndSuspended) или индекса (ContentIndexState)

# ServerManager

`Get-Command *WindowsFeature*` source module ServerManager \
`Get-WindowsFeature -ComputerName "localhost"` \
`Get-WindowsFeature | where Installed -eq $True` список установленных ролей и компонентов \
`Get-WindowsFeature | where FeatureType -eq "Role"` отсортировать по списку ролей \
`Install-WindowsFeature -Name DNS` установить роль \
`Get-Command *DNS*` \
`Get-DnsServerSetting -ALL` \
`Uninstall-WindowsFeature -Name DNS` удалить роль

## PSWA

`Install-WindowsFeature -Name WindowsPowerShellWebAccess -IncludeManagementTools` \
`Install-PswaWebApplication -UseTestCertificate` Создать веб-приложение /pswa \
`Add-PswaAuthorizationRule -UserGroupName "$domain\Domain Admins" -ComputerName * -ConfigurationName * -RuleName "For Admins"` добавить права авторизации

## WSB (Windows Server Backup)

При создании backup DC через WSB, создается копия состояния системы (System State), куда попадает база AD (NTDS.DIT), объекты групповых политик, содержимое каталога SYSVOL, реестр, метаданные IIS, база AD CS, и другие системные файлы и ресурсы. Резервная копия создается через службу теневого копирования VSS. \
`Get-WindowsFeature Windows-Server-Backup` проверить установлена ли роль \
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

`Get-Command -Module RemoteDesktop` \
`Get-RDServer -ConnectionBroker $broker` список всех серверов в фермеы, указывается полное доменное имя при обращение к серверу с ролью RDCB \
`Get-RDRemoteDesktop -ConnectionBroker $broker` список коллекций \
`(Get-RDLicenseConfiguration -ConnectionBroker $broker | select *).LicenseServer` список серверов с ролью RDL \
`Get-RDUserSession -ConnectionBroker $broker` список всех активных пользователей \
`Disconnect-RDUser -HostServer $srv -UnifiedSessionID $id -Force` отключить сессию пользователя \
`Get-RDAvailableApp -ConnectionBroker $broker -CollectionName C03` список установленного ПО на серверах в коллекции \
`(Get-RDSessionCollectionConfiguration -ConnectionBroker $broker -CollectionName C03 | select *).CustomRdpProperty` use redirection server name:i:1 \
`Get-RDConnectionBrokerHighAvailability`

# DNSServer

`Get-Command -Module DnsServer` \
`Show-DnsServerCache` отобразить весь кэш DNS-сервера \
`Show-DnsServerCache | where HostName -match ru` \
`Clear-DnsServerCache` \
`Get-DnsServerCache` \
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
`Sync-DnsServerZone –passthru` синхронизировать зоны с другими DC в домене \
`Remove-DnsServerZone -Name domain.local` удалить зону \
`Get-DnsServerResourceRecord -ZoneName domain.local -RRType A` вывести все А-записи в указанной зоне \
`Add-DnsServerResourceRecordA -Name new-host-name -IPv4Address 192.168.1.100 -ZoneName domain.local -TimeToLive 01:00:00 -CreatePtr` создать А-запись и PTR для нее \
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

`dfsutil /root:\\domain.sys\public /export:C:\export-dfs.txt` экспорт конфигурации namespace root \
`dfsutil /AddFtRoot /Server:\\$srv /Share:public` на новой машине предварительно создать корень на основе домена \
`dfsutil /root:\\domain.sys\public /import:C:\export-dfs.txt /<verify /set` Import (перед импортом данных в существующий корень DFS, утилита создает резервную копию конфигурации корня в текущем каталоге, из которого запускается утилита dfsutil) \
`/verify` выводит изменения, которые будут внесены в процессе импорта, без применения \
`/set` меняет целевое пространство имен путем полной перезаписи и замены на конфигурацию пространства имен из импортируемого файла \
`/merge` импортирует конфигурацию пространства имен в дополнение к существующей конфигурации для слияния, параметры из файла конфигурации будут иметь больший приоритет, чем существующие параметры пространства имен

`Export-DfsrClone` экспортирует клонированную базу данных репликации DFS и параметры конфигурации тома \
`Get-DfsrCloneState` получает состояние операции клонирования базы данных \
`Import-DfsrClone` импортирует клонированную базу данных репликации DFS и параметры конфигурации тома

`net use x: \\$srv1\public\*` примонтировать диск \
`Get-DfsrFileHash x:\* | Out-File C:\$srv1.txt` забрать hash всех файлов диска в файл (файлы с одинаковыми хешами всегда являются точными копиями друг друга) \
`net use x: /d` отмонтировать \
`net use x: \\$srv2\public\*` \
`Get-DfsrFileHash x:\* | Out-File C:\$srv2.txt` \
`net use x: /d` \
`Compare-Object -ReferenceObject (Get-Content C:\$srv1.txt) -DifferenceObject (Get-Content C:\$srv2.txt) -IncludeEqual` сравнить содержимое файлов

`Get-DfsrBacklog -DestinationComputerName "fs-06" -SourceComputerName "fs-05" -GroupName "folder-rep" -FolderName "folder" -Verbose` получает список ожидающих обновлений файлов между двумя партнерами репликации DFS \
`Get-DfsrConnection` отображает группы репликации, участников и статус \
`Get-DfsReplicatedFolder` отображает имя и полный путь к папкам реликации в системе DFS \
`Get-DfsrState -ComputerName fs-06 -Verbose` состояние репликации DFS для члена группы \
`Get-DfsReplicationGroup` отображает группы репликации и их статус \
`Add-DfsrConnection` создает соединение между членами группы репликации \
`Add-DfsrMember` добавляет компьютеры в группу репликации \
`ConvertFrom-DfsrGuid` преобразует идентификаторы GUID в понятные имена в заданной группы репликации \
`Get-DfsrConnectionSchedule` получает расписание соединений между членами группы репликации \
`Get-DfsrGroupSchedule` извлекает расписание группы репликации \
`Get-DfsrIdRecord` получает записи ID для реплицированных файлов или папок из базы данных репликации DFS \
`Get-DfsrMember` получает компьютеры в группе репликации \
`Get-DfsrMembership` получает параметры членства для членов групп репликации \
`Get-DfsrPreservedFiles` получает список файлов и папок, ранее сохраненных репликацией DFS \
`Get-DfsrServiceConfiguration` получает параметры службы репликации DFS для членов группы \
`Grant-DfsrDelegation` предоставляет разрешения участникам безопасности для группы репликации \
`Revoke-DfsrDelegation` отменяет разрешения участников безопасности для группы репликации \
`New-DfsReplicationGroup` создает группу репликации \
`New-DfsReplicatedFolder` создает реплицированную папку в группе репликации \
`Remove-DfsrConnection` удаляет соединение между членами группы репликации \
`Remove-DfsReplicatedFolder` удаляет реплицированную папку из группы репликации \
`Remove-DfsReplicationGroup` удаляет группу репликации \
`Remove-DfsrMember` удаляет компьютеры из группы репликации \
`Restore-DfsrPreservedFiles` восстанавливает файлы и папки, ранее сохраненные репликацией DFS \
`Set-DfsrConnection` изменяет параметры соединения между членами группы репликации \
`Set-DfsrConnectionSchedule` изменяет параметры расписания соединений между членами группы репликации \
`Set-DfsReplicatedFolder` изменяет настройки реплицированной папки \
`Set-DfsReplicationGroup` изменяет группу репликации \
`Set-DfsrGroupSchedule` изменяет расписание группы репликации \
`Set-DfsrMember` изменяет информацию о компьютере-участнике в группе репликации \
`Set-DfsrMembership` настраивает параметры членства для членов группы репликации \
`Set-DfsrServiceConfiguration` изменяет параметры службы репликации DFS \
`Sync-DfsReplicationGroup` синхронизирует репликацию между компьютерами независимо от расписания \
`Suspend-DfsReplicationGroup` приостанавливает репликацию между компьютерами независимо от расписания \
`Update-DfsrConfigurationFromAD` инициирует обновление службы репликации DFS \
`Write-DfsrHealthReport` создает отчет о работоспособности репликации DFS \
`Write-DfsrPropagationReport` создает отчеты для тестовых файлов распространения в группе репликации \
`Start-DfsrPropagationTest` создает тестовый файл распространения в реплицированной папке

# StorageReplica

`Install-WindowsFeature Storage-Replica –IncludeManagementTools -Restart` \
`Get-Command -Module StorageReplica` \
`Test-SRTopology` проверить соответствует ли сервер и канал связи технологии Storage Replica \
`New-SRPartnership -SourceComputerName srv-01 -SourceRGName srv-01-rep-group-01 -SourceVolumeName D: -SourceLogVolumeName L: -DestinationComputerName srv-02 -DestinationRGName srv-02-rep-group-01 -DestinationVolumeName D: -DestinationLogVolumeName L: -LogSizeInBytes 1GB` \
`Get-Counter -Counter "\Storage Replica Statistics(*)"` \
`Get-WinEvent -ProviderName Microsoft-Windows-StorageReplica -max 10` \
`Set-SRPartnership -ReplicationMode Asynchronous` переключить режим репликации на асинхронный \
`Set-SRPartnership -NewSourceComputerName srv-02 -SourceRGName srv-02-rep-group-01 -DestinationComputerName srv-01 -DestinationRGName srv-01-rep-group-01` изменить вручную направление репликации данных, переведя вторичную копию в онлайн режим (при выходе из строя основного сервера) \
`Get-SRGroup` информация о состояние группы реплизации \
`Get-SRPartnerShip` информация о направлении репликации \
`(Get-SRGroup).Replicas | Select-Object numofbytesremaining` проверить длину очереди копирования \
`Get-SRPartnership | Remove-SRPartnership` удалить реплизацию на основном сервере \
`Get-SRGroup | Remove-SRGroup` удалить реплизацию на обоих серверах

# SMB

`Get-SmbServerConfiguration` \
`Set-SmbServerConfiguration -EnableSMB1Protocol $false -Force` отключить протокол SMB v1 \
`Get-WindowsFeature | Where-Object {$_.name -eq "FS-SMB1"} | ft Name,Installstate` модуль ServerManager, проверить установлен ли компонент SMB1 \
`Install-WindowsFeature FS-SMB1` установить SMB1 \
`Uninstall-WindowsFeature –Name FS-SMB1 –Remove` удалить SMB1 клиента (понадобится перезагрузка) \
`Get-WindowsOptionalFeature -Online` модуль DISM, для работы с компонентами Windows \
`Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol -Remove` удалить SMB1 \
`Set-SmbServerConfiguration –AuditSmb1Access $true` включить аудит SMB1 \
`Get-SmbConnection` список активных сессий и используемая версия SMB (Dialect) \
`Get-SmbOpenFile | select ClientUserName,ClientComputerName,Path,SessionID` список открытых файлов \
`Get-SmbShare` список сетевых папок \
`New-SmbShare -Name xl-share -Path E:\test` создать новую общую сетевую папку (расшарить) \
`-EncryptData $True` включить шифрование SMB \
`-Description` имя в сетевом окружении \
`-ReadAccess "domain\username"` доступ на чтение \
`-ChangeAccess` доступ на запись \
`-FullAccess` полный доступ \
`-NoAccess ALL` нет прав \
`-FolderEnumerationMode [AccessBased | Unrestricted]` позволяет скрыть в сетевой папке объекты, на которых у пользователя нет доступа с помощью Access-Based Enumeration (ABE) \
`Get-SmbShare xl-share | Set-SmbShare -FolderEnumerationMode AccessBased` ключить ABE для всех расшаренных папок \
`Remove-SmbShare xl-share -force` удалить сетевой доступ (шару) \
`Get-SmbShareAccess xl-share` вывести список доступов безопасности к шаре \
`Revoke-SmbShareAccess xl-share -AccountName Everyone –Force` удалить группу из списка доступов \
`Grant-SmbShareAccess -Name xl-share -AccountName "domain\XL-Share" -AccessRight Change –force` изменить/добавить разрешения на запись (Full,Read) \
`Grant-SmbShareAccess -Name xl-share -AccountName "все" -AccessRight Change –force` \
`Block-SmbShareAccess -Name xl-share -AccountName "domain\noAccess" -Force` принудительный запрет \
`New-SmbMapping -LocalPath X: -RemotePath \\$srv\xl-share -UserName support4 -Password password –Persistent $true` подключить сетевой диск \
`-Persistent` восстановление соединения после отключения компьютера или сети \
`-SaveCredential` позволяет сохранить учетные данные пользователя для подключения в диспетчер учетных данных Windows Credential Manager \
`Stop-Process -Name "explorer" | Start-Process -FilePath "C:\Windows\explorer.exe"` перезапустить процесс для отображения в проводнике \
`Get-SmbMapping` список подключенных сетевых дисков \
`Remove-SmbMapping X: -force` отмонтировать сетевой диск \
`$CIMSession = New-CIMSession –Computername $srv` создать сеанс CIM (аудентификация на SMB) \
`Get-SmbOpenFile -CIMSession $CIMSession | select ClientUserName,ClientComputerName,Path | Out-GridView -PassThru | Close-SmbOpenFile -CIMSession $CIMSession -Confirm:$false –Force` закрыть файлы (открыть к ним сетевой доступ)

## Get-Acl

`(Get-Acl \\$srv\xl-share).access` доступ ACL на уровне NTFS \
`Get-Acl C:\Drivers | Set-Acl C:\Distr` скопировать NTFS разрешения с одной папки и применить их на другую

## NTFSSecurity

`Install-Module -Name NTFSSecurity -force` \
`Get-Item "\\$srv\xl-share" | Get-NTFSAccess` \
`Add-NTFSAccess -Path "\\$srv\xl-share" -Account "domain\xl-share" -AccessRights Fullcontrol -PassThru` добавить \
`Remove-NTFSAccess -Path "\\$srv\xl-share" -Account "domain\xl-share" -AccessRights FullControl -PassThru` удалить \
`Get-ChildItem -Path "\\$srv\xl-share" -Recurse -Force | Clear-NTFSAccess` удалить все разрешения, без удаления унаследованных разрешений \
`Get-ChildItem -Path "\\$srv\xl-share" -Recurse -Force | Enable-NTFSAccessInheritance` включить NTFS наследование для всех объектов в каталоге

## Storage

`Get-Command -Module Storage` \
`Get-Disk` список логических дисков \
`Get-Partition` отобразить разделы на всех дисках \
`Get-Volume` список логичких разделов \
`Get-PhysicalDisk` список физических дисков \
`Initialize-Disk 1 –PartitionStyle MBR` инициализировать диск \
`New-Partition -DriveLetter D –DiskNumber 1 -Size 500gb` создать раздел (выделить все место -UseMaximumSize) \
`Format-Volume -DriveLetter D -FileSystem NTFS -NewFileSystemLabel Disk-D` форматировать раздел \
`Set-Partition -DriveLetter D -IsActive $True` сделать активным \
`Remove-Partition -DriveLetter D –DiskNumber 1` удалить раздел \
`Clear-Disk -Number 1 -RemoveData` очистить диск \
`Repair-Volume –driveletter C –Scan` Check disk \
`Repair-Volume –driveletter C –SpotFix` \
`Repair-Volume –driverletter C -Scan –Cimsession $CIMSession`

## iSCSI

`New-IscsiVirtualDisk -Path D:\iSCSIVirtualDisks\iSCSI2.vhdx -Size 20GB` создать динамический vhdx-диск (для фиксированного размера -UseFixed) \
`New-IscsiServerTarget -TargetName iscsi-target-2 -InitiatorIds "IQN:iqn.1991-05.com.microsoft:srv3.contoso.com"` создать Target \
`Get-IscsiServerTarget | fl TargetName, LunMappings` \
`Connect-IscsiTarget -NodeAddress "iqn.1995-05.com.microsoft:srv2-iscsi-target-2-target" -IsPersistent $true` подключиться инициатором к таргету \
`Get-IscsiTarget | fl` \
`Disconnect-IscsiTarget -NodeAddress "iqn.1995-05.com.microsoft:srv2-iscsi-target-2-target" -Confirm:$false` отключиться

# WSUS

`Get-Hotfix | Sort-Object -Descending  InstalledOn` список установленных обновлений (информация из cimv2) \
`Get-Hotfix -Description "Security update"` \
`Get-CimInstance Win32_QuickFixEngineering` \
`Get-Service uhssvc` служба Microsoft Health Update Tools, которая отвечает за предоставление обновлений

## WindowsUpdate

`Get-Command -Module WindowsUpdate` \
`Get-WindowsUpdateLog` формирует отчет в $home\AppData\Local\Temp\WindowsUpdateLog в формате csv

## PSWindowsUpdate

`Install-Module -Name PSWindowsUpdate -Scope CurrentUser` \
`Import-Module PSWindowsUpdate` \
`Get-Command -Module PSWindowsUpdate` \
`Get-WindowsUpdate` список обновлений для скачать и установить с сервера WSUS или Microsoft Update \
`Get-WindowsUpdate -Download` загрузить все обновления \
`Get-WindowsUpdate –Install` установить все обновления \
`Install-WindowsUpdate -MicrosoftUpdate -AcceptAll -IgnoreReboot` установить все обновления без перезагрузки \
`Get-WindowsUpdate -KBArticleID KB2267602, KB4533002 -Install` \
`Get-WindowsUpdate -KBArticleID KB2538243 -Hide` скрыть обновления, что бы они никогда не устанавливались \
`Get-WindowsUpdate –IsHidden` отобразить скрытые обновления (Hide-WindowsUpdate) \
`Remove-WindowsUpdate -KBArticleID KB4011634 -NoRestart` удалить обновление \
`Uninstall-WindowsUpdate` удалить обновление \
`Add-WUServiceManager` регистрация сервера обновления (Windows Update Service Manager) \
`Enable-WURemoting` включить правила Windows Defender, разрешающие удаленное использование командлета PSWindowsUpdate \
`Get-WUApiVersion` версия Windows Update Agent \
`Get-WUHistory` список всех установленных обновлений (история обновлений) \
`Get-WUHistory | Where-Object {$_.Title -match "KB4517389"}` поиск обновления \
`Get-WULastResults` даты последнего поиска и установки обновлений \
`Get-WURebootStatus` проверить, нужна ли перезагрузка для применения конкретного обновления \
`Get-WUServiceManager` выводит источники обновлений \
`Get-WUInstallerStatus` статус службы Windows Installer \
`Remove-WUServiceManager` отключить Windows Update Service Manager

## UpdateServices

`Install-WindowsFeature -Name UpdateServices-RSAT` установить роль UpdateServices \
`$UpdateScope = New-Object Microsoft.UpdateServices.Administration.UpdateScope` \
`[enum]::GetValues([Microsoft.UpdateServices.Administration.ApprovedStates])` список утвержденных состояний \
`[enum]::GetValues([Microsoft.UpdateServices.Administration.UpdateInstallationStates])` статус установки (неизвестный, непригодный, не установлен, скачено, установлен, неуспешный, установлен и ожидает перезагрузки, все) \
`$UpdateScope.ApprovedStates = [Microsoft.UpdateServices.Administration.ApprovedStates]"NotApproved"` выставляем статус не утвержденных обновлений на сервере WSUS \
`$UpdateScope.IncludedInstallationStates = [Microsoft.UpdateServices.Administration.UpdateInstallationStates]"NotInstalled"` выставляем статус не установленных обновлений \
`$UpdateScope.IncludedInstallationStates = [Microsoft.UpdateServices.Administration.UpdateInstallationStates]"NotInstalled,Downloaded"` обновления загружены, но не установлены

## PoshWSUS

`Install-Module -Name PoshWSUS` \
`Get-Command -Module PoshWSUS` нужны права администратора \
`Add-PSWSUSClientToGroup` добавление клиента в группу \
`Approve-PSWSUSUpdate` утверждение обновления \
`Connect-PSWSUSDatabaseServer` подключение к серверу базы данных WSUS \
`Connect-PSWSUSServer` подключение к серверу WSUS \
`Deny-PSWSUSUpdate` отклонение обновления \
`Disconnect-PSWSUSServer` отключение от сервера WSUS \
`Export-PSWSUSMetaData` экспорт метаданных WSUS \
`Get-PoshWSUSSyncUpdateCategories` получение категорий обновлений для синхронизации \
`Get-PoshWSUSSyncUpdateClassifications` получение классификаций обновлений для синхронизации \
`Get-PSWSUSCategory` получение категории WSUS \
`Get-PSWSUSChildServer` получение дочернего сервера WSUS \
`Get-PSWSUSClassification` получение классификации WSUS \
`Get-PSWSUSClient` получение информации о клиенте \
`Get-PSWSUSClientGroupMembership` получение групповой принадлежности клиента \
`Get-PSWSUSClientPerUpdate` получение информации о клиентах по обновлениям \
`Get-PSWSUSClientsInGroup` получение клиентов в группе \
`Get-PSWSUSCommand` получение информации о командах WSUS \
`Get-PSWSUSConfig` получение конфигурации WSUS \
`Get-PSWSUSConfigEnabledUpdateLanguages` получение включенных языков обновлений \
`Get-PSWSUSConfigProxyServer` получение конфигурации прокси-сервера \
`Get-PSWSUSConfigSupportedUpdateLanguages` получение поддерживаемых языков обновлений \
`Get-PSWSUSConfigSyncSchedule` получение расписания синхронизации \
`Get-PSWSUSConfigSyncUpdateCategories` получение категорий обновлений для синхронизации \
`Get-PSWSUSConfigSyncUpdateClassifications` получение классификаций обновлений для синхронизации \
`Get-PSWSUSConfigUpdateFiles` получение конфигурации файлов обновлений \
`Get-PSWSUSConfigUpdateSource` получение источника обновлений \
`Get-PSWSUSConfiguration` получение полной конфигурации WSUS \
`Get-PSWSUSContentDownloadProgress` получение прогресса загрузки контента \
`Get-PSWSUSCurrentUserRole` получение роли текущего пользователя \
`Get-PSWSUSDatabaseConfig` получение конфигурации базы данных \
`Get-PSWSUSDownstreamServer` получение конфигурации нижестоящего сервера \
`Get-PSWSUSEmailConfig` получение конфигурации электронной почты \
`Get-PSWSUSEnabledUpdateLanguages` получение включенных языков обновлений \
`Get-PSWSUSEvent` получение событий WSUS \
`Get-PSWSUSGroup` получение информации о группе \
`Get-PSWSUSInstallableItem` получение информации об установленных элементах \
`Get-PSWSUSInstallApprovalRule` получение правил утверждения установки \
`Get-PSWSUSProxyServer` получение информации о прокси-сервере \
`Get-PSWSUSServer` получение информации о сервере WSUS \
`Get-PSWSUSStatus` получение статуса WSUS \
`Get-PSWSUSSubscription` получение подписки WSUS \
`Get-PSWSUSSupportedUpdateLanguages` получение поддерживаемых языков обновлений \
`Get-PSWSUSSyncEvent` получение событий синхронизации \
`Get-PSWSUSSyncHistory` получение истории синхронизаций \
`Get-PSWSUSSyncProgress` получение прогресса синхронизации \
`Get-PSWSUSSyncSchedule` получение расписания синхронизации \
`Get-PSWSUSUpdate` получение информации об обновлении \
`Get-PSWSUSUpdateApproval` получение информации об утверждении обновлений \
`Get-PSWSUSUpdateCategory` получение категории обновлений \
`Get-PSWSUSUpdateClassification` получение классификации обновлений \
`Get-PSWSUSUpdateFile` получение файлов обновлений \
`Get-PSWSUSUpdateFiles` получение списка файлов обновлений \
`Get-PSWSUSUpdatePerClient` получение информации об обновлениях по клиентам \
`Get-PSWSUSUpdateSource` получение источника обновлений \
`Get-PSWSUSUpdateSummary` получение сводки по обновлениям \
`Get-PSWSUSUpdateSummaryPerClient` получение сводки по обновлениям для каждого клиента \
`Get-PSWSUSUpdateSummaryPerGroup` получение сводки по обновлениям для каждой группы \
`Import-PSWSUSMetaData` импорт метаданных WSUS \
`New-PSWSUSComputerScope` создание области охвата компьютеров \
`New-PSWSUSGroup` создание новой группы \
`New-PSWSUSInstallApprovalRule` создание правила утверждения установки \
`New-PSWSUSUpdateScope` создание области охвата обновлений \
`Remove-PSWSUSClient` удаление клиента \
`Remove-PSWSUSClientFromGroup` удаление клиента из группы \
`Remove-PSWSUSGroup` удаление группы \
`Remove-PSWSUSInstallApprovalRule` удаление правила утверждения установки \
`Remove-PSWSUSUpdate` удаление обновления \
`Reset-PSWSUSContent` сброс контента WSUS \
`Resume-PSWSUSDownload` возобновление загрузки \
`Resume-PSWSUSUpdateDownload` возобновление загрузки обновлений \
`Set-PoshWsusClassification` установка классификации WSUS \
`Set-PoshWSUSProduct` установка продукта WSUS \
`Set-PSWSUSConfigEnabledUpdateLanguages` установка включенных языков обновлений \
`Set-PSWSUSConfigProduct` установка продукта конфигурации WSUS \
`Set-PSWsusConfigProxyServer` установка прокси-сервера конфигурации WSUS \
`Set-PSWSUSConfigSyncSchedule` установка расписания синхронизации \
`Set-PSWSUSConfigTargetingMode` установка режима таргетирования \
`Set-PSWSUSConfigUpdateClassification` установка классификации обновлений \
`Set-PSWSUSConfigUpdateFiles` установка файлов обновлений \
`Set-PSWSUSConfigUpdateSource` установка источника обновлений \
`Set-PSWSUSEmailConfig` установка конфигурации электронной почты \
`Set-PSWSUSEnabledUpdateLanguages` установка включенных языков обновлений \
`Set-PSWSUSInstallApprovalRule` установка правила утверждения установки \
`Set-PSWSUSProxyServer` установка прокси-сервера \
`Set-PSWSUSSyncSchedule` установка расписания синхронизации \
`Set-PSWSUSTargetingMode` установка режима таргетирования \
`Set-PSWSUSUpdateFiles` установка файлов обновлений \
`Set-PSWSUSUpdateSource` установка источника обновлений \
`Start-PSWSUSCleanup` запуск очистки WSUS \
`Start-PSWSUSInstallApprovalRule` запуск правила утверждения установки \
`Start-PSWSUSSync` запуск синхронизации \
`Stop-PSWSUSDownload` остановка загрузки \
`Stop-PSWSUSSync` остановка синхронизации \
`Stop-PSWSUSUpdateDownload` остановка загрузки обновлений \
`Test-PSWSUSDatabaseServerConnection` тестирование подключения к серверу базы данных

# VMWare

`Install-Module -Name VMware.PowerCLI # -AllowClobber` установить модуль (PackageProvider: nuget) \
`Get-Module -ListAvailable VMware* | Select Name,Version` \
`Import-Module VMware.VimAutomation.Core` импортировать в сессию \
`Get-PSProvider | format-list Name,PSSnapIn,ModuleName` список оснасток Windows PowerShell

`Get-PowerCLIConfiguration` конфигурация подключения \
`Set-PowerCLIConfiguration -Scope AllUsers -InvalidCertificateAction ignore -confirm:$false` eсли используется самоподписанный сертификат, изменить значение параметра InvalidCertificateAction с Unset на Ignore/Warn \
`Set-PowerCLIConfiguration -Scope AllUsers -ParticipateInCeip $false` отключить уведомление сбора данных через VMware Customer Experience Improvement Program (CEIP)

`Read-Host –AsSecureString | ConvertFrom-SecureString | Out-File "$home\Documents\vcsa_password.txt"` зашифровать пароль и сохранить в файл \
`$esxi = "vcsa.domain.local"` \
`$user = "administrator@vsphere.local"` \
`$pass = Get-Content "$home\Documents\vcsa_password.txt" | ConvertTo-SecureString` прочитать пароль \
`$pass = "password"` \
`$Cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $user ,$pass` \
`Connect-VIServer $esxi -User $Cred.Username -Password $Cred.GetNetworkCredential().password` подключиться, используя PSCredential ($Cred) \
`Connect-VIServer $esxi -User $user -Password $pass` подключиться, используя логин и пароль

`Get-Command –Module *vmware*` отобразить список команд модуля VMware \
`Get-Command –Module *vmware* -name *get*iscsi*` найти команды модуля VMware, связанные с iSCSI \
`Get-IScsiHbaTarget` получить цели iSCSI HBA (Host Bus Adapter) \
`Get-Datacenter` получить список датацентров в инфраструктуре VMware \
`Get-Cluster` список кластеров \
`Get-VMHost` список хостов виртуальных машин (ESXi хостов) \
`Get-VMHost | Select-Object Name,Model,ProcessorType,MaxEVCMode,NumCpu,CpuTotalMhz,CpuUsageMhz,MemoryTotalGB,MemoryUsageGB` получить информацию о хостах виртуальных машин, включая имя, модель, тип процессора и использование ресурсов \
`Get-VMHostDisk | Select-Object VMHost,ScsiLun,TotalSectors` получить информацию о дисках хостов виртуальных машин, включая хост, SCSI LUN и общее количество секторов

`Get-Datastore` список всех хранилищ данных \
`Get-Datastore TNAS-vmfs-4tb-01` получить информацию о хранилище данных с именем TNAS-vmfs-4tb-01 \
`Get-Datastore TNAS-vmfs-4tb-01 | get-vm` получить список виртуальных машин, которые используют хранилище данных \
`Get-Datastore -RelatedObject vm-01` получить информацию о хранилищах данных, связанных с виртуальной машиной \
`$(Get-Datastore TNAS-vmfs-4tb-01).ExtensionData.Info.GetType()` получить тип информации о хранилище данных \
`$(Get-Datastore TNAS-vmfs-4tb-01).ExtensionData.Info.Vmfs.Extent` получить информацию о том, на каких дисках находится хранилище данных

`Get-Command –Module *vmware* -name *disk*` найти команды модуля VMware, связанные с дисками \
`Get-VM vm-01 | Get-Datastore` получить информацию о хранилищах данных, используемых виртуальной машиной \
`Get-VM vm-01 | Get-HardDisk` получить информацию о подключенных дисках к виртуальной машины \
`Get-VM | Get-HardDisk | select Parent,Name,CapacityGB,StorageFormat,FileName | ft` получить информацию о дисках всех виртуальных машин, включая родительскую ВМ, имя, емкость, формат и имя файла \
`Copy-HardDisk` скопировать жесткий диск виртуальной машины \
`Get-VM | Get-Snapshot` список всех снимков виртуальных машин \
`Get-VM | where {$_.Powerstate -eq "PoweredOn"}` список всех включенных виртуальных машин \
`Get-VMHost esxi-05 | Get-VM | where {$_.Powerstate -eq "PoweredOff"} | Move-VM –Destination (Get-VMHost esxi-06)` переместить все выключенные виртуальные машины с хоста esxi-05 на хост esxi-06
```PowerShell
Get-VM | select Name,VMHost,PowerState,NumCpu,MemoryGB,
@{Name="UsedSpaceGB"; Expression={[int32]($_.UsedSpaceGB)}},
@{Name="ProvisionedSpaceGB"; Expression={[int32]($_.ProvisionedSpaceGB)}},
CreateDate,CpuHotAddEnabled,MemoryHotAddEnabled,CpuHotRemoveEnabled,Notes
```
`Get-VMGuest vm-01 | Update-Tools` обновить VMware Tools на виртуальной машине vm-01 \
`Get-VMGuest vm-01 | select OSFullName,IPAddress,HostName,State,Disks,Nics,ToolsVersion` получить информацию о гостевой операционной системе виртуальной машины vm-01 (имя ОС, IP-адрес, имя хоста, состояние, диски, сетевые адаптеры и версию VMware Tools) \
`Get-VMGuest * | select -ExpandProperty IPAddress` получить IP-адреса всех гостевых ОС виртуальных машин \
`Restart-VMGuest -vm vm-01 -Confirm:$False` перезагрузить гостевую ОС виртуальной машины без запроса подтверждения \
`Start-VM -vm vm-01 -Confirm:$False` включить виртуальную машину без запроса подтверждения \
`Shutdown-VMGuest -vm vm-01 -Confirm:$false` выключить

`New-VM –Name vm-01 -VMHost esxi-06 –ResourcePool Production –DiskGB 60 –DiskStorageFormat Thin –Datastore TNAS-vmfs-4tb-01` создать новую виртуальную машину vm-01 на хосте esxi-06 в пуле ресурсов Production с диском 60 ГБ в формате Thin и размещением на хранилище данных TNAS-vmfs-4tb-01

`Get-VM vm-01 | Copy-VMGuestFile -Source "\\$srv\Install\Soft\Btest.exe" -Destination "C:\Install\" -LocalToGuest -GuestUser USER -GuestPassword PASS -force` Скопировать файл с хоста на гостевую ОС виртуальной машины vm-01 с принудительным выполнением

`Get-VM -name vm-01 | Export-VApp -Destination C:\Install -Format OVF` экспортировать виртуальную машину vm-01 в шаблон OVF (.ovf, .vmdk, .mf) \
`Get-VM -name vm-01 | Export-VApp -Destination C:\Install -Format OVA` экспортировать виртуальную машину vm-01 в шаблон OVA

`Get-VMHostNetworkAdapter | select VMHost,Name,Mac,IP,@{Label="Port Group"; Expression={$_.ExtensionData.Portgroup}} | ft` получить информацию о сетевых адаптерах хостов виртуальных машин, включая хост, имя, MAC-адрес, IP-адрес и портовую группу \
`Get-VM | Get-NetworkAdapter | select Parent,Name,Id,Type,MacAddress,ConnectionState,WakeOnLanEnabled | ft` получить информацию о сетевых адаптерах виртуальных машин, включая родительскую ВМ, имя, идентификатор, тип, MAC-адрес, состояние подключения и поддержку Wake-on-LAN

`Get-Command –Module *vmware* -name *event*` \
`Get-VIEvent -MaxSamples 1000 | where {($_.FullFormattedMessage -match "power")} | select username,CreatedTime,FullFormattedMessage` получить последние 1000 событий, связанных с питанием, включая имя пользователя, время создания и полное сообщение \
`Get-logtype | select Key,SourceEntityId,Filename,Creator,Info` получить информацию о типах логов \
`$(Get-Log vpxd:vpxd.log).Entries | select -Last 50` получить последние 50 записей из лога vpxd

`Get-Command –Module *vmware* -name *syslog*` \
`Set-VMHostSysLogServer -VMHost esxi-05 -SysLogServer "tcp://192.168.3.100" -SysLogServerPort 3515` установить сервер syslog сервер для хранения системных логов для хоста esxi-05 \
`Get-VMHostSysLogServer -VMHost esxi-05`

# Hyper-V

`Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart` установить роль на Windows Server \
`Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V –All` установить роль на Windows Desktop \
`Get-Command -Module hyper-v` \
`Get-VMHost`
```PowerShell
New-VMSwitch -name NAT -SwitchType Internal # создать виртуальный коммутатор и адаптер для него
Get-NetAdapter | where InterfaceDescription -match Hyper-V # список сетевых адаптеров
New-NetNat -Name LocalNat -InternalIPInterfaceAddressPrefix "192.168.3.0/24" # задать сеть
Get-NetAdapter "vEthernet (NAT)" | New-NetIPAddress -IPAddress 192.168.3.200 -AddressFamily IPv4 -PrefixLength 24 # присвоить адрес, необходимо на ВМ указать шлюз 192.168.3.200, что бы находиться за NAT, или в настройка ВМ указать соответствующий адаптер
Add-NetNatStaticMapping -NatName LocalNat -Protocol TCP -ExternalIPAddress 0.0.0.0 -ExternalPort 2222 -InternalIPAddress 192.168.3.103 -InternalPort 2121 # проброс, вест трафик который приходит на хост Hyper-V TCP/2222, будет перенаправляться на соответствующий порт виртуальной машины за NAT.
(Get-NetAdapter | where Name -match NAT).Status
```
`Get-NetNatStaticMapping` отобразить пробросы (NAT) \
`Get-NetNat` список сетей \
`Remove-NetNatStaticMapping -StaticMappingID 0` удалить проброс \
`Remove-NetNat -Name LocalNat` удалить сеть

`New-VMSwitch -Name Local -AllowManagementOS $True -NetAdapterName "Ethernet 4" -SwitchType External` создать вшений (External) виртуальный коммутатор 
```PowerShell
$VMName = "hv-dc-01"
$VM = @{
    Name = $VMName
    MemoryStartupBytes = 4Gb
    Generation = 2
    NewVHDPath = "D:\VM\$VMName\$VMName.vhdx"
    NewVHDSizeBytes = 50Gb
    BootDevice = "VHD"
    Path = "D:\VM\$VMName"
    SwitchName = "NAT"
}
```
`New-VM @VM` создать виртуальную машину с параметрами

`Set-VMDvdDrive -VMName $VMName -Path "C:\Users\Lifailon\Documents\WS-2016.iso"` примонтировать образ \
`New-VHD -Path "D:\VM\$VMName\disk_d.vhdx" -SizeBytes 10GB` создать VHDX диск \
`Add-VMHardDiskDrive -VMName $VMName -Path "D:\VM\$VMName\disk_d.vhdx"` примонтировать диск \
`Get-VM –VMname $VMName | Set-VM –AutomaticStartAction Start` автозапуск \
`Get-VM -Name $VMName | Set-VMMemory -StartupBytes 8Gb` назначить стартовый размер оперативной памяти при запуске \
`Set-VMProcessor $VMName -Count 2` количество виртуальных процессоров (vCPU: ядер/потоков) \
`Set-VMProcessor $VMName -Count 2 -Maximum 4 -Reserve 50 -RelativeWeight 200` указать максимальное количество выделяемых процессоров, резервируется 50% ресурсов процессора хоста и установить относительный вес 200 для приоритизации распределения ресурсов процессора относительно других виртуальных машин \
`Get-VM -Name $VMName | Checkpoint-VM -SnapshotName "Snapshot-1"` создать снапшот \
`Restore-VMCheckpoint -Name "Snapshot-1" -VMName $VMName -Confirm:$false` восстановление из снапшота \
`Get-VM | Select -ExpandProperty NetworkAdapters | Select VMName,IPAddresses,Status` получить IP адрес всех ВМ

## VMConnect via RDCMan

`vmconnect.exe localhost $VMHost` подключиться к виртуальной машине через VMConnect (используется в диспетчере Hyper-V) \
`Get-NetTCPConnection -State Established,Listen | Where-Object LocalPort -Match 2179` найти порт слушателя  \
`Get-Process -Id (Get-NetTCPConnection -State Established,Listen | Where-Object LocalPort -Match 2179).OwningProcess` найти процесс по ID (vmms/VMConnect) \
`New-NetFirewallRule -Name "Hyper-V" -DisplayName "Hyper-V" -Group "Hyper-V" -Direction Inbound -Protocol TCP -LocalPort 2179 -Action Allow -Profile Public` открыть порт в локальном Firewall \
`Get-LocalGroupMember -Group "Администраторы Hyper-V"` \
`Get-LocalGroupMember -Group "Hyper-V Administrators"` \
`Add-LocalGroupMember -Group "Администраторы Hyper-V" -Member "lifailon"` добавить пользователя в группу администраторов (для возможности подключения) \
`Get-VM * | Select-Object Name,Id` добавить id в RDCMan для подключения \
`Grant-VMConnectAccess -ComputerName plex-01 -VMName hv-devops-01 -UserName lifailon` дать доступ на подключение не администратору \
`Get-VMConnectAccess` \
`Revoke-VMConnectAccess -VMName hv-devops-01 -UserName lifailon` забрать доступ

Error: `Unknown disconnection reason 3848` - добавить ключи реестра на стороне клиента
```PowerShell
New-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\Credssp\PolicyDefaults\AllowFreshCredentialsDomain -Name Hyper-V -PropertyType String -Value "Microsoft Virtual Console Service/*" -Force
New-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\Credssp\PolicyDefaults\AllowDefaultCredentials -Name Hyper-V -PropertyType String -Value "Microsoft Virtual Console Service/*" -Force
New-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\Credssp\PolicyDefaults\AllowFreshCredentialsWhenNTLMOnlyDomain -Name Hyper-V -PropertyType String -Value "Microsoft Virtual Console Service/*" -Force
ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\Credssp\PolicyDefaults\AllowDefaultCredentialsDomain -Name Hyper-V -PropertyType String -Value "Microsoft Virtual Console Service/*" -Force
New-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\Credssp\PolicyDefaults\AllowFreshCredentials -Name Hyper-V -PropertyType String -Value "Microsoft Virtual Console Service/*" -Force
New-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\Credssp\PolicyDefaults\AllowFreshCredentialsWhenNTLMOnly -Name Hyper-V -PropertyType String -Value "Microsoft Virtual Console Service/*" -Force
New-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\Credssp\PolicyDefaults\AllowSavedCredentialsWhenNTLMOnly -Name Hyper-V -PropertyType String -Value "Microsoft Virtual Console Service/*" -Force
New-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\Credssp\PolicyDefaults\AllowSavedCredentials -Name Hyper-V -PropertyType String -Value "Microsoft Virtual Console Service/*" -Force
New-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\Credssp\PolicyDefaults\AllowSavedCredentialsDomain -Name Hyper-V -PropertyType String -Value "Microsoft Virtual Console Service/*" -Force
```

# Azure

`Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force` установить все модули для работы с Azure \
`Get-Module *Az.*` список всех модулей

`Get-Command -Module Az.Accounts` отобразить список команд модуля Az.Accounts \
`Connect-AzAccount` подключиться у учетной записи Azure \
`Get-AzContext` получить текущий статус подключения к Azure \
`Get-AzSubscription` получить список подписок Azure, доступных для текущего пользователя \
`Set-AzContext` установить контекст Azure для конкретной подписки и/или учетной записи \
`Disconnect-AzAccount` отключиться от учетной записи Azure

`Get-Command -Module Az.Compute` \
`Get-AzVM` получить список виртуальных машин в текущей подписке или группе ресурсов \
`Get-AzVMSize` получить список доступных размеров виртуальных машин в определенном регионе \
`Get-AzVMImage` получить список доступных образов виртуальных машин \
`New-AzVM` создать новую виртуальную машину \
`Remove-AzVM` удалить виртуальную машину \
`Start-AzVM` запустить виртуальную машину \
`Stop-AzVM` остановить виртуальную машину \
`Restart-AzVM` перезагрузить виртуальную машину

`Get-Command -Module Az.Network` \
`Get-AzVirtualNetwork` получить список виртуальных сетей в текущей подписке или группе ресурсов \
`New-AzVirtualNetwork` создать новую виртуальную сеть \
`Remove-AzVirtualNetwork` удалить виртуальную сеть \
`Get-AzNetworkInterface` получить список сетевых интерфейсов \
`New-AzNetworkInterface` создать новый сетевой интерфейс \
`Remove-AzNetworkInterface` удалить сетевой интерфейс

`Get-Command -Module Az.Storage` \
`Get-AzStorageAccount` получить список учетных записей хранилища \
`New-AzStorageAccount` создать новую учетную запись хранилища \
`Remove-AzStorageAccount` удалить учетную запись хранилища \
`Get-AzStorageContainer` список контейнеров в учетной записи хранилища \
`New-AzStorageContainer` создать новый контейнер в учетной записи хранилища \
`Remove-AzStorageContainer` удалить контейнер

`Get-Command -Module Az.ResourceManager` \
`Get-AzResourceGroup` получить список групп ресурсов в текущей подписке \
`New-AzResourceGroup` создать новую группу ресурсов \
`Remove-AzResourceGroup` удалить группу ресурсов \
`Get-AzResource` получить список ресурсов \
`New-AzResource` создать новый ресурс \
`Remove-AzResource` удалить ресурс

`Get-Command -Module Az.KeyVault` \
`Get-AzKeyVault` список хранилищ ключей \
`New-AzKeyVault` создать новое хранилище ключей в Azure \
`Remove-AzKeyVault` удалить хранилище ключей в Azure

`Get-Command -Module Az.Identity` \
`Get-AzADUser` получить информацию о пользователях Azure Active Directory \
`New-AzADUser` создать нового пользователя \
`Remove-AzADUser` удалить пользователя \
`Get-AzADGroup` получить информацию о группах \
`New-AzADGroup` создать новую группу \
`Remove-AzADGroup` удалить группу

## Manage-VM

Source: https://learn.microsoft.com/ru-ru/azure/virtual-machines/windows/tutorial-manage-vm

`New-AzResourceGroup -Name "Resource-Group-01" -Location "EastUS"` создать группу ресурсов (логический контейнер, в котором происходит развертывание ресурсов Azure) \
`Get-AzVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"` список доступных образов Windows Server для установки \
`$cred = Get-Credential` \
`New-AzVm -ResourceGroupName "Resource-Group-01" -Name "vm-01" -Location 'EastUS' -Image "MicrosoftWindowsServer:WindowsServer:2022-datacenter-azure-edition:latest" -Size "Standard_D2s_v3" -OpenPorts 80,3389 --Credential $cred` создать виртуальную машину \
`Get-AzVM -ResourceGroupName "Resource-Group-01" -Name "vm-01" -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}` статус виртуальной машины \
`Start-AzVM -ResourceGroupName "Resource-Group-01" -Name "vm-01"` запустить виртуальную машину \
`Stop-AzVM -ResourceGroupName "Resource-Group-01" -Name "vm-01" -Force` остановить виртуальную машину \
`Invoke-AzVMRunCommand -ResourceGroupName "Resource-Group-01" -VMName "vm-01" -CommandId "RunPowerShellScript" -ScriptString "Install-WindowsFeature -Name Web-Server -IncludeManagementTools"` установить роль веб-сервера IIS

## Manage-Disk

Source: https://learn.microsoft.com/ru-ru/azure/virtual-machines/windows/tutorial-manage-data-disk

`$diskConfig = New-AzDiskConfig -Location "EastUS" -CreateOption Empty -DiskSizeGB 512 -SkuName "Standard_LRS"` создать диск на 512 Гб \
`$dataDisk = New-AzDisk -ResourceGroupName "Resource-Group-01" -DiskName "disk-512" -Disk $diskConfig` создание объекта диска для подготовки диска данных к работе \
`Get-AzDisk -ResourceGroupName "Resource-Group-01" -DiskName "disk-512"` список дисков \
`$vm = Get-AzVM -ResourceGroupName "Resource-Group-01" -Name "vm-01"` \
`Add-AzVMDataDisk -VM $vm -Name "Resource-Group-01" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 1` подключить диск к виртуальной машине \
`Update-AzVM -ResourceGroupName "Resource-Group-01" -VM $vm` обновить конфигурацию виртуальной машины \
`Get-Disk | Where PartitionStyle -eq 'raw' | Initialize-Disk -PartitionStyle MBR -PassThru | New-Partition -AssignDriveLetter -UseMaximumSize | Format-Volume -FileSystem NTFS -NewFileSystemLabel "disk-512" -Confirm:$false` инициализировать диск в ОС (необходимо подключиться к виртуальной машине) с таблицей MBR, создать раздел и назначить все пространство и форматировать в файловую систему NTFS

# NAS

## TrueNAS

Module: [PowerTrueNas](https://github.com/PowerTrueNas/TrueNas)

`Install-Module TrueNas` \
`Import-Module TrueNas` \
`$(Get-Module TrueNas).ExportedCommands` \
`Connect-TrueNasServer -Server tnas-01 -SkipCertificateCheck` \
`Get-TrueNasCertificate` настройки сертификата \
`Get-TrueNasSetting` настройки языка, time zone, syslog level и server, https port \
`Get-TrueNasUser` список пользователей \
`Get-TrueNasSystemVersion` характеристики (Physical Memory, Model, Cores) и Uptime \
`Get-TrueNasSystemAlert` snmp для оповещений \
`Get-TrueNasSystemNTP` список используемых NTP серверов \
`Get-TrueNasDisk` список разделов физического диска \
`Get-TrueNasInterface` сетевые интерфейсы \
`Get-TrueNasGlobalConfig` сетевые настройки \
`Get-TrueNasDnsServer` настроенные DNS-сервера \
`Get-TrueNasIscsiTarget` отобразить ID группы инициаторов использующих таргет, используемый portal, authentification и authen-method \
`Get-TrueNasIscsiInitiator` отобразить группы инициаторов \
`Get-TrueNasIscsiPortal` слушатель (Listen) и порт \
`Get-TrueNasIscsiExtent` список ISCSi Target (статус работы, путь) \
`Get-TrueNasPool` список pool (Id, Path, Status, Healthy) \
`Get-TrueNasVolume -Type FILESYSTEM` список pool файловых систем \
`Get-TrueNasVolume -Type VOLUME` список разделов в pool и их размер \
`Get-TrueNasService | ft` список служб и их статус \
`Start-TrueNasService ssh` запустить службу \
`Stop-TrueNasService ssh` остановить службу

## Synology

Module: [pSynology](https://github.com/pspete/pSynology)

`New-SYNOSession` аутентификация на Synology Diskstation и запуск нового сеанса API \
`Close-SYNOSession` выход из сеанса API \
`Get-SYNOInfo` получить информацию об API DiskStation \
`Add-SYNOFSFAvorite` добавить папку в избранное пользователя \
`Add-SYNOFSFile` загрузить файл \
`Clear-SYNOFSBackgroundTask` удалить все завершенные фоновые задачи \
`Clear-SYNOFSFavoriteStatus` удалить все избранное с неработающим статусом \
`Clear-SYNOFSSharingLink` удалить все просроченные и неработающие ссылки для обмена \
`Get-SYNOFSArchiveCompress` получить статус задачи сжатия \
`Get-SYNOFSArchiveContent` вывести содержимое архива \
`Get-SYNOFSArchiveExtract` получить статус задачи извлечения \
`Get-SYNOFSBackgroundTask` список всех фоновых задач, включая копирование \
`Get-SYNOFSCopy` получить статус операции копирования \
`Get-SYNOFSDeleteItem` получить статус задачи удаления \
`Get-SYNOFSDirSize` получить статус задачи расчета размера \
`Get-SYNOFSFavorite` список избранного пользователя \
`Get-SYNOFSFile` перечислить файлы в заданной папке \
`Get-SYNOFSFileInfo` получить информацию о файлах или файле \
`Get-SYNOFSInfo` получить информацию о файловой станции \
`Get-SYNOFSMD5` получить статус вычислительной задачи MD5 \
`Get-SYNOFSSearch` перечислить совпадающие файлы во временной базе данных поиска \
`Get-SYNOFSShare` список всех общих папок \
`Get-SYNOFSSharingLink` список ссылок пользователя на общий доступ к файлам \
`Get-SYNOFSSharingLinkInfo` получить информацию о ссылке общего доступа по идентификатору ссылки общего доступа \
`Get-SYNOFSThumbnail` получить миниатюру файла \
`Get-SYNOFSVirtualFolder` список всех папок точек монтирования заданного типа виртуальной файловой системы \
`New-SYNOFSFolder` создать папку \
`New-SYNOFSSharingLink` создать одну или несколько ссылок для общего доступа по пути к файл или папку \
`Remove-SYNOFSFAvorite` удаление избранного из избранного пользователя \
`Remove-SYNOFSItem` удалить файл или папку \
`Remove-SYNOFSSearch` удалить временные базы данных поиска \
`Remove-SYNOFSSharingLink` удалить одну или несколько ссылок общего доступа \
`Rename-SYNOFSItem` переименовываем файл или папку \
`Save-SYNOFSFile` загрузить файл или папку \
`Set-SYNOFSFAvorite` редактировать избранное имя \
`Set-SYNOFSSharingLink` изменить ссылку для общего доступа \
`Start-SYNOFSArchiveCompress` запустить задание сжатия файла или папки \
`Start-SYNOFSArchiveExtract` запустить задание распаковки архива \
`Start-SYNOFSCopy` запустить задание копирования файлов \
`Start-SYNOFSDeleteItem` запустить задание удаления файлов или папок \
`Start-SYNOFSDirSize` расчитать размер для одного или нескольких путей к файлам или папкам \
`Start-SYNOFSMD5` получить MD5 файла \
`Start-SYNOFSSearch` поиск файлов по заданным критериям \
`Stop-SYNOFSArchiveCompress` остановить задачу сжатия \
`Stop-SYNOFSArchiveExtract` остановить задачу извлечения \
`Stop-SYNOFSCopy` остановить задачу копирования \
`Stop-SYNOFSDeleteItem` остановить задачу удаления \
`Stop-SYNOFSDirSize` остановить расчет размера \
`Stop-SYNOFSMD5` остановить вычисление MD5 файла \
`Stop-SYNOFSSearch` остановить задачу поиска \
`Test-SYNOFSPermission` проверить, имеет ли вошедший в систему пользователь разрешение на запись в данную папку \
`Update-SYNOFSFAvorite` заменить несколько избранных папок в избранном пользователя

# Veeam

Modules: [VeeamHub](https://github.com/VeeamHub/powershell)

## Get-VBRCommand

`Set-ExecutionPolicy AllSigned` \
`Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))` установить choco \
`choco install veeam-backup-and-replication-console` установить консоль управления (https://community.chocolatey.org/packages/veeam-backup-and-replication-console) \
`Get-Module Veeam.Backup.PowerShell` модуль, который устанавливается вместе с клиентской консолью по умолчанию \
`Get-Command -Module Veeam.Backup.PowerShell` \
`Get-Command -Module Get-VBRCommand` \
`Connect-VBRServer -Server $srv -Credential $cred -Port 9392` \
`Get-VBRJob` \
`Get-VBRCommand *get*backup*` \
`Get-VBRComputerBackupJob` \
`Get-VBRBackup` \
`Get-VBRBackupRepository` \
`Get-VBRBackupSession` \
`Get-VBRBackupServerCertificate` \
`Get-VBRRestorePoint` \
`Get-VBRViProxy`

## Veeam-REStat

Install module [Veeam-REStat](https://github.com/Lifailon/Veeam-REStat): `Install-Module Veeam-REStat -Repository NuGet`

```PowerShell
$path = ($env:PSModulePath.Split(";")[0])+"\Veeam-REStat\Veeam-REStat.psm1"
if (!(Test-Path $path)) {
    New-Item $path -ItemType "File" -Force
}
$(iwr https://raw.githubusercontent.com/Lifailon/Veeam-REStat/rsa/Veeam-REStat/Veeam-REStat.psm1).Content | Out-File $path -Force
```

`Veeam-REStat -Server srv-veeam-11 -Port 9419` при первом запуске необходимо заполнить Credential для подключения к экземпляру сервера VBR, которые сохраняются в файл с именем сервера в формате xml с применением шифрования PSCredential для последующего подключения \
`Veeam-REStat -Reset` сброс учетных данных для подключения к серверу VBR \
`Veeam-REStat -Statistic` статистика всех заданий с сортировкой по дате (выводит время начала, завершения и статус работы, процент прогресса, результат выполнений и сообщение с причиной в случае ошибки: Warning или Failed) \
`Veeam-REStat -Jobs` подробная статистика по всем настроенным заданиям резеврного копирования: статус работы (In Active/disabled), результат последнего задания (LastResult), тип аутентификации (Standard/Linux), имя и размер виртуальной машины, тип резервного копирования (например, Incremental), дату и время последнего и следущего выполнения \
`Veeam-REStat -ConfigBackup` отображает статус состояния работы резервного копирования конфигурации сервера VBR, кол-во точек восстановления, дату и время последней копии \
`Veeam-REStat -Repositories` статистика по инвентарным данным репозиториев: тип хранилища, путь на сервере до директории хранения, общий (capacityGB), свободный (freeGB) и используемый (usedSpaceGB) размер диска под данные \
`Veeam-REStat -Backup` список заданий резервного копирования, тип копирования (VM/Directory) и кол-во точек восстановления \
`Veeam-REStat -Points` история статистики всех точек восстановления с датой создания \
`Veeam-REStat -Hosts` список физически (в ручную) добавленных хостов в инфраструктуру VBR \
`Veeam-REStat -Proxy` список серверов с ролью Proxy \
`Veeam-REStat -Users` список УЗ, добавленных для подключения к серверам \
`Veeam-REStat -Service` выводит информацию о связанных внутренних службах, подключение к этим службам может потребоваться только для интеграции с VBR
