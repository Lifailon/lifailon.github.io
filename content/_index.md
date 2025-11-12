+++
insert_anchor_links = "left"
title = "Home"
[extra]
go_to_top = true
+++

{% crt() %}
```
     ____                        ____  _          _ _     ____                                          _     
    |  _ \ _____      _____ _ __/ ___|| |__   ___| | |   / ___|___  _ __ ___  _ __ ___   __ _ _ __   __| |___  
    | |_) / _ \ \ /\ / / _ \ '__\___ \| '_ \ / _ \ | |  | |   / _ \| '_ ` _ \| '_ ` _ \ / _` | '_ \ / _` / __|
    |  __/ (_) \ V  V /  __/ |   ___) | | | |  __/ | |  | |__| (_) | | | | | | | | | | | (_| | | | | (_| \__ \
    |_|   \___/ \_/\_/ \___|_|  |____/|_| |_|\___|_|_|   \____\___/|_| |_| |_|_| |_| |_|\__,_|_| |_|\__,_|___/

```
{% end %}

<p align="center" style="display: flex; justify-content: center; gap: 10px;">
    <a href="/linux/"><img title="Linux Commands" src="linux.svg"></a>
    <a href="/devops/"><img title="DevOps Tools" src="devops.svg"></a>
    <a href="/compose/"><img title="Compose Stacks" src="compose.svg"></a>
    <a href="/golang/"><img title="GoLang Cheat Sheet"src="golang.svg"></a>
    <a href="/node-js/"><img title="Node.js Cheat Sheet"src="node.js.svg"></a>
</p>

<p align="center">
    Заметки по синтаксису <a href="/powershell/">PowerShell</a>, <a href="/golang/">Go</a>, <a href="/node-js/">JavaScript</a>, командам <a href="/linux/">Linux</a> и инструментам <a href="/devops/">DevOps</a>,
    <br>
    а также коллекция стеков <a href="/compose/">Docker Compose</a> из более чем 200 сервисов.
</p>

[PowerShell](https://github.com/PowerShell/PowerShell) - это объектно-ориентированный и кроссплатформенный язык программирования с открытым исходным кодом, который способен напрямую работать с классами и методами `C#` через платформу `.NET`, что позволяет создавать программы с графическим интерфейсом на базе [WinForms](https://github.com/Lifailon/PS-Commands/blob/rsa/WinForms) и [WPF](https://ru.wikipedia.org/wiki/Windows_Presentation_Foundation), веб-сервера и `REST API` с помощью класса [HttpListener](https://github.com/Lifailon/PS-Commands/blob/rsa/HttpListener/KeePassREST.psm1), управлять браузером с помощь библиотеки [Selenium](https://github.com/Lifailon/Selenium-Modules), управление различными СУБД и языками разметки (нативная поддержка `XML`, `JSON` и `CSV`, [YAML](https://github.com/Phil-Factor/PSYaml) и [TOML](https://github.com/jborean93/PSToml) с помощью модулей или `HTML` через библиотеку [HAP](https://github.com/zzzprojects/html-agility-pack)) как с любыми другими объектами за счет типовой экосистемы взаимодействия. Такой язык в первую очередь будет полезен DevOps инженерам и системным администраторам, так как является незаменимым инструментом для автоматизации Windows систем и управления компонентами Windows Server (`Active Directory`, `MS Exchange`, `MS SQL`, `Hyper-V`, `DNS`, `DHCP`, `SMB` и т.п.).

---

### Другие проекты

- [lazyjournal](https://github.com/Lifailon/lazyjournal) - терминальный пользовательский интерфейс для `journalctl` (инструмент для чтения логов из системы [systemd-journald](https://github.com/systemd/systemd/tree/main/src/journal)), `auditd`, логов файловой системы (включая архивные журналы), контейнеров `Docker`, `Podman` и `Kubernetes` pods для быстрого просмотра и фильтрации в режиме реального времени с поддержкой нечеткого поиска, регулярных выражений (в стиле `fzf` и `grep`), временной метке и покраской вывода, написанный на языке `Go` с использованием библиотеки [gocui](https://github.com/awesome-gocui/gocui).

- [multranslate](https://github.com/Lifailon/multranslate) - терминальный пользовательский интерфейс на базе библиотеки [Blessed](https://github.com/chjj/blessed) для одновременного перевода текста с использованием нескольких популярных источников перевода и `LLM`. Все источники не требуют токена доступа к API (за исключением официального `OpenAI` и `OpenRouter`). Поддерживает автоматическое определение исходного и целевого языка на уровне кода между английским и любым из поддерживаемых языков, а также доступ к истории переводов через [SQLite](https://github.com/WiseLibs/better-sqlite3).

- [TorAPI](https://github.com/Lifailon/TorAPI/blob/main/README_RU.md) - неофициальный `API` (backend) для торрент трекеров RuTracker, Kinozal, RuTor и NoNameClub. Используется для быстрого и централизованного поиска раздач, получения торрент файлов, магнитных ссылок и подробной информации о раздаче по названию фильма, сериала или идентификатору раздачи, а также предоставляет новостную RSS ленту для всех провайдеров с фильтрацией по категориям.

- [LibreKinopoisk](https://github.com/Lifailon/LibreKinopoisk/tree/rsa) - расширение для Google Chrome, [Mozilla Firefox](https://addons.mozilla.org/ru/firefox/addon/librekinopoisk) и мобильных устройств, которое добавляет кнопки на сайт [Кинопоиск](http://kinopoisk.ru) и в контекстное меню браузера, а также реализует интерфейс [TorAPI](https://github.com/Lifailon/TorAPI) (frontend) в стиле [Jackett](https://github.com/Jackett/Jackett) для быстрого поиска фильмов и сериалов в открытых источниках без использования VPN.

- [Kinozal Bot](https://github.com/Lifailon/Kinozal-Bot) - бот Telegram, который позволяет автоматизировать процесс доставки контента до вашего телевизора, используя только телефон. Предоставляет удобный интерфейс для взаимодействия с торрент трекером [Кинозал](https://kinozal.tv) и базой данных [TMDB](https://www.themoviedb.org) для отслеживания даты выхода серий, сезонов и поиска актеров для каждой серии, а также возможность управлять торрент клиентом [qBittorrent](https://github.com/qbittorrent/qBittorrent) или [Transmission](https://github.com/transmission/transmission) на вашем компьютере, находясь удаленно от дома и из единого интерфейса.

- [OpenRouter Bot](https://github.com/Lifailon/openrouter-bot) - этот проект позволяет за несколько минут запустить своего Telegram бота для общения с бесплатными или платными моделями AI через [OpenRouter](https://openrouter.ai) или локальными LLM, например, через [LM Studio](https://lmstudio.ai).

- [SSH Bot](https://github.com/Lifailon/ssh-bot) - менеджер SSH подключений в Telegram. Бот позволяет выполнять команды на выбранном хосте в вашей домашней сети и возвращать результат их выполнения. Бот не устанавливает постоянного соединения с удалённым хостом, что позволяет выполнять команды асинхронно. Такое решение предоставляет возможность не тратить время на настройку `VPN` и деньги на внешний IP адрес или VPS сервер для доступа в локальную сеть, а также исключает необходимость использования сторонних приложений (`VPN` и `ssh` клиентов) на удаленном устройстве и не требует стабильного подключения к Интернету.

- [logporter](https://github.com/Lifailon/logporter) - Простая и легкая альтернатива [cAdvisor](https://github.com/google/cadvisor) для получения всех базовых метрик из контейнеров Docker с поддержкой пользовательских метрик по логам. По сравнению с cAdvisor, среднии показатели потребления CPU в 15–20 раз, а потребление памяти в 10 раз ниже.

- [froxy](https://github.com/Lifailon/froxy/blob/main/README_RU.md) - классический и обратный прокси сервер на базе `.NET` для запуска в режиме командной строки и контейнера [Docker](https://hub.docker.com/r/lifailon/froxy). Поддерживает проксирование `HTTPS` трафика (`CONNECT` запросы) и протокол `SOCKS5` для туннелирования `TCP` трафика, а также `TCP`, `UDP` и `HTTP/HTTPS` протоколы для обратоного проксирования (поддерживается обработка `GET` и `POST` запросов с передачей заголовков и тела запроса для работы с `API` и передачи `cookie`).

- [IntelliShell](https://github.com/Lifailon/intellishell) - это обработчик, работающий поверх оболочки `Bash` и реализующий механизм автодополнения команд с использованием выпадающего списка в режиме реального времени. Вы можете просматривать историю выполненных команд с поддержкой фильтрации и регулярных выражений, а также использовать навигацию по каталогам, не покидая текущую строку ввода. Кроме того, поддерживается вывод переменных, быстрый поиск с фильтрацией по выводу последней выполненной команды (интерактивный Grep), поиск исполняемых команд и доступ к примерам для них через сервис [cheet.sh](https://github.com/chubin/cheat.sh).

- [RSA](https://github.com/Lifailon/RSA) (Remote Shadow Administrator) - интерфейс для удаленного подключения к текущим `RDP`/`RDS` сессиям пользователей на базе `RDShadow` и `WinForms`. Также содержит набор модулей для автоматизации удаленного администрирования и управления ОС Windows (пользовательские процессы, службы, обновления, настройки `KMS`, `NTP` и т.д.).

- [WinAPI](https://github.com/Lifailon/WinAPI) - `REST API` и `Web` сервер для удаленного управления ОС Windows через браузер или `REST` запросы (например, `curl` в Linux). Запуск и остановка служб и процессов, получение информации о системе, просмотр и фильтрация журналов событий (логов) в браузере и т.д. Статья на Хабр: [REST API/Web сервер на PowerShell](https://habr.com/ru/articles/783022).

- [iPerf-GUI](https://github.com/Lifailon/iPerf-GUI) - графический интерфейс для `iperf3`, а также модуль [ps-iperf](https://github.com/Lifailon/PS-iPerf) для мониторинга пропускной способности канала связи.

- [AD Manager](https://github.com/Lifailon/AD-Manager) - форма автоматизации создания пользователей в `Active Directory` и почтовых ящиков `Exchange`, с возможностью формирования базового отчета, управления группами и пользователями.

- [PST Export GUI](https://github.com/Lifailon/PST-Export-GUI) - графический интерфейс для получения состояния баз данных и почтовых ящиков на серверах `Microsoft Exchange 2010-2016`, просмотр и фильтрация `Message Tracking Log`, а также создания и управления заданями экспорта почтовых ящиков в формат `PST` через модуль `EMShell`.

- [VMWare Inventory](https://github.com/Lifailon/VMW-Invent) - графический инерфейс для быстрого получения состояния виртуальных машин в формате отчета (альтернатива выгрузки в формате Excel), дисков, ESXi хостов, хранилищ данных и сетевых интерфейсов в реальном времени, а также централизованное управлением питанием через модуль `PowerCLI`.

---