---
title: "File System"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

### Items

`Get-Content $home/desktop\test.txt -Wait` аналог tail 
`Test-Path $path` проверить доступность пути 
`Get-FileHash -Algorithm SHA256 "$path"` узнать хэш файла по алгоритму sha256 
`Get-ChildItem $path -Filter *.txt -Recurse` отобразить содержимое каталога (Alias: ls/gci/dir) и дочерних каталогов (-Recurse) и отфильтровать вывод 
`Get-Location` отобразить текущие месторасположение (Alias: pwd/gl) 
`Set-Location $path` перемещение по каталогам (Alias: cd/sl) 
`Invoke-Item $path` открыть файл (Alias: ii/start) 
`Get-ItemProperty $env:userprofile\Documents\dns-list.txt | select FullName,Directory,Name,BaseName,Extension` свойтсва файла 
`Get-ItemProperty -Path $path\* | select FullName,CreationTime,LastWriteTime` свойства файлов содержимого директории, дата их создания и последнего изменения 
`New-Item -Path "C:\test\" -ItemType "Directory"` создать директорию (Alias: mkdir/md) 
`New-Item -Path "C:\test\file.txt" -ItemType "File" -Value "Добавить текст в файл"` создать файл 
`"test" > "C:\test\file.txt"` заменить содержимое 
`"test" >> "C:\test\file.txt"` добавить строку в файл 
`New-Item -Path "C:\test\test\file.txt" -Force` ключ используется для создания отсутствующих в пути директорий или перезаписи файла если он уже существует 
`Move-Item` перемещение объектов (Alias: mv/move) 
`Remove-Item "$path\" -Recurse` удаление всех файлов внутри каталога, без запроса подверждения (Alias: rm/del) 
`Remove-Item $path -Recurse -Include "*.txt","*.temp" -Exclude "log.txt"` удалить все файлы с расширением txt и temp ([Array]), кроме log.txt 
`Rename-Item "C:\test\*.*" "*.jpg"` переименовать файлы по маске (Alias: ren) 
`Copy-Item` копирование файлов и каталогов (Alias: cp/copy) 
`Copy-Item -Path "\\server-01\test" -Destination "C:\" -Recurse` копировать директорию с ее содержимым (-Recurse) 
`Copy-Item -Path "C:\*.txt" -Destination "C:\test\"` знак '\' в конце Destination используется для переноса папки внутрь указанной, отсутствие, что это новое имя директории 
`Copy-Item -Path "C:\*" -Destination "C:\test\" -Include '*.txt','*.jpg'` копировать объекты с указанным расширением (Include) 
`Copy-Item -Path "C:\*" -Destination "C:\test\" -Exclude '*.jpeg'` копировать объекты, за исключением файлов с расширением (Exclude) 
`$log = Copy-Item "C:\*.txt" "C:\test\" -PassThru` вывести результат копирования (логирование) в переменную, можно забирать строки с помощью индексов $log[0].FullName 
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
### System.IO.File

`$file = [System.IO.File]::Create("$home\desktop\test.txt")` создать файл 
`$file.Close()` закрыть файл 
`[System.IO.File]::ReadAllLines("$home\desktop\test.txt")` прочитать файл 
`$file = New-Object System.IO.StreamReader("$home\desktop\test.txt")` файл будет занят процессом PowerShell 
`$file | Get-Member` 
`$file.ReadLine()` построчный вывод 
`$file.ReadToEnd()` прочитать файл целиком

### Read/Write Bytes

`$file = [io.file]::ReadAllBytes("$home\desktop\powershell.jpg")` метод открывает двоичный файл, считывает его в массив байт и закрывает файл 
`[io.file]::WriteAllBytes("$home\desktop\tloztotk-2.jpg",$file)` сохранить байты в файл (можно использовать для выгрузки двоичных файлов из БД)

## Archive

### Microsoft.PowerShell.Archive

`Compress-Archive -Path $srcPath -DestinationPath "$($srcPath).zip" -CompressionLevel Optimal` архивировать (по исходному пути и названию с добавлением расширения) 
`Expand-Archive -Path $zip` разархивировать 
`Expand-Archive -Path $zip -DestinationPath $dstPath` указать путь извлечения 
`Expand-Archive -Path $zip -OutputPath $dstPath`

### System.IO.Compression.FileSystem
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
`cd "$home\Downloads"` 
`Expand-ArchivePassword archive.rar qwe123`

## Handle

`$url = "https://download.sysinternals.com/files/Handle.zip"` 
`Invoke-RestMethod $url -OutFile "$env:TEMP\handle.zip"` 
`Expand-ArchiveFile -Path "$env:TEMP\handle.zip" -DestinationPath "$home\Documents" -FileName "handle.exe"` извлекаем выбранный файл из архива 
`Remove-Item "$env:TEMP\handle.zip"` 
`$handle = "$home\Documents\handle.exe"` 
`$test = New-Object System.IO.StreamReader("$home\desktop\test.txt")` занять файл текущим процессом pwsh ($pid) 
`$SearchProcess = & $handle "C:\Users\Lifailon\Desktop\test.txt" -nobanner -u -v | ConvertFrom-Csv` вывести список дескрипторов по пути к файлу (имя процесса, его PID и пользователь который запустил) 
`Stop-Process $SearchProcess.PID` завершить процесс, который удерживал файл

## PSEverything

`Install-Module PSEverything -Repository NuGet` 
`Find-Everything pingui` найти все файлы в системе (на всех локальных дисках) с именем *pingui* через dll csharp версии Everything (по умолчанию) 
`Find-Everything pingui-0.1.py | Format-List` 
`Find-Everything pingui -es` использовать cli версию Everything для поиска (при первом использовании версии, необходимо дождаться автоматической установки файлов зависимостей) 
`Find-Everything pingui-0.1 -ComputerName localhost` поиск на удаленном компьютере через REST API, если запущен HTTP-сервер Everything

## Console-Menu
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
`ls-menu` 
`ls-menu $home` 
`ls-menu "D:\"`

## SMB

`Get-SmbServerConfiguration` 
`Set-SmbServerConfiguration -EnableSMB1Protocol $false -Force` отключить протокол SMB v1 
`Get-WindowsFeature | Where-Object {$_.name -eq "FS-SMB1"} | ft Name,Installstate` модуль ServerManager, проверить установлен ли компонент SMB1 
`Install-WindowsFeature FS-SMB1` установить SMB1 
`Uninstall-WindowsFeature –Name FS-SMB1 –Remove` удалить SMB1 клиента (понадобится перезагрузка) 
`Get-WindowsOptionalFeature -Online` модуль DISM, для работы с компонентами Windows 
`Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol -Remove` удалить SMB1 
`Set-SmbServerConfiguration –AuditSmb1Access $true` включить аудит SMB1 
`Get-SmbConnection` список активных сессий и используемая версия SMB (Dialect) 
`Get-SmbOpenFile | select ClientUserName,ClientComputerName,Path,SessionID` список открытых файлов 
`Get-SmbShare` список сетевых папок 
`New-SmbShare -Name xl-share -Path E:\test` создать новую общую сетевую папку (расшарить) 
`-EncryptData $True` включить шифрование SMB 
`-Description` имя в сетевом окружении 
`-ReadAccess "domain\username"` доступ на чтение 
`-ChangeAccess` доступ на запись 
`-FullAccess` полный доступ 
`-NoAccess ALL` нет прав 
`-FolderEnumerationMode [AccessBased | Unrestricted]` позволяет скрыть в сетевой папке объекты, на которых у пользователя нет доступа с помощью Access-Based Enumeration (ABE) 
`Get-SmbShare xl-share | Set-SmbShare -FolderEnumerationMode AccessBased` ключить ABE для всех расшаренных папок 
`Remove-SmbShare xl-share -force` удалить сетевой доступ (шару) 
`Get-SmbShareAccess xl-share` вывести список доступов безопасности к шаре 
`Revoke-SmbShareAccess xl-share -AccountName Everyone –Force` удалить группу из списка доступов 
`Grant-SmbShareAccess -Name xl-share -AccountName "domain\XL-Share" -AccessRight Change –force` изменить/добавить разрешения на запись (Full,Read) 
`Grant-SmbShareAccess -Name xl-share -AccountName "все" -AccessRight Change –force` 
`Block-SmbShareAccess -Name xl-share -AccountName "domain\noAccess" -Force` принудительный запрет 
`New-SmbMapping -LocalPath X: -RemotePath \\$srv\xl-share -UserName support4 -Password password –Persistent $true` подключить сетевой диск 
`-Persistent` восстановление соединения после отключения компьютера или сети 
`-SaveCredential` позволяет сохранить учетные данные пользователя для подключения в диспетчер учетных данных Windows Credential Manager 
`Stop-Process -Name "explorer" | Start-Process -FilePath "C:\Windows\explorer.exe"` перезапустить процесс для отображения в проводнике 
`Get-SmbMapping` список подключенных сетевых дисков 
`Remove-SmbMapping X: -force` отмонтировать сетевой диск 
`$CIMSession = New-CIMSession –Computername $srv` создать сеанс CIM (аудентификация на SMB) 
`Get-SmbOpenFile -CIMSession $CIMSession | select ClientUserName,ClientComputerName,Path | Out-GridView -PassThru | Close-SmbOpenFile -CIMSession $CIMSession -Confirm:$false –Force` закрыть файлы (открыть к ним сетевой доступ)

## ACL

`(Get-Acl \\$srv\xl-share).access` доступ ACL на уровне NTFS 
`Get-Acl C:\Drivers | Set-Acl C:\Distr` скопировать NTFS разрешения с одной папки и применить их на другую

## NTFS

`Install-Module -Name NTFSSecurity -force` 
`Get-Item "\\$srv\xl-share" | Get-NTFSAccess` 
`Add-NTFSAccess -Path "\\$srv\xl-share" -Account "domain\xl-share" -AccessRights Fullcontrol -PassThru` добавить 
`Remove-NTFSAccess -Path "\\$srv\xl-share" -Account "domain\xl-share" -AccessRights FullControl -PassThru` удалить 
`Get-ChildItem -Path "\\$srv\xl-share" -Recurse -Force | Clear-NTFSAccess` удалить все разрешения, без удаления унаследованных разрешений 
`Get-ChildItem -Path "\\$srv\xl-share" -Recurse -Force | Enable-NTFSAccessInheritance` включить NTFS наследование для всех объектов в каталоге

## Storage

`Get-Command -Module Storage` 
`Get-Disk` список логических дисков 
`Get-Partition` отобразить разделы на всех дисках 
`Get-Volume` список логичких разделов 
`Get-PhysicalDisk` список физических дисков 
`Initialize-Disk 1 –PartitionStyle MBR` инициализировать диск 
`New-Partition -DriveLetter D –DiskNumber 1 -Size 500gb` создать раздел (выделить все место -UseMaximumSize) 
`Format-Volume -DriveLetter D -FileSystem NTFS -NewFileSystemLabel Disk-D` форматировать раздел 
`Set-Partition -DriveLetter D -IsActive $True` сделать активным 
`Remove-Partition -DriveLetter D –DiskNumber 1` удалить раздел 
`Clear-Disk -Number 1 -RemoveData` очистить диск 
`Repair-Volume –driveletter C –Scan` Check disk 
`Repair-Volume –driveletter C –SpotFix` 
`Repair-Volume –driverletter C -Scan –Cimsession $CIMSession`

## iSCSI

`New-IscsiVirtualDisk -Path D:\iSCSIVirtualDisks\iSCSI2.vhdx -Size 20GB` создать динамический vhdx-диск (для фиксированного размера -UseFixed) 
`New-IscsiServerTarget -TargetName iscsi-target-2 -InitiatorIds "IQN:iqn.1991-05.com.microsoft:srv3.contoso.com"` создать Target 
`Get-IscsiServerTarget | fl TargetName, LunMappings` 
`Connect-IscsiTarget -NodeAddress "iqn.1995-05.com.microsoft:srv2-iscsi-target-2-target" -IsPersistent $true` подключиться инициатором к таргету 
`Get-IscsiTarget | fl` 
`Disconnect-IscsiTarget -NodeAddress "iqn.1995-05.com.microsoft:srv2-iscsi-target-2-target" -Confirm:$false` отключиться

## Base64

### UTF8

`$loginPassword = "login:password"` 
`$Base64 = [Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($loginPassword))` закодировать логин и пароль в строку Base64 
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

## Veeam

VeeamHub (https://github.com/VeeamHub/powershell)

### Get-VBRCommand

`Set-ExecutionPolicy AllSigned` 
`Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))` установить choco 
`choco install veeam-backup-and-replication-console` установить консоль управления (https://community.chocolatey.org/packages/veeam-backup-and-replication-console) 
`Get-Module Veeam.Backup.PowerShell` модуль, который устанавливается вместе с клиентской консолью по умолчанию 
`Get-Command -Module Veeam.Backup.PowerShell` 
`Get-Command -Module Get-VBRCommand` 
`Connect-VBRServer -Server $srv -Credential $cred -Port 9392` 
`Get-VBRJob` 
`Get-VBRCommand *get*backup*` 
`Get-VBRComputerBackupJob` 
`Get-VBRBackup` 
`Get-VBRBackupRepository` 
`Get-VBRBackupSession` 
`Get-VBRBackupServerCertificate` 
`Get-VBRRestorePoint` 
`Get-VBRViProxy`

### Veeam-REStat
```PowerShell
$path = ($env:PSModulePath.Split(";")[0])+"\Veeam-REStat\Veeam-REStat.psm1"
if (!(Test-Path $path)) {
    New-Item $path -ItemType "File" -Force
}
$(iwr https://raw.githubusercontent.com/Lifailon/Veeam-REStat/rsa/Veeam-REStat/Veeam-REStat.psm1).Content | Out-File $path -Force
```
`Veeam-REStat -Server srv-veeam-11 -Port 9419` при первом запуске необходимо заполнить Credential для подключения к экземпляру сервера VBR, которые сохраняются в файл с именем сервера в формате xml с применением шифрования PSCredential для последующего подключения 
`Veeam-REStat -Reset` сброс учетных данных для подключения к серверу VBR 
`Veeam-REStat -Statistic` статистика всех заданий с сортировкой по дате (выводит время начала, завершения и статус работы, процент прогресса, результат выполнений и сообщение с причиной в случае ошибки: Warning или Failed) 
`Veeam-REStat -Jobs` подробная статистика по всем настроенным заданиям резеврного копирования: статус работы (In Active/disabled), результат последнего задания (LastResult), тип аутентификации (Standard/Linux), имя и размер виртуальной машины, тип резервного копирования (например, Incremental), дату и время последнего и следущего выполнения 
`Veeam-REStat -ConfigBackup` отображает статус состояния работы резервного копирования конфигурации сервера VBR, кол-во точек восстановления, дату и время последней копии 
`Veeam-REStat -Repositories` статистика по инвентарным данным репозиториев: тип хранилища, путь на сервере до директории хранения, общий (capacityGB), свободный (freeGB) и используемый (usedSpaceGB) размер диска под данные 
`Veeam-REStat -Backup` список заданий резервного копирования, тип копирования (VM/Directory) и кол-во точек восстановления 
`Veeam-REStat -Points` история статистики всех точек восстановления с датой создания 
`Veeam-REStat -Hosts` список физически (в ручную) добавленных хостов в инфраструктуру VBR 
`Veeam-REStat -Proxy` список серверов с ролью Proxy 
`Veeam-REStat -Users` список УЗ, добавленных для подключения к серверам 
`Veeam-REStat -Service` выводит информацию о связанных внутренних службах, подключение к этим службам может потребоваться только для интеграции с VBR

## NAS

### TrueNAS

[PowerTrueNas](https://github.com/PowerTrueNas/TrueNas)

`Install-Module TrueNas` 
`Import-Module TrueNas` 
`$(Get-Module TrueNas).ExportedCommands` 
`Connect-TrueNasServer -Server tnas-01 -SkipCertificateCheck` 
`Get-TrueNasCertificate` настройки сертификата 
`Get-TrueNasSetting` настройки языка, time zone, syslog level и server, https port 
`Get-TrueNasUser` список пользователей 
`Get-TrueNasSystemVersion` характеристики (Physical Memory, Model, Cores) и Uptime 
`Get-TrueNasSystemAlert` snmp для оповещений 
`Get-TrueNasSystemNTP` список используемых NTP серверов 
`Get-TrueNasDisk` список разделов физического диска 
`Get-TrueNasInterface` сетевые интерфейсы 
`Get-TrueNasGlobalConfig` сетевые настройки 
`Get-TrueNasDnsServer` настроенные DNS-сервера 
`Get-TrueNasIscsiTarget` отобразить ID группы инициаторов использующих таргет, используемый portal, authentification и authen-method 
`Get-TrueNasIscsiInitiator` отобразить группы инициаторов 
`Get-TrueNasIscsiPortal` слушатель (Listen) и порт 
`Get-TrueNasIscsiExtent` список ISCSi Target (статус работы, путь) 
`Get-TrueNasPool` список pool (Id, Path, Status, Healthy) 
`Get-TrueNasVolume -Type FILESYSTEM` список pool файловых систем 
`Get-TrueNasVolume -Type VOLUME` список разделов в pool и их размер 
`Get-TrueNasService | ft` список служб и их статус 
`Start-TrueNasService ssh` запустить службу 
`Stop-TrueNasService ssh` остановить службу

### Synology

[pSynology](https://github.com/pspete/pSynology)

`New-SYNOSession` аутентификация на Synology Diskstation и запуск нового сеанса API 
`Close-SYNOSession` выход из сеанса API 
`Get-SYNOInfo` получить информацию об API DiskStation 
`Add-SYNOFSFAvorite` добавить папку в избранное пользователя 
`Add-SYNOFSFile` загрузить файл 
`Clear-SYNOFSBackgroundTask` удалить все завершенные фоновые задачи 
`Clear-SYNOFSFavoriteStatus` удалить все избранное с неработающим статусом 
`Clear-SYNOFSSharingLink` удалить все просроченные и неработающие ссылки для обмена 
`Get-SYNOFSArchiveCompress` получить статус задачи сжатия 
`Get-SYNOFSArchiveContent` вывести содержимое архива 
`Get-SYNOFSArchiveExtract` получить статус задачи извлечения 
`Get-SYNOFSBackgroundTask` список всех фоновых задач, включая копирование 
`Get-SYNOFSCopy` получить статус операции копирования 
`Get-SYNOFSDeleteItem` получить статус задачи удаления 
`Get-SYNOFSDirSize` получить статус задачи расчета размера 
`Get-SYNOFSFavorite` список избранного пользователя 
`Get-SYNOFSFile` перечислить файлы в заданной папке 
`Get-SYNOFSFileInfo` получить информацию о файлах или файле 
`Get-SYNOFSInfo` получить информацию о файловой станции 
`Get-SYNOFSMD5` получить статус вычислительной задачи MD5 
`Get-SYNOFSSearch` перечислить совпадающие файлы во временной базе данных поиска 
`Get-SYNOFSShare` список всех общих папок 
`Get-SYNOFSSharingLink` список ссылок пользователя на общий доступ к файлам 
`Get-SYNOFSSharingLinkInfo` получить информацию о ссылке общего доступа по идентификатору ссылки общего доступа 
`Get-SYNOFSThumbnail` получить миниатюру файла 
`Get-SYNOFSVirtualFolder` список всех папок точек монтирования заданного типа виртуальной файловой системы 
`New-SYNOFSFolder` создать папку 
`New-SYNOFSSharingLink` создать одну или несколько ссылок для общего доступа по пути к файл или папку 
`Remove-SYNOFSFAvorite` удаление избранного из избранного пользователя 
`Remove-SYNOFSItem` удалить файл или папку 
`Remove-SYNOFSSearch` удалить временные базы данных поиска 
`Remove-SYNOFSSharingLink` удалить одну или несколько ссылок общего доступа 
`Rename-SYNOFSItem` переименовываем файл или папку 
`Save-SYNOFSFile` загрузить файл или папку 
`Set-SYNOFSFAvorite` редактировать избранное имя 
`Set-SYNOFSSharingLink` изменить ссылку для общего доступа 
`Start-SYNOFSArchiveCompress` запустить задание сжатия файла или папки 
`Start-SYNOFSArchiveExtract` запустить задание распаковки архива 
`Start-SYNOFSCopy` запустить задание копирования файлов 
`Start-SYNOFSDeleteItem` запустить задание удаления файлов или папок 
`Start-SYNOFSDirSize` расчитать размер для одного или нескольких путей к файлам или папкам 
`Start-SYNOFSMD5` получить MD5 файла 
`Start-SYNOFSSearch` поиск файлов по заданным критериям 
`Stop-SYNOFSArchiveCompress` остановить задачу сжатия 
`Stop-SYNOFSArchiveExtract` остановить задачу извлечения 
`Stop-SYNOFSCopy` остановить задачу копирования 
`Stop-SYNOFSDeleteItem` остановить задачу удаления 
`Stop-SYNOFSDirSize` остановить расчет размера 
`Stop-SYNOFSMD5` остановить вычисление MD5 файла 
`Stop-SYNOFSSearch` остановить задачу поиска 
`Test-SYNOFSPermission` проверить, имеет ли вошедший в систему пользователь разрешение на запись в данную папку 
`Update-SYNOFSFAvorite` заменить несколько избранных папок в избранном пользователя
