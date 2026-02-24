+++
title = "Compose Stacks"
[extra]
toc = true
toc_sidebar = true
go_to_top = true
+++

<p align="center">
    <a href="https://github.com/Lifailon/rudocs/tree/main/Docker-Compose"><img title="Compose Stacks"src="logo.png"></a>
</p>

<p align="center">
    Коллекция стеков Docker Compose из более чем 300 сервисов. Каждое приложение было отлажено и проверено в домашней лаборатории, конфигурации к некоторым сервисам доступны в исходном <a href="https://github.com/Lifailon/rudocs/tree/main/Docker-Compose">репозитории</a>.
</p>

---
services:
  autobrr:
    image: ghcr.io/autobrr/autobrr:develop
    container_name: autobrr
    restart: unless-stopped
    volumes:
      - ./config:/config
    ports:
      - 7474:7474

  autobrr-postgres:
    image: postgres:12.10
    container_name: autobrr-postgres
    restart: unless-stopped
    volumes:
      - ./postgres_data:/var/lib/postgresql/data
    ports:
      - 5432:5432
    environment:
      - POSTGRES_USER=autobrr
      - POSTGRES_PASSWORD=autobrr
      - POSTGRES_DB=autobrr
```

## Game Stack

### Sunshine

[Sunshine](https://github.com/LizardByte/Sunshine) - самостоятельный хостинг-сервер игровых трансляций (like NVIDIA GameStream и Parsec) для клиента [Moonlight](https://github.com/moonlight-stream/moonlight-qt).

```yaml
services:
  sunshine:
    image: lizardbyte/sunshine:latest-ubuntu-24.04
    container_name: sunshine
    restart: unless-stopped
    volumes:
      - ./sunshine_config:/config
    environment:
      - PUID=1001
      - PGID=1001
      - TZ=Etc/GMT+3
    ports:
      - 47984-47990:47984-47990/tcp
      - 47998-48000:47998-48000/udp
      - 48010:48010
    ipc: host
```

### VDD

[VDD](https://github.com/VirtualDrivers/Virtual-Display-Driver) (Virtual Display Driver) - драйвер для создания виртуального монитора в Windows, который функционирует точно так же, как физический. Он используется в связке с приложениями для потоковой передачи видео, например, [Sunshine](ttps://github.com/LizardByte/Sunshine).

После установки драйвера в интерфейсе Sunshine идем в Troubleshooting и ищем по содержимому логов идентификатор виртуального монитора. 

```json
[
  {
    "device_id": "{d54a4360-26df-5f1a-b161-298c03c03b66}",
    "display_name": "\\\\.\\DISPLAY8",
    "edid": {
      "manufacturer_id": "MTT",
      "product_code": "1337",
      "serial_number": 518463207
    },
    "friendly_name": "VDD by MTT",
    "info": {
      "hdr_state": null,
      "origin_point": {
        "x": 3840,
        "y": 1468
      },
      "primary": false,
      "refresh_rate": {
        "type": "rational",
        "value": {
          "denominator": 1,
          "numerator": 60
        }
      },
      "resolution": {
        "height": 1080,
        "width": 1920
      },
      "resolution_scale": {
        "type": "rational",
        "value": {
          "denominator": 100,
          "numerator": 150
        }
      }
    }
  }
]
```

Переходим в `Configuration` => `Audio/Video` и в после `config.output_name_windows` всталяем содержимое из `device_id`: `{d54a4360-26df-5f1a-b161-298c03c03b66}` для захвата изображения с виртуального дисплея при подключение.

### Wolf

[Wolf](https://github.com/games-on-whales/wolf) - потоковый сервер для [Moonlight](https://github.com/moonlight-stream/moonlight-qt), который позволяет нескольким удаленным пользователям совместно использовать один сервер для игр. Особенности включают поддержку многопользовательского режима, создание виртуальных столов с возможностью настройки разрешения и FPS, а также одновременное использование различных графических процессоров для задач, таких как кодирование и игры. Сервер обеспечивает низкую задержку в стриминге видео и аудио, совместим с игровыми контроллерами и ориентирован на Linux и Docker, что обеспечивает безопасность в низкопривилегированных контейнерах.

```yaml
services:
  wolf:
    image: ghcr.io/games-on-whales/wolf:stable
    container_name: wolf
    restart: unless-stopped
    device_cgroup_rules:
      - 'c 13:* rmw'
    volumes:
      - /etc/wolf/:/etc/wolf
      - /var/run/docker.sock:/var/run/docker.sock:rw
      - /dev/:/dev/:rw
      - /run/udev:/run/udev:rw
    devices:
      - /dev/dri
      - /dev/uinput
      - /dev/uhid
    network_mode: host
```

### Dolphin

[Dolphin](https://github.com/dolphin-emu/dolphin) - эмулятор GameCube и Wii собранный в [Docker образе](https://github.com/linuxserver/docker-dolphin) для запуска в браузере на базе [Selkies](https://github.com/selkies-project/selkies).

```yaml
services:
  dolphin:
    image: lscr.io/linuxserver/dolphin:latest
    container_name: dolphin
    restart: unless-stopped
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/GMT+3
    volumes:
      - ./dolphin_config:/config
      - ./dolphin_games:/games
    ports:
      - 3001:3001
      - 3002:3000
    shm_size: 1gb
```

### DuckStation 

[DuckStation](https://github.com/stenzek/duckstation) - эмулятор PlayStation 1, собранный в [Docker образе](https://docs.linuxserver.io/images/docker-duckstation/).

```yaml
services:
  duckstation:
    image: lscr.io/linuxserver/duckstation:latest
    container_name: duckstation
    restart: unless-stopped
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC+3
    volumes:
      - ./duckstation_config:/config
      - ./duckstation_games:/games
    ports:
      - 3001:3001
      - 3002:3000
    shm_size: 1gb
```

### RetroAssembly

[RetroAssembly](https://github.com/arianrhodsandlot/retroassembly) - библиотека ретро-игры в браузере, с поддержку виртуального контроллера.

```yaml
services:
  retroassembly:
    image: arianrhodsandlot/retroassembly
    container_name: retroassembly
    restart: unless-stopped
    volumes:
      - ./game_data:/app/data # ROMs and save states
    ports:
      - 8000:8000
```

### Emulator.js

[Emulator.js](https://github.com/EmulatorJS/EmulatorJS) - веб-интерфейс для [RetroArch](https://github.com/libretro/RetroArch).

🔗 [Emulator.js Playground](https://demo.emulatorjs.org) ↗

```yaml
services:
  emulator.js:
    image: lscr.io/linuxserver/emulatorjs:latest
    container_name: emulator.js
    restart: unless-stopped
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC+3
      - SUBFOLDER=/
    volumes:
      - ./emulatorjs_config:/config
      - ./roms:/data
    ports:
      - 80:80
      - 3000:3000
      # - 4001:4001
```

### Junie

[Junie](https://github.com/Namaneo/Junie) - интерфейс Libretro, работающий в браузере.

🔗 [Junie Playground](https://namaneo.github.io/Junie) ↗

```yaml
services:
  junie:
    image: namaneo/junie
    container_name: junie
    restart: unless-stopped
    volumes:
      - ./games:/junie/games
    ports:
      - 8008:8000
```

### Quizzle

[Quizzle](https://github.com/gnmyt/Quizzle) - платформа проведения викторин для школ от создателя [Nexterm](https://github.com/gnmyt/Nexterm) и [MySpeed](https://github.com/gnmyt/MySpeed).

```yaml
services:
  quizzle:
    image: germannewsmaker/quizzle:latest
    container_name: quizzle
    restart: unless-stopped
    environment:
      - TZ=Etc/UTC+3
    volumes:
      - ./quizzle_data:/quizzle/data
    ports:
      - 6412:6412
```