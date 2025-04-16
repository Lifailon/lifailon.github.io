+++
title = "DevOps"
[extra]
toc = true
toc_sidebar = true
go_to_top = true
+++

Заметки по инструментам и системам направления `DevOps`.

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
`git pull` загрузить изменения из удаленного хранилища для обновления локального репозитория (выполняет `git fetch`, чтобы получить последние изменения из удаленного репозитория, а затеим объеденяем изменения с локальной копией с помощью `git merge` для обновления текущей рабочей ветки) \
`git stash` сохраняет текущие незакоммиченные изменения в временное хранилище (например, на время выполнения `git pull`), в т.ч. неотслеживаемые файлы и очищает рабочую директорию (вернет в состояние, соответствующее последнему коммиту) \
`git stash pop` применяет последние изменения из стэша к текущей ветке (вернутся только измененные строки в файлах, при этом будут сохранены новые добавленные строки в файле без конфликтов) и удаляет их из стэша \
`git stash apply` применяет изменения, но не удаляет их из стэша \
`git status` отобразить статус изменений по файлам \
`git diff` отобразить историю изменений построчно \
`git diff pandoc` сравнивает изменения в текущей рабочей директории с последним коммитом в указанной ветке `pandoc` \
`git add .` добавить (проиндексировать) изменения во всех файлах текущего каталога \
`git commit -m "update powershell commands"` сохранить изменения с комментарием \
`git push` синхронизировать локальные изменения с репозиторием на сервере \
`git push origin mkdocs-material` отправить в конкретную ветку \
`git push origin --delete mkdocs` удалить ветку на удаленном сервере \
`git commit --amend` изменить комментарий в последнем коммите (до `push`) \
`git commit --amend --no-edit --date="Sun Oct 27 23:20:00 2024 +0300"` изменить дату последнего коммита \
`git branch -a` отобразить все ветки (в том числе удаленные remotes/origin) \
`git branch hugo` создать новую ветку \
`git branch -m hugo-public` переименовать текущую ветку \
`git branch -d hugo-public` удалить ветку \
`git switch hugo` переключиться на другую ветку \
`git push origin hugo` отправить изменения в указанную ветку \
`git branch --set-upstream-to=origin/hugo hugo` локальная ветка `hugo` будет отслеживать удаленную ветку `hugo` на удаленном сервере-репозитории `origin` (позволяет не указывать название удаленной ветки при каждом использовании команд `git push` или `git pull`) \
`git switch pandoc` переключиться на другую ветку \
`git merge hugo` слияние указанной ветки (`hugo`) в текущую ветку (`pandoc`)  \
`git log --oneline --all` отобразить список всех коммитов и их сообщений \
`git log --graph` коммиты и следование веток \
`git log --author="Lifailon"` показывает историю коммитов указанного пользователя \
`git blame .\posh.md` показывает, кто и когда внес изменения в каждую строку указанного файла (`НОМЕР_КОММИТА (ИМЯ_ПОЛЬЗОВАТЕЛЯ ДАТА НОМЕР_СТРОКИ) ТЕКСТ.`) \
`git show d01f09dead3a6a8d75dda848162831c58ca0ee13` отобразить подробный лог по номеру коммита \
`git checkout filename` устаревшая команда, откатить не проиндексированные изменения для коммита, возвращая его к состоянию, каким оно было на момент последнего коммита (если не было индексации через `add`) \
`git restore filename` отменить все локальные изменения в рабочей копии независимо от того, были они проиндексированы или нет (через `add`), возвращая его к состоянию на момент последнего коммита \
`git restore --source d01f09dead3a6a8d75dda848162831c58ca0ee13 filename` восстановить файл на указанную версию по хэшу индентификатора коммита \
`git reset HEAD filename` удалить указанный файл из индекса без удаления самих изменений в файле для последующей повторной индексации (если был `add` но не было `commit`, потом выполнить `checkout`) \
`git reset --soft HEAD^` отменяет последний (^) коммит, сохраняя изменения из этого коммита в рабочем каталоге и индексе (подготовленной области), можно внести изменения в файлы и повторно их зафиксировать через `commit` \
`git reset --hard HEAD^` полностью отменяет последний коммит, удаляя все его изменения из рабочего каталога и индекса до состояния предыдущего перед последним коммитом (аналогично `HEAD~1`) \
`git push origin main --force` удалить последний коммит на удаленном сервере репозитория после `reset --hard HEAD^`  \
`git reset --hard d01f09dead3a6a8d75dda848162831c58ca0ee13` откатывает изменения к указанному коммиту и удаляет все коммиты, которые были сделаны после него (будут потеряны все незакоммиченные изменения и историю коммитов после указанного) \
`git revert HEAD --no-edit` создает новый коммит, который отменяет последний коммит (`HEAD^`) и новый коммит будет добавлен поверх него (события записываются в `git log`) \
`git revert d01f09dead3a6a8d75dda848162831c58ca0ee13` создает новый коммит, который отменяет изменения, внесенные в указанный коммит с хешем (не изменяет историю коммитов, а создает новый коммит с изменениями отмены)

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

### act

[act](https://github.com/nektos/act) - пользволяет запускать действия GitHub Actions локально.
```bash
version=$(curl -s https://api.github.com/repos/nektos/act/releases/latest | jq -r .tag_name)
curl -L "https://github.com/nektos/act/releases/download/$version/act_$(uname -s)_$(uname -m).tar.gz" -o $HOME/.local/bin/act.tar.gz
tar -xzf $HOME/.local/bin/act.tar.gz -C $HOME/.local/bin
chmod +x $HOME/.local/bin/act
rm $HOME/.local/bin/act.tar.gz
act --version
```
`act --list` список доступных действий, указаных в файлах .github/workflows \
`act -j build` запуск указанного действия по Job ID (имя файла, не путать с названием Workflow) \
`act -n -j build` пробный запуск (--dry-run), без выполнения команд, для отображения всех выполняемых jobs и steps
```json
{
  "inputs": {
    "Distro": "ubuntu-24.04",
    "Update": "false",
    "Linter": "true",
    "Test": "false",
    "Release": "false",
    "Binary": "true"
  }
}
```
`act -e event.json -W .github/workflows/build.yml -P ubuntu-24.04=catthehacker/ubuntu:act-latest` запустить указанный файл workflow с переданным файлом переменных (предварительно определенных параметров) и указанным сборщиком \
`act -e event.json -W .github/workflows/build.yml -P ubuntu-24.04=catthehacker/ubuntu:act-latest --artifact-server-path $PWD/artifacts` примонтировать рабочий каталог в контейнер для сохранения артефактов
```bash
echo "DOCKER_HUB_USERNAME=username" >> .secrets
echo "DOCKER_HUB_PASSWORD=password" >> .secrets
```
`act --secret-file .secrets` \
`act -s DOCKER_HUB_USERNAME=username -s DOCKER_HUB_PASSWORD=password` передать содержимое секретов \
`act push` симуляция push-ивента (имитация коммита и запуск workflow, который реагирует на push) \
`act --reuse` не удалять контейнер из успешно завершенных рабочих процессов для сохранения состояния между запусками (кэширование) \
`act --parallel` запуск всех jobs одновременно или последовательно (--no-parallel, по умолчанию)

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

# Jenkins

Примеры `Pipeline` и базовый синтаксис `Groovy`.

`docker run -d --name=jenkins -p 8080:8080 -p 50000:50000 --restart=unless-stopped -v jenkins_home:/var/jenkins_home jenkins/jenkins:latest` \
`ls /var/lib/docker/volumes/jenkins_home/_data/jobs` директория хранящая историю сборок в хостовой системе \
`docker exec -it jenkins /bin/bash` подключиться к контейнеру \
`cat /var/jenkins_home/secrets/initialAdminPassword` получить токен инициализации
```
docker run -d \
  --name jenkins-remote-agent-01 \
  --restart unless-stopped \
  -e JENKINS_URL=http://192.168.3.101:8080 \
  -e JENKINS_AGENT_NAME=remote-agent-01 \
  -e JENKINS_SECRET=3ad54fc9f914957da8205f8b4e88ff8df20d54751545f34f22f0e28c64b1fb29 \
  -v jenkins_agent:/home/jenkins \
  jenkins/inbound-agent:latest

# Или ссылаться на локальный контейнер сервера по имени
# --link jenkins:jenkins
# -e JENKINS_URL=http://jenkins:8080
```
`docker exec -u root -it jenkins-remote-agent-01 /bin/bash` подключиться к slave агенту под root \
`apt-get update && apt-get install -y iputils-ping netcat-openbsd` установить ping и nc на машину сборщика (slave)

`jenkinsVolumePath=$(docker inspect jenkins | jq -r .[].Mounts.[].Source)` получить путь к директории Jenkins в хостовой системе \
`sudo tar -czf $HOME/jenkins-backup.tar.gz -C $jenkinsVolumePath .` резервная копия всех файлов \
`(crontab -l ; echo "0 23 * * * sudo tar -czf /home/lifailon/jenkins-backup.tar.gz -C /var/lib/docker/volumes/jenkins_home/_data .") | crontab -` \
`sudo tar -xzf $HOME/jenkins-backup.tar.gz -C /var/lib/docker/volumes/jenkins_home/_data` восстановление

`wget http://127.0.0.1:8080/jnlpJars/jenkins-cli.jar -P $HOME/` скачать jenkins-cli (http://127.0.0.1:8080/manage/cli) \
`apt install openjdk-17-jre-headless` установить java runtime \
`java -jar jenkins-cli.jar -auth lifailon:password -s http://127.0.0.1:8080 -webSocket help` получить список команд \
`java -jar jenkins-cli.jar -auth lifailon:password -s http://127.0.0.1:8080 groovysh` запустить консоль Groovy \
`java -jar jenkins-cli.jar -auth lifailon:password -s http://127.0.0.1:8080 install-plugin ssh-steps -deploy` устанавливаем плагин SSH Pipeline Steps

## API
```PowerShell
$username = "Lifailon"
$password = "password"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username,$password)))
$headers = @{Authorization=("Basic {0}" -f $base64AuthInfo)}
Invoke-RestMethod "http://192.168.3.101:8080/rssAll" -Headers $headers # RSS лента всех сборок и их статус в title
Invoke-RestMethod "http://192.168.3.101:8080/rssFailed" -Headers $headers # RSS лента всех неудачных сборок
$(Invoke-RestMethod "http://192.168.3.101:8080/computer/local-agent/api/json" -Headers $headers).offline # проверить статус работы slave агента

$jobs = Invoke-RestMethod "http://192.168.3.101:8080/api/json/job" -Headers $headers
$jobs.jobs.name # список всех проектов
$jobName = "Update SSH authorized_keys"
$job = Invoke-RestMethod "http://192.168.3.101:8080/job/${jobName}/api/json" -Headers $headers
$job.builds # список всех сборок
$buildNumber = $job.lastUnsuccessfulBuild.number # последняя неуспешная сборка
Invoke-RestMethod "http://192.168.3.101:8080/job/${jobName}/${buildNumber}/consoleText" -Headers $headers # вывести лог указанной сборки

$lastCompletedBuild = $job.lastCompletedBuild.number # последняя успешная сборка
$crumb = $(Invoke-RestMethod "http://192.168.3.101:8080/crumbIssuer/api/json" -Headers $headers).crumb # получаем временный токен доступа (crumb)
$headers["Jenkins-Crumb"] = $crumb # добавляем crumb в заголовки
$body = @{".crumb" = $crumb} # добавляем crumb в тело запроса
Invoke-RestMethod "http://192.168.3.101:8080/job/${jobName}/${lastCompletedBuild}/rebuild" -Headers $headers -Method POST -Body $body # перезапустить сборку
```
## Plugins

| Плагин                                                                        | Описание                                                                                                      |
| -                                                                             | -                                                                                                             |
| [Web Monitoring](https://plugins.jenkins.io/monitoring)                       | Конечная точка `/monitoring` для отображения графиков мониторинга в веб-интерфейсе.                           |
| [Prometheus Metrics](https://plugins.jenkins.io/prometheus)                   | Предоставляет конечную точку `/prometheus` с метриками, которые используются для сбора данных.                |
| [Embeddable Build Status](https://plugins.jenkins.io/embeddable-build-status) | Предоставляет настраиваемые значки (like `shields.io`), который возвращает статус сборки.                     |
| [Job Configuration History](https://plugins.jenkins.io/jobConfigHistory)      | Сохраняет копию файла сборки в формате `xml` (который хранится на сервере) и позволяет производить сверку.    |
| [SSH Pipeline Steps](https://plugins.jenkins.io/ssh-steps)                    | Плагин для подключения к удаленным машинам через протокол ssh по ключу или паролю.                            |
| [Active Choices](https://plugins.jenkins.io/uno-choice)                       | Активные параметры, которые позволяют динамически обновлять содержимое параметров.                            |
| [File parameters](https://plugins.jenkins.io/file-parameters)                 | Поддержка параметров для загрузки файлов (перезагрузить Jenkins для использования нового параметра).          |
| [Email Extension](https://plugins.jenkins.io/email-ext)                       | Плагин для отправки на почту из pipeline.                                                                     |
| [Schedule Build](https://plugins.jenkins.io/schedule-build)                   | Позволяет запланировать сборку на указанный момент времени.                                                   |
| [Test Results Analyzer](https://plugins.jenkins.io/test-results-analyzer)     | Показывает историю результатов сборки junit тестов в табличном древовидном виде.                              |

## SSH Steps and Artifacts

Добавляем логин и `Private Key` для авторизации по ssh: `Manage (Settings)` => `Credentials` => `Global` => `Add credentials` => Kind: `SSH Username with private key`

Сценарий проверяет доступность удаленной машины, подключается к ней по ssh, выполняет скрипт [hwstat](https://github.com/Lifailon/hwstat) для сбора метрик и выгружает json отчет в артефакты:
```Groovy
// Глобальный массив для хранения данных подключения по ssh 
def remote = [:]

pipeline {
    agent any // { label 'remote-agent-01' }
    parameters {
        string(name: 'address', defaultValue: '192.168.3.101', description: 'Адрес удаленного сервера')
        // choice(name: "addresses", choices: ["192.168.3.101","192.168.3.102"], description: "Выберите сервер из выпадающего списка")
        string(name: 'port', defaultValue: '22', description: 'Порт ssh')
        string(name: 'credentials', defaultValue: 'd5da50fc-5a98-44c4-8c55-d009081a861a', description: 'Идентификатор учетных данных из Jenkins')
        booleanParam(name: "root", defaultValue: false, description: 'Запуск с повышенными привилегиями')
        booleanParam(name: "report", defaultValue: true, description: 'Выгружать отчет в формате json')
    }
    triggers {
        cron('H */6 * * 1-5') // выполнять запуск каждын 6 часов с понедельника по пятницу
    }
    options {
        timeout(time: 5, unit: 'MINUTES') // период ожидания, после которого нужно прервать Pipeline
        retry(2) // в случае неудачи повторить весь Pipeline указанное количество раз
    }
    environment {
        // Переменная окружения для хранения пути временного файла с содержимым приватного ключа
        SSH_KEY_FILE = "/tmp/ssh_key_${UUID.randomUUID().toString()}"
    }
    stages {
        stage('Проверка доступности хоста (icmp и tcp)') {
            steps {
                script {
                    def check = sh(
                        script: """
                            ping -c 1 ${params.address} > /dev/null || exit 1
                            nc -z ${params.address} ${params.port} || exit 2
                        """,
                        returnStatus: true // исключить завершение Pipeline с ошибкой
                    )
                    if (check == 1) {
                        error("Сервер ${params.address} недоступен (icmp ping)")
                    } else if (check == 2) {
                        error("Порт ${params.address} закрыт (tcp check)")
                    } else {
                        echo "Сервер ${params.address} доступен и порт ${params.port} открыт"
                    }
                }
            }
        }
        stage('Извлечение данных для авторизации по ключу') {
            steps {
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: params.credentials, usernameVariable: 'SSH_USER', keyFileVariable: 'SSH_KEY', passphraseVariable: '')]) {
                        // Записываем содержимое приватного ключа во временный файл
                        writeFile(file: env.SSH_KEY_FILE, text: readFile(SSH_KEY))
                        sh "chmod 600 ${env.SSH_KEY_FILE}"
                        remote.name = params.address
                        remote.host = params.address
                        remote.port = params.port.toInteger()
                        remote.user = SSH_USER
                        remote.identityFile = env.SSH_KEY_FILE
                        remote.allowAnyHosts = true
                    }
                }
            }
        }
        stage('Запуск скрипта через через ssh') {
            steps {
                script {
                    def runCommand
                    if (params.root) {
                        runCommand = """
                            curl -sS https://raw.githubusercontent.com/Lifailon/hwstat/rsa/hwstat.sh | sudo bash -s -- "json" > "hwstat-report.json"
                        """
                    } else {
                        runCommand = """
                            curl -sS https://raw.githubusercontent.com/Lifailon/hwstat/rsa/hwstat.sh | bash -s -- "json" > "hwstat-report.json"
                        """
                    }
                    def jsonOutput = sshCommand remote: remote, command: runCommand
                    if (params.report) {
                        // Записать содержимое переменной в файл
                        // writeFile file: 'hwstat-report.json', text: jsonOutput
                        // Загрузить файл из удаленной машины
                        sshGet remote: remote, from: "hwstat-report.json", into: "${env.WORKSPACE}/hwstat-report.json", override: true
                    }
                    sshCommand remote: remote, command: "rm hwstat-report.json"
                }
            }
        }
        stage('Загрузка json отчета в Jenkins') {
            // Проверка условия перед выполнением шага (пропуск если false)
            when {
                expression { params.report }
            }
            steps {
                archiveArtifacts artifacts: 'hwstat-report.json', allowEmptyArchive: true
            }
        }
    }
    post {
        // Выполнять независимо от успеха или ошибки
        always {
            script {
                sh "rm -f ${env.SSH_KEY_FILE}"
            }
        }
        success   { echo "Сборка завершена успешно" }
        failure   { echo "Сборка завершилась с ошибкой" }
        unstable  { echo "Сборка завершилась с предупреждениями" }
        changed   { echo "Текущий статус завершения изменился по сравнению с предыдущим запуском" }
        fixed     { echo "Сборка завершена успешно по сравнению с предыдущим запуском" }
        aborted   { echo "Запуск был прерван" }
    }
}
```
## Update SSH authorized_keys

Добавляем логин и пароль для авторизации по ssh: `Manage (Settings)` => `Credentials` => `Global` => `Add credentials` => Kind: `Username with password`

Сценарий обновляет параметр со списком текущих пользователей на машине и добавляет или заменяет ssh ключ для выбранного пользователя:
```Groovy
def remote = [:]

pipeline {
    agent any
    parameters {
        string(name: 'address', defaultValue: '192.168.3.101', description: 'Адрес удаленного сервера')
        string(name: 'port', defaultValue: '22', description: 'Порт ssh')
        string(name: 'credentials', defaultValue: '15d05be6-682a-472b-9c1d-cf5080e98170', description: 'Идентификатор учетных данных из Jenkins')
        booleanParam(name: "getUsers", defaultValue: true, description: 'Получить список текущих пользователей системы')
        string(name: 'sshKey', defaultValue: '', description: 'Открытый ssh ключ для добавления в authorized_keys')
        booleanParam(name: "rewriteKey", defaultValue: false, description: 'Перезаписать текущие ключи в файле authorized_keys')
    }
    stages {
        stage('Извлекаем параметры для авторизации по ssh') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: params.credentials, usernameVariable: 'SSH_USER', passwordVariable: 'SSH_PASS')]) {
                        remote.name = params.address
                        remote.host = params.address
                        remote.port = params.port.toInteger()
                        remote.user = env.SSH_USER
                        remote.password = env.SSH_PASS
                        remote.allowAnyHosts = true
                    }
                }
            }
        }
        stage('Обновить список пользователей') {
            when {
                expression { params.getUsers }
            }
            steps {
                script {
                    def mainCommand = "echo \$(ls /home)"
                    def users = sshCommand remote: remote, command: mainCommand
                    def usersList = users.trim().split("\\s")
                    usersList += 'root'
                    def usersListChoice = usersList.toList()
                    writeFile file: 'user_list.txt', text: usersList.join("\\s")
                    properties([
                        parameters([
                            string(name: 'address', defaultValue: params.address, description: 'Адрес удаленного сервера'),
                            string(name: 'port', defaultValue: params.port, description: 'Порт ssh'),
                            string(name: 'credentials', defaultValue: params.credentials, description: 'Идентификатор учетных данных из Jenkins'),
                            booleanParam(name: "getUsers", defaultValue: params.getUsers, description: 'Получить список текущих пользователей системы'),
                            string(name: 'sshKey', defaultValue: '', description: 'Открытый ssh ключ для добавления в authorized_keys'),
                            booleanParam(name: "rewriteKey", defaultValue: false, description: 'Перезаписать текущие ключи в файле authorized_keys'),
                            choice(
                                name: 'userList',
                                choices: usersListChoice,
                                description: 'Выбрать пользователя'
                            )
                        ])
                    ])
                }
            }
        }
        stage('Добавить новый SSH ключ') {
            when {
                expression { !params.getUsers && params.sshKey }
            }
            steps {
                script {
                    def selectedUser = params.userList
                    def sshKey = params.sshKey
                    if (selectedUser == "root") {
                        path = "/root/.ssh/authorized_keys"
                    } else {
                        path= "/home/${selectedUser}/.ssh/authorized_keys"
                    }
                    if (params.rewriteKey) {
                        echo "Обновляем все SSH ключи для пользователя: ${selectedUser}"
                        teeCommand = "tee"
                    } else {
                        echo "Добавляем новый SSH ключ для пользователя: ${selectedUser}"
                        teeCommand = "tee -a"
                    }
                    def mainCommand = """
                        checkFile=\$(ls $path 2> /dev/null || echo false)
                        if [ \$checkFile == "false" ]; then
                            mkdir -p \$(dirname $path) && touch $path
                        fi
                        echo $sshKey | $teeCommand $path > /dev/null
                    """
                    sshCommand remote: remote, command: mainCommand
                }
            }
        }
    }
}
```
## Upload File Parameter

Передача файла через параметр и чтение его содержимого:
```Groovy
pipeline {
    agent any
    parameters {
        base64File 'UPLOAD_FILE'
    }
    stages {
        stage('Читаем содержимое файла') {
            steps {
                // Переменная хранит содержимое файла в формате base64
                // echo UPLOAD_FILE
                // Декодируем base64
                withFileParameter('UPLOAD_FILE') {
                    sh """
                        echo "$UPLOAD_FILE" # выводим путь к временному файлу с содержимым переданного файла
                        cat "$UPLOAD_FILE"  # читаем содержимое файла
                    """
                }
            }
        }
    }
}
```
## Input Text and File

Останавливает выполнение `Pipeline` и заставляет пользователя передать текстовый параметр и файл:
```Groovy
pipeline {
    agent any
    stages {
        stage('Input text') {
            input {
                message "Что бы продолжить, передайте текст в поле ввода"
                ok "Передать"
                parameters {
                    string(name: 'TEXT', defaultValue: 'test', description: 'Введите текст')
                }
            }
            steps {
                echo "Переданный текст в параметре input: ${TEXT}"
            }
        }
        stage('Input file') {
            steps {
                script {
                    def fileBase64 = input message: "Передайте файл", parameters: [base64File('file')]
                    sh "echo $fileBase64 | base64 -d"
                }
            }
        }
    }
}
```
## HttpURLConnection

Любой код Groovy возможно запустить и проверить через `Script Console` (http://127.0.0.1:8080/manage/script)

Пример `API` запроса к репозиторию PowerShell на GitHub для получения последней версии и всех доступных версий:
```Groovy
import groovy.json.JsonSlurper
def url = new URL("https://api.github.com/repos/PowerShell/PowerShell/tags")
def connection = url.openConnection()
connection.setRequestMethod("GET")
connection.setRequestProperty("Accept", "application/json")
def responseCode = connection.getResponseCode()
if (responseCode == 200) {
    // Получаем данные ответа
    def response = connection.getInputStream().getText()
    // Парсим JSON ответ
    def jsonSlurper = new JsonSlurper()
    def tags = jsonSlurper.parseText(response)
    // Проверяем количество элементов в массиве
    if (tags.size() > 0) {
        // Забираем последний тег из списка
        def latestTag = tags[0].name
        println("Latest version: " + latestTag)
        println()
        // Проходимся по всем тегам
        println("List of all versions:")
        for (tag in tags) {
            println(tag.name)
        }
    } else {
        error("No tags found in the response")
    }
} else {
    error("Failed to call API, response code: ${responseCode}")
}
connection.disconnect()
```
## Active Choices Parameter

Пример выбора репозитория, получения списка доступных версий и содержимого файлов выбранного релиза.

- 1. Active Choices Parameter

Name: `Repos`

Groovy Script:
```Groovy
return [
    'Lifailon/lazyjournal',
    'jesseduffield/lazydocker'
]
```
- 2. Active Choices Reactive Parameter

Name: `Versions`

Groovy Script:
```Groovy
import groovy.json.JsonSlurper
def selectedRepo = Repos
def apiUrl = "https://api.github.com/repos/${selectedRepo}/tags"
def conn = new URL(apiUrl).openConnection()
conn.setRequestProperty("User-Agent", "Jenkins")
def response = conn.getInputStream().getText()
def json = new JsonSlurper().parseText(response)
def versionsCount = json.size()
def data = []
for (int i = 0; i < versionsCount; i++) {
    data += json.name[i]
}
return data
```
Настройки параметров:
Choice Type: `Single Select`
Привязать параметр `Repos` из `Active Choices` в `Reactive Parameter` через `Referenced parameters`
Включить фильтрацию через `Enable filters`

- 3. Active Choices Reactive Parameter

Name: `Files`

Groovy Script:
```Groovy
import groovy.json.JsonSlurper
def selectedRepo = Repos
def selectedVer = Versions
def apiUrl = "https://api.github.com/repos/${selectedRepo}/releases/tags/${selectedVer}"
def conn = new URL(apiUrl).openConnection()
conn.setRequestProperty("User-Agent", "Jenkins")
def response = conn.getInputStream().getText()
def json = new JsonSlurper().parseText(response)
def data = []
for (file in json.assets) {
    data += file.name
}
return data
```
Referenced parameters: `Repos,Versions`

Pipeline script:
```Groovy
pipeline {
    agent any
    stages {
        stage('Selected parameters') {
            steps {
                script {
                    echo "Selected repository: https://github.com/${params.Repos}"
                    echo "Selected version: ${params.Versions}"
                    echo "Selected file: ${params.Files}"
                    echo "Url for download: https://github.com/${params.Repos}/releases/download/${params.Versions}/${params.Files}"
                }
            }
        }
    }
}
```
## Vault

Интеграция [HashiCorp Vault](https://github.com/hashicorp/vault) в Jenkins Pipeline через `REST API` для получения содержимого секретов и использовая в последующих стадиях/этапах сборки:
```Groovy
def getVaultSecrets(
    String address,
    String path,
    String token
) {
    def url = new URL("${address}/${path}")
    
    def connection = url.openConnection()
    connection.setRequestMethod("GET")
    connection.setRequestProperty("X-Vault-Token", token)
    connection.setRequestProperty("Accept", "application/json")
    
    def response = new groovy.json.JsonSlurper().parse(connection.inputStream)
    def user = response.data.data.user
    def password = response.data.data.password
    return [
        user: user,
        password: password
    ]
}

def USER_NAME
def USER_PASS

pipeline {
    agent any
    parameters {
        string(name: 'url', defaultValue: 'http://192.168.3.101:8200', description: 'Url адресс хранилища секретов')
        string(name: 'path', defaultValue: 'v1/kv/data/ssh-auth', description: 'Путь для извлечения секретов')
        password(name: 'token', defaultValue: 'hvs.bySybhyYOxSWEVk4FQDdcyyg', description: 'Токен доступа к API HashiCorp Vault')
    }
    stages {
        stage('Get vault secrets') {
            steps {
                script {
                    def secrets = getVaultSecrets(
                        "${params.url}",
                        "${params.path}",
                        "${params.token}"
                    )
                    USER_NAME = secrets.user
                    USER_PASS = secrets.password
                }
            }
        }
        stage('Use secrets') {
            steps {
                script {
                    echo "User: ${USER_NAME}"
                    echo "Password: ${USER_PASS}"
                }
            }
        }
    }
}
```
## Email Extension

Для отправки на почту и настроить SMTP сервер в настройках Jenkins (`System` => `Extended E-mail Notification`)

SMTP server: `smtp.yandex.ru`
SMTP port: `587`
Credentials: `Username with password` (`username@yandex.ru` и `app-password`)
`Use TLS`
Default Content Type: `HTML (text/html)`

Настройка логирования в System Log: `emailDebug` + фильтр `hudson.plugins.emailext` и уровень `ALL`
```Groovy
pipeline {
    agent any
    parameters {
        string(name: 'emailTo', defaultValue: 'test@yandex.ru', description: 'Почтовый адрес назначения')
    }
    stages {
        stage('Вывод всех переменных окружения') {
            steps {
                script {
                    env.getEnvironment().each { key, value ->
                        echo "${key} = ${value}"
                    }
                }
            }
        }
        stage('Отправка на почту') {
            options {
                timeout(time: 1, unit: 'MINUTES')
            }
            steps {
                script {
                    emailext (
                        to:	"${params.emailTo}",
                        subject: "${env.JOB_NAME} - ${BUILD_NUMBER}",
                        mimeType: "text/html",
                        body: """
                            <html>
                                <body>
                                    <div style="padding-left: 30px; padding-bottom: 15px;" color="blue">
                                    <font name="Arial" color="#906090" size="3" font-weight="normal">
                                        <b> ${env.JOB_NAME} - ${env.BUILD_NUMBER} </b>
                                    </font>
                                    <br>
                                    <div style="padding-left: 30px; padding-bottom: 15px;" color="black">
                                    <br>
                                    <font name="Arial" color="black" size="2" font-weight="normal"> 
                                        <pre> Build url: ${env.BUILD_URL} </pre>
                                    </font>	
                                </body>
                            </html>
                        """
                    )
                }
            }
        }
    }
}
```
## Parallel
```Groovy
pipeline {
    agent any
    stages {
        stage('Parallel sleeps') {
            parallel {
                stage('Task 1') {
                    steps {
                        script {
                            def currentTime = new Date().format('yyyy-MM-dd HH:mm:ss')
                            echo "[${currentTime}] Начало задачи 1"
                            sh 'sleep 10'
                            currentTime = new Date().format('yyyy-MM-dd HH:mm:ss')
                            echo "[${currentTime}] Завершение задачи 1"
                        }
                    }
                }
                stage('Task 2') {
                    steps {
                        script {
                            def currentTime = new Date().format('yyyy-MM-dd HH:mm:ss')
                            echo "[${currentTime}] Начало задачи 2"
                            sh 'sleep 5'
                            currentTime = new Date().format('yyyy-MM-dd HH:mm:ss')
                            echo "[${currentTime}] Завершение задачи 2"
                        }
                    }
                }
            }
        }
        stage('Parallel tasks via loop') {
            steps {
                script {
                    // Массив, где ключи содержит имя задачи, а значение содержит блоки кода для выполнения
                    def tasks = [:]
                    def taskNames = ['Task 1', 'Task 2', 'Task 3']
                    taskNames.each { taskName ->
                        tasks[taskName] = {
                            def currentTime = new Date().format('yyyy-MM-dd HH:mm:ss')
                            echo "[${currentTime}] Начало задачи: $taskName"
                            sh "sleep ${taskName == 'Task 1' ? 10 : taskName == 'Task 2' ? 5 : 3}"
                            currentTime = new Date().format('yyyy-MM-dd HH:mm:ss')
                            echo "[${currentTime}] Завершение задачи: $taskName"
                        }
                    }
                    parallel tasks
                }
            }
        }
    }
}
```
## Groovy

Базовый синтаксис языка `Groovy`
```Groovy
// Переменные
javaString = 'java'
javaString
println javaString
javaString.class    // class java.lang.String
println 100.class   // class java.lang.Integer
j = '${javaString}' // не принимает переменные в одинарных кавычках
groovyString = "${javaString}"
bigGroovyString = """
    ${javaString}
    ${j}
    ${groovyString}
    ${2 + 2}
"""

// java
// ${javaString}
// java
// 4

a = "a"   // a
a + "123" // a123
a * 5     // aaaaa

// Массивы и списки
list =[1,2,3]
list[0]    // 1
list[0..1] // [1, 2]
range = "0123456789"
range[1..5] // 12345
map = [key1: true, key2: false]
map["key1"] // true
server = [:]
server.ip = "192.168.3.1"
server.port = 22
println(server) // [ip:192.168.3.1, port:22]

// Функции
def sum(a,b) {
    println a+b
}
sum(2,2) // 4

// Условия
def diff(x) {
    if (x < 10) {
        println("${x} < 10")
    } else if (x == 10) {
        println("${x} = 10")
    } else {
        println("${x} > 10")
    }
}
diff(11) // 11 > 10

// Циклы
list.each { l ->
    print l
}
// 123

for (i in 0..5) { 
    print i
}
// 012345

for (int i = 0; i < 10; i++) {
    print i
}
// 0123456789

i = 0
while (i < 3) {
    println(i)
    i++
}
// 0
// 1
// 2
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

## HashiCorp/Vault

`mkdir vault && cd vault && mkdir vault_config`

Создать конфигурацию:
```bash
echo '
# Использовать локальное файловое хранилище
storage "file" {
  path = "/vault/file"
}
# Отключение режим dev (не будет выгружать данные в память)
disable_mlock = false
# Настройка слушателя для REST API
listener "tcp" {
  address = "0.0.0.0:8200"
  tls_disable = 1  # Отключить TLS
}
# Включение интерфейс
ui = true
# Включение аутентификации в API по токену
api_addr = "http://localhost:8200"
auth "token" {}
' > vault_config/vault.hcl
```
Запускаем в контейнере:
```bash
docker run -d --name=vault \
  --restart=unless-stopped \
  -e VAULT_ADDR=http://0.0.0.0:8200 \
  -e VAULT_API_ADDR=http://localhost:8200 \
  -p 8200:8200 \
  -v ./vault_config:/vault/config \
  -v ./vault_data:/vault/file \
  --cap-add=IPC_LOCK \
  hashicorp/vault:latest \
  vault server -config=/vault/config/vault.hcl
```
Получить ключи разблокировки и root ключ для первичной инициализации:
```bash
docker exec -it vault vault operator init
```
Ввести любые 3 из 5 ключей для разблокировки после перезапуска контейнера:
```bash
docker exec -it vault vault operator unseal BPJSmuLvKAEr6wtE/8TOMRMM+x0fW3UhOxGFLn9Gmi5N
docker exec -it vault vault operator unseal 44ntLYvSMN5FNLyddLo2IylRsLk7lqYXZOShvhV/2gbG
docker exec -it vault vault operator unseal xP9+YTyW13W6xGz52mMut2MdOnzxtbhDW8dK9zdF4aLY
```
Проверить статус (должно быть `Sealed: false`) и авторизацию по root ключу в хранилище:
```bash
docker exec -it vault vault status
docker exec -it vault vault login hvs.rxlYkJujkX6Fdxq2XAP3cd3a
```
`Secrets Engines` -> `Enable new engine` + `KV` \
API Swagger: http://192.168.3.100:8200/ui/vault/tools/api-explorer
```PowerShell
$TOKEN = "hvs.rxlYkJujkX6Fdxq2XAP3cd3a"
$Headers = @{
    "X-Vault-Token" = $TOKEN
}
# Указать путь до секретов (создается в корне kv)
$path = "main-path"
$url = "http://192.168.3.101:8200/v1/kv/data/$path"
$data = Invoke-RestMethod -Uri $url -Method GET -Headers $Headers
# Получить содержимое ключа по его названию (key_name)
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
## HashiCorp/Consul

[Consul](https://github.com/hashicorp/consul) используется для кластеризации и централизованного хранения данных `Vault`, а также как самостоятельное `Key-Value` хранилище.

Создать конфигурацию:
```bash
echo '
ui = true
log_level = "INFO"
acl {
  enabled = true
  default_policy = "deny"
  enable_token_persistence = true
}
' > consul.hcl
```
Запускаем в контейнере:
```bash
docker run -d \
  --name=consul \
  --restart=unless-stopped \
  -p 8500:8500 \
  -v ./consul_data:/consul/data \
  -v ./consul.hcl:/consul/config/consul.hcl \
  hashicorp/consul:latest \
  agent -server -bootstrap-expect=1 -client=0.0.0.0
```
Создать `root token`, который будет использоваться для управления системой `ACL` и для создания политик доступа и других токенов доступа:
```bash
docker exec -it consul consul acl bootstrap
```
Создать новую политику доступа:
```bash
docker exec -it consul consul acl policy create -name "default" -rules 'node_prefix "" { policy = "write" } service_prefix "" { policy = "write" } key_prefix "" { policy = "write" }' -token "382834da-28b6-c72c-7ffb-11acf9bf20bc"
```
Создать новый токен доступа:
```bash
docker exec -it consul consul acl token create -policy-name "default" -token "382834da-28b6-c72c-7ffb-11acf9bf20bc"
```
`curl http://localhost:8500/v1/health/service/consul?pretty` \
`curl --request PUT --data "ssh-rsa AAAA" http://localhost:8500/v1/kv/ssh/key` записать секрет KV Store Consul \
`curl -s http://localhost:8500/v1/kv/ssh/key | jq -r .[].Value | base64 --decode` извлечь содержимое секрета

# pussh

[Pussh](https://github.com/bearstech/pussh) — инструмент для параллельного выполнения команд через SSH на нескольких хостах одновременно, выводя результаты с указанием имени каждого хоста. Был внутренним инструментом Bearstech (хостинг-провайдер в Париже, Франция) примерно с 2008 года.
```bash
sudo curl -s https://raw.githubusercontent.com/bearstech/pussh/refs/heads/master/pussh -o /usr/bin/pussh
sudo chmod +x /usr/bin/pussh

bash pussh -h root@192.168.3.102,root@192.168.3.103 uname -a

echo -e "root@192.168.3.102\nroot@192.168.3.103" > host.list
pussh -f host.list uname -a
```

# Sake

[Sake](https://github.com/alajmo/sake) - это командный раннер для локальных и удаленных хостов. Вы определяете серверы и задачи в файле `sake.yaml`, а затем запускаете задачи на серверах.
```bash
curl -sfL https://raw.githubusercontent.com/alajmo/sake/main/install.sh | sh
sake init # инициализировать sake.yml файл
sake list servers # вывести список машин
sake list tags  # список тегов (группы серверов)
sake list tasks # список задач
sake run ping --all # запустить задачу на все хосты
sake exec --all "uname -a && uptime" # запустить команду на всех хостах
```
Пример конфигурации:
```yaml
servers:
  localhost:
    host: 0.0.0.0
    local: true
  obsd:
    host: root@192.168.3.102:22
    tags: [bsd]
  fbsd:
    host: root@192.168.3.103:22
    tags: [bsd]
    work_dir: /tmp

env:
  DATE: $(date -u +"%Y-%m-%dT%H:%M:%S%Z")

specs:
  info:
    output: table
    ignore_errors: true
    omit_empty_rows: true
    omit_empty_columns: true
    any_fatal_errors: false
    ignore_unreachable: true
    strategy: free

tasks:
  ping:
    desc: Pong
    spec: info
    cmd: echo "pong"

  uname:
    name: OS
    desc: Print OS
    spec: info
    cmd: |
      os=$(uname -s)
      release=$(uname -r)
      echo "$os $release"

  uptime:
    name: Uptime
    desc: Print uptime
    spec: info
    cmd: uptime

  info:
    desc: Get system overview
    spec: info
    tasks:
      - task: ping
      - name: date
        cmd: echo $DATE
      - name: pwd
        cmd: pwd
      - task: uname
      - task: uptime
```
`sake run info --tags bsd` запустить набор из 5 заданий из группы info

# Puppet

### Bolt

[Bolt](https://github.com/puppetlabs/bolt) - это инструмент оркестровки, который выполняет заданную команду или группу команд на локальной рабочей станции, а также напрямую подключается к удаленным целям с помощью SSH или WinRM, что не требует установки агентов.

Docs: https://www.puppet.com/docs/bolt/latest/getting_started_with_bolt.html
```bash
wget https://apt.puppet.com/puppet-tools-release-bullseye.deb
sudo dpkg -i puppet-tools-release-bullseye.deb
sudo apt-get update
sudo apt-get install puppet-bolt
```
`nano inventory.yaml`
```yaml
groups:
- name: bsd
  targets:
    - uri: 192.168.3.102:22
      name: openbsd
    - uri: 192.168.3.103:22
      name: freebsd
  config:
    transport: ssh
    ssh:
      user: root
      # password: root
      host-key-check: false
```
`bolt command run uptime --inventory inventory.yaml --targets bsd` выполнить команду uptime на группе хостов bsd, заданной в файле inventory

`echo name: lazyjournal > bolt-project.yaml` создать файл проекта

`mkdir plans && nano test.yaml` создать директорию и файл с планом работ
```yaml
parameters:
  targets:
    type: TargetSpec

steps:
  - name: clone
    command: rm -rf lazyjournal && git clone https://github.com/Lifailon/lazyjournal
    targets: $targets

  - name: test
    command: cd lazyjournal && go test -v -cover --run TestMainInterface
    targets: $targets

  - name: remove
    command: rm -rf lazyjournal
    targets: $targets
```
`bolt plan show` вывести список всех планов

`bolt plan run lazyjournal::test --inventory inventory.yaml --targets bsd -v` запустить план

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

# Jinja

Локальное использование:

`pip install jinja2 --break-system-packages`

`inventory.j2` шаблон для генерации
```
[dev]
{% for host in hosts -%}
{{ host }} ansible_host={{ host }}
{% endfor %}
```
`env.json` файл с переменными
```json
{
  "hosts": ["192.168.3.101", "192.168.3.102", "192.168.3.103"]
}
```
`render.py` скрипт для генерации файла inventory
```Python
from jinja2 import Environment, FileSystemLoader
import json
# Загружаем переменные из JSON
with open('env.json') as f:
    data = json.load(f)
# Настройка шаблонизатора
env = Environment(loader=FileSystemLoader('.'))
template = env.get_template('inventory.j2')
output = template.render(data)
# Сохраняем результат в файл
with open('inventory.generated', 'w') as f:
    f.write(output)
```
`python render.py`

Использование в Ansible для обновления файла `hosts`:

`inventory.ini`
```
[dev]
dev1 ansible_host=192.168.3.101
dev2 ansible_host=192.168.3.102
dev3 ansible_host=192.168.3.103
```
`templates/hosts.j2`
```
127.0.0.1 localhost
{% for host in groups['all'] -%}
{{ hostvars[host]['ansible_host'] }} {{ host }}
{% endfor %}
```
`playbook.yml`
```yaml
- name: Update hosts file
  hosts: all
  become: true
  tasks:
    - name: Generate hosts file
      template:
        src: hosts.j2
        dest: /etc/hosts
        owner: root
        group: root
        mode: '0644'
```
`ansible-playbook -i inventory.ini playbook.yml --check --diff` отобразит изменения без их реального применения \
`ansible-playbook -i inventory.ini playbook.yml -K` позволяет передать пароль для root

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

# Prometheus

Пример создания экспортера для получения метрик температуры всех дисков из CrystalDiskInfo и отправки в [Prometheus](https://github.com/prometheus/prometheus) через [PushGateway](https://github.com/prometheus/pushgateway).

1. Запускаем `pushgateway` в контейнере:

`docker run -d --name pushgateway --restart unless-stopped -p 19091:9091 prom/pushgateway`

2. Запускаем скрипт в консоли:
```PowerShell
$instance = [System.Net.Dns]::GetHostName()
$pushgatewayUrl = "http://192.168.3.100:19091/metrics/job/disk_temperature"
# Изменить адрес шлюза на имя контейнера при запуске через compose
# $pushgatewayUrl = "http://pushgateway:9091/metrics/job/disk_temperature"
$path = "C:/Program Files/CrystalDiskInfo/Smart"
# Изменить путь при запуске в контейнере Docker через WSL
# $path = "/mnt/c/Program Files/CrystalDiskInfo/Smart"
# Необходимо строго использовать синтаксис PowerShell (избегая псевдонимы ls)
$diskArray = $(Get-ChildItem $path).Name
while ($true) {
    $metrics = "# TYPE disk_temperature gauge`n"
    foreach ($diskName in $diskArray) {
        $lastTemp = $(@("Date,Value")+$(Get-Content "$path/$diskName/Temperature.csv") | ConvertFrom-Csv)[-1].Value
        $diskLabel = $diskName -replace "[^a-zA-Z0-9]", "_"
        $metrics += "disk_temperature{disk=`"$diskLabel`",instance=`"$instance`"} $lastTemp`n"
    }
    $metrics
    Invoke-RestMethod -Uri $pushgatewayUrl -Method POST -Body $metrics
    Start-Sleep 10
}
```
3. Проверяем наличие метрик на конечной точке шлюза:
```PowerShell
$(Invoke-RestMethod http://192.168.3.100:9091/metrics).Split("`n") | Select-String "disk_temperature"
```
4. Добавляем конфигурацию в `prometheus.yml`:
```yaml
scrape_configs:
  - job_name: cdi-exporter
    scrape_interval: 10s
    scrape_timeout: 2s
    metrics_path: /metrics
    static_configs:
      - targets:
        - '192.168.3.100:19091'
```
`docker-compose kill -s SIGHUP prometheus` применяем изменения

5. Собираем контейнер в среде `WSL` с монтированием системного диска Windows:
```dockerfile
Write-Output '
FROM mcr.microsoft.com/powershell:latest
WORKDIR /cdi-exporter
COPY cdi-exporter.ps1 ./cdi-exporter.ps1
CMD ["pwsh", "-File", "cdi-exporter.ps1"]
' | Out-File -FilePath dockerfile
```
`docker build -t cdi-exporter .` \
`docker run -d -v /mnt/c:/mnt/c --name cdi-exporter cdi-exporter`

6. Собираем стек из шлюза и скрипта в `compose`:
```yaml
Write-Output '
services:
  cdi-exporter:
    build:
      context: .
      dockerfile: dockerfile
    container_name: cdi-exporter
    volumes:
      - /mnt/c:/mnt/c
    restart: unless-stopped

  pushgateway:
    image: prom/pushgateway
    container_name: pushgateway
    ports:
      - "19091:9091"
    restart: unless-stopped
' | Out-File -FilePath docker-compose.yml
```
`docker-compose up -d`

7. Настраиваем `Dashboard` в `Grafana`:

Переменные для фильтрации запроса: \
hostName: `label_values(exported_instance)` \
diskName: `label_values(disk)` \
Метрика температуры: `disk_temperature{exported_instance="$hostName", disk=~"$diskName"}`

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
