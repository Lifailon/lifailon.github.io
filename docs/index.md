---
hide:
  - title
---

<p align="center">
    <a href="https://github.com/Lifailon/PS-Commands"><img title="PS-Commands Logo"src="Logo/PS-Commands-Logo.png"></a>
</p>

<p align="center">
    <a href="https://github.com/Lifailon/PS-Commands"><img title="GitHub Source"src="https://img.shields.io/badge/GitHub_Source-34495E?style=for-the-badge&logo=github&logoColor=white"></a>
    <a href="https://www.nuget.org/profiles/Lifailon"><img title="NuGet Modules"src="https://img.shields.io/badge/NuGet_Modules-4479A1.svg?style=for-the-badge&logo=nuget&logoColor=white"></a>
    <a href="Linux"><img title="Linux Commands"src="https://img.shields.io/badge/Linux_Commands-FCC624?style=for-the-badge&logo=linux&logoColor=black"></a>
</p>

<p align="center">
    <b>Большая база заметок PowerShell на русском языке.</b>
</p>

Структура языка, синтаксис командлетов и модулей, работа с объектами, данными, файловой системой, сетевыми утилитами, базами данных, REST API, классами и методами платформы .NET, системами мониторинга и системного администрирования (Active Directory, MS Exchange, Hyper-V, VMWare). Заметки основаны на принципе примеров и краткого описания.

Исходный репозиторий содержит набор полезных [скриптов и модулей](https://github.com/Lifailon/PS-Commands/tree/rsa/Scripts) автора, а также [тестовый стенд WinForms](https://github.com/Lifailon/PS-Commands/tree/rsa/WinForms) с примерами реализации большинства функционала (DataGridView, Button, Checkbox и т.д.), который можно использовать как шаблон для создания программы с графическим интерфейсом.

---

- 💬 **20.05.2024**: Добавлены заметки по работе с 🐳 [Docker](Docker)
- 💬 **15.07.2024**: Добавлены заметки по работе с системными командами и консольными утилитами 🐧 [Linux](Linux)
- 💬 **25.08.2024**: Обновлены заметки по направлению [DevOps](DevOps)

---

Другие проекты:

- 🧲 [Kinozal Bot](https://github.com/Lifailon/Kinozal-Bot) - Telegram бот, который позволяет автоматизировать процесс доставки контента до вашего телевизора, используя только телефон, предоставляющий привычный и удобный интерфейс для взаимодействия с торрент трекером [Кинозал](https://kinozal.tv), а также возможность управлять торрент клиентом [qBittorrent](https://github.com/qbittorrent/qBittorrent) или [Transmission](https://github.com/transmission/transmission) на вашем компьютере и синхронизацию с [Plex Media Server](https://www.plex.tv/personal-media-server), находясь удаленно от дома.

- ✨ [TorAPI](https://github.com/Lifailon/TorAPI/blob/main/README_RU.md) - неофициальный `API` (backend) для торрент трекеров RuTracker, Kinozal, RuTor и NoNameClub. Используется для быстрого поиска раздач, а также получения torrent-файлов, магнитных ссылок и подробной информации о раздаче по названию фильма, сериала или идентификатору раздачи, а также предоставляет новостную RSS ленту для всех провайдеров.

- 🔎 [LibreKinopoisk](https://github.com/Lifailon/LibreKinopoisk) - расширение для Google Chrome, которое добавляет кнопки на сайт Кинопоиск и предоставляет интерфейс **TorAPI** в стиле [Jackett](https://github.com/Jackett/Jackett) (без необходимости устанавливать серверную часть и использовать VPN) для быстрого поиска фильмов и сериалов в открытых источниках.

- ❤️ [WebTorrent Desktop api](https://github.com/Lifailon/webtorrent-desktop-api) - форк [WebTorrent Desktop](https://github.com/webtorrent/webtorrent-desktop) клиента, в котором добавлен механизм удаленного управления через `REST API` на базе [Express Framework](https://github.com/expressjs/express).

- 📡 [Reverse Proxy .NET](https://github.com/Lifailon/rpnet/blob/main/README_RU.md) - кроссплатформенная утилита командной строки для реализации обратного прокси-сервер на базе **.NET**. Используется для предоставления доступа хостам в сети с одного сетевого интерфейса к удаленным приложениям через протоколы **TCP**, **UDP** или **HTTP/HTTPS** (поддерживаются `GET` и `POST` запросы для доступа к внешним ресурсам через Интернет) доступных через другой сетевой интерфейс на вашем хосте без лишних настроек и с поддержкой авторизации.

- 🐚 [Intelli Shell](https://github.com/Lifailon/intellishell) - это обработчик, работающий поверх оболочки Bash и реализующий механизм автодополнения команд с использованием выпадающего списка в режиме реального времени. Вы можете просматривать историю выполненных команд с поддержкой фильтрации и регулярных выражений, а также использовать навигацию по каталогам, не покидая текущую строку ввода. Кроме того, поддерживается вывод переменных, быстрый поиск с фильтрацией по выводу последней выполненной команды (интерактивный **Grep**), поиск исполняемых команд и доступ к примерам для них через сервис [cheet.sh](https://github.com/chubin/cheat.sh).

---

Проекты на PowerShell:

- [REST API и Web-сервер](https://github.com/Lifailon/WinAPI) (статья на [Хабр](https://habr.com/ru/articles/783022/)) для удаленного управления ОС Windows через браузер или REST-клиент (например, curl в Linux). Запуск и остановка служб и процессов, получение информации о системе, просмотр и фильтрация журналов событий (логов) в браузере и т.д.

- [Syslog сервер и клиент](https://github.com/Lifailon/pSyslog) на базе .NET и PowerShell.

- [Remote Shadow Administrator](https://github.com/Lifailon/RSA). Инструмент удаленного подключения к текущим RDP-сессиям пользователей на базе RDShadow. Также содержит набор модулей, направленного на автоматизацию удаленного администрирования и управления ОС Windows (пользовательские процессы, службы, обновления, настройки KMS, NTP и т.д.).

- [Графический интерфейс для iperf3](https://github.com/Lifailon/iPerf-GUI) и модуль [psiperf](https://github.com/Lifailon/PS-iPerf) для мониторинга пропускной способности канала связи.

- [AD Manager](https://github.com/Lifailon/AD-Manager). Форма автоматизации создания пользователей в Active Directory и почтовых ящиков Exchange, с возможностью формирования базового отчета, управления группами и пользователями.

- [PST Export GUI](https://github.com/Lifailon/PST-Export-GUI). Графический интерфейс получения состояния баз данных и почтовых ящиков на серверах Exchange 2010-2016, просмотр и фильтрация Message Tracking Log, а также создания и управления заданями экспорта почтовых ящиков в формат PST.

- [VMWare Inventory](https://github.com/Lifailon/VMW-Invent). Графический инерфейс реализующий формат отчета состояния виртуальных машин, дисков, ESXi хостов, хранилищ данных и сетевых интерфейсов в реальном времени, а также централизованное управлением питанием (на базе WinForms и модуля PowerCLI).

- [WinEvent Viewer](https://github.com/Lifailon/WinEvent-Viewer). Интерфейс просмотра и фильтрации журналов событий (логов) ОС Windows.

- [DNS Change Tray](https://github.com/Lifailon/DNS-Change-Tray). Программа для быстрой смены DNS-адреса на выбранном сетевом адаптере в трее.

- [ACL-Backup](https://github.com/Lifailon/ACL-Backup). Резервное копирование списка прав доступа файловой системы NTFS в txt-файл с возможностью восстановления.

- [Интеграция InfluxDB 1.x](https://github.com/Lifailon/psinfluxdb) и PowerShell для управления базами данных, политиками хранения, пользователями и таблицами, а также вывод измерений в формате объекта.

- [Сценарий развертывания](https://github.com/Lifailon/Deploy-Selenium) всех зависимостей для работы с Selenium через PowerShell и модули для [AI (GPT)](https://github.com/Lifailon/gpt-cli), [SpeedTest](https://github.com/Lifailon/Selenium-Modules/blob/rsa/Modules/Get-SpeedTest/Get-SpeedTest.psm1) и [Translation](https://github.com/Lifailon/Selenium-Modules/blob/rsa/Modules/Get-Translation/Get-Translation.psm1) с использованием разных провайдеров. Статья на habr [PowerShell и Selenium. Проще, чем кажется](https://habr.com/ru/articles/785538).

- [Модуль](https://github.com/Lifailon/PowerShell.HardwareMonitor) для локального и удаленного сбора метрик температуры, нагрузки и других датчиков системы через [LibreHardwareMonitor](https://github.com/LibreHardwareMonitor/LibreHardwareMonitor) и [OpenHardwareMonitor](https://github.com/openhardwaremonitor/openhardwaremonitor). Статья на habr [мониторинг температуры Windows](https://habr.com/ru/articles/793296) (создание метрик, настройка InfluxDB и Grafana).

- [Темы производительности системы](https://github.com/Lifailon/oh-my-posh-themes-performance) для [oh-my-posh](https://github.com/jandedobbeleer/oh-my-posh).

- [PSEverything](https://github.com/Lifailon/PSEverything). Модуль мнгновенного поиска файлов на локальных и удаленных системах Windows через [Everything](https://www.voidtools.com) и [библиотеку .NET](https://github.com/dipique/everythingio). 

- [Интерфейс командной строки](https://github.com/Lifailon/CrystalDisk-Cli) для [CrystalDiskInfo](https://github.com/hiyohiyo/CrystalDiskInfo).

- [Console-Translate](https://github.com/Lifailon/Console-Translate). Интерфейс командной строки перевода текста (использует 3 провайдера и режим автоматического определения исходного языка).

- [Console-Download](https://github.com/Lifailon/Console-Download). Инструмент командной строки для загрузки файлов из переданного списка URL-адресов в многопоточном режиме и отображения скорости загрузки в реальном времени. Реализует тестирования пропускной способности сетевого интерфейса через хосты [Looking Glass](https://github.com/gnif/LookingGlass) (интеграция с [Looking.House](https://looking.house)) с целью отладки датчиков системы мониторинга и проверки скорости сети Интернет.

- [PSDomainTest](https://github.com/Lifailon/PSDomainTest?tab=readme-ov-file) для тестирования DNS-записей домена с использованием API [ZoneMaster](https://github.com/zonemaster/zonemaster) и [кроссплатформенный модуль](https://github.com/Lifailon/Check-Host) для проверок ping, http, tcp, udp на хостах в сети Интернете через API [Check-Host](https://check-host.net).

- [Тестирование пропускной способности сети Интернет](https://github.com/Lifailon/Ookla-SpeedTest-API) в режиме командной строки через провайдер [Ookla SpeedTest](https://www.speedtest.net) (без использования API и зависимостей) для передачи полученных метрик в InfluxDB и мониторинга в Grafana.

- Veeam Backup & Replication: [состояние инфраструктуры](https://github.com/Lifailon/Veeam-REStat) резервного копирования, [статистика заданий](https://github.com/Lifailon/Veeam-Job-Stat), [мониторинг репозиториев](https://github.com/Lifailon/Veeam-Rep-Stat) и их доступности.

- [Шаблон Zabbix](https://github.com/Lifailon/Windows-User-Sessions) для проверки количества активных и неактивных терминальных пользовательских сессий на машинах с ОС Windows и Windows Server через модуль [Get-Query](https://github.com/Lifailon/Get-Query).

- [Реализация оповещений](https://github.com/Lifailon/ITInvent-SQL-Alert) о истечении срока действия лицензий в [It-Invent](https://it-invent.ru) через СУБД MS SQL.

- [Get Invent SQLite](https://github.com/Lifailon/Get-Invent-SQLite). Модуль сбора данных характеристик физического оборудования на компьютерах в локальной сети через WMI для наполнения базы данных SQLite.

- [Мониторинг Excel-таблиц](https://github.com/Lifailon/Excel-Date-Report) для отправки уведомлений о завершении срока действия лицензий и доступа к ресурсам.

- [RDCMan-LDAP](https://github.com/Lifailon/RDCMan-LDAP). Интеграция списка всех компьютеров с текущего месторасположением в доменной структуре из Active Directory.

- [ConvertTo-Base64](https://github.com/Lifailon/ConvertTo-Base64). Конвертация текста и изображений в формат Base64.

- [Браузер классов WMI](https://github.com/Lifailon/WMI-Class-Viewer).

#