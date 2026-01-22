+++
insert_anchor_links = "left"
title = "Home"
[extra]
go_to_top = true
+++

<!--
{% crt() %}
```

                    ▄▄▄▄▄▄                     
                    ███▀▀██▄                   
        ████▄ ██ ██ ███  ███ ▄███▄ ▄████ ▄█▀▀▀ 
        ██ ▀▀ ██ ██ ███  ███ ██ ██ ██    ▀███▄ 
        ██    ▀██▀█ ██████▀  ▀███▀ ▀████ ▄▄▄█▀ 
                                                                                              
```
{% end %}
-->

<p align="center">
    <a href="https://github.com/Lifailon/rudocs"><img title="ruDocs"src="logo.png"></a>
</p>

<p align="center" style="display: flex; justify-content: center; gap: 10px;">
    <a href="/linux/"><img title="Linux Commands" src="linux.svg"></a>
    <a href="/devops/"><img title="DevOps Tools" src="devops.svg"></a>
    <a href="/compose/"><img title="Compose Stacks" src="compose.svg"></a>
    <a href="/golang/"><img title="GoLang Cheat Sheet"src="golang.svg"></a>
    <a href="/node-js/"><img title="Node.js Cheat Sheet"src="node.js.svg"></a>
</p>

---

<p align="center">
    Веб-версия заметок по <a href="/powershell/">PowerShell</a>, командам <a href="/linux/">Linux</a>, инструментам <a href="/devops/">DevOps</a> и сервисам <a href="/compose/">Docker Compose</a> из репозитория <a href="https://github.com/Lifailon/rudocs">ruDocs</a>, а также синтаксису <a href="/golang/">Go</a> и <a href="/node-js/">Node.js</a> на русском языке, собранные на пути от системного администратора Windows систем до инженера DevOps.
</p>

---

Пожалуйста, ознакомьтесь с другими проектами на ⭐ GitHub:

- [lazyjournal](https://github.com/Lifailon/lazyjournal) - терминальный пользовательский интерфейс (TUI) для просмотра логов из journald, auditd, файловой системы, контейнеров Docker и Podman, стеков Compose и подов Kubernetes с поддержкой подсветки вывода и нескольких режимов фильтрации. Написан на Go с использованием библиотеки [gocui](https://github.com/jroimartin/gocui).

- [multranslate](https://github.com/Lifailon/multranslate) - терминальный пользовательский интерфейс на базе библиотеки [Blessed](https://github.com/chjj/blessed) для одновременного перевода текста с использованием нескольких популярных источников перевода (настройка доступа к API не требуется). Поддерживается использование ИИ (включая локальные LLM и бесплатные модели на [OpenRouter](https://openrouter.ai)), определение исходного и целевого языка на уровне кода, а также сохранение истории переводов в [SQLite](https://github.com/WiseLibs/better-sqlite3).

- [TorAPI](https://github.com/Lifailon/TorAPI/blob/main/README_RU.md) - неофициальный API (backend) на базе [Express](https://github.com/expressjs/express) для торрент трекеров RuTracker, Kinozal, RuTor и NoNameClub. Используется для быстрого и централизованного поиска раздач, получения торрент файлов, магнитных ссылок и подробной информации о раздаче по названию фильма, сериала или идентификатору раздачи, а также предоставляет новостную RSS ленту для всех провайдеров с фильтрацией по категориям.

- [LibreKinopoisk](https://github.com/Lifailon/LibreKinopoisk/tree/rsa) - расширение для Google Chrome, [Mozilla Firefox](https://addons.mozilla.org/ru/firefox/addon/librekinopoisk) и мобильных устройств, которое добавляет кнопки на сайт [Кинопоиск](http://kinopoisk.ru) и в контекстное меню браузера, а также реализует интерфейс (frontend) [TorAPI](https://github.com/Lifailon/TorAPI) в стиле [Jackett](https://github.com/Jackett/Jackett) для быстрого поиска фильмов и сериалов в открытых источниках без использования VPN.

- [Kinozal-Bot](https://github.com/Lifailon/Kinozal-Bot) - это Telegram бот, который позволяет автоматизировать процесс доставки контента до вашего телевизора, используя только телефон. Предоставляет удобный интерфейс для взаимодействия с торрент трекером [Кинозал](https://kinozal.tv) и базой данных [TMDB](https://www.themoviedb.org) для отслеживания даты выхода серий, сезонов и поиска актеров для каждой серии, а также возможность управлять торрент клиентом [qBittorrent](https://github.com/qbittorrent/qBittorrent) или [Transmission](https://github.com/transmission/transmission) на вашем компьютере, находясь удаленно от дома и из единого интерфейса.

- [ssh-bot](https://github.com/Lifailon/ssh-bot) - менеджер SSH подключений в Telegram, который позволяет выполнять команды на выбранном хосте в вашей домашней сети и возвращать результат их выполнения. Бот не устанавливает постоянного соединения с удалённым хостом, что позволяет выполнять команды асинхронно. Такое решение предоставляет возможность не тратить время на настройку VPN и деньги на внешний IP адрес или VPS сервер для доступа в локальную сеть, а также исключает необходимость использования сторонних приложений (VPN и ssh клиентов) на удаленном устройстве и не требует стабильного подключения к Интернету.

- [OpenRouter-Bot](https://github.com/Lifailon/openrouter-bot) - этот проект позволяет за несколько минут запустить своего Telegram бота для общения с бесплатными или платными моделями AI через [OpenRouter](https://openrouter.ai) или локальными LLM, например, через [LM Studio](https://lmstudio.ai).

- [logporter](https://github.com/Lifailon/logporter) - простая и легкая альтернатива [cAdvisor](https://github.com/google/cadvisor) для получения всех базовых метрик из контейнеров Docker с поддержкой пользовательских метрик по логам. По сравнению с cAdvisor, среднии показатели потребления CPU в 15–20 раз, а потребление памяти в 10 раз ниже.

- [Froxy](https://github.com/Lifailon/froxy/blob/main/README_RU.md) - прямой и обратный прокси сервер на базе .NET для запуска в режиме командной строки или контейнере [Docker](https://hub.docker.com/r/lifailon/froxy). Поддерживает проксирование HTTPS трафика (CONNECT запросы) и протокол SOCKS5 для туннелирования TCP трафика, а также TCP, UDP и HTTP/HTTPS протоколы для обратоного проксирования (поддерживается обработка GET и POST запросов с передачей заголовков и тела запроса для работы с API и передачи Cookie).

- [Intelli Shell](https://github.com/Lifailon/intellishell) - это обработчик, работающий поверх оболочки Bash и реализующий механизм автодополнения команд с использованием выпадающего списка в режиме реального времени. Вы можете просматривать историю выполненных команд с поддержкой фильтрации и регулярных выражений, а также использовать навигацию по каталогам, не покидая текущую строку ввода.

- [RSA](https://github.com/Lifailon/RSA) (Remote Shadow Administrator) - интерфейс для удаленного подключения к текущим RDP сессиям пользователей на базе RDShadow и WinForms. Также содержит набор модулей для автоматизации удаленного администрирования и управления ОС Windows (пользовательские процессы, службы, обновления, настройки KMS, NTP и другие функции).

- [WinAPI](https://github.com/Lifailon/WinAPI) - веб-сервер для удаленного управления ОС Windows через браузер или REST-запросы (например, curl в Linux). Запуск и остановка служб и процессов, получение информации о системе, просмотр и фильтрация журналов событий (логов) в браузере и другие функции. Статья на Хабр: [REST API/Web сервер на PowerShell](https://habr.com/ru/articles/783022).

- [iPerf-GUI](https://github.com/Lifailon/iPerf-GUI) - графический интерфейс для управления клиентом и сервером [iperf3](https://github.com/esnet/iperf).

- [PST-Export-GUI](https://github.com/Lifailon/PST-Export-GUI) - графический интерфейс для сбора статистики состояния почтовых ящиков, баз данных, DAG и просмотра Message Tracking Log, а также создания и управления заданями экспорта почтовых ящиков в формате PST на серверах MS Exchange.

- [AD-Manager](https://github.com/Lifailon/AD-Manager) - графический интерфейс для автоматизации создания УЗ, а также управления группами, пользователями и хостами в Active Directory.
