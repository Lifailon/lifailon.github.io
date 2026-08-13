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
    Обширная база заметок по синтаксису <a href="/powershell/">PowerShell</a>, <a href="/golang/">Go</a> и <a href="/node-js/">Node.js</a>, управлению <a href="/linux/">Linux</a> и инструментам <a href="/devops/">DevOps</a>,
    <br>
    а также содержит коллекцию стеков <a href="/compose/">Docker Compose</a> из более чем 300 сервисов.
</p>

---

<p align="center">
    Пожалуйста, ознакомьтесь с другими проектами на GitHub ⭐
</p>

<table width="100%">
  <tr>
    <td valign="middle" style="min-width: 400px;">
      <img src="https://github.com/Lifailon/lazyjournal/blob/main/img/logo.png?raw=true">
    </td>
    <td width="100%" valign="middle">
      <h3><a href="https://github.com/Lifailon/lazyjournal">Lazyjournal</a></h3>
      <p>Терминальный пользовательский интерфейс (TUI) для просмотра логов из journald, auditd, файловой системы, контейнеров Docker и Podman, стеков Compose и подов Kubernetes с поддержкой подсветки вывода и нескольких режимов фильтрации.</p>
      <p>Написан на Go с использованием библиотеки <a href="https://github.com/jroimartin/gocui">gocui</a>.</p>
      <p>Статьи на Хабр 👉 <a href="https://habr.com/ru/articles/872536">Простой и универсальный способ чтения логов в терминале</a> и <a href="https://habr.com/ru/articles/899750">ленивый интерфейс для поиска и анализа логов</a>.</p>
    </td>
  </tr>
</table>

<table width="100%">
  <tr>
    <td valign="middle" style="min-width: 400px;">
      <img src="https://github.com/Lifailon/logporter/blob/main/img/logo.png?raw=true">
    </td>
    <td width="100%" valign="middle">
      <h3><a href="https://github.com/Lifailon/logporter">Logporter</a></h3>
      <p>Легковесная альтернатива 🦉 <a href="https://github.com/google/cadvisor">cAdvisor</a> для экспорта метрик из контейнеров Docker и сборщик логов для отправки в Loki, с поддержкой фильтрации по меткам compose.</p>
    </td>
  </tr>
</table>

<table width="100%">
  <tr>
    <td valign="middle" style="min-width: 400px;">
      <img src="https://github.com/Lifailon/TorAPI/blob/main/image/logo-02.png?raw=true">
    </td>
    <td width="100%" valign="middle">
      <h3><a href="https://github.com/Lifailon/TorAPI">TorAPI</a></h3>
      <p>Неофициальный API (backend) на базе <a href="https://github.com/expressjs/express">Express</a> для торрент трекеров RuTracker, Kinozal, RuTor и NoNameClub.</p>
      <p>Используется для быстрого и централизованного поиска раздач, получения торрент файлов, магнитных ссылок и подробной информации о раздаче по названию фильма, сериала или идентификатору раздачи, а также предоставляет новостную RSS ленту для всех провайдеров с фильтрацией по категориям.</p>
    </td>
  </tr>
</table>

<table width="100%">
  <tr>
    <td valign="middle" style="min-width: 210px;">
      <img src="https://github.com/Lifailon/LibreKinopoisk/blob/rsa/ChromeExtension/icons/icon256.png?raw=true" style="background-color: transparent; box-shadow: none">
    </td>
    <td colspan="2" valign="middle">
      <h3><a href="https://github.com/Lifailon/LibreKinopoisk">LibreKinopoisk</a></h3>
      <p>Расширение для Google Chrome, <a href="https://addons.mozilla.org/ru/firefox/addon/librekinopoisk">Mozilla Firefox</a> и мобильных устройств, которое добавляет кнопки на сайт Кинопоиск и в контекстное меню браузера, а также реализует интерфейс <b>TorAPI</b> (frontend) в стиле <a href="https://github.com/Jackett/Jackett">Jackett</a> для быстрого поиска фильмов и сериалов в открытых источниках без использования VPN.</p>
    </td>
  </tr>
</table>

<table width="100%">
  <tr>
    <td valign="middle" style="min-width: 240px;">
      <img src="https://github.com/Lifailon/doorr/blob/main/assets/logo.png?raw=true">
    </td>
    <td width="100%" valign="middle">
      <h3><a href="https://github.com/Lifailon/doorr">Doorr</a></h3>
      <p>Интерфейс Android на базе <a href="https://github.com/flutter/flutter">Flutter</a> для поиска торрентов через <a href="https://github.com/prowlarr/prowlarr">Prowlarr</a> и <a href="https://github.com/Jackett/Jackett">Jackett</a>.</p>
      <p>Поддерживает фильтрацию раздач и предварительный просмотр содержимого торрент-файлов до их загрузки.</p>
    </td>
  </tr>
</table>

<table width="100%">
  <tr>
    <td valign="middle" style="min-width: 110px;">
      <img src="https://github.com/Lifailon/Kinozal-Bot/blob/rsa/image/logo/kinozal-bot-512px.png?raw=true">
    </td>
    <td colspan="2" valign="middle">
      <h3><a href="https://github.com/Lifailon/Kinozal-Bot">Kinozal Bot</a></h3>
      <p><a href="https://habr.com/ru/articles/826774">Telegram бот для управления торрент клиентом и трекером</a>, который позволяет автоматизировать процесс доставки контента до вашего телевизора, используя только телефон.</p>
      <p>Предоставляет интерфейс для торрент трекера <a href="https://kinozal.tv">Кинозал</a> и интеграций с базой данных <a href="https://www.themoviedb.org">TMDB</a> для отслеживания даты выхода серий, сезонов и поиска актеров для каждой серии, а также управление торрент клиентами <a href="https://github.com/qbittorrent/qBittorrent">qBittorrent</a> и <a href="https://github.com/transmission/transmission">Transmission</a> на вашем компьютере, находясь удаленно от дома и из единого интерфейса.</p>
    </td>
  </tr>
</table>

<table width="100%">
  <tr>
    <td valign="middle" style="min-width: 240px;">
      <img src="https://github.com/Lifailon/ssh-bot/blob/main/img/logo.png?raw=true">
    </td>
    <td width="100%" valign="middle">
      <h3><a href="https://github.com/Lifailon/ssh-bot">SSH Bot</a></h3>
      <p>Менеджер SSH подключений в Telegram, который позволяет выполнять команды на выбранном хосте в вашей домашней сети и возвращать результат их выполнения.</p>
      <p>Бот не устанавливает постоянного соединения с удалённым хостом, что позволяет выполнять команды асинхронно. Такое решение предоставляет возможность не тратить время на настройку VPN и деньги на внешний IP адрес или VPS сервер для доступа в локальную сеть, а также исключает необходимость использования сторонних приложений (VPN и ssh клиентов) на удаленном устройстве и не требует стабильного подключения к Интернету.</p>
    </td>
  </tr>
</table>

<table width="100%">
  <tr>
    <td valign="middle" style="min-width: 250px;">
      <img src="https://github.com/Lifailon/openrouter-bot/blob/main/img/logo.png?raw=true" style="background-color: transparent; box-shadow: none" width="200">
    </td>
    <td colspan="2" valign="middle">
      <h3><a href="https://github.com/Lifailon/openrouter-bot">OpenRouter Bot</a></h3>
      <p>Проект позволяет за несколько минут запустить своего Telegram бота для общения с бесплатными или платными моделями AI через <a href="https://openrouter.ai">OpenRouter</a> или локальными LLM, например, через <a href="https://lmstudio.ai">LM Studio</a>.</p>
    </td>
  </tr>
</table>

<table width="100%">
  <tr>
    <td valign="middle" style="min-width: 200px;">
      <img src="https://github.com/Lifailon/multranslate/blob/rsa/image/logo.png?raw=true" style="background-color: transparent; box-shadow: none">
    </td>
    <td colspan="2" valign="middle">
      <h3><a href="https://github.com/Lifailon/multranslate">Multranslate</a></h3>
      <p>Терминальный пользовательский интерфейс (TUI) на базе библиотеки <a href="https://github.com/chjj/blessed">Blessed</a> для одновременного перевода текста с использованием нескольких популярных источников перевода без необходимости настройки доступа к API.</p>
      <p>Поддерживается использование ИИ (включая локальные LLM и бесплатные модели на OpenRouter), определение исходного и целевого языка на уровне кода, а также сохранение истории переводов в локальной базе данных <a href="https://github.com/WiseLibs/better-sqlite3">SQLite</a>.</p>
      <p>Статья на Хабр 👉 <a href="https://habr.com/ru/articles/842288">Переводчик текста для терминала</a>.</p>
    </td>
  </tr>
</table>

<table width="100%">
  <tr>
    <td valign="middle" style="min-width: 400px;">
      <img src="https://github.com/Lifailon/RSA/blob/rsa/Image/Screen/Services.jpg?raw=true">
    </td>
    <td width="100%" valign="middle">
      <h3><a href="https://github.com/Lifailon/RSA">RSA (Remote Shadow Administrator)</a></h3>
      <p>Интерфейс для удаленного подключения к текущим RDP сессиям пользователей на базе RDShadow и WinForms.</p>
      <p>Включаем в себя набор модулей для автоматизации удаленного администрирования и управления ОС Windows (пользовательскими процессами, службами, обновлениями, настройками KMS, NTP и другие функции).</p>
    </td>
  </tr>
</table>

<table width="100%">
  <tr>
    <td colspan="2" valign="middle">
      <h3><a href="https://github.com/Lifailon/froxy/blob/main/README_RU.md">Froxy</a></h3>
      <p>Прямой и обратный прокси сервер на базе .NET для запуска в контейнере <a href="https://hub.docker.com/r/lifailon/froxy">Docker</a> или использования в качестве инструмента командной строки.</p>
      <p>Поддерживает проксирование HTTPS трафика (CONNECT запросы) и протокол SOCKS5 для туннелирования TCP трафика, а также TCP, UDP и HTTP/HTTPS протоколы для обратоного проксирования (поддерживается обработка GET и POST запросов с передачей заголовков и тела запроса для работы с API и передачи Cookie).</p>
    </td>
  </tr>
</table>
