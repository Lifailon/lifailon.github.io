+++
title = "Compose Stacks"
[extra]
toc = true
toc_sidebar = true
go_to_top = true
+++

<p align="center">
    <a href="https://github.com/Lifailon/PS-Commands/tree/rsa/Docker-Compose"><img title="Compose Stacks"src="logo.png"></a>
</p>

<p align="center">
    Коллекция стеков Docker Compose из более чем 200 сервисов. Каждое приложение было отлажено и проверено в домашней лаборатории, конфигурации к некоторым сервисам доступны в исходном <a href="https://github.com/Lifailon/PS-Commands/tree/rsa/Docker-Compose">репозитории</a>.
</p>

---

## Bot Stack

### SSH Bot

[SSH Bot](https://github.com/Lifailon/ssh-bot) - Telegram бот, который позволяет запускать заданные команды на выбранном хосте в домашней сети и возвращать результат их выполнения. Бот не устанавливает постоянное соединение с удаленным хостом, что позволяет выполнять команды асинхронно.

```yaml
services:
  OpenRouter-Bot:
    container_name: OpenRouter-Bot
    image: lifailon/openrouter-bot:latest
    volumes:
      - ./openrouter-bot.env:/openrouter-bot/.env
    restart: unless-stopped
```

env:

```env
# Telegram api key from https://telegram.me/BotFather
TELEGRAM_BOT_TOKEN=XXXXXXXXXX:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
# Your Telegram id from https://t.me/getmyid_bot
TELEGRAM_USER_ID=7777777777

# Interpreter used only when running the bot local in Windows
# Available values: powershell/pwsh
WIN_SHELL=pwsh
# Interpreter used on local and remote hosts in Linux
# Available values: sh/bash/zsh or other
LINUX_SHELL=bash

# Parallel (async) execution of commands (default: false)
PARALLEL_EXEC=true

# Global parameters for ssh connection (low priority)
SSH_PORT=2121
SSH_USER=lifailon
# Use password to connect (optional)
SSH_PASSWORD=
# Full path to private key (default: ~/.ssh/id_rsa)
SSH_PRIVATE_KEY_PATH=
SSH_CONNECT_TIMEOUT=2
# Save and reuse passed variables and functions (default: false)
SSH_SAVE_ENV=true
# List of hosts separated by comma (high priority for username and port)
SSH_HOST_LIST=192.168.3.101,root@192.168.3.102:22,root@192.168.3.103:22,192.168.3.105,192.168.3.106

# Log the output of command execution
LOG_MODE=DEBUG
```

### OpenRouter Bot

[OpenRouter Bot](https://github.com/Lifailon/openrouter-bot) - Telegram бота для общения с бесплатными и платными моделями ИИ через [OpenRouter](https://openrouter.ai), или локальными LLM, например, через [LM Studio](https://lmstudio.ai).

```yaml
services:
  ssh-bot:
    container_name: ssh-bot
    image: lifailon/ssh-bot:latest
    volumes:
      - ./ssh-bot.env:/ssh-bot/.env
      - $HOME/.ssh/id_rsa:/root/.ssh/id_rsa
    restart: unless-stopped
```

env:

```env
# OpenRouter api key from https://openrouter.ai/settings/keys
API_KEY=

# Telegram api key from https://telegram.me/BotFather
TELEGRAM_BOT_TOKEN=

# Your Telegram id from https://t.me/getmyid_bot
ADMIN_IDS=
# List of users to access the bot, separated by commas
ALLOWED_USER_IDS=

BASE_URL=https://openrouter.ai/api/v1
# List of free models: https://openrouter.ai/models?max_price=0
MODEL=deepseek/deepseek-r1:free
# System preset that specifies the role for AI
#ASSISTANT_PROMPT="Ты переводчик, умеешь только переводить текст с русского на англйский язык (и наоборот) и не отвечаешь на вопросы."

# Using local LLM via LM Studio (https://lmstudio.ai)
#BASE_URL=http://localhost:1234/v1
# Using local model: https://huggingface.co/ruslandev/llama-3-8b-gpt-4o-ru1.0
#MODEL=llama-3-8b-gpt-4o-ru1.0

VISION=false
#VISION_PROMPT="Описание изображения"
#VISION_DETAIL="низкий"

# The maximum number of messages or time in minutes for store messages in history
MAX_HISTORY_SIZE=20
MAX_HISTORY_TIME=60

#BUDGET_PERIOD=monthly
# Enable user access (default)
#USER_BUDGET=1
# Disable guest access (enabled by default)
GUEST_BUDGET=0

# Language used for bot responses (supported: EN/RU)
LANG=RU

# ADMIN/USER/GUEST
STATS_MIN_ROLE=ADMIN
```

### Kinozal Bot

[Kinozal Bot](https://github.com/Lifailon/Kinozal-Bot) - Telegram бот, который позволяет автоматизировать процесс доставки контента до вашего телевизора, используя только телефон. С помощью бота вы получите удобный и привычный интерфейс для взаимодействия с торрент трекером [Кинозал](https://kinozal.tv) и базой данных [TMDB](https://www.themoviedb.org) для отслеживания даты выхода серий, сезонов и поиска актеров для каждой серии, а также возможность управлять торрент клиентом [qBittorrent](https://github.com/qbittorrent/qBittorrent) или [Transmission](https://github.com/transmission/transmission) на вашем компьютере, находясь удаленно от дома, а главное, все это доступно из единого интерфейса и без установки клиентского приложения на конечные устройства. В отличии от других приложений, предназначенных для удаленного управления торрент клиентами, вам не нужно находиться в той же локальной сети или использовать VPN.

```yaml
services:
  kinozal-bot:
    image: lifailon/kinozal-bot:latest
    container_name: kinozal-bot
    restart: unless-stopped
    volumes:
      - ./torrents:/kinozal-bot/torrents
      - ./kinozal-bot.conf:/kinozal-bot/kinozal-bot.conf
```
