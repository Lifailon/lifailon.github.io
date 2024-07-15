---
title: "SCM"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

## Git

`git --version` 
`git config --global user.name "Lifailon"` добавить имя для коммитов 
`git config --global user.email "lifailon@yandex.ru"` 
`git config --global --edit` 
`ssh-keygen -t rsa -b 4096` 
`Get-Service | where name -match "ssh-agent" | Set-Service -StartupType Automatic` 
`Get-Service | where name -match "ssh-agent" | Start-Service` 
`Get-Service | where name -match "ssh-agent" | select Name,Status,StartType` 
`ssh-agent` 
`ssh-add C:\Users\Lifailon\.ssh\id_rsa` 
`cat ~\.ssh\id_rsa.pub | Set-Clipboard` copy to [settings keys](https://github.com/settings/keys) 
`cd $home\Documents\Git` 
`git clone git@github.com:Lifailon/lifailon.github.io` 
`cd lifailon.github.io` 
`git grep "ping ya.ru"` поиск текста в файлах 
`git fetch` загрузить изменения из удаленного хранилища для обновления всех веток локального репозитория, не затрагивая текущую рабочую ветку (загружает все коммиты, ветки и т.д. которые не присутствуют в локальном репозитории) 
`git fetch --all` загрузить все ветки с удаленного репозитория (обновляет информацию о состоянии удаленного репозитория и загружает все изменения ваших веток без автоматического объединения) 
`git pull` загрузить изменения из удаленного хранилища для обновления локального репозитория (выполняет git fetch, чтобы получить последние изменения из удаленного репозитория, а затеим объеденяем изменения с локальной копией с помощью git merge для обновления текущей рабочей ветки) 
`git status` отобразить статус изменений по файлам 
`git diff` отобразить историю изменений построчно 
`git diff pandoc` сравнивает изменения в текущей рабочей директории с последним коммитом в указанной ветке 
`git add .` добавить (проиндексировать) изменения во всех файлах текущего каталога 
`git commit -m "update powershell commands"` сохранить изменения с комментарием 
`git push` синхронизировать локальные изменения с репозиторием на сервере 
`git push origin mkdocs-material` отправить в конкретную ветку 
`git push origin --delete mkdocs` удалить ветку на удаленном сервере 
`git branch -a` отобразить все ветки (в том числе удаленные remotes/origin) 
`git branch hugo` создать новую ветку 
`git branch -m hugo-public` переименовать текущую ветку 
`git branch -d hugo-public` удалить ветку 
`git switch hugo` переключиться на другую ветку 
`git push origin hugo` отправить изменения в указанную ветку 
`git push --set-upstream origin hugo` отслеживать изменения в ветке (позволяет делать push без указания ветки) 
`git merge hugo` слияние текущей ветки (pandoc) с указанной (hugo) 
`git log --oneline --all` лог коммитов 
`git log --graph` коммиты и следование веток 
`git log --author="Lifailon"` показывает историю коммитов указанного пользователя 
`git blame index.html` показывает, кто и когда внес изменения в каждую строку указанного файла 
`git show d01f09dead3a6a8d75dda848162831c58ca0ee13` отобразить подробный лог по номеру коммита 
`git restore filename` отменить все локальные изменения в рабочей копии независимо от того, были они проиндексированы или нет (если была индексация через add), возвращая его к состоянию на момент последнего коммита 
`git restore --source d01f09dead3a6a8d75dda848162831c58ca0ee13 filename` восстановить файл на указанную версию по хэшу индентификатора коммита 
`git checkout filename` откатить изменения не проиндексированные для коммита, возвращая его к состоянию, каким оно было на момент последнего коммита (если не было индексации через add) 
`git checkout d01f09dead3a6a8d75dda848162831c58ca0ee13` переключить локальные файлы рабочей копии на указанный коммит (переключает HEAD на указанный коммит) 
`git reset HEAD filename` удалить указанный файл из индекса без удаления самих изменений в файле для последующей повторной индексации (если был add но не было commit, потом выполнить checkout) 
`git reset --soft HEAD^` отменяет последний (^) коммит, сохраняя изменения из этого коммита в рабочем каталоге и индексе (подготовленной области), можно внести изменения в файлы и повторно их зафиксировать 
`git reset --hard HEAD^` полностью отменяет последний коммит, удаляя все его изменения из рабочего каталога и индекса до состояния последнего коммита 
`git reset --hard d01f09dead3a6a8d75dda848162831c58ca0ee13` откатывает HEAD к указанному коммиту и удаляет все коммиты, которые были сделаны после него (будут потеряны все незакоммиченные изменения и историю коммитов после указанного) 
`git revert HEAD --no-edit` создает новый коммит, который отменяет последний коммит (HEAD) и новый коммит будет добавлен поверх него (события записываются в git log) 
`git revert d01f09dead3a6a8d75dda848162831c58ca0ee13` создает новый коммит, который отменяет изменения, внесенные в указанный коммит с хешем (не изменяет историю коммитов, а создает новый коммит с изменениями отмены) 
`git stash` сохраняет текущие изменения в стэш (временное хранилище) и очищает рабочую директорию 
`git stash pop` применяет последние изменения из стэша к текущей ветке

## GitHub-api

`$user = "Lifailon"` 
`$repository = "ReverseProxyNET"` 
`Invoke-RestMethod https://api.github.com/users/$($user)` получаем информацию о пользователе 
`Invoke-RestMethod https://api.github.com/users/$($user)/repos` получаем список последних (актуальные коммиты) 30 репозиториев указанного пользователя 
`Invoke-RestMethod https://api.github.com/users/$($user)/repos?per_page=100` получаем список последних (актуальные коммиты) 100 репозиториев указанного пользователя 
`Invoke-RestMethod https://api.github.com/repos/$($user)/$($repository)/contents` получаем содержимое корневой директории репозитория 
`Invoke-RestMethod https://api.github.com/repos/$($user)/$($repository)/contents/source/rpnet.cs` получаем содержимое файла в формате Base64 
`$commits = Invoke-RestMethod https://api.github.com/repos/$($user)/$($repository)/commits` получаем список коммитов 
`$commits[0].commit.message` читаем комментарий последнего коммита 
`$commits[0].commit.committer.date` получаем дату последнего коммита 
`Invoke-RestMethod https://api.github.com/repos/$($user)/$($repository)/commits/$($commits[0].sha)` получаем подробную информацию изменений о последнем коммите в репозитории 
`$releases_latest = Invoke-RestMethod "https://api.github.com/repos/$($user)/$($repository)/releases/latest"` получаем информацию о последнем релизе в репозитории 
`$releases_latest.assets.name` список приложенных файлов последнего релиза 
`$releases_latest.assets.browser_download_url` получаем список url для загрузки файлов 
`$($releases_latest.assets | Where-Object name -like "*win*x64*exe*").browser_download_url` фильтруем по ОС и разрядности 
`$(Invoke-RestMethod -Uri "https://api.github.com/repos/Lifailon/epic-games-radar/commits?path=api/giveaway/index.json")[0].commit.author.date` узнать дату последнего обновления файла в репозитории 
`$issues = Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/issues?per_page=500` получаем список открытых проблем в репозитории (получаем максимум 100 последних, по умолчанию забираем последние 30 issues) 
`$issue_number = $($issues | Where-Object title -match "PowerShell").number` получаем номер issue, в заголовке которого есть слово "PowerShell" 
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/issues/$($issue_number)/comments` отобразить список комментарием указанного issues 
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/languages` получаем список языков программирования, используемых в репозитории 
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/pulls` получаем список всех pull requests в репозитории 
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/forks` получаем список форков (forks) 
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/stargazers?per_page=4000` получаем список пользователей, которые поставили звезды репозиторию 
`Invoke-RestMethod https://api.github.com/repos/LibreHardwareMonitor/LibreHardwareMonitor/subscribers` получаем список подписчиков (watchers) репозитория

## Atlassian

### Bitbucket
```PowerShell
$url = "https://github.com/AtlassianPS/BitbucketPS/archive/refs/heads/master.zip"
Invoke-RestMethod $url -OutFile $home\Downloads\BitbucketPS.zip
Expand-Archive -Path "$home\Downloads\BitbucketPS.zip" -OutputPath "$home\Downloads"
Copy-Item -Path "$home\Downloads\BitbucketPS-master\*" -Destination "$($env:PSModulePath.Split(";")[0])\PSBitBucket" -Recurse
Remove-Item "$home\Downloads\Bitbucket*" -Recurse -Force
```
`Import-Module PSBitBucket` 
`Get-Command -Module PSBitBucket` 
`Set-BitBucketConfigServer -Url $url -User username -Password password` установить конфигурацию сервера BitBucket 
`Get-BitBucketConfigServer` получить текущую конфигурацию сервера BitBucket 
`Get-Repositories` получить список всех репозиториев для текущей конфигурации сервера BitBucket 
`Get-ProjectKey` получить ключ проекта BitBucket 
`Get-BranchList -Repository pSyslog` список всех веток в репозитории 
`Get-Branch -Repository pSyslog -Branch main` получить информацию о конкретной ветке репозитория 
`Get-CommitMessage -Repository pSyslog -CommitHash $hash` получить сообщение коммита по его хэшу 
`Get-Commits -Repository pSyslog -Limit 10` список последних 10 коммитов в репозитории 
`Get-CommitsForBranch -Repository pSyslog -Branch main` список коммитов для конкретной ветки в репозитории

### Jira

`Install-Module JiraPS -Scope CurrentUser -Repository PSGallery -AllowClobber -Force` 
`Get-Command -Module JiraPS` 
`Get-JiraServerInfo` информация о сервере 
`Add-JiraFilterPermission` добавить разрешения для фильтра 
`Add-JiraGroupMember` добавить участника в группу 
`Add-JiraIssueAttachment` добавить вложения к задаче 
`Add-JiraIssueComment` добавить комментария к задаче 
`Add-JiraIssueLink` добавить ссылки на задачу 
`Add-JiraIssueWatcher` добавить наблюдателя к задаче 
`Add-JiraIssueWorklog` добавить рабочего журнала к задаче 
`Find-JiraFilter` поиск фильтра 
`Format-Jira` форматирование данных Jira 
`Get-JiraComponent` получение компонента проекта 
`Get-JiraConfigServer` получение конфигурации сервера Jira 
`Get-JiraField` получение поля Jira 
`Get-JiraFilter` получение фильтра 
`Get-JiraFilterPermission` получение разрешения фильтра 
`Get-JiraGroup` получение группы 
`Get-JiraGroupMember` получение участников группы 
`Get-JiraIssue` получение задачи 
`Get-JiraIssueAttachment` получение вложения задачи 
`Get-JiraIssueAttachmentFile` получение файла вложения задачи 
`Get-JiraIssueComment` получение комментария задачи 
`Get-JiraIssueCreateMetadata` получение метаданных создания задачи 
`Get-JiraIssueEditMetadata` получение метаданных редактирования задачи 
`Get-JiraIssueLink` получение ссылки задачи 
`Get-JiraIssueLinkType` получение типа ссылки задачи 
`Get-JiraIssueType` получение типа задачи 
`Get-JiraIssueWatcher` получение наблюдателя задачи 
`Get-JiraIssueWorklog` получение рабочего журнала задачи 
`Get-JiraPriority` получение приоритета задачи 
`Get-JiraProject` получение проекта 
`Get-JiraRemoteLink` получение удаленной ссылки 
`Get-JiraServerInformation` получение информации о сервере Jira 
`Get-JiraSession` получение сессии 
`Get-JiraUser` получение пользователя 
`Get-JiraVersion` получение версии проекта 
`Invoke-JiraIssueTransition` выполнение перехода задачи 
`Invoke-JiraMethod` выполнение метода Jira 
`Move-JiraVersion` перемещение версии проекта 
`New-JiraFilter` создание нового фильтра 
`New-JiraGroup` создание новой группы 
`New-JiraIssue` создание новой задачи 
`New-JiraSession` создание новой сессии 
`New-JiraUser` создание нового пользователя 
`New-JiraVersion` создание новой версии проекта 
`Remove-JiraFilter` удаление фильтра 
`Remove-JiraFilterPermission` удаление разрешения фильтра 
`Remove-JiraGroup` удаление группы 
`Remove-JiraGroupMember` удаление участника группы 
`Remove-JiraIssue` удаление задачи 
`Remove-JiraIssueAttachment` удаление вложения задачи 
`Remove-JiraIssueLink` удаление ссылки задачи 
`Remove-JiraIssueWatcher` удаление наблюдателя задачи 
`Remove-JiraRemoteLink` удаление удаленной ссылки 
`Remove-JiraSession` удаление сессии 
`Remove-JiraUser` удаление пользователя 
`Remove-JiraVersion` удаление версии проекта 
`Set-JiraConfigServer` установка конфигурации сервера Jira 
`Set-JiraFilter` установка фильтра 
`Set-JiraIssue` установка задачи 
`Set-JiraIssueLabel` установка метки задачи 
`Set-JiraUser` установка пользователя 
`Set-JiraVersion` установка версии проекта

### Confluence

`Install-Module ConfluencePS -Scope CurrentUser -Repository PSGallery -AllowClobber -Force` 
`Get-Command -Module ConfluencePS` 
`Add-ConfluenceAttachment` добавить вложения к странице 
`Add-ConfluenceLabel` добавить метки к странице 
`ConvertTo-ConfluenceStorageFormat` конвертация содержимого в формат хранения Confluence 
`ConvertTo-ConfluenceTable` конвертация данных в таблицу Confluence 
`Get-ConfluenceAttachment` получение вложения страницы 
`Get-ConfluenceAttachmentFile` получение файла вложения страницы 
`Get-ConfluenceChildPage` получение дочерних страниц 
`Get-ConfluenceLabel` получение меток страницы 
`Get-ConfluencePage` получение информации о странице 
`Get-ConfluenceSpace` получение информации о пространстве 
`Invoke-ConfluenceMethod` выполнение метода Confluence 
`New-ConfluencePage` создание новой страницы 
`New-ConfluenceSpace` создание нового пространства 
`Remove-ConfluenceAttachment` удаление вложения страницы 
`Remove-ConfluenceLabel` удаление метки со страницы 
`Remove-ConfluencePage` удаление страницы 
`Remove-ConfluenceSpace` удаление пространства 
`Set-ConfluenceAttachment` установка вложения страницы 
`Set-ConfluenceInfo` установка информации о странице 
`Set-ConfluenceLabel` установка метки страницы 
`Set-ConfluencePage` установка страницы
