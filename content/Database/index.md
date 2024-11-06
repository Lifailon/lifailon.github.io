---
title: "Database"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

## InfluxDB

[Download InfluxDB 1.x Open
Source](https://www.influxdata.com/downloads)\
[InfluxDB-Studio](https://github.com/CymaticLabs/InfluxDBStudio)

### Install Windows

``` powershell
Invoke-RestMethod "https://dl.influxdata.com/influxdb/releases/influxdb-1.8.10_windows_amd64.zip" -OutFile "$home\Downloads\influxdb-1.8.10_windows_amd64.zip"
Expand-Archive "$home\Downloads\influxdb-1.8.10_windows_amd64.zip" -DestinationPath "$home\Documents\"
Remove-Item "$home\Downloads\influxdb-1.8.10_windows_amd64.zip"
& "$home\Downloads\influxdb-1.8.10-1\influxd.exe"
```

### Install Ubuntu

``` bash
wget https://dl.influxdata.com/influxdb/releases/influxdb_1.8.10_amd64.deb
sudo dpkg -i influxdb_1.8.10_amd64.deb
systemctl start influxdb
systemctl status influxdb

ps aux | grep influxdb | grep -Ev "grep"
netstat -natpl | grep 80[8-9][3-9]
```

### API

``` bash
nano /etc/influxdb/influxdb.conf

[http]
  enabled = true
  bind-address = ":8086"
  auth-enabled = false

systemctl restart influxdb
```

### Chronograf

    wget https://dl.influxdata.com/chronograf/releases/chronograf-1.10.2_windows_amd64.zip -UseBasicParsing -OutFile chronograf-1.10.2_windows_amd64.zip
    Expand-Archive .\chronograf-1.10.2_windows_amd64.zip -DestinationPath 'C:\Program Files\InfluxData\chronograf\'

    wget https://dl.influxdata.com/chronograf/releases/chronograf_1.10.2_amd64.deb
    sudo dpkg -i chronograf_1.10.2_amd64.deb
    systemctl status influxdb
    http://192.168.3.102:8888

### Grafana

[Download](https://grafana.com/grafana/download)

`invoke-RestMethod https://dl.grafana.com/enterprise/release/grafana-enterprise-10.3.1.windows-amd64.msi -OutFile "$home\Download\grafana.msi"`

``` bash
apt-get install -y adduser libfontconfig1 musl
wget https://dl.grafana.com/enterprise/release/grafana-enterprise_10.3.1_amd64.deb
dpkg -i grafana-enterprise_10.3.1_amd64.deb
systemctl start grafana-server
systemctl status grafana-server
```

### CLI Client

`apt install influxdb-client`\
`influx`\
`influx --host 192.168.3.102 --username admin --password password`

``` powershell
$influx_client_exec = "$home\Documents\influxdb-1.8.10-1\influx.exe"
& $influx_client_exec -host 192.168.3.102 -port 8086
help
show databases
use PowerShell
SELECT * FROM "HardwareMonitor" WHERE time > now() - 5m
```

### USERS

`SHOW USERS` отобразить пользователей и их права доступа\
`CREATE USER admin WITH PASSWORD 'password' WITH ALL PRIVILEGES` создать
пользователя\
`GRANT ALL PRIVILEGES TO "admin"` предоставить права доступа\
`GRANT READ ON "database" TO "admin"` доступ на чтение для БД или запись
(WRITE)\
`REVOKE ALL PRIVILEGES FROM "admin"` отозвать права доступа\
`SHOW GRANTS FOR "admin"` БД и привелегии доступа для указанного
пользователя\
`SET PASSWORD FOR "admin" = 'new_password'` изменить пароль\
`DROP USER "admin"` удалить пользователя

### DATABASE

`CREATE DATABASE powershell` создать БД\
`SHOW DATABASES` отобразить список БД\
`DROP DATABASE powershell` удалить БД\
`USE powershell`\
`SHOW measurements` отобразить все таблицы\
`INSERT performance,host=console,counter=CPU value=0.88` записать данные
в таблицу performance

### MEASUREMENT

`SHOW TAG KEYS FROM "HardwareMonitor"` отобразить все тэги в таблице\
`SHOW TAG VALUES FROM "HardwareMonitor" WITH KEY = "HardwareName"`
отобразить все значения указанного тэга\
`SHOW FIELD KEYS FROM "HardwareMonitor"` отобразить все Field Tags и их
тип данных\
`SHOW SERIES FROM "HardwareMonitor"` отобразить список всех уникальных
серий в указанной таблице. Серия - это набор точек данных, которые имеют
одинаковые значения для всех тегов, за исключением времени.\
`DROP SERIES FROM "HardwareMonitor"` очистить все данные в таблице\
`DROP MEASUREMENT "HardwareMonitor"` удалить таблицу

### SELECT/WHERE

`SELECT * FROM performance` отобразить все данные в таблице\
`SELECT value FROM performance` отфильтровать по столбцу value (только
Field Keys)\
`SELECT * FROM performance limit 10` отобразить 10 единиц данных\
`SELECT * FROM performance WHERE time > now() -2d` отобразить данные за
последние 2 дня\
`SELECT * FROM performance WHERE time > now() +3h -5m` данные за
последние 5 минут (+3 часа от текущего времени по UTC 0 -5 минут)\
`SELECT * FROM performance WHERE counter = 'CPU'` выборка по тэгу\
`SELECT upload/1000 FROM speedtest WHERE upload/1000 <= 250` выборка по
столбцу upload и разделить вывод на 1000, вывести upload меньше 250\
`DELETE FROM performance WHERE time > now() -1h` удалить данные за
последние 1/4 часа\
`DELETE FROM performance WHERE time < now() -24h` удалить данные старше
24 часов

### REGEX

`SELECT * FROM "win_pdisk" WHERE instance =~/.*C:/ and time > now() - 5m`
и\
`SELECT * FROM "win_pdisk" WHERE instance =~/.*E:/ or instance =~ /.*F:/`
или\
`SELECT * FROM "win_pdisk" WHERE instance !~ /.*Total/` не равно
(исключить)\
`SELECT * FROM "HardwareMonitor" WHERE time > now() - 5m and HardwareName =~ /Intel/`
приблизительно равно\
`SELECT * FROM "HardwareMonitor" WHERE time > now() - 5m and HardwareName =~ /Intel.+i7/`
эквивалент 12th_Gen_Intel_Core_i7-1260P\
`SELECT * FROM "HardwareMonitor" WHERE time > now() - 5m and HardwareName =~ /^Intel/`
начинается на Intel\
`SELECT * FROM "HardwareMonitor" WHERE time > now() - 5m and HardwareName =~ /00$/`
заканчивается на 00

### GROUP BY tag_key

`SELECT * FROM "HardwareMonitor" WHERE time > now() - 5m and SensorName = 'Temperature' GROUP BY HardwareName`
создать уникальные группы по тэгу HardwareName\
`SELECT * FROM "HardwareMonitor" WHERE time > now() - 5m and SensorName = 'Temperature' GROUP BY Host,HardwareName`
больше групп по двум тэгаам

### Functions(field_key)

[Functions](https://docs.influxdata.com/influxdb/v1.8/query_language/functions)

`SELECT instance,LAST(Avg._Disk_Read_Queue_Length) FROM "win_pdisk" GROUP BY instance`
отфильтровать вывод по последнему/текущему значению\
`SELECT instance,FIRST(Avg._Disk_Read_Queue_Length) FROM "win_pdisk" GROUP BY instance`
отфильтровать вывод по первому значению за весь или указанный отрезок
времени\
`SELECT instance,MIN(Avg._Disk_Read_Queue_Length) FROM "win_pdisk" GROUP BY instance`
отфильтровать вывод с отображением минимального значения\
`SELECT instance,MAX(Avg._Disk_Read_Queue_Length) FROM "win_pdisk" GROUP BY instance`
отфильтровать вывод с отображением максимального значения\
`SELECT SUM(Bytes_Received_persec) FROM "win_net" GROUP BY instance`
суммах всех значений\
`SELECT COUNT(Bytes_Received_persec) FROM "win_net" WHERE Bytes_Received_persec >= 0 GROUP BY instance`
кол-во данных, где значение выше или равно 0\
`SELECT MEAN(Bytes_Received_persec) FROM "win_net" WHERE Bytes_Received_persec < 1000 GROUP BY instance`
среднее значение данных с показателем от 0 до 1000 (509)

`SELECT *,MAX(Value) FROM "HardwareMonitor" WHERE time > now() -1h GROUP BY SensorName,Host`
создать группы для выявления максимального значения значения стобца
Value каждого тэга SensorName и хоста за последний час\
`SELECT *,MAX(Value) FROM "HardwareMonitor" WHERE time > now() -1h and SensorName = 'CPU_Package' GROUP BY Host`
масимальное значение CPU_Package за последний час для каждого хоста\
`SELECT MEAN(Value) FROM "HardwareMonitor" WHERE time > now() -1h and SensorName = 'CPU_Package' GROUP BY Host`
среднее значение CPU_Package за последний час

### POLICY

`CREATE DATABASE powershell WITH DURATION 48h REPLICATION 1 NAME "del2d"`
создать БД с политикой хранения 2 дня\
`CREATE RETENTION POLICY del2h ON powershell DURATION 2h REPLICATION 1`
создать новую политику хранения для БД\
`CREATE RETENTION POLICY del6h ON powershell DURATION 6h REPLICATION 1 SHARD DURATION 2h`
указать период хранения 6 часов + 2 часа до очистки (по умолчанию 1ч или
больше)\
`ALTER RETENTION POLICY del6h ON powershell DEFAULT` изменить (ALTER)
политику хранения для БД на del6h (DEFAULT)\
`DROP RETENTION POLICY del2d ON powershell` удаление политики хранения
приводит к безвозвратному удалению всех измерений (таблиц) и данных,
хранящихся в политике хранения\
`SHOW RETENTION POLICIES ON PowerShell` отобразить действующие политики
базы данных PowerShell

## InfluxDB-api

``` powershell
$data = Invoke-RestMethod http://192.168.3.102:8086/query?q="SHOW RETENTION POLICIES ON PowerShell"
$col = $data.results.series.columns
$val = $data.results.series.values
$mass    = @()
$mass   += [string]$col
foreach ($v in $val) {
    $mass += [string]$v
}
$mass = $mass -replace '^','"'
$mass = $mass -replace '$','"'
$mass = $mass -replace '\s','","'
$mass | ConvertFrom-Csv
```

### API POST

Вместо таблиц в InfluxDB имеются измерения. Вместо столбцов в ней есть
теги и поля.

       Table                        Tag (string/int)                          Field (double/int)           TIMESTAMP
    measurement,Tag_Keys1=Tag_Values1,Tag_Keys2=Tag_Values2 Field_Keys1="Values",Field_Keys2="Values" 0000000000000000000
               1                                           2                                         3

    $ip        = "192.168.3.104"
    $port      = "8086"
    $db        = "powershell"
    $table     = "speedtest"
    $ipp       = $ip+":"+$port
    $url       = "http://$ipp/write?db=$db"
    $user      = "admin"
    $pass      = "password" | ConvertTo-SecureString -AsPlainText -Force
    $cred      = [System.Management.Automation.PSCredential]::new($user,$pass)
    $unixtime  = (New-TimeSpan -Start (Get-Date "01/01/1970") -End (Get-Date)).TotalSeconds
    $timestamp = ([string]$unixtime -replace "\..+") + "000000000"

    Invoke-RestMethod -Method POST -Uri $url -Body "$table,host=$(hostname) download=200000,upload=300000,ping=3 $timestamp"

### API GET

`curl http://192.168.3.104:8086/query --data-urlencode "q=SHOW DATABASES"`
pwsh7 (ConvertFrom-Json) and bash

`$dbs = irm "http://192.168.3.104:8086/query?q=SHOW DATABASES"`\
`$dbs = irm "http://192.168.3.104:8086/query?epoch=ms&u=admin&p=password&q=SHOW DATABASES"`\
`$dbs.results.series.values`

``` powershell
$ip    = "192.168.3.104"
$port  = "8086"
$db    = "powershell"
$table = "speedtest"
$query = "SELECT * FROM $table"
$ipp   = $ip+":"+$port
$url   = "http://$ipp/query?db=$db&q=$query"
$data  = Invoke-RestMethod -Method GET -Uri $url` -Credential $cred 
$data.results.series.name   ` имя таблицы
$data.results.series.columns` столбцы/ключи
$data.results.series.values ` данные построчно
```

### Endpoints

[API doc](https://docs.influxdata.com/influxdb/v1.7/tools/api/)

``` powershell
$stats = irm http://192.168.3.104:8086/debug/vars` статистика сервера
$stats."database:powershell".values` кол-во таблиц к БД
$stats.queryExecutor.values` количество query-запросов (обращений к endpoint /query)
$stats.write.values` количество write-запросов
$stats.system.uptime
```

`http://192.168.3.104:8086/debug/requests` кол-во клиентских
HTTP-запросов к конечным точкам /writeи /query\
`http://192.168.3.104:8086/debug/pprof`\
`http://192.168.3.104:8086/ping`\
`http://192.168.3.104:8086/query`\
`http://192.168.3.104:8086/write`

`http://192.168.3.99:8086/api/v2/setup`\
`http://192.168.3.99:8086/api/v2/config`\
`http://192.168.3.99:8086/api/v2/write`

### PingTo-InfluxDB

``` powershell
while ($true) {
    $tz = (Get-TimeZone).BaseUtcOffset.TotalMinutes
    $unixtime  = (New-TimeSpan -Start (Get-Date "01/01/1970") -End ((Get-Date).AddMinutes(-$tz))).TotalSeconds` -3h UTC
    $timestamp = ([string]$unixtime -replace "\..+") + "000000000"
    $tnc = tnc 8.8.8.8
    $Status = $tnc.PingSucceeded
    $RTime = $tnc.PingReplyDetails.RoundtripTime
    Invoke-RestMethod -Method POST -Uri "http://192.168.3.104:8086/write?db=powershell" -Body "ping,host=$(hostname) status=$status,rtime=$RTime $timestamp"
    sleep 1
}
```

`SELECT * FROM ping WHERE status = false`

### PerformanceTo-InfluxDB

``` powershell
function ConvertTo-Encoding ([string]$From, [string]$To) {
    Begin {
        $encFrom = [System.Text.Encoding]::GetEncoding($from)
        $encTo = [System.Text.Encoding]::GetEncoding($to)
    }
    Process {
        $bytes = $encTo.GetBytes($_)
        $bytes = [System.Text.Encoding]::Convert($encFrom, $encTo, $bytes)
        $encTo.GetString($bytes)
    }
}

$localization = (Get-Culture).LCID` текущая локализация
if ($localization -eq 1049) {
    $performance = "\\$(hostname)\Процессор(_Total)\% загруженности процессора" | ConvertTo-Encoding UTF-8 windows-1251` декодировать кириллицу
} else {
    $performance = "\Processor(_Total)\% Processor Time"
}

$tz = (Get-TimeZone).BaseUtcOffset.TotalMinutes
while ($true) {
    $unixtime  = (New-TimeSpan -Start (Get-Date "01/01/1970") -End ((Get-Date).AddMinutes(-$tz))).TotalSeconds` -3h UTC
    $timestamp = ([string]$unixtime -replace "\..+") + "000000000"
    [double]$value = (Get-Counter $performance).CounterSamples.CookedValue.ToString("0.00").replace(",",".")` округлить в тип данных Double
    Invoke-RestMethod -Method POST -Uri "http://192.168.3.104:8086/write?db=powershell" -Body "performance,host=$(hostname),counter=CPU value=$value $timestamp"
    sleep 5
}
```

### Service

``` powershell
$powershell_Path = (Get-Command powershell).Source
$NSSM_Path = "C:\NSSM\NSSM-2.24.exe"
$Script_Path = "C:\NSSM\PerformanceTo-InfluxDB.ps1"
$Service_Name = "PerformanceTo-InfluxDB"
& $NSSM_Path install $Service_Name $powershell_Path -ExecutionPolicy Bypass -NoProfile -f $Script_Path
Get-Service $Service_Name | Start-Service
Get-Service $Service_Name | Set-Service -StartupType Automatic
```

## PSInfluxDB

`Install-Module psinfluxdb -Repository NuGet`\
`Get-InfluxUsers -server 192.168.3.102` список пользователей на сенвере
InfluxDB и права доступа\
`Get-InfluxDatabases -server 192.168.3.102` список баз данных\
`Get-InfluxDatabases -server 192.168.3.102 -creat -database test`
создать базу данных\
`Get-InfluxDatabases -server 192.168.3.102 -delete -database test`
удалить базу данных\
`Get-InfluxPolicy -server 192.168.3.102 -database PowerShell` список
политик хранения указанной базы данных\
`Get-InfluxPolicy -server 192.168.3.102 -database PowerShell -creat -policyName del2d -hours 48`
создать политику хранения\
`Get-InfluxPolicy -server 192.168.3.102 -database PowerShell -policyName del2d -default`
применить политику хранения к базе данных по умолчанию\
`Get-InfluxTables -server 192.168.3.102 -database PowerShell` список
таблиц/измерений в базе данных\
`$Data = Get-InfluxData -server 192.168.3.102 -database PowerShell -table ping | Format-Table`
отобразить данные в таблице\
`$Data | Where-Object {$_.SensorName -match "CPU_Package" -and $_.Value -gt 90} | Format-Table`
отфильтровать данные по двум параметрам (сенсоры процессора с
показателем выше 90)\
`$influx = Get-InfluxData -server 192.168.3.104 -database PowerShell -table speedtest`\
`Get-InfluxChart -time ($influx.time) -data ($influx.download) -title "SpeedTest Download" -path "C:\Users\Lifailon\Desktop"`
создать график (WinForms Chart) измерений и сохранить в jpeg-файл

## Telegraf

[Plugins](https://docs.influxdata.com/telegraf/v1.27/plugins/#input-plugins)

`iwr https://dl.influxdata.com/telegraf/releases/telegraf-1.27.1_windows_amd64.zip -UseBasicParsing -OutFile telegraf-1.27.1_windows_amd64.zip`\
`Expand-Archive .\telegraf-1.27.1_windows_amd64.zip -DestinationPath "C:\Telegraf"`\
`rm telegraf-1.27.1_windows_amd64.zip`\
`cd C:\Telegraf`\
`.\telegraf.exe -sample-config --input-filter cpu:mem:dns_query --output-filter influxdb > telegraf_nt.conf`
создать конфигурацию с выбарнными плагинами для сбора метрик\
`Start-Process notepad++ C:\Telegraf\telegraf_nt.conf`

    [[outputs.influxdb]]
      urls = ["http://192.168.3.104:8086"]
      database = "telegraf_nt"
      username = "user"
      password = "pass"
    [[inputs.cpu]]
      percpu = false
      totalcpu = true
    [[inputs.dns_query]]
      servers = ["8.8.8.8"]
      network = "udp"
      domains = ["."]
      record_type = "A"
      port = 53
      timeout = "2s"

`.\telegraf.exe --test -config C:\Telegraf\telegraf_nt.conf` тест
конфигурации (получения метрик с выводом в консоль)\
`C:\Telegraf\telegraf.exe -config C:\Telegraf\telegraf_nt.conf`
запустить telegraf (тест отправки данных)\
`.\telegraf.exe --config "C:\Telegraf\telegraf_nt.conf" --service install`
создать службу\
`Get-Service telegraf | Start-Service`\
`.\telegraf.exe --service uninstall`

`USE telegraf`\
`SELECT usage_idle,usage_system,usage_user FROM cpu`

## SQLite

``` powershell
$path = "$home\Documents\Get-Service.db"
$Module = Get-Module MySQLite
if ($Module -eq $null) {
    Install-Module MySQLite -Repository PSGallery -Scope CurrentUser
}
Import-Module MySQLite
New-MySQLiteDB -Path $path # создать БД
Invoke-MySQLiteQuery -Path $path -Query "CREATE TABLE Service (Name TEXT NOT NULL, DisplayName TEXT NOT NULL, Status TEXT NOT NULL);" # создать таблицу

$Service = Get-Service | select Name,DisplayName,Status
foreach ($S in $Service) {
    $Name = $S.Name
    $DName = $S.DisplayName
    $Status = $S.Status
    Invoke-MySQLiteQuery -Path $path -Query "INSERT INTO Service (Name, DisplayName, Status) VALUES ('$Name', '$DName', '$Status');"
}
```

`(Get-MySQLiteDB $path).Tables` список таблиц в базе\
`Invoke-MySQLiteQuery -Path $path -Query "SELECT name FROM sqlite_master WHERE type='table';"`
список таблиц в базе\
`Invoke-MySQLiteQuery -Path $path -Query "DROP TABLE Service;"` удалить
таблицу

``` powershell
$TableName = "Service"
Invoke-MySQLiteQuery -Path $path -Query "SELECT * FROM $TableName" # прочитать содержимое таблицы (в формате объекта)
```

`Get-Service | select  Name,DisplayName,Status | ConvertTo-MySQLiteDB -Path $path -TableName Service -force`
конвертировать объект в таблицу

### Database password

``` powershell
$Connection = New-SQLiteConnection -DataSource $path
$Connection.ChangePassword("password")
$Connection.Close()
Invoke-SqliteQuery -Query "SELECT * FROM Service" -DataSource "$path;Password=password"
```

## MySQL

`apt -y install mysql-server mysql-client`\
`mysql -V`\
`systemctl status mysql`\
`mysqladmin -u root password` задать пароль root

`nano /etc/mysql/mysql.conf.d/mysqld.cnf`

    [mysqld]
    user                    = mysql
    # pid-file              = /var/run/mysqld/mysqld.pid
    # socket                = /var/run/mysqld/mysqld.sock
    # port                  = 3306
    # datadir               = /var/lib/mysql
    # tmpdir                = /tmp
    bind-address            = 0.0.0.0
    mysqlx-bind-address     = 0.0.0.0
    log_error = /var/log/mysql/error.log

`systemctl restart mysql`\
`ss -tulnp | grep 3306`\
`ufw allow 3306/tcp`\
`nc -zv 192.168.1.253 3306`\
`tnc 192.168.1.253 -p 3306`

`mysql -u root -p`\
`SELECT user(), now(), version();`\
`quit;`

`mysql -u root -p -e 'SHOW TABLES FROM db_aduser;'` отобразить список
таблиц без подключения к консоли MySQL

`CREATE` создать БД, пользователя, таблицу\
`ALTER` управление столбцами таблице\
`DROP` удалить БД, пользователя, таблицу\
`USE` выбрать БД\
`SHOW` вывесли список БД, прав доступа пользователя (GRANTS), названия
столбцов и их свойства\
`GRANT` дать доступ пользователю к БД\
`REVOKE` удалить доступ пользователя к БД\
`UPDATE` изменить права доступа, значения с таблице\
`FLUSH` обновить права доступа\
`SELECT` отобразить выбранную БД, вывести список пользователей, выборка
данных в таблице\
`INSERT` внести данные\
`DELETE` удалить данные в (FROM) таблице

### DATA TYPE

`VARCHAR(N)` строка переменной длины, в формате ASCII, где один символ
занимает 1 байт, числом N указывается максимальная возможная длина
строки\
`NVARCHAR(N)` строка переменной длины, в формате Unicode, где один
символ занимает 2 байта\
`CHAR(N)/nchar(N)` строка фиксированной длины, которая всегда
дополняется справа пробелами до длины N и в базе данных она занимает
ровно N символов\
`INT` целое число, от -2147483648 до 2147483647, занимает 4 байта\
`FLOAT` число, в котором может присутствовать десятичная точка
(запятая)\
`BIT` флаг, Да - 1 или Нет - 0\
`DATE` формат даты, например 25.05.2023\
`TIME` 23:30:55.1234567\
`DATETIME` 25.05.2023 23:30:55.1234567

### DATABASE

    SHOW databases;                                                                     # вывести список БД
    CREATE DATABASE db_aduser;                                                          # создать БД
    CREATE DATABASE db_rep DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;  # создать БД с кодировкой UTF-8
    DROP DATABASE db_rep;                                                               # удалить БД
    USE db_aduser;                                                                      # выбрать/переключиться на выбранную БД
    SELECT database();                                                                  # отобразить выбранную БД

### USER

    SELECT USER,HOST FROM mysql.user;                                     # вывести список УЗ
    CREATE USER posh@localhost IDENTIFIED BY '1qaz!QAZ';                  # создать УЗ, которая будет подключаться с локального сервера
    CREATE USER posh@localhost IDENTIFIED BY '1qaz!QAZ';                  # создать УЗ, которая будет подключаться с указанного сервера
    CREATE USER posh@'192.168.3.99' IDENTIFIED BY '1qaz!QAZ';             # УЗ для доступа с конкретного сервера
    CREATE USER 'admin'@'%' IDENTIFIED BY 'Admin12#';                     # УЗ для доступа с любого сервера (% - wildcard)
    DROP USER posh@localhost;                                             # удалить пользователя
    SHOW GRANTS FOR posh@'%';                                             # отобразить права доступа пользователя
    GRANT ALL PRIVILEGES ON db_aduser.* TO posh@'192.168.3.99';           # полный доступ для posh к БД db_aduser
    GRANT ALL PRIVILEGES ON *.* TO posh@'%';                              # доступ к всем БД c любого клиентского хоста
    GRANT SELECT,DELETE ON mysql.* TO posh@'%';                           # права SELECT и DELETE на встроенную БД mysql
    REVOKE DELETE ON mysql.* FROM posh@'%';                               # удалить доступ DELETE
    UPDATE mysql.user SET super_priv='Y' WHERE USER='posh' AND host='%';  # изменить привелегии для пользователя
    SELECT USER,HOST,super_priv FROM mysql.user;                          # список УЗ и таблица с правами SUPER privilege
    FLUSH PRIVILEGES;                                                     # обновить права доступа

### TABLE

    SHOW TABLES;               # отобразить список всех таблиц
    SHOW TABLES LIKE '%user';  # поиск таблицы по wildcard-имени
    CREATE TABLE table_aduser (id INT NOT NULL AUTO_INCREMENT, Name VARCHAR(100), email VARCHAR(100), PRIMARY KEY (ID));  # создать таблицу
    DROP TABLE table_aduser;   # удалить таблицу

### COLUMN

    SHOW COLUMNS FROM table_aduser;                                                         # отобразить название стобцов и их свойства
    ALTER TABLE table_aduser DROP COLUMN id;                                                # удалить столбец id
    ALTER TABLE table_aduser ADD COLUMN info VARCHAR(10);                                   # добавить столбец info
    ALTER TABLE table_aduser CHANGE info new_info VARCHAR(100);                             # изменить имя столбца info на new_info и его тип данных
    ALTER TABLE table_aduser ADD COLUMN (id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY (ID)); # добавить столбец id

### INSERT

    INSERT table_aduser (Name,email) VALUES ('Alex','no-email');
    INSERT table_aduser (Name,email) VALUES ('Alex','no-email');
    INSERT table_aduser (Name) VALUES ('Support');
    INSERT table_aduser (Name) VALUES ('Jack');

### SELECT

    SELECT * FROM table_aduser;                     # содержимое всех стобцов в выбранной (FROM) таблице
    SELECT Name,email FROM table_aduser;            # содержимое указанных стобцов
    SELECT DISTINCT Name,Email FROM table_aduser;   # отобразить уникальные записи (без повторений)
    SELECT * FROM table_aduser ORDER BY Name;       # отсортировать по Name
    SELECT * FROM table_aduser ORDER BY Name DESC;  # обратная сортировка
    SELECT COUNT(*) FROM table_aduser;              # количество строк в таблице
    SELECT COUNT(new_info) FROM table_aduser;       # количество строк в столбце

### WHERE

    NOT; AND; OR                                                  # по приоритетам условий
    SELECT * FROM table_aduser WHERE Name = 'Alex';               # поиск по содержимому
    SELECT * FROM table_aduser WHERE NOT Name != 'Alex';          # условие NOT где Name не равен значению
    SELECT * FROM table_aduser WHERE email != '';                 # вывести строки, где содержимое email не рано null
    SELECT * FROM table_aduser WHERE email != '' OR id > 1000;    # или id выше 1000
    SELECT * FROM table_aduser WHERE Name RLIKE "support";        # регистронезависемый (RLIKE) поиск
    SELECT * FROM table_aduser WHERE Name RLIKE "^support";       # начинаются только с этого словосочетания

### DELETE

    SELECT * FROM table_aduser WHERE Name RLIKE "alex";   # найти и проверить значения перед удалением
    DELETE FROM table_aduser WHERE Name RLIKE "alex";     # Query OK, 2 rows affected # удалено две строки
    DELETE FROM table_aduser;                             # удалить ВСЕ значения

### UPDATE

    SELECT * FROM table_aduser WHERE Name = 'Jack';             # найти и проверить значение перед изменением
    UPDATE table_aduser SET Name = 'Alex' WHERE Name = 'Jack';  # изменить значение 'Jack' на 'Alex'
    UPDATE db_aduser.table_aduser SET Name='BCA' WHERE id=1;    # изменить значение в строке с ID 1

### CHECK

    CHECK TABLE db_aduser.table_aduser;     # проверить
    ANALYZE TABLE db_aduser.table_aduser;   # анализировать
    OPTIMIZE TABLE db_aduser.table_aduser;  # оптимизировать
    REPAIR TABLE db_aduser.table_aduser;    # восстановить
    TRUNCATE TABLE db_aduser.table_aduser;  # очистить

### DUMP

    mysqldump -u root -p --databases db_aduser > /bak/db_aduser.sql
    mysql -u root -p db_aduser < /bak/db_aduser.sql

    crontab -e
    00 22 * * * /usr/bin/mysqldump -uroot -p1qaz!QAZ db_zabbix | /bin/bzip2 > `date +/dump/zabbix/zabbix-\%d-\%m-\%Y-\%H:\%M.bz2`
    00 23 * * * /usr/bin/mysqldump -uroot -p1qaz!QAZ db_zabbix > `date +/dump/smb/zabbix-\%d-\%m-\%Y-\%H:\%M.sql`
    0 0 * * * find /dump/zabbix -mtime +7 -exec rm {} \;

    mysqldump -u root --single-transaction db_zabbix > /dump/zabbix/db_zabbix.sql
    mysql -u user_zabbix -p -e 'CREATE DATABASE db_zabbix;'
    mysql -u user_zabbix -p db_zabbix < /root/db_zabbix.sql

### innodb_force_recovery

    sed -i '/innodb_force_recovery/d' /etc/mysql/my.cnf # удалить
    mode=6; sed -i "/^\[mysqld\]/{N;s/$/\ninnodb_force_recovery=$mode/}" /etc/mysql/my.cnf # добавить mode 6
    systemctl restart mysql

    [mysqld]
    innodb_force_recovery=1 # сервер пытается начать работу независимо от того, есть ли поврежденные данные InnoDB или нет
    innodb_force_recovery=2 # удается восстановить работу за счет остановки потока команд, которые были частично выполнены или не выполнены (не запускает фоновые операции)
    innodb_force_recovery=3 # отменяет откат после восстановления поврежденных файлов (не пытается откатить транзакции)
    innodb_force_recovery=6 # запуск СУБД в режиме read only

## MySQL Connector NET

### Add-ADUser

``` powershell
$ip = "192.168.1.253"
$user = "posh"
$pass = "1qaz!QAZ"
$db = "db_aduser"
Add-Type –Path "$home\Documents\MySQL-Connector-NET\8.0.31-4.8\MySql.Data.dll"
$Connection = [MySql.Data.MySqlClient.MySqlConnection]@{
    ConnectionString="server=$ip;uid=$user;pwd=$pass;database=$db"
}
$Connection.Open()
$Command = New-Object MySql.Data.MySqlClient.MySqlCommand
$Command.Connection = $Connection
$UserList = Get-ADUser -filter * -properties name,EmailAddress
foreach ($user in $UserList) {
    $uname=$user.Name
    $uemail=$user.EmailAddress
    $Command.CommandText = "INSERT INTO table_aduser (Name,Email) VALUES ('$uname','$uemail')"
    $Command.ExecuteNonQuery()
}
$Connection.Close()
```

### Get-ADUser

``` powershell
$ip = "192.168.1.253"
$user = "posh"
$pass = "1qaz!QAZ"
$db = "db_aduser"
Add-Type –Path "$home\Documents\MySQL-Connector-NET\8.0.31-4.8\MySql.Data.dll"
$Connection = [MySql.Data.MySqlClient.MySqlConnection]@{
    ConnectionString = "server=$ip;uid=$user;pwd=$pass;database=$db"
}
$Connection.Open()
$Command = New-Object MySql.Data.MySqlClient.MySqlCommand
$Command.Connection = $Connection
$MYSQLDataAdapter = New-Object MySql.Data.MySqlClient.MySqlDataAdapter
$MYSQLDataSet = New-Object System.Data.DataSet
$Command.CommandText = "SELECT * FROM table_aduser"
$MYSQLDataAdapter.SelectCommand = $Command
$NumberOfDataSets = $MYSQLDataAdapter.Fill($MYSQLDataSet, "data")
$Collections = New-Object System.Collections.Generic.List[System.Object]
foreach($DataSet in $MYSQLDataSet.tables[0]) {
    $Collections.Add([PSCustomObject]@{
    Name = $DataSet.name;
    Mail = $DataSet.email
})
}
$Connection.Close()
$Collections
```

## MSSQL

`wget -qO- https://packages.microsoft.com/keys/microsoft.asc | apt-key add -`
импортировать GPG-ключ для репозитория\
`https://packages.microsoft.com/config/ubuntu/` выбрать репозиторий и
скопировать URL\
`add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/20.04/mssql-server-2019.list)"`\
`apt-get update` обновить список пакетов\
`apt-get install mssql-server`\
`/opt/mssql/bin/mssql-conf setup` скрипт начальной конфигурации (выбрать
редакцию, 3 - express и русский язык 9 из 11)\
`systemctl status mssql-server`\
`curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -`
установить клиент\
`curl https://packages.microsoft.com/config/ubuntu/20.04/prod.list | tee /etc/apt/sources.list.d/msprod.list`\
`apt-get update`\
`apt-get install mssql-tools`\
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc` добавить
в домашний каталог файла bashrc, что бы не писать путь к исполняемому
файлу\
`export PATH="$PATH:/opt/mssql-tools/bin"`\
`iptables -I INPUT 1 -p tcp --dport 1433 -j ACCEPT`

    sqlcmd -S localhost -U SA
    CREATE DATABASE itinvent
    go
    SELECT name FROM master.dbo.sysdatabases
    go

### System.Data.SqlClient

``` powershell
$user = "itinvent"
$pass = "itinvent"
$db   = "itinvent"
$srv  = "192.168.3.103"
$SqlConnection = New-Object System.Data.SqlClient.SqlConnection
$SqlConnection.ConnectionString = "server=$srv;database=$db;user id=$user;password=$pass;Integrated Security=false"

$SqlCommand = New-Object System.Data.SqlClient.SqlCommand` класс формата команды
$SqlCommand.CommandText = "SELECT * FROM ITINVENT.dbo.USERS"` отобразить содержимое таблицы
#$SqlCommand.CommandText = "SELECT LICENCE_DATE,DESCR,MODEL_NO,TYPE_NO FROM ITINVENT.dbo.ITEMS where LICENCE_DATE IS NOT NULL"
$SqlCommand.Connection = $SqlConnection` передать формат подключения
$SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter` создать адаптер подключения для выполнения SELECT запросов к БД
$SqlAdapter.SelectCommand = $SqlCommand` передать команду

$DataSet = New-Object System.Data.DataSet` создать объект приема данных формата XML
$SqlAdapter.Fill($DataSet)` заполнить данными полученные от адаптера (возвращает кол-во объектов)
$SqlConnection.Close()
$Data = $DataSet.Tables
$Data[0] | ft
```

### SqlClient INSERT

``` powershell
$user = "itinvent"
$pass = "itinvent"
$db   = "db_test"
$srv  = "192.168.3.103"
$sql = "INSERT INTO table_test (column_user) VALUES ('lifailon')"` добавить данные в таблицу table_test в колонку column_user
$SqlConnection = New-Object System.Data.SqlClient.SqlConnection
$SqlConnection.ConnectionString = "server=$srv;database=$db;user id=$user;password=$pass;Integrated Security=false"
$SqlCommand = New-Object System.Data.SqlClient.SqlCommand
$SqlCommand.CommandText = $sql
$SqlCommand.Connection = $SqlConnection
$SqlConnection.Open()
$rowsAffected = $SqlCommand.ExecuteNonQuery();` для запросов INSERT/UPDATE/DELETE не используется SqlDataAdapter
$SqlConnection.Close()
```

### SSMS INSERT

    USE [db_test]
    GO
    INSERT INTO [dbo].[table_test]
               ([column_user])
         VALUES
               ('lifailon')
    GO
    SELECT TOP (1000) [column_user]
    FROM [db_test].[dbo].[table_test]

### T-SQL

-   DDL (Data Definition Language / Язык определения данных). К этому
    типу относятся команды, которые создают базу данных, таблицы,
    индексы, хранимые процедуры.\
    `CREATE` создает объекты базы данных (саму базу даных, таблицы,
    индексы и т.д.)\
    `ALTER` изменяет объекты базы данных\
    `DROP` удаляет объекты базы данных\
    `TRUNCATE` удаляет все данные из таблиц

-   DML (Data Manipulation Language / Язык манипуляции данными). К этому
    типу относят команды по выбору, обновлению, добавлению и удалению
    данных.\
    `SELECT` извлекает данные из БД\
    `UPDATE` обновляет данные\
    `INSERT` добавляет новые данные\
    `DELETE` удаляет данные

-   DCL (Data Control Language / Язык управления доступа к данным). К
    этому типу относят команды, которые управляют правами по доступу к
    данным.\
    `GRANT` предоставляет права для доступа к данным\
    `REVOKE` отзывает права на доступ к данным

```sql
    -- Переменные
    DECLARE @text NVARCHAR(20), @int INT;
    SET @text='Test';
    SET @int = 21;
    select @text,@int

    -- Имена сервера и экземпляра 
    Select @@SERVERNAME as [Server\Instance]; 

    -- версия SQL Server 
    Select @@VERSION as SQLServerVersion; 

    -- Текущая БД (БД, в контексте которой выполняется запрос)
    Select DB_NAME() AS CurrentDB_Name;

    -- Время работы с момента запуска сервера
    SELECT  @@Servername AS ServerName ,
            create_date AS  ServerStarted ,
            DATEDIFF(s, create_date, GETDATE()) / 86400.0 AS DaysRunning ,
            DATEDIFF(s, create_date, GETDATE()) AS SecondsRunnig
    FROM    sys.databases
    WHERE   name = 'tempdb';

    -- Количество активных соединений
    SELECT  @@Servername AS Server ,
            DB_NAME(database_id) AS DatabaseName ,
            COUNT(database_id) AS Connections ,
            Login_name AS  LoginName ,
            MIN(Login_Time) AS Login_Time ,
            MIN(COALESCE(last_request_end_time, last_request_start_time))
                                                             AS  Last_Batch
    FROM    sys.dm_exec_sessions
    WHERE   database_id > 0
            AND DB_NAME(database_id) NOT IN ( 'master', 'msdb' )
    GROUP BY database_id ,
             login_name
    ORDER BY DatabaseName;

    -- Статус Backup
    SELECT  @@Servername AS ServerName ,
            d.Name AS DBName ,
            MAX(b.backup_finish_date) AS LastBackupCompleted
    FROM    sys.databases d
            LEFT OUTER JOIN msdb..backupset b
                        ON b.database_name = d.name
                           AND b.[type] = 'D'
    GROUP BY d.Name
    ORDER BY d.Name;

    -- Путь к Backup
    SELECT  @@Servername AS ServerName ,
            d.Name AS DBName ,
            b.Backup_finish_date ,
            bmf.Physical_Device_name
    FROM    sys.databases d
            INNER JOIN msdb..backupset b ON b.database_name = d.name
                                            AND b.[type] = 'D'
            INNER JOIN msdb.dbo.backupmediafamily bmf ON b.media_set_id = bmf.media_set_id
    ORDER BY d.NAME ,
            b.Backup_finish_date DESC; 

    -- Вывести список всех БД, модели восстановления и путь к mdf/ldf
    EXEC sp_helpdb; 
    SELECT  @@SERVERNAME AS Server ,
            d.name AS DBName ,
            create_date ,
            recovery_model_Desc AS RecoveryModel ,
            m.physical_name AS FileName
    FROM    sys.databases d
            JOIN sys.master_files m ON d.database_id = m.database_id
    ORDER BY d.name;

    -- Размер БД
    with fs
    as
    (
        select database_id, type, size * 8.0 / 1024 size
        from sys.master_files
    )
    select 
        name,
        (select sum(size) from fs where type = 0 and fs.database_id = db.database_id) DataFileSizeMB,
        (select sum(size) from fs where type = 1 and fs.database_id = db.database_id) LogFileSizeMB
    from sys.databases 

    -- Поиск таблицы по маске имени (вывод: названия схемы где распологается объект, тип объекта, дата создания и последней модификации):
    select [object_id], [schema_id],
           schema_name([schema_id]) as [schema_name], 
           [name], 
           [type], 
           [type_desc], 
           [create_date], 
           [modify_date]
    from sys.all_objects
    -- where [name]='INVENT';
    where [name] like '%INVENT%';

    -- Кол-во строк в таблицах
    SELECT  @@ServerName AS Server ,
            DB_NAME() AS DBName ,
            OBJECT_SCHEMA_NAME(p.object_id) AS SchemaName ,
            OBJECT_NAME(p.object_id) AS TableName ,
            i.Type_Desc ,
            i.Name AS IndexUsedForCounts ,
            SUM(p.Rows) AS Rows
    FROM    sys.partitions p
            JOIN sys.indexes i ON i.object_id = p.object_id
                                  AND i.index_id = p.index_id
    WHERE   i.type_desc IN ( 'CLUSTERED', 'HEAP' )
                                 -- This is key (1 index per table) 
            AND OBJECT_SCHEMA_NAME(p.object_id) <> 'sys'
    GROUP BY p.object_id ,
            i.type_desc ,
            i.Name
    ORDER BY SchemaName ,
            TableName; 

    -- Найти строковое (nvarchar) значение 2023 по всем таблицам базы данных
    -- Отображается в какой таблице и столбце хранится значение, а также количество найденных пары таблица-колонка
    set nocount on
    declare @name varchar(128), @substr nvarchar(4000), @column varchar(128)
    set @substr = '%2023%'
    declare @sql nvarchar(max);
    create table`rslt 
    (table_name varchar(128), field_name varchar(128), [value] nvarchar(max))
    declare s cursor for select table_name as table_name from information_schema.tables where table_type = 'BASE TABLE' order by table_name
    open s
    fetch next from s into @name
    while @@fetch_status = 0
    begin
    declare c cursor for 
    select quotename(column_name) as column_name from information_schema.columns 
    where data_type in ('text', 'ntext', 'varchar', 'char', 'nvarchar', 'char', 'sysname', 'int', 'tinyint') and table_name  = @name
    set @name = quotename(@name)
    open c
    fetch next from c into @column
    while @@fetch_status = 0
    begin
    --print 'Processing table - ' + @name + ', column - ' + @column
    set @sql='insert into`rslt select ''' + @name + ''' as Table_name, ''' + @column + ''', cast(' + @column + 
    ' as nvarchar(max)) from' + @name + ' where cast(' + @column + ' as nvarchar(max)) like ''' + @substr + '''';
    print @sql;
    exec(@sql);
    fetch next from c into @column;
    end
    close c
    deallocate c
    fetch next from s into @name
    end
    select table_name as [Table Name], field_name as [Field Name], count(*) as [Found Mathes] from`rslt
    group by table_name, field_name
    order by table_name, field_name
    drop table`rslt
    close s
    deallocate s

    -- Поиск в таблице [CI_HISTORY] и столбцу [HIST_ID]:
    SELECT * FROM ITINVENT.dbo.CI_HISTORY where [HIST_ID] like '%2023%';

    -- Узнать фрагментацию индексов
    DECLARE @db_id SMALLINT;
    SET @db_id = DB_ID(N'itinvent');
    IF @db_id IS NULL
    BEGIN;
        PRINT N'Неправильное имя базы';
    END;
    ELSE
    BEGIN;
        SELECT
            object_id AS [ID объекта],
            index_id AS [ID индекса],
            index_type_desc AS [Тип индекса],
            avg_fragmentation_in_percent AS [Фрагментация в %]
            
        FROM sys.dm_db_index_physical_stats(@db_id, NULL, NULL, NULL , 'LIMITED')
         
        ORDER BY [avg_fragmentation_in_percent] DESC;
    END;
    GO

    -- TempDB
    -- Initial size - начальный/минимальный размер БД (1024 MB)
    -- Autogrowh - прирост (512MB)
    -- По умолчанию tempdb настроена на авто-расширение (Autogrow) и при каждой перезагрузке SQL Server пересоздаёт файлы этой БД с минимальным размером инициализации.
    -- Увеличив размер инициализации файлов tempdb, можно свести к минимуму затраты системных ресурсов на операции авто-расширения.

    -- Изменить путь к БД:
    USE master;
    GO
    ALTER DATABASE tempdb
    MODIFY FILE (NAME = tempdev, FILENAME = 'F:\tempdb.mdf');
    GO
    ALTER DATABASE tempdb
    MODIFY FILE (NAME = temp2, FILENAME = 'F:\tempdb_mssql_2.ndf');
    GO
    ALTER DATABASE tempdb
    MODIFY FILE (NAME = temp3, FILENAME = 'F:\tempdb_mssql_3.ndf');
    GO
    ALTER DATABASE tempdb
    MODIFY FILE (NAME = temp4, FILENAME = 'F:\tempdb_mssql_4.ndf');
    GO
    ALTER DATABASE tempdb
    MODIFY FILE (NAME = temp5, FILENAME = 'F:\tempdb_mssql_5.ndf');
    GO
    ALTER DATABASE tempdb
    MODIFY FILE (NAME = temp6, FILENAME = 'F:\tempdb_mssql_6.ndf');
    GO
    ALTER DATABASE tempdb
    MODIFY FILE (NAME = temp7, FILENAME = 'F:\tempdb_mssql_7.ndf');
    GO
    ALTER DATABASE tempdb
    MODIFY FILE (NAME = temp8, FILENAME = 'F:\tempdb_mssql_8.ndf');
    GO
    ALTER DATABASE tempdb
    MODIFY FILE (NAME = templog, FILENAME = 'F:\templog.ldf');
    GO

    -- Указать размер файла:
    MODIFY FILE (NAME = temp2, FILENAME = 'F:\tempdb_mssql_2.ndf' , SIZE = 1048576KB , FILEGROWTH = 524288KB);

### Тип резервной копии

-   Full (Полная копия). Когда стартует полное резервирование,
    записывается Log Sequence Number (LSN - последовательный номер
    журнала), а так же LSN записывается и при завершении полного
    резервирования. Этот LSN является механизмом, используемым SQL
    Server, чтобы знать, в каком порядке выполнялись операторы INSERT,
    UPDATE или DELETE. При этом наличие записанных LSN начала и
    окончания, как части полного бэкапа, обеспечивает согласованное с
    точки зрения транзакций резервное копирование, поскольку при полном
    резервном копировании учитываются изменения, произошедшие во время
    резервного копирования. Это обеспечивает обработку таких транзакций
    в процессе восстановления бэкапа.
-   Differential (дифференциальная/разностная копия). Хранит данных,
    изменившиеся с момента последней Полной резервной копии. При
    восстановлении нужно сначала восстановить Полную резервную копию в
    режиме NORECOVERY, потом можно применить любую из последующих
    Разностных копий, без предыдущей Полной резервной копии Разностная
    копия бесполезна. Каждая последующая Разностная копия будет хранить
    все данные, входящие в предыдущую Разностную резервную копию,
    сделанную после предыдущей Полной копии.
-   Incremental (инкрементальная/копия журналов транзакций). Резервное
    копирования журнала транзакций копирует все транзакции, которые
    произошли с момента последнего резервного копирования, а затем
    урезает журнал транзакций для освобождения дискового пространства.
    Транзакции происходят в определенном порядке (LSN), бэкап журнала
    поддерживает этот порядок транзакций. Бэкапы журналов транзакций
    должны восстанавливаться по порядку. Для восстановления базы данных
    потребуется вся цепочка резервных копий: полная и все последующие
    инкрементальные журнала транзакций.

### Модели восстановления

-   Simple (Простая). Хранится только необходимый для жизни остаток
    журнала транзакций. Журнал транзакций (лог) автоматически очищается.
    Создание резервных копий журнала транзакций невозможна, поэтому
    остается самое ограниченное число опций по восстановлению.
    Недоступен функционал: Always On, Point-In-Time восстановление,
    Резервные копии журнала транзакций.
-   Full (Полная). Хранится журнал транзакций всех изменений в БД с
    момента последнего резервного копирования журнала транзакций. Журнал
    транзакций не будет очищаться до тех пор, пока не будет сделана
    резервная копия журнала транзакций.
-   Bulk logged (С неполным протоколированием). Идентична Full, за
    исключение: SELECT INTO, BULK INSERT и BCP, INSERT INTO SELECT,
    операции с индексами (CREATE INDEX, ALTER INDEX REBUILD, DROP INDEX)

### Системные БД

-   master. Хранятся все данные системного уровня (конфигурация системы,
    сведенья об учетных записях входа, информация обо всех других базах
    данных) для экземпляра SQL Server.
-   tempdb. Рабочее пространство для временных объектов, таких как
    глобальные или локальные временные таблицы, временные хранимые
    процедуры, табличные переменные и курсоры. Пересоздаётся при каждом
    запуске SQL Server.
-   model. Используется в качестве шаблона для всех баз данных,
    создаваемых в экземпляре SQL Server, все содержимое базы данных
    model, включая параметры базы данных, копируется в создаваемую базу
    данных. Так как база данных tempdb создается каждый раз при запуске
    SQL Server, база данных model всегда должна существовать в системе
    SQL Server.
-   msdb. Используется агентом SQL Server для создания расписания
    предупреждений (оператор) и выполнение заданий, а также другими
    компонентами. SQL Server хранит полный журнал резервного копирования
    и восстановления в базе данных msdb. Для отправки почты оператору
    используется: USE \[msdb\].
-   resource. Доступная только для чтения база данных, которая содержит
    все системные объекты, например sys.objects, физически хранятся в
    базе данных resource, но логически присутствуют в схеме sys каждой
    базы данных.

### Регламентные операции

-   Проверка целостности базы данных

`DBCC CHECKDB`

-   Индексы. Индексы используются для быстрого поиска данных без
    необходимости поиска/просмотра всех строк в таблице базы данных при
    каждом обращении к таблице базы данных. Индекс ускоряет процесс
    запроса, предоставляя быстрый доступ к строкам данных в таблице,
    аналогично тому, как указатель в книге помогает вам быстро найти
    необходимую информацию. Индексы предоставляют путь для быстрого
    поиска данных на основе значений в этих столбцах. Для каждого
    индекса обязательно хранится его статистика. MS SQL Server
    самостоятельно создает и изменяет индексы при работе с базой. С
    течением времени данные в индексе становятся фрагментированными,
    т.е. разбросанными по базе данных, что серьезно снижает
    производительность запросов. Если фрагментация составляет от 5 до
    30% (стандартно в задании 15%), то рекомендуется ее устранить с
    помощью реорганизации, при фрагментации выше 30% (по умолчанию в
    задаче \> 30% фрагментации и число страниц \> 1000) необходимо
    полное перестроение индексов. После перестроения планово
    используется только реорганизация.

-   Реорганизация (Reorganize) или дефрагментация индекса --- это серия
    небольших локальных перемещений страниц так, чтобы индекс не был
    фрагментирован. После реорганизации статистика не обновляется. Во
    время выполнения почти все данные доступны, пользователи смогут
    работать.

`sp_msforeachtable N'DBCC INDEXDEFRAG (<имя базы данных>, ''?'')'`

-   Перестроение (Rebuild) индексов (или задача в мастере планов
    обслуживания: Восстановить индекс) запускает процесс полного
    построения индексов. В версии MS SQL Server Standard происходит
    отключение всех клиентов от базы на время выполнения операции. После
    перестроения обязательно обновляется статистика.

`sp_msforeachtable N'DBCC DBREINDEX (''?'')'`

-   Обновление статистики. Статистика --- небольшая таблица (обычно до
    200 строк), в которой хранится обобщенная информация о том, какие
    значения и как часто встречаются в таблице. На основании статистики
    сервер принимает решение, как лучше построить запрос. Когда
    происходят запросы к БД (например, SELECT) вы получаете данные, но
    не описываете то, как эти данные должны быть извлечены. В получении
    и обработке данных помогает статистика. Во время выполнения
    процедуры обновления статистики данные не блокируются.

`exec sp_msforeachtable N'UPDATE STATISTICS ? WITH FULLSCAN'`

-   Очистка процедурного кэша, выполняется после обновления статистики.
    Оптимизатор MS SQL Server кэширует планы запросов для их повторного
    выполнения. Это делается для того, чтобы экономить время,
    затрачиваемое на компиляцию запроса в том случае, если такой же
    запрос уже выполнялся и его план известен. После обновия статистики,
    не будет очищен процедурный кэш, то SQL Server может выбрать старый
    (неоптимальный) план запроса из кэша вместо того, чтобы построить
    новый (более оптимальный) план.

`DBCC FREEPROCCACHE`

## Elasticsearch

`Install-Module -Name Elastic.Console -AllowPrerelease` [github
source](https://github.com/elastic/powershell/blob/master/Elastic.Console/README.md)\
`Get-Command -Module Elastic.Console`\
`Get-ElasticsearchVersion`\
`Set-ElasticsearchVersion 7.3.0`\
`Invoke-Elasticsearch` REST API запросы

## CData

[PowerShell Gallery
CData](https://www.powershellgallery.com/profiles/CData)\
[Automate Elasticsearch Integration Tasks from
PowerShell](https://www.cdata.com/kb/tech/elasticsearch-ado-powershell.rst)

`Install-Module ElasticsearchCmdlets` [пакет драйвера в
psgallery](https://www.powershellgallery.com/packages/ElasticsearchCmdlets/23.0.8565.1)\
`Import-Module ElasticsearchCmdlets`\
`Get-Command -Module ElasticsearchCmdlets`

``` powershell
$elasticsearch = Connect-Elasticsearch  -Server "$Server" -Port "$Port" -User "$User" -Password "$Password"
$shipcity = "New York"
$orders = Select-Elasticsearch -Connection $elasticsearch -Table "Orders" -Where "ShipCity = `'$ShipCity`'"` поиск и получение данных
$orders = Invoke-Elasticsearch -Connection $elasticsearch -Query 'SELECT * FROM Orders WHERE ShipCity = @ShipCity' -Params @{'@ShipCity'='New York'}` SQL запросы
```

### ADO.NET Assembly

`Install-Package CData.Elasticsearch` [пакет драйвера в
nuget](https://www.nuget.org/packages/CData.Elasticsearch)\
`[Reflection.Assembly]::LoadFile("C:\Program Files\PackageManagement\NuGet\Packages\CData.Elasticsearch.23.0.8565\lib\net40\System.Data.CData.Elasticsearch.dll")`

``` powershell
$connect = New-Object System.Data.CData.Elasticsearch.ElasticsearchConnection("Server=127.0.0.1;Port=9200;User=admin;Password=123456;")
$connect.Open()
$sql = "SELECT OrderName, Freight from Orders"
$da = New-Object System.Data.CData.Elasticsearch.ElasticsearchDataAdapter($sql, $conn)
$dt = New-Object System.Data.DataTable
$da.Fill($dt)
$dt.Rows | foreach {
Write-Host $_.ordername $_.freight
}
```

### UPDATE

``` powershell
Update-Elasticsearch -Connection $Elasticsearch -Columns @('OrderName','Freight') -Values @('MyOrderName', 'MyFreight') -Table Orders -Id "MyId"

$cmd =  New-Object System.Data.CData.Elasticsearch.ElasticsearchCommand("UPDATE Orders SET ShipCity='New York' WHERE Id = @myId", $conn)
$cmd.Parameters.Add(new System.Data.CData.Elasticsearch.ElasticsearchParameter("@myId","10456255-0015501366"))
$cmd.ExecuteNonQuery()
```

### INSERT

``` powershell
Add-Elasticsearch -Connection $Elasticsearch -Table Orders -Columns @("OrderName", "Freight") -Values @("MyOrderName", "MyFreight")

$cmd =  New-Object System.Data.CData.Elasticsearch.ElasticsearchCommand("INSERT INTO Orders (ShipCity) VALUES (@myShipCity)", $conn)
$cmd.Parameters.Add(new System.Data.CData.Elasticsearch.ElasticsearchParameter("@myShipCity","New York"))
$cmd.ExecuteNonQuery()
```

### DELETE

``` powershell
Remove-Elasticsearch -Connection $Elasticsearch -Table "Orders" -Id "MyId"

$cmd =  New-Object System.Data.CData.Elasticsearch.ElasticsearchCommand("DELETE FROM Orders WHERE Id=@myId", $conn)
$cmd.Parameters.Add(new System.Data.CData.Elasticsearch.ElasticsearchParameter("@myId","001d000000YBRseAAH"))
$cmd.ExecuteNonQuery()
```

## ODBC

`Get-Command -Module Wdac`\
`Get-OdbcDriver | ft` список установленных драйверов

[Elasticsearch ODBC драйвер для доступа к данным Elasticsearch из
Microsoft
PowerShell](https://www.elastic.co/guide/en/elasticsearch/reference/current/sql-client-apps-ps1.html)

``` powershell
$connectstring = "DSN=Local Elasticsearch;"
$sql = "SELECT * FROM library"
$conn = New-Object System.Data.Odbc.OdbcConnection($connectstring)
$conn.open()
$cmd = New-Object system.Data.Odbc.OdbcCommand($sql,$conn)
$da = New-Object system.Data.Odbc.OdbcDataAdapter($cmd)
$dt = New-Object system.Data.datatable
$null = $da.fill($dt)
$conn.close()
$dt
```

## PostgreSQL

### ODBC Driver

[Скачать и установить
драйвер](https://www.postgresql.org/ftp/odbc/versions/msi/)

``` powershell
$dbServer = "192.168.3.101"
$port = "5432"
$dbName = "test"
$dbUser = "admin"
$dbPass = "admin"
$szConnect = "Driver={PostgreSQL Unicode(x64)};Server=$dbServer;Port=$port;Database=$dbName;Uid=$dbUser;Pwd=$dbPass;" 

$cnDB = New-Object System.Data.Odbc.OdbcConnection($szConnect)
$dsDB = New-Object System.Data.DataSet
try {
    $cnDB.Open()
    $adDB = New-Object System.Data.Odbc.OdbcDataAdapter
    $adDB.SelectCommand = New-Object System.Data.Odbc.OdbcCommand("SELECT id, name, age, login FROM public.users" , $cnDB)
    $adDB.Fill($dsDB)
    $cnDB.Close()
}
catch [System.Data.Odbc.OdbcException] {
    $_.Exception
    $_.Exception.Message
    $_.Exception.ItemName
}
foreach ($row in $dsDB[0].Tables[0].Rows) {
    $row.login
    $row.age
}
```

### npgsql

[Source](https://github.com/npgsql/npgsql)\
[Package](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL)

``` powershell
# Подключаем сборку Npgsql
Add-Type -Path "$home\Documents\Npgsql.PostgreSQL\8.0.4\net8.0\Npgsql.EntityFrameworkCore.PostgreSQL.dll"
# Определяем строку подключения к базе данных PostgreSQL
$connString = "Host=myserver;Username=mylogin;Password=mypass;Database=mydatabase"
# Создаем объект подключения
$conn = New-Object Npgsql.NpgsqlConnection($connString)
# Открываем соединение с базой данных
$conn.Open()
try {
    # Создаем SQL команду
    $cmd = $conn.CreateCommand()
    $cmd.CommandText = "INSERT INTO data (some_field) VALUES (@p)"
    # Добавляем параметр и его значение
    $param = $cmd.Parameters.Add("p", [NpgsqlTypes.NpgsqlDbType]::Text)
    $param.Value = "Hello world"
    # Выполняем команду
    $cmd.ExecuteNonQuery() | Out-Null
} finally {
    $cmd.Dispose()
}
# Извлекаем все строки из таблицы
try {
    # Создаем команду SQL для извлечения данных
    $cmd = $conn.CreateCommand()
    $cmd.CommandText = "SELECT some_field FROM data"
    # Выполняем команду и получаем объект чтения данных
    $reader = $cmd.ExecuteReader()
    # Читаем и выводим данные построчно
    while ($reader.Read()) {
        Write-Host $reader.GetString(0)
    }
} finally {
    # Освобождаем ресурсы чтения данных и команды
    $reader.Dispose()
    $cmd.Dispose()
}
# Закрываем соединение с базой данных
$conn.Close()
$conn.Dispose()
```
