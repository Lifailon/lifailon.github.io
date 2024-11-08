---
author: "Lifailon"
date: "2024-05-20T03:00:00+03:00"
---

<p align="center">
    <a href="https://github.com/Lifailon/PS-Commands/blob/rsa/posh.md/#docker"><img title="Dpcler Commands Logo"src="Docker-Commands-Logo.png"></a>
</p>

---

- [WSL](#wsl)
- [Install](#install)
- [Mirror](#mirror)
- [Proxy](#proxy)
- [Run](#run)
- [Update](#update)
- [Stats](#stats)
- [Logs](#logs)
- [Volume](#volume)
- [Network](#network)
- [Inspect](#inspect)
- [Exec](#exec)
- [Prune](#prune)
- [Remove](#remove)
- [Docker Socket API](#docker-socket-api)
- [Docker TCP API](#docker-tcp-api)
- [Context](#context)
- [ctop](#ctop)
- [LazyDocker](#lazydocker)
- [Dockerfile](#dockerfile)
- [Push](#push)
- [Docker-Compose](#docker-compose)
- [Uptime-Kuma](#uptime-kuma)
- [Uptime-Kuma-Api](#uptime-kuma-api)
- [Swarm](#swarm)
- [Dozzle](#dozzle)
- [Dozzle-Auth](#dozzle-auth)
- [Portainer](#portainer)
- [Docker DotNet](#docker-dotnet)


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

## Dockerfile

`FROM` указывает базовый образ, на основе которого будет создаваться новый образ \
`LABEL` добавляет метаданные к образу в формате ключ-значение \
`ENV` устанавливает переменные окружения, которые будут доступны внутри контейнера \
`RUN` выполняет команды в контейнере во время сборки образа \
`COPY` копирует файлы и каталоги из указанного источника на локальной машине в файловую систему контейнера \
`ADD` копирует файлы и каталоги в контейнер, поддерживает URL и автоматическое извлечение архивов \
`CMD` определяет команду, которая будет выполняться при запуске контейнера, может быть переопределена при запуске \
`ENTRYPOINT` задает основную команду, которая будет выполняться при запуске контейнера \
`WORKDIR` устанавливает рабочий каталог внутри контейнера для последующих команд \
`ARG` определяет переменные, которые могут быть переданы на этапе сборки образа \
`VOLUME` создает точку монтирования для хранения данных, сохраняемых вне контейнера \
`EXPOSE` указывает, какие порты контейнера будут доступны извне \
`USER` устанавливает пользователя, от имени которого будут выполняться следующие команды \
`HEALTHCHECK` определяет команду для проверки состояния работающего контейнера \
`ONBUILD` задает команды, которые будут автоматически выполнены при сборке дочерних образов \
`STOPSIGNAL` определяет сигнал, который будет отправлен контейнеру для его остановки \
`SHELL` задает командную оболочку, которая будет использоваться для исполнения команд RUN, CMD, ENTRYPOINT и т.д.

`git clone https://github.com/Lifailon/TorAPI` \
`cd TorAPI` \
`nano Dockerfile`
```Dockerfile
# Указать базовый образ, который содержит последнюю версию Node.js и npm для создания контейнера
FROM node:latest
# Установить рабочую директорию для контейнера (все последующие команды будут выполняться относительно этой директории)
WORKDIR /torapi
# Копирует файл package.json из текущей директории на хосте в рабочую директорию контейнера
COPY package.json ./
# Запускает команду (используя оболочку по умолчанию) для установки зависимостей, указанных в package.json
RUN npm install
# Копирует все файлы из текущей директории на хосте в рабочую директорию контейнера
COPY . .
# Определяем переменные окружения по умолчанию
ENV PORT=8443
# Открывает порт 8443 для доступа к приложению из контейнера
EXPOSE $PORT
# Устанавливает команду по умолчанию для запуска при старте контейнера, которая запускает приложение с помощью npm start
CMD ["npm", "start"]
```
`docker build -t torapi .` собрать образ из dockerfile \
`docker run -d --name TorAPI -p 8443:8443 torapi`

## Push

`docker login` \
`git clone https://github.com/Lifailon/TorAPI` \
`cd TorAPI` \
`docker build -t lifailon/torapi .` собрать образ для публикации на Docker Hub \
`docker push lifailon/torapi` загрузить образ на Docker Hub

`docker pull lifailon/torapi:latest` загрузить образ из Docker Hub \
`docker run -d --name TorAPI -p 8443:8443 lifailon/torapi:latest` загрузить образ и создать контейнер

## Docker-Compose
```bash
curl -L "https://github.com/docker/compose/releases/download/1.27.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
```
`nano docker-compose.yml`
```yaml
version: "3.8"
services:
  torapi:
    image: lifailon/torapi:latest
    container_name: TorAPI
    volumes:
      - torapi:/rotapi
    ports:
      - "8443:8443"
    restart: unless-stopped
volumes:
  torapi:
```
`docker-compose up -d` при повторном запуске пересоздаст контейнер с изменениями

## Uptime-Kuma

`nano docker-compose.yml`
```yaml
version: "3.8"
services:
  uptime-kuma:
    image: louislam/uptime-kuma:1
    container_name: uptime-kuma
    volumes:
      - uptime-kuma:/app/data
    ports:
      - "8080:3001"
    restart: unless-stopped
volumes:
  uptime-kuma:
```
`docker-compose up -d`

## Uptime-Kuma-Api

`nano docker-compose.yml`
```yaml
version: "3.9"
services:
  kuma:
    container_name: uptime-kuma-frontend
    image: louislam/uptime-kuma:latest
    ports:
      - "8080:3001"
    restart: unless-stopped
    volumes:
      - uptime-kuma:/app/data
  api:
    container_name: uptime-kuma-backend-api
    image: medaziz11/uptimekuma_restapi
    volumes:
      - api:/db
    restart: unless-stopped
    environment:
      - KUMA_SERVER=http://kuma:3001
      - KUMA_USERNAME=admin
      - KUMA_PASSWORD=KumaAdmin
      - ADMIN_PASSWORD=KumaApiAdmin
    depends_on:
      - kuma
    ports:
      - "8081:8000"
volumes:
  uptime-kuma:
  api:
```
`docker-compose up -d`

OpenAPI doc: http://192.168.3.102:8081/docs \
`TOKEN=$(curl -s -X POST http://192.168.3.102:8081/login/access-token --data "username=admin" --data "password=KumaApiAdmin" | jq -r .access_token)` \
`curl -s -X GET -H "Authorization: Bearer ${TOKEN}" http://127.0.0.1:8081/monitors | jq .` \
`curl -s -X GET -H "Authorization: Bearer ${TOKEN}" http://127.0.0.1:8081/monitors/1 | jq '.monitor | "\(.name) - \(.active)"'`

## Swarm

`docker swarm init` инициализировать manager node и получить токен для подключения worker node (сервер) \
`docker swarm join-token manager` узнать токен подключения \
`docker swarm join --token SWMTKN-1-1a078rm7vuenefp6me84t4swqtvdoveu6dh2pw34xjcf2gyw33-81f8r32jt3kkpk4dqnt0oort9 192.168.3.101:2377` подключение на worker node (клиент) \
`docker node ls` отобразить список node на manager node \
`docker node inspect u4u897mxb1oo39pbj5oezd3um` подробная информация (конфигурация) о node по id \
`docker swarm leave --force` выйти из кластера на worker node (на manager node изменится статус с Ready на Down) \
`docker node rm u4u897mxb1oo39pbj5oezd3um` удалить node (со статусом Down) на manager node \
`docker pull lifailon/torapi:latest` \
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
`docker stack deploy -c docker-compose-stack.yml TorAPI` собрать стэк сервисов, определенных в docker-compose (на worker node появится контейнер TorAPI_torapi.1.ug5ngdlqkl76dt) \
`docker stack ls` отобразить список стэков \
`docker service ls` список сервисов всех стэков \
`docker stack ps TorAPI` список задач в стеке \
`docker stack services TorAPI` список сервисов внутри стэка указанного стэка по имени \
`docker service ps TorAPI_torapi` подробная информация о сервисе по его имени (TorAPI имя стэка и _torapi имя сервиса) \
`docker service inspect --pretty TorAPI_torapi` конфигурация сервиса \
`docker service inspect TorAPI_torapi` конфигурация сервиса в формате JSON \
`docker service logs TorAPI_torapi` журнала конкретного сервиса по всем серверам кластера \
`docker service scale TorAPI_torapi=3` масштабирует сервис до указанного числа реплик \
`docker stack rm TorAPI` удалить стэк (не требует остановки контейнеров)

## Dozzle

`docker run -d --name dozzle -v /var/run/docker.sock:/var/run/docker.sock -p 9999:8080 amir20/dozzle:latest` \
`docker run -d --name dozzle -v /var/run/docker.sock:/var/run/docker.sock -p 9999:8080 amir20/dozzle:latest --remote-host tcp://192.168.3.102:2375|mon-01` доступ к удаленному хосту через Docker-tcp-api \
http://192.168.3.102:9999

## Dozzle-Auth

`echo -n DozzleAdmin | shasum -a 256` получить пароль в формате sha-256 \
`mkdir dozzle && nano ./dozzle/users.yml` создать авторизационный файл

```yaml
users:
  admin:
    name: "admin"
    password: "a800c3ee4dac5102ed13ba673589077cf0a87a7ddaff59882bb3c08f275a516e"
```

`nano docker-compose.yml`

```yaml
version: "3.8"
services:
  dozzle:
    image: amir20/dozzle:latest
    container_name: dozzle
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./dozzle:/data
    ports:
      - 9999:8080
    environment:
      DOZZLE_AUTH_PROVIDER: simple
      DOZZLE_HOSTNAME: dozzle
      DOZZLE_REMOTE_HOST: tcp://192.168.3.102:2375|mon-01
```
`docker-compose up -d`

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

## Docker DotNet

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
