+++
title = "DevOps"
[extra]
toc = true
toc_sidebar = true
go_to_top = true
+++

<p align="center">
    <a href="https://github.com/Lifailon/rudocs/tree/main/devops.md"><img title="DevOps Tools"src="logo.png"></a>
</p>

<p align="center">
    Заметки по инструментам направления <b>DevOps</b>.
</p>

---
{{ end }}
```
### Define

Используются как как функции, позволяя не дублировать код и поддерживают глобальную область видимости. Они объявляются в любом манифесте из директории `templates` или в специальном файле шаблона (например, `_helpers.tpl`).

Переменные в файле `Values`:
```yaml
app:
  image: app
  tag: "v1.2.3"
```
Объявляем `define`:
```yaml
{{- define "app.image" -}}
{{- $image := .Values.app.image -}}
{{- $tag := .Values.app.tag | default .Chart.AppVersion -}}
{{- printf "%s:%s" $image $tag -}}
{{- end -}}
```
Используем в любом манифесте:
```yaml
spec:
  containers:
    - name: web
      image: {{ include "app.image" . }}
```
## GitHub API

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

## GitHub Actions

### Docker Build and Push

Сборка Docker образа из указанного коммита и публикация указанной версии на [Docker Hub](https://hub.docker.com).
```yaml
name: Docker build and push from specified commit

on:
  workflow_dispatch:
    inputs:
      # Принимает два строковых параметра
      Commit:
        description: "Commit for git checkout"
        required: true
        default: ""
      Version:
        description: "Version for docker tag"
        required: true
        default: ""

jobs:
  build:
    name: Docker build on ubuntu-latest

    runs-on: ubuntu-latest

    env:
      REPO_NAME: 'lazyjournal'

    steps:
      # Клонируем репозиторий (ветку main и историю всех комиттов)
      - name: Checkout repository (main branch and all commits)
        uses: actions/checkout@v4
        with:
          fetch-depth: 0
          ref: main

      # Переключаем состояние истории репозитория на указанный коммит
      - name: Checkout the specified commit
        run: git checkout ${{ github.event.inputs.Commit }}

      # Авторизуемся
      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          # Предварительно создаем секреты в https://github.com/<userName>/<repoName>/settings/secrets/actions
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      # Устанавливаем плагин buildx для мультиархитектурной сборки (amd64 и arm64)
      - name: Install Docker Buildx
        uses: docker/setup-buildx-action@v3
        with:
          driver: docker-container
          install: true

      # Собираем и публикуем одной командой (используем тег из параметра)
      - name: Build and push Docker images for Linux on amd64 and arm64
        run: |
          version=${{ github.event.inputs.Version }}
          docker buildx build \
            --platform linux/amd64,linux/arm64 \
            -t ${{ secrets.DOCKER_USERNAME }}/${{ env.REPO_NAME }}:$version \
            --push .
```
### Dockerfile Linters Check

Проверка Dockerfile на [базовые линтеры](https://docs.docker.com/reference/build-checks) и с помощью инструмента [Hadolint](https://github.com/hadolint/hadolint).

🔗 [Hadolint Playground](https://hadolint.github.io/hadolint) ↗


```yaml
name: Dockerfile linters check

on:
  workflow_dispatch:
    # Принимает два булевых параметра
    inputs:
      Lint:
        description: 'Dockerfile basic linters check'
        default: false
        type: boolean
      Hadolint:
        description: 'Dockerfile hadolint check'
        default: false
        type: boolean

jobs:
  build:
    name: Docker build on ubuntu-latest

    runs-on: ubuntu-latest

    steps:
      # Клонируем репозиторий (ветку main и последний коммит)
      - name: Checkout repository (main branch and all commits)
        id: dockerPull
        uses: actions/checkout@v4
        with:
          fetch-depth: 1
          ref: main

      - name: Dockerfile basic linters check
        # Проверка условия перед выполнения шага
        if: ${{ github.event.inputs.Lint == 'true' }}
        # Определяем индивидуальный идентификатор для извлечения данных из шага
        id: lint
        run: docker build --check --build-arg "BUILDKIT_DOCKERFILE_CHECK=experimental=all" .

      - name: Dockerfile hadolint check
        if: ${{ github.event.inputs.Hadolint == 'true' }}
        id: hadolint
        run: docker run --rm -i hadolint/hadolint:latest < Dockerfil
```
### Telegram Notification

Отправка уведомлений в Telegram с помощью [Telegram Actions](https://github.com/appleboy/telegram-action).

```yaml
- name: Send report to Telegram
  if: ${{ github.event.inputs.Lint == 'true' || github.event.inputs.Hadolint == 'true' }}
  uses: appleboy/telegram-action@master
  with:
    token: ${{ secrets.TELEGRAM_API_TOKEN }}
    to: ${{ secrets.TELEGRAM_CHANNEL_ID }}
    debug: true
    format: markdown
    message: |
      🔔 **Action**: Docker linters check

      📁 **Repository**: ${{ github.repository }}
      👤 **User**: ${{ github.actor }}
      
      ${{ steps.lint.outcome == 'failure' && '❌' || '✅' }} **Dockerfile basic linters check**: ${{ steps.lint.outcome }}
      ${{ steps.hadolint.outcome == 'failure' && '❌' || '✅' }} **Dockerfile hadolint check**: ${{ steps.hadolint.outcome }}
```
### AI Issue Analysis

Анализ Issues с использованием [AI Inference](https://github.com/actions/ai-inference) и автоматическим ответом в комментариях.
```yaml
name: AI Issue Analysis

on:
  issues:
    types: [opened, closed, reopened]
  issue_comment:
    types: [created]

run-name: "Issue #${{ github.event.issue.number }}: ${{ github.event.issue.title }}"

jobs:
  issue_analysis:
    permissions:
      issues: write
      models: read

    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository (main branch and 1 last commits)
        uses: actions/checkout@v4
        with:
          fetch-depth: 1
          ref: main

      # Отправляем оповещение в Telegram с темой и содержимым проблемы
      # Срабатываем на открытие, закрытие и комментарии в задаче
      - name: Send message to Telegram
        uses: appleboy/telegram-action@master
        with:
          token: ${{ secrets.TELEGRAM_API_TOKEN }}
          to: ${{ secrets.TELEGRAM_CHANNEL_ID }}
          debug: true
          format: markdown
          message: |
            🔔 **Action**: ${{ github.event_name }} ${{ github.event.action }} [#${{ github.event.issue.number }}](${{ github.event.comment.html_url || github.event.issue.html_url }})

            📁 **Repository**: ${{ github.repository }}
            👤 **From user**: ${{ github.actor }}

            📌 **Title**: ${{ github.event.issue.title }}
            💬 **Description**:
            ${{ github.event.comment.body || github.event.issue.body }}

      # Генерируем отчет (только при открытие новой проблемы)
      - name: Generate report using AI
        if: ${{ github.event_name == 'issues' && github.event.action == 'opened' }}
        id: ai_query
        uses: actions/ai-inference@v2
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          endpoint: https://models.github.ai/inference
          model: gpt-4.1
          max-tokens: 1024
          system-prompt: |
            Ты консультант разработчика.
            Твоя задача анализировать проблемы (issues) на GitHub и предлагать решения.
            Твои ответы должны быть краткими и на английском языке.
          prompt: |
            Title: ${{ github.event.issue.title }}
            Description: ${{ github.event.issue.body }}

      # Постим комментарий от AI в ответ на Issue
      - name: Post comment from AI
        if: ${{ github.event_name == 'issues' && github.event.action == 'opened' && steps.ai_query.outputs.response != '' }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          ISSUE_NUMBER: ${{ github.event.issue.number }}
          AI_RESPONSE: ${{ steps.ai_query.outputs.response }}
        run: |
          gh pr comment "$PR_NUMBER" --body "### AI Issue Analysis
          
          $AI_RESPONSE"
```
### Go Build and Testing

Процесс тестирования и сборки Go приложений с выгрузкой артифактов.
```yaml
name: CI (Build and Testing)

on:
  workflow_dispatch:
    inputs:
      # Параметр выпадающего списка для выбора
      # Список доступных дистрибутивов на публичных сборщиках: https://github.com/actions/runner-images
      Distro:
        description: 'Select runner image'
        required: true
        default: 'ubuntu-24.04'
        type: choice
        options:
          - 'ubuntu-22.04'
          - 'ubuntu-24.04'
          - 'macos-26'
          - 'macos-15'
          - 'windows-2025'
          - 'windows-2022'
      # Обновление зависимостей
      Update:
        description: 'Update dependencies'
        default: false
        type: boolean
      # Статическая проверка кода
      Linters:
        description: 'Go linters check'
        default: false
        type: boolean
      # Запуск unit тестов
      Test:
        description: 'Go unit testing'
        default: false
        type: boolean
      # Сборка
      Binary:
        description: 'Build binary and deb packages'
        default: false
        type: boolean

# Определяем индивидуальное название для каждой сборки
run-name: "Build #${{ github.run_number }} on ${{ github.event.inputs.Distro }}"

jobs:
  test:
    name: Testing on ${{ github.event.inputs.Distro }}

    runs-on: ${{ github.event.inputs.Distro }}

    # Объявляем переменные окружения, которые будут использоваться в процессе сборки
    env:
      APP_NAME: 'app'
      APP_VERSION: 'latest'
      COVERAGE: 'n/a'

    steps:
      - name: Checkout repository (main branch and 1 last commits)
        uses: actions/checkout@v4
        with:
          fetch-depth: 1
          ref: main

      # Установка Go указанной версии
      - name: Install Go
        uses: actions/setup-go@v5
        with:
          go-version: 1.25

      # Форматирование, статический анализ, установка зависимостей и проверка компиляции
      - name: Install dependencies
        run: |
          go fmt ./...
          go vet ./...
          go get ./...
          go mod tidy
          go mod verify
          go build -v ./...

      # Извлекаем версию приложения и сохраняем ее в переменные окружения для использования в других шагах
      - name: Get app version in gh env for build
        run: |
          version=$(go run main.go -v)
          echo "APP_VERSION=$version" >> $GITHUB_ENV

      # Обновление зависимостей в go.mod
      - name: Update dependencies
        if: ${{ github.event.inputs.Update == 'true' }}
        run: go get -u ./...

      # Устанавливаем пакет golangci-lint и запуск анализа кода
      - name: Golangci linters check
        if: ${{ github.event.inputs.Linters == 'true' }}
        # Исключаем падение всего шага при ошибке (last exit code != 0)
        continue-on-error: true
        run: |
          go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest
          golangci-lint run -v ./main.go

      # Запуск unit тестов и записываем результат в лог файл
      - name: Unit testing
        if: ${{ github.event.inputs.Test == 'true' }}
        # Ограничиваем время выполнения
        timeout-minutes: 10
        continue-on-error: true
        run: sudo env "PATH=$PATH" go test -v -cover | tee test.log

      # Извлекаем результат покрытия кода и публикуем на страницу сборки в формате Markdown
      - name: Unit testing
        if: ${{ github.event.inputs.Test == 'true' }}
        continue-on-error: true
        run: |
          COVERAGE=$(cat test.log | tail -n 2 | head -n 1 | sed "s/coverage: //" | sed -E "s/of.+//g")
          echo "## Test results" >> $GITHUB_STEP_SUMMARY
          echo -e "Version: $APP_VERSION" >> $GITHUB_STEP_SUMMARY
          echo -e "Coverage: $COVERAGE" >> $GITHUB_STEP_SUMMARY

      # Собираем приложение для разных ОС и архитектур
      - name: Build binaries
        if: ${{ github.event.inputs.Binary == 'true' }}
        run: |
          mkdir -p bin
          architectures=("amd64" "arm64")
          oss=("linux" "darwin" "openbsd" "freebsd" "windows")
          for arch in "${architectures[@]}"; do
              for os in "${oss[@]}"; do
                  binName="bin/$APP_NAME-$APP_VERSION-$os-$arch"
                  if [[ "$os" == "windows" ]]; then
                      binName="$binName.exe"
                  fi
                  CGO_ENABLED=0 GOOS=$os GOARCH=$arch go build -o "$binName"
              done 
          done
          ls -lh bin
          # Формируем названия архива для выгрузки артефактов
          echo "ARTIFACT_NAME=$APP_NAME-$APP_VERSION" >> $GITHUB_ENV

      # Выгружаем все файлы из директории bin в артефакты GitHub
      - name: Upload binaries
        if: ${{ github.event.inputs.Binary == 'true' }}
        uses: actions/upload-artifact@v4
        with:
          name: ${{ env.ARTIFACT_NAME }}
          path: bin/
        env:
          ARTIFACT_NAME: ${{ env.ARTIFACT_NAME }}
```
### Ubuntu PPA Repository

Создание и подготовка PPA (Personal Package Archives) репозитория для публикации `deb` пакетов.

Генерируем ключ:

`gpg --gen-key`

Получить путь к `pubring.kbx`, а также спислк всех ключей с их отпечаток и сроком действия:

`gpg --list-secret-keys --keyid-format=long`

Получить приватный ключ по email или отпечатку:

`gpg --armor --export-secret-keys lifailon@main.com` \

Отправляем ключ на сервер ключей Ubuntu:

`gpg --keyserver keyserver.ubuntu.com --send-keys <ОТПЕЧАТОК>`

Поиск ключа на сервере ключей: https://keyserver.ubuntu.com

Импортируем ключ: https://launchpad.net/~lifailon/+editpgpkeys \
Выбираем: `Import an OpenPGP key` \
Вставляем отпечаток в `Fingerprint` и нажимаем `Import Key` \
Дожидаемся сообщение на почту, указанную в ключе

Расшифровать содержимое содержимое `pgp` сообщения в формате:
```bash
echo '-----BEGIN PGP MESSAGE-----
-----END PGP MESSAGE-----' > pgp.txt
```
`gpg --decrypt pgp.txt` \
Переходим по полученной ссылке после расшифровки и подтверждаем добавление ключа.

Загружаем Ubuntu Codes of Conduct последней версии: https://launchpad.net/codeofconduct/2.0

Подписываем кодекс поведения:

`gpg -u A60D863D --clearsign UbuntuCodeofConduct-2.0.txt` \
`cat UbuntuCodeofConduct-2.0.txt.asc` \
Переходим по ссылке: https://launchpad.net/codeofconduct \
Нажимаем `Sign it!` и вставляем содержимое `UbuntuCodeofConduct-2.0.txt.asc`

После этих действий создаем PPA репозиторий.

Идем в настройки репозитория: \
https://launchpad.net/~<userName>/+archive/ubuntu/<ppaName>/+edit \
Добавляем `amd64` и `arm64` в `Processors` для мультиархитектурной сборки

### Ubuntu Build and Pfush

Использование данного подхода может сэкономить много времени на подготовке к сборке и публикации Go приложения в PPA.

```yaml
name: Build deb package and push to PPA

on:
  workflow_dispatch:
    inputs:
      # Дистрибутив на котором будет производиться сборка
      # Это влияет на название версии дистрибутива Ubuntu в файле changelog по умолчанию
      Runner:
        description: 'Select runner image'
        required: true
        default: 'ubuntu-22.04'
        type: choice
        options:
          - 'ubuntu-24.04' # noble
          - 'ubuntu-22.04' # jammy
      # Название дистрибутива Ubuntu для обновления в файле changelog
      Distro:
        description: 'Ubuntu distro name in changelog'
        required: true
        default: 'jammy'
        type: choice
        options:
          - 'resolute' # 26.04
          - 'questing' # 25.10
          - 'noble'    # 24.04
          - 'jammy'    # 22.04
          - 'focal'    # 20.04
          - 'bionic'   # 18.04
          - 'xenial'   # 16.04
          - 'trusty'   # 14.04
      # Обновить версию в changelog (для пересборок)
      Version:
        description: 'Version for build'
        required: false
        default: '' # 0.8.4 -> 0.8.4.1 -> 0.8.4.2
      # Публикация в репозитория PPA
      Push:
        description: 'Push to PPA'
        default: false
        type: boolean
      # Запустить только установку
      Install:
        description: 'Check install only'
        default: false
        type: boolean

jobs:
  test:
    name: Build deb package and push to PPA

    runs-on: ${{ github.event.inputs.Runner }}

    steps:
      - name: Checkout repository (main branch and 1 last commits)
        uses: actions/checkout@v4
        with:
          fetch-depth: 1
          ref: main

      # Устанавливаем зависимости для сборки
      - name: Install package tools
        if: ${{ github.event.inputs.Install != 'true' }}
        run: |
          sudo apt-get update
          sudo apt-get install -y devscripts debhelper dh-make-golang dput

      # Импортируем GPG ключ из секретов в систему
      - name: Import GPG key
        if: ${{ github.event.inputs.Install != 'true' }}
        uses: crazy-max/ghaction-import-gpg@v6
        with:
          gpg_private_key: ${{ secrets.PPA_GPG_PRIVATE_KEY }}

      - name: Build deb package and publish to Launchpad PPA
        if: ${{ github.event.inputs.Install != 'true' }}
        run: |
          # Объявляем переменные для сборки
          export DEBFULLNAME="lifailon"
          # Email из gpg ключа
          export DEBEMAIL="${{ secrets.PPA_EMAIL }}"
          
          # Генерируем Debia шаблоны (структуру файлов и каталогов) для Go приложения
          echo -e "\n\033[33m>>> Debian template generation\033[0m\n"
          dh-make-golang make github.com/Lifailon/lazyjournal
          cd lazyjournal

          # Загружаем все зависимости (используемые пакеты) и обновляем архив
          # Для сборки без интернета на серверах Ubuntu
          echo -e "\n\033[33m>>> Download dependencies for offline build\033[0m\n"
          go mod vendor
          VERSION=$(ls ../*.orig.tar.gz | sed -E 's/.*_([0-9.]+)\.orig\.tar\.gz/\1/')
          rm ../*.orig.tar.gz
          tar --exclude-vcs -C .. -czf ../lazyjournal_$VERSION.orig.tar.gz lazyjournal
          sed -i '1a export GOFLAGS=-mod=vendor' debian/rules

          # Обновляем версию Go на актуальную во избежание ошибок при сборке
          echo -e "\n\033[33m>>> Update go version\033[0m\n"
          sed -i 's/golang-any/golang-1.23-go/' debian/control
          # Экспортируем путь к Go в основной Makefile для сборки
          sed -i '1a export PATH := /usr/lib/go-1.23/bin:$(PATH)' debian/rules

          # Обновляем секцию TODO в файл debian control для избежания ошибок
          echo -e "\n\033[33m>>> Update debian control\033[0m\n"
          sed -i 's/Section: TODO/Section: utils/' debian/control
          # Используем мультиархитектурную сборку
          sed -i -E 's/Architecture:.+/Architecture: any/' debian/control

          # Пропускаем встроенные тесты и dwz проверки
          echo -e "\n\033[33m>>> Skip go test and dwz in build\033[0m\n"
          # export DEB_BUILD_OPTIONS=nocheck
          printf "\noverride_dh_auto_test:\n\t:\n" >> debian/rules
          printf "\noverride_dh_dwz:\n\t:\n" >> debian/rules
          echo -e "\n\033[33m>>> Rules\033[0m\n"
          cat debian/rules

          # Обновляем название дистрибутива
          echo -e "\n\033[33m>>> Update distro name in changelog\033[0m\n"
          sed -i "1s/)[[:space:]]\+[^;]\+;/) ${{ github.event.inputs.Distro }};/" debian/changelog
          sed -i 's/(Closes: TODO)//' debian/changelog
          
          # Обновляем версию (если значение определено в параметре)
          if [ -n "${{ github.event.inputs.Version }}" ]; then
            echo -e "\n\033[33m>>> Update version in changelog\033[0m\n"
            VERSION=$(cat debian/changelog | head -n 1 | sed -E "s/.+\(//" | sed -E "s/\).+//" | sed -E "s/-[0-9]+//")
            sed -i -E "s/$VERSION-/${{ github.event.inputs.Version }}-/" debian/changelog
            mv ../lazyjournal_$VERSION.orig.tar.gz ../lazyjournal_${{ github.event.inputs.Version }}.orig.tar.gz
            echo -e "\n\033[33m>>> Changelog\033[0m\n"
            cat debian/changelog
          fi

          # Проверяем сборку (эта команда выполняется на серверах Ubuntu перед публикацией)
          echo -e "\n\033[33m>>> Check build\033[0m\n"
          dpkg-buildpackage -us -uc -b

          # Собираем файл changes
          echo -e "\n\033[33m>>> Build changes\033[0m\n"
          rm -f ../*.upload ../*.changes
          debuild -S -sa -k"$DEBEMAIL"
          cd ..
          ls *.changes

      # Публикуем пакет в репозиторий PPA
      - name: Push to PPA
        if: ${{ github.event.inputs.Install != 'true' && github.event.inputs.Push == 'true' }}
        run: |
          dput ppa:lifailon/lazyjournal ../*.changes

      # Шаг проверки установки пакета
      - name: Check install package
        if: ${{ github.event.inputs.Install == 'true' }}
        run: |
          sudo add-apt-repository -y ppa:lifailon/lazyjournal
          sudo apt update
          apt-cache policy lazyjournal
          sudo apt install -y lazyjournal
          lazyjournal -v
          sudo apt remove -y lazyjournal
```
API запрос для получения версии и создание динамического бейджа в [Shields](https://shields.io/badges/dynamic-json-badge) c помощью json запрос.
```bash
apiUrl="https://api.launchpad.net/1.0/~lifailon/+archive/ubuntu/lazyjournal?ws.op=getPublishedSources&distro_series=https://api.launchpad.net/1.0/ubuntu/jammy&status=Published"
apiQuery="entries[0].source_package_version"
curl -sS "$apiUrl" | jq ".$apiQuery"

# Кодируем URL
ppaUrl=$(jq -rn --arg url "$apiUrl" '$url | @uri')

# Формируем динамический json запрос в Shields для получения бейджа с версией
echo "https://img.shields.io/badge/dynamic/json?url=$ppaUrl&query=$apiQuery&label=Ubuntu+PPA&logo=ubuntu&color=orange"
```
### Repository Dispatch

Repository Dispatch используется для отправки запроса из скрипта или другой системы для запуска Workflow.

Отправляем запрос на сервер:
```bash
userName=Lifailon
repoName=lazyjournal
token=ghp_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

curl -X POST "https://api.github.com/repos/$userName/$repoName/dispatches" \
    -H "Authorization: token $token" \
    -H "Accept: application/vnd.github.v3+json" \
    -d '{
      "event_type": "script_run", 
      "client_payload": {
        "app_version": "0.8.5",
        "run_tests": true
      }
    }'
```
Создаем действие для реакции на событие:
```yaml
name: Webhook Payload

on:
  repository_dispatch:
    # Workflow запустится только если event_type совпадает с одним из списка
    types: [script_run, test]

jobs:
  payload:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Check version
        run: |
          echo "App version: ${{ github.event.client_payload.app_version }}"
          echo "Run tests: ${{ github.event.client_payload.run_tests }}"

```
### Actions API
```PowerShell
$userName = "Lifailon"
$repoName = "lazyjournal"

# Получить количество всех рабочих процессов
$(Invoke-RestMethod https://api.github.com/repos/$userName/$repoName/actions/workflows).total_count
# Подробная информации о запускаемых рабочих процессах
$(Invoke-RestMethod https://api.github.com/repos/$userName/$repoName/actions/workflows).workflows
# Получить идентификатор первого workflow
$workflowId = $(Invoke-RestMethod https://api.github.com/repos/$userName/$repoName/actions/workflows).workflows[0].id
# Подробная информация о последней сборке
$(Invoke-RestMethod https://api.github.com/repos/$userName/$repoName/actions/workflows/$workflowId/runs).workflow_runs
# Получить идентификатор последнего запуска указанного workflow
$lastRunId = $(Invoke-RestMethod https://api.github.com/repos/$userName/$repoName/actions/workflows/$workflowId/runs).workflow_runs.id[0]
# Подробная информация для всех шагов выполнения (время работы и статус выполнения из conclusion)
$(Invoke-RestMethod "https://api.github.com/repos/$userName/$repoName/actions/runs/$lastRunId/jobs").jobs.steps

# Получить идентификатор последнего jobs в конкретной сборке
$lastJobsId = $(Invoke-RestMethod "https://api.github.com/repos/$userName/$repoName/actions/runs/$lastRunId/jobs").jobs[-1].id
# Отобразить логи выполнения указанного задания
$url = "https://api.github.com/repos/$userName/$repoName/actions/jobs/$lastJobsId/logs"
$headers = @{
    Authorization = "token ghp_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
}
Invoke-RestMethod -Uri $url -Headers $headers
```
### Actions locally

[act](https://github.com/nektos/act) - пользволяет запускать действия GitHub Actions локально (используется в [Gitea](https://github.com/go-gitea/gitea)).
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

## Groovy

Базовый синтаксис языка `Groovy`:
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

// Классы
def str = "start"
println str
class Main {
    def echo (param) {
        println param
    }
}
def main = new Main()
def array = [1, 2, 3]
for (element in array) {
    main.echo(element)
}
// 1
// 2
// 3

// Обработка ошибок
def newList = [:]
newList[0] = 1
newList[1] = 2
for (index in 0..1) {
    try {
        newList[index] += 3
        main.echo(newList[index])
    } catch (Exception e) {
        main.echo("Ошибка: ${e.message}")
    } finally {
        if (newList[index] >= 5) {
            main.echo(newList[index]+1)
        }
    }
}
// 4
// 5
// 6

println str.replace("start", "final")

// Коллекция для синхронизации сохранения данных в потоках
def sharedList = Collections.synchronizedList([])
// Анонимная функция для обработки данных в потоке
def runTask = { name, delay ->
    Thread.start {
        println "${name} запущена в потоке ${Thread.currentThread().name}"
        sleep(delay)
        println "${name} завершена"
        synchronized(sharedList) {
            sharedList << "${name} завершена"
        }
    }
}

def threads = []
// Ждём завершения всех потоков
threads << runTask("Задача 1", 3000)
threads << runTask("Задача 2", 2000)
threads << runTask("Задача 3", 1000)

threads*.join()
println "Результат: $sharedList"

// Функции строк
" text ".trim()                     // удаляет пробелы в начале и конце => "text"
"ping".replace("i", "o")            // заменяет буквы в строке => pong
"a,b,c".split(",")                  // разбивает строку по разделителю => ["a", "b", "c"]
"abc".size()                        // возвращает длину строки или размер списка (кол-во элементов) => 3
"abc".reverse()                     // переворачивает строку => "cba"
"abc".contains("b")                 // проверяет наличие подстроки => true
"abc".startsWith("a")               // проверяет начало строки => true
"abc".endsWith("c")                 // проверяет конец строки => true
"123".isNumber()                    // проверяет, является ли строка числом => true
"abc".matches("a.*")                // проверяет соответствие регулярному выражения => true
"hello".toUpperCase()               // преобразует строку в верхний регистр => "HELLO"
"HELLO".toLowerCase()               // преобразует строку в нижний регистр => "hello"

// Функции массивов
["a","b","c"].join(",")             // объединяет элементы в строку => "a,b,c"
["a","b","c"].contains("b")         // проверяет наличие элемента => true
[1, 2, 3].sum()                     // суммирует элементы => 6
[1, 2, 3].max()                     // находит максимум => 3
[1, 2, 3].min()                     // находит минимум => 1
[1, 2, 3].average()                 // вычисляет среднее => 2
[1, 2, 3].reverse()                 // переворачивает список => [3, 2, 1]
[3, 2, 1].sort()                    // сортирует список => [1, 2, 3]
[1, 2, 2, 3, 3].unique()            // удаляет дубли => [1, 2, 3]
[1, 2, 3].findAll { it > 1 }        // фильтрует элементы => [2, 3]
[1, 2, 3].collect { it * 2 }        // преобразует элементы => [2, 4, 6]
["1","2"].collect {it.toInteger()}  // строки => числа => [1, 2]

def users = [
    [name: "Alex", age: 30],
    [name: "Jack", age: 35]
]  
users.collect { it.name }
// ["Alex", "Jack"]

// Функции карт (map)
["a": 1, "b": 2].get("a")                       // получает значение по ключу => 1
["a": 1, "b": 2].keySet()                       // возвращает все ключи => ["a", "b"]
["a": 1, "b": 2].values()                       // возвращает все значения => [1, 2]
["a": 1, "b": 2].containsKey("a")               // проверяет наличие ключа => true
["a": 1, "b": 2].findAll { k, v -> v > 1 }      // фильтрует записи => ["b": 2]
["a": 1, "b": 2].collect { k, v -> "$k-$v" }    // преобразует => ["a-1", "b-2"]
["a": 1].put("b", 2)                            // добавляет новую пару ключ-значение => ["a": 1, "b": 2]
["a": 1].plus(["b": 2])                         // объединяет мапы => ["a": 1, "b": 2]

// Директории и файлы
new File("dir").mkdir()                         // создает директорию => boolean
new File("dir/subdir").mkdirs()                 // создает все недостающие директории d genb => boolean
new File("dir").list()                          // список имен файлов => String[]
new File("dir").listFiles()                     // возвращает список файлов в директории => File[]
new File("dir").deleteDir()                     // удаляет директорию (рекурсивно) => boolean
new File("dir").isDirectory()                   // проверяет, что это директория => boolean
new File("file.txt").createNewFile()            // создает пустой файл => boolean
new File("file.txt").delete()                   // удаляет файл => boolean
new File("file.txt").exists()                   // проверяет существование файла => boolean
new File("file.txt").isFile()                   // проверяет, что это файл => boolean
new File("file.txt").length()                   // возвращает размер файла в байтах => long
new File("file.txt").lastModified()             // возвращает время последнего изменения => long (timestamp)
new File("file.txt").getName()                  // возвращает имя файла (без пути) => String
new File("file.txt").getPath()                  // возвращает относительный путь => String
new File("file.txt").getAbsolutePath()          // возвращает абсолютный путь => String
new File("file.txt").text                       // читает содержимое файла в строку
new File("file.txt").getText("UTF-8")           // указать кодировку при чтение
new File("file.txt").readBytes()                // читает файл как массив байтов => byte[]
new File("file.txt").readLines()                // читает файл построчно (получаем массив из строк) => List<String>
new File("file.txt").eachLine { it }            // обработать каждую строку
new File("file.txt").write("text")              // перезаписывает файл (если существует) => void
new File("file.txt").setText("text")            // аналог write() => void
new File("file.txt").bytes = [1, 2, 3]          // записывает массив байтов => void
new File("file.txt") << "text"                  // добавляет текст в конец файла => void

(versions[1].toInteger() + 1).toString().padLeft(4, '0') // 0019 + 1 = 0020 ("19".padLeft(4, '0') -> "0019")
```
## Jenkins

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

### API
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
### Plugins

| Плагин                                                                                  | Описание                                                                                                                |
| -                                                                                       | -                                                                                                                       |
| [Pipeline: Nodes and Processes](https://plugins.jenkins.io/pipeline-stage-view)         | Плагин, который предоставляет доступ к интерпретаторам `sh`, `bat`, `powershell` и `pwsh`                               |
| [Pipeline Utility Steps](https://jenkins.io/doc/pipeline/steps/pipeline-utility-steps)  | Добавляет методы `readJSON`, `writeJSON`, `readYaml`, `writeYaml`, `readTOML`, `writeTOM`, `untar`, `unzip`, и другие.  |
| [HTTP Request](https://plugins.jenkins.io/http_request)                                 | Простой REST API Client для отправки и обработки `GET` и `POST` запросов через метод `httpRequest`.                     |
| [Credentials Binding Plugin](https://jenkins.io/doc/pipeline/steps/credentials-binding) | Добавляет метод `withCredentials` для доступа к секретам.                                                               |
| [HashiCorp Vault](https://plugins.jenkins.io/hashicorp-vault-plugin)                    | Автоматизирует процесс получения содержимого значений из Vault с помощью метода `withVault`                             |
| [Ansible](https://plugins.jenkins.io/ansible)                                           | Параметраризует запуск `ansible-playbook` (требуется установка на агенте) через метод `ansiblePlaybook`.                |
| [Pipeline Stage View](https://plugins.jenkins.io/pipeline-stage-view)                   | Визуализация шагов (stages) в интерфейсе проекта с временем их выполнения.                                              |
| [Rebuilder](https://plugins.jenkins.io/rebuild)                                         | Позволяет перезапускать параметризованную сборку с предустановленными параметрами в выбранной сборке.                   |
| [Schedule Build](https://plugins.jenkins.io/schedule-build)                             | Позволяет запланировать сборку на указанный момент времени.                                                             |
| [Webhook Trigger](https://plugins.jenkins.io/generic-webhook-trigger)                   | Принимает POST запросы на конечной точке `/generic-webhook-trigger/invoke` для извлечения значений и запуска Pipeline.  |
| [Job Configuration History](https://plugins.jenkins.io/jobConfigHistory)                | Сохраняет копию файла сборки в формате `xml` (который хранится на сервере) и позволяет производить сверку.              |
| [Export Job Parameters](https://plugins.jenkins.io/export-job-parameters)               | Добавляет кнопку `Export Job Parameters` для конвертации все параметров в декларативный синтаксис Pipeline.             |
| [SSH Pipeline Steps](https://plugins.jenkins.io/ssh-steps)                              | Плагин для подключения к удаленным машинам через протокол ssh по ключу или паролю.                                      |
| [SSH Agent Plugin](https://www.jenkins.io/doc/pipeline/steps/ssh-agent)                 | Плагин для подключения к удаленным машинам с использованием `ssh-agent` и `credentials`.                                |
| [Active Choices Parameters](https://plugins.jenkins.io/uno-choice)                      | Активные параметры, которые позволяют динамически обновлять содержимое параметров.                                      |
| [File Parameters](https://plugins.jenkins.io/file-parameters)                           | Поддержка параметров для загрузки файлов (перезагрузить Jenkins для использования нового параметра).                    |
| [Separator Parameter](https://plugins.jenkins.io/parameter-separator)                   | Параметр для разграничения набора параметров на странице сборки задания с поддержкой HTML.                              |
| [Custom Tools](https://plugins.jenkins.io/custom-tools-plugin)                          | Позволяет загружать пакеты из интернета с помощью предустановленного набора команд.                                     |
| [ANSI Color](https://plugins.jenkins.io/ansicolor)                                      | Добавляет поддержку стандартных escape-последовательностей ANSI для покраски вывода.                                    |
| [Email Extension](https://plugins.jenkins.io/email-ext)                                 | Отправка сообщений на почту из Pipeline.                                                                                |
| [Test Results Analyzer](https://plugins.jenkins.io/test-results-analyzer)               | Показывает историю результатов сборки `junit` тестов в табличном древовидном виде.                                      |
| [Embeddable Build Status](https://plugins.jenkins.io/embeddable-build-status)           | Предоставляет настраиваемые значки (like `shields.io`), который возвращает статус сборки.                               |
| [Prometheus Metrics](https://plugins.jenkins.io/prometheus)                             | Предоставляет конечную точку `/prometheus` с метриками, которые используются для сбора данных.                          |
| [Web Monitoring](https://plugins.jenkins.io/monitoring)                                 | Добавляет конечную точку `/monitoring` для отображения графиков мониторинга в веб-интерфейсе.                           |
| [CloudBees Disk Usage](https://plugins.jenkins.io/cloudbees-disk-usage-simple)          | Отображает использование диска всеми заданиями во вкладке `Manage-> Disk usage`.                                        |

### Credentials

Примеры использования метода `withCredentials` для извлечения и использования секретов:

```Groovy
withCredentials([string(
  credentialsId: 'github-token', variable: 'TOKEN'
)]) {
  sh 'curl -H "Authorization: Bearer $TOKEN" https://api.github.com/rate_limit'
}

withCredentials([usernamePassword(
  credentialsId: 'nexus-creds',
  usernameVariable: 'NEXUS_USER',
  passwordVariable: 'NEXUS_PASS'
)]) {
  sh 'echo "$NEXUS_PASS" | docker login -u "$NEXUS_USER" --password-stdin registry.example.com'
}

withCredentials([sshUserPrivateKey(
  credentialsId: params.credentials,
  usernameVariable: 'SSH_USER',
  keyFileVariable: 'SSH_KEY',
  passphraseVariable: ''
)]) {
    // sh 'GIT_SSH_COMMAND="ssh -i $SSH_KEY -o StrictHostKeyChecking=no" git fetch'
    writeFile(file: env.SSH_KEY_FILE, text: readFile(SSH_KEY))
    sh "chmod 600 ${env.SSH_KEY_FILE}"
}

withCredentials([file(
  credentialsId: 'google-cloud-key',
  variable: 'KEYFILE'
)]) {
  sh 'gcloud auth activate-service-account --key-file="$KEYFILE"'
}
```

### configFile

Загрузка конфигурации из Jenkins:

```Groovy
configFileProvider([configFile(
    fileId: 'dev-config',
    variable: 'DEV_CONFIG'
)]) {
    sh "cp ${DEV_CONFIG} devsecops-config.yml"
}
def config = readYaml(
    file: 'dev-config.yml'
)
```

### SSH Agent

```Groovy
sshagent (credentials: ['d5da50fc-5a98-44c4-8c55-d009081a861a']) {
  sh 'ssh -o StrictHostKeyChecking=no -l lifailon 192.168.3.101 uname -a'
}
```

### SSH Steps and Artifacts

Добавляем логин и `Private Key` для авторизации по ssh: `Manage (Settings)` => `Credentials` => `Global` => `Add credentials` => Kind: `SSH Username with private key`

Сценарий проверяет доступность удаленной машины, подключается к ней по ssh, выполняет скрипт [hwstat](https://github.com/Lifailon/hwstat) для сбора метрик и выгружает json отчет в артефакты:
```Groovy
// Глобальный массив для хранения данных подключения по ssh 
def remote = [:]

pipeline {
    agent { label 'local-agent' }
    parameters {
        string(name: 'address', defaultValue: '192.168.3.105', description: 'Адрес удаленного сервера')
        // choice(name: "addresses", choices: ["192.168.3.101","192.168.3.102"], description: "Выберите сервер из выпадающего списка")
        string(name: 'port', defaultValue: '2121', description: 'Порт ssh')
        string(name: 'credentials', defaultValue: 'd5da50fc-5a98-44c4-8c55-d009081a861a', description: 'Идентификатор учетных данных из Jenkins')
        booleanParam(name: "root", defaultValue: false, description: 'Запуск с повышенными привилегиями')
        booleanParam(name: "report", defaultValue: true, description: 'Выгружать отчет в формате json')
    }
    // triggers {
    //     cron('H */6 * * 1-5') // выполнять запуск каждын 6 часов с понедельника по пятницу
    // }
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
                        error("Порт ${params.port} закрыт (tcp check)")
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
### Upload File Parameter

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
### Input Text and File

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
### HttpURLConnection

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
### httpRequest

Пример HTTP запроса и чтения `json` файла с помощью плагинов `HTTP Request` и `Pipeline Utility Steps`:
```groovy
pipeline {
    agent any
    stages {
        stage('HTTP Request and Read JSON') {
            steps {
                script {
                    def url = "https://torapi.vercel.app/api/provider/list"
                    def response = httpRequest(url: url, httpMode: "GET", contentType: "APPLICATION_JSON")
                    echo "${response.status}"
                    echo "${response.headers}"
                    echo "${response.content}"
                    def jsonData = readJSON(text: response.content)
                    echo "Url array: ${jsonData[0].Urls}"
                    echo "One url: ${jsonData[0].Urls[0]}"
                    for (u in jsonData[0].Urls) {
                        echo u
                    }
                    writeJSON(file: "debug.json", json: jsonData)
                    archiveArtifacts "debug.json"
                }
            }
        }
    }
}
```
### Active Choices Parameter

Пример получения списка доступных версий в выбранном репозитории и содержимого файлов для выбранного релиза, а также загрузка указанного файла:
```Groovy
pipeline {
    agent any
        parameters {
        activeChoice(
            name: 'Repos',
            description: 'Select repository.',
            choiceType: 'PT_RADIO',
            filterable: false,
            script: [
                $class: 'GroovyScript',
                script: [
                    sandbox: true,
                    script: '''
                        return [
                            'Lifailon/$APP_NAME',
                            'jesseduffield/lazydocker'
                        ]
                    '''
                ]
            ]
        )
        reactiveChoice(
            name: 'Versions',
            description: 'Select version.',
            choiceType: 'PT_SINGLE_SELECT',
            filterable: true,
            filterLength: 1,
            script: [
                $class: 'GroovyScript',
                script: [
                    sandbox: true,
                    script: '''
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
                    '''
                ]
            ],
            referencedParameters: 'Repos'
        )
        reactiveChoice(
            name: 'Files',
            description: 'Select file.',
            choiceType: 'PT_SINGLE_SELECT',
            filterable: true,
            filterLength: 1,
            script: [
                $class: 'GroovyScript',
                script: [
                    sandbox: true,
                    script: '''
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
                    '''
                ]
            ],
            referencedParameters: 'Repos,Versions'
        )
    }
    stages {
        stage('Selected parameters') {
            steps {
                script {
                    echo "Selected repository: https://github.com/${params.Repos}"
                    echo "Selected version: ${params.Versions}"
                    echo "Selected file: ${params.Files}"
                    def downloadUrl = "https://github.com/${params.Repos}/releases/download/${params.Versions}/${params.Files}"
                    echo "Url for download: ${downloadUrl}"
                    httpRequest(url: downloadUrl, outputFile: params.Files)
                    archiveArtifacts params.Files
                }
            }
        }
    }
}
```
### Vault

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
### withVault

Команда (скрипт) для загрузки `kubectl` в Custom tool:
```bash
mkdir -p ./bin
curl https://dl.k8s.io/release/v1.33.3/bin/linux/amd64/kubectl -sSLo ./bin/kubectl
chmod +x ./bin/kubectl
# Домашний каталог утилиты: bin
```
Получение секретов (на примере содержимого `kubeconfig`) с помощью метода `withVault`:
```Groovy
def log = {
    def m = [:]
    m.info = { text -> echo "\u001B[34m${text}\u001B[0m" }
    m.success = { text -> echo "\u001B[32m${text}\u001B[0m" }
    m.error = { text -> echo "\u001B[31m${text}\u001B[0m" }
    return m
}()

pipeline {
    agent any
    options {
        ansiColor("xterm")
        timestamps()
        timeout(time: 10, unit: "MINUTES")
    }
    environment {
        KUBECONFIG = "${WORKSPACE}/kubeconfig"
        KUBECTLPATH = tool(
            name: "kubectl-amd64-1.33.3",
            type: "com.cloudbees.jenkins.plugins.customtools.CustomTool"
        )
        PATH = "${KUBECTLPATH}:${env.PATH}"
    }
    parameters {
        separator(
            name: "separatorVault",
            sectionHeader: "Vault",
            separatorStyle: "border-color: blue",
            sectionHeaderStyle: "font-size: 1.5em; font-weight: bold;"
        )
        string(
            name: "vaultUrl",
            defaultValue: "http://192.168.3.101:8200",
            description: "Адрес Vault"
        )
        string(
            name: "vaultPath",
            defaultValue: "v1/kv/kube",
            description: "Путь к секретам в Vault (где хранится ключ config с содержимым kubeconfig)"
        )
        credentials(
            name: "vaultAppRole",
            credentialType: "com.datapipe.jenkins.vault.credentials.VaultAppRoleCredential",
            defaultValue: "main_approle",
            description: "AppRole для доступа на чтение секретов из Vault"
        )
        separator(
            name: "separatorDebug",
            sectionHeader: "Debug",
            separatorStyle: "border-color: blue",
            sectionHeaderStyle: "font-size: 1.5em; font-weight: bold;"
        )
        booleanParam(
            name: "checkConfig",
            defaultValue: true,
            description: "Проверить содержимое kubeconfig и версию kubectl"
        )
        // text(name: "multiLine", defaultValue: "line1\nline2")
        // choice(name: "addresses", choices: ["192.168.3.101", "192.168.3.105","192.168.3.106"])
        // password(name: "token", defaultValue: "YWRtaW4K")
        // credentials(name: "sshKey", credentialType: "com.cloudbees.jenkins.plugins.sshcredentials.impl.BasicSSHUserPrivateKey", defaultValue: "d5da50fc-5a98-44c4-8c55-d009081a861a", required: true)
        // activeChoice(
        //     name: "activeChoicesParameter", choiceType: "PT_CHECKBOX", filterable: true,
        //     script: [$class: "GroovyScript", box: true, script: [script: '''return ["1","2","3"]''']]
        // )
        // reactiveChoice(
        //     name: "activeChoicesReactiveParameter", choiceType: "PT_RADIO", filterable: false,
        //     referencedParameters: "activeChoicesParameter",
        //     script: [$class: "GroovyScript", box: true, script: [script: '''return [activeChoicesParameter]''']]
        // )
    }
    stages {
        stage("Get kubeconfig from Vault") {
            steps {
                script {
                    // Конфигурация для подключения к Vault
                    def vaultConfiguration = [
                        vaultUrl:           params.vaultUrl,
                        vaultCredentialId:  params.vaultAppRole,
                        engineVersion:      1
                    ]
                    // Переменная для извлечения секретов
                    def vaultSecrets  = [
                        [
                            path: params.vaultPath,
                            engineVersion: 1,
                            secretValues: [
                                [
                                    envVar: "kubeconfig", // название переменной
                                    vaultKey: "config"    // ключ в Vault
                                ]
                            ]
                        ]
                    ]
                    // Метод извлечения секретов из Vault
                    withVault(
                        [
                            configuration:  vaultConfiguration,
                            vaultSecrets:   vaultSecrets
                        ]
                    ) {
                        // Записываем содержимое в файл
                        writeFile(
                            file: "${WORKSPACE}/kubeconfig", // Не принимает переменные из environment
                            text: kubeconfig
                        )
                    }
                }
            }
        }
        stage("Check kubeconfig and kubectl") {
            when {
                expression { params.checkConfig }
            }
            steps {
                script {
                    log.info("Проверяем содержимое kubeconfig")
                    def kubeconfig = readFile(
                        file: KUBECONFIG
                    )
                    if (kubeconfig.trim().length() == 0) {
                        log.error("Конфигурация отсутствует (файл kubeconfig пустой)")
                    } else {
                        def firstLine = kubeconfig.split("\n")[0]
                        log.success("Конфигурация получена")
                        log.success(firstLine)
                    }
                    log.info("Проверка версии kubectl")
                    def kubectlVersion = sh(
                        script: """
                            kubectl version --output=json || true
                        """,
                        returnStatus: false, // Возвращяет код возврата если true (для проверки или игнорирования ошибок)
                        returnStdout: true   // Возвращяет вывод в переменную
                    )
                    log.success(kubectlVersion)
                }
            }
        }
    }
    post {
        always {
            script {
                sh(
                    script: """
                        ls -lh
                        rm -rf ./*
                        ls -lh
                    """
                )
            }
        }
    }
}
```
### Email Extension

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
### Parallel
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
## Configuration Management

### Ansible

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

`ansible --version`

Конфигурация настроек Ansible в файле `/etc/ansible/ansible.cfg`
```yaml
[defaults]
inventory = /etc/ansible/hosts
# Отключить проверку ключа ssh (для подключения используя пароль)
host_key_checking = False
```
Настройка списка групп хостов в файле `/etc/ansible/hosts`
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

#### Windows Modules

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
#### Jinja

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

### Puppet/Bolt

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

`echo name: $APP_NAME > bolt-project.yaml` создать файл проекта

`mkdir plans && nano test.yaml` создать директорию и файл с планом работ
```yaml
parameters:
  targets:
    type: TargetSpec

steps:
  - name: clone
    command: rm -rf $APP_NAME && git clone https://github.com/Lifailon/$APP_NAME
    targets: $targets

  - name: test
    command: cd $APP_NAME && go test -v -cover --run TestMainInterface
    targets: $targets

  - name: remove
    command: rm -rf $APP_NAME
    targets: $targets
```
`bolt plan show` вывести список всех планов

`bolt plan run $APP_NAME::test --inventory inventory.yaml --targets bsd -v` запустить план

### Sake

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

## Secret Manager

### Bitwarden

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
### Infisical

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
### HashiCorp/Vault

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
API Swagger: http://192.168.3.101:8200/ui/vault/tools/api-explorer
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
$urlUpdate = "http://192.168.3.101:8200/v1/kv/data/main-path"
Invoke-RestMethod -Uri $urlUpdate -Method POST -Headers $Headers -Body $Body

# Удалить все секреты
Invoke-RestMethod -Uri "http://192.168.3.101:8200/v1/kv/data/main-path" -Method DELETE -Headers $Headers
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
### HashiCorp/Consul

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

## Prometheus

Создание экспортера на примере получения метрик температуры всех дисков из [CrystalDiskInfo](https://crystalmark.info/en/software/crystaldiskinfo) с помощью PowerShell и отправки в [Prometheus](https://github.com/prometheus/prometheus) через [PushGateway](https://github.com/prometheus/pushgateway).

Формат метрик:
```
# HELP название_метрики Описание метрики
# TYPE название_метрики ТИП-ДАННЫХ
название_метрики{лейбл="НАЗВАНИЕ ДИСКА 1", instance="HOSTNAME"} ЗНАЧЕНИЕ
название_метрики{лейбл="НАЗВАНИЕ ДИСКА 2", instance="HOSTNAME"} ЗНАЧЕНИЕ
```
Типы данных:

- `counter` - возрастающее значение (например, количество запросов, ошибок, завершенных задач)
- `gauge` - переменное значение (может увеличиваться или уменьшаться, например, нагрузка CPU, объем свободной памяти, температура)
- `histogram` - разделенные данные на корзины (buckets, с помощью лэйбла `le`) и подсчет наблюдения в них (автоматически создает три метрики: <name>_bucket, <name>_sum, <name>_count)
- `summary` - аналогичен гистограмме, но вычисляет квантили

Строки метрик содержат имя, лейблы (в фигурных скобках) и значение.

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

5. Собираем контейнер в среде `WSL` с помощью `dockerfile` монтированием системного диска Windows:
```dockerfile
FROM mcr.microsoft.com/powershell:latest
WORKDIR /cdi-exporter
COPY cdi-exporter.ps1 ./cdi-exporter.ps1
CMD ["pwsh", "-File", "cdi-exporter.ps1"]
```
`docker build -t cdi-exporter .` \
`docker run -d -v /mnt/c:/mnt/c --name cdi-exporter cdi-exporter`

6. Собираем стек из шлюза и скрипта в `docker-compose.yml`:
```yaml
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
```
`docker-compose up -d`

7. Настраиваем `Dashboard` в `Grafana`:

Переменные для фильтрации запроса: \
hostName: `label_values(exported_instance)` \
diskName: `label_values(disk)` \
Метрика температуры: `disk_temperature{exported_instance="$hostName", disk=~"$diskName"}`

## PromQL

Функции `PromQL`:

| Функция                       | Тип данных        | Описание                                                              | Пример                                                            |
| -                             | -                 | -                                                                     | -                                                                 |
| `rate()`                      | `counter`         | Средняя скорость роста метрики за интервал (increase / seconds)       | `rate(http_requests_total[$__rate_interval])`                     |
| `irate()`                     | `counter`         | Мгновенная скорость роста (использует последние 2 точки)              | `irate(http_requests_total[1m])`                                  |
| `increase()`                  | `counter`         | Абсолютный прирост метрики за интервал (end time - start time)        | `increase(http_requests_total[5m])`                               |
| `resets()`                    | `counter`         | Количество сбросов counter-метрики за интервал.                       | `resets(process_cpu_seconds_total[1h])`                           |
| `delta()`                     | `gauge`           | Разница между первым и последним значением метрики за интервал        | `delta(node_memory_free[[5m]])`                                   |
| `idelta()`                    | `gauge`           | Разница между последними двумя точками                                | `delta(node_memory_free[1m])`                                     |
| `avg_over_time()`             | `gauge`           | Среднее значение за интервал	                                        | `avg_over_time(temperature[5m])`                                  |
| `max_over_time()`             | `gauge`           | Максимальное значение за интервал                                     | `max_over_time(temperature[5m])`                                  |
| `predict_linear()`            | `gauge`           | Предсказывает значение метрики через N секунд (для прогнозирования)   | `predict_linear(disk_free[1h], 3600)`                             |
| `count()`                     | `counter`/`gauge` | Количество элементов метрики                                          | `count(http_requests_total) by (status_code)`                     |
| `sum()`                       | `counter`/`gauge` | Суммирует значения метрик по указанным labels                         | `sum(rate(cpu_usage[5m])) by (pod)`                               |
| `avg()`                       | `counter`/`gauge` | Среднее значение метрики по указанным labels                          | `avg(node_memory_usage_bytes) by (instance)`                      |
| `min() / max()`               | `counter`/`gauge` | Возвращает минимальное/максимальное значение                          | `max(container_cpu_usage) by (namespace)`                         |
| `round()`                     | `counter`/`gauge` | Округляет значения до указанного числа дробных знаков                 | `round(container_memory_usage / 1e9, 2)`                          |
| `floor() / ceil()`            | `counter`/`gauge` | Округляет вниз/вверх до целого числа                                  | `floor(disk_usage_percent)`                                       |
| `absent()`                    | `counter`/`gauge` | Возвращает 1, если метрика отсутствует (для алертинга)                | `absent(up{job="node-exporter"})`                                 |
| `clamp_min() / clamp_max()`   | `counter`/`gauge` | Ограничивает значения минимумом/максимумом (уменьшает если больше)    | `clamp_max(disk_usage_percent, 100)`                              |
| `label_replace()`             | `counter`/`gauge` | Изменяет или добавляет labels в метрике                               | `label_replace(metric, "new_label", "$1", "old_label", "(.*)")`   |
| `sort() / sort_desc()`        | `counter`/`gauge` | Сортирует метрики по возрастанию/убыванию                             | `sort(node_filesystem_free_bytes)`                                |

## Cloud

### AWS/LocalStack

Установка [aws cli](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html#getting-started-install-instructions):
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
rm awscliv2.zip
```

Настройка профиля по умолчанию:

```bash
aws configure # --profile localstack
AWS Access Key ID: test
AWS Secret Access Key: test
Default region name: us-east-1
Default output format: json
```

Переменные окружения для подключения к облаку или localstack:

```bash
export AWS_ENDPOINT_URL="http://192.168.3.101:4566"
# export AWS_ACCESS_KEY_ID="test"
# export AWS_SECRET_ACCESS_KEY="test"
# export AWS_DEFAULT_REGION="us-east-1"
# Windows
$env:AWS_ENDPOINT_URL="http://192.168.3.101:4566"
```

#### S3

Создание s3 хранилища:

```bash
aws --endpoint-url=http://192.168.3.101:4566 --profile localstack s3 mb s3://test-bucket
aws --endpoint-url=http://192.168.3.101:4566 --profile localstack s3 ls
```

#### Fluent Bit

Запускаем [localstack](https://github.com/localstack/localstack) вместе с [fluent-bit](https://github.com/fluent/fluent-bit) для пересылки логов:

```yaml
services:
  localstack:
    image: localstack/localstack
    container_name: localstack
    restart: always
    ports:
      - 4566:4566
      - 4510-4559:4510-4559
    environment:
      - DEBUG=1
      - PERSISTENCE=1
      - EXTRA_CORS_ALLOWED_ORIGINS=*
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./localstack_data:/var/lib/localstack

  fluent-bit:
    image: fluent/fluent-bit:latest
    container_name: fluent-bit
    ports:
      - 24224:24224
    environment:
      - AWS_ENDPOINT_URL=http://localstack:4566
      - AWS_ACCESS_KEY_ID=test
      - AWS_SECRET_ACCESS_KEY=test
      - AWS_REGION=us-east-1
    volumes:
      - ./fluent-bit.conf:/fluent-bit/etc/fluent-bit.conf
```

Создаем конфигурацию `fluent-bit.conf` для приема логов из контейнеров Docker и их переадресации в AWS CloudWatch:

```conf
[SERVICE]
    Log_Level    info

[INPUT]
    Name  forward
    Listen 0.0.0.0
    Port  24224

[OUTPUT]
    Name                localstack_cloudwatch_logs
    Match               *
    region              us-east-1
    # Автоматическое создание группы, если она отсутствует
    auto_create_group   On
    # Название группы
    log_group_name      docker-logs
    # Название потока, например, container-zerobyte
    log_stream_prefix   container-
    # Адрес сервера localstack
    endpoint            localstack
    port                4566
```

Подключаем драйвер `fluentd` для отправки логов из любого контейнера Docker:

```yaml
    logging:
      driver: fluentd
      options:
        fluentd-address: localhost:24224
        tag: zerobyte
```

#### CloudWatch

Создание группы, потока и запись логов в CloudWatch:

```bash
# Создание группы для хранения логов (Log Group)
aws logs create-log-group --log-group-name "docker-logs"
# Отобразить все группы
aws logs describe-log-groups
# Создание потока для записи логов (Log Stream)
aws logs create-log-stream --log-group-name "docker-logs" --log-stream-name "script-test"
# Отобразить все потоки в группе
aws logs describe-log-streams --log-group-name "docker-logs"
# Отправка лога (Put Log Events)
aws logs put-log-events --log-group-name "docker-logs" --log-stream-name "script-test" --log-events timestamp=$(date +%s000),message="Test message from Bash"
# Windows
$timestamp = [DateTimeOffset]::Now.ToUnixTimeMilliseconds()
aws logs put-log-events --log-group-name "docker-logs" --log-stream-name "script-test" --log-events "timestamp=$timestamp,message='Test message from PowerShell'"
# Чтение логов (Get Log Events)
aws logs get-log-events --log-group-name "docker-logs" --log-stream-name "script-test"
# Фильтрация логов
aws logs filter-log-events --log-group-name "docker-logs" --query "events[*].message" --output text 
# Чтение логов из всех потоков за последние сутки
aws logs tail docker-logs --since 1d
```

### Azure

`Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force` установить все модули для работы с Azure \
`Get-Module *Az.*` список всех модулей

`Get-Command -Module Az.Accounts` отобразить список команд модуля Az.Accounts \
`Connect-AzAccount` подключиться у учетной записи Azure \
`Get-AzContext` получить текущий статус подключения к Azure \
`Get-AzSubscription` получить список подписок Azure, доступных для текущего пользователя \
`Set-AzContext` установить контекст Azure для конкретной подписки и/или учетной записи \
`Disconnect-AzAccount` отключиться от учетной записи Azure

`Get-Command -Module Az.Compute` \
`Get-AzVM` получить список виртуальных машин в текущей подписке или группе ресурсов \
`Get-AzVMSize` получить список доступных размеров виртуальных машин в определенном регионе \
`Get-AzVMImage` получить список доступных образов виртуальных машин \
`New-AzVM` создать новую виртуальную машину \
`Remove-AzVM` удалить виртуальную машину \
`Start-AzVM` запустить виртуальную машину \
`Stop-AzVM` остановить виртуальную машину \
`Restart-AzVM` перезагрузить виртуальную машину

`Get-Command -Module Az.Network` \
`Get-AzVirtualNetwork` получить список виртуальных сетей в текущей подписке или группе ресурсов \
`New-AzVirtualNetwork` создать новую виртуальную сеть \
`Remove-AzVirtualNetwork` удалить виртуальную сеть \
`Get-AzNetworkInterface` получить список сетевых интерфейсов \
`New-AzNetworkInterface` создать новый сетевой интерфейс \
`Remove-AzNetworkInterface` удалить сетевой интерфейс

`Get-Command -Module Az.Storage` \
`Get-AzStorageAccount` получить список учетных записей хранилища \
`New-AzStorageAccount` создать новую учетную запись хранилища \
`Remove-AzStorageAccount` удалить учетную запись хранилища \
`Get-AzStorageContainer` список контейнеров в учетной записи хранилища \
`New-AzStorageContainer` создать новый контейнер в учетной записи хранилища \
`Remove-AzStorageContainer` удалить контейнер

`Get-Command -Module Az.ResourceManager` \
`Get-AzResourceGroup` получить список групп ресурсов в текущей подписке \
`New-AzResourceGroup` создать новую группу ресурсов \
`Remove-AzResourceGroup` удалить группу ресурсов \
`Get-AzResource` получить список ресурсов \
`New-AzResource` создать новый ресурс \
`Remove-AzResource` удалить ресурс

`Get-Command -Module Az.KeyVault` \
`Get-AzKeyVault` список хранилищ ключей \
`New-AzKeyVault` создать новое хранилище ключей в Azure \
`Remove-AzKeyVault` удалить хранилище ключей в Azure

`Get-Command -Module Az.Identity` \
`Get-AzADUser` получить информацию о пользователях Azure Active Directory \
`New-AzADUser` создать нового пользователя \
`Remove-AzADUser` удалить пользователя \
`Get-AzADGroup` получить информацию о группах \
`New-AzADGroup` создать новую группу \
`Remove-AzADGroup` удалить группу

- [Manage-VM](https://learn.microsoft.com/ru-ru/azure/virtual-machines/windows/tutorial-manage-vm)

`New-AzResourceGroup -Name "Resource-Group-01" -Location "EastUS"` создать группу ресурсов (логический контейнер, в котором происходит развертывание ресурсов Azure) \
`Get-AzVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"` список доступных образов Windows Server для установки \
`$cred = Get-Credential` \
`New-AzVm -ResourceGroupName "Resource-Group-01" -Name "vm-01" -Location 'EastUS' -Image "MicrosoftWindowsServer:WindowsServer:2022-datacenter-azure-edition:latest" -Size "Standard_D2s_v3" -OpenPorts 80,3389 --Credential $cred` создать виртуальную машину \
`Get-AzVM -ResourceGroupName "Resource-Group-01" -Name "vm-01" -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}` статус виртуальной машины \
`Start-AzVM -ResourceGroupName "Resource-Group-01" -Name "vm-01"` запустить виртуальную машину \
`Stop-AzVM -ResourceGroupName "Resource-Group-01" -Name "vm-01" -Force` остановить виртуальную машину \
`Invoke-AzVMRunCommand -ResourceGroupName "Resource-Group-01" -VMName "vm-01" -CommandId "RunPowerShellScript" -ScriptString "Install-WindowsFeature -Name Web-Server -IncludeManagementTools"` установить роль веб-сервера IIS

- [Manage-Disk](https://learn.microsoft.com/ru-ru/azure/virtual-machines/windows/tutorial-manage-data-disk)

`$diskConfig = New-AzDiskConfig -Location "EastUS" -CreateOption Empty -DiskSizeGB 512 -SkuName "Standard_LRS"` создать диск на 512 Гб \
`$dataDisk = New-AzDisk -ResourceGroupName "Resource-Group-01" -DiskName "disk-512" -Disk $diskConfig` создание объекта диска для подготовки диска данных к работе \
`Get-AzDisk -ResourceGroupName "Resource-Group-01" -DiskName "disk-512"` список дисков \
`$vm = Get-AzVM -ResourceGroupName "Resource-Group-01" -Name "vm-01"` \
`Add-AzVMDataDisk -VM $vm -Name "Resource-Group-01" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 1` подключить диск к виртуальной машине \
`Update-AzVM -ResourceGroupName "Resource-Group-01" -VM $vm` обновить конфигурацию виртуальной машины \
`Get-Disk | Where PartitionStyle -eq 'raw' | Initialize-Disk -PartitionStyle MBR -PassThru | New-Partition -AssignDriveLetter -UseMaximumSize | Format-Volume -FileSystem NTFS -NewFileSystemLabel "disk-512" -Confirm:$false` инициализировать диск в ОС (необходимо подключиться к виртуальной машине) с таблицей MBR, создать раздел и назначить все пространство и форматировать в файловую систему NTFS

### Vercel

[Vercel](https://github.com/vercel/vercel) - это бессерверная платформа для публикации веб-приложений и `REST API`.

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

Развертвывание `JavaScript` приложения через GitHub Actions:
```yaml
name: CD (Deploy to Vercel)

on:
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Install node.js
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
## Load Balancer

### Traefik

Запускаем контейнере [Traefik](https://github.com/traefik/traefik) в стеке с [Jaeger](https://github.com/jaegertracing/jaeger) для анализа трассировки трафика и [DNS](https://github.com/TechnitiumSoftware/DnsServer) сервером:
```yaml
services:
  traefik:
    image: traefik:v3
    container_name: traefik
    restart: always
    # Используем режим хоста для доступа к сети всех контейнеров 
    network_mode: host
    # ports:
    #   - 8080:8080   # Web UI
    #   - 80:80       # HTTP Proxy
    #   - 443:443     # HTTPS Proxy
    #   - 4318:4318   # Prometheus Metrics
    dns:
      - 127.0.0.1
    volumes:
      - ./traefik.yml:/etc/traefik/traefik.yml
      - ./rules:/rules
      - /var/run/docker.sock:/var/run/docker.sock:ro
    healthcheck:
      test: wget -qO- http://127.0.0.1:8080/ping
      start_period: 10s
      interval: 30s
      timeout: 5s
      retries: 5
    labels:
      # Включаем маршрутизацию и определяем имя хоста
      - traefik.enable=true
      - traefik.http.routers.traefik.rule=Host(`traefik.docker.local`)
      # Указываем порт назначения в контейнере (если используется несколько портов)
      - traefik.http.services.traefik.loadbalancer.server.port=8080
      # Создаем базовую авторизацию
      - traefik.http.middlewares.basic-auth-traefik.basicauth.users=admin:$$2y$$05$$c0r5A6SCKX4R6FjuCgRqrufbIE5tmXw2sDPq1vZ8zNrrwNZIH9jgW # htpasswd -nbB admin admin
      # Включаем базовую авторизацию в маршрутизацию текущего сервиса
      - traefik.http.routers.traefik.middlewares=basic-auth-traefik
      # Настраиваем подключение к Authentik
      # - traefik.http.middlewares.authentik.forwardauth.address=http://192.168.3.101:9000/outpost.goauthentik.io/auth/traefik
      # - traefik.http.middlewares.authentik.forwardauth.trustForwardHeader=true
      # - traefik.http.middlewares.authentik.forwardauth.authResponseHeaders=X-authentik-username,X-authentik-groups,X-authentik-entitlements,X-authentik-email,X-authentik-name,X-authentik-uid,X-authentik-jwt,X-authentik-meta-jwks,X-authentik-meta-outpost,X-authentik-meta-provider,X-authentik-meta-app,X-authentik-meta-version
      # Включаем авторизацию через Authentik из провайдера Docker
      # - traefik.http.routers.traefik.middlewares=authentik@docker
      # Включаем авторизацию через Authentik из провайдера file
      # - traefik.http.routers.traefik.middlewares=authentik@file

  jaeger:
    image: jaegertracing/all-in-one:1.55
    container_name: jaeger
    restart: always
    ports:
      - 16686:16686 # Веб-интерфейс
      - 4317:4317   # Сборщик трассировок

  tech-dns-srv:
    image: technitium/dns-server:latest
    container_name: tech-dns-srv
    restart: always
    volumes:
      - ./dns_data:/etc/dns
    environment:
      - DNS_SERVER_DOMAIN=dns.docker.local
      - DNS_SERVER_FORWARDERS=1.1.1.1,8.8.8.8
      - DNS_SERVER_BLOCK_LIST_URLS=https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts
    network_mode: host
    labels:
      - traefik.enable=true
      # Определяем FQDN
      - traefik.http.routers.tech-dns-srv.rule=Host(`dns.docker.local`)
      # Переадресация на порт
      - traefik.http.services.tech-dns-srv.loadbalancer.server.port=5380
```
Конфигурация настроек в файле `traefik.yml`:
```yaml
entryPoints:
  # Настраиваем переадресацию на websecure для принудительного использования HTTPS
  web:
    address: ":80"
    http:
      redirections:
        entryPoint:
          to: websecure
          scheme: https
          permanent: true
  websecure:
    address: ":443"
    http:
      tls: {}
    transport:
      keepAliveMaxRequests: 0
      # Определяем время ожидания для неактивного соединения (для поддержания активной сессии)
      respondingTimeouts:
        idleTimeout: "30m"
  # Адрес веб-интерфейса
  traefik:
    address: ":8080"
  # Переопределяем порт для получения метрик
  metrics:
    address: ":4318"

# Включаем веб-интерфейс
api:
  dashboard: true
  insecure: true

# Конечная точка для healthcheck
ping:
  terminatingStatusCode: 204

# Настройка переадресации трассировок в Jaeger
tracing:
  serviceName: traefik
  otlp:
    grpc:
      endpoint: jaeger:4317
      insecure: true

# Включаем prometheus exporter
metrics:
  prometheus:
    entryPoint: metrics
    addRoutersLabels: true
    addServicesLabels: true

# Настраиваем формата логов
log:
  format: "common"  # common or json
  level: "INFO"     # DEBUG, INFO, WARN, ERROR, FATAL and PANIC

# Настройка фильтрации логов доступа
accessLog:
  format: "common"
  filters:
    minDuration: "1ms"
    statusCodes:
      - "200-299"
      - "300-399"
      - "400-499"
      - "500-599"

# Провайдеры для автоматического опредиления сервисов
providers:
  docker:
    endpoint: "unix:///var/run/docker.sock"
    exposedByDefault: true
    # Формат по умолчанию при опредиление сервисов compose: <service_name>-<project_name>
    # defaultRule: "Host(`{{ .Name }}.docker.local`)"
    # Шаблон в формате (используем функцию index): index <map> <key>
    defaultRule: "Host(`{{ index .Labels \"com.docker.compose.service\" | default .Name }}.docker.local`)"
  file:
    directory: /rules
    watch: true
```
Настройка дополнительных конфигураций из провайдера `file` в директории `rules` на примере интеграции с [Authentik](https://github.com/goauthentik/authentik) для авторизации с использование технологии `SSO`.

Настройка промежуточной переадресации (между Traefik и веб-приложением) в файле `authentik-middlewares.yml`:
```yaml
http:
  middlewares:
    authentik:
      forwardAuth:
        address: http://192.168.3.101:9000/outpost.goauthentik.io/auth/traefik
        trustForwardHeader: true
        authResponseHeaders:
          - X-authentik-username
          - X-authentik-groups
          - X-authentik-entitlements
          - X-authentik-email
          - X-authentik-name
          - X-authentik-uid
          - X-authentik-jwt
          - X-authentik-meta-jwks
          - X-authentik-meta-outpost
          - X-authentik-meta-provider
          - X-authentik-meta-app
          - X-authentik-meta-version
```
Настройка маршрутизации в файле `authentik-routers.yml`:
```yaml
http:
  routers:
    homepage-src:
      rule: "Host(`home.docker.local`)"
      service: homepage-dst
      entryPoints:
        - websecure
      middlewares:
        - authentik
    dns-src:
      rule: "Host(`dns.docker.local`)"
      service: dns-dst
      entryPoints:
        - websecure

  services:
    homepage-dst:
      loadBalancer:
        servers:
          - url: http://172.26.0.2:3000
    dns-dst:
      loadBalancer:
        servers:
          - url: http://192.168.3.101:5380
```
### HAProxy

Запускаем [HAProxy](https://github.com/haproxy/haproxy) в контейнере Docker:
```yaml
services:
  httpbin-proxy:
    image: haproxy:3.2.4-alpine
    container_name: httpbin-proxy
    restart: unless-stopped
    ports:
      - 8089:8080
      - 2376:2376
    volumes:
      - ./haproxy.cfg:/haproxy.cfg
    command:
      - haproxy
      - -f
      - /haproxy.cfg
      - -d
    environment:
      - STATS_USER=admin
      - STATS_PASS=admin
      - STATS_URI=/
      - METRICS_URI=/metrics

  httpbin-go:
    image: ghcr.io/mccutchen/go-httpbin
    container_name: httpbin-go
    restart: unless-stopped
```
Конфигурация с проверкой тела ответа:
```conf
global
    log stdout format raw daemon info
    maxconn 4096

defaults
    mode http
    maxconn global
    log global
    option httplog
    option log-health-checks
    timeout connect 5000ms
    timeout client 50000ms
    timeout server 50000ms

frontend metrics
    bind *:2376
    mode http
    stats enable
    stats uri "$STATS_URI"
    stats auth "$STATS_USER":"$STATS_PASS"
    stats refresh 5s
    http-request use-service prometheus-exporter if { path "$METRICS_URI" }
    no log

frontend frontend_httpbin
    bind *:8080
    default_backend backend_httpbin

backend backend_httpbin
    mode http
    balance roundrobin

    timeout check 10s
    default-server inter 5s fall 3 rise 3
    option httpchk GET /get
    http-check expect status 200
    http-check expect string "origin"

    server internal-httpbin-go httpbin-go:8080 check weight 20
    # server external-httpbin-go httpbingo.org:443 ssl verify none check weight 10
    server external-httpbin httpbin.org:443 ssl verify none check weight 10

```
- options:

`maxconn` максимальное количество одновременных соединений \
`nbproc` количество процессов HAProxy \
`option httplog` включает журналирование HTTP-трафика, полезно для отладки и мониторинга прохождения трафика через HAProxy и дает возможность просматривать HTTP-трафик в журнале, чтобы отслеживать запросы и ответы \
`option httpchk` отправлять HTTP-запросы к серверам в бэкенде, чтобы определить, работают ли они, это позволяет выявлять неработающие сервера и перераспределять запросы на работающие \
`option httpchk GET / HTTP/1.1\r\nHost:\ localhost` отправляет GET-запрос на корневой путь (/) используя версию протокола HTTP 1.1, Host:\ localhost - это часть заголовка Host, который также включается в HTTP-запрос и указывает на целевой хост, который проверяется \
`option tcp-check` активирует общую функцию TCP-проверок для всего бэкэнда, без необходимости указывать порт явно \
`tcp-check connect port 443` HAProxy будет устанавливать соединение с серверами в бэкенде на порту 443 для проверки, что серверы доступны и способны принимать соединения на этом порту

- balance:

`Round Robin (roundrobin)` алгоритм используемый по умолчанию, отправляет запросы на сервера по очереди \
`static-rr` похож на roundrobin, но он сохраняет порядок серверов в конфигурации \
`Least Connections (leastconn)` выбирает сервер с наименьшим количеством активных соединений, это полезно, если у серверов разная производительность или загруженность, так как запросы будут отправляться на менее загруженные серверы \
`source` использует IP-адрес источника (клиента) для привязки к одному и тому же серверу, это означает, что клиент всегда будет направляться к одному и тому же серверу, это полезно для сохранения состояния сеанса \
`uri` запросы с одним и тем же URL (до знака вопроса) будут переправляться на один и тот же сервер, это полезно для балансировки запросов к разным частям приложения \
`rdp-cookie` используется для балансировки запросов RDP (Remote Desktop Protocol), он анализирует cookie-заголовок RDP для принятия решений о направлении запросов

- server:

`ssl` использование SSL \
`verify none` отсутствие проверки сертификата \
`weight` распределение запросов по весу, если необходимо на определенный сервер отправлять больше запросов \
`inter` изменяет интервал между проверками, по умолчанию две секунды \
`fall` устанавливает допустимое количество неудачных проверок, по умолчанию три \
`rise` задает, сколько проходных проверок должно быть, прежде чем вернуть ранее отказавший сервер в ротацию, по умолчанию два \
`check port 443` указать явную проверку порта для конкретного сервера \
`check backup` параметр означает, что сервер будет использоваться только в случае, если все основные серверы становятся недоступными и не будет участвовать в балансировке, пока основные серверы функционируют

### Keepalive

**VRRP (Virtual Router Redundancy Protocol)** - сетевой протокол, предназначенный для увеличения доступности маршрутизаторов, выполняющих роль шлюза \
**VRRP-пакеты** - это специальные сообщения, которые узлы (маршрутизаторы/сервера) в VRRP-группе рассылают для сообщения своего состояния \
**VIP (Virtual IP)** - виртуальный IP адрес, который может автоматически переключаться между серверами в случае сбоя (frondend для haproxy/dns-rr), у кого в данный момент в сетевом интерфейсе прописан VIP, тот сервер и работает \
**Master** - сервер, на котором в данный момент активен VIP (отправляет VRRP-пакеты на backup nodes) \
**Backup** - сервера на которые переключится VIP, в случае сбоя мастера (следим за мастером) \
**VRID (virtual_router_id)** - сервера, объединенные общим виртуальным IP (VIP) образуют виртуальный роутер, уникальный идентификатор которого, принимает значения от 1 до 255. Сервер может одновременно состоять в нескольких VRID, при этом для каждой VRID должны использоваться уникальные виртуальные IP адреса. \
Master сервер с заданным интервалом отправляет VRRP пакеты на зарезервированный адрес multicast (многоадресной) рассылки или unicast на указанные ip-адреса, а все backup/slave сервера слушают этот адрес. Если Slave сервер не получает пакеты, он начинает процедуру выбора Master в соответствии с приоритетом, и если он переходит в состояние Master, то у него активирует VIP (поднимается виртуальный интерфейс) и отравляет gratuitous ARP. \
**Gratuitous ARP** - это вид ARP ответа, который обновляет MAC таблицу на подключенных коммутаторах, чтобы проинформировать о смене владельца виртуального IP-адреса и MAC-адреса для перенаправления трафика. При настройке VRRP, в качестве адреса для виртуального IP не используется реальный адрес сервера, так как, в случае сбоя, его адрес переместится на соседний, и при восстановлении, он окажется изолированным от сети, и чтобы вернуть свой адрес, нужно отправить в сеть VRRP пакет, но не будет IP адреса, с которого это возможно сделать.

`nano /etc/keepalived/keepalived.conf`
```conf
global_defs {
    enable_script_security
}

vrrp_script nginx_check {
    script "/usr/bin/curl http://127.0.0.1"
    interval 5
    user nginx
}

vrrp_instance web {
    state MASTER # на втором сервере BACKUP
    interface ens33
    virtual_router_id 110
    priority 255 # на втором сервере 100
    advert_int 2
    notify /etc/keepalived/notify-web.sh root
    virtual_ipaddress {
        192.168.3.110
    }
    track_interface {
        ens333
    }
    track_script {
        nginx_check
    }
}
```
`state <MASTER|BACKUP>` начальное состояние при запуске, в режиме nopreempt единственное допустимое значение - BACKUP \
`interface` интерфейс, на котором будет работать VRRP и подниматься VIP \
`virtual_router_id <0-255>` уникальный идентификатор VRRP экземпляра, должен совпадать на всех серверах одной группы \
`priority <0-255>` задает приоритет при выборе MASTER, сервер с большим числом приоритета становится MASTER \
`advert_int <число секунд>` определяет, с какой периодичностью мастер должен сообщать остальным о себе, и если по истечению данного периода сервера не получат от мастера широковещательный пакет, то они инициируют выборы нового мастера \
`nopreempt` если мастер пропал из сети, и был выбран новый мастер с меньшим приоритетом, то по возвращении старшего мастера, он останется в состоянии BACKUP, пока новый мастер не отвалится \
`preempt_delay` что бы мастером был конкретный сервер, то заменить настройку nopreempt на preempt_delay \
`notify` скрипт, который будет выполняться при каждом изменении состояния сервера, и имя пользователя, от имени которого данный скрипт будет выполняться (логирование или отправка на почту) \
`virtual_ipaddress` виртуальный IP-адрес (VIP), которые будет активирован на сервере в состоянии MASTER, должны совпадать на всех серверах внутри VRRP экземпляра \
`track_interface` мониторинг состояния интерфейсов, переводит VRRP экземпляр в состояние FAULT, если один из перечисленных интерфейсов находится в состоянии DOWN \
`track_script` мониторинг с использованием скрипта, который должен возвращать 0 если проверка завершилась успешно или 1, если проверка завершилась с ошибкой \
`fall <число>` количество раз, которое скрипт вернул не нулевое значение, при котором перейти в состояние FAULT \
`rise <число>` количество раз, которое скрипт вернул нулевое значение, при котором выйти из состояния FAULT \
`timeout <число>` время ожидания, пока скрипт вернет результат, после которого вернуть ненулевое значение

`journalctl -u keepalived` \
`cat /var/log/messages | grep -i keepalived` \
`tail /var/run/keepalived.INSTANCE.web.state`

## GlusterFS

[GlusterFS](https://github.com/gluster/glusterfs) — это распределенная и масштабируемая файловая система, которая объединяет хранилища с разных серверов в единое виртуальное сетевое хранилище, обеспечивая отказоустойчивость и высокую производительность без необходимости отдельного сервера для метаданных. Работает поверх обычных файловых систем (например, `ext4`) с помощью технологии FUSE (в пользовательском пространстве). 

```bash
# Определить имена хостов для всех нод
echo '
192.168.3.101  hv-us-101
192.168.3.105  rpi-105
192.168.3.106  rpi-106
' >> /etc/hosts

# Установить сервер на все ноды
add-apt-repository ppa:gluster/glusterfs-11
apt update
apt install glusterfs-server -y
systemctl start glusterd
systemctl enable glusterd
systemctl status glusterd

# Подключить (peer detach для отключения) все узлы к пулу Trusted Server Pool (TSP)
gluster peer probe rpi-105
gluster peer probe rpi-106
gluster peer status
gluster pool list

# Создать директорию хранения на всех нодах
mkdir /gluster

# Создать том в пуле и запустить его
gluster volume create docker-fs replica 3 hv-us-01:/gluster rpi-105:/gluster rpi-106:/gluster force
gluster volume start docker-fs
gluster volume status docker-fs
gluster volume info docker-fs
gluster volume list

echo 'localhost:/docker-fs /mnt glusterfs defaults,_netdev,backupvolfile-server=localhost 0 0' >> /etc/fstab
mount localhost:/docker-fs /mnt
df -h

# Монтирование с помощью клиента
apt install -y glusterfs-client
mount -t glusterfs server1:/gv0 /mnt/gluster-volume

# Включение модуля NFS для удаленного монтирования
gluster volume set docker-fs nfs.disable off
mount -t nfs hv-us-101:/docker-fs /mnt/gluster-nfs

# Включить CIFS/SMB протокол
gluster volume set docker-fs storage.brick-multiplex off
gluster volume set docker-fs server.allow-insecure on
```
