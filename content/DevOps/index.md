---
title: "DevOps"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

## Git

`git --version` получить версию git\
`git config --global user.name "Lifailon"` добавить имя для коммитов\
`git config --global user.email "lifailon@yandex.ru"` добавить email для
коммитов\
`git config --global --edit` изменить глобальную конфигурацию в
редакторе по умолчанию\
`ssh-keygen -t rsa -b 4096` сгенерировать ключ RSA\
`Get-Service | where name -match "ssh-agent" | Set-Service -StartupType Automatic`
включить автоматический режим запуска службы ssh-agent\
`Get-Service | where name -match "ssh-agent" | Start-Service`
перезапустить службу\
`Get-Service | where name -match "ssh-agent" | select Name,Status,StartType`
статус службы\
`ssh-agent` позволяет хранить и использовать SSH-ключи для
аутентификации без необходимости каждый раз вводить парольную фразу
(passphrase)\
`ssh-add C:\Users\Lifailon\.ssh\id_rsa` добавляет SSH-ключ в агент, что
позволяет агенту управлять ключом и использовать его для аутентификации\
`cat ~\.ssh\id_rsa.pub | Set-Clipboard` скопировать ключ в буфер обмена
и использовать для авторизации на GitHub в [settings
keys](https://github.com/settings/keys)\
`cd $home\Documents\Git`\
`git clone git@github.com:Lifailon/lifailon.github.io` клонировать
репозиторий\
`cd lifailon.github.io`\
`git grep "ping ya.ru"` поиск текста в файлах\
`git fetch` загрузить изменения из удаленного хранилища для обновления
всех веток локального репозитория, не затрагивая текущую рабочую ветку
(загружает все коммиты, ветки и т.д. которые не присутствуют в локальном
репозитории)\
`git fetch --all` загрузить все ветки с удаленного репозитория
(обновляет информацию о состоянии удаленного репозитория и загружает все
изменения ваших веток без автоматического объединения)\
`git pull` загрузить изменения из удаленного хранилища для обновления
локального репозитория (выполняет git fetch, чтобы получить последние
изменения из удаленного репозитория, а затеим объеденяем изменения с
локальной копией с помощью `git merge` для обновления текущей рабочей
ветки)\
`git status` отобразить статус изменений по файлам\
`git diff` отобразить историю изменений построчно\
`git diff pandoc` сравнивает изменения в текущей рабочей директории с
последним коммитом в указанной ветке\
`git add .` добавить (проиндексировать) изменения во всех файлах
текущего каталога\
`git commit -m "update powershell commands"` сохранить изменения с
комментарием\
`git push` синхронизировать локальные изменения с репозиторием на
сервере\
`git push origin mkdocs-material` отправить в конкретную ветку\
`git push origin --delete mkdocs` удалить ветку на удаленном сервере\
`git commit --amend` изменить комментарий в последнем коммите (до push)\
`git branch -a` отобразить все ветки (в том числе удаленные
remotes/origin)\
`git branch hugo` создать новую ветку\
`git branch -m hugo-public` переименовать текущую ветку\
`git branch -d hugo-public` удалить ветку\
`git switch hugo` переключиться на другую ветку\
`git push origin hugo` отправить изменения в указанную ветку\
`git push --set-upstream origin hugo` отслеживать изменения в ветке
(позволяет делать push без указания ветки)\
`git merge hugo` слияние текущей ветки (pandoc) с указанной (hugo)\
`git log --oneline --all` лог коммитов\
`git log --graph` коммиты и следование веток\
`git log --author="Lifailon"` показывает историю коммитов указанного
пользователя\
`git blame index.html` показывает, кто и когда внес изменения в каждую
строку указанного файла\
`git show d01f09dead3a6a8d75dda848162831c58ca0ee13` отобразить подробный
лог по номеру коммита\
`git restore filename` отменить все локальные изменения в рабочей копии
независимо от того, были они проиндексированы или нет (если была
индексация через `add`), возвращая его к состоянию на момент последнего
коммита\
`git restore --source d01f09dead3a6a8d75dda848162831c58ca0ee13 filename`
восстановить файл на указанную версию по хэшу индентификатора коммита\
`git checkout filename` откатить изменения не проиндексированные для
коммита, возвращая его к состоянию, каким оно было на момент последнего
коммита (если не было индексации через `add`)\
`git checkout d01f09dead3a6a8d75dda848162831c58ca0ee13` переключить
локальные файлы рабочей копии на указанный коммит (переключает `HEAD` на
указанный коммит)\
`git reset HEAD filename` удалить указанный файл из индекса без удаления
самих изменений в файле для последующей повторной индексации (если был
add но не было `commit`, потом выполнить `checkout`)\
`git reset --soft HEAD^` отменяет последний (\^) коммит, сохраняя
изменения из этого коммита в рабочем каталоге и индексе (подготовленной
области), можно внести изменения в файлы и повторно их зафиксировать\
`git reset --hard HEAD^` полностью отменяет последний коммит, удаляя все
его изменения из рабочего каталога и индекса до состояния последнего
коммита\
`git reset --hard d01f09dead3a6a8d75dda848162831c58ca0ee13` откатывает
HEAD к указанному коммиту и удаляет все коммиты, которые были сделаны
после него (будут потеряны все незакоммиченные изменения и историю
коммитов после указанного)\
`git revert HEAD --no-edit` создает новый коммит, который отменяет
последний коммит (HEAD) и новый коммит будет добавлен поверх него
(события записываются в `git log`)\
`git revert d01f09dead3a6a8d75dda848162831c58ca0ee13` создает новый
коммит, который отменяет изменения, внесенные в указанный коммит с хешем
(не изменяет историю коммитов, а создает новый коммит с изменениями
отмены)\
`git stash` сохраняет текущие изменения в стэш (временное хранилище) и
очищает рабочую директорию\
`git stash pop` применяет последние изменения из стэша к текущей ветке

## Atlassian

### Bitbucket

``` powershell
$url = "https://github.com/AtlassianPS/BitbucketPS/archive/refs/heads/master.zip"
Invoke-RestMethod $url -OutFile $home\Downloads\BitbucketPS.zip
Expand-Archive -Path "$home\Downloads\BitbucketPS.zip" -OutputPath "$home\Downloads"
Copy-Item -Path "$home\Downloads\BitbucketPS-master\*" -Destination "$($env:PSModulePath.Split(";")[0])\PSBitBucket" -Recurse
Remove-Item "$home\Downloads\Bitbucket*" -Recurse -Force
```

`Import-Module PSBitBucket`\
`Get-Command -Module PSBitBucket`\
`Set-BitBucketConfigServer -Url $url -User username -Password password`
установить конфигурацию сервера BitBucket\
`Get-BitBucketConfigServer` получить текущую конфигурацию сервера
BitBucket\
`Get-Repositories` получить список всех репозиториев для текущей
конфигурации сервера BitBucket\
`Get-ProjectKey` получить ключ проекта BitBucket\
`Get-BranchList -Repository pSyslog` список всех веток в репозитории\
`Get-Branch -Repository pSyslog -Branch main` получить информацию о
конкретной ветке репозитория\
`Get-CommitMessage -Repository pSyslog -CommitHash $hash` получить
сообщение коммита по его хэшу\
`Get-Commits -Repository pSyslog -Limit 10` список последних 10 коммитов
в репозитории\
`Get-CommitsForBranch -Repository pSyslog -Branch main` список коммитов
для конкретной ветки в репозитории

### Jira

`Install-Module JiraPS -Scope CurrentUser -Repository PSGallery -AllowClobber -Force`\
`Get-Command -Module JiraPS`\
`Get-JiraServerInfo` информация о сервере\
`Add-JiraFilterPermission` добавить разрешения для фильтра\
`Add-JiraGroupMember` добавить участника в группу\
`Add-JiraIssueAttachment` добавить вложения к задаче\
`Add-JiraIssueComment` добавить комментария к задаче\
`Add-JiraIssueLink` добавить ссылки на задачу\
`Add-JiraIssueWatcher` добавить наблюдателя к задаче\
`Add-JiraIssueWorklog` добавить рабочего журнала к задаче\
`Find-JiraFilter` поиск фильтра\
`Format-Jira` форматирование данных Jira\
`Get-JiraComponent` получение компонента проекта\
`Get-JiraConfigServer` получение конфигурации сервера Jira\
`Get-JiraField` получение поля Jira\
`Get-JiraFilter` получение фильтра\
`Get-JiraFilterPermission` получение разрешения фильтра\
`Get-JiraGroup` получение группы\
`Get-JiraGroupMember` получение участников группы\
`Get-JiraIssue` получение задачи\
`Get-JiraIssueAttachment` получение вложения задачи\
`Get-JiraIssueAttachmentFile` получение файла вложения задачи\
`Get-JiraIssueComment` получение комментария задачи\
`Get-JiraIssueCreateMetadata` получение метаданных создания задачи\
`Get-JiraIssueEditMetadata` получение метаданных редактирования задачи\
`Get-JiraIssueLink` получение ссылки задачи\
`Get-JiraIssueLinkType` получение типа ссылки задачи\
`Get-JiraIssueType` получение типа задачи\
`Get-JiraIssueWatcher` получение наблюдателя задачи\
`Get-JiraIssueWorklog` получение рабочего журнала задачи\
`Get-JiraPriority` получение приоритета задачи\
`Get-JiraProject` получение проекта\
`Get-JiraRemoteLink` получение удаленной ссылки\
`Get-JiraServerInformation` получение информации о сервере Jira\
`Get-JiraSession` получение сессии\
`Get-JiraUser` получение пользователя\
`Get-JiraVersion` получение версии проекта\
`Invoke-JiraIssueTransition` выполнение перехода задачи\
`Invoke-JiraMethod` выполнение метода Jira\
`Move-JiraVersion` перемещение версии проекта\
`New-JiraFilter` создание нового фильтра\
`New-JiraGroup` создание новой группы\
`New-JiraIssue` создание новой задачи\
`New-JiraSession` создание новой сессии\
`New-JiraUser` создание нового пользователя\
`New-JiraVersion` создание новой версии проекта\
`Remove-JiraFilter` удаление фильтра\
`Remove-JiraFilterPermission` удаление разрешения фильтра\
`Remove-JiraGroup` удаление группы\
`Remove-JiraGroupMember` удаление участника группы\
`Remove-JiraIssue` удаление задачи\
`Remove-JiraIssueAttachment` удаление вложения задачи\
`Remove-JiraIssueLink` удаление ссылки задачи\
`Remove-JiraIssueWatcher` удаление наблюдателя задачи\
`Remove-JiraRemoteLink` удаление удаленной ссылки\
`Remove-JiraSession` удаление сессии\
`Remove-JiraUser` удаление пользователя\
`Remove-JiraVersion` удаление версии проекта\
`Set-JiraConfigServer` установка конфигурации сервера Jira\
`Set-JiraFilter` установка фильтра\
`Set-JiraIssue` установка задачи\
`Set-JiraIssueLabel` установка метки задачи\
`Set-JiraUser` установка пользователя\
`Set-JiraVersion` установка версии проекта

### Confluence

`Install-Module ConfluencePS -Scope CurrentUser -Repository PSGallery -AllowClobber -Force`\
`Get-Command -Module ConfluencePS`\
`Add-ConfluenceAttachment` добавить вложения к странице\
`Add-ConfluenceLabel` добавить метки к странице\
`ConvertTo-ConfluenceStorageFormat` конвертация содержимого в формат
хранения Confluence\
`ConvertTo-ConfluenceTable` конвертация данных в таблицу Confluence\
`Get-ConfluenceAttachment` получение вложения страницы\
`Get-ConfluenceAttachmentFile` получение файла вложения страницы\
`Get-ConfluenceChildPage` получение дочерних страниц\
`Get-ConfluenceLabel` получение меток страницы\
`Get-ConfluencePage` получение информации о странице\
`Get-ConfluenceSpace` получение информации о пространстве\
`Invoke-ConfluenceMethod` выполнение метода Confluence\
`New-ConfluencePage` создание новой страницы\
`New-ConfluenceSpace` создание нового пространства\
`Remove-ConfluenceAttachment` удаление вложения страницы\
`Remove-ConfluenceLabel` удаление метки со страницы\
`Remove-ConfluencePage` удаление страницы\
`Remove-ConfluenceSpace` удаление пространства\
`Set-ConfluenceAttachment` установка вложения страницы\
`Set-ConfluenceInfo` установка информации о странице\
`Set-ConfluenceLabel` установка метки страницы\
`Set-ConfluencePage` установка страницы

## GitHub API

`$user = "Lifailon"`\
`$repository = "ReverseProxyNET"`\
`Invoke-RestMethod https://api.github.com/users/$($user)` получаем
информацию о пользователе\
`Invoke-RestMethod https://api.github.com/users/$($user)/repos` получаем
список последних (актуальные коммиты) 30 репозиториев указанного
пользователя\
`Invoke-RestMethod https://api.github.com/users/$($user)/repos?per_page=100`
получаем список последних (актуальные коммиты) 100 репозиториев
указанного пользователя\
`Invoke-RestMethod https://api.github.com/repos/$($user)/$($repository)/contents`
получаем содержимое корневой директории репозитория\
`Invoke-RestMethod https://api.github.com/repos/$($user)/$($repository)/contents/source/rpnet.cs`
получаем содержимое файла в формате Base64\
`$commits = Invoke-RestMethod https://api.github.com/repos/$($user)/$($repository)/commits`
получаем список коммитов\
`$commits[0].commit.message` читаем комментарий последнего коммита\
`$commits[0].commit.committer.date` получаем дату последнего коммита\
`Invoke-RestMethod https://api.github.com/repos/$($user)/$($repository)/commits/$($commits[0].sha)`
получаем подробную информацию изменений о последнем коммите в
репозитории\
`$releases_latest = Invoke-RestMethod "https://api.github.com/repos/$($user)/$($repository)/releases/latest"`
получаем информацию о последнем релизе в репозитории\
`$releases_latest.assets.name` список приложенных файлов последнего
релиза\
`$releases_latest.assets.browser_download_url` получаем список url для
загрузки файлов\
`$($releases_latest.assets | Where-Object name -like "*win*x64*exe*").browser_download_url`
фильтруем по ОС и разрядности\
`$(Invoke-RestMethod -Uri "https://api.github.com/repos/Lifailon/epic-games-radar/commits?path=api/giveaway/index.json")[0].commit.author.date`
узнать дату последнего обновления файла в репозитории\
`$issues = Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/issues?per_page=500`
получаем список открытых проблем в репозитории (получаем максимум 100
последних, по умолчанию забираем последние 30 issues)\
`$issue_number = $($issues | Where-Object title -match "PowerShell").number`
получаем номер issue, в заголовке которого есть слово "PowerShell"\
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/issues/$($issue_number)/comments`
отобразить список комментарием указанного issues\
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/languages`
получаем список языков программирования, используемых в репозитории\
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/pulls`
получаем список всех pull requests в репозитории\
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/forks`
получаем список форков (forks)\
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/stargazers?per_page=4000`
получаем список пользователей, которые поставили звезды репозиторию\
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/subscribers`
получаем список подписчиков (watchers) репозитория

## GitHub Actions

### Runner (Agent)

`mkdir actions-runner; cd actions-runner`\
`Invoke-WebRequest -Uri https://github.com/actions/runner/releases/download/v2.316.1/actions-runner-win-x64-2.316.1.zip -OutFile actions-runner-win-x64-2.316.1.zip`
загрузить пакет с Runner последней версии\
`if((Get-FileHash -Path actions-runner-win-x64-2.316.1.zip -Algorithm SHA256).Hash.ToUpper() -ne 'e41debe4f0a83f66b28993eaf84dad944c8c82e2c9da81f56a850bc27fedd76b'.ToUpper()){ throw 'Computed checksum did not match' }`
проверить валидность пакета с помощью hash-суммы\
`Add-Type -AssemblyName System.IO.Compression.FileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory("$PWD/actions-runner-win-x64-2.316.1.zip", "$PWD")`
разархивировать\
`Remove-Item *.zip` удалить архив\
`./config.cmd --url https://github.com/Lifailon/egapi --token XXXXXXXXXXXXXXXXXXXXXXXXXXXXX`
авторизовать и сконфигурировать сборщика с помощью скрипта (что бы на
последнем пункте создать службу для управления сборщиком, нужно
запустить консоль с правами администратора)\
`./run.cmd` запустить процесс (если не используется служба)\
`Get-Service *actions* | Start-Service` запустить службу\
`Get-Process *Runner.Listener*`\
`./config.cmd remove --token XXXXXXXXXXXXXXXXXXXXXXXXXXXXX` удалить
конфигурацию

### Build (Pipeline)

``` yaml
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

### Docker CI

``` yaml
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

### Actions Logs

`$(Invoke-RestMethod https://api.github.com/repos/Lifailon/TorAPI/actions/workflows).total_count`получить
количество запусков всех рабочих процессов\
`$(Invoke-RestMethod https://api.github.com/repos/Lifailon/TorAPI/actions/workflows).workflows`подробная
информации о запускаемых рабочих процессах\
`$actions_last_id = $(Invoke-RestMethod https://api.github.com/repos/Lifailon/TorAPI/actions/workflows).workflows[-1].id`получить
идентификатор последнего события\
`$(Invoke-RestMethod https://api.github.com/repos/Lifailon/TorAPI/actions/workflows/$actions_last_id/runs).workflow_runs`подробная
информация о последней сборке\
`$run_id = $(Invoke-RestMethod https://api.github.com/repos/Lifailon/TorAPI/actions/workflows/$actions_last_id/runs).workflow_runs.id`получить
идентификатор запуска рабочего процесса\
`$(Invoke-RestMethod "https://api.github.com/repos/Lifailon/TorAPI/actions/runs/$run_id/jobs").jobs.steps`подробная
информация для всех шагов выполнения (время работы и статус выполнения)\
`$jobs_id = $(Invoke-RestMethod "https://api.github.com/repos/Lifailon/TorAPI/actions/runs/$run_id/jobs").jobs[0].id`получить
идентификатор последнего задания указанного рабочего процесса

``` powershell
$url = "https://api.github.com/repos/Lifailon/TorAPI/actions/jobs/$jobs_id/logs"
$headers = @{
    Authorization = "token ghp_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
}
Invoke-RestMethod -Uri $url -Headers $headers # получить логи задания
```

## Vercel

`npm i -g vercel` установить глобально в систему Vercel CLI\
`vercel --version` выводит текущую версию установленного Vercel CLI\
`vercel login` выполняет вход в аккаунт Vercel
(`> Continue with GitHub`)\
`vercel logout` выполняет выход из аккаунта Vercel\
`vercel init` инициализирует новый проект в текущей директории (создает
файл конфигурации vercel.json и другие файлы, необходимые для проекта)\
`vercel dev` запускает локальный сервер для проверки работоспособности
(http://localhost:3000)\
`vercel deploy` загружает проект на серверы Vercel и развертывает его\
`vercel link` привязывает текущую директорию к существующему проекту на
сервере Vercel (выбрать из списка)\
`vercel unlink` отменяет привязку текущей директории от проекта Vercel\
`vercel env` управляет переменными окружения для проекта\
`vercel env pull` подтягивает переменные окружения с Vercel в локальный
.env файл\
`vercel env ls` показывает список всех переменных окружения для проекта\
`vercel env add <key> <environment>` добавляет новую переменную
окружения для указанного окружения (production, preview, development)\
`vercel env rm <key> <environment>` удаляет переменную окружения из
указанного окружения\
`vercel projects` управляет проектами Vercel\
`vercel projects ls` показывает список всех проектов\
`vercel projects add` добавляет новый проект\
`vercel projects rm <project>` удаляет указанный проект\
`vercel pull` подтягивает последние настройки окружения с Vercel\
`vercel alias` управляет алиасами доменов для проектов\
`vercel alias ls` показывает список всех алиасов для текущего проекта\
`vercel alias set <alias>` устанавливает алиас для указанного проекта\
`vercel alias rm <alias>` удаляет указанный алиас\
`vercel domains` управляет доменами, привязанными к проекту\
`vercel domains ls` показывает список всех доменов\
`vercel domains add <domain>` добавляет новый домен к проекту\
`vercel domains rm <domain>` удаляет указанный домен\
`vercel teams` управляет командами и членами команд на Vercel\
`vercel teams ls` показывает список всех команд\
`vercel teams add <team>` добавляет новую команду\
`vercel teams rm <team>` удаляет указанную команду\
`vercel logs <deployment>` выводит логи для указанного деплоя\
`vercel secrets` управляет секретами, используемыми в проектах\
`vercel secrets add <name> <value>` добавляет новый секрет\
`vercel secrets rm <name>` удаляет указанный секрет\
`vercel secrets ls` показывает список всех секретов\
`vercel switch <team>` переключается между командами и аккаунтами Vercel

### CD

``` yaml
name: Deploy to Vercel

on:
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - name: Clone repository
      uses: actions/checkout@v2

    - name: Install Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '20'

    - name: Install dependencies
      run: npm install

    - name: Install Vercel CLI
      run: npm install --global vercel@latest

    - name: Deploy to Vercel
      run: vercel deploy --prod --token=${{ secrets.VERCEL_TOKEN }} --yes
```

## GitLab

    docker run --detach \
        --hostname 192.168.3.101 \
        --publish 443:443 --publish 80:80 --publish 2222:22 \
        --name gitlab \
        --restart always \
        --volume /srv/gitlab/config:/etc/gitlab \
        --volume /srv/gitlab/logs:/var/log/gitlab \
        --volume /srv/gitlab/data:/var/opt/gitlab \
        gitlab/gitlab-ee:latest

`docker logs -f gitlab` логи контейнера\
`docker exec -it gitlab cat /etc/gitlab/initial_root_password` получить
пароль для root\
`docker exec -it gitlab cat /etc/gitlab/gitlab.rb` конфигурация сервера

Получить токен регистрации Runner:
http://192.168.3.101/root/torapi/-/settings/ci_cd#js-runners-settings

`curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64`
загрузить исполняемый файл Runner\
`chmod +x /usr/local/bin/gitlab-runner`

    docker run -d --name gitlab-runner --restart always \
        -v /srv/gitlab-runner/config:/etc/gitlab-runner \
        gitlab/gitlab-runner:latest

`docker exec -it gitlab-runner bash`\
`gitlab-runner list` список сборщиков\
`gitlab-runner verify` проверка\
`gitlab-runner restart` применить настройки\
`gitlab-runner status` статус\
`gitlab-runner unregister --all-runners` удалить все регистрации\
`gitlab-runner install` установить службу\
`gitlab-runner run` запустить с выводом в консоль

`gitlab-runner register`

    Enter the GitLab instance URL (for example, https://gitlab.com/): http://192.168.3.101/
    Enter the registration token: GR1348941enqAxqQgm8AZJD_g7vme
    Enter an executor: shell

`cat /etc/gitlab-runner/config.toml` конфигурация

Включить импорт проектов из GitHub:
http://192.168.3.101/admin/application_settings/general#js-import-export-settings

``` yaml
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

## Jenkins

`docker run -d --name=jenkins -p 8080:8080 --restart=always -v jenkins_home:/var/jenkins_home jenkins/jenkins:latest`\
`ls /var/lib/docker/volumes/jenkins_home/_data/jobs` директория хранящая
историю сборок в хостовой системе\
`docker exec -it jenkins /bin/bash` подключиться к контейнеру\
`cat /var/jenkins_home/secrets/initialAdminPassword` получить токен
инициализации

Cli: http://127.0.0.1:8080/manage/cli\
`apt install openjdk-17-jre-headless`\
`wget http://127.0.0.1:8080/jnlpJars/jenkins-cli.jar -P /usr/local/bin/`
скачать jenkins-cli\
`java -jar /usr/local/bin/jenkins-cli.jar -auth lifailon:password -s http://127.0.0.1:8080 -webSocket help`
получить список команд

### SSH Steps

Создать авторизационные данные для подключения по `ssh` используя логин
и пароль:

Manage \> Credentials \> Global \> New credentials \> Kind: Username
with password

Пример Pipeline подключения к удаленному серверу по `ssh` с
использованием параметров для получения в консоли логов работы
[Kinozal-Bot](https://github.com/Lifailon/Kinozal-Bot) или статистики
сервера через скрипт [hwstat](https://github.com/Lifailon/hwstat):

``` groovy
pipeline {
    agent any
    parameters {
        string(name: 'Count', defaultValue: '100', description: 'Number of lines from log')
        choice(name: "Mode", choices: ["Log","Stats"], description: "Select mode")
        // booleanParam(name: "State", defaultValue: false)
    }
    stages {
        stage('Remote SSH') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: '91a9b0bb-bde9-4e06-ba19-9eb5bfd61612', usernameVariable: 'SSH_USER', passwordVariable: 'SSH_PASS')]) {
                        sshCommand remote: [
                            name: '192.168.3.101',
                            host: '192.168.3.101',
                            port: 2121,
                            user: env.SSH_USER,
                            password: env.SSH_PASS,
                            allowAnyHosts: true
                        ], command: """
                            if [[ "${params.Mode}" == "Log" ]]; then
                                bash kinozal-bot/kinozal-bot-0.4.5.sh log bot ${params.Count}
                            elif [[ "${params.Mode}" == "Stats" ]]; then
                                curl -s https://raw.githubusercontent.com/Lifailon/hwstat/rsa/hwstat.sh | bash
                            fi
                        """
                    }
                }
            }
        }
    }
}
```

### HttpURLConnection

Пример **REST API** запроса к фейковому серверу
[jsonplaceholder](https://jsonplaceholder.typicode.com)

> Тело скрипта можно проверить в консоли Jenkins:
> http://127.0.0.1:8080/manage/script

``` groovy
pipeline {
    agent any
    stages {
        stage('Call REST API') {
            steps {
                script {
                    def url = new URL("https://jsonplaceholder.typicode.com/posts")
                    def connection = url.openConnection()
                    connection.setRequestMethod("GET")
                    connection.setRequestProperty("Accept", "application/json")
                    def responseCode = connection.getResponseCode()
                    if (responseCode == 200) {
                        def response = connection.getInputStream().getText()
                        println("Response: " + response)
                    } else {
                        error("Failed to call API, response code: ${responseCode}")
                    }
                    connection.disconnect()
                }
            }
        }
    }
}
```

### Active Choices Parameter

[Plugin](https://plugins.jenkins.io/uno-choice)

Пример динамического параметра для получения списка `choice` тэгов
релизов [PowerShell Core](https://github.com/PowerShell/PowerShell) из
репозитория GitHub:

Name: `selectedVersion`

Groovy Script:

``` groovy
import groovy.json.JsonSlurper
def apiUrl = "https://api.github.com/repos/PowerShell/PowerShell/tags"
def conn = new URL(apiUrl).openConnection()
conn.setRequestProperty("User-Agent", "Jenkins")
def response = conn.getInputStream().getText()
def json = new JsonSlurper().parseText(response)
def versions = json.collect { it.name }
return versions
```

Pipeline script:

``` groovy
pipeline {
    agent any
    stages {
        stage('Display Selected Version') {
            steps {
                script {
                    echo "Selected PowerShell version: ${params.selectedVersion}"
                }
            }
        }
    }
}
```

## Graylog

[Graylog Docker Image](https://hub.docker.com/r/itzg/graylog)

-   Установка MongoDB:

``` bash
docker run --name mongo -d mongo:3
```

-   Используем прокси для установки Elassticsearch:

``` bash
docker run --name elasticsearch \
    -e "http.host=0.0.0.0" -e "xpack.security.enabled=false" \
    -d dockerhub.timeweb.cloud/library/elasticsearch:5.5.1
```

-   Указать статический IP адрес для подключения к API

``` bash
docker run --name Graylog \
    --link mongo \
    --link elasticsearch \
    -p 9000:9000 -p 12201:12201 -p 514:514 -p 5044:5044 \
    -e GRAYLOG_WEB_ENDPOINT_URI="http://192.168.3.101:9000/api" \
    -d graylog/graylog:2.3.2-1
```

-   Настройка syslog на клиенте Linux:

`nano /etc/rsyslog.d/graylog.conf`

``` bash
*.* @@192.168.3.101:514;RSYSLOG_SyslogProtocol23Format
```

`systemctl restart rsyslog`

-   Создать входящий поток (inputs) для Syslog на порту 514 по протоколу
    TCP:

http://192.168.3.101:9000/system/inputs

-   Фильтр для логов Kinozal-Bot:

`facility:"system daemon" AND application_name:bash AND message:\[ AND message:\]`

-   Настройка Winlogbeat на клиенте Windows:

Установка агента:

``` powershell
irm https://artifacts.elastic.co/downloads/beats/winlogbeat/winlogbeat-8.15.0-windows-x86_64.zip -OutFile $home\Documents\winlogbeat-8.15.0.zip
Expand-Archive $home\Documents\winlogbeat-8.15.0.zip
cd $home\Documents\winlogbeat-8.15.0-windows-x86_64
```

Добавить отправку в Logstash:

`code winlogbeat.yml`

``` bash
output.logstash:
  hosts: ["192.168.3.101:5044"]
```

И закомментировать отправку данных в Elasticsearch
(output.elasticsearch)

`.\winlogbeat.exe -c winlogbeat.yml` запустить агент с правами
администратора в консоли

``` bash
.\install-service-winlogbeat.ps1 # установить службу
Get-Service winlogbeat | Start-Service
```

-   Настроить Inputs для приема Beats на порту 5044

## Ansible

`apt -y update && apt -y upgrade`\
`apt -y install ansible` v2.10.8\
`apt -y install ansible-core` v2.12.0\
`apt -y install sshpass`

`ansible-galaxy collection install ansible.windows` установить коллекцию
модулей\
`ansible-galaxy collection install community.windows`\
`ansible-galaxy collection list | grep windows`\
`ansible-config dump | grep DEFAULT_MODULE_PATH` путь хранения модулей

`apt-get -y install python-dev libkrb5-dev krb5-user` пакеты для
Kerberos аутентификации\
`apt install python3-pip`\
`pip3 install requests-kerberos`\
`nano /etc/krb5.conf` настроить \[realms\] и \[domain_realm\]\
`kinit -C support4@domail.local`\
`klist`

`ansible --version`\
`config file = None`\
`nano /etc/ansible/ansible.cfg` файл конфигурации

    [defaults]
    inventory = /etc/ansible/hosts
    # uncomment this to disable SSH key host checking
    # Отключить проверку ключа ssh (для подключения используя пароль)
    host_key_checking = False

### Hosts

`nano /etc/ansible/hosts`

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

`ansible-inventory --list` проверить конфигурацию (читает в формате
JSON) или YAML (-y) с просмотром все применяемых переменных

### Win_Modules

`ansible us -m ping`\
`ansible win_ssh -m ping`\
`ansible us -m shell -a "uptime && df -h | grep lv"`\
`ansible us -m setup | grep -iP "mem|proc"` информация о железе\
`ansible us -m apt -a "name=mc" -b` повысить привилегии sudo (-b)\
`ansible us -m service -a "name=ssh state=restarted enabled=yes" -b`
перезапустить службу\
`echo "echo test" > test.sh`\
`ansible us -m copy -a "src=test.sh dest=/root mode=777" -b`\
`ansible us -a "ls /root" -b`\
`ansible us -a "cat /root/test.sh" -b`

`ansible-doc -l | grep win_` [список всех модулей
Windows](https://docs.ansible.com/ansible/latest/collections/ansible/windows/)\
`ansible ws -m win_ping` windows модуль\
`ansible ws -m win_ping -u WinRM-Writer` указать логин\
`ansible ws -m setup` собрать подробную информацию о системе\
`ansible ws -m win_whoami` информация о правах доступах, группах
доступа\
`ansible ws -m win_shell -a '$PSVersionTable'`\
`ansible ws -m win_shell -a 'Get-Service | where name -match "ssh|winrm"'`\
`ansible ws -m win_service -a "name=sshd state=stopped"`\
`ansible ws -m win_service -a "name=sshd state=started"`

#### win_shell (vars/debug)

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

`ansible-playbook /etc/ansible/PowerShell-Vars.yml`\
`ansible-playbook /etc/ansible/PowerShell-Vars.yml --extra-vars "SearchName='LogLevel|Syslog'"`
передать переменную

#### win_powershell

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

#### win_chocolatey

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

#### win_regedit

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

#### win_service

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

#### win_service_info

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

#### fetch/slurp

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

#### win_copy

`echo "Get-Service | where name -eq vss | Start-Service" > /home/lifailon/Start-Service-VSS.ps1`\
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

`curl -OL https://github.com/PowerShell/PowerShell/releases/download/v7.3.6/PowerShell-7.3.6-win-x64.msi`\
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

#### win_command

`nano /etc/ansible/run-script-ps1.yml`

```yaml
    - hosts: ws
      tasks:
      - name: Run PowerShell Script
        win_command: powershell -ExecutionPolicy ByPass -File C:\Users\Lifailon\Desktop\Start-Service-VSS.ps1
```

`ansible-playbook /etc/ansible/run-script-ps1.yml`

#### win_package

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

#### win_firewall_rule

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

#### win_group

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

#### win_group_membership

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

#### win_user

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

#### win_feature

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

#### win_reboot

`nano /etc/ansible/win-reboot.yml`

```yaml
    - hosts: ws
      tasks:
      - name: Reboot a slow machine that might have lots of updates to apply
        win_reboot:
          reboot_timeout: 3600
```

`ansible-playbook /etc/ansible/win-reboot.yml`

#### win_find

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

#### win_uri

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

#### win_updates

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

#### win_chocolatey

[Install](https://chocolatey.org/install)\
[API](https://community.chocolatey.org/api/v2/package/chocolatey)\
[Deployment](https://docs.chocolatey.org/en-us/guides/organizations/organizational-deployment-guide)

```yaml
    - name: Ensure Chocolatey installed from internal repo
      win_chocolatey:
        name: chocolatey
        state: present
        # source: URL-адрес внутреннего репозитория
        source: https://community.chocolatey.org/api/v2/ChocolateyInstall.ps1
```

## DSC

`Import-Module PSDesiredStateConfiguration`\
`Get-Command -Module PSDesiredStateConfiguration`\
`(Get-Module PSDesiredStateConfiguration).ExportedCommands`\
`Get-DscLocalConfigurationManager`

`Get-DscResource`\
`Get-DscResource -Name File -Syntax`
[синтаксис](https://learn.microsoft.com/ru-ru/powershell/dsc/reference/resources/windows/fileresource?view=dsc-1.1)

`Ensure = Present` настройка должна быть включена (каталог должен
присутствовать, процесс должен быть запущен, если нет -- создать,
запустить)\
`Ensure = Absent` настройка должна быть выключена (каталога быть не
должно, процесс не должен быть запущен, если нет -- удалить, остановить)

``` powershell
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
#       WindowsFeature IIS
#       {
#            Ensure = "Present"
#            Name = "Web-Server"
#       }
    }
}
```

`$Path = (DSConfigurationProxy).DirectoryName`\
`Test-DscConfiguration -Path $Path | select *` ResourcesInDesiredState -
уже настроено, ResourcesNotInDesiredState - не настроено (не
соответствует)\
`Start-DscConfiguration -Path $Path`\
`Get-Job`\
`$srv = "vproxy-01"`\
`Get-Service -ComputerName $srv | ? name -match w32time # Start-Service`\
`icm $srv {Get-Process | ? ProcessName -match calc} | ft # Stop-Process -Force`\
`icm $srv {ls C:\ | ? name -match Temp} | ft` 

```powershell
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

`$Path = (InstallPowerShellCore).DirectoryName`\
`Test-DscConfiguration -Path $Path`\
`Start-DscConfiguration -Path $path -Wait -Verbose`\
`Get-Job`

## PSAppDeployToolkit

### Install-DeployToolkit

``` powershell
$githubRepository = "psappdeploytoolkit/psappdeploytoolkit"
$filenamePatternMatch = "PSAppDeployToolkit*.zip"
$psadtReleaseUri = "https://api.github.com/repos/$githubRepository/releases/latest"
$psadtDownloadUri = ((Invoke-RestMethod -Method GET -Uri $psadtReleaseUri).assets | Where-Object name -like $filenamePatternMatch ).browser_download_url
$zipExtractionPath = Join-Path $env:USERPROFILE "Downloads" "PSAppDeployToolkit"
$zipTempDownloadPath = Join-Path -Path $([System.IO.Path]::GetTempPath()) -ChildPath $(Split-Path -Path $psadtDownloadUri -Leaf)
### Download to a temporary folder
Invoke-WebRequest -Uri $psadtDownloadUri -Out $zipTempDownloadPath
### Remove any Zone.Identifier alternate data streams to unblock the file (if required)
Unblock-File -Path $zipTempDownloadPath
New-Item -Type Directory $zipExtractionPath
Expand-Archive -Path $zipTempDownloadPath -OutputPath $zipExtractionPath -Force
Write-Host ("File: {0} extracted to Path: {1}" -f $psadtDownloadUri, $zipExtractionPath) -ForegroundColor Yellow
Remove-Item $zipTempDownloadPath
```

### Deploy-Notepad-Plus-Plus

`$url_notepad = "https://github.com/notepad-plus-plus/notepad-plus-plus/releases/download/v8.6.6/npp.8.6.6.Installer.x64.exe"`\
`Invoke-RestMethod $url_notepad -OutFile "$home\Downloads\PSAppDeployToolkit\Toolkit\Files\npp.8.6.6.Installer.x64.exe"`

``` powershell
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

### Uninstall-Notepad-Plus-Plus

``` powershell
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

### Deploy-WinSCP

``` powershell
$PSAppDeployToolkit = "$home\Downloads\PSAppDeployToolkit\"
$version = "6.3.3"
$url_winscp = "https://cdn.winscp.net/files/WinSCP-$version.msi?secure=P2HLWGKaMDigpDQw-H9BgA==,1716466173"
$WinSCP_Template = Get-Content "$PSAppDeployToolkit\Examples\WinSCP\Deploy-Application.ps1" # читаем пример конфигурации для WinSCP
$WinSCP_Template_Latest = $WinSCP_Template -replace "6.3.2","$version" # обновляем версию на актуальную
$WinSCP_Template_Latest > "$PSAppDeployToolkit\Toolkit\Deploy-Application.ps1" # заменяем скрипт развертывания 
Invoke-RestMethod $url_winscp -OutFile "$PSAppDeployToolkit\Toolkit\Files\WinSCP-$version.msi" # загружаем msi-пакет
powershell -File "$PSAppDeployToolkit\Toolkit\Deploy-Application.ps1" # запускаем установку
```

## Pester

[Source](https://github.com/pester/Pester)

`Install-Module -Name Pester -Repository PSGallery -Force -AllowClobber`\
`Import-Module Pester`\
`$(Get-Module Pester -ListAvailable).Version`

`.Tests.ps1`

``` powershell
function Add-Numbers {
    param (
        [int]$a,
        [int]$b
    )
    $a + $b
}
Describe "Add-Numbers" {
    Context "При сложении двух чисел" {
        It "Должна вернуться правильная сумма" {
            $result = Add-Numbers -a 3 -b 4
            $result | Should -Be 7
        }
    }
    Context "При сложении двух чисел" {
        It "Должна вернуться ошибка (5+0 -ne 4)" {
            $result = Add-Numbers -a 5 -b 0
            $result | Should -Be 4
        }
    }
}

function Get-RunningProcess {
    return Get-Process | Select-Object -ExpandProperty Name
}
Describe "Get-RunningProcess" {
    Context "При наличии запущенных процессов" {
        It "Должен возвращать список имен процессов" {
            $result = Get-RunningProcess
            $result | Should -Contain "svchost"
            $result | Should -Contain "explorer"
        }
    }
    Context "Когда нет запущенных процессов" {
        It "Должен возвращать пустой список" {
            # Замокать функцию Get-Process, чтобы она всегда возвращала пустой список процессов
            Mock Get-Process { return @() }
            $result = Get-RunningProcess
            $result | Should -BeEmpty
        }
    }
}
```
