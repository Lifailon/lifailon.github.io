---
title: "Microsoft Exchange"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

## Connect

`$srv_cas = "exchange-cas"`\
`$session_exchange = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri http://$srv_cas/PowerShell/`
-Credential \$Cred -Authentication Kerberos\
`Get-PSSession`\
`Import-PSSession $session_exchange -DisableNameChecking` импортировать
в текущую сессию

## Settings

`Get-ExchangeServer | select name,serverrole,admindisplayversion,Edition,OriginatingServer,WhenCreated,WhenChanged,DataPath | ft`
список всех серверов\
`Get-ImapSettings` настройки IMAP\
`Get-ExchangeCertificate` список сертификатов\
`Get-ExchangeCertificate -Thumbprint "5CEC8544D4743BC279E5FEA1679F79F5BD0C2B3A" | Enable-ExchangeCertificate -Services  IMAP, POP, IIS, SMTP`\
`iisreset`\
`Get-ClientAccessService | fl identity, *uri*` настройки службы
автообнаружения в Exchange 2016\
`Get-ClientAccessService -Identity $srv | Set-ClientAccessService -AutoDiscoverServiceInternalUri https://mail.domain.ru/Autodiscover/Autodiscover.xml`
изменить на внешний адрес\
`Get-OutlookAnywhere` OA позволяет клиентам Outlook подключаться к своим
почтовым ящикам за пределами локальной сети (без использования VPN)\
`Get-WebServicesVirtualDirectory`\
`Get-OwaVirtualDirectory`\
`Get-ActiveSyncVirtualDirectory`\
`Get-OabVirtualDirectory` виртуальная директория автономной адресной
книги\
`Get-OabVirtualDirectory -Server $srv | Set-OabVirtualDirectory -InternalUrl "https://mail.domain.ru/OAB" -ExternalUrl "https://mail.domain.ru/OAB"`

## Roles

MS (Mailbox) - сервер с БД почтовых ящиков и общих папок, отвечает
только за их размещение и не выполняет маршрутизацию никаких сообщений.\
CAS (Client Access Server) - обработка клиентских подключений к почтовым
ящикам, которые создаются клиентами Outlook Web Access (HTTP для Outlook
Web App), Outlook Anywhere, ActiveSync (для мобильных устройств),
интернет протоколы POP3 и IMAP4, MAPI для клиентов Microsoft Outlook.\
Hub Transort - ответвечает за маршрутизацию сообщений интернета и
инфраструктурой Exchange, а также между серверами Exchange. Сообщения
всегда маршрутизируются с помощью роли транспортного
сервера-концентратора, даже если почтовые ящики источника и назначения
находятся в одной базе данных почтовых ящиков.\
Relay - роль пограничного транспортного сервера (шлюз SMTP в периметре
сети).

SCP (Service Connection Point) - запись прописывается в AD, при создание
сервера CAS. Outlook запрашивает SCP, выбирает те, которые находятся в
одном сайте с ним и по параметру WhenCreated -- по дате создания,
выбирая самый старый.\
Autodiscover. Outlook выбирает в качестве сервера Client Access тот,
который прописан в атрибуте RPCClientAccessServer базы данных
пользователя. Сведения о базе данных и сервере mailbox, на котором она
лежит, берутся из AD.

## MessageTrackingLog

`Get-MessageTrackingLog -ResultSize Unlimited | select Timestamp,Sender,Recipients,RecipientCount,MessageSubject,Source,EventID,ClientHostname,ServerHostname,ConnectorId, @{Name="MessageSize"; Expression={[string]([int]($_.TotalBytes / 1024))+" KB"}},@{Name="MessageLatency"; Expression={$_.MessageLatency -replace "\.\d+$"}}`\
`Get-MessageTrackingLog -Start (Get-Date).AddHours(-24) -ResultSize Unlimited | where {[string]$_.recipients -like "*@yandex.ru"}`
вывести сообщения за последние 24 часа, где получателем был указанный
домен\
-Start "04/01/2023 09:00:00" -End "04/01/2023 18:00:00" - поиск по
указанному промежутку времени\
-MessageSubject "Тест" - поиск по теме письма\
-Recipients "support4@domain.ru" - поиск по получателю\
-Sender - поиск по отправителю\
-EventID -- поиск по коду события сервера (RECEIVE, SEND, FAIL, DSN,
DELIVER, BADMAIL, RESOLVE, EXPAND, REDIRECT, TRANSFER, SUBMIT,
POISONMESSAGE, DEFER)\
-Server -- поиск на определенном транспортном сервере\
-messageID -- трекинг письма по его ID

## Mailbox

`Get-Mailbox -Database "it2"` список почтовых серверов в базе данных\
`Get-Mailbox -resultsize unlimited | ? Emailaddresses -like "support4" | format-list name,emailaddresses,database,servername`
какую БД, сервер и smtp-адреса использует почтовый ящик\
`Get-Mailbox -Database $db_name -Archive` отобразить архивные почтовые
ящики

`Get-MailboxFolderStatistics -Identity "support4" -FolderScope All | select Name,ItemsInFolder,FolderSize`
отобразить кол-во писем и размер в каждой папке\
`Get-MailboxStatistics "support4" | select DisplayName,LastLoggedOnUserAccount,LastLogonTime,LastLogoffTime,ItemCount,TotalItemSize,DeletedItemCount,TotalDeletedItemSize,Database,ServerName`
общее кол-во писем, их размер, время последнего входа и выхода, имя
сервера и БД\
`Get-Mailbox -Server s2 | Get-MailboxStatistics | where {$_.Lastlogontime -lt (get-date).AddDays(-30)} | Sort Lastlogontime -desc | ft displayname,Lastlogontime,totalitemsize`
ящики, которые не использовались 30 и более дней

`Enable-Mailbox -Identity support9 -Database test_base` создать почтовый
ящик для существующего пользователя в AD\
`New-Mailbox -Name $login -UserPrincipalName "$login@$domain" -Database $select_db -OrganizationalUnit $path -Password (ConvertTo-SecureString -String "$password" -AsPlainText -Force)`
создать новый почтовый ящик без привязки к пользователю AD\
`Get-MailboxDatabase -Database $db_name | Remove-MailboxDatabase`
удалить БД

`Set-MailBox "support4" -PrimarySmtpAddress support24@domain.ru -EmailAddressPolicyEnabled $false`
добавить и изменить основной SMTP-адрес электронной почты для
пользователя\
`Set-Mailbox -Identity "support4" -DeliverToMailboxAndForward $true -ForwardingSMTPAddress "username@outlook.com"`
включить переадресацию почты (электронная почта попадает в почтовый ящик
пользователя support4 и одновременно пересылается по адресу
username@outlook.com)

## MoveRequest

`Get-Mailbox -Database $db_in | New-MoveRequest -TargetDatabase $db_out`
переместить все почтовые ящики из одной БД в другую\
`New-MoveRequest -Identity $db_in -TargetDatabase $db_out` переместить
один почтовый ящик\
`Get-MoveRequest | Suspend-MoveRequest` остановить запросы перемещения\
`Get-MoveRequest | Remove-MoveRequest` удалить запросы на перемещение\
`Get-MoveRequest | Get-MoveRequestStatistics` статус перемещения

Status:\
Cleanup - нужно подождать\
Queued - в очереди\
InProgress - в процессе\
Percent Complete - процент выполнения\
CompletionInProgress - завершение процесса\
Completed - завершено

`Remove-MoveRequest -Identity $db_name` завершить процесс перемещения
(убрать статус перемещения с почтового ящика и очистить список
перемещений)\
`Get-MailboxDatabase | Select Name, MailboxRetention` после перемещения
ящиков, размер базы не изменится, полное удаление из базы произойдет,
как пройдет количество дней, выставленное в параметре MailboxRetention\
`Set-MailboxDatabase -MailboxRetention '0.00:00:00' -Identity $db_name`
изменить значение

## Archive

`Enable-Mailbox -Identity $name -Archive` включить архив для
пользователя\
`Get-Mailbox $name | New-MoveReques –ArchiveOnly –ArchiveTargetDatabase DBArch`
переместить архивный почтовый ящик в другую БД\
`Get-Mailbox $name | fl Name,Database,ArchiveDatabase` место
расположения БД пользователя и БД его архива\
`Disable-Mailbox -Identity $name -Archive` отключить архив\
`Connect-Mailbox -Identity "8734c04e-981e-4ccf-a547-1c1ac7ebf3e2" -Archive -User $name -Database it2`
подключение архива пользователя к указанному почтовому ящику\
`Get-Mailbox $name | Set-Mailbox -ArchiveQuota 20GB -ArchiveWarningQuota 19GB`
настроить квоты хранения архива

## Quota

`Get-Mailbox -Identity $mailbox | fl IssueWarningQuota, ProhibitSendQuota, ProhibitSendReceiveQuota, UseDatabaseQuotaDefaults`
отобразить квоты почтового ящика\
IssueWarningQuota --- квота, при достижении которой Exchange отправит
уведомление\
ProhibitSendQuota --- при достижении будет запрещена отправка\
ProhibitSendReceiveQuota --- при достижении будет запрещена отправка и
получение\
UseDatabaseQuotaDefaults --- используется ли квота БД или false -
индвидиуальные\
`Set-Mailbox -Identity $mailbox -UseDatabaseQuotaDefaults $false -IssueWarningQuota "3 GB" -ProhibitSendQuota "4 GB" -ProhibitSendReceiveQuota "5 GB"`
задать квоту для пользователя

`Get-MailboxDatabase $db_name | fl Name, *Quota` отобразить квоты
наложенные на БД\
`Set-MailboxDatabase $db -ProhibitSendReceiveQuota "5 GB" -ProhibitSendQuota "4 GB" -IssueWarningQuota "3 GB"`
настроить квоты на БД

## Database

`Get-MailboxDatabase -Status | select ServerName,Name,DatabaseSize`
список и размер всех БД на всех MX-серверах\
`New-MailboxDatabase -Name it_2022 -EdbFilePath E:\Bases\it_2022\it_2022.edb -LogFolderPath G:\Logs\it_2022 -OfflineAddressBook "Default Offline Address List" -server exch-mx-01`
создать БД\
`Restart-Service MSExchangeIS`\
`Get-Service | Where {$_ -match "exchange"} | Restart-Service`\
`Get-MailboxDatabase -Server exch-01` список баз данных на MX-сервере\
`New-MoveRequest -Identity "support4" -TargetDatabase it_2022`
переместить почтовый ящик в новую БД\
`Move-Databasepath $db_name –EdbFilepath "F:\DB\$db_name\$db_name.edb" –LogFolderpath "E:\DB\$db_name\logs\"`
переместить БД и транзакционные логи на другой диск\
`Set-MailboxDatabase -CircularLoggingEnabled $true -Identity $db_name`
включить циклическое ведение журнала (Circular Logging), где
последовательно пишутся 4 файла логов по 5 МБ, после чего первый
лог-файл перезаписывается\
`Set-MailboxDatabase -CircularLoggingEnabled $false -Identity $db_name`
отключить циклическое ведение журнала\
`Get-MailboxDatabase -Server "exch-mx-01" -Status | select EdbFilePath,LogFolderPath,LogFilePrefix`
путь к БД, логам, имя текущего актуального лог-файла

## MailboxRepairRequest

`New-MailboxRepairRequest -Database it2 -CorruptionType ProvisionedFolder, SearchFolder, AggregateCounts, Folderview`
запустить последовательный тест (в конкретный момент времени не доступен
один почтовый ящик) и исправление ошибок на прикладном уровне\
`Get-MailboxRepairRequest -Database it2` прогресс выполнения\
Позволяет исправить:\
ProvisionedFolder -- нарушения логической структуры папок\
SearchFolder -- ошибки в папках поиска\
AggregateCounts -- проверка и исправление информации о количестве
элементов в папках и их размере\
FolderView -- неверное содержимое, отображаемое представлениями папок

## eseutil

При отправке/получении любого письма Exchange сначала вносит информацию
в транзакционный лог, и только потом сохраняет элемент непосредственно в
базу данных. Размер одного лог файла - 1 Мб. Есть три способа урезания
логов: DAG, Backup на базе Volume Shadow Copy, Circular Logging.

Ручное удаление журналов транзакций:\
`cd E:\MS_Exchange_2010\MailBox\Reg_v1_MailBoxes\` перейти в каталог с
логами\
`ls E*.chk` узнать имя файла, в котором находится информация из
контрольной точки фиксации журналов\
`eseutil /mk .\E18.chk` узнать последний файл журнала, действия из
которого были занесены в БД Exchange\
`Checkpoint: (0x561299,8,16)` 561299 имя файла, который был последним
зафиксирован (его информация уже в базе данных)\
Находим в проводнике файл E0500561299.txt, можно удалять все файлы
журналов, которые старше найденного файла

Восстановление БД (если две копии БД с ошибкой):\
`Get-MailboxDatabaseCopyStatus -Identity db_name\* | Format-List Name,Status,ContentIndexState`\
Status : FailedAndSuspended\
ContentIndexState : Failed\
Status : Dismounted\
ContentIndexState : Failed

`Get-MailboxDatabase -Server exch-mx-01 -Status | fl Name,EdbFilePath,LogFolderPath`
проверить расположение базы и транзакционных логов\
LogFolderPath - директория логов\
E18 - имя транкзакционного лога (из него читаются остальные логи)\
`dismount-Database db_name` отмантировать БД\
`eseutil /mh D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb` проверить
базу\
State: Dirty Shutdown - несогласованное состояние, означает, что часть
транзакций не перенесена в базу, например, после того, как была
осуществлена аварийная перезагрузка сервера.\
`eseutil /ml E:\MS_Exchange_2010\MailBox\db_name\E18` проверка
целостности транзакционных логи, если есть логи транзакций и они не
испорчены, то можно восстановить из них, из файла E18 считываются все
логи, должен быть статус - ОК

Soft Recovery (мягкое восстановление) - необходимо перевести базу в
состояние корректного отключения (Clear shutdown) путем записи
недостающих файлов журналов транзакций в БД.\
`eseutil /R E18 /l E:\MS_Exchange_2010\MailBox\db_name\ /d D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb`\
`eseutil /R E18 /a /i /l E:\MS_Exchange_2010\MailBox\db_name\ /d D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb`
если с логами что-то не так, можно попробовать восстановить базу
игнорируя ошибку в логах\
`eseutil /mk D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb` cостоянии
файла контрольных точек\
`eseutil /g D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb` проверка
целостности БД\
`eseutil /k D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb` проверка
контрольных сумм базы (CRC)

Hard Recovery - если логи содержат ошибки и база не восстанавливается,
то восстанавливаем базу без логов.\
`eseutil /p D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb`\
/p - удалит поврежденные страницы, эта информация будет удалена из БД и
восстановит целостность\
`esetuil /d D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb` выполнить
дефрагментацию (если был потерян большой объем данных, то может сильно
снизиться производительность)\
После выполнения команд необходимо вручную удалить все файлы с
расширением log в папке MDBDATA, перед попыткой смонтировать базу
данных.\
`isinteg -s "db_name.edb" -test alltests` проверьте целостность базы
данных\
`isinteg -s "server_name" -fix -test -alltests` если проверка будет
провалена. Выполнять команду до тех пор, пока у всех ошибок не станет
статус 0 или статус не перестанет меняться, иногда необходимо 3 прохода
для достижения результата.\
`eseutil /mh D:\MS_Exchange_2010\Mailbox\db_name\db_name.edb | Select-String -Pattern "State:","Log Required:"`
проверить статус\
State: Clear shutdown - успешный статус\
`Log Required` требуются ли файлы журналов, необходимые базе, чтобы
перейти в согласованное состояние. Если база размонтирована корректно,
то это значение будет равняться 0.\
`mount-Database -force db_name` примонтировать БД\
`Get-MailboxDatabase –Status db_name | fl Mounted` статус БД\
`New-MailboxRepairRequest -Database db_name -CorruptionType SearchFolder,AggregateCounts,ProvisionedFolder,FolderView`
восстановление логической целостности данных\
После этого восстановить Index.\
Если индексы не восстанавливаются, но БД монтируется, то перенести
почтовые ящики в новую БД.

Восстановление БД из Backup:

1-й вариант:\
1. Отмантировать текущую БД и удалить или переименовать директорию с
файлами текущей БД.\
3. Восстановить в ту же директорию из Backup базу с логами.\
4. Запустить мягкое восстановление БД (Soft Recovery).\
5. Примониторвать.

2-й вариант:\
1. Отмантировать и удалить текущую БД.\
2. Восстановить БД с логами из Backup в любое место.\
3. Запустить мягкое восстановление БД (Soft Recovery).\
4. Создать новую БД.\
5. Создать Recovery Database и смонтировать в нее восстановленную из
бэкапа БД, скопировать из неё почтовые ящики в новую БД и переключить на
них пользователей.\
6. Если использовать Dial Tone Recovery, то так же перенести из
временной БД промежуточные данные почтовых ящиков.

3-й вариант:\
1. Восстановить целостность Soft Repair или Hard Recovery.\
2. Создать новую БД. Указывать в свойствах: «база может быть
перезаписана при восстановлении».\
3. Если база была только что оздана и еще не была подмонтирована, то эта
папка будет пуста, туда перемещаем базу из Backup, которая была
обработана ESEUTIL вместе со всеми файлами. Указать имя .edb такое же,
которое было при создании новой базы.\
4. Монтируем базу.\
5. Перенацеливаем ящики со старой (Mailbox_DB_02), неисправной базы, на
новую базу (Mailbox_DB_02_02):\
`Get-Mailbox -Database Mailbox_DB_02 | where {$_.ObjectClass -NotMatch '(SystemAttendantMailbox|ExOleDbSystemMailbox)'} | Set-Mailbox -Database Mailbox_DB_02_02`\
6. Восстановление логической целостности данных:\
`New-MailboxRepairRequest -Database "Mailbox_DB_02_02" -CorruptionType ProvisionedFolder, SearchFolder, AggregateCounts, Folderview`

## Dial Tone Recovery

`Get-Mailbox -Database "MailboxDB" | Set-Mailbox -Database "TempDB"`
перенацелить ящики с одной БД (нерабочей) на другую (пустую)\
`Get-Mailbox -Database TempDB` отобразить почтовые ящики в БД TempDB\
`Restart-Service MSExchangeIS` перезапустить службу Mailbox Information
Store (банка данных), иначе пользователи будут по-прежнему пытаться
подключиться к старой БД\
`iisreset`\
`Get-Mailbox -Database "TempDB" | Set-Mailbox -Database "MailboxDB"`
после восстановления старой БД, нужно переключить пользователей с
временной БД обратно\
После этого сделать слияние с временной БД с помощью Recovery.

## Recovery database (RDB)

`New-MailboxDatabase –Recovery –Name RecoveryDB –Server $exch_mx –EdbFilePath "D:\TempDB\TempDB.edb" -LogFolderPath "D:\TempDB"`
для переноса новых писем из временной БД в основную необходим только сам
файл TempDB.edb со статусом Clean Shutdown, из нее необходимо создать
служебную БД (ключ -Recovery)\
`Mount-Database "D:\TempDB\TempDB.edb"` примонтировать БД\
`Get-MailboxStatistics -Database RecoveryDB`\
`New-MailboxRestoreRequest –SourceDatabase RecoveryDB –SourceStoreMailbox support –TargetMailbox support`
скопировать данные почтового ящика с DisplayName: support из RecoveryDB
в почтовый ящик с псевдонимом support существующей базы. По умолчанию
ищет в почтовой базе совпадающие LegacyExchangeDN либо проверяет
совпадение адреса X500, если нужно восстановить данные в другой ящик,
нужно указывать ключ -AllowLegacyDNMisMatch\
`New-MailboxRestoreRequest –SourceDatabase RecoveryDB –SourceStoreMailbox support –TargetMailbox support –TargetRootFolder "Restore"`
скопировать письма в отдельную папку в ящике назначения (создается
автоматически), возможно восстановить содержимое конкретной папки
-IncludeFolders "##Inbox##"\
`Get-MailboxRestoreRequest | Get-MailboxRestoreRequestStatistics` статус
запроса восстановления\
`Get-MailboxRestoreRequestStatistics -Identity support`\
`Get-MailboxRestoreRequest -Status Completed | Remove-MailboxRestoreRequest`
удалить все успешные запросы

## Transport

`Get-TransportServer $srv_cas | select MaxConcurrentMailboxDeliveries,MaxConcurrentMailboxSubmissions,MaxConnectionRatePerMinute,MaxOutboundConnections,MaxPerDomainOutboundConnections,PickupDirectoryMaxMessagesPerMinute`
настройки пропускной способности транспортного сервера\
MaxConcurrentMailboxDeliveries --- максимальное количество одновременных
потоков, которое может открыть сервер для отправки писем.\
MaxConcurrentMailboxSubmissions --- максимальное количество
одновременных потоков, которое может открыть сервер для получения
писем.\
MaxConnectionRatePerMinute --- максимальное возможная скорость открытия
входящих соединений в минуту.\
MaxOutboundConnections --- максимальное возможное количество соединений,
которое может открыть Exchange для отправки.\
MaxPerDomainOutboundConnections --- максимальное возможное количество
исходящих соединений, которое может открыть Exchange для одного
удаленного домена.\
PickupDirectoryMaxMessagesPerMinute --- скорость внутренней обработки
сообщений в минуту (распределение писем по папкам).\
`Set-TransportServer exchange-cas -MaxConcurrentMailboxDeliveries 21 -MaxConcurrentMailboxSubmissions 21 -MaxConnectionRatePerMinute 1201 -MaxOutboundConnections 1001 -MaxPerDomainOutboundConnections 21 -PickupDirectoryMaxMessagesPerMinute 101`
изменить значения

`Get-TransportConfig | select MaxSendSize, MaxReceiveSize` ограничение
размера сообщения на уровне траспорта (наименьший приоритет, после
коннектора и почтового ящика).\
`New-TransportRule -Name AttachmentLimit -AttachmentSizeOver 15MB -RejectMessageReasonText "Sorry, messages with attachments over 15 MB are not accepted"`
создать транспортное правило для проверки размера вложения

## Connector

`Get-ReceiveConnector | select Name,MaxMessageSize,RemoteIPRanges,WhenChanged`
ограничения размера сообщения на уровне коннектора (приоритет ниже, чем
у почтового ящика)\
`Set-ReceiveConnector ((Get-ReceiveConnector).Identity)[-1] -MaxMessageSize 30Mb`
изменить размер у последнего коннектора в списке (приоритет выше, чем у
траспорта)\
`Get-Mailbox "support4" | select MaxSendSize, MaxReceiveSize` наивысший
приоритет\
`Set-Mailbox "support4" -MaxSendSize 30MB -MaxReceiveSize 30MB` изменить
размер

`Set-SendConnector -Identity "ConnectorName" -Port 26` изменить порт
коннектора отправки\
`Get-SendConnector "proxmox" | select port`

`Get-ReceiveConnector | select Name,MaxRecipientsPerMessage` по
умолчанию Exchange принимает ограниченное количество адресатов в одном
письме (200)\
`Set-ReceiveConnector ((Get-ReceiveConnector).Identity)[-1] -MaxRecipientsPerMessage 50`
изменить значение\
`Set-ReceiveConnector ((Get-ReceiveConnector).Identity)[-1] -MessageRateLimit 1000`
задать лимит обработки сообщений в минуту для коннектора

`Get-OfflineAddressbook | Update-OfflineAddressbook` обновить OAB\
`Get-ClientAccessServer | Update-FileDistributionService`

## PST

`New-MailboxExportRequest -Mailbox $name -filepath "\\$srv\pst\$name.PST" ## -ContentFilter {(Received -lt "01/01/2021")} -Priority Highest/Lower ## -IsArchive`
выполнить экспорт из архива пользователя\
`New-MailboxExportRequest -Mailbox $name -IncludeFolders "##Inbox##" -FilePath "\\$srv\pst\$name.PST"`
только папку входящие\
`New-MailboxImportRequest -Mailbox $name "\\$srv\pst\$name.PST"` импорт
из PST\
`Get-MailboxExportRequest` статус запросов\
`Get-MailboxExportRequest -Status Completed | Remove-MailboxExportRequest`
удалить успешно завершенные запросы\
`Remove-MailboxExportRequest -RequestQueue MBXDB01 -RequestGuid 25e0eaf2-6cc2-4353-b83e-5cb7b72d441f`
отменить экспорт

## DistributionGroup

`Get-DistributionGroup` список групп рассылки\
`Get-DistributionGroupMember "!_Офис"` список пользователей в группе\
`Add-DistributionGroupMember -Identity "!_Офис" -Member "$name@$domain"`
добавить в группу рассылки\
`Remove-DistributionGroupMember -Identity "!_Офис" -Member "$name@$domain"`\
`New-DistributionGroup -Name "!_Тест" -Members "$name@$domain"` создать
группу\
`Set-DistributionGroup -Identity "support4" -HiddenFromAddressListsEnabled $true (или Set-Mailbox)`
скрыть из списка адресов Exchange

## Search

`Search-Mailbox -Identity "support4" -SearchQuery 'Тема:"Mikrotik DOWN"'`
поиск писем по теме\
`Search-Mailbox -Identity "support4" -SearchQuery 'Subject:"Mikrotik DOWN"'`\
`Search-Mailbox -Identity "support4" -SearchQuery 'attachment -like:"*.rar"'`\
`Search-Mailbox -Identity "support4" -SearchQuery "отправлено: < 01/01/2020" -DeleteContent -Force`
удаление писем по дате

Формат даты в зависимости от региональных настроек сервера:\
`20/07/2018`\
`07/20/2018`\
`20-Jul-2018`\
`20/July/2018`

## AuditLog

`Get-AdminAuditLogConfig` настройки аудита\
`Set-Mailbox -Identity "support4" -AuditOwner HardDelete` добавить
логирование HardDelete писем\
`Set-mailbox -identity "support4" -AuditlogAgelimit 120` указать время
хранения\
`Get-mailbox -identity "support4" | Format-list Audit*` данные аудита\
`Search-MailboxAuditLog -Identity "support4" -LogonTypes Delegate -ShowDetails -Start "2022-02-22 18:00" -End "2022-03-22 18:00"`
просмотр логов\
`Search-AdminAuditLog -StartDate "02/20/2022" | ft CmdLetName,Caller,RunDate,ObjectModified -Autosize`
поиск событий истории выполненых команд в журнале аудита Exchange

## Test

`Test-ServiceHealth` проверить доступность ролей сервера: почтовых
ящиков, клиентского доступа, единой системы обмена сообщениями,
траспортного сервера\
`$mx_srv_list | %{Test-MapiConnectivity -Server $_}` проверка
подключения MX-серверов к БД\
`Test-MAPIConnectivity -Database $db` проверка возможности логина в
базу\
`Test-MAPIConnectivity –Identity $user@$domain` проверка возможности
логина в почтовый ящик\
`Test-ComputerSecureChannel` проверка работы службы AD\
`Test-MailFlow` результат тестового потока почты

## Queue

`Get-TransportServer | %{Get-Queue -Server $_.Name}` отобразить очереди
на всех транспортных серверах\
`Get-Queue -Identity EXCHANGE-CAS\155530 | Format-List` подробная
информация об очереди\
`Get-Queue -Identity EXCHANGE-CAS\155530 | Get-Message -ResultSize Unlimited | Select FromAddress,Recipients`
отобразить список отправителей (FromAddress) и список получателей в
очереди (Recipients)\
`Get-Message -Queue EXCHANGE-CAS\155530` отобразить индентификатор
сообщений в конкретной очереди
(сервер`\очередь`{=tex}`\идентификатор `{=tex}письма)\
`Resume-Message EXCHANGE-CAS\155530\444010` повторить отправку письма из
очереди\
`Retry-Queue -Filter {Status -eq "Retry"}` принудительно повторить
отправку всех сообщений c статусом "Повторить"\
`Get-Queue -Identity EXCHANGE-CAS\155530 | Get-Message -ResultSize unlimited | Remove-Message -WithNDR $False`
очистить очередь\
`Get-transportserver EXCHANGE-CAS | Select MessageExpirationTimeout`
отобразить время жизни сообщений в очереди (по умолчанию, 2 дня)

Error Exchange 452 4.3.1 Insufficient system resources - окончание
свободного места на диске, на котором находятся очереди службы Exchange
Hub Transport, за мониторинг отвечает компонент доступных ресурсов Back
Pressure, который в том числе отслеживает свободное место на диске\
Порог Medium (90%) --- перестать принимать по SMTP почту от внешних
отправителей (почта от MAPI клиентов при этом обрабатывается)\
Порог High (99%) --- обработка потока почты полностью прекращается\
Решение: очистить, например логи IIS
(C:`\inetpub`{=tex}`\logs`{=tex}`\LogFiles`{=tex}`\W`{=tex}3SVC1),
увеличить размер диска, отключить мониторинг Back Pressure (плохой
вариант) или перенести транспортные очередь на другой диск достаточного
объёма.

`Get-Service | ? name -like "MSExchangeTransport" | Stop-Service`
остановить служу очереди\
`Rename-Item "C:\Program Files\Microsoft\Exchange Server\V15\TransportRoles\data\Queue" "C:\Program Files\Microsoft\Exchange Server\V15\TransportRoles\data\Queue_old"`
очистить базу очереди\
`C:\Program Files\Microsoft\Exchange Server\V15\Bin\EdgeTransport.exe.config`
конфигурационный файл, который содержит путь к бд с очередью (блок
`<appSettings>`{=html} ключи
`<add key="QueueDatabasePath" value="$new_path" />`{=html} и
QueueDatabaseLoggingPath)\
Для переноса БД, необходимо переместить существующие файлы базы данных
Mail.que и Trn.chk (контрольные точки для отслеживания записи в логах)
из исходного местоположения в новое. Переместите существующие файлы
журнала транзакций Trn.log, Trntmp.log, Trn nnnn.log , Trnres00001.jrs,
Trnres00002.jrs и Temp.edb из старого расположения в новое. tmp.edb ---
временный файл для проверки схемы самой базы, перености не нужно.\
После запуска службы транспорта удалить старую базу данных очереди и
файлы журнала транзакций из старого расположения.

## Defrag

`Get-MailboxDatabase -Status | ft Name, DatabaseSize, AvailableNewMailboxSpace`\
DatabaseSize - текущий размер базы\
AvailableNewMailboxSpace - объём пустых страниц, пространство, которое
можно освободить при дефрагментации\
(DatabaseSize --- AvailableNewMailboxSpace) x 1,1 - необходимо
дополнительно иметь свободного места не менее 110% от текущего размера
базы (без учета пустых страниц)\
`cd $path`\
`Dismount-Database "$path\$db_name"` отмонтировать БД\
`eseutil /d "$path\$db_name.edb"`\
`Mount-Database "$path\$db"` примонтировать БД

## DAG (Database Availability Group)

`Install-WindowsFeature -Name Failover-Clustering -ComputerName EXCH-MX-01`
основывается на технологии Windows Server Failover Cluster\
`New-DatabaseAvailabilityGroup -Name dag-01 -WitnessServer fs-05 -WitnessDirectory C:\witness_exchange1`
создать группу с указанием файлового свидетеля для кворума\
Quorum - это процесс голосования, в котором для принятия решения нужно
иметь большинство голосов, что бы сделать текущую копию базы данных
активной.\
WitnessDirectory --- используется для хранения данных файлового
ресурса-свидетеля.\
`Set-DatabaseAvailabilityGroup dag-01 –DatabaseAvailabilityGroupIPAdress $ip`
изменить ip-адрес группы\
`Get-DatabaseAvailabilityGroup` список всех групп\
`Get-DatabaseAvailabilityGroup -Identity dag-01`\
`Add-DatabaseAvailabilityGroupServer -Identity dag-01 -MailboxServer EXCH-MX-01`
добавить первый сервер (все БД на серверах в DAG должны храниться по
одинаковому пути)\
`Add-MailboxDatabaseCopy -Identity db_name -MailboxServer EXCH-MX-04`
добавить копию БД\
`Get-MailboxDatabaseCopyStatus -Identity db_name\* | select Name,Status,LastInspectedLogTime`
статус и время последнего копирования журнала транзакий

Status:\
Mounted - рабочая база\
Suspended - приостановлено копирование\
Healthy - рабочая пассивная копия\
ServiceDown - недоступна (выключен сервер)\
Dismounted - отмонтирована\
FailedAndSuspended - ошибка и приостановка копирования\
Resynchronizing - процесс синхронизация, где будет постепенно
уменьшаться длина очереди\
CopyQueue Length - длина репликационной очереди копирования (0 - значит
все изменения из активной базы реплицированы в пассивную копию)

`Resume-MailboxDatabaseCopy -Identity db_name\EXCH-MX-04` возобновить
(Resume) или запустить копирование бд на EXCH-MX-04 (из статуса
Suspended в Healthy)\
`Suspend-MailboxDatabaseCopy -Identity db_name\EXCH-MX-04` остановить
копирование (в статус Suspended)\
`Update-MailboxDatabaseCopy -Identity db_name\EXCH-MX-04 -DeleteExistingFiles`
обновить копию БД (сделать Full Backup)\
`Set-MailboxDatabaseCopy -Identity db_name\EXCH-MX-04 -ActivationPreference 1`
изменить приоритет для активации копий БД (какую использовать, 1 --
самое высокое значение)\
`Move-ActiveMailboxDatabase db_name -ActivateOnServer EXCH-MX-04 -MountDialOverride:None -Confirm:$false`
включить копию БД в DAG (переключиться на активную копию)\
`Remove-MailboxDatabaseCopy -Identity db_name\EXCH-MX-04 -Confirm:$False`
удалить копии пассивной базы в DAG-группе (у БД должно быть отключено
ведение циклического журнала)\
`Remove-DatabaseAvailabilityGroupServer -Identity dag-01 -MailboxServer EXCH-MX-04 -ConfigurationOnly`
удалить MX сервер из группы DAG\
`Import-Module FailoverClusters`\
`Get-ClusterNode EXCH-MX-04 | Remove-ClusterNode -Force` удалить
отказавший узел из Windows Failover Cluster

`Get-DatabaseAvailabilityGroup | Get-DatabaseAvailabilityGroupHealth`
мониторинг

## Index

`Get-MailboxDatabaseCopyStatus * | select name,status,ContentIndexState,ContentIndexErrorMessage,ActiveDatabaseCopy,LatestCopyBackupTime,CopyQueueLength`
узнать состояние работы индксов БД и текст ошибки, на каком сервере
активная копия БД, дата последней копии и текущая очередь\
`Get-MailboxDatabaseCopyStatus -Identity $db_name\* | Format-List Name,ContentIndexState`
отобразить список всех копий конкретной БД на всех серверах, и статус их
индексов, если у второго сервера статус Healthy, можно восстановить из
него\
`Get-MailboxDatabaseCopyStatus -Identity $db_name\EXCH-MX-04 | Update-MailboxDatabaseCopy -SourceServer EXCH-MX-01 -CatalogOnly`
восстановить БД из копии\
`cd %PROGRAMFILES%\Microsoft\Exchange Server\V14\Scripts` или v15 для
Exchange 2016\
`.\ResetSearchIndex.ps1 $db_name` скрипт восстановления индекса

`Get-MailboxDatabaseCopyStatus * | where {$_.ContentIndexState -eq "Failed" -or $_.ContentIndexState -eq "FailedAndSuspended"}`
отобразить у какой БД произошел сбой работы (FailedAndSuspended) или
индекса (ContentIndexState)
