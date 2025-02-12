+++
title = "DevOps"
[extra]
toc = true
toc_sidebar = true
+++

Заметки по направлению `DevOps`.

---

# Git

`git --version` \
`git config --global user.name "Lifailon"` добавить имя для коммитов \
`git config --global user.email "lifailon@yandex.ru"` \
`git config --global --edit` \
`git config --global core.editor "code --wait"` изменить редактор коммитов по умолчанию \
`ssh-keygen -t rsa -b 4096` \
`Get-Service | where name -match "ssh-agent" | Set-Service -StartupType Automatic` \
`Get-Service | where name -match "ssh-agent" | Start-Service` \
`Get-Service | where name -match "ssh-agent" | select Name,Status,StartType` \
`ssh-agent` \
`ssh-add C:\Users\Lifailon\.ssh\id_rsa` \
`cat ~\.ssh\id_rsa.pub | Set-Clipboard` copy to [settings keys](https://github.com/settings/keys) \
`cd $home\Documents\Git` \
`git clone git@github.com:Lifailon/lifailon.github.io` \
`cd lifailon.github.io` \
`git grep "ping ya.ru"` поиск текста в файлах \
`git fetch` загрузить изменения из удаленного хранилища для обновления всех веток локального репозитория, не затрагивая текущую рабочую ветку (загружает все коммиты, ветки и т.д. которые не присутствуют в локальном репозитории) \
`git fetch --all` загрузить все ветки с удаленного репозитория (обновляет информацию о состоянии удаленного репозитория и загружает все изменения ваших веток без автоматического объединения) \
`git pull` загрузить изменения из удаленного хранилища для обновления локального репозитория (выполняет git fetch, чтобы получить последние изменения из удаленного репозитория, а затеим объеденяем изменения с локальной копией с помощью git merge для обновления текущей рабочей ветки) \
`git status` отобразить статус изменений по файлам \
`git diff` отобразить историю изменений построчно \
`git diff pandoc` сравнивает изменения в текущей рабочей директории с последним коммитом в указанной ветке \
`git add .` добавить (проиндексировать) изменения во всех файлах текущего каталога \
`git commit -m "update powershell commands"` сохранить изменения с комментарием \
`git push` синхронизировать локальные изменения с репозиторием на сервере \
`git push origin mkdocs-material` отправить в конкретную ветку \
`git push origin --delete mkdocs` удалить ветку на удаленном сервере \
`git commit --amend` изменить комментарий в последнем коммите (до push) \
`git commit --amend --no-edit --date="Sun Oct 27 23:20:00 2024 +0300"` изменить дату последнего коммита \
`git branch -a` отобразить все ветки (в том числе удаленные remotes/origin) \
`git branch hugo` создать новую ветку \
`git branch -m hugo-public` переименовать текущую ветку \
`git branch -d hugo-public` удалить ветку \
`git switch hugo` переключиться на другую ветку \
`git push origin hugo` отправить изменения в указанную ветку \
`git push --set-upstream origin hugo` отслеживать изменения в ветке (позволяет делать push без указания ветки) \
`git merge hugo` слияние текущей ветки (pandoc) с указанной (hugo) \
`git log --oneline --all` лог коммитов \
`git log --graph` коммиты и следование веток \
`git log --author="Lifailon"` показывает историю коммитов указанного пользователя \
`git blame index.html` показывает, кто и когда внес изменения в каждую строку указанного файла \
`git show d01f09dead3a6a8d75dda848162831c58ca0ee13` отобразить подробный лог по номеру коммита \
`git restore filename` отменить все локальные изменения в рабочей копии независимо от того, были они проиндексированы или нет (если была индексация через add), возвращая его к состоянию на момент последнего коммита \
`git restore --source d01f09dead3a6a8d75dda848162831c58ca0ee13 filename` восстановить файл на указанную версию по хэшу индентификатора коммита \
`git checkout filename` откатить изменения не проиндексированные для коммита, возвращая его к состоянию, каким оно было на момент последнего коммита (если не было индексации через add) \
`git checkout d01f09dead3a6a8d75dda848162831c58ca0ee13` переключить локальные файлы рабочей копии на указанный коммит (переключает HEAD на указанный коммит) \
`git reset HEAD filename` удалить указанный файл из индекса без удаления самих изменений в файле для последующей повторной индексации (если был add но не было commit, потом выполнить checkout) \
`git reset --soft HEAD^` отменяет последний (^) коммит, сохраняя изменения из этого коммита в рабочем каталоге и индексе (подготовленной области), можно внести изменения в файлы и повторно их зафиксировать \
`git reset --hard HEAD^` полностью отменяет последний коммит, удаляя все его изменения из рабочего каталога и индекса до состояния последнего коммита \
`git reset --hard d01f09dead3a6a8d75dda848162831c58ca0ee13` откатывает HEAD к указанному коммиту и удаляет все коммиты, которые были сделаны после него (будут потеряны все незакоммиченные изменения и историю коммитов после указанного) \
`git revert HEAD --no-edit` создает новый коммит, который отменяет последний коммит (HEAD) и новый коммит будет добавлен поверх него (события записываются в git log) \
`git revert d01f09dead3a6a8d75dda848162831c58ca0ee13` создает новый коммит, который отменяет изменения, внесенные в указанный коммит с хешем (не изменяет историю коммитов, а создает новый коммит с изменениями отмены) \
`git stash` сохраняет текущие изменения в стэш (временное хранилище) и очищает рабочую директорию \
`git stash pop` применяет последние изменения из стэша к текущей ветке

# GitHub api

`$user = "Lifailon"` \
`$repository = "ReverseProxyNET"` \
`Invoke-RestMethod https://api.github.com/users/$($user)` получаем информацию о пользователе \
`Invoke-RestMethod https://api.github.com/users/$($user)/repos` получаем список последних (актуальные коммиты) 30 репозиториев указанного пользователя \
`Invoke-RestMethod https://api.github.com/users/$($user)/repos?per_page=100` получаем список последних (актуальные коммиты) 100 репозиториев указанного пользователя \
`Invoke-RestMethod https://api.github.com/repos/$($user)/$($repository)/contents` получаем содержимое корневой директории репозитория \
`Invoke-RestMethod https://api.github.com/repos/$($user)/$($repository)/contents/source/rpnet.cs` получаем содержимое файла в формате Base64 \
`$commits = Invoke-RestMethod https://api.github.com/repos/$($user)/$($repository)/commits` получаем список коммитов \
`$commits[0].commit.message` читаем комментарий последнего коммита \
`$commits[0].commit.committer.date` получаем дату последнего коммита \
`Invoke-RestMethod https://api.github.com/repos/$($user)/$($repository)/commits/$($commits[0].sha)` получаем подробную информацию изменений о последнем коммите в репозитории \
`$releases_latest = Invoke-RestMethod "https://api.github.com/repos/$($user)/$($repository)/releases/latest"` получаем информацию о последнем релизе в репозитории \
`$releases_latest.assets.name` список приложенных файлов последнего релиза \
`$releases_latest.assets.browser_download_url` получаем список url для загрузки файлов \
`$($releases_latest.assets | Where-Object name -like "*win*x64*exe*").browser_download_url` фильтруем по ОС и разрядности \
`$(Invoke-RestMethod -Uri "https://api.github.com/repos/Lifailon/epic-games-radar/commits?path=api/giveaway/index.json")[0].commit.author.date` узнать дату последнего обновления файла в репозитории \
`$issues = Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/issues?per_page=500` получаем список открытых проблем в репозитории (получаем максимум 100 последних, по умолчанию забираем последние 30 issues) \
`$issue_number = $($issues | Where-Object title -match "PowerShell").number` получаем номер issue, в заголовке которого есть слово "PowerShell" \
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/issues/$($issue_number)/comments` отобразить список комментарием указанного issues \
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/languages` получаем список языков программирования, используемых в репозитории \
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/pulls` получаем список всех pull requests в репозитории \
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/forks` получаем список форков (forks) \
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/stargazers?per_page=4000` получаем список пользователей, которые поставили звезды репозиторию \
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/subscribers` получаем список подписчиков (watchers) репозитория

# GitHub Actions

## Runner (Agent)

`mkdir actions-runner; cd actions-runner` \
`Invoke-WebRequest -Uri https://github.com/actions/runner/releases/download/v2.316.1/actions-runner-win-x64-2.316.1.zip -OutFile actions-runner-win-x64-2.316.1.zip` загрузить пакет с Runner последней версии \
`if((Get-FileHash -Path actions-runner-win-x64-2.316.1.zip -Algorithm SHA256).Hash.ToUpper() -ne 'e41debe4f0a83f66b28993eaf84dad944c8c82e2c9da81f56a850bc27fedd76b'.ToUpper()){ throw 'Computed checksum did not match' }` проверить валидность пакета с помощью hash-суммы \
`Add-Type -AssemblyName System.IO.Compression.FileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory("$PWD/actions-runner-win-x64-2.316.1.zip", "$PWD")` разархивировать \
`Remove-Item *.zip` удалить архив \
`./config.cmd --url https://github.com/Lifailon/egapi --token XXXXXXXXXXXXXXXXXXXXXXXXXXXXX` авторизовать и сконфигурировать сборщика с помощью скрипта (что бы на последнем пункте создать службу для управления сборщиком, нужно запустить консоль с правами администратора) \
`./run.cmd` запустить процесс (если не используется служба) \
`Get-Service *actions* | Start-Service` запустить службу \
`Get-Process *Runner.Listener*` \
`./config.cmd remove --token XXXXXXXXXXXXXXXXXXXXXXXXXXXXX` удалить конфигурацию

## Build (Pipeline)

```yaml
name: build-game-list

on:
  # Разрешить ручной запуск workflow через интерфейс GitHub
  workflow_dispatch:
  
  # Запускать workflow по расписанию каждый час в 00 минут
  schedule:
  - cron: '00 * * * *'

jobs:
  Job_01:
    # Указываем, что job будет выполняться на последней версии Ubuntu
    runs-on: ubuntu-latest
    
    steps:
    # Шаги, которые будут выполнены в рамках этого job
    - name: Checkout repository
      # Клонирования репозиторий
      uses: actions/checkout@v2
    
    - name: Get content and write to file
      # Выполняем скрипт PowerShell, расположенный в ./scripts/Get-GameList.ps1
      run: pwsh -File ./scripts/Get-GameList.ps1
      # Указываем, что команда должна выполняться в оболочке bash
      shell: bash 

    - name: Commit and push changes
      run: |
        # Задаем имя пользователя и email для коммитов
        git config --global user.name 'GitHub Actions'
        git config --global user.email 'actions@github.com'
        # Добавляем все изменения в индекс
        git add .
        # Делаем коммит с комментарием
        git commit -m "update game list"
        # Отправляем коммит в удаленный репозиторий
        git push
```

## CI

```yaml
name: Docker Build and Push Image

on:
  # Запусать при git push в ветку main
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Клонируем репозиторий
      uses: actions/checkout@v2

    - name: Авторизация в Docker Hub
      uses: docker/login-action@v3
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Сборка образа и отправка в Docker Hub
      run: |
        docker build -t lifailon/torapi:latest .
        docker push lifailon/torapi:latest
```

## Logs

`$(Invoke-RestMethod https://api.github.com/repos/Lifailon/TorAPI/actions/workflows).total_count` получить количество запусков всех рабочих процессов \
`$(Invoke-RestMethod https://api.github.com/repos/Lifailon/TorAPI/actions/workflows).workflows` подробная информации о запускаемых рабочих процессах \
`$actions_last_id = $(Invoke-RestMethod https://api.github.com/repos/Lifailon/TorAPI/actions/workflows).workflows[-1].id` получить идентификатор последнего события \
`$(Invoke-RestMethod https://api.github.com/repos/Lifailon/TorAPI/actions/workflows/$actions_last_id/runs).workflow_runs` подробная информация о последней сборке \
`$run_id = $(Invoke-RestMethod https://api.github.com/repos/Lifailon/TorAPI/actions/workflows/$actions_last_id/runs).workflow_runs.id` получить идентификатор запуска рабочего процесса \
`$(Invoke-RestMethod "https://api.github.com/repos/Lifailon/TorAPI/actions/runs/$run_id/jobs").jobs.steps` подробная информация для всех шагов выполнения (время работы и статус выполнения) \
`$jobs_id = $(Invoke-RestMethod "https://api.github.com/repos/Lifailon/TorAPI/actions/runs/$run_id/jobs").jobs[0].id` получить идентификатор последнего задания указанного рабочего процесса

```PowerShell
$url = "https://api.github.com/repos/Lifailon/TorAPI/actions/jobs/$jobs_id/logs"
$headers = @{
    Authorization = "token ghp_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
}
Invoke-RestMethod -Uri $url -Headers $headers # получить логи задания
```

# Vercel

`npm i -g vercel` установить глобально в систему Vercel CLI \
`vercel --version` выводит текущую версию установленного Vercel CLI \
`vercel login` выполняет вход в аккаунт Vercel (`> Continue with GitHub`) \
`vercel logout` выполняет выход из аккаунта Vercel \
`vercel init` инициализирует новый проект в текущей директории (создает файл конфигурации vercel.json и другие файлы, необходимые для проекта) \
`vercel dev` запускает локальный сервер для проверки работоспособности (http://localhost:3000) \
`vercel deploy` загружает проект на серверы Vercel и развертывает его \
`vercel link` привязывает текущую директорию к существующему проекту на сервере Vercel (выбрать из списка) \
`vercel unlink` отменяет привязку текущей директории от проекта Vercel \
`vercel env` управляет переменными окружения для проекта \
`vercel env pull` подтягивает переменные окружения с Vercel в локальный .env файл \
`vercel env ls` показывает список всех переменных окружения для проекта \
`vercel env add <key> <environment>` добавляет новую переменную окружения для указанного окружения (production, preview, development) \
`vercel env rm <key> <environment>` удаляет переменную окружения из указанного окружения \
`vercel projects` управляет проектами Vercel \
`vercel projects ls` показывает список всех проектов \
`vercel projects add` добавляет новый проект \
`vercel projects rm <project>` удаляет указанный проект \
`vercel pull` подтягивает последние настройки окружения с Vercel \
`vercel alias` управляет алиасами доменов для проектов \
`vercel alias ls` показывает список всех алиасов для текущего проекта \
`vercel alias set <alias>` устанавливает алиас для указанного проекта \
`vercel alias rm <alias>` удаляет указанный алиас \
`vercel domains` управляет доменами, привязанными к проекту \
`vercel domains ls` показывает список всех доменов \
`vercel domains add <domain>` добавляет новый домен к проекту \
`vercel domains rm <domain>` удаляет указанный домен \
`vercel teams` управляет командами и членами команд на Vercel \
`vercel teams ls` показывает список всех команд \
`vercel teams add <team>` добавляет новую команду \
`vercel teams rm <team>` удаляет указанную команду \
`vercel logs <deployment>` выводит логи для указанного деплоя \
`vercel secrets` управляет секретами, используемыми в проектах \
`vercel secrets add <name> <value>` добавляет новый секрет \
`vercel secrets rm <name>` удаляет указанный секрет \
`vercel secrets ls` показывает список всех секретов \
`vercel switch <team>` переключается между командами и аккаунтами Vercel

## CD

```yaml
name: Deploy to Vercel

on:
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - name: Clone repository
      uses: actions/checkout@v4

    - name: Install Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'

    - name: Install dependencies
      run: npm install

    - name: Deploy to Vercel
      uses: amondnet/vercel-action@v25
      with:
        vercel-token: ${{ secrets.VERCEL_TOKEN }}
        vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
        vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
        vercel-args: '--prod'
```

# GitLab

```bash
docker run --detach \
    --hostname 192.168.3.101 \
    --publish 443:443 --publish 80:80 --publish 2222:22 \
    --name gitlab \
    --restart always \
    --volume /srv/gitlab/config:/etc/gitlab \
    --volume /srv/gitlab/logs:/var/log/gitlab \
    --volume /srv/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ee:latest
```

`docker logs -f gitlab` логи контейнера \
`docker exec -it gitlab cat /etc/gitlab/initial_root_password` получить пароль для root \
`docker exec -it gitlab cat /etc/gitlab/gitlab.rb` конфигурация сервера

Получить токен регистрации Runner: http://192.168.3.101/root/torapi/-/settings/ci_cd#js-runners-settings

`curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64` загрузить исполняемый файл Runner
`chmod +x /usr/local/bin/gitlab-runner`

```bash
docker run -d --name gitlab-runner --restart always \
    -v /srv/gitlab-runner/config:/etc/gitlab-runner \
    gitlab/gitlab-runner:latest
```

`docker exec -it gitlab-runner bash` \
`gitlab-runner list` список сборщиков \
`gitlab-runner verify` проверка \
`gitlab-runner restart` применить настройки \
`gitlab-runner status` статус \
`gitlab-runner unregister --all-runners` удалить все регистрации \
`gitlab-runner install` установить службу \
`gitlab-runner run` запустить с выводом в консоль

`gitlab-runner register`
```
Enter the GitLab instance URL (for example, https://gitlab.com/): http://192.168.3.101/
Enter the registration token: GR1348941enqAxqQgm8AZJD_g7vme
Enter an executor: shell
```
`cat /etc/gitlab-runner/config.toml` конфигурация

Включить импорт проектов из GitHub: http://192.168.3.101/admin/application_settings/general#js-import-export-settings

```yaml
variables:
  PORT: 2024
  TITLE: "The+Rookie"

stages:
  - test

test:
  stage: test
  script:
    - |
        pwsh -Command "
            Write-Host PORT - $env:PORT
            Write-Host TITLE - $env:TITLE
            npm install
            Start-Process -NoNewWindow -FilePath 'npm' -ArgumentList 'start -- --port $env:PORT' -RedirectStandardOutput 'torapi.log'
            Start-Sleep -Seconds 5
            Invoke-RestMethod -Uri http://localhost:$env:PORT/api/search/title/all?query=$env:TITLE | Format-List
            Get-Content torapi.log
            Stop-Process -Name 'node' -Force -ErrorAction SilentlyContinue
        "
```

# Secret Manager

## Bitwarden

`choco install bitwarden-cli || npm install -g @bitwarden/cli || sudo snap install bw` установить bitwarden cli \
`bw login <email> --apikey` авторизвация в хранилище, используя client_id и client_secret \
`$session = bw unlock --raw` получить токен сессии \
`$items = bw list items --session $session | ConvertFrom-Json` получение всех элементов в хранилище с использованием мастер-пароля \
`echo "master_password" | bw get item GitHub bw get password $items[0].name` получить пароль по названию секрета \
`bw lock` завершить сессию
```PowerShell
# Авторизация в организации
$client_id = "organization.ClientId"
$client_secret = "client_secret"
$deviceIdentifier = [guid]::NewGuid().ToString()
$deviceName = "PowerShell-Client"
$response = Invoke-RestMethod -Uri "https://identity.bitwarden.com/connect/token" -Method POST `
    -Headers @{ "Content-Type" = "application/x-www-form-urlencoded" } `
    -Body @{
        grant_type = "client_credentials"
        scope = "api.organization"
        client_id = $client_id
        client_secret = $client_secret
        deviceIdentifier = $deviceIdentifier
        deviceName = $deviceName
    }
# Получение токена доступа
$accessToken = $response.access_token
# Название элемента в хранилище
$itemName = "GitHub"
# Поиск элемента в хранилище
$itemResponse = Invoke-RestMethod -Uri "https://api.bitwarden.com/v1/objects?search=$itemName" -Method GET `
    -Headers @{ "Authorization" = "Bearer $accessToken" }
$item = $itemResponse.data[0]
# Получение информации об элементе
$detailsResponse = Invoke-RestMethod -Uri "https://api.bitwarden.com/v1/objects/$($item.id)" -Method GET `
    -Headers @{ "Authorization" = "Bearer $accessToken" }
# Получение логина и пароля
$login = $detailsResponse.login.username
$password = $detailsResponse.login.password
```
## Infisical

`npm install -g @infisical/cli` \
`infisical login` авторизоваться в хранилище (cloud или Self-Hosting) \
`infisical init` инициализировать - выбрать организацию и проект \
`infisical secrets` получить список секретов и их SECRET VALUE из добавленных групп Environments (Development, Staging, Production)
```PowerShell
$clientId = "<client_id>" # создать организацию и клиент в Organization Access Control - Identities и предоставить права на Projects (Secret Management)
$clientSecret = "<client_secret>" # на той же вкладке вкладке в Authentication сгенерировать секрет (Create Client Secret)
$body = @{
    clientId     = $clientId
    clientSecret = $clientSecret
}
$response = Invoke-RestMethod -Uri "https://app.infisical.com/api/v1/auth/universal-auth/login" `
    -Method POST `
    -ContentType "application/x-www-form-urlencoded" `
    -Body $body
$TOKEN = $response.accessToken # получить токен доступа
# Получить содержимое секрета
$secretName = "FOO" # название секрета
$workspaceId = "82488c0a-6d3a-4220-9d69-19889f09c8c8" # можно взять из url проекта Secret Management
$environment = "dev" # группа
$headers = @{
    Authorization = "Bearer $TOKEN"
}
$secrets = Invoke-RestMethod -Uri "https://app.infisical.com/api/v3/secrets/raw/${secretName}?workspaceId=${workspaceId}&environment=${environment}" -Method GET -Headers $headers
$secrets.secret.secretKey
$secrets.secret.secretValue
```

## HashiCorp

```bash
docker run --cap-add=IPC_LOCK -d --name=hashicorp-vault -p 8200:8200 \
  -v hashicorp-vault-file:/vault/file \
  -v hashicorp-vault-logs:/vault/logs \
  hashicorp/vaul

2025-01-26 20:06:14 Api Address: http://0.0.0.0:8200
2025-01-26 20:06:14 Unseal Key: XOD8uWWSL7LAAUwPqBTvryr3U6l9J3Q7CDVc+YmTET8=
2025-01-26 20:06:14 Root Token: hvs.aYaGulrLe2pySPTDbZhOQCar
```

`Secrets Engines` -> `Enable new engine` + `KV` \
API Swagger: http://192.168.3.100:8200/ui/vault/tools/api-explorer

```PowerShell
$TOKEN = "hvs.aYaGulrLe2pySPTDbZhOQCar"
$Headers = @{
    "X-Vault-Token" = $TOKEN
}
# Указать путь до секретов (создается в корне kv)
$path = "main-path"
$url = "http://192.168.3.100:8200/v1/kv/data/$path"
$data = Invoke-RestMethod -Uri $url -Method GET -Headers $Headers
# Получить содержимое ключа по его названию
$data.data.data.key_name # secret_value

# Перезаписать все секреты
$Headers = @{
    "X-Vault-Token" = $TOKEN
}
$Body = @{
    data = @{
        key_name_1 = "key_value_1"
        key_name_2 = "key_value_2"
    }
    options = @{}
    version = 0
} | ConvertTo-Json
$urlUpdate = "http://192.168.3.100:8200/v1/kv/data/main-path"
Invoke-RestMethod -Uri $urlUpdate -Method POST -Headers $Headers -Body $Body

# Удалить все секреты
Invoke-RestMethod -Uri "http://192.168.3.100:8200/v1/kv/data/main-path" -Method DELETE -Headers $Headers
```

Vault client:

```bash
# Установить клиент в Linux (debian):
wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install vault
# Включить механизм секретов KV
vault secrets enable -version=1 kv 
# Создать секрет
vault kv put kv/main-path key_name=secret_value
# Список секретов
vault kv list kv/
# Получить содержимое секрета
vault kv get -mount="kv" "main-path"
# Удалить секреты
vault kv delete kv/my-secret
```

# Ansible

`apt -y update && apt -y upgrade` \
`apt -y install ansible` v2.10.8 \
`apt -y install ansible-core` v2.12.0 \
`apt -y install sshpass`

`ansible-galaxy collection install ansible.windows` установить коллекцию модулей \
`ansible-galaxy collection install community.windows` \
`ansible-galaxy collection list | grep windows` \
`ansible-config dump | grep DEFAULT_MODULE_PATH` путь хранения модулей

`apt-get -y install python-dev libkrb5-dev krb5-user` пакеты для Kerberos аутентификации \
`apt install python3-pip` \
`pip3 install requests-kerberos` \
`nano /etc/krb5.conf` настроить [realms] и [domain_realm] \
`kinit -C support4@domail.local` \
`klist`

`ansible --version` \
`config file = None` \
`nano /etc/ansible/ansible.cfg` файл конфигурации

```yaml
[defaults]
inventory = /etc/ansible/hosts
# uncomment this to disable SSH key host checking
# Отключить проверку ключа ssh (для подключения используя пароль)
host_key_checking = False
```

## Hosts

`nano /etc/ansible/hosts`
```yaml
[us]
pi-hole-01 ansible_host=192.168.3.101
zabbix-01 ansible_host=192.168.3.102
grafana-01 ansible_host=192.168.3.103
netbox-01 ansible_host=192.168.3.104

[all:vars]
ansible_ssh_port=2121
ansible_user=lifailon
ansible_password=123098
path_user=/home/lifailon
ansible_python_interpreter=/usr/bin/python3

[ws]
huawei-book-01 ansible_host=192.168.3.99
plex-01 ansible_host=192.168.3.100

[ws:vars]
ansible_port=5985
#ansible_port=5986
ansible_user=Lifailon
#ansible_user=support4@DOMAIN.LOCAL
ansible_password=123098
ansible_connection=winrm
ansible_winrm_scheme=http
ansible_winrm_transport=basic
#ansible_winrm_transport=kerberos
ansible_winrm_server_cert_validation=ignore
validate_certs=false

[win_ssh]
huawei-book-01 ansible_host=192.168.3.99
plex-01 ansible_host=192.168.3.100

[win_ssh:vars]
ansible_python_interpreter=C:\Users\Lifailon\AppData\Local\Programs\Python\Python311\` добавить переменную среды интерпритатора Python в Windows
ansible_connection=ssh
#ansible_shell_type=cmd
ansible_shell_type=powershell
```
`ansible-inventory --list` проверить конфигурацию (читает в формате JSON) или YAML (-y) с просмотром все применяемых переменных

## Windows Modules

`ansible us -m ping` \
`ansible win_ssh -m ping` \
`ansible us -m shell -a "uptime && df -h | grep lv"` \
`ansible us -m setup | grep -iP "mem|proc"` информация о железе \
`ansible us -m apt -a "name=mc" -b` повысить привилегии sudo (-b) \
`ansible us -m service -a "name=ssh state=restarted enabled=yes" -b` перезапустить службу \
`echo "echo test" > test.sh` \
`ansible us -m copy -a "src=test.sh dest=/root mode=777" -b` \
`ansible us -a "ls /root" -b` \
`ansible us -a "cat /root/test.sh" -b`

`ansible-doc -l | grep win_` [список всех модулей Windows](https://docs.ansible.com/ansible/latest/collections/ansible/windows/) \
`ansible ws -m win_ping` windows модуль \
`ansible ws -m win_ping -u WinRM-Writer` указать логин \
`ansible ws -m setup` собрать подробную информацию о системе \
`ansible ws -m win_whoami` информация о правах доступах, группах доступа \
`ansible ws -m win_shell -a '$PSVersionTable'` \
`ansible ws -m win_shell -a 'Get-Service | where name -match "ssh|winrm"'` \
`ansible ws -m win_service -a "name=sshd state=stopped"` \
`ansible ws -m win_service -a "name=sshd state=started"`

- win_shell (vars/debug)

`nano /etc/ansible/PowerShell-Vars.yml`
```yaml
- hosts: ws
 ` Указать коллекцию модулей
  collections:
  - ansible.windows
 ` Задать переменные
  vars:
    SearchName: PermitRoot
  tasks:
  - name: Get port ssh
    win_shell: |
      Get-Content "C:\Programdata\ssh\sshd_config" | Select-String "{{SearchName}}"
   ` Передать вывод в переменную
    register: command_output
  - name: Output port ssh
   ` Вывести переменную на экран
    debug:
      var: command_output.stdout_lines
```
`ansible-playbook /etc/ansible/PowerShell-Vars.yml` \
`ansible-playbook /etc/ansible/PowerShell-Vars.yml --extra-vars "SearchName='LogLevel|Syslog'"` передать переменную

- win_powershell

`nano /etc/ansible/powershell-param.yml`
```yaml
- hosts: ws
  tasks:
  - name: Run PowerShell script with parameters
    ansible.windows.win_powershell:
      parameters:
        Path: C:\Temp
        Force: true
      script: |
        [CmdletBinding()]
        param (
          [String]$Path,
          [Switch]$Force
        )
        New-Item -Path $Path -ItemType Directory -Force:$Force
```
`ansible-playbook /etc/ansible/powershell-param.yml`

- win_chocolatey

`nano /etc/ansible/setup-adobe-acrobat.yml`
```yaml
- hosts: ws
  tasks:
  - name: Install Acrobat Reader
    win_chocolatey:
      name: adobereader
      state: present
```
`ansible-playbook /etc/ansible/setup-adobe-acrobat.yml`

`nano /etc/ansible/setup-openssh.yml`
```yaml
- hosts: ws
  tasks:
  - name: install the Win32-OpenSSH service
    win_chocolatey:
      name: openssh
      package_params: /SSHServerFeature
      state: present
```
`ansible-playbook /etc/ansible/setup-openssh.yml`

- win_regedit

`nano /etc/ansible/win-set-shell-ssh-ps7.yml`
```yaml
- hosts: ws
  tasks:
  - name: Set the default shell to PowerShell 7 for Windows OpenSSH
    win_regedit:
      path: HKLM:\SOFTWARE\OpenSSH
      name: DefaultShell
     ` data: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
      data: 'C:\Program Files\PowerShell\7\pwsh.exe'
      type: string
      state: present
```
`ansible-playbook /etc/ansible/win-set-shell-ssh-ps7.yml`

- win_service

`nano /etc/ansible/win-service.yml`
```yaml
- hosts: ws
  tasks:
  - name: Start service
    win_service:
      name: sshd
      state: started
#     state: stopped
#     state: restarted
#     start_mode: auto
```
`ansible-playbook /etc/ansible/win-service.yml`

- win_service_info

`nano /etc/ansible/get-service.yml`
```yaml
- hosts: ws
  tasks:
  - name: Get info for a single service
    win_service_info:
      name: sshd
    register: service_info
  - name: Print returned information
    ansible.builtin.debug:
      var: service_info.services
```
`ansible-playbook /etc/ansible/get-service.yml`

- fetch/slurp

`nano /etc/ansible/copy-from-win-to-local.yml`
```yaml
- hosts: ws
  tasks:
  - name: Retrieve remote file on a Windows host
#   Скопировать файл из Windows-системы
    ansible.builtin.fetch:
#   Прочитать файл (передать в память в формате Base64)
#   ansible.builtin.slurp:
      src: C:\Telegraf\telegraf.conf
      dest: /root/telegraf.conf
      flat: yes
    register: telegraf_conf
  - name: Print returned information
    ansible.builtin.debug:
      msg: "{{ telegraf_conf['content'] | b64decode }}"
```
`ansible-playbook /etc/ansible/copy-from-win-to-local.yml`

- win_copy

`echo "Get-Service | where name -eq vss | Start-Service" > /home/lifailon/Start-Service-VSS.ps1` \
`nano /etc/ansible/copy-file-to-win.yml`
```yaml
- hosts: ws
  tasks:
  - name: Copy file to win hosts
    win_copy:
      src: /home/lifailon/Start-Service-VSS.ps1
      dest: C:\Users\Lifailon\Desktop\Start-Service-VSS.ps1
```
`ansible-playbook /etc/ansible/copy-file-to-win.yml`

`curl -OL https://github.com/PowerShell/PowerShell/releases/download/v7.3.6/PowerShell-7.3.6-win-x64.msi` \
`nano /etc/ansible/copy-file-to-win.yml`
```yaml
- hosts: ws
  tasks:
  - name: Copy file to win hosts
    win_copy:
      src: /home/lifailon/PowerShell-7.3.6-win-x64.msi
      dest: C:\Install\PowerShell-7.3.6.msi
```
`ansible-playbook /etc/ansible/copy-file-to-win.yml`

- win_command

`nano /etc/ansible/run-script-ps1.yml`
```yaml
- hosts: ws
  tasks:
  - name: Run PowerShell Script
    win_command: powershell -ExecutionPolicy ByPass -File C:\Users\Lifailon\Desktop\Start-Service-VSS.ps1
```
`ansible-playbook /etc/ansible/run-script-ps1.yml`

- win_package

`nano /etc/ansible/setup-msi-package.yml`
```yaml
- hosts: ws
  tasks:
  - name: Install MSI Package
    win_package:
#     path: C:\Install\7z-23.01.msi
      path: C:\Install\PowerShell-7.3.6.msi
      arguments:
        - /quiet
        - /passive
        - /norestart
```
`ansible-playbook /etc/ansible/setup-msi-package.yml`

- win_firewall_rule

`nano /etc/ansible/win-fw-open.yml`
```yaml
- hosts: ws
  tasks:
  - name: Open RDP port
    win_firewall_rule:
      name: Open RDP port
      localport: 3389
      action: allow
      direction: in
      protocol: tcp
      state: present
      enabled: yes
```
`ansible-playbook /etc/ansible/win-fw-open.yml`

- win_group

`nano /etc/ansible/win-creat-group.yml`
```yaml
- hosts: ws
  tasks:
  - name: Create a new group
    win_group:
      name: deploy
      description: Deploy Group
      state: present
```
`ansible-playbook /etc/ansible/win-creat-group.yml`

- win_group_membership

`nano /etc/ansible/add-user-to-group.yml`
```yaml
- hosts: ws
  tasks:
  - name: Add a local and domain user to a local group
    win_group_membership:
      name: deploy
      members:
        - WinRM-Writer
      state: present
```
`ansible-playbook /etc/ansible/add-user-to-group.yml`

- win_user

`nano /etc/ansible/creat-win-user.yml`
```yaml
- hosts: ws
  tasks:
  - name: Creat user
    win_user:
      name: test
      password: 123098
      state: present
      groups:
        - deploy
```
`ansible-playbook /etc/ansible/creat-win-user.yml`

`nano /etc/ansible/delete-win-user.yml`
```yaml
- hosts: ws
  tasks:
  - name: Delete user
    ansible.windows.win_user:
      name: test
      state: absent
```
`ansible-playbook /etc/ansible/delete-win-user.yml`

- win_feature

`nano /etc/ansible/install-feature.yml`
```yaml
- hosts: ws
  tasks:
  - name: Install Windows Feature
      win_feature:
        name: SNMP-Service
        state: present
```
`ansible-playbook /etc/ansible/install-feature.yml`

- win_reboot

`nano /etc/ansible/win-reboot.yml`
```yaml
- hosts: ws
  tasks:
  - name: Reboot a slow machine that might have lots of updates to apply
    win_reboot:
      reboot_timeout: 3600
```
`ansible-playbook /etc/ansible/win-reboot.yml`

- win_find

`nano /etc/ansible/win-ls.yml`
```yaml
- hosts: ws
  tasks:
  - name: Find files in multiple paths
    ansible.windows.win_find:
      paths:
      - D:\Install\OpenSource
      patterns: ['*.rar','*.zip','*.msi']
     ` Файл созданный менее 7 дней назад
      age: -7d
     ` Размер файла больше 10MB
      size: 10485760
     ` Рекурсивный поиск (в дочерних директориях)
      recurse: true
    register: command_output
  - name: Output
    debug:
      var: command_output
```
`ansible-playbook /etc/ansible/win-ls.yml`

- win_uri

`nano /etc/ansible/rest-get.yml`
```yaml
- hosts: ws
  tasks:
  - name: REST GET request to endpoint github
    ansible.windows.win_uri:
      url: https://api.github.com/repos/Lifailon/pSyslog/releases/latest
    register: http_output
  - name: Output
    debug:
      var: http_output
```
`ansible-playbook /etc/ansible/rest-get.yml`

- win_updates

`nano /etc/ansible/win-update.yml`
```yaml
- hosts: ws
  tasks:
  - name: Install only particular updates based on the KB numbers
    ansible.windows.win_updates:
      category_names:
      - SecurityUpdates
      - CriticalUpdates
      - UpdateRollups
      - Drivers
     ` Фильтрация
     ` accept_list:
     ` - KB2267602
     ` Поиск обновлений
     ` state: searched
     ` Загрузить обновления
     ` state: downloaded
     ` Установить обновления
      state: installed
      log_path: C:\Ansible-Windows-Upadte-Log.txt
      reboot: false
    register: wu_output
  - name: Output
    debug:
      var: wu_output
```
`ansible-playbook /etc/ansible/win-update.yml`

- win_chocolatey

[Install](https://chocolatey.org/install) \
[API](https://community.chocolatey.org/api/v2/package/chocolatey) \
[Deployment](https://docs.chocolatey.org/en-us/guides/organizations/organizational-deployment-guide)
```yaml
- name: Ensure Chocolatey installed from internal repo
  win_chocolatey:
    name: chocolatey
    state: present
	# source: URL-адрес внутреннего репозитория
    source: https://community.chocolatey.org/api/v2/ChocolateyInstall.ps1
```

# DSC

`Import-Module PSDesiredStateConfiguration` \
`Get-Command -Module PSDesiredStateConfiguration` \
`(Get-Module PSDesiredStateConfiguration).ExportedCommands` \
`Get-DscLocalConfigurationManager`

`Get-DscResource` \
`Get-DscResource -Name File -Syntax` [синтаксис](https://learn.microsoft.com/ru-ru/powershell/dsc/reference/resources/windows/fileresource?view=dsc-1.1)

`Ensure = Present` настройка должна быть включена (каталог должен присутствовать, процесс должен быть запущен, если нет – создать, запустить) \
`Ensure = Absent` настройка должна быть выключена (каталога быть не должно, процесс не должен быть запущен, если нет – удалить, остановить)
```PowerShell
Configuration TestConfiguraion
{
    Ctrl+Space
}

Configuration DSConfigurationProxy 
{
    Node vproxy-01 
    {
        File CreateDir
        {
            Ensure = "Present"
            Type = "Directory"
            DestinationPath = "C:\Temp"
        }
        Service StopW32time
        {
            Name = "w32time"
            State = "Stopped"` Running
        }
    WindowsProcess RunCalc
        {
            Ensure = "Present"
            Path = "C:\WINDOWS\system32\calc.exe"
            Arguments = ""
        }
        Registry RegSettings
        {
            Ensure = "Present"
            Key = "HKEY_LOCAL_MACHINE\SOFTWARE\MySoft"
            ValueName = "TestName"
            ValueData = "TestValue"
            ValueType = "String"
        }
#		WindowsFeature IIS
#       {
#            Ensure = "Present"
#            Name = "Web-Server"
#       }
    }
}
```
`$Path = (DSConfigurationProxy).DirectoryName` \
`Test-DscConfiguration -Path $Path | select *` ResourcesInDesiredState - уже настроено, ResourcesNotInDesiredState - не настроено (не соответствует) \
`Start-DscConfiguration -Path $Path` \
`Get-Job` \
`$srv = "vproxy-01"` \
`Get-Service -ComputerName $srv | ? name -match w32time # Start-Service` \
`icm $srv {Get-Process | ? ProcessName -match calc} | ft # Stop-Process -Force` \
`icm $srv {ls C:\ | ? name -match Temp} | ft` rm`
```PowerShell
Configuration InstallPowerShellCore {
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    Node localhost {
        Script InstallPowerShellCore {
            GetScript = {
                return @{
                    GetScript = $GetScript
                }
            }
            SetScript = {
        [string]$url = $(Invoke-RestMethod https://api.github.com/repos/PowerShell/PowerShell/releases/latest).assets.browser_download_url -match "win-x64.zip"
                $downloadPath = "$home\Downloads\PowerShell.zip"
                $installPath = "$env:ProgramFiles\PowerShell\7"
                Invoke-WebRequest -Uri $url -OutFile $downloadPath
                Expand-Archive -Path $downloadPath -DestinationPath $installPath -Force
            }
            TestScript = {
                return Test-Path "$env:ProgramFiles\PowerShell\7\pwsh.exe"
            }
        }
    }
}
```
`$Path = (InstallPowerShellCore).DirectoryName` \
`Test-DscConfiguration -Path $Path` \
`Start-DscConfiguration -Path $path -Wait -Verbose` \
`Get-Job`

# PSAppDeployToolkit

## Install-DeployToolkit
```PowerShell
$githubRepository = "psappdeploytoolkit/psappdeploytoolkit"
$filenamePatternMatch = "PSAppDeployToolkit*.zip"
$psadtReleaseUri = "https://api.github.com/repos/$githubRepository/releases/latest"
$psadtDownloadUri = ((Invoke-RestMethod -Method GET -Uri $psadtReleaseUri).assets | Where-Object name -like $filenamePatternMatch ).browser_download_url
$zipExtractionPath = Join-Path $env:USERPROFILE "Downloads" "PSAppDeployToolkit"
$zipTempDownloadPath = Join-Path -Path $([System.IO.Path]::GetTempPath()) -ChildPath $(Split-Path -Path $psadtDownloadUri -Leaf)
## Download to a temporary folder
Invoke-WebRequest -Uri $psadtDownloadUri -Out $zipTempDownloadPath
## Remove any Zone.Identifier alternate data streams to unblock the file (if required)
Unblock-File -Path $zipTempDownloadPath
New-Item -Type Directory $zipExtractionPath
Expand-Archive -Path $zipTempDownloadPath -OutputPath $zipExtractionPath -Force
Write-Host ("File: {0} extracted to Path: {1}" -f $psadtDownloadUri, $zipExtractionPath) -ForegroundColor Yellow
Remove-Item $zipTempDownloadPath
```
## Deploy-Notepad-Plus-Plus

`$url_notepad = "https://github.com/notepad-plus-plus/notepad-plus-plus/releases/download/v8.6.6/npp.8.6.6.Installer.x64.exe"` \
`Invoke-RestMethod $url_notepad -OutFile "$home\Downloads\PSAppDeployToolkit\Toolkit\Files\npp.8.6.6.Installer.x64.exe"`
```PowerShell
'# Подключаем модуль PSAppDeployToolkit
Import-Module "$PSScriptRoot\AppDeployToolkit\AppDeployToolkitMain.ps1"
# Название приложения
$AppName = "Notepad++"
# Версия приложения
$AppVersion = "8.6.6"
# Путь к установщику Notepad++
$InstallerPath = "$PSScriptRoot\Files\npp.$AppVersion.Installer.x64.exe"
# Проверка существования установщика
If (-not (Test-Path $InstallerPath)) {
    Write-Host "Установщик Notepad++ не найден: $InstallerPath"
    Exit-Script -ExitCode 1
}
# Настройки установки Notepad++
$InstallerArguments = "/S /D=$ProgramFiles\Notepad++"
Function Install-Application {
    # Выводим сообщение о начале установки
    Show-InstallationWelcome -CloseApps "iexplore" -CheckDiskSpace -PersistPrompt
    # Запускаем установку
    Execute-Process -Path $InstallerPath -Parameters $InstallerArguments -WindowStyle Hidden -IgnoreExitCodes "3010"
    # Выводим сообщение об успешной установке
    Show-InstallationPrompt -Message "Установка $AppName завершена." -ButtonRightText "Закрыть" -Icon Information -NoWait
    # Завершаем процесс установки
    Exit-Script -ExitCode $AppDependentExitCode
}
Install-Application' | Out-File "$home\Downloads\PSAppDeployToolkit\Toolkit\Deploy-Application.ps1" -Encoding unicode
```
`powershell -File "$home\Downloads\PSAppDeployToolkit\Toolkit\Deploy-Application.ps1"`

## Uninstall-Notepad-Plus-Plus
```PowerShell
'Import-Module "$PSScriptRoot\AppDeployToolkit\AppDeployToolkitMain.ps1"
$AppName = "Notepad++"
$UninstallerPath = "C:\Program Files\Notepad++\uninstall.exe"
If (-not (Test-Path $UninstallerPath)) {
    Write-Host "Деинсталлятор Notepad++ не найден: $UninstallerPath"
    Exit-Script -ExitCode 1
}
Function Uninstall-Application {
    Show-InstallationWelcome -CloseApps "iexplore" -CheckDiskSpace -PersistPrompt
    Execute-Process -Path $UninstallerPath -Parameters "/S" -WindowStyle Hidden -IgnoreExitCodes "3010"
    Show-InstallationPrompt -Message "Программа $AppName удалена." -ButtonRightText "Закрыть" -Icon Information -NoWait
    Exit-Script -ExitCode $AppDependentExitCode
}
Uninstall-Application' | Out-File "$home\Downloads\PSAppDeployToolkit\Toolkit\Deploy-Application.ps1" -Encoding unicode
```
`powershell -File "$home\Downloads\PSAppDeployToolkit\Toolkit\Deploy-Application.ps1"`

## Deploy-WinSCP
```PowerShell
$PSAppDeployToolkit = "$home\Downloads\PSAppDeployToolkit\"
$version = "6.3.3"
$url_winscp = "https://cdn.winscp.net/files/WinSCP-$version.msi?secure=P2HLWGKaMDigpDQw-H9BgA==,1716466173"
$WinSCP_Template = Get-Content "$PSAppDeployToolkit\Examples\WinSCP\Deploy-Application.ps1" # читаем пример конфигурации для WinSCP
$WinSCP_Template_Latest = $WinSCP_Template -replace "6.3.2","$version" # обновляем версию на актуальную
$WinSCP_Template_Latest > "$PSAppDeployToolkit\Toolkit\Deploy-Application.ps1" # заменяем скрипт развертывания 
Invoke-RestMethod $url_winscp -OutFile "$PSAppDeployToolkit\Toolkit\Files\WinSCP-$version.msi" # загружаем msi-пакет
powershell -File "$PSAppDeployToolkit\Toolkit\Deploy-Application.ps1" # запускаем установку
```

# Atlassian

## Bitbucket
```PowerShell
$url = "https://github.com/AtlassianPS/BitbucketPS/archive/refs/heads/master.zip"
Invoke-RestMethod $url -OutFile $home\Downloads\BitbucketPS.zip
Expand-Archive -Path "$home\Downloads\BitbucketPS.zip" -OutputPath "$home\Downloads"
Copy-Item -Path "$home\Downloads\BitbucketPS-master\*" -Destination "$($env:PSModulePath.Split(";")[0])\PSBitBucket" -Recurse
Remove-Item "$home\Downloads\Bitbucket*" -Recurse -Force
```
`Import-Module PSBitBucket` \
`Get-Command -Module PSBitBucket` \
`Set-BitBucketConfigServer -Url $url -User username -Password password` установить конфигурацию сервера BitBucket \
`Get-BitBucketConfigServer` получить текущую конфигурацию сервера BitBucket \
`Get-Repositories` получить список всех репозиториев для текущей конфигурации сервера BitBucket \
`Get-ProjectKey` получить ключ проекта BitBucket \
`Get-BranchList -Repository pSyslog` список всех веток в репозитории \
`Get-Branch -Repository pSyslog -Branch main` получить информацию о конкретной ветке репозитория \
`Get-CommitMessage -Repository pSyslog -CommitHash $hash` получить сообщение коммита по его хэшу \
`Get-Commits -Repository pSyslog -Limit 10` список последних 10 коммитов в репозитории \
`Get-CommitsForBranch -Repository pSyslog -Branch main` список коммитов для конкретной ветки в репозитории

## Jira

`Install-Module JiraPS -Scope CurrentUser -Repository PSGallery -AllowClobber -Force` \
`Get-Command -Module JiraPS` \
`Get-JiraServerInfo` информация о сервере \
`Add-JiraFilterPermission` добавить разрешения для фильтра \
`Add-JiraGroupMember` добавить участника в группу \
`Add-JiraIssueAttachment` добавить вложения к задаче \
`Add-JiraIssueComment` добавить комментария к задаче \
`Add-JiraIssueLink` добавить ссылки на задачу \
`Add-JiraIssueWatcher` добавить наблюдателя к задаче \
`Add-JiraIssueWorklog` добавить рабочего журнала к задаче \
`Find-JiraFilter` поиск фильтра \
`Format-Jira` форматирование данных Jira \
`Get-JiraComponent` получение компонента проекта \
`Get-JiraConfigServer` получение конфигурации сервера Jira \
`Get-JiraField` получение поля Jira \
`Get-JiraFilter` получение фильтра \
`Get-JiraFilterPermission` получение разрешения фильтра \
`Get-JiraGroup` получение группы \
`Get-JiraGroupMember` получение участников группы \
`Get-JiraIssue` получение задачи \
`Get-JiraIssueAttachment` получение вложения задачи \
`Get-JiraIssueAttachmentFile` получение файла вложения задачи \
`Get-JiraIssueComment` получение комментария задачи \
`Get-JiraIssueCreateMetadata` получение метаданных создания задачи \
`Get-JiraIssueEditMetadata` получение метаданных редактирования задачи \
`Get-JiraIssueLink` получение ссылки задачи \
`Get-JiraIssueLinkType` получение типа ссылки задачи \
`Get-JiraIssueType` получение типа задачи \
`Get-JiraIssueWatcher` получение наблюдателя задачи \
`Get-JiraIssueWorklog` получение рабочего журнала задачи \
`Get-JiraPriority` получение приоритета задачи \
`Get-JiraProject` получение проекта \
`Get-JiraRemoteLink` получение удаленной ссылки \
`Get-JiraServerInformation` получение информации о сервере Jira \
`Get-JiraSession` получение сессии \
`Get-JiraUser` получение пользователя \
`Get-JiraVersion` получение версии проекта \
`Invoke-JiraIssueTransition` выполнение перехода задачи \
`Invoke-JiraMethod` выполнение метода Jira \
`Move-JiraVersion` перемещение версии проекта \
`New-JiraFilter` создание нового фильтра \
`New-JiraGroup` создание новой группы \
`New-JiraIssue` создание новой задачи \
`New-JiraSession` создание новой сессии \
`New-JiraUser` создание нового пользователя \
`New-JiraVersion` создание новой версии проекта \
`Remove-JiraFilter` удаление фильтра \
`Remove-JiraFilterPermission` удаление разрешения фильтра \
`Remove-JiraGroup` удаление группы \
`Remove-JiraGroupMember` удаление участника группы \
`Remove-JiraIssue` удаление задачи \
`Remove-JiraIssueAttachment` удаление вложения задачи \
`Remove-JiraIssueLink` удаление ссылки задачи \
`Remove-JiraIssueWatcher` удаление наблюдателя задачи \
`Remove-JiraRemoteLink` удаление удаленной ссылки \
`Remove-JiraSession` удаление сессии \
`Remove-JiraUser` удаление пользователя \
`Remove-JiraVersion` удаление версии проекта \
`Set-JiraConfigServer` установка конфигурации сервера Jira \
`Set-JiraFilter` установка фильтра \
`Set-JiraIssue` установка задачи \
`Set-JiraIssueLabel` установка метки задачи \
`Set-JiraUser` установка пользователя \
`Set-JiraVersion` установка версии проекта

## Confluence

`Install-Module ConfluencePS -Scope CurrentUser -Repository PSGallery -AllowClobber -Force` \
`Get-Command -Module ConfluencePS` \
`Add-ConfluenceAttachment` добавить вложения к странице \
`Add-ConfluenceLabel` добавить метки к странице \
`ConvertTo-ConfluenceStorageFormat` конвертация содержимого в формат хранения Confluence \
`ConvertTo-ConfluenceTable` конвертация данных в таблицу Confluence \
`Get-ConfluenceAttachment` получение вложения страницы \
`Get-ConfluenceAttachmentFile` получение файла вложения страницы \
`Get-ConfluenceChildPage` получение дочерних страниц \
`Get-ConfluenceLabel` получение меток страницы \
`Get-ConfluencePage` получение информации о странице \
`Get-ConfluenceSpace` получение информации о пространстве \
`Invoke-ConfluenceMethod` выполнение метода Confluence \
`New-ConfluencePage` создание новой страницы \
`New-ConfluenceSpace` создание нового пространства \
`Remove-ConfluenceAttachment` удаление вложения страницы \
`Remove-ConfluenceLabel` удаление метки со страницы \
`Remove-ConfluencePage` удаление страницы \
`Remove-ConfluenceSpace` удаление пространства \
`Set-ConfluenceAttachment` установка вложения страницы \
`Set-ConfluenceInfo` установка информации о странице \
`Set-ConfluenceLabel` установка метки страницы \
`Set-ConfluencePage` установка страницы

# Zabbix

## Zabbix Agent

**Zabbix Agent Deploy:**
```PowerShell
$url = "https://cdn.zabbix.com/zabbix/binaries/stable/6.4/6.4.5/zabbix_agent2-6.4.5-windows-amd64-static.zip"
$path = "$home\Downloads\zabbix-agent2-6.4.5.zip"
$WebClient = New-Object System.Net.WebClient
$WebClient.DownloadFile($url, $path) # скачать файл
Expand-Archive $path -DestinationPath "C:\zabbix-agent2-6.4.5\" # разархивировать
Remove-Item $path # удалить архив
New-NetFirewallRule -DisplayName "Zabbix-Agent" -Profile Any -Direction Inbound -Action Allow -Protocol TCP -LocalPort 10050,10051 # открыть порты в FW

$Zabbix_Server = "192.168.3.102"
$conf = "C:\zabbix-agent2-6.4.5\conf\zabbix_agent2.conf"
$cat = cat $conf
$rep = $cat -replace "Server=.+","Server=$Zabbix_Server"
$rep | Select-String Server=
$rep > $conf

$exe = "C:\zabbix-agent2-6.4.5\bin\zabbix_agent2.exe"
.$exe --config $conf --install # установить службу
Get-Service *Zabbix*Agent* | Start-Service # запустить службу
#.$exe --config $conf --uninstall # удалить службу
```
**zabbix_agent2.conf**
```conf
# Агент может работать в пассивном (сервер забирает сам информацию) и активном режиме (агент сам отправляет):
Server=192.168.3.102
ServerActive=192.168.3.102
# Требуется указать hostname для ServerActive:
Hostname=huawei-book-01
# Если не указано, используется для генерации имени хоста (игнорируется, если имя хоста определено):
# HostnameItem=system.hostname
# Как часто обновляется список активных проверок, в секундах (Range: 60-3600):
RefreshActiveChecks=120
# IP-адрес источника для исходящих соединений:
# SourceIP=
# Агент будет слушать на этом порту соединения с сервером (Range: 1024-32767):
# ListenPort=10050
# Список IP-адресов, которые агент должен прослушивать через запятую
# ListenIP=0.0.0.0
# Агент будет прослушивать этот порт для запросов статуса HTTP (Range: 1024-32767):
# StatusPort=
ControlSocket=\\.\pipe\agent.sock
# Куда вести журнал (file/syslog/console):
LogType=file
LogFile=C:\zabbix-agent2-6.4.5\zabbix_agent2.log
# Размер лога от 0-1024 MB (0 - отключить автоматическую ротацию логов)
LogFileSize=100
# Уровень логирования. 4 - для отладки (выдает много информации)
DebugLevel=4
```
## Zabbix Sender

Используется для отправки данных на сервер

Создать host - задать произвольное имя (powershell-host) и добавить в группу на сервере

Создать Items вручную:

`Name`: Service Count \
`Type`: Zabbix trapper \
`Key`: service.count \
`Type of Information`: Numeric
```PowerShell
$path = "C:\zabbix-agent2-6.4.5\bin"
$scount = (Get-Service).Count
.$path\zabbix_sender.exe -z 192.168.3.102 -s "powershell-host" -k service.count -o $scount # отправить данные на сервер
```
## Zabbix Get

Используется для получения данных с агента (как их запрашивает сервер)

`apt install zabbix-get` \
`nano /etc/zabbix/zabbix_agentd.conf` \
`Server=127.0.0.1,192.168.3.102,192.168.3.99` добавить сервера для получения данных через zabbix_get с агента (как их запрашивает сервер)

`.$path\zabbix_get -s 192.168.3.101 -p 10050 -k agent.version` проверить версию агента \
`.$path\zabbix_get -s 192.168.3.101 -p 10050 -k agent.ping` 1 - ok \
`.$path\zabbix_get -s 192.168.3.101 -p 10050 -k net.if.discovery` список сетевых интерфейсов \
`.$path\zabbix_get -s 192.168.3.101 -p 10050 -k net.if.in["ens33"]` \
`.$path\zabbix_get -s 192.168.3.101 -p 10050 -k net.if.out["ens33"]`

## UserParameter

Пользовательские параметры

`UserParameter=process.count,powershell -Command "(Get-Process).Count"` \
`UserParameter=process.vm[*],powershell -Command "(Get-Process $1).ws"`

Получение данных:

`C:\zabbix-agent2-6.4.5\bin\zabbix_get.exe -s 127.0.0.1 -p 10050 -k process.count` \
`C:\zabbix-agent2-6.4.5\bin\zabbix_get.exe -s 127.0.0.1 -p 10050 -k process.vm[zabbix_agent2] `\
`C:\zabbix-agent2-6.4.5\bin\zabbix_get.exe -s 127.0.0.1 -p 10050 -k process.vm[powershell]`

Создать новые Items на сервере:

key: `process.count` \
key: `process.vm[zabbix_agent2]`

## Include Plugins

- Добавить параметр Include для включения конфигурационных файлов подключаемых плагинов

`'Include=.\zabbix_agent2.d\plugins.d\*.conf' >> C:\zabbix-agent2-6.4.5\conf\zabbix_agent2.conf`

- Создать конфигурационный файл с пользовательскими параметрами в каталоге, путь к которому указан в zabbix_agentd.conf

`'UserParameter=Get-Query-Param[*],powershell.exe -noprofile -executionpolicy bypass -File C:\zabbix-agent2-6.4.5\conf\zabbix_agent2.d\scripts\User-Sessions\Get-Query-Param.ps1 $1' > C:\zabbix-agent2-6.4.5\conf\zabbix_agent2.d\plugins.d\User-Sessions.conf`

- Поместить скрипт Get-Query-Param.ps1 в каталог, путь к которому указан в User-Sessions.conf. Скрипт содержим пользовательские параметры, которые он принимает от Zabbix сервера для передачи их в функции скрипта.
```PowerShell
Param([string]$select)
if ($select -eq "ACTIVEUSER") {
    (Get-Query | where status -match "Active").User
}
if ($select -eq "INACTIVEUSER") {
    (Get-Query | where status -match "Disconnect").User
}
if ($select -eq "ACTIVECOUNT") {
    (Get-Query | where status -match "Active").Status.Count
}
if ($select -eq "INACTIVECOUNT") {
    (Get-Query | where status -match "Disconnect").Status.Count
}
```
- Проверить работу скрипта:

`$path = "C:\zabbix-agent2-6.4.5\conf\zabbix_agent2.d\scripts\User-Sessions"` \
`.$path\Get-Query-Param.ps1 ACTIVEUSER` \
`.$path\Get-Query-Param.ps1 INACTIVEUSER` \
`.$path\Get-Query-Param.ps1 ACTIVECOUNT` \
`.$path\Get-Query-Param.ps1 INACTIVECOUNT`

- Создать Items с ключами:

`Get-Query-Param[ACTIVEUSER]` Type: Text \
`Get-Query-Param[INACTIVEUSER]` Type: Text \
`Get-Query-Param[ACTIVECOUNT]` Type: Int \
`Get-Query-Param[INACTIVECOUNT]` Type: Int

- Макросы:

`{$ACTIVEMAX} = 16` \
`{$ACTIVEMIN} = 0`

- Триггеры:

`last(/Windows-User-Sessions/Get-Query-Param[ACTIVECOUNT])>{$ACTIVEMAX}` \
`min(/Windows-User-Sessions/Get-Query-Param[ACTIVECOUNT],24h)={$ACTIVEMIN}`

## Zabbix API

[Documentation](https://www.zabbix.com/documentation/current/en/manual/api/reference)

`$ip = "192.168.3.102"` \
`$url = "http://$ip/zabbix/api_jsonrpc.php"`

Получение токена доступа:
```PowerShell
$data = @{
    "jsonrpc"="2.0";
    "method"="user.login";
    "params"=@{
        "username"="Admin"; # в версии до 6.4 параметр "user"
        "password"="zabbix";
    };
    "id"=1;
}
$token = (Invoke-RestMethod -Method POST -Uri $url -Body ($data | ConvertTo-Json) -ContentType "application/json").Result
```
`$token = "2eefd25fdf1590ebcdb7978b5bcea1fff755c65b255da8cbd723181b639bb789"` сгенерировать токен в UI (http://192.168.3.102/zabbix/zabbix.php?action=token.list)

- user.get method
```PowerShell
$data = @{
    "jsonrpc"="2.0";
    "method"="user.get";
    "params"=@{
    };
    "auth"=$token;
    "id"=1;
}
$users = (Invoke-RestMethod -Method POST -Uri $url -Body ($data | ConvertTo-Json) -ContentType "application/json").Result
```
- problem.get method
```PowerShell
$data = @{
    "jsonrpc"="2.0";
    "method"="problem.get";
    "params"=@{
    };
    "auth"=$token;
    "id"=1;
}
(Invoke-RestMethod -Method POST -Uri $url -Body ($data | ConvertTo-Json) -ContentType "application/json").Result
```
- host.get method

Получить список всех хостов (имя и id)

[Endpoint host documentation](https://www.zabbix.com/documentation/current/en/manual/api/reference/host)

**host.create** — создание новых хостов \
**host.delete** — удаление хостов \
**host.get** — получить список хостов \
**host.massadd** - добавление (привязка) объектов на хосты \
**host.massremove** - удаление объектов \
**host.massupdate** - замена или обновление объектов \
**host.update** - обновление хостов
```PowerShell
$data = @{
    "jsonrpc"="2.0";
    "method"="host.get";
    "params"=@{
        "output"=@( # отфильтровать вывод
            "hostid";
            "host";
        );
    };
    "id"=2;
    "auth"=$token;
}
$hosts = (Invoke-RestMethod -Method POST -Uri $url -Body ($data | ConvertTo-Json) -ContentType "application/json").Result
$host_id = $hosts[3].hostid # забрать id хоста по индексу
```
- item.get

Получить id элементов данных по наименованию ключа для конкретного хоста
```PowerShell
$data = @{
    "jsonrpc"="2.0";
    "method"="item.get";
    "params"=@{
        "hostids"=@($host_id); # отфильтровать по хосту
    };
    "auth"=$token;
    "id"=1;
}
$items = (Invoke-RestMethod -Method POST -Uri $url -Body ($data | ConvertTo-Json) -ContentType "application/json").Result
$items_id = ($items | where key_ -match system.uptime).itemid # забрать id элемента данных
```
- history.get

Получить всю историю элемента данных по его id
```PowerShell
$data = @{
    "jsonrpc"="2.0";
    "method"="history.get";
    "params"=@{
        "hostids"=@($host_id);  # фильтрация по хосту
        "itemids"=@($items_id); # фильтрация по элементу данных
    };
    "auth"=$token;
    "id"=1;
}
$items_data_uptime = (Invoke-RestMethod -Method POST -Uri $url -Body ($data | ConvertTo-Json) -ContentType "application/json").Result # получить все данные по ключу у конкретного хоста
```
Ковенртация секунд в `TimeSpan`:

`$sec = $items_data_uptime.value`
```PowerShell
function ConvertSecondsTo-TimeSpan {
    param (
        $insec
    )
    $TimeSpan = [TimeSpan]::fromseconds($insec)
    "{0:dd' day 'hh\:mm\:ss}" -f $TimeSpan
}
```
`$UpTime = ConvertSecondsTo-TimeSpan $sec[-1]`

Конвертация из времени `Unix`:

`$time = $items_data_uptime.clock`
```PowerShell
function ConvertFrom-UnixTime {
    param (
        $intime
    )
    $EpochTime = [DateTime]"1/1/1970"
    $TimeZone = Get-TimeZone
    $UTCTime = $EpochTime.AddSeconds($intime)
    $UTCTime.AddMinutes($TimeZone.BaseUtcOffset.TotalMinutes)
}
```
`$GetDataTime = ConvertFrom-UnixTime $time[-1]`

`($hosts | where hostid -eq $host_id).host` получить имя хоста \
`$UpTime` последнее полученное значение времени работы хоста \
`$GetDataTime` время последнего полученного значения

# Load Testing

### Apache Benchmark
```PowerShell
$path = "$HOME\Downloads\apache"
New-Item $path -Type Directory
cd $path
curl -L -o apache.zip "https://www.apachelounge.com/download/VS17/binaries/httpd-2.4.63-250207-win64-VS17.zip"
Expand-Archive -Path apache.zip
Copy-Item .\Apache24\bin\ $HOME\Documents\apache\ -Recurse
cd .. && Remove-Item "$HOME\Downloads\apache" -Recurse
```
`$ab = "$HOME\Documents\apache\ab.exe"` \
`. $ab -n 10000 -c 100 http://192.168.3.100:8444/api/provider/list`
```
Количество одновременных запросов:  100
Время проведения тестов:            52,402 секунды
Выполненные запросы:                10000
Неудачные запросы:                  0
Передано всего:                     6830000 байтов
Передано HTML:                      3290000 байт
RPS (Requests Per Second):          190,83 [#/sec] (среднее)
Время одного запроса:               524.017 [MS] (среднее)
Время одного запроса:               5.240 [MS] (среднее, во всех одновременных запросах)
Скорость передачи:                  127,28 [Kbytes/Sec]
```
### Locust

[Locust](https://github.com/locustio/locust) - это инструмент нагрузочного тестирования для `HTTP` и других протоколов на `Python`.

`pip3 install locust`
```Python
echo '
import os
from locust import HttpUser, task, between
class TorApiUser(HttpUser):
    # Каждый виртуальный пользователь будет ждать от 2 до 5 секунд перед выполнением следующего @task
    wait_time = between(2, 5)
    # Определяем заголовки запросов
    headers = {
        "User-Agent": "Locust"
    }
    # Получаем параметры из переменных окружения или использовать значение по умолчанию
    QUERY = os.getenv("QUERY", "test")
    # GET запросы (вес приоритета задачи для частоты ее выполнения, чем выше, тем чаще выполнение)
    @task(1)
    def test_status(self):
        self.client.get("/api/provider/list", headers=self.headers)
    @task(2)
    def test_search(self):
        # Словарь параметров, который автоматически конвертируется в строку запроса (?key=value&key2=value2)
        searchParams = {
            "query": {self.QUERY},
            "category": 0,
            "page": 0
        }
        self.client.get("/api/search/title/rutracker", headers=self.headers, params=searchParams)
    # POST запрос с телом запроса
    # @task(3)
    # def test_post_auth(self):
    #     self.client.post("/api/auth", json={"username": "admin", "password": "password"})
' > locustfile.py
```
`locust -f locustfile.py --host http://192.168.3.100:8444` \
`$env:QUERY = "The+Rookie"` определяем переменную окружения для параметра запросов \
`locust -f locustfile.py --host http://192.168.3.100:8444 -u 10 -r 2 -t 30s` количество виртуальных пользователей (VU), частота появления новых пользователей в секунду (10 пользователей будут созданы за 5 секунд) и длительность 30 секунд \
`locust -f locustfile.py --host http://192.168.3.100:8444 -u 10 -r 2 -t 30s --headless --csv locustresult` запуск без веб-интерфейса с выгрузкой результатов в csv файлы

Запуск Web-интерфейса в контейнере Docker:

`mkdir locust && cd locust`
```dockerfile
FROM alpine:latest
RUN apk add --no-cache python3 py3-pip gcc musl-dev linux-headers python3-dev
RUN python3 -m venv /venv
RUN /venv/bin/pip install --no-cache-dir locust
ENV PATH="/venv/bin:$PATH"
COPY locustfile.py .
EXPOSE 8089
CMD ["locust", "-f", "/locustfile.py"]
```
`sudo docker build -t locust-alpine-web . && sudo docker run -d --name locust -p 8089:8089 --restart=unless-stopped locust-alpine-web`

# Graylog

[Graylog Docker Image](https://hub.docker.com/r/itzg/graylog)

- Установка MongoDB:
```bash
docker run --name mongo -d mongo:3
```
- Используем прокси для установки Elassticsearch:
```bash
docker run --name elasticsearch \
    -e "http.host=0.0.0.0" -e "xpack.security.enabled=false" \
    -d dockerhub.timeweb.cloud/library/elasticsearch:5.5.1
```
- Указать статический IP адрес для подключения к API
```bash
docker run --name Graylog \
    --link mongo \
    --link elasticsearch \
    -p 9000:9000 -p 12201:12201 -p 514:514 -p 5044:5044 \
    -e GRAYLOG_WEB_ENDPOINT_URI="http://192.168.3.101:9000/api" \
    -d graylog/graylog:2.3.2-1
```
- Настройка syslog на клиенте Linux:

`nano /etc/rsyslog.d/graylog.conf`
```bash
*.* @@192.168.3.101:514;RSYSLOG_SyslogProtocol23Format
```
`systemctl restart rsyslog`

- Создать входящий поток (inputs) для Syslog на порту 514 по протоколу TCP:

http://192.168.3.101:9000/system/inputs

- Фильтр для логов Kinozal-Bot:

`facility:"system daemon" AND application_name:bash AND message:\[ AND message:\]`

- Настройка Winlogbeat на клиенте Windows

Установка агента:
```PowerShell
irm https://artifacts.elastic.co/downloads/beats/winlogbeat/winlogbeat-8.15.0-windows-x86_64.zip -OutFile $home\Documents\winlogbeat-8.15.0.zip
Expand-Archive $home\Documents\winlogbeat-8.15.0.zip
cd $home\Documents\winlogbeat-8.15.0-windows-x86_64
```
Добавить отправку в Logstash:

`code winlogbeat.yml`
```bash
output.logstash:
  hosts: ["192.168.3.101:5044"]
```
И закомментировать отправку данных в Elasticsearch (output.elasticsearch)

`.\winlogbeat.exe -c winlogbeat.yml` запустить агент с правами администратора в консоли
```bash
.\install-service-winlogbeat.ps1 # установить службу
Get-Service winlogbeat | Start-Service
```
- Настроить Inputs для приема Beats на порту 5044
