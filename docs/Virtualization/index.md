---
title: "Virtualization"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

## VMWare/PowerCLI

`Install-Module -Name VMware.PowerCLI # -AllowClobber` установить модуль (PackageProvider: nuget) 
`Get-Module -ListAvailable VMware* | Select Name,Version` 
`Import-Module VMware.VimAutomation.Core` импортировать в сессию 
`Get-PSProvider | format-list Name,PSSnapIn,ModuleName` список оснасток Windows PowerShell

`Get-PowerCLIConfiguration` конфигурация подключения 
`Set-PowerCLIConfiguration -Scope AllUsers -InvalidCertificateAction ignore -confirm:$false` eсли используется самоподписанный сертификат, изменить значение параметра InvalidCertificateAction с Unset на Ignore/Warn 
`Set-PowerCLIConfiguration -Scope AllUsers -ParticipateInCeip $false` отключить уведомление сбора данных через VMware Customer Experience Improvement Program (CEIP)

`Read-Host –AsSecureString | ConvertFrom-SecureString | Out-File "$home\Documents\vcsa_password.txt"` зашифровать пароль и сохранить в файл 
`$esxi = "vcsa.domain.local"` 
`$user = "administrator@vsphere.local"` 
`$pass = Get-Content "$home\Documents\vcsa_password.txt" | ConvertTo-SecureString` прочитать пароль 
`$pass = "password"` 
`$Cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $user ,$pass` 
`Connect-VIServer $esxi -User $Cred.Username -Password $Cred.GetNetworkCredential().password` подключиться, используя PSCredential ($Cred) 
`Connect-VIServer $esxi -User $user -Password $pass` подключиться, используя логин и пароль

`Get-Command –Module *vmware*` отобразить список команд модуля VMware 
`Get-Command –Module *vmware* -name *get*iscsi*` найти команды модуля VMware, связанные с iSCSI 
`Get-IScsiHbaTarget` получить цели iSCSI HBA (Host Bus Adapter) 
`Get-Datacenter` получить список датацентров в инфраструктуре VMware 
`Get-Cluster` список кластеров 
`Get-VMHost` список хостов виртуальных машин (ESXi хостов) 
`Get-VMHost | Select-Object Name,Model,ProcessorType,MaxEVCMode,NumCpu,CpuTotalMhz,CpuUsageMhz,MemoryTotalGB,MemoryUsageGB` получить информацию о хостах виртуальных машин, включая имя, модель, тип процессора и использование ресурсов 
`Get-VMHostDisk | Select-Object VMHost,ScsiLun,TotalSectors` получить информацию о дисках хостов виртуальных машин, включая хост, SCSI LUN и общее количество секторов

`Get-Datastore` список всех хранилищ данных 
`Get-Datastore TNAS-vmfs-4tb-01` получить информацию о хранилище данных с именем TNAS-vmfs-4tb-01 
`Get-Datastore TNAS-vmfs-4tb-01 | get-vm` получить список виртуальных машин, которые используют хранилище данных 
`Get-Datastore -RelatedObject vm-01` получить информацию о хранилищах данных, связанных с виртуальной машиной 
`$(Get-Datastore TNAS-vmfs-4tb-01).ExtensionData.Info.GetType()` получить тип информации о хранилище данных 
`$(Get-Datastore TNAS-vmfs-4tb-01).ExtensionData.Info.Vmfs.Extent` получить информацию о том, на каких дисках находится хранилище данных

`Get-Command –Module *vmware* -name *disk*` найти команды модуля VMware, связанные с дисками 
`Get-VM vm-01 | Get-Datastore` получить информацию о хранилищах данных, используемых виртуальной машиной 
`Get-VM vm-01 | Get-HardDisk` получить информацию о подключенных дисках к виртуальной машины 
`Get-VM | Get-HardDisk | select Parent,Name,CapacityGB,StorageFormat,FileName | ft` получить информацию о дисках всех виртуальных машин, включая родительскую ВМ, имя, емкость, формат и имя файла 
`Copy-HardDisk` скопировать жесткий диск виртуальной машины 
`Get-VM | Get-Snapshot` список всех снимков виртуальных машин 
`Get-VM | where {$_.Powerstate -eq "PoweredOn"}` список всех включенных виртуальных машин 
`Get-VMHost esxi-05 | Get-VM | where {$_.Powerstate -eq "PoweredOff"} | Move-VM –Destination (Get-VMHost esxi-06)` переместить все выключенные виртуальные машины с хоста esxi-05 на хост esxi-06
```PowerShell
Get-VM | select Name,VMHost,PowerState,NumCpu,MemoryGB,
@{Name="UsedSpaceGB"; Expression={[int32]($_.UsedSpaceGB)}},
@{Name="ProvisionedSpaceGB"; Expression={[int32]($_.ProvisionedSpaceGB)}},
CreateDate,CpuHotAddEnabled,MemoryHotAddEnabled,CpuHotRemoveEnabled,Notes
```
`Get-VMGuest vm-01 | Update-Tools` обновить VMware Tools на виртуальной машине vm-01 
`Get-VMGuest vm-01 | select OSFullName,IPAddress,HostName,State,Disks,Nics,ToolsVersion` получить информацию о гостевой операционной системе виртуальной машины vm-01 (имя ОС, IP-адрес, имя хоста, состояние, диски, сетевые адаптеры и версию VMware Tools) 
`Get-VMGuest * | select -ExpandProperty IPAddress` получить IP-адреса всех гостевых ОС виртуальных машин 
`Restart-VMGuest -vm vm-01 -Confirm:$False` перезагрузить гостевую ОС виртуальной машины без запроса подтверждения 
`Start-VM -vm vm-01 -Confirm:$False` включить виртуальную машину без запроса подтверждения 
`Shutdown-VMGuest -vm vm-01 -Confirm:$false` выключить

`New-VM –Name vm-01 -VMHost esxi-06 –ResourcePool Production –DiskGB 60 –DiskStorageFormat Thin –Datastore TNAS-vmfs-4tb-01` создать новую виртуальную машину vm-01 на хосте esxi-06 в пуле ресурсов Production с диском 60 ГБ в формате Thin и размещением на хранилище данных TNAS-vmfs-4tb-01

`Get-VM vm-01 | Copy-VMGuestFile -Source "\\$srv\Install\Soft\Btest.exe" -Destination "C:\Install\" -LocalToGuest -GuestUser USER -GuestPassword PASS -force` Скопировать файл с хоста на гостевую ОС виртуальной машины vm-01 с принудительным выполнением

`Get-VM -name vm-01 | Export-VApp -Destination C:\Install -Format OVF` экспортировать виртуальную машину vm-01 в шаблон OVF (.ovf, .vmdk, .mf) 
`Get-VM -name vm-01 | Export-VApp -Destination C:\Install -Format OVA` экспортировать виртуальную машину vm-01 в шаблон OVA

`Get-VMHostNetworkAdapter | select VMHost,Name,Mac,IP,@{Label="Port Group"; Expression={$_.ExtensionData.Portgroup}} | ft` получить информацию о сетевых адаптерах хостов виртуальных машин, включая хост, имя, MAC-адрес, IP-адрес и портовую группу 
`Get-VM | Get-NetworkAdapter | select Parent,Name,Id,Type,MacAddress,ConnectionState,WakeOnLanEnabled | ft` получить информацию о сетевых адаптерах виртуальных машин, включая родительскую ВМ, имя, идентификатор, тип, MAC-адрес, состояние подключения и поддержку Wake-on-LAN

`Get-Command –Module *vmware* -name *event*` 
`Get-VIEvent -MaxSamples 1000 | where {($_.FullFormattedMessage -match "power")} | select username,CreatedTime,FullFormattedMessage` получить последние 1000 событий, связанных с питанием, включая имя пользователя, время создания и полное сообщение 
`Get-logtype | select Key,SourceEntityId,Filename,Creator,Info` получить информацию о типах логов 
`$(Get-Log vpxd:vpxd.log).Entries | select -Last 50` получить последние 50 записей из лога vpxd

`Get-Command –Module *vmware* -name *syslog*` 
`Set-VMHostSysLogServer -VMHost esxi-05 -SysLogServer "tcp://192.168.3.100" -SysLogServerPort 3515` установить сервер syslog сервер для хранения системных логов для хоста esxi-05 
`Get-VMHostSysLogServer -VMHost esxi-05`

## Hyper-V

`Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart` установить роль на Windows Server 
`Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V –All` установить роль на Windows Desktop 
`Get-Command -Module hyper-v` 
`Get-VMHost`
```PowerShell
New-VMSwitch -name NAT -SwitchType Internal # создать виртуальный коммутатор и адаптер для него
Get-NetAdapter | where InterfaceDescription -match Hyper-V # список сетевых адаптеров
New-NetNat -Name LocalNat -InternalIPInterfaceAddressPrefix "192.168.3.0/24" # задать сеть
Get-NetAdapter "vEthernet (NAT)" | New-NetIPAddress -IPAddress 192.168.3.200 -AddressFamily IPv4 -PrefixLength 24 # присвоить адрес, необходимо на ВМ указать шлюз 192.168.3.200, что бы находиться за NAT, или в настройка ВМ указать соответствующий адаптер
Add-NetNatStaticMapping -NatName LocalNat -Protocol TCP -ExternalIPAddress 0.0.0.0 -ExternalPort 2222 -InternalIPAddress 192.168.3.103 -InternalPort 2121 # проброс, вест трафик который приходит на хост Hyper-V TCP/2222, будет перенаправляться на соответствующий порт виртуальной машины за NAT.
(Get-NetAdapter | where Name -match NAT).Status
```
`Get-NetNatStaticMapping` отобразить пробросы (NAT) 
`Get-NetNat` список сетей 
`Remove-NetNatStaticMapping -StaticMappingID 0` удалить проброс 
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

`Set-VMDvdDrive -VMName $VMName -Path "C:\Users\Lifailon\Documents\WS-2016.iso"` примонтировать образ 
`New-VHD -Path "D:\VM\$VMName\disk_d.vhdx" -SizeBytes 10GB` создать VHDX диск 
`Add-VMHardDiskDrive -VMName $VMName -Path "D:\VM\$VMName\disk_d.vhdx"` примонтировать диск 
`Get-VM –VMname $VMName | Set-VM –AutomaticStartAction Start` автозапуск 
`Get-VM -Name $VMName | Set-VMMemory -StartupBytes 8Gb` назначить стартовый размер оперативной памяти при запуске 
`Set-VMProcessor $VMName -Count 2` количество виртуальных процессоров (vCPU: ядер/потоков) 
`Set-VMProcessor $VMName -Count 2 -Maximum 4 -Reserve 50 -RelativeWeight 200` указать максимальное количество выделяемых процессоров, резервируется 50% ресурсов процессора хоста и установить относительный вес 200 для приоритизации распределения ресурсов процессора относительно других виртуальных машин 
`Get-VM -Name $VMName | Checkpoint-VM -SnapshotName "Snapshot-1"` создать снапшот 
`Restore-VMCheckpoint -Name "Snapshot-1" -VMName $VMName -Confirm:$false` восстановление из снапшота 
`Get-VM | Select -ExpandProperty NetworkAdapters | Select VMName,IPAddresses,Status` получить IP адрес всех ВМ

### VMConnect via RDCMan

`vmconnect.exe localhost $VMHost` подключиться к виртуальной машине через VMConnect (используется в диспетчере Hyper-V) 
`Get-NetTCPConnection -State Established,Listen | Where-Object LocalPort -Match 2179` найти порт слушателя  
`Get-Process -Id (Get-NetTCPConnection -State Established,Listen | Where-Object LocalPort -Match 2179).OwningProcess` найти процесс по ID (vmms/VMConnect) 
`New-NetFirewallRule -Name "Hyper-V" -DisplayName "Hyper-V" -Group "Hyper-V" -Direction Inbound -Protocol TCP -LocalPort 2179 -Action Allow -Profile Public` открыть порт в локальном Firewall 
`Get-LocalGroupMember -Group "Администраторы Hyper-V"` 
`Get-LocalGroupMember -Group "Hyper-V Administrators"` 
`Add-LocalGroupMember -Group "Администраторы Hyper-V" -Member "lifailon"` добавить пользователя в группу администраторов (для возможности подключения) 
`Get-VM * | Select-Object Name,Id` добавить id в RDCMan для подключения 
`Grant-VMConnectAccess -ComputerName plex-01 -VMName hv-devops-01 -UserName lifailon` дать доступ на подключение не администратору 
`Get-VMConnectAccess` 
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

## Azure

`Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force` установить все модули для работы с Azure 
`Get-Module *Az.*` список всех модулей

`Get-Command -Module Az.Accounts` отобразить список команд модуля Az.Accounts 
`Connect-AzAccount` подключиться у учетной записи Azure 
`Get-AzContext` получить текущий статус подключения к Azure 
`Get-AzSubscription` получить список подписок Azure, доступных для текущего пользователя 
`Set-AzContext` установить контекст Azure для конкретной подписки и/или учетной записи 
`Disconnect-AzAccount` отключиться от учетной записи Azure

`Get-Command -Module Az.Compute` 
`Get-AzVM` получить список виртуальных машин в текущей подписке или группе ресурсов 
`Get-AzVMSize` получить список доступных размеров виртуальных машин в определенном регионе 
`Get-AzVMImage` получить список доступных образов виртуальных машин 
`New-AzVM` создать новую виртуальную машину 
`Remove-AzVM` удалить виртуальную машину 
`Start-AzVM` запустить виртуальную машину 
`Stop-AzVM` остановить виртуальную машину 
`Restart-AzVM` перезагрузить виртуальную машину

`Get-Command -Module Az.Network` 
`Get-AzVirtualNetwork` получить список виртуальных сетей в текущей подписке или группе ресурсов 
`New-AzVirtualNetwork` создать новую виртуальную сеть 
`Remove-AzVirtualNetwork` удалить виртуальную сеть 
`Get-AzNetworkInterface` получить список сетевых интерфейсов 
`New-AzNetworkInterface` создать новый сетевой интерфейс 
`Remove-AzNetworkInterface` удалить сетевой интерфейс

`Get-Command -Module Az.Storage` 
`Get-AzStorageAccount` получить список учетных записей хранилища 
`New-AzStorageAccount` создать новую учетную запись хранилища 
`Remove-AzStorageAccount` удалить учетную запись хранилища 
`Get-AzStorageContainer` список контейнеров в учетной записи хранилища 
`New-AzStorageContainer` создать новый контейнер в учетной записи хранилища 
`Remove-AzStorageContainer` удалить контейнер

`Get-Command -Module Az.ResourceManager` 
`Get-AzResourceGroup` получить список групп ресурсов в текущей подписке 
`New-AzResourceGroup` создать новую группу ресурсов 
`Remove-AzResourceGroup` удалить группу ресурсов 
`Get-AzResource` получить список ресурсов 
`New-AzResource` создать новый ресурс 
`Remove-AzResource` удалить ресурс

`Get-Command -Module Az.KeyVault` 
`Get-AzKeyVault` список хранилищ ключей 
`New-AzKeyVault` создать новое хранилище ключей в Azure 
`Remove-AzKeyVault` удалить хранилище ключей в Azure

`Get-Command -Module Az.Identity` 
`Get-AzADUser` получить информацию о пользователях Azure Active Directory 
`New-AzADUser` создать нового пользователя 
`Remove-AzADUser` удалить пользователя 
`Get-AzADGroup` получить информацию о группах 
`New-AzADGroup` создать новую группу 
`Remove-AzADGroup` удалить группу

### Manage-VM

Source: https://learn.microsoft.com/ru-ru/azure/virtual-machines/windows/tutorial-manage-vm

`New-AzResourceGroup -Name "Resource-Group-01" -Location "EastUS"` создать группу ресурсов (логический контейнер, в котором происходит развертывание ресурсов Azure) 
`Get-AzVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"` список доступных образов Windows Server для установки 
`$cred = Get-Credential` 
`New-AzVm -ResourceGroupName "Resource-Group-01" -Name "vm-01" -Location 'EastUS' -Image "MicrosoftWindowsServer:WindowsServer:2022-datacenter-azure-edition:latest" -Size "Standard_D2s_v3" -OpenPorts 80,3389 --Credential $cred` создать виртуальную машину 
`Get-AzVM -ResourceGroupName "Resource-Group-01" -Name "vm-01" -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}` статус виртуальной машины 
`Start-AzVM -ResourceGroupName "Resource-Group-01" -Name "vm-01"` запустить виртуальную машину 
`Stop-AzVM -ResourceGroupName "Resource-Group-01" -Name "vm-01" -Force` остановить виртуальную машину 
`Invoke-AzVMRunCommand -ResourceGroupName "Resource-Group-01" -VMName "vm-01" -CommandId "RunPowerShellScript" -ScriptString "Install-WindowsFeature -Name Web-Server -IncludeManagementTools"` установить роль веб-сервера IIS

### Manage-Disk

Source: https://learn.microsoft.com/ru-ru/azure/virtual-machines/windows/tutorial-manage-data-disk

`$diskConfig = New-AzDiskConfig -Location "EastUS" -CreateOption Empty -DiskSizeGB 512 -SkuName "Standard_LRS"` создать диск на 512 Гб 
`$dataDisk = New-AzDisk -ResourceGroupName "Resource-Group-01" -DiskName "disk-512" -Disk $diskConfig` создание объекта диска для подготовки диска данных к работе 
`Get-AzDisk -ResourceGroupName "Resource-Group-01" -DiskName "disk-512"` список дисков 
`$vm = Get-AzVM -ResourceGroupName "Resource-Group-01" -Name "vm-01"` 
`Add-AzVMDataDisk -VM $vm -Name "Resource-Group-01" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 1` подключить диск к виртуальной машине 
`Update-AzVM -ResourceGroupName "Resource-Group-01" -VM $vm` обновить конфигурацию виртуальной машины 
`Get-Disk | Where PartitionStyle -eq 'raw' | Initialize-Disk -PartitionStyle MBR -PassThru | New-Partition -AssignDriveLetter -UseMaximumSize | Format-Volume -FileSystem NTFS -NewFileSystemLabel "disk-512" -Confirm:$false` инициализировать диск в ОС (необходимо подключиться к виртуальной машине) с таблицей MBR, создать раздел и назначить все пространство и форматировать в файловую систему NTFS
