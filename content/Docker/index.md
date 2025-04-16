+++
title = "Docker"
[extra]
toc = true
toc_sidebar = true
go_to_top = true
+++

<p align="center">
    <a href="https://github.com/Lifailon/PS-Commands/blob/rsa/posh.md/#docker"><img title="Dpcler Commands Logo"src="Docker-Commands-Logo.png"></a>
</p>

<p align="center">
    Заметки по работе с системой контейнеризации 🐳 <b>Docker</b>.
</p>

---

## WSL

`wsl --list` список установленных дистрибутивов Linux \
`wsl --list --online` список доступных дистрибутивов \
`wsl --install -d Ubuntu` установить Ubuntu в Windows Subsystem for Linux \
`wsl --status` \
`wsl --exec "htop"` выполнить команду в подсистеме Linux по умолчанию \
`wsl -e bash -c "docker -v"` \
`wsl -e bash -c "systemctl status docker"`

## Install

`apt update && apt upgrade -y` \
`apt install docker.io` \
`systemctl status docker` \
`systemctl start docker` \
`systemctl enable docker` \
`iptables -t nat -N DOCKER` \
`docker -v` \
`docker -h` \
`curl https://registry-1.docker.io/v2/` проверить доступ к Docker Hub \
`curl -s -X POST -H "Content-Type: application/json" -d '{"username": "lifailon", "password": "password"}' https://hub.docker.com/v2/users/login | jq -r .token > dockerToken.txt` получить временный токен доступа для авторизации \
`sudo docker login` вход в реестр репозитория hub.docker.com \
`cat dockerToken.txt | sudo docker login --username lifailon --password-stdin` передать токен авторизации (https://hub.docker.com/settings/security) из файла через stdin \
`cat /root/.docker/config.json | jq -r .auths[].auth` место хранения токена авторизации в системе \
`cat /root/.docker/config.json | python3 -m json.tool`

## Mirror

`echo '{ "registry-mirrors": ["https://dockerhub.timeweb.cloud"] }' > "/etc/docker/daemon.json"` \
`echo '{ "registry-mirrors": ["https://huecker.io"] }' > "/etc/docker/daemon.json"` \
`echo '{ "registry-mirrors": ["https://mirror.gcr.io"] }' > "/etc/docker/daemon.json"` \
`echo '{ "registry-mirrors": ["https://daocloud.io"] }' > "/etc/docker/daemon.json"` \
`echo '{ "registry-mirrors": ["https://c.163.com"] }' > "/etc/docker/daemon.json"`

`systemctl restart docker`

## Proxy
```bash
mkdir -p /etc/systemd/system/docker.service.d

'[Service]
Environment="HTTP_PROXY=http://docker:password@192.168.3.100:9090"
Environment="HTTPS_PROXY=http://docker:password@192.168.3.100:9090"' > /etc/systemd/system/docker.service.d/http-proxy.conf
```
`systemctl daemon-reload` \
`systemctl restart docker`

## Run 

Commands: `search/pull/images/creat/start/ps/restart/pause/unpause/rename/stop/kill/rm/rmi`

`docker search speedtest` поиск образа в реестре \
`docker pull adolfintel/speedtest` скачать образ LibreSpeed из реестра Docker Hub (https://hub.docker.com/r/adolfintel/speedtest) \
`docker images (docker image ls)` отобразить все локальные (уже загруженные) образы docker (image ls) \
`docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"` отфильтровать вывод (json-формат) \
`docker create -it --name speedtest -p 8080:80 adolfintel/speedtest` создать контейнер из образа adolfintel/speedtest с именем speedtest и проброс 80 порта контейнера на 8080 порт хоста \
`docker start speedtest` запустить созданный контейнер \
`ss -ltp | grep 8080` проверить, что порт открыт \
`docker ps` отобразить все запущенные докер контейнеры \
`docker ps -a` список всех существующих контейнеров (для их запуска/удаления по NAMES/ID и код выхода Exited 0 - успешная остановка) \
`docker ps -s` размер контейнеров (--size) \
`docker restart speedtest` перезапустить контейнер \
`docker pause speedtest` приостановить контейнер \
`docker unpause uptime-kuma` возобновить работу контейнера \
`docker rename speedtest speedtest-2` переименоввать контейнер (docker rename old_name new_name) \
`docker stop speedtest-2` остановить работающий контейнер с отправкой главному процессу контейнера сигнал SIGTERM, и через время SIGKILL \
`docker kill uptime-kuma` остановить работающий контейнер с отправкой главному процессу контейнера сигнал SIGKILL \
`docker kill $(docker ps -q)` остановить все контейнеры \
`docker rm speedtest-2` удалить контейнер \
`docker rmi adolfintel/speedtest` удалить образ \
`docker run -p 8443:8443 -it --entrypoint /bin/sh container_name` запустить контейнер и подключиться к нему (даже если контейнер уходит в ошибку при запуске) \
`docker run -d --restart=unless-stopped --name openspeedtest -p 3000:3000 -p 3001:3001 openspeedtest/latest` загрузить образ OpenSpeedTest (https://hub.docker.com/r/openspeedtest/latest), создать контейнер и запустить в одну команду в фоновом режиме (-d/--detach, терминал возвращает контроль сразу после запуска контейнера, если не используется, можно видеть логи, но придется остановить контейнер для выхода) \
`docker rm openspeedtest && docker rmi openspeedtest/latest` удаляем контейнер и образ в одну команду \
`docker run --name pg1 -p 5433:5432 -e POSTGRES_PASSWORD=PassWord -d postgres` создать контейнер postgres (https://hub.docker.com/_/postgres) с параметрами (-e) \
`docker run -d --restart=always --name uptime-kuma -p 8080:3001 louislam/uptime-kuma:1` создать и запустить контейнер Uptime-Kuma (https://hub.docker.com/r/elestio/uptime-kuma) в режиме always, при котором контейнер должен перезапускаться автоматически, если он остановится или если перезапустится Docker (например, после перезагрузки хоста)

## Update

`docker update --restart unless-stopped uptime-kuma` изменить режим перезапуска контейнера после его остановки на unless-stopped (режим аналогичен always, но контейнер не будет перезапущен, если он был остановлен вручную с помощью docker stop) \
`docker update --restart on-failure uptime-kuma` контейнер будет перезапущен только в случае его завершения с ошибкой, когда код завершения отличается от 0, через двоеточие можно указать количество попыток перезапуска (например, on-failure:3) \
`docker update --cpu-shares 512 --memory 500M uptime-kuma` задать ограничения по CPU, контейнер будет иметь доступ к указанной доле процессорного времени в диапазоне от 2 до 262,144 (2^18) или --cpus (количество процессоров), --memory/--memory-swap и --blkio-weight для IOps (относительный вес от 10 до 1000)

## Stats

`docker stats` посмотреть статистику потребляемых ресурсов запущенными контейнерами (top) \
`docker stats --no-stream --format json` вывести результат один раз в формате json

## Logs

`docker logs uptime-kuma --tail 100` показать логи конкретного запущенного контейнера в терминале (последние 100 строк) \
`docker system events` предоставляют события от демона dockerd в реальном времени \
`journalctl -xeu docker.service` \
`docker system df` отобразить сводную информацию занятого пространства образами и контейнерами \
`du -h --max-depth=1 /var/lib/docker` \
`du -h --max-depth=2 /var/lib/docker/containers`

```bash
docker run \
  --log-driver json-file \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  container_name
```

`--log-driver json-file` стандартный драйвер логов Docker \
`--log-opt max-size=10m` устанавливаем максимальный размер каждого лог-файла в 10МБайт
`--log-opt max-file=3` сохраняем только 3 файла с логами (текущий и два предыдущих). Когда лимит будет превышен, Docker автоматически удалит старые логи.

## Volume

`docker volume ls` показывает список томов и место хранения (механизмы хранения постояннымх данных контейнера на хостовой машине, которые сохраняются между перезапусками или пересозданиями контейнеров) \
`docker volume inspect uptime-kuma` подробная информация конфигурации тома (отображает локальный путь к данным в системе, Mountpoint: /var/lib/docker/volumes/uptime-kuma/_data) \
`docker volume create test` создать том \
`docker volume rm test` удалить том \
`docker run -d --restart=always --name uptime-kuma -p 8080:3001 -v uptime-kuma:/app/data louislam/uptime-kuma:1` создать и запустить контейнер на указанном томе (том создается автоматически, в дальнейшем его можно указывать при создании контейнера, если необходимо загружать из него сохраненные данные)

## Network

`docker network ls` список сетей \
`docker network inspect bridge` подробная информация о сети bridge \
`docker inspect uptime-kuma | jq .[].NetworkSettings.Networks` узнать наименование сетевого адаптера указанного контейнера \
`docker run -d --name uptime-kuma --network host nginx louislam/uptime-kuma:1` запуск контейнера с использованием host сети, которая позволяет контейнеру использовать сеть хостовой машины \
`docker network create network_test` создать новую сеть \
`docker network connect network_test uptime-kuma` подключить работающий контейнер к указанной сети \
`docker network disconnect network_test uptime-kuma` отключить от сети

## Inspect

`docker inspect uptime-kuma` подробная информация о контейнере (например, конфигурация NetworkSettings) \
`docker inspect uptime-kuma --format='{{.LogPath}}'` показать, где хранятся логи для конкретного контейнера в локальной системе \
`docker inspect uptime-kuma | grep LogPath` \
`docker inspect $(docker ps -q) --format='{{.NetworkSettings.Ports}}'` отобразить TCP порты всех запущенных контейнеров \
`docker inspect $(docker ps -q) --format='{{.NetworkSettings.Ports}}' | grep -Po "[0-9]+(?=}])"` отобразить порты хоста (внешние) \
`docker port uptime-kuma` отобразить проброшенные порты контейнера \
`for ps in $(docker ps -q); do docker port $ps | sed -n 2p | awk -F ":" '{print $NF}'; done` отобразить внешние порты всех запущенных контейнеров \
`id=$(docker inspect uptime-kuma | jq -r .[].Id)` узнать ID контейнера по его имени в конфигурации \
`cat /var/lib/docker/containers/$id/config.v2.json | jq .` прочитать конфигурационный файл контейнера

## Exec

`docker exec -it uptime-kuma /bin/bash` подключиться к работающему контейнеру (при выходе из оболочки, контейнер будет работать), используя интерпритатор bash \
`docker top uptime-kuma` отобразить работающие процессы контейнера \
`docker exec -it --user root uptime-kuma bash apt-get install -y procps` авторизоваться под пользователем root и установить procps \
`docker exec -it uptime-kuma ps -aux` отобразить работающие процессы внутри контейнера \
`docker exec uptime-kuma kill -9 25055` убить процесс внутри контейнера \
`docker exec -it uptime-kuma ping 8.8.8.8` \
`docker exec -it uptime-kuma pwd` \
`docker cp ./Console-Performance.sh uptime-kuma:/app` скопировать из локальной системы в контейнер \
`docker exec -it uptime-kuma ls` \
`docker cp uptime-kuma:/app/db/ backup/db` сокпировать из контейнера в локальную систему \
`ls backup/db`

## Prune

`docker network prune && docker image prune && docker volume prune && docker container prune` удалить все неиспользуемые сети, висящие образа, остановленные контейнеры, все неиспользуемые тома \
`system prune –volumes` заменяет все четыре команды для очистки и дополнительно очищает кеш сборки

## Remove

`systemctl stop docker.service` \
`systemctl stop docker.socket` \
`pkill -f docker` \
`pkill -f containerd` \
`apt purge docker.io -y || dpkg --purge docker.io` \
`dpkg -l | grep docker` \
`rm -rf /var/lib/docker` \
`rm -rf /run/docker` \
`rm -rf /run/docker.sock`

## Docker Socket API

`curl --silent -XGET --unix-socket /run/docker.sock http://localhost/version | jq .` использовать локальный сокет (/run/docker.sock) для взаимодействия с Docker daemon через его API \
`curl --silent -XGET --unix-socket /run/docker.sock http://localhost/info | jq .` количество образов, запущенных и остановленных контейнеров и остальные метрики ОС \
`curl --silent -XGET --unix-socket /run/docker.sock http://localhost/events` логи Docker daemon \
`curl --silent -XGET --unix-socket /run/docker.sock -H "Content-Type: application/json" http://localhost/containers/json | jq .` список работающих контейнеров и их параметры конфигурации \
`curl --silent -XGET --unix-socket /run/docker.sock http://localhost/containers/uptime-kuma/json | jq .` подробные сведения (конфигурация) контейнера \
`curl --silent -XPOST --unix-socket /run/docker.sock -d "{"Image":"nginx:latest"}" http://localhost/containers/create?name=nginx` создать контейнер с указанным образом в теле запроса (должен уже присутствовать образ) \
`curl --silent -XPOST --unix-socket /run/docker.sock http://localhost/containers/17fab06a820debf452fe685d1522a9dd1611daa3a5087ff006c2dabbe25e52a1/start` запустить контейнер по Id \
`curl --silent -XPOST --unix-socket /run/docker.sock http://localhost/containers/17fab06a820debf452fe685d1522a9dd1611daa3a5087ff006c2dabbe25e52a1/stop` остановить контейнер \
`curl --silent -XDELETE --unix-socket /run/docker.sock http://localhost/containers/17fab06a820debf452fe685d1522a9dd1611daa3a5087ff006c2dabbe25e52a1` удалить контейнер

## Docker TCP API
```bash
echo '{
    "hosts": ["tcp://0.0.0.0:2375", "unix:///var/run/docker.sock"]
}' > "/etc/docker/daemon.json"
service=$(cat /lib/systemd/system/docker.service | sed "s/ -H fd:\/\///")
printf "%s\n" "$service" > /lib/systemd/system/docker.service
systemctl daemon-reload
systemctl restart docker
```
curl --silent -XGET http://192.168.3.102:2375/version | jq .

## Context

`docker context create devops-01 --docker "host=tcp://192.168.3.101:2375"` подключиться к удаленному сокету \
`docker context ls` список контекстов \
`docker context inspect devops-01` конфигурация указанного контекста \
`docker context use devops-01` использовать выбранный контекст по умолчанию (возможно на прямую взаимосдействовать с удаленным Docker Engine через cli) \
`docker context rm devops-01` удалить контекст

## ctop

`scoop install ctop` установка в Windows (https://github.com/bcicen/ctop)
```bash
wget https://github.com/bcicen/ctop/releases/download/v0.7.7/ctop-0.7.7-linux-amd64 -O /usr/local/bin/ctop
chmod +x /usr/local/bin/ctop
```
`ctop` отображает сводную таблицу (top) CPU, MEM, NET RX/TX, IO R/W \
`o` - графики \
`l` - логи контейнера в реальном времени \
`s` - stop/start \
`R` - remove после stop \
`p` - pause/unpause \
`r` - restart \
`e` - exec shell

## Dockly

`npm install -g dockly` TUI интерфейс на базе Node.js и Blessed.js \
`docker run -it --rm -v /var/run/docker.sock:/var/run/docker.sock lirantal/dockly` запуск в Docker \
`dockly`

## LazyDocker

`scoop install lazydocker || choco install lazydocker` установка в Windows (https://github.com/jesseduffield/lazydocker)
```bash
wget https://github.com/jesseduffield/lazydocker/releases/download/v0.23.1/lazydocker_0.23.1_Linux_x86.tar.gz -O ~/lazydocker.tar.gz
tar -xzf ~/lazydocker.tar.gz lazydocker
rm ~/lazydocker.tar.gz
mv lazydocker /usr/local/bin/lazydocker
chmod +x /usr/local/bin/lazydocker
```
lazydocker

## Lazyjournal

`curl -sS https://raw.githubusercontent.com/Lifailon/lazyjournal/main/install.sh | bash` установка в Unix \
`Invoke-RestMethod https://raw.githubusercontent.com/Lifailon/lazyjournal/main/install.ps1 | Invoke-Expression` установка в Windows \
`lazyjournal` \
`lazyjournal --help` \
`lazyjournal --version`

## Dockerfile

`FROM` указывает базовый образ, на основе которого будет создаваться новый образ \
`LABEL` добавляет метаданные к образу в формате ключ-значение \
`ENV` устанавливает переменные окружения, которые будут доступны внутри контейнера со значениями по умолчанию (можно переопределить через `-e`, который имеет повышенный приоритет) \
`ARG` определяет переменные, которые могут быть переданы и доступны только на этапе сборки образа (выполнения инструкций в dockerfile через `docker build --build-arg`) и недоступны в контейнере \
`USER` устанавливает пользователя, от имени которого будут выполняться следующие команды \
`WORKDIR` устанавливает рабочий каталог внутри контейнера для последующих команд \
`SHELL` задает командную оболочку, которая будет использоваться для выполнения команд RUN, CMD и ENTRYPOINT (по умолчанию `/bin/sh -c`, например на `SHELL ["/bin/bash", "-c"]`) \
`RUN` выполняет команды в контейнере во время сборки образа \
`COPY` копирует файлы и каталоги из указанного источника на локальной машине в файловую систему контейнера \
`ADD` копирует файлы и каталоги в контейнер, поддерживает загрузку файлов из URL и автоматическое извлечение архивов \
`CMD` определяет команду, которая будет выполняться при запуске контейнера, может быть переопределена при запуске \
`ENTRYPOINT` задает основную команду, которая будет выполняться при запуске контейнера без возможности ее переопредиления, но с возможностью передачи аргументов \
`VOLUME` создает точку монтирования для хранения данных в хостовой системе \
`EXPOSE` указывает, какие порты контейнера будут доступны извне \
`HEALTHCHECK` определяет команду для проверки состояния работающего контейнера \
`ONBUILD` задает команды, которые будут автоматически выполнены при сборке дочерних образов \
`STOPSIGNAL` определяет сигнал, который будет отправлен контейнеру для его остановки

`git clone https://github.com/Lifailon/TorAPI` \
`cd TorAPI` \
`nano Dockerfile`
```Dockerfile
# Указать базовый образ для сборки, который содержит последнюю версию Node.js и npm
FROM node:alpine AS build
# Установить рабочую директорию для контейнера (все последующие команды будут выполняться относительно этой директории)
WORKDIR /torapi
# Копирует файл package.json из текущей директории на хосте в рабочую директорию
COPY package.json ./
# Запускает команду (используя оболочку по умолчанию) для установки зависимостей, указанных в package.json
RUN npm install && npm update && npm cache clean --force
# Копирует все файлы из текущей директории на хосте в рабочую директорию контейнера
COPY . .
# Создает новый рабочий образ для создания контейнера
FROM node:alpine
WORKDIR /torapi
# Копирует только те файлы, которые необходимые для работы приложения
COPY --from=build /torapi/node_modules ./node_modules
COPY --from=build /torapi/package.json ./package.json
COPY --from=build /torapi/main.js ./main.js
COPY --from=build /torapi/swagger/swagger.js ./swagger/swagger.js
COPY --from=build /torapi/category.json ./category.json
# Определить переменные окружения по умолчанию, которые могут быть переопределены при запуске контейнера
ENV PORT=8443
ENV PROXY_ADDRESS=""
ENV PROXY_PORT=""
ENV USERNAME=""
ENV PASSWORD=""
# Открывает порт 8443 для доступа к приложению из контейнера
EXPOSE $PORT
# Определить команду для проверки работоспособности контейнера (для примера)
# Проверка будет запускаться каждые 120 секунд, если команда не завершится за 30 секунд, она будет считаться неуспешной, если команда не проходит 3 раза подряд, контейнер будет помечен как нездоровый
# Docker будет ждать 5 секунд после старта контейнера перед тем, как начать проверки здоровья
HEALTHCHECK --interval=120s --timeout=30s --retries=3 --start-period=10s \
    CMD ["sh", "-c", "npm start -- --test"]
# Устанавливает команду по умолчанию для запуска приложения при запуске контейнера
ENTRYPOINT ["sh", "-c", "npm start -- --port $PORT --proxyAddress $PROXY_ADDRESS --proxyPort $PROXY_PORT --username $USERNAME --password $PASSWORD"]
```
`docker build -t torapi .` собрать образ из dockerfile
```bash
docker run -d --name TorAPI -p 8443:8443 --restart=unless-stopped \
  -e PROXY_ADDRESS="192.168.3.100" \
  -e PROXY_PORT="9090" \
  -e USERNAME="TorAPI" \
  -e PASSWORD="TorAPI" \
  torapi
```
## Push

`docker login` \
`git clone https://github.com/Lifailon/TorAPI` \
`cd TorAPI` \
`docker build -t lifailon/torapi .` собрать образ для публикации на Docker Hub \
`docker push lifailon/torapi` загрузить образ на Docker Hub

`docker pull lifailon/torapi:latest` загрузить образ из Docker Hub \
`docker run -d --name TorAPI -p 8443:8443 lifailon/torapi:latest` загрузить образ и создать контейнер

## ADD

```bash
FROM alpine:latest
# Загрузка и распаковка архива напрямую из GitHub
ADD https://github.com/<username>/<repository>/archive/refs/heads/main.zip /app/
# Установка инструмента для работы с архивами
RUN apk add --no-cache unzip && \
    unzip /app/main.zip -d /app/ && \
    rm /app/main.zip
```

# Compose
```bash
version=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | jq -r .tag_name)
curl -L "https://github.com/docker/compose/releases/download/$version/docker-compose-$(uname -s)-$(uname -m)" -o $HOME/.local/bin/docker-compose
chmod +x $HOME/.local/bin/docker-compose
docker-compose --version
```
## Uptime-Kuma

[Uptime-Kuma](https://github.com/louislam/uptime-kuma) - веб-интерфейс для мониторинга доступности хостов (ICMP), портов (TCP), веб-контент (HTTP/HTTPS запросы), gRPC, DNS, контейнеры Docker, базы данных и т.д с поддержкой уведомлений в Telegram.

`nano docker-compose.yml`
```yaml
services:
  uptime-kuma:
    image: louislam/uptime-kuma:latest
    container_name: uptime-kuma
    volumes:
      - uptime-kuma:/app/data
    ports:
      - "8081:3001"
    restart: unless-stopped
volumes:
  uptime-kuma:
```
`docker-compose up -d`

`kuma_db=$(docker inspect uptime-kuma | jq -r .[].Mounts.[].Source)` место хранения конфигураций в базе SQLite \
`cp $kuma_db/kuma.db $HOME/uptime-kuma-backup.db`

Сгенерировать API ключ: `http://192.168.3.101:8081/settings/api-keys` \
`curl -u":uk1_fl3JxkSDwGLzQuHk2FVb8z89SCRYq0_3JbXsy73t" http://192.168.3.101:8081/metrics`

Пример конфигурации для Prometheus:
```yaml
scrape_configs:
  - job_name: uptime-kuma
    scrape_interval: 30s
    metrics_path: /metrics
    static_configs:
      - targets:
        - '192.168.3.101:8081'
    basic_auth:
      password: uk1_fl3JxkSDwGLzQuHk2FVb8z89SCRYq0_3JbXsy73t
```
Dashboard для Grafana - [Uptime Kuma - SLA/Latency/Certs](https://grafana.com/grafana/dashboards/18667-uptime-kuma-metrics) (id 18667)

[Uptime-Kuma-Web-API](https://github.com/MedAziz11/Uptime-Kuma-Web-API) - оболочка API и Swagger документация написанная на Python с использованием FastAPI и [Uptime-Kuma-API](https://github.com/lucasheld/uptime-kuma-api).

nano docker-compose.yml
```yaml
services:
  uptime-kuma-web:
    container_name: uptime-kuma-frontend
    image: louislam/uptime-kuma:latest
    ports:
      - "8081:3001"
    restart: unless-stopped
    volumes:
      - uptime-kuma:/app/data

  uptime-kuma-api:
    container_name: uptime-kuma-backend
    image: medaziz11/uptimekuma_restapi
    volumes:
      - uptime-api:/db
    restart: unless-stopped
    environment:
      - KUMA_SERVER=http://uptime-kuma-web:3001
      - KUMA_USERNAME=admin
      - KUMA_PASSWORD=KumaAdmin
      - ADMIN_PASSWORD=KumaApiAdmin
    depends_on:
      - uptime-kuma-web
    ports:
      - "8082:8000"

volumes:
  uptime-kuma:
  uptime-api:
```
`docker-compose up -d`

OpenAPI Docs (Swagger): http://192.168.3.101:8082/docs
```bash
TOKEN=$(curl -sS -X POST http://192.168.3.101:8082/login/access-token --data "username=admin" --data "password=KumaApiAdmin" | jq -r .access_token)
curl -s -X GET -H "Authorization: Bearer ${TOKEN}" http://192.168.3.101:8082/monitors | jq .
curl -s -X GET -H "Authorization: Bearer ${TOKEN}" http://192.168.3.101:8082/monitors/1 | jq '.monitor | "\(.name) - \(.active)"'
```
## Dozzle

Dozzle (https://github.com/amir20/dozzle) - легковесное приложение с веб-интерфейсом для мониторинга журналов Docker (без хранения).

`mkdir dozzle && cd dozzle && mkdir dozzle_data`

`echo -n DozzleAdmin | shasum -a 256` получить пароль в формате sha-256 и передать в конфигурацию
```yaml
echo '
users:
  admin:
    name: "admin"
    password: "a800c3ee4dac5102ed13ba673589077cf0a87a7ddaff59882bb3c08f275a516e"
' > ./dozzle_data/users.yml
```
Запускаем контейнер:
```yaml
echo '
services:
  dozzle:
    image: amir20/dozzle:latest
    container_name: dozzle
    restart: unless-stopped
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./dozzle_data:/data
    ports:
      - 9090:8080
    environment:
      DOZZLE_AUTH_PROVIDER: simple
      # Доступ к удаленному хосту через Docker API (tcp socket)
      # DOZZLE_REMOTE_HOST: tcp://192.168.3.102:2375|mon-01
' > docker-compose.yml
```
`docker-compose up -d`

## Watchtower

[Watchtower](https://github.com/containrrr/watchtower) - следить за тегом `latest` в реестре Docker Hub и обновлять контейнер, если он станет устаревшим.
```yaml
echo "
services:
  watchtower:
    image: containrrr/watchtower
    container_name: watchtower
    environment:
      - WATCHTOWER_LIFECYCLE_HOOKS=1
      - WATCHTOWER_NOTIFICATIONS=shoutrrr
      - WATCHTOWER_NOTIFICATION_URL=telegram://<BOT_API_KEY>@telegram/?channels=<CHAT/CHANNEL_ID>
      # - WATCHTOWER_HTTP_API_TOKEN=demotoken
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: --interval 600 --http-api-metrics --http-api-token demotoken # --http-api-update # --http-api-periodic-polls
    ports:
      - 8070:8080
    restart: unless-stopped
" > docker-compose.yml
```
`docker-compose up -d`

Проброс потра используется для получения метрик через Prometheus. Если нужно запускать обновления только через API, нужно добавить команду `--http-api-update`, или указать команду `--http-api-periodic-polls`, что бы использовать ручное и автоматическое обновление.

`curl -H "Authorization: Bearer demotoken" http://192.168.3.101:8070/v1/metrics` получить метрики \
`curl -H "Authorization: Bearer demotoken" http://192.168.3.101:8070/v1/update` проверить и запустить обновления

Добавить `scrape_configs` в `prometheus.yml` для сбора метрик:
```yaml
scrape_configs:
  - job_name: watchtower
    scrape_interval: 5s
    metrics_path: /v1/metrics
    bearer_token: demotoken
    static_configs:
      - targets:
        - '192.168.3.101:8070'
```
`docker-compose restart prometheus`

Чтобы исключить обновления, нужно добавить "lable" при запуске контейнера:
```bash
docker run -d --name kinozal-bot \
  -v /home/lifailon/kinozal-bot/torrents:/home/lifailon/kinozal-bot/torrents \
  --restart=unless-stopped \
  --label com.centurylinklabs.watchtower.enable=false \
  kinozal-bot
```
## Portainer

`curl -L https://downloads.portainer.io/portainer-agent-stack.yml -o portainer-agent-stack.yml` скачать yaml файл \
`version_update=$(cat portainer-agent-stack.yml | sed "s/2.11.1/latest/g")` \
`printf "%s\n" "$version_update" > portainer-agent-stack.yml` обновить версию в yaml файле на последнюю доступную в Docker Hub (2.19.5) \
`docker stack deploy -c portainer-agent-stack.yml portainer` развернуть в кластере swarm (на каждом node будет установлен агент, который будет собирать данные, а на manager будет установлен сервер с web панелью) \
https://192.168.3.101:9443

`docker run -d --name portainer_agent -p 9001:9001 --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/volumes:/var/lib/docker/volumes portainer/agent:2.19.5` установить агент на удаленный хост \
https://192.168.3.101:9443/#!/endpoints добавить удаленный хост по URL 192.168.3.102:9001

`docker volume create portainer_data` создать volume для установки локального контейнера (не в кластер swarm) \
`docker create -it --name=portainer -p 9000:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer` создать локальный контейнер \
`docker start portainer` \
http://192.168.3.101:9000

# Docker.DotNet
```PowerShell
# Импорт библиотеки Docker.DotNet (https://nuget.info/packages/Docker.DotNet/3.125.15)
Add-Type -Path "$home\Documents\Docker.DotNet-3.125.15\lib\netstandard2.1\Docker.DotNet.dll"
# Указываем адрес удаленного сервера Docker, на котором слушает сокет Docker API
$config = [Docker.DotNet.DockerClientConfiguration]::new("http://192.168.3.102:2375")
# Подключаемся клиентом
$client = $config.CreateClient()
# Получить список методов класса клиента
$client | Get-Member
# Выводим список контейнеров
$containers = $client.Containers.ListContainersAsync([Docker.DotNet.Models.ContainersListParameters]::new()).GetAwaiter().GetResult()
# Забираем id по имени
$kuma_id = $($containers | Where-Object names -match "uptime-kuma-front").id
# Получить список дочерних методов
$client.Containers | Get-Member
# Остановить контейнер по его id
$StopParameters = [Docker.DotNet.Models.ContainerStopParameters]::new()
$client.Containers.StopContainerAsync($kuma_id, $StopParameters)
# Запустить контейнер
$StartParameters = [Docker.DotNet.Models.ContainerStartParameters]::new()
$client.Containers.StartContainerAsync($kuma_id, $StartParameters)
```
# Swarm

`docker swarm init` инициализировать manager node и получить токен для подключения worker node (сервер) \
`docker swarm join-token manager` узнать токен подключения \
`docker swarm join --token SWMTKN-1-1a078rm7vuenefp6me84t4swqtvdoveu6dh2pw34xjcf2gyw33-81f8r32jt3kkpk4dqnt0oort9 192.168.3.101:2377` подключение на worker node (клиент) \
`docker node ls` отобразить список node на manager node \
`docker node inspect u4u897mxb1oo39pbj5oezd3um` подробная информация (конфигурация) о node по id \
`docker swarm leave --force` выйти из кластера на worker node (на manager node изменится статус с Ready на Down) \
`docker node rm u4u897mxb1oo39pbj5oezd3um` удалить node (со статусом Down) на manager node \
`docker pull lifailon/torapi:latest`

`nano docker-compose-stack.yml`
```yaml
version: "3.8"
services:
  torapi:
    image: lifailon/torapi:latest
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
      update_config:
        order: start-first
      # Режим виртуального IP для балансировки нагрузки
      endpoint_mode: vip
    volumes:
      - torapi:/rotapi
    ports:
      # Порт внутри контейнера
      - target: 8443
        published: 8443
        protocol: tcp
        # Режим балансировки нагрузки по умолчанию
        mode: ingress
volumes:
  torapi:
```
`docker stack deploy -c docker-compose-stack.yml TorAPI` собрать стек сервисов, определенных в docker-compose (на worker node появится контейнер TorAPI_torapi.1.ug5ngdlqkl76dt)

`docker stack ls` отобразить список стеков \
`docker service ls` список сервисов всех стеков \
`docker stack ps TorAPI` список задач в стеке \
`docker stack services TorAPI` список сервисов внутри стека указанного стека по имени \
`docker service ps TorAPI_torapi` подробная информация о сервисе по его имени (TorAPI имя стека и _torapi имя сервиса) \
`docker service inspect --pretty TorAPI_torapi` конфигурация сервиса \
`docker service inspect TorAPI_torapi` конфигурация сервиса в формате JSON \
`docker service logs TorAPI_torapi` журнала конкретного сервиса по всем серверам кластера \
`docker service scale TorAPI_torapi=3` масштабирует сервис до указанного числа реплик \
`docker stack rm TorAPI` удалить стек (не требует остановки контейнеров)

# Kubernetes

## Micro8s

[Micro8s](https://github.com/canonical/microk8s) - это полностью совместимый и легкий Kubernetes в одном пакете, работающий на 42 разновидностях Linux.

`snap install microk8s --classic` установка \
`microk8s status --wait-ready` отобразить статус работы (дождаться инициализации служб Kubernetes) и список дополнений \
`microk8s start` запустить или остановить (stop) MicroK8s и его службы \
`microk8s enable dashboard` запустить dashboard \
`microk8s enable dns` установка обновлений \
`sudo usermod -a -G microk8s $USER && mkdir -p ~/.kube && chmod 0700 ~/.kube` добавить текущего пользователя в группу управления microk8s (создается при установке) \
`alias kubectl='microk8s kubectl'` добавить псевдоним, для использования команды kubectl через microk8s \
`kubectl get nodes` отобразить список нод \
`kubectl config view --raw > $HOME/.kube/config` передать конфигурацию в MicroK8s, для использования с существующим kubectl

## k3s

[K3s](https://github.com/k3s-io/k3s) — это полностью совместимый дистрибутив Kubernetes в формате единого двоичного файле, который удаляет хранение драйверов и поставщика облачных услуг, а также добавляет поддержку sqlite3 для backend хранилища.

`curl -sfL https://get.k3s.io | sh -` установка службы в systemd и утилит `kubectl`, `crictl`, `k3s-killall.sh` и `k3s-uninstall.sh` \
`/etc/rancher/k3s/k3s.yaml` конфигурация \
`/var/lib/rancher/k3s/server/node-token` токен авторизации \
`curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=XXX sh -` передать переменные окружения K3S_URL и K3S_TOKEN токен для установки на рабочие ноды \
`sudo k3s server &` запустить сервер кластера \
`sudo k3s agent --server https://myserver:6443 --token ${NODE_TOKEN}` подключиться к кластеру \
`sudo k3s kubectl get nodes` отобразить список нод в кластере

## Minikube

[Minikube](https://github.com/kubernetes/minikube) - это локальный кластер Kubernetes от создателя оригинального k8s
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-windows-amd64.exe
mv minikube-windows-amd64.exe minikube.exe
```
`minikube start --vm-driver=hyperv --memory=4g --cpus=2` запустить кластер и/или создать виртуальную машину \
`minikube status` статус работы кластера \
`minikube stop` остановить кластер \
`minikube delete` удалить виртуальную машину \
`minikube profile list` узнать информацию о драйвере, ip, версии и количество Nodes \
`minikube dashboard --port 8085` запустить api сервер и интерфейс состояния

`minikube addons list`  список доступных дополнений и их статус работы \
`minikube addons enable metrics-server` активировать дополнение, которое предоставляет метрики для HPA, такие как загрузка процессора и использование памяти \
`kubectl get deployment metrics-server -n kube-system` текущее состояние развертывания metrics-server в кластере \
`kubectl get pod,svc -n kube-system` отобразить список системных подов и сервисов в кластере (pod/metrics-server-7fbb699795-wvfxb) \
`kubectl logs -n kube-system deployment/metrics-server` отобразить логи metrics-server \
`kubectl top pods` отобразить метрики на подах (CPU/MEM) \
`minikube addons disable metrics-server` отключить дополнение

`minikube addons enable ingress` включить Nginx Ingress Controller \
`kubectl get pods -n kube-system` отобразить список системных подов (должен появиться ingress-nginx-controller) \
`minikube tunnel --alsologtostderr` создает виртуальный LoadBalancer в Minikube, для перенаправления трафика на нужный сервис, вместо использования NodePort

## kubectl

`Node` - физическая или виртуальная машина, на которой работает Kubernetes-кластер, каждый узел выполняет контейнеры и поды \
`Pod` - содержит один или несколько контейнеров работающих вместе, которые всегда разворачиваются в кластере \
`Deployment` - управляет состоянием подов и отвечает за масштабируемость (автоматический перезапуск контейнеров и замена подов при сбоях), чтобы их количество соответствовало желаемому числу реплик (ReplicaSet) \
`Service` - абстракция, которая отвечает за балансировку нагрузки (обрабатывает входящий трафик и распределяет его между подами), а также обеспечивая стабильный IP-адрес и DNS-имя для общения с ними

`kubectl config view` отобразить конфигурацию кластера (настройка подключения kubectl к Kubernetes, которое взаимодействует с приложением через конечные точки REST API) \
`sudo cp ~/.minikube/ca.crt /usr/local/share/ca-certificates/minikube.crt && update-ca-certificates && openssl verify /usr/local/share/ca-certificates/minikube.crt` установка сертификатов в Linux \
`Import-Certificate -FilePath "$HOME\.minikube\ca.crt" -CertStoreLocation Cert:\LocalMachine\Root && Import-Certificate -FilePath "$HOME\.minikube\profiles\minikube\client.crt" -CertStoreLocation Cert:\CurrentUser\My && ls Cert:\LocalMachine\Root | Where-Object Subject -Match "minikube"` установка сертификатов в Windows \
`curl -k https://192.168.27.252:8443/version` удаленный доступ к API Kubernetes (адрес и порт можно взять из config view) \
`kubectl get namespaces` вывести все namespace \
`kubectl get nodes` отобразить список node и их статус работы, роль (master/node), время запуска и версию \
`kubectl get events` отобразить логи кластера

`kubectl create deployment test-node --image=registry.k8s.io/e2e-test-images/agnhost:2.39 -- /agnhost netexec --http-port=8080` создать под из указанного Docker образа (запускает контейнер и внутри него команду для запуска веб-сервера на порту 8080) \
`kubectl get deployments` статус всех Deployments (контроллеров), которые в свою очередь управляют Pod-ами (RADY - количество экземпляров-реплик, UP-TO-DATE — количество реплик, которые были обновлены) \
`kubectl get pods` статус всех подов \
`kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}'` получить список имен всех под через шаблон фильтра

`kubectl proxy` запустить прокси сервер для локального взаимодействия с частной сетью кластера через API (без авторизации), где автоматически создаются конечные точки для каждого пода в соответствии с его именем \
`curl http://localhost:8001` отобразить список всех конечных точек (endpoints) \
`curl http://localhost:8001/api/v1/namespaces/default/pods/test-node-69f66d85f8-2d2tv:8080/proxy/` конечная точка, которая проксирует запрос внутрь пода по его имени (напрямую к приложению в контейнере) \
`kubectl port-forward pod/test-node-69f66d85f8-2d2tv 8080:8080` запустить сервер для проброса порта из пода \
`curl http://localhost:8080`

`kubectl expose deployment test-node --type=LoadBalancer --port=8080` предоставить pod как service (пробросить порт из частной сети Kubernetes) в режиме балансировки нагрузки \
`--type=ClusterIP` - открывает доступ к сервису по внутреннему IP-адресу в кластере (по умолчанию), этот тип делает сервис доступным только внутри кластера \
`--type=NodePort` - открывает сервис на том же порту каждого выбранного узла в кластере с помощью NAT, и делает сервис доступным вне кластера через `<NodeIP>:<NodePort>` (надмножество ClusterIP) \
`--type=LoadBalancer` - создает внешний балансировщик нагрузки и назначает фиксированный внешний IP-адрес для сервиса (надмножество NodePort) \
`--type=ExternalName` - открывает доступ к сервису по содержимому поля externalName (например, foo.bar.example.com), возвращая запись CNAME с его значением

`kubectl get services` отобразить список сервисов (CLUSTER-IP, EXTERNAL-IP и PORT 8080:32467/TCP), которые принимают внешний трафик \
`kubectl describe services test-node` отобразить настройки сервиса для внешнего доступа (ip, тип сервиса и конечные точки) \
`curl http://192.168.27.252:32467` проверить доступность приложения

`kubectl describe pods test-node` отобразить какие контейнеры находятся внутри пода, а также какие образы и команды (/agnhost netexec --http-port=8080) использовались при сборке этих контейнеров \
`kubectl logs test-node-69f66d85f8-2d2tv` отобразить логи контейнера в поде (сообщения, которые приложение отправляет в standard output) \
`kubectl exec test-node-69f66d85f8-2d2tv -c agnhost -- ls -lha` выполнить команду в контейнере указанного пода \
`kubectl exec test-node-69f66d85f8-2d2tv -c agnhost -- env` отобразить список глобальных переменных в контейнере \
`kubectl exec -it test-node-69f66d85f8-2d2tv -c agnhost -- curl http://localhost:8080` проверить доступность приложения внутри контейнера \
`kubectl exec -it test-node-69f66d85f8-2d2tv -c agnhost -- bash` запустить bash сессию в контейнере пода

`kubectl get rs` состояние реплик (ReplicaSet) для всех deployment \
`kubectl scale deployments/test-node --replicas=4` масштабируем deployment до 4 реплик \
`kubectl scale deployments/test-node --replicas=2` уменьшить deployment до 2 реплик подов \
`kubectl describe deployments/test-node` изменения фиксируется в конфигурации deployment -> Events (Scaled down replica set test-node-69f66d85f8 from 4 to 2) \
`kubectl get rs` проверить текущее количество под в deployment и их состояние (DESIRED - желаемое количество экземпляров-реплик и CURRENT - текущее количество реплик) \
`kubectl get endpoints test-node` отобразить на какие адреса (ip и порт) подов перенаправляется трафик сервиса test-node \
`kubectl get pods -o wide` отобразить количество всех подов (у каждого пода разное время работы в AGE и свой ip-адрес)

`kubectl logs -l app=test-node --follow` выводить лог в реальном времени для всех запущенных репликах подов указанного deployment \
`PODS_NAME=$(kubectl get pods -l app=test-node -o jsonpath="{.items[*].metadata.name}")` получаем названия всех подов указанного deployment \
`for POD_NAME in $PODS_NAME; do kubectl logs $POD_NAME --follow | awk -v pod=$POD_NAME '{print "[" pod "] " $0}' & done` отобразить лог приложения конкретного пода по имени \
`NODE_PORT="$(kubectl get services test-node -o go-template='{{(index .spec.ports 0).nodePort}}')"` получить порт указанного сервиса \
`for i in {1..5}; do curl -s "http://$(minikube ip):$NODE_PORT"; echo ""; done` каждый запрос будет попадать на разный под

`kubectl delete service test-node` удалить службу \
`kubectl delete deployment test-node` удалить под

`kubectl run busybox --rm -it --image=busybox:latest -- /bin/sh` создание временного пода для отладки (контейнер busybox, который можно использовать для отладки сети и команд curl, ping и т.д.)

`kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 \
`kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080 \
`kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=docker.io/jocatalin/kubernetes-bootcamp:v2` выполнение плавающего обновления версии образа работающего контейнера \
`kubectl rollout status deployments/kubernetes-bootcamp` проверить статус обновления \
`kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v10` выполнить обновление на несуществующую версию \
`kubectl rollout undo deployments/kubernetes-bootcamp` откатить deployment к последней работающей версии (к предыдущему известному состоянию в образе v2)

`kubectl get configmap` Получить все ConfigMap \
`kubectl describe configmap kube-root-ca.crt` отобразить содержимое корневого сертифика

## Deployment
```yaml
echo '
apiVersion: apps/v1
kind: Deployment
metadata:
  name: torapi # ммя Deployment, который управляет созданием подов (Pods)
spec:
  replicas: 2 # количество реплик (2 пода с одинаковыми настройками)
  selector:
    matchLabels:
      app: torapi # определяет, какие поды будут управляться этим Deployment
  template:
    metadata:
      labels:
        app: torapi  # метка, которая связывает этот шаблон с селектором выше
    spec:
      containers:
      - name: torapi                    # имя контейнера внутри пода
        image: lifailon/torapi:latest   # используемый образ контейнера
        ports:
        - containerPort: 8443           # порт, который будет открыт внутри контейнера
        resources:                      # ограничения и минимальные требования по ресурсам
          requests:
            cpu: "100m"                 # Минимальный запрашиваемый процессор (100 милли-ядра)
            memory: "64Mi"              # Минимальный запрашиваемый объем оперативной памяти (64 МБайт)
          limits:
            cpu: "200m"                 # Максимально доступное процессорное время 
            memory: "256Mi"             # Максимальный объем памяти
        livenessProbe:                  # Проверка работоспособности контейнера
          httpGet:
            path: /api/provider/list    # endpoint контейнера, по которому проверяется работоспособность
            port: 8443                  # порт, на котором доступен этот endpoint внутри контейнера
          initialDelaySeconds: 5        # ждет 5 секунд после запуска контейнера перед первой проверкой
          periodSeconds: 10             # интервал проверки (повторяет проверку каждые 10 секунд)
          timeoutSeconds: 3             # максимальное время ожидания ответа
          failureThreshold: 3           # количество неудачных попыток перед рестартом
' > torapi-deployment.yaml
```
`kubectl apply -f torapi-deployment.yaml`
```yaml
echo '
apiVersion: v1
kind: Service
metadata:
  name: torapi-service
  namespace: default
spec:
  selector:
    app: torapi
  ports:
    - protocol: TCP
      port: 8444        # Внутренний порт сервиса
      targetPort: 8443  # Порт контейнера
      nodePort: 30000   # Фиксированный внешний порт (valid range 30000-32767)
  type: LoadBalancer
' > torapi-service.yaml
```
`kubectl apply -f torapi-service.yaml`

`kubectl get pods` будет создано два пода \
`kubectl logs torapi-54775d94b8-vp26b` отобразить логи пода, будут идти запросы от 10.244.0.1 (kube-probe/1.32) для проверки доступности \
`kubectl exec -it torapi-54775d94b8-vp26b -- npm --version` вывести версию npm внутри контейнера \
`kubectl get services torapi-service` \
`kubectl describe service torapi-service` узнать Server Port, TargetPort (container) и NodePort \
`kubectl port-forward --address 0.0.0.0 service/torapi-service 8444:8444` \
`curl http://192.168.3.100:8444/api/provider/list`

## HPA

`HPA` (Horizontal Pod Autoscaling) - горизонтальное масштабирование позволяет автоматически увеличивать или уменьшать количество реплик (подов) в зависимости от текущей нагрузки по показателям метрик, получаемых из `metrics-server`.

`kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml` активировать metrics-server в Docker-Desktop (загрузить и применить конфигурацию) \
`kubectl logs -n kube-system deployment/metrics-server` проверить логи metrics-server \
`kubectl get deployment metrics-server -n kube-system` отобразить статус работы metrics-server \
`kubectl top nodes` отобразить метрики ресурсов для всех узлов в кластере

`kubectl edit deployment metrics-server -n kube-system` отключить проверку TLS
```yaml
spec:
  containers:
  - args:
    - --kubelet-insecure-tls
```
`kubectl rollout restart deployment metrics-server -n kube-system` перезапустить metrics-server
```yaml
echo '
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: torapi-hpa
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: torapi
  minReplicas: 1
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50 # когда среднее использование CPU превышает 50%, будет увеличено количество реплик, чтобы уменьшить нагрузку на поды
' > torapi-hpa.yaml
```
`kubectl apply -f torapi-hpa.yaml`

`kubectl get hpa` отобразить статус работы всех HPA и текущие таргеты (cpu: 1%/50%) \
`kubectl get pods` будет активен 1 под из 5 подов (вместо двух, изначально определенных в Deployment)

## Ingress

`Ingress` - это балансировщик нагрузки, который также управляет HTTP/HTTPS трафиком в кластер и направляет его к нужным логическим сервисам (маршрутизация запросов к разным конечным точкам в path).

`kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml` установить Ingress Controller \
`kubectl get pods -n ingress-nginx` \
`kubectl get svc -n ingress-nginx`
```yaml
echo '
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: torapi-ingress
  namespace: default
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: torapi.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: torapi-service
            port:
              number: 8444
' > torapi-ingress.yaml
```
`kubectl apply -f torapi-ingress.yaml` \
`kubectl get ingress` отобразить статус работы ingress

Настраиваем `HPA` на основе 100 и выше HTTP-запросов в секунду через метрику `nginx_ingress_controller_requests`:
```yaml
echo '
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: torapi-hpa
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: torapi
  minReplicas: 1
  maxReplicas: 5
  metrics:
  - type: External
    external:
      metric:
        name: nginx_ingress_controller_requests
      target:
        type: Value
        value: "100"
' > torapi-hpa.yaml
```
`kubectl apply -f torapi-hpa.yaml` \
`kubectl get hpa` отобразить статус работы HPA

## Secrets

`kubectl create secret generic admin-password --from-literal=username=admin --from-literal=password=Secret2025` создать секрет в формате ключ-значение \
`kubectl create secret generic api-key --from-file=api-key.txt` создать секрет из содержимого файла \
`kubectl get secret` получить список всех секретов \
`kubectl describe secret admin-password` получить информацию о секрете (размер в байтах) \
`kubectl get secret admin-password -o yaml` получить содержимое секретов в кодировке base64 \
`kubectl get secret admin-password -o jsonpath="{.data.password}" | base64 --decode` декодировать содержимое секрета \
`kubectl delete secret admin-password` удалить секрет
```yaml
echo '
apiVersion: v1
kind: Secret
metadata:
  name: admin-password
type: Opaque
data:
  username: YWRtaW4=
  password: U2VjcmV0MjAyNQ==
' > admin-secret.yaml
```
`kubectl apply -f admin-secret.yaml`

Передать secret в контейнер через переменные окружения:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secret-test
spec:
  containers:
  - name: nginx-secret-test
    image: nginx
    env:
    - name: USERNAME
      valueFrom:
        secretKeyRef:
          name: admin-password  # Имя секрета
          key: username         # Ключ в секрете
    - name: PASSWORD
      valueFrom:
        secretKeyRef:
          name: admin-password
          key: password
```
## Kompose

[Kompose](https://github.com/kubernetes/kompose) - это инструмент, который конвертируемт спецификацию docker-compose в файлы Kubernetes.

`curl -L https://github.com/kubernetes/kompose/releases/download/v1.35.0/kompose-linux-amd64 -o kompose` установка \
`kompose --file docker-compose.yaml convert` конвертация

## k9s

[K9s](https://github.com/derailed/k9s) - это TUI интерфейс для взаимодействия с кластерами Kubernetes (управление и чтение логов).

`snap install k9s --devmode || wget https://github.com/derailed/k9s/releases/download/v0.32.7/k9s_linux_amd64.deb && apt install ./k9s_linux_amd64.deb && rm k9s_linux_amd64.deb` \
`winget install k9s || scoop install k9s || choco install k9s || curl.exe -A MS https://webinstall.dev/k9s | powershell`
