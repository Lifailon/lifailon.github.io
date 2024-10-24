---
hide:
  - title
---

<p align="center">
    <a href="Linux"><img title="Linux Commands"src="https://img.shields.io/badge/Linux_Commands-FCC624?style=for-the-badge&logo=linux&logoColor=black"></a>
    <a href="Docker"><img title="Docker Commands"src="https://img.shields.io/badge/Docker_Commands-2094f3?style=for-the-badge&logo=docker&logoColor=white"></a>
    <a href="https://github.com/Lifailon/rudocs"><img title="Node.js syntax"src="https://img.shields.io/badge/Node.js_Docs-7ab65f?style=for-the-badge&logo=node.js&logoColor=white"></a>
</p>

<p align="center">
    <b>Большая база заметок PowerShell на русском языке.</b>
</p>

Структура языка PowerShell, синтаксис командлетов и модулей, работа с объектами, данными, файловой системой, сетевыми утилитами, базами данных, REST API, классами и методами платформы .NET, системами мониторинга и системного администрирования (Active Directory, MS Exchange, Hyper-V, VMWare). Заметки основаны на принципе примеров и краткого описания.

Исходный репозиторий содержит набор полезных [скриптов и модулей](https://github.com/Lifailon/PS-Commands/tree/rsa/Scripts) автора. Также, на данном сайте будет публиковаться краткая документация в примерах по основам синтаксиса для других языкам программирования, из репозитория 📚 [RuDocs](https://github.com/Lifailon/rudocs).

---

- 💬 **24.10.2024**: Добавлены заметки по синтаксису [JavaScript для Node.js](Node.js).
- 💬 **25.08.2024**: Обновлены заметки по направлению [DevOps](DevOps)
- 💬 **15.07.2024**: Добавлены заметки по работе с системными командами и консольными утилитами 🐧 [Linux](Linux)
- 💬 **20.05.2024**: Добавлены заметки по работе с 🐳 [Docker](Docker)

---

### Другие проекты:

- 🧲 [Kinozal Bot](https://github.com/Lifailon/Kinozal-Bot) - Telegram бот, который позволяет автоматизировать процесс доставки контента до вашего телевизора, используя только телефон. Предоставляет удобный интерфейс для взаимодействия с торрент трекером [Кинозал](https://kinozal.tv) и базой данных [TMDB](https://www.themoviedb.org) для отслеживания даты выхода серий, сезонов и поиска актеров для каждой серии, а также возможность управлять торрент клиентом [qBittorrent](https://github.com/qbittorrent/qBittorrent) или [Transmission](https://github.com/transmission/transmission) на вашем компьютере, находясь удаленно от дома и из единого интерфейса.

- ✨ [TorAPI](https://github.com/Lifailon/TorAPI/blob/main/README_RU.md) - неофициальный `API` (backend) для торрент трекеров RuTracker, Kinozal, RuTor и NoNameClub. Используется для быстрого и централизованного поиска раздач, получения торрент файлов, магнитных ссылок и подробной информации о раздаче по названию фильма, сериала или идентификатору раздачи, а также предоставляет новостную RSS ленту для всех провайдеров с фильтрацией по категориям.

- 🔎 [LibreKinopoisk](https://github.com/Lifailon/LibreKinopoisk/tree/rsa) - расширение для Google Chrome, [Mozilla Firefox](https://addons.mozilla.org/ru/firefox/addon/librekinopoisk) и мобильных устройств, которое добавляет кнопки на сайт [Кинопоиск](http://kinopoisk.ru) и в контекстное меню браузера, а также реализует интерфейс [TorAPI](https://github.com/Lifailon/TorAPI) в стиле [Jackett](https://github.com/Jackett/Jackett) для быстрого поиска фильмов и сериалов в открытых источниках без использования VPN.

- 📡 [Froxy](https://github.com/Lifailon/froxy/blob/main/README_RU.md) - кроссплатформенная утилита командной строки для реализации SOCKS, HTTP и обратного прокси сервера на базе **.NET**. Поддерживается протокол **SOCKS5** для туннелирования TCP трафика и **HTTP** протокол для прямого (классического) проксирования любого **HTTPS** трафика (`CONNECT` запросы), а также **TCP**, **UDP** и **HTTP/HTTPS** протоколы для обратоного проксирования. Для переадресации веб-траффика через обратный прокси поддерживаются `GET` и `POST` запросы с передачей заголовков и тела запроса от клиента, что позволяет использовать `API` запросы и проходить авторизацию на сайтах (передача cookie).

- 🧠 [Intelli Shell](https://github.com/Lifailon/intellishell) 🐚 - это обработчик, работающий поверх оболочки Bash и реализующий механизм автодополнения команд с использованием выпадающего списка в режиме реального времени. Вы можете просматривать историю выполненных команд с поддержкой фильтрации и регулярных выражений, а также использовать навигацию по каталогам, не покидая текущую строку ввода. Кроме того, поддерживается вывод переменных, быстрый поиск с фильтрацией по выводу последней выполненной команды (интерактивный **Grep**), поиск исполняемых команд и доступ к примерам для них через сервис [cheet.sh](https://github.com/chubin/cheat.sh).

---

### Проекты на PowerShell:

- [REST API и Web-сервер](https://github.com/Lifailon/WinAPI) (статья на [Хабр](https://habr.com/ru/articles/783022/)) для удаленного управления ОС Windows через браузер или REST-клиент (например, curl в Linux). Запуск и остановка служб и процессов, получение информации о системе, просмотр и фильтрация журналов событий (логов) в браузере и т.д.

- [Syslog сервер и клиент](https://github.com/Lifailon/pSyslog) на базе .NET и PowerShell.

- [Remote Shadow Administrator](https://github.com/Lifailon/RSA). Инструмент удаленного подключения к текущим RDP-сессиям пользователей на базе RDShadow. Также содержит набор модулей, направленного на автоматизацию удаленного администрирования и управления ОС Windows (пользовательские процессы, службы, обновления, настройки KMS, NTP и т.д.).

- [Графический интерфейс iperf3](https://github.com/Lifailon/iPerf-GUI) и модуль [psiperf](https://github.com/Lifailon/PS-iPerf) для мониторинга пропускной способности канала связи.

- [AD Manager](https://github.com/Lifailon/AD-Manager). Форма автоматизации создания пользователей в Active Directory и почтовых ящиков Exchange, с возможностью формирования базового отчета, управления группами и пользователями.

- [PST Export GUI](https://github.com/Lifailon/PST-Export-GUI). Графический интерфейс получения состояния баз данных и почтовых ящиков на серверах Exchange 2010-2016, просмотр и фильтрация Message Tracking Log, а также создания и управления заданями экспорта почтовых ящиков в формат PST.

- [VMWare Inventory](https://github.com/Lifailon/VMW-Invent). Графический инерфейс реализующий формат отчета состояния виртуальных машин, дисков, ESXi хостов, хранилищ данных и сетевых интерфейсов в реальном времени, а также централизованное управлением питанием (на базе WinForms и модуля PowerCLI).

- [WinEvent Viewer](https://github.com/Lifailon/WinEvent-Viewer). Интерфейс просмотра и фильтрации журналов событий (логов) ОС Windows.

- [DNS Change Tray](https://github.com/Lifailon/DNS-Change-Tray). Программа для быстрой смены DNS-адреса через трей.

- [ACL-Backup](https://github.com/Lifailon/ACL-Backup). Резервное копирование списка прав доступа файловой системы NTFS в txt-файл с возможностью восстановления.

- [Реализация оповещений](https://github.com/Lifailon/ITInvent-SQL-Alert) о истечении срока действия лицензий в [It-Invent](https://it-invent.ru) через СУБД MS SQL.

- [Модуль](https://github.com/Lifailon/PowerShell.HardwareMonitor) для локального и удаленного сбора метрик температуры, нагрузки и других датчиков системы через [LibreHardwareMonitor](https://github.com/LibreHardwareMonitor/LibreHardwareMonitor). Статья на Хабр: [мониторинг температуры Windows](https://habr.com/ru/articles/793296) (создание метрик, настройка InfluxDB и Grafana).

- [Интеграция InfluxDB 1.x](https://github.com/Lifailon/psinfluxdb) и PowerShell для управления базами данных, политиками хранения, пользователями и таблицами, а также вывод измерений в формате объекта.

- [Сценарий развертывания](https://github.com/Lifailon/Deploy-Selenium) всех зависимостей для работы с Selenium через PowerShell и модули для [AI (GPT)](https://github.com/Lifailon/gpt-cli), [SpeedTest](https://github.com/Lifailon/Selenium-Modules/blob/rsa/Modules/Get-SpeedTest/Get-SpeedTest.psm1) и [Translation](https://github.com/Lifailon/Selenium-Modules/blob/rsa/Modules/Get-Translation/Get-Translation.psm1) с использованием разных провайдеров. Статья на habr [PowerShell и Selenium. Проще, чем кажется](https://habr.com/ru/articles/785538).

- [PSEverything](https://github.com/Lifailon/PSEverything). Модуль мнгновенного поиска файлов на локальных и удаленных системах Windows с помощью [Everything](https://www.voidtools.com) через API и [библиотеку .NET](https://github.com/dipique/everythingio). 

- [Интерфейс командной строки](https://github.com/Lifailon/CrystalDisk-Cli) для [CrystalDiskInfo](https://github.com/hiyohiyo/CrystalDiskInfo).

- [Console-Translate](https://github.com/Lifailon/Console-Translate). Интерфейс командной строки перевода текста (использует 3 провайдера и режим автоматического определения исходного языка).

- [Console-Download](https://github.com/Lifailon/Console-Download). Инструмент командной строки для загрузки файлов из переданного списка URL-адресов в многопоточном режиме и отображения скорости загрузки в реальном времени. Реализует тестирования пропускной способности сетевого интерфейса через хосты [Looking Glass](https://github.com/gnif/LookingGlass) (интеграция с [Looking.House](https://looking.house)) с целью отладки датчиков системы мониторинга и проверки скорости сети Интернет.

- [PSDomainTest](https://github.com/Lifailon/PSDomainTest?tab=readme-ov-file) для тестирования DNS-записей домена с использованием API [ZoneMaster](https://github.com/zonemaster/zonemaster) и [кроссплатформенный модуль](https://github.com/Lifailon/Check-Host) для проверок ping, http, tcp, udp на хостах в сети Интернете через API [Check-Host](https://check-host.net).

- [Тестирование пропускной способности сети Интернет](https://github.com/Lifailon/Ookla-SpeedTest-API) в режиме командной строки через провайдер [Ookla SpeedTest](https://www.speedtest.net) (без использования API и зависимостей) для передачи полученных метрик в InfluxDB и мониторинга в Grafana.

- [Модуль Veeam Backup & Replication](https://github.com/Lifailon/Veeam-REStat) для получения состояние инфраструктуры резервного копирования, [статистика заданий](https://github.com/Lifailon/Veeam-Job-Stat), [мониторинг репозиториев](https://github.com/Lifailon/Veeam-Rep-Stat) и их доступности.

- [Шаблон Zabbix](https://github.com/Lifailon/Windows-User-Sessions) для проверки количества активных и неактивных терминальных пользовательских сессий на машинах с ОС Windows и Windows Server через модуль [Get-Query](https://github.com/Lifailon/Get-Query).

- [Get Invent SQLite](https://github.com/Lifailon/Get-Invent-SQLite). Модуль сбора данных характеристик физического оборудования на компьютерах в локальной сети через WMI для наполнения базы данных SQLite.

- [Мониторинг Excel-таблиц](https://github.com/Lifailon/Excel-Date-Report) для отправки уведомлений о завершении срока действия лицензий и доступа к ресурсам.

- [RDCMan-LDAP](https://github.com/Lifailon/RDCMan-LDAP). Интеграция списка всех компьютеров с текущего месторасположением в доменной структуре из Active Directory.

- [Темы производительности системы](https://github.com/Lifailon/oh-my-posh-themes-performance) для [oh-my-posh](https://github.com/jandedobbeleer/oh-my-posh).

- [Простой браузер классов WMI](https://github.com/Lifailon/WMI-Class-Viewer).

#