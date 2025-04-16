Заметки по работе с `REST API` через **PowerShell** и **curl**.

---

# REST API

`$url = "https://habr.com/ru/rss/users/Lifailon/publications/articles/?fl=ru"` RSS лента публикаций на Habr \
`Invoke-RestMethod $url` \
`$iwr = Invoke-WebRequest -Uri $url` \
`$iwr | Get-Member` \
`$iwr.Content` \
`$iwr.StatusCode -eq 200` \
`$iwr.Headers` \
`$iwr.ParsedHtml | Select lastModified` \
`$iwr.Links | fl title,innerText,href` \
`$iwr.Images.src`

### Methods

**GET** - Read \
**POST** - Create \
**PATCH** - Partial update/modify \
**PUT** - Update/replace \
**DELETE** - Remove

### Download Image
```PowerShell
function Download-Image {
param (
    [Parameter(Mandatory = $True)]$url
)
    $folder = $url -replace "http.+://" -replace "/","-" -replace "-$"
    $path = "$home\Pictures\$folder"
    if (Test-Path $path) {
        Remove-Item $path -Recurse -Force
        New-Item -ItemType Directory $path > $null
    } else {
        New-Item -ItemType Directory $path > $null
    }
    $irm = Invoke-WebRequest -Uri $url
    foreach ($img in $irm.Images.src) {
        $name = $img -replace ".+/"
        Start-Job {
            Invoke-WebRequest $using:img -OutFile "$using:path\$using:name"
        } > $null
    }
    while ($True){
        $status_job = (Get-Job).State[-1]
        if ($status_job -like "Completed"){
        Get-Job | Remove-Job -Force
        break
    }}
    $count_all = $irm.Images.src.Count
    $count_down = (Get-Item $path\*).count
    "Downloaded $count_down of $count_all files to $path"
}
```
`Download-Image -url https://losst.pro/`

### Token
```PowerShell
https://veeam-11:9419/swagger/ui/index.html
$Header = @{
    "x-api-version" = "1.0-rev2"
}
$Body = @{
    "grant_type" = "password"
    "username" = "$login"
    "password" = "$password"
}
$vpost = iwr "https://veeam-11:9419/api/oauth2/token" -Method POST -Headers $Header -Body $Body -SkipCertificateCheck
$vtoken = (($vpost.Content) -split '"')[3]
```
### GET
```PowerShell
$token = $vtoken | ConvertTo-SecureString -AsPlainText –Force
$vjob = iwr "https://veeam-11:9419/api/v1/jobs" -Method GET -Headers $Header -Authentication Bearer -Token $token -SkipCertificateCheck

$Header = @{
    "x-api-version" = "1.0-rev1"
    "Authorization" = "Bearer $vtoken"
}
$vjob = iwr "https://veeam-11:9419/api/v1/jobs" -Method GET -Headers $Header -SkipCertificateCheck
$vjob = $vjob.Content | ConvertFrom-Json

$vjob = Invoke-RestMethod "https://veeam-11:9419/api/v1/jobs" -Method GET -Headers $Header -SkipCertificateCheck
$vjob.data.virtualMachines.includes.inventoryObject
```
### Cookie

Получить hash торрент файла на сайте Кинозал
```PowerShell
function Get-KinozalTorrentHash {
    param (
        [Parameter(Mandatory = $True)][string]$id,
        [Parameter(Mandatory = $True)][string]$cookies
    )
    $url = "https://kinozal.tv/get_srv_details.php?id=$($id)&action=2"
    $cookies = "uid=...+"
    $headers = @{
        "User-Agent" = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36"
        "Cookie" = $cookies
    }
    $result = Invoke-RestMethod -Uri $url -Headers $headers -Method Get
    $result -match "Инфо хеш: (.+)</li><li>Размер" | Out-Null
    return $Matches[1]
}
```
`$id = 1656552` \
`$cookies = "uid=..."` получить cookie в браузере на вкладке сеть из загловка запросов после авторизации на сайте \
`Get-KinozalTorrentHash $id $cookies`

# Pode

[PowerShell Web framework](https://github.com/Badgerati/Pode) для создания `REST API`, Веб-сайтов, `TCP` и `SMTP` серверов.

```PowerShell
Start-PodeServer {
    Add-PodeEndpoint -Address localhost -Port "8080" -Protocol "HTTP"
    ### Get info endpoints
    Add-PodeRoute -Path "/" -Method "GET" -ScriptBlock {
        Write-PodeJsonResponse -Value @{
        "service"="/api/service";
        "process"="/api/process"
        }
    }
    ### GET
    Add-PodeRoute -Path "/api/service" -Method "GET" -ScriptBlock {
        Write-PodeJsonResponse -Value $(
            Get-Service | Select-Object Name,@{
                Name="Status"; Expression={[string]$_.Status}
            },@{
                Name="StartType"; Expression={[string]$_.StartType}
            } | ConvertTo-Json
        )
    }
    Add-PodeRoute -Path "/api/process" -Method "GET" -ScriptBlock {
        Write-PodeJsonResponse -Value $(
            Get-Process | Sort-Object -Descending CPU | Select-Object -First 15 ProcessName,
            @{Name="ProcessorTime"; Expression={$_.TotalProcessorTime -replace "\.\d+$"}},
            @{Name="Memory"; Expression={[string]([int]($_.WS / 1024kb))+"MB"}},
            @{Label="RunTime"; Expression={((Get-Date) - $_.StartTime) -replace "\.\d+$"}}
        )
    }
    Add-PodeRoute -Path "/api/process-html" -Method "GET" -ScriptBlock {
        Write-PodeHtmlResponse -Value (
            Get-Process | Sort-Object -Descending CPU | Select-Object -First 15 ProcessName,
            @{Name="ProcessorTime"; Expression={$_.TotalProcessorTime -replace "\.\d+$"}},
            @{Name="Memory"; Expression={[string]([int]($_.WS / 1024kb))+"MB"}},
            @{Label="RunTime"; Expression={((Get-Date) - $_.StartTime) -replace "\.\d+$"}} # Auto ConvertTo-Html
        )
    }
    ### POST
    Add-PodeRoute -Path "/api/service" -Method "POST" -ScriptBlock {
        # https://pode.readthedocs.io/en/latest/Tutorials/WebEvent/
        # $WebEvent | Out-Default
        $Value = $WebEvent.Data["ServiceName"]
        $Status = (Get-Service -Name $Value).Status
        Write-PodeJsonResponse -Value @{
            "Name"="$Value";
            "Status"="$Status";
        }
    }
}
```
`irm http://localhost:8080/api/service -Method Get` \
`irm http://localhost:8080/api/process -Method Get` \
`http://localhost:8080/api/process-html` использовать браузер \
`irm http://localhost:8080/api/service -Method Post -Body @{"ServiceName" = "AnyDesk"}`

# Selenium

`Invoke-Expression(New-Object Net.WebClient).DownloadString("https://raw.githubusercontent.com/Lifailon/Deploy-Selenium/rsa/Deploy-Selenium-Drivers.ps1")` установка всех драйверов и Chromium соответствующий версии для драйвера
```powershell
$path = "$home\Documents\Selenium\"
$log = "$path\ChromeDriver.log"
$ChromeDriver = "$path\ChromeDriver.exe"
$WebDriver = "$path\WebDriver.dll"
$SupportDriver = "$path\WebDriver.Support.dll"
$Chromium = (Get-ChildItem $path -Recurse | Where-Object Name -like chrome.exe).FullName
Add-Type -Path $WebDriver
Add-Type -Path $SupportDriver
try {
    $ChromeOptions = New-Object OpenQA.Selenium.Chrome.ChromeOptions # создаем объект с настройками запуска браузера
    $ChromeOptions.BinaryLocation = $Chromium # передаем путь до исполняемого файла, который отвечает за запуск браузера
    $ChromeOptions.AddArgument("start-maximized") # добавляем аргумент, который позволяет запустить браузер на весь экран
    #$ChromeOptions.AddArgument("start-minimized") # запускаем браузер в окне
    #$ChromeOptions.AddArgument("window-size=400,800") # запускаем браузер с заданными размерам окна в пикселях
    $ChromeOptions.AcceptInsecureCertificates = $True # игнорировать предупреждение на сайтах с не валидным сертификатом
    #$ChromeOptions.AddArgument("headless") # скрывать окно браузера при запуске
    $ChromeDriverService = [OpenQA.Selenium.Chrome.ChromeDriverService]::CreateDefaultService($ChromeDriver) # создаем объект настроек службы драйвера
    $ChromeDriverService.HideCommandPromptWindow = $True # отключаем весь вывод логирования драйвера в консоль (этот вывод нельзя перенаправить)
    $ChromeDriverService.LogPath = $log # указать путь до файла с журналом
    $ChromeDriverService.EnableAppendLog = $True # не перезаписывать журнал при каждом новом запуске
    #$ChromeDriverService.EnableVerboseLogging = $True # кроме INFO и ошибок, записывать DEBUG сообщения
    $Selenium = New-Object OpenQA.Selenium.Chrome.ChromeDriver($ChromeDriverService, $ChromeOptions) # инициализируем запуск с указанными настройками

    $Selenium.Navigate().GoToUrl("https://google.com") # переходим по указанной ссылке в браузере
    #$Selenium.Manage().Window.Minimize() # свернуть окно браузера после запуска и перехода по нужному url (что бы считать страницу корректно)
    # Ищем поле для ввода текста:
    $Search = $Selenium.FindElements([OpenQA.Selenium.By]::Id('APjFqb'))
    $Search = $Selenium.FindElements([OpenQA.Selenium.By]::XPath('//*[@id="APjFqb"]'))
    $Search = $Selenium.FindElements([OpenQA.Selenium.By]::Name('q'))
    $Search = $Selenium.FindElements([OpenQA.Selenium.By]::XPath('//*[@name="q"]'))
    $Search = $Selenium.FindElements([OpenQA.Selenium.By]::ClassName('gLFyf'))
    $Search = $Selenium.FindElements([OpenQA.Selenium.By]::CssSelector('[jsname="yZiJbe"]'))
    $Search = $Selenium.FindElements([OpenQA.Selenium.By]::TagName('textarea')) | Where-Object ComputedAccessibleRole -eq combobox
    $Search.SendKeys("calculator online") # передаем текст выбранному элементу
    $Search.SendKeys([OpenQA.Selenium.Keys]::Enter) # нажимаем Enter для вызова функции поиска

    Start-Sleep 1
    $div = $Selenium.FindElements([OpenQA.Selenium.By]::TagName("div"))
    $2 = $div | Where-Object {($_.ComputedAccessibleRole -eq "button") -and ($_.ComputedAccessibleLabel -eq "2")}
    $2.Click()
    $2.Click()
    $plus = $div | Where-Object {($_.ComputedAccessibleRole -eq "button") -and ($_.Text -eq "+")}
    $plus.Click()
    $3 = $Selenium.FindElement([OpenQA.Selenium.By]::CssSelector('[jsname="KN1kY"]'))
    $3.Click()
    $3.Click()
    $sum = $Selenium.FindElement([OpenQA.Selenium.By]::CssSelector('[jsname="Pt8tGc"]'))
    $sum.Click()
    $result = $Selenium.FindElement([OpenQA.Selenium.By]::CssSelector('[jsname="VssY5c"]')).Text
    Write-Host "Result: $result" -ForegroundColor Green
}
finally {
    $Selenium.Close()
    $Selenium.Quit()
}
```
### Selenium modules
```PowerShell
Invoke-RestMethod https://raw.githubusercontent.com/Lifailon/Selenium-Modules/rsa/Modules/Get-GPT/Get-GPT.psm1 | Out-File -FilePath "$(New-Item -Path "$($($Env:PSModulePath -split ";")[0])\Get-GPT" -ItemType Directory -Force)\Get-GPT.psm1" -Force
```
`Get-GPT "Исполняй роль калькулятора. Посчитай сумму чисел: 22+33"`
```PowerShell
Invoke-RestMethod https://raw.githubusercontent.com/Lifailon/Selenium-Modules/rsa/Modules/Get-Translation/Get-Translation.psm1 | Out-File -FilePath "$(New-Item -Path "$($($Env:PSModulePath -split ";")[0])\Get-Translation" -ItemType Directory -Force)\Get-Translation.psm1" -Force
```
`Get-Translation -Provider DeepL -Text "I translating the text"` \
`Get-Translation -Provider DeepL -Text "Я перевожу текст"` \
`Get-Translation -Provider Google -Text "I translating the text"` \
`Get-Translation -Provider Google -Text "Я перевожу текст" -Language en`
```PowerShell
Invoke-RestMethod https://raw.githubusercontent.com/Lifailon/Selenium-Modules/rsa/Modules/Get-SpeedTest/Get-SpeedTest.psm1 | Out-File -FilePath "$(New-Item -Path "$($($Env:PSModulePath -split ";")[0])\Get-SpeedTest" -ItemType Directory -Force)\Get-SpeedTest.psm1" -Force
```
`Get-SpeedTest -Provider Libre` \
`Get-SpeedTest -Provider Open` \
`Get-SpeedTest -Provider Ookla`

# IE

`$ie.document.IHTMLDocument3_getElementsByTagName("input")  | select name` получить имена всех Input Box \
`$ie.document.IHTMLDocument3_getElementsByTagName("button") | select innerText` получить имена всех Button \
`$ie.Document.documentElement.innerHTML` прочитать сырой Web Content (<input name="login" tabindex="100" class="input__control input__input" id="uniq32005644019429136" spellcheck="false" placeholder="Логин") \
`$All_Elements = $ie.document.IHTMLDocument3_getElementsByTagName("*")` забрать все элементы \
`$Go_Button = $All_Elements | ? innerText -like "go"` поиск элемента по имени \
`$Go_Button | select ie9_tagName` получить TagName (SPAN) для быстрого дальнейшего поиска \
`$SPAN_Elements = $ie.document.IHTMLDocument3_getElementsByTagName("SPAN")`
```PowerShell
$ie = New-Object -ComObject InternetExplorer.Application
$ie.navigate("https://yandex.ru")
$ie.visible = $true
$ie.document.IHTMLDocument3_getElementByID("login").value = "Login"
$ie.document.IHTMLDocument3_getElementByID("passwd").value = "Password"
$Button_Auth = ($ie.document.IHTMLDocument3_getElementsByTagName("button")) | ? innerText -match "Войти"
$Button_Auth.Click()
$Result = $ie.Document.documentElement.innerHTML
$ie.Quit()
```

# LLM

## OpenAI

Пример запроса для перевода текста
```PowerShell
$text = "The OpenAI API uses API keys for authentication. You can create API keys at a user or service account level." # https://platform.openai.com/docs/api-reference/authentication
$toLang = "Russian"
$presetPrompt = "Translate the following text into $toLang :
$text
Respond ONLY with the translated text. Do not include any other explanations, context, or comments.
"
$apiKey = "sk-proj-XXXXXXXXXX"
$apiUrl = "https://api.openai.com/v1/chat/completions"

$body = @{
    model = "gpt-4o-mini"
    messages = @(@{
        role = "user";
        content = $presetPrompt
    })
    temperature = 0.7
} | ConvertTo-Json -Depth 10 -Compress

$response = Invoke-RestMethod -Uri $apiUrl -Method Post -Headers @{
    "Content-Type" = "application/json"
    "Authorization" = "Bearer $apiKey"
} -Body $body

$response.choices.message.content
```
## Mock

Создаем серверную заглушку для `API` OpenAI через [JSON Server](https://github.com/typicode/json-server)

`npm install -g json-server@0.17.4`

Конфигурация ответов в файле `openai.json`
```json
{
  "completions": {
    "model": "gpt-4o-mini-2024-07-18",
    "choices": [
      {
        "message": {
          "role": "assistant",
          "content": "Response from JSON Server",
        },
        "finish_reason": "stop"
      }
    ]
  }
}
```
Настройка маршрутизации в файле `routes.json`
```json
{
    "/v1/chat/completions": "/completions"
}
```
Конфигурация сервера в файле `json-server.json`
```json
{
    "port": 3001
}
```
Запускаем сервер:

`json-server --watch openai.json --routes routes.json`

Делаем запрос:

`$(Invoke-RestMethod -Uri "http://localhost:3001/v1/chat/completions").choices.message.content`

## OpenRouter

Регестрируем аккаунт на [OpenRouter](https://openrouter.ai) через Google, выпускаем [api ключ](https://openrouter.ai/settings/keys) и выбираем [бесплатную модель](https://openrouter.ai/models?max_price=0).
```PowerShell
$OPENROUTER_API_KEY = "sk-or-v1-KEY"
$OPENROUTER_MODEL = "deepseek/deepseek-r1:free"
$headers = @{
    "Content-Type"  = "application/json"
    "Authorization" = "Bearer $OPENROUTER_API_KEY"
}
$body = @{
    "model" = $OPENROUTER_MODEL
    "messages" = @(
        @{
            "role"    = "system"
            "content" = "Your role is a translator. You only translate the text into Russian and do not analyze the answer."
        },
        @{
            "role"    = "user"
            "content" = "Hello! I translate the text into English!"
        }
    )
} | ConvertTo-Json -Depth 10 -Compress
$response = Invoke-RestMethod -Uri "https://openrouter.ai/api/v1/chat/completions" -Method Post -Headers $headers -Body $body
$response.choices.message.content
```
## LM Studio

`API` в [LM Studio](https://lmstudio.ai) совместим с OpenAI

Получить список моделей:
```PowerShell
$(Invoke-RestMethod -Uri "http://127.0.0.1:1234/v1/models/").data.id
deepseek-r1-distill-llama-8b
llama-3.2-3b-instruct
text-embedding-nomic-embed-text-v1.5
```
Режим чата (когда `stream` установлен в `True`, ответ приходит по частям):
```bash
curl http://127.0.0.1:1234/v1/chat/completions `
    -H "Content-Type: application/json" `
    -d '{
    "model": "deepseek-r1-distill-llama-8b",
    "messages": [ 
        { "role": "system", "content": "Только переводишь текст на англйский язык, не анализируешь ответ и не пишешь ничего дополнительного." },
        { "role": "user", "content": "Привет! Я перевожу текст на англйский язык!" }
    ], 
    "temperature": 0.7, 
    "max_tokens": -1,
    "stream": false
}'
```
## Ollama
```PowerShell
cd $home\Downloads
$versionLatest = $(Invoke-RestMethod "https://api.github.com/repos/ollama/ollama/releases/latest").tag_name
irm https://github.com/ollama/ollama/releases/download/${versionLatest}/ollama-windows-amd64.zip -OutFile ollama.zip
Expand-Archive -Path ollama.zip -OutputPath ".\ollama" # -DestinationPath for Windows PowerShell 5.1
Remove-Item ollama.zip; cd ollama
```
`.\ollama serve` запускаем сервер \
`.\ollama pull mistral:7b-instruct` загружаем модель (https://ollama.com/library/mistral) \
`.\ollama run mistral` запустить консоль для общения с LLM в режиме чата
```PowerShell
# Отправляем API запрос
$data = curl -sS -X POST http://localhost:11434/api/generate -d '{
  "model": "mistral",
  "prompt":"Return only the word test in the answer"
}'
# Собираем ответ из частей response
[string]$($data | ConvertFrom-Json).response.trim()
```
## GigaChat

### Windows

[Developers chat](https://developers.sber.ru/gigachat/login)

- Установка сертификатов:

`Invoke-WebRequest "https://gu-st.ru/content/lending/russian_trusted_root_ca_pem.crt" -OutFile "$home\Downloads\russian_trusted_root_ca.cer"` скачать сертификат минцифры \
`Invoke-WebRequest "https://gu-st.ru/content/lending/russian_trusted_sub_ca_pem.crt" -OutFile "$home\Downloads\russian_trusted_sub_ca.cer"` \
`Import-Certificate -FilePath "$home\Downloads\russian_trusted_root_ca.cer" -CertStoreLocation "Cert:\CurrentUser\Root"` установить сертификат минцифры \
`Import-Certificate -FilePath "$home\Downloads\russian_trusted_sub_ca.cer" -CertStoreLocation "Cert:\CurrentUser\CA"`

- 2. Авторизация по Sber ID и генерация новых авторизационных данных для получения токена: [Developers](https://developers.sber.ru/studio) (время жизни 30 минут)

- 3. Формирование авторизационных данных в формате Base64 из Client ID и Client Secret:
```PowerShell
$Client_ID     = "7e6d2f9f-825e-49b7-98f4-62fbb7506427" # [System.Guid]::Parse("7e6d2f9f-825e-49b7-98f4-62fbb7506427")
$Client_Secret = "c35113ee-6757-47ba-9853-ea1d0d9db1ef" # [System.Guid]::Parse("c35113ee-6757-47ba-9853-ea1d0d9db1ef")
$Client_Join   = $Client_ID+":"+$Client_Secret # объединяем два UUID в одну строку, разделяя их символом ':'
$Bytes         = [System.Text.Encoding]::UTF8.GetBytes($Client_Join) # преобразуем строку в массив байт
$Cred_Base64   = [Convert]::ToBase64String($Bytes) # кодируем байты в строку Base64
```
- 4. Получение токена:

`$Cred_Base64   = "N2U2ZDJmOWYtODI1ZS00OWI3LTk4ZjQtNjJmYmI3NTA2NDI3OmIyYzgwZmZmLTEzOGUtNDg1Mi05MjgwLWE2MGI4NTc0YTM2MQ=="` \
`$UUID = [System.Guid]::NewGuid()` генерируем UUID для журналирования входящих вызовов и разбора инцидентов
```PowerShell
$url = "https://ngw.devices.sberbank.ru:9443/api/v2/oauth"
$headers = @{
    "Authorization" = "Basic $Cred_Base64"
    "RqUID" = "$UUID"
    "Content-Type" = "application/x-www-form-urlencoded"
}
$body = @{
    scope = "GIGACHAT_API_PERS"
}
$GIGA_TOKEN = $(Invoke-RestMethod -Uri $url -Method POST -Headers $headers -Body $body).access_token
```
- 5. Параметры:
```PowerShell
[string]$content = "Посчитай сумму чисел: 22+33"
[string]$role = "user" # роль автора сообщения (user/assistant/system)
[float]$temperature = 0.7 # температура выборки в диапазоне от 0 до 2. Чем выше значение, тем более случайным будет ответ модели.
[float]$top_p = 0.1 # используется как альтернатива temperature и изменяется в диапазоне от 0 до 1. Задает вероятностную массу токенов, которые должна учитывать модель. Так, если передать значение 0.1, модель будет учитывать только токены, чья вероятностная масса входит в верхние 10%.
[int64]$n = 1 # количество вариантов ответов (1..4), которые нужно сгенерировать для каждого входного сообщения
[int64]$max_tokens = 512 # максимальное количество токенов, которые будут использованы для создания ответов
[boolean]$stream = $false # передавать сообщения по частям в потоке
```
- 6. Составление запросов:
```PowerShell
$url = "https://gigachat.devices.sberbank.ru/api/v1/chat/completions"
$headers = @{
    "Authorization" = "Bearer $GIGA_TOKEN"
    "Content-Type" = "application/json"
}

$(Invoke-RestMethod -Uri "https://gigachat.devices.sberbank.ru/api/v1/models" -Headers $headers).data # список доступных моделей

$body = @{
    model = "GigaChat:latest"
    messages = @(
        @{
            role = $role
            content = $content
        }
    )
    temperature = $temperature
	n = $n
	max_tokens = $max_tokens
	stream = $stream
} | ConvertTo-Json
$Request = Invoke-RestMethod -Method POST -Uri $url -Headers $headers -Body $body
$Request.choices.message.content
```
### Linux

- Установка сертификатов в Ubuntu:

`wget https://gu-st.ru/content/lending/russian_trusted_root_ca_pem.crt` \
`wget https://gu-st.ru/content/lending/russian_trusted_sub_ca_pem.crt` \
`mkdir /usr/local/share/ca-certificates/russian_trusted` \
`cp russian_trusted_root_ca_pem.crt russian_trusted_sub_ca_pem.crt /usr/local/share/ca-certificates/russian_trusted` \
`update-ca-certificates -v` \
`wget -qS --spider --max-redirect=0 https://www.sberbank.ru`

- Получение токена:
```Bash
Cred_Base64="N2U2ZDJmOWYtODI1ZS00OWI3LTk4ZjQtNjJmYmI3NTA2NDI3OmIyYzgwZmZmLTEzOGUtNDg1Mi05MjgwLWE2MGI4NTc0YTM2MQ=="
UUID=$(uuidgen)
GIGA_TOKEN=$(curl -s --location --request POST "https://ngw.devices.sberbank.ru:9443/api/v2/oauth" \
--header "Authorization: Basic $Cred_Base64" \
--header "RqUID: $UUID" \
--header "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode 'scope=GIGACHAT_API_PERS' | jq -r .access_token)
```
`curl -s --location "https://gigachat.devices.sberbank.ru/api/v1/models" --header "Authorization: Bearer $GIGA_TOKEN" | jq .` для проверки

- Составление запроса:
```Bash
request=$(curl -s https://gigachat.devices.sberbank.ru/api/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $GIGA_TOKEN" \
  -d '{
  "model": "GigaChat:latest",
  "messages": [
        {
            "role": "user",
            "content": "Когда уже ИИ захватит этот мир?"
        }
    ],
  "temperature": 0.7
}')
echo $request | jq -r .choices[].message.content
```
## YandexGPT

- Получить OAuth-Token:

[Create AIM Token](https://cloud.yandex.ru/ru/docs/iam/operations/iam-token/create) время жизни IAM-токена не больше 12 часов \
`yandexPassportOauthToken="y0_AgAAAAAGaLFLAATuwQAAAAD3xtRLQE4hvlazQ5euKO43XXXXXXXXXXX"` для bash \
`$yandexPassportOauthToken = "y0_AgAAAAAGaLFLAATuwQAAAAD3xtRLQE4hvlazQ5euKO43XXXXXXXXXXX"` для PowerShell

- Обменять OAuth-Token на IAM-Token:

`IAM_TOKEN=$(curl -s -d "{\"yandexPassportOauthToken\":\"$yandexPassportOauthToken\"}" "https://iam.api.cloud.yandex.net/iam/v1/tokens" | jq -r .iamToken)` \
`$IAM_TOKEN = $(Invoke-RestMethod -Method POST -Uri "https://iam.api.cloud.yandex.net/iam/v1/tokens" -Body $(@{yandexPassportOauthToken = "$yandexPassportOauthToken"} | ConvertTo-Json -Compress)).iamToken`

- Получить FOLDER_ID:
```Bash
CLOUD_ID=$(curl -s -H "Authorization: Bearer $IAM_TOKEN" https://resource-manager.api.cloud.yandex.net/resource-manager/v1/clouds | jq -r .clouds[].id) # получить cloud id
curl -s --request GET -H "Authorization: Bearer $IAM_TOKEN" https://resource-manager.api.cloud.yandex.net/resource-manager/v1/folders -d "{\"cloudId\": \"$CLOUD_ID\"}" # получить список директорий в облаке
curl -s --request POST -H "Authorization: Bearer $IAM_TOKEN" https://resource-manager.api.cloud.yandex.net/resource-manager/v1/folders -d "{\"cloudId\": \"$CLOUD_ID\", \"name\": \"test\"}" # создать директорию в облаке
FOLDER_ID=$(curl -s --request GET -H "Authorization: Bearer $IAM_TOKEN" https://resource-manager.api.cloud.yandex.net/resource-manager/v1/folders -d '{"cloudId": "b1gf9n6heihqj0pt5piu"}' | jq -r '.folders[] | select(.name == "test") | .id') # забрать id директории
```
```PowerShell
$CLOUD_ID = $(Invoke-RestMethod -Method Get -Uri "https://resource-manager.api.cloud.yandex.net/resource-manager/v1/clouds" -Headers @{"Authorization"="Bearer $IAM_TOKEN"; "Content-Type"="application/json"}).clouds.id
$FOLDER_ID = $(Invoke-RestMethod -Method Get -Uri "https://resource-manager.api.cloud.yandex.net/resource-manager/v1/folders" -Headers @{"Authorization"="Bearer $IAM_TOKEN"; "Content-Type"="application/json"} -Body (@{"cloudId"= $CLOUD_ID} | ConvertTo-Json)).folders | Where-Object name -eq test | Select-Object -ExpandProperty id
```
- Составление запроса:
```Bash
model="gpt://$FOLDER_ID/yandexgpt/latest" # https://cloud.yandex.ru/ru/docs/yandexgpt/concepts/models
body=$(cat <<EOF
{
  "modelUri": "$model",
  "completionOptions": {
    "stream": false,
    "temperature": 0.6,
    "maxTokens": 2000
  },
  "messages": [
    {
      "role": "user",
      "text": "Посчитай сумму 22+33"
    }
  ]
}
EOF)
curl --request POST \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $IAM_TOKEN" \
  -H "x-folder-id: $FOLDER_ID" \
  -d "$body" \
  "https://llm.api.cloud.yandex.net/foundationModels/v1/completion"
```
```PowerShell
$model = "gpt://$FOLDER_ID/yandexgpt/latest"
$body = @"
{
  "modelUri": "$model",
  "completionOptions": {
    "stream": false,
    "temperature": 0.6,
    "maxTokens": 2000
  },
  "messages": [
    {
      "role": "user",
      "text": "Посчитай сумму 22+33"
    }
  ]
}
"@
Invoke-RestMethod -Method POST -Uri "https://llm.api.cloud.yandex.net/foundationModels/v1/completion" -Headers @{"Content-Type"="application/json"; "Authorization"="Bearer $IAM_TOKEN"; "x-folder-id"="$FOLDER_ID"} -Body $body
```
## SuperAGI

[Source](https://github.com/TransformerOptimus/SuperAGI) \
[Playground generate](https://models.superagi.com/playground/generate) \
[API Doc (exaples)](https://documenter.getpostman.com/view/30119783/2s9YR3cFJG)
```Bash
SUPERAGI_API_KEY="31f72164129XXXXX"
prompt="посчитай сумму 22+33, дай только ответ без лишнего текста"
request=$(curl -s -X POST 'https://api.superagi.com/v1/generate/65437cbf227a4018516ad1ce' \
-H 'Content-Type: application/json' \
-H "Authorization: Bearer $SUPERAGI_API_KEY" \
-d '{
  "prompt": ["$prompt"],
  "max_tokens": 500,
  "temperature": 0.9,
  "top_p": 0.15,
  "repetition_penalty": 0,
  "best_of": 1.05,
  "top_k": 50,
  "stream": false
}')
echo $request | sed "s/data: //" | jq -r .choices[].text
```
```PowerShell
$SUPERAGI_API_KEY = "31f72164129XXXXX"
$prompt = "посчитай сумму 22+33, дай только ответ без лишнего текста"
$request = Invoke-RestMethod -Method Post -Uri 'https://api.superagi.com/v1/generate/65437cbf227a4018516ad1ce' -Headers @{
    'Content-Type' = 'application/json'
    'Authorization' = "Bearer $SUPERAGI_API_KEY"
} -Body (@{
    prompt = @($prompt)
    max_tokens = 500
    temperature = 0.9
    top_p = 0.15
    repetition_penalty = 0
    best_of = 1.05
    top_k = 50
    stream = $false
} | ConvertTo-Json)
$($request -replace "^data: " | ConvertFrom-Json).choices.text
```
## Replicate

[API curl examples](https://replicate.com/stability-ai/stable-diffusion/examples?input=http)
```Bash
REPLICATE_API_TOKEN="r8_STyeUNXiGonkLfxE1FSKaqll26lXXXXXXXXXX"
prompt="Жираф в полоску зебры"
request=$(curl -s -X POST \
  -H "Authorization: Token $REPLICATE_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d $'{
    "version": "ac732df83cea7fff18b8472768c88ad041fa750ff7682a21affe81863cbe77e4",
    "input": {
      "prompt": "$prompt"
    }
  }' \
  https://api.replicate.com/v1/predictions)
request_url=$(echo $request | jq -r .urls.get)
response_status=$(curl -s -H "Authorization: Token $REPLICATE_API_TOKEN" $request_url | jq -r .status)
while [[ $response_status != succeeded ]]; do
    response_status=$(curl -s -H "Authorization: Token $REPLICATE_API_TOKEN" $request_url | jq -r .status)
done
curl -s -H "Authorization: Token $REPLICATE_API_TOKEN" $request_url | jq -r .output[]
```
```PowerShell
$REPLICATE_API_TOKEN = "r8_STyeUNXiGonkLfxE1FSKaqll26lXXXXXXXXXX"
$prompt = "Жираф в полоску зебры"
$body = @{
   version = "ac732df83cea7fff18b8472768c88ad041fa750ff7682a21affe81863cbe77e4"
   input = @{
       prompt = $prompt
   }
} | ConvertTo-Json
$headers = @{
   "Authorization" = "Token $REPLICATE_API_TOKEN"
   "Content-Type" = "application/json"
}
$request = Invoke-RestMethod -Uri "https://api.replicate.com/v1/predictions" -Method POST -Body $body -Headers $headers
$response = Invoke-RestMethod $($request.urls.get) -Headers @{"Authorization" = "Token $REPLICATE_API_TOKEN"}
while ($response.status -ne "succeeded") {
    $response = Invoke-RestMethod $($request.urls.get) -Headers @{"Authorization" = "Token $REPLICATE_API_TOKEN"}
}
$response.output
```

# Google API

## Google Translate
```PowerShell
$Key = "<TOKEN_API>" # получить токен: https://console.cloud.google.com/apis/credentials
$Text = "You can see in the right corner how long each translation request takes (this does not depend on the amount of text being transferred)."
$LanguageTarget = "RU"
$LanguageSource = "EN"
$url = "https://translation.googleapis.com/language/translate/v2?key=$key"
$Header = @{
    "Content-Type" = "application/json"
}
$Body = @{
    "q" = "$Text"
    "target" = "$LanguageTarget"
    "source" = "$LanguageSource"
} | ConvertTo-Json
$WebClient = New-Object System.Net.WebClient
foreach ($key in $Header.Keys) {
    $WebClient.Headers.Add($key, $Header[$key])
}
$Response = $WebClient.UploadString($url, "POST", $Body) | ConvertFrom-Json
$Response.data.translations.translatedText
```
## Google Search
```PowerShell
$Key = "<TOKEN_API>" # получить токен: https://developers.google.com/custom-search/v1/overview?hl=ru (пользовательский поиск JSON API предоставляет 100 поисковых запросов в день бесплатно)
$cx = "35c78340f49eb474a" # создать поисковую систему https://programmablesearchengine.google.com/controlpanel/all
$Query = "как создать бота discord"
$Lang = "ru"
$Num = 10
$Start = 0
$response = Invoke-RestMethod "https://www.googleapis.com/customsearch/v1?q=$Query&key=$Key&cx=$cx&lr=lang_$Lang&num=$Num&$start=$Start"
$response.items | Select-Object title,snippet,displayLink,link | Format-List
```

## Google Search via RapidAPI

[Google-Search72](https://rapidapi.com/ru/neoscrap-net/api/google-search72)
```PowerShell
$Key = "<TOKEN_API>"
$headers=@{}
$headers.Add("X-RapidAPI-Key", "$Key")
$headers.Add("X-RapidAPI-Host", "google-search72.p.rapidapi.com")
$query = "как создать бота discord"
$response = Invoke-RestMethod "https://google-search72.p.rapidapi.com/search?q=$query%20gitgub&gl=us&lr=lang_ru&num=20&start=0" -Method GET -Headers $headers
$response.items | Select-Object title,snippet,displayLink,link | Format-List
```

## Google Filter

`https://www.google.com/search?q=the+rookie+2018+imdb` формат url-запроса поиска с пробелами \
`https://www.google.com/search?q=the+rookie+2018+site:imdb.com` поиск по сайту \
`https://www.google.com/search?q=the+rookie+intitle:index.of+"last modified"+(mkv|avi)` искать страницы, на которых указано "last modified" (последние изменения), заголовок страницы через расширенный оператор поиска (все перечисленные слова должны встречаться в заголовке) содержит слово "index.of" (указывает на директорию на веб-сервере, которая содержит список файлов) и искать файлы с расширениями .mkv или (|) .avi \
`https://www.google.com/search?q=the+rookie+2018+filetype:torrent` \
`инструкция gopro hero 11 filetype:pdf` искать сразу документ (на странице .pdf или загрузка) \
`"действия/глаголы, утвержденные для использования в командлетах"` искать по фразе целиком, без разбиения на отдельные слова \
`"ягуар скорость -животное -xe -xj"` узнаем скорость Ягуара, исключаем животное и модели автомобиля \
`"intitle:лучшие фильмы 2023"` запрос ищет страницы, заголовки (title HTML документа) которых содержат слова "лучшие", "фильмы" и "2023" (все слова должны быть в заголовке) \
`"allintitle:лучшие фильмы 2023"` запрос ищет страницы, заголовки (title HTML документа) которых содержат слова "лучшие", "фильмы" или "2023" (одно из) \
`"intext:telegram бот powershell"` поиск страниц, содержащих указанное ключевое слово в тексте страницы (а не только в заголовке) \
`"inurl:lifailon"` поиск страниц, в URL которых содержится указанное ключевое слово \
`intitle:index.of "game of thrones" mkv daterange:2010..2015` фильтрация по дате изменения, оператор позволяет задать диапазон дат в формате YYYYMMDD..YYYYMMDD \
`intitle:index.of "game of thrones" mkv after:2015` ограничить результаты поиска файлов, измененных до (before) или после (after) указанной даты \
`intitle:index.of "game of thrones" mkv from:2010 to:2015` фильтрация по диапазону дат \
`https://www.google.com/search?q=the-rookie-2018+site:imdb.com&btnI` редирект на первый url

# Media API

## IMDb

[IMDb8](https://rapidapi.com/apidojo/api/imdb8)
```PowerShell
$key = "<TOKEN_API>" # 500 запросов в месяц
$query="Break"
$headers=@{}
$headers.Add("X-RapidAPI-Key", "$key")
$headers.Add("X-RapidAPI-Host", "imdb8.p.rapidapi.com")
$response = Invoke-RestMethod "https://imdb8.p.rapidapi.com/title/find?q=$query" -Method GET -Headers $headers
$response.results | select title,titletype,year,runningTimeInMinutes,id | Format-Table
"https://www.imdb.com$($response.results.id[0])"
$response.results.principals # актеры
$response.results.image
```
## MoviesDatabase

[MoviesDatabase](https://rapidapi.com/SAdrian/api/moviesdatabase)
```PowerShell
$key = "<TOKEN_API>"
$imdb_id = "tt0455275"
$headers=@{}
$headers.Add("X-RapidAPI-Key", "$key")
$headers.Add("X-RapidAPI-Host", "moviesdatabase.p.rapidapi.com")
$response = Invoke-RestMethod "https://moviesdatabase.p.rapidapi.com/titles/$imdb_id" -Method GET -Headers $headers
$response.results
```
## TMDB

[Developer TMDB](https://developer.themoviedb.org/reference/intro/getting-started)
```PowerShell
$TOKEN = "548e444e7812575caa0a7eXXXXXXXXXX"
$Endpoint = "search/tv" # поиск сериала (tv) и фильма (movie) по названию
$Query = "зимородок"
$url = $("https://api.themoviedb.org/3/$Endpoint"+"?api_key=$TOKEN&query=$Query")
$(Invoke-RestMethod -Uri $url -Method Get).results
$id = $(Invoke-RestMethod -Uri $url -Method Get).results.id # забрать id сериала (210865) https://www.themoviedb.org/tv/210865

$Endpoint = "tv/$id" # получение информации о сериале по его ID
$url = $("https://api.themoviedb.org/3/$Endpoint"+"?api_key=$TOKEN")
$(Invoke-RestMethod -Uri $url -Method Get) # список сезонов (.seasons), количество эпизодов (.seasons.episode_count)

(Invoke-RestMethod -Uri "https://api.themoviedb.org/3/tv/$id/season/2?api_key=$Token" -Method Get).episodes # вывести 2 сезон
Invoke-RestMethod -Uri "https://api.themoviedb.org/3/tv/$id/season/2/episode/8?api_key=$Token" -Method Get # вывести 8 эпизод
```
## OMDb

Получение API ключа по [email](https://www.omdbapi.com)

`$API_KEY = "XXXXXXXX"` \
`$IMDb_ID = "tt7587890"` \
`curl -s "https://omdbapi.com/?apikey=$($API_KEY)&i=$($IMDb_ID)" | jq .` \
`curl -s "https://omdbapi.com/?apikey=$($API_KEY)&i=$($IMDb_ID)" | ConvertFrom-Json` \
`Invoke-RestMethod "https://omdbapi.com/?apikey=$($API_KEY)&s=The Rookie"` \
`Invoke-RestMethod "https://omdbapi.com/?apikey=$($API_KEY)&t=The Rookie"` поиск по Title \
`Invoke-RestMethod "https://omdbapi.com/?apikey=$($API_KEY)&t=The Rookie&y=1990"` поиск по Title и году выхода \
`Invoke-RestMethod "https://omdbapi.com/?apikey=$($API_KEY)&t=The Rookie&type=movie"` поиск только фильма (movie) или сериала (series) \
`$(Invoke-RestMethod "https://omdbapi.com/?apikey=$($API_KEY)&s=The Rookie").Search` поиск всех совпадений (фильмы и сериалы)

## ivi

[ivi api doc](https://ask.ivi.ru/knowledge-bases/10/articles/51697-dokumentatsiya-dlya-api-ivi)

`Invoke-RestMethod https://api.ivi.ru/mobileapi/categories` список категорий и жанров (genres/meta_genres) \
`Invoke-RestMethod https://api.ivi.ru/mobileapi/collections` подборки

`(Invoke-RestMethod "https://api.ivi.ru/mobileapi/search/v7/?query=zimorodok").result.seasons.number` кол-во сезонов \
`(Invoke-RestMethod "https://api.ivi.ru/mobileapi/search/v7/?query=zimorodok").result.seasons[1].episode_count` кол-во серий во втором сезоне \
`(Invoke-RestMethod "https://api.ivi.ru/mobileapi/search/v7/?query=zimorodok").result.seasons[1].ivi_release_info.date_interval_min` дата выхода следующей серии \
`(Invoke-RestMethod "https://api.ivi.ru/mobileapi/search/v7/?query=zimorodok").result.kp_rating` рейтинг в Кинопоиск (8.04)

`$id = (Invoke-RestMethod "https://api.ivi.ru/mobileapi/search/v7/?query=zimorodok").result.kp_id` получить id в Кинопоиск (5106881) \
`id=$(curl -s https://api.ivi.ru/mobileapi/search/v7/?query=zimorodok | jq .result[].kp_id)` получить id в Кинопоиск

## Kinopoisk

```Bash
id=5106881
get=$(curl -s https://www.kinopoisk.ru/film/$id/episodes/)
printf "%s\n" "${get[@]}" | grep -A 1 "Сезон 2" | grep "эпизодов" | sed -r "s/^.+\: //" # количество эпиздовод во втором сезоне
```

### kinopoisk.dev

[Получить токен](https://t.me/kinopoiskdev_bot) \
[Документация по API в формате OpenAPI](https://kinopoisk.dev/documentation)

`GET /v1.4/movie/{id}` поиск по id
```PowerShell
$id = 5106881
$API_KEY = "ZYMNJJA-0J8MNPN-PB4N7R7-XXXXXXX"

$Header = @{
    "accept" = "application/json"
    "X-API-KEY" = "$API_KEY"
}
$irm = Invoke-RestMethod "https://api.kinopoisk.dev/v1.4/movie/$id" -Method GET -Headers $Header
$irm.rating.kp # рейтинг в Кинопоиск (8,079)
$irm.seasonsInfo # количество сезонов и эпизодов в них
```
```Bash
id=5106881
API_KEY="ZYMNJJA-0J8MNPN-PB4N7R7-XXXXXXX"
get=$(curl -s -X GET \
  "https://api.kinopoisk.dev/v1.4/movie/$id" \
  -H "accept: application/json" \
  -H "X-API-KEY: $API_KEY")
echo $get | jq .rating.kp # рейтинг в Кинопоиск (8,079)
echo $get | jq .seasonsInfo[1].episodesCount # количество эпизодов во втором [1] сезоне (6)
```
`GET /v1.4/movie/search`
```Bash
query="zimorodok"
page=1 # кол-во страниц для выборки
limit=1 # кол-во элементов на странице
curl -s -X GET \
  "https://api.kinopoisk.dev/v1.4/movie/search?page=$page&limit=$limit&query=$query" \
  -H "accept: application/json" \
  -H "X-API-KEY: $API_KEY" | jq .

limit=5
request=$(curl -s -X GET \
  "https://api.kinopoisk.dev/v1.4/movie/search?page=$page&limit=$limit&query=%D0%B7%D0%B8%D0%BC%D0%BE%D1%80%D0%BE%D0%B4%D0%BE%D0%BA" \
  -H "accept: application/json" \
  -H "X-API-KEY: $API_KEY" | jq .)
echo $request | jq '.docs[] | select(.year == 2022)' # отфильтровать вывод по году выхода
```
```PowerShell
$API_KEY = "ZYMNJJA-0J8MNPN-PB4N7R7-XXXXXXX"
$page = 1
$limit = 5
$query = "%D0%B7%D0%B8%D0%BC%D0%BE%D1%80%D0%BE%D0%B4%D0%BE%D0%BA"
$request = Invoke-RestMethod -Uri "https://api.kinopoisk.dev/v1.4/movie/search?page=$page&limit=$limit&query=$query" -Headers @{"accept"="application/json"; "X-API-KEY"="$API_KEY"}
$request.docs | Where-Object year -eq 2022
```
### UrlCode
```PowerShell
function Get-PercentEncode ($str) {
   $bytes = [System.Text.Encoding]::UTF8.GetBytes($str)
   ($bytes | ForEach-Object { "{0:X2}" -f $_ }) -join '%' -replace "^","%"
}
Get-PercentEncode "зимородок"
```
```PowerShell
function Get-UrlEncode($str) {
   [System.Web.HttpUtility]::UrlEncode($str)
}
UrlEncode "зимородок"
```
```Bash
percent-encode() {
  str=$1
    echo -n "$1" | iconv -t utf8 | od -An -tx1 | tr ' ' % | tr -d '\n'
}
percent-encode "зимородок"
```
```PowerShell
function Get-UrlDecode($encoded) {
    [System.Uri]::UnescapeDataString($encoded)
}
Get-UrlDecode "%D0%B7%D0%B8%D0%BC%D0%BE%D1%80%D0%BE%D0%B4%D0%BE%D0%BA"
```
```Bash
percent-decode() {
    encoded=$1
    local url_encoded="${1//+/ }"
    printf '%b' "${url_encoded//%/\\x}"
}
percent-decode "%D0%B7%D0%B8%D0%BC%D0%BE%D1%80%D0%BE%D0%B4%D0%BE%D0%BA"
```
### KinopoiskApiUnofficial

Бесплатно 500 запросов в сутки. [Swagger documentation](https://kinopoiskapiunofficial.tech/documentation/api)
```PowerShell
API_KEY="828ec96a-f45d-4e3d-84b1-XXXXXXXXXXXX"
$headers = @{
    "accept" = "application/json"
    "X-API-KEY" = "$API_KEY"
}
Invoke-RestMethod -Uri 'https://kinopoiskapiunofficial.tech/api/v2.2/films/1142153' -Headers $headers
```
`curl -s "https://kinopoiskapiunofficial.tech/api/v2.2/films/1142153" -H "accept: application/json" -H "X-API-KEY: $API_KEY" | jq .`

## Kinobox

`$url = "https://www.kinopoisk.ru/film/694051"` \
`$kp_id = $url -replace ".+/"` \
`https://kinomix.web.app/#694051` \
`curl -s -X GET "https://kinobox.tv/api/players/main?kinopoisk=$kp_id" -H "accept: application/json"` поиск по id Кинопоиск \
`curl -s -X GET "https://kinobox.tv/api/players/main?imdb=tt2293640" -H "accept: application/json"` поиск по id IMDb \
`curl -s -X GET "https://kinobox.tv/api/players/main?title=minions" -H "accept: application/json"` поиск основных плееров по названию \
`curl -s -X GET "https://kinobox.tv/api/players/all?title=minions" -H "accept: application/json"` поиск всех плееров \
`curl -s -X GET "https://kinobox.tv/api/popular/films" -H "accept: application/json"` популярные фильмы \
`curl -s -X GET "https://kinobox.tv/api/popular/series" -H "accept: application/json"` популярные сериалы

## VideoCDN

[API](https://github.com/notssh/videocdn-api) \
[Source](https://github.com/API-Movies/videocdn) \
[API JSON](https://api-movies.github.io/videocdn/index.json)
```PowerShell
$kp_id = 5106881
$token = "YfTWH2p3Mai7ziqDoGjS3yXXXXXXXXXX"
$ep = "tv-series"
$(Invoke-RestMethod $("https://videocdn.tv/api/$ep"+"?api_token=$token&field=kinopoisk_id&query=$kp_id")).data.episodes | Where-Object season_num -eq 2 | Select-Object @{Name="Episode"; Expression={$_.num}}, @{Name="Voice"; Expression={$_.media.translation.title}} # отфильтровать серии по второму сезону и отобразить все озвучки к сериям
```
```Bash
kp_id=5106881
token="YfTWH2p3Mai7ziqDoGjS3yXXXXXXXXXX"
ep="tv-series"
curl -s "https://videocdn.tv/api/$ep?api_token=$token&field=kinopoisk_id&query=$kp_id" | jq ".data[].episodes | length" # количество серий
curl -s "https://videocdn.tv/api/$ep?api_token=$token&field=kinopoisk_id&query=$kp_id" | jq ".data[].episodes[] | select(.season_num == 2) | {episode: .ru_title, voice: .media[].translation.title}" # отфильтровать параметры вывода
```

# Torrent

### Jackett

[Source](https://github.com/Jackett/Jackett)

`mkdir /jackett` \
`docker-compose.yml`
```yaml
---
services:
  jackett:
    image: lscr.io/linuxserver/jackett:latest
    container_name: jackett
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /jackett/data:/config
      - /jackett/blackhole:/downloads
    ports:
      - 9117:9117
    restart: unless-stopped
```
`docker-compose up -d jackett` \
`docker exec -it jackett /bin/bash` доступ к оболочке во время работы контейнера \
`docker logs -f jackett` мониторинг журналов контейнера

`/jackett/data/Jackett/ServerConfig.json` место хранения конфигурации сервера \
`/jackett/data/Jackett/Indexers/*.json` место хранения конфигурации индексаторов

`$API_KEY = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"` \
`Invoke-RestMethod "http://127.0.0.1:9117/api/v2.0/indexers/rutor/results/torznab/api?apikey=$API_KEY"` Прочитать RSS ленту RuTor \
`$query = "the+rookie"` \
`Invoke-RestMethod "http://127.0.0.1:9117/api/v2.0/indexers/rutor/results/torznab/api?apikey=$API_KEY&t=search&cat=&q=$query"` поиск в RuTor \
`Invoke-RestMethod "http://127.0.0.1:9117/api/v2.0/indexers/kinozal/results/torznab/api?apikey=$API_KEY&t=search&q=$query"` поиск в кинозал \
`Invoke-RestMethod "http://127.0.0.1:9117/api/v2.0/indexers/kinozal/results/torznab/api?apikey=$API_KEY&t=search&q=$query&cat=5000"` отфильтровать вывод по сериалам (Capabilities: 5000) \
`Invoke-RestMethod "http://127.0.0.1:9117/api/v2.0/indexers/all/results/torznab/api?apikey=$API_KEY&t=search&q=riverdale"` поиск во всех индексаторах \
`$(Invoke-RestMethod "http://127.0.0.1:9117/api/v2.0/indexers/all/results/torznab/api?apikey=$API_KEY&t=indexers&configured=true").indexers.indexer` cписок всех настроенных индексаторов (трекеров)

### Torrent-API-py

[Source](https://github.com/Ryuk-me/Torrent-Api-py) \
[Documentation](https://torrent-api-py-nx0x.onrender.com/docs#/default/health_route_health_get)
```
git clone https://github.com/Ryuk-me/Torrent-Api-py
cd Torrent-Api-py
pip install virtualenv
py -3 -m venv api-py
# Активировать виртуальную среду для Windows
.\api-py\Scripts\activate
# Активировать виртуальную среду для Linux
# $ source api-py/bin/activate
# Установить зависимости и запустить
pip install -r requirements.txt
python main.py
# Proxy: https://github.com/dperson/torproxy
# export HTTP_PROXY="http://proxy-host:proxy-port"
```
`$srv = "http://localhost:8009"` local \
`$srv = "https://torrent-api-py-nx0x.onrender.com"` public \
`Invoke-RestMethod $srv/api/v1/sites` список доступных трекеров \
`Invoke-RestMethod "$srv/api/v1/search/?site=torlock&query=the+rookie&limit=0&page=1"` поиск в выбранном трекере \
`Invoke-RestMethod "$srv/api/v1/all/search?query=the+rookie&limit=0"` поиск по названию во всех трекерах

### Plex

`$API_TOKEN = "XXXXXXXXXXXXXXXXXXXX"`
```
$headers = @{
    "X-Plex-Token" = $API_TOKEN
    "accept" = "application/json"
}
```
`$(Invoke-RestMethod -Headers $headers -Uri http://localhost:32400/servers).MediaContainer.Server` версия сервера \
`Invoke-RestMethod -Headers $headers -Uri http://localhost:32400/diagnostics/logs -OutFile log.zip` выгруить лог с сервера \
`$(Invoke-RestMethod -Headers $headers -Uri http://localhost:32400/library/sections).MediaContainer.Directory` список секций добавленных на сервер \
`$section_key = $(Invoke-RestMethod -Headers $headers -Uri http://localhost:32400/library/sections).MediaContainer.Directory.key[0]` \
`Invoke-RestMethod -Headers $headers -Uri http://localhost:32400/library/sections/$section_key/refresh` синхронизация указанной секции в Plex по ключу \
`$(Invoke-RestMethod -Headers $headers -Uri http://localhost:32400/library/sections/2/folder).MediaContainer.Metadata` получить список директорий и файлов в корне выбранной секции \
`$(Invoke-RestMethod -Headers $headers -Uri http://localhost:32400/library/sections/2/folder?parent=204).MediaContainer.Metadata` получить список всех файлов в указанной директории через ключ (MediaContainer.Metadata.key) конечной точки

### Jellyfin

[Source](https://github.com/jellyfin/jellyfin) \
[API Docs](https://api.jellyfin.org)

`$API_TOKEN "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"` \
`Invoke-RestMethod -Headers @{"X-Emby-Token" = $API_TOKEN} http://localhost:8096/Users` список пользователей и их id \
`$Users = Invoke-RestMethod -Headers @{"X-Emby-Token" = $API_TOKEN} http://localhost:8096/Users` \
`$UserId = $($Users | Where-Object Name -match "Lifailon").Id` забрать id пользователя \
`Invoke-RestMethod -Headers @{"X-Emby-Token" = $API_TOKEN} http://localhost:8096/System/Info` информация о системе \
`$(Invoke-RestMethod -Headers @{"X-Emby-Token" = $API_TOKEN} http://localhost:8096/Items).Items` список добавленных объектов директорий \
`$ItemId = $(Invoke-RestMethod -Headers @{"X-Emby-Token" = $API_TOKEN} http://localhost:8096/Items).Items[-1].Id` забрать id директории \
`$Data = $(Invoke-RestMethod -Headers @{"X-Emby-Token" = $API_TOKEN} "http://localhost:8096/Users/$UserId/Items?ParentId=$ItemId").Items` получить содержимое корневой директории по Id из Items \
`$TvId = $($data | Where-Object Name -match "Rookie").Id` найти сериал или фильм по имени и забрать его Id \
`$(Invoke-RestMethod -Headers @{"X-Emby-Token" = $API_TOKEN} "http://localhost:8096/Users/$UserId/Items?ParentId=$TvId").Items` получить содержимое дочерней директории по Id ее родительской директории

# Telegram

@BotFather (https://t.me/BotFather) /newbot

Format: `https://api.telegram.org/bot<token>/<endpoint>`

[getupdates](https://core.telegram.org/bots/api#getupdates)
```PowerShell
function Get-FromTelegram {
    param (
        $Token = "687...:AAF...",
        [switch]$Date,
        [switch]$Last,
        [switch]$ChatID
    )
    $endpoint = "getUpdates"
    $url      = "https://api.telegram.org/bot$Token/$endpoint"
    $result   = Invoke-RestMethod -Uri $url
    if ($Date) {
        $Collections = New-Object System.Collections.Generic.List[System.Object]
        foreach ($r in $($result.result)) {
            $EpochTime = [DateTime]"1/1/1970"
            $TimeZone = Get-TimeZone
            $UTCTime = $EpochTime.AddSeconds($r.message.date)
            $d = $UTCTime.AddMinutes($TimeZone.BaseUtcOffset.TotalMinutes)
            $Collections.Add([PSCustomObject]@{
                Message = $r.message.text;
                Date    = $d
            })
        }
        $Collections
    }
    else {
        if ($Last) {
            $result.result.message.text[-1]
        }
        elseif ($ChatID) {
            $Collections = New-Object System.Collections.Generic.List[System.Object]
            foreach ($r in $($result.result)) {
                $Collections.Add([PSCustomObject]@{
                    Message = $r.message.text;
                    UserName = $r.message.chat.username;
                    ChatID = $r.message.chat.id;
                    ChatType = $r.message.chat.type
                })
            }
            $Collections
        }
        else {
            $result.result.message.text
        }
    }
}
```
`Get-FromTelegram` \
`Get-FromTelegram -Last` \
`Get-FromTelegram -Date` \
`Get-FromTelegram -ChatID`

[sendmessage](https://core.telegram.org/bots/api#sendmessage)
```PowerShell
function Send-ToTelegram {
param (
    [Parameter(Mandatory = $True)]$Text,
    $Token    = "687...:AAF...",
    $Chat     = "125468108",
    $Keyboard
)
    $endpoint = "sendMessage"
    $url      = "https://api.telegram.org/bot$Token/$endpoint"
    $Body = @{
        chat_id = $Chat
        text    = $Text
    }
    if ($keyboard -ne $null) {
        $Body += @{reply_markup = $keyboard}
    }
    Invoke-RestMethod -Uri $url -Body $Body
}
```
`Send-ToTelegram -Text "Send test from powershell"`
```PowerShell
$LastDate = (Get-FromTelegram -date)[-1].Date
while ($true) {
    $LastMessage  = (Get-FromTelegram -date)[-1]
    Start-Sleep 1
    $LastDateTest = $LastMessage.Date
    if (($LastMessage.Message -match "/Service") -and ($LastDate -ne $LastDateTest)) {
        $ServiceName = $($LastMessage.Message -split " ")[-1]
        $Result = $(Get-Service $ServiceName -ErrorAction Ignore).Status
        if ($Result) {
            Send-ToTelegram -Text $Result
        } else {
            Send-ToTelegram -Text "Service not found"
        }
        $LastDate = $LastDateTest
    }
}
```
`/Service vpnagent` \
`/Service WinRM` \
`/Service test`

### Button
```PowerShell
$keyboard = '{
    "inline_keyboard":[[
        {"text":"Uptime","callback_data":"/Uptime"},
        {"text":"Test","callback_data":"/Test"}
    ]]
}'
Send-ToTelegram -Text "Test buttons" -Keyboard $keyboard
$request = (Invoke-RestMethod -Uri "https://api.telegram.org/bot$Token/getUpdates").result.callback_query
$request.data # прочитать callback_data нажатой кнопки
$request.message.date
```
### Send-ToTelegramFile

https://core.telegram.org/bots/api#senddocument
```PowerShell
function Send-ToTelegramFile {
    param (
        [Parameter(Mandatory = $true)][string]$Path,
        [Parameter(Mandatory = $true)][string]$Token,
        [Parameter(Mandatory = $true)][string]$Chat,
        $Keyboard
    )
    $endpoint = "senddocument"
    $url      = "https://api.telegram.org/bot$Token/$endpoint"
    $multipartContent = [System.Net.Http.MultipartFormDataContent]::new()
    $fileStream = [System.IO.FileStream]::new($Path, [System.IO.FileMode]::Open)
    $fileContent = [System.Net.Http.StreamContent]::new($fileStream)
    $fileHeader = [System.Net.Http.Headers.ContentDispositionHeaderValue]::new("form-data")
    $fileHeader.Name = "document"
    $fileHeader.FileName = [System.IO.Path]::GetFileName($Path)
    $fileContent.Headers.ContentDisposition = $fileHeader
    $multipartContent.Add($fileContent, "document")
    if ($Keyboard) {
        $keyboardContent = [System.Net.Http.StringContent]::new($Keyboard)
        $keyboardContent.Headers.ContentType.MediaType = "application/json"
        $multipartContent.Add($keyboardContent, "reply_markup")
    }
    $chatContent = [System.Net.Http.StringContent]::new($Chat)
    $multipartContent.Add($chatContent, "chat_id")
    $response = Invoke-RestMethod -Uri $url -Method Post -Body $multipartContent -ContentType "multipart/form-data"
    $fileStream.Dispose()
    return $response
}
```
`Send-ToTelegramFile -Path "C:\Users\Lifailon\Documents\lake.jpg" -Token "7777777777:AAF..." -Chat "7777777777"`

# Discord

[Developers](https://discord.com/developers/applications)

Создаем Applications (General Information). В Bot привязываем к Application и копируем токен авторизации. В OAuth2 - URL Generator выбираем bot и права Administrator и копируем созданный URL для добавления на канал. Переходим по url и добавляем бота на сервер. Получаем ID канала на сервере (текстовые каналы, правой кнопкой мыши копируем ссылку и забираем последний id в url).

### Send to Discord
```Bash
DISCORD_TOKEN="MTE5NzE1NjM0NTM3NjQxMTcyOQ.XXXXXX.EzBF6RA9Kx_MSuhLW5elH1U-XXXXXXXXXXXXXX"
DISCORD_CHANNEL_ID="119403124XXXXXXXXXX"
TEXT="test from bash"
URL="https://discordapp.com/api/channels/$DISCORD_CHANNEL_ID/messages"
curl -s -X POST $URL \
  -H "Authorization: Bot $DISCORD_TOKEN" \
  -H "Content-Type: application/json" \
  -d "{\"content\": \"$TEXT\"}"
```
```PowerShell
$DISCORD_TOKEN = "MTE5NzE1NjM0NTM3NjQxMTcyOQ.XXXXXX.EzBF6RA9Kx_MSuhLW5elH1U-XXXXXXXXXXXXXX"
$DISCORD_CHANNEL_ID = "119403124XXXXXXXXXX"
$TEXT = "test from PowerShell"
$URL = "https://discordapp.com/api/channels/$DISCORD_CHANNEL_ID/messages"
$Body = @{
    content = $TEXT
} | ConvertTo-Json
curl -s $URL -X POST -H "Authorization: Bot $DISCORD_TOKEN" -H "Content-Type: application/json" -d $Body
```
### Read from Discord
```Bash
curl -s -X GET $URL \
  -H "Authorization: Bot $DISCORD_TOKEN" \
  -H "Content-Type: application/json" | jq -r .[0].content
```
```PowerShell
$messages = (curl -s -X GET $URL -H "Authorization: Bot $DISCORD_TOKEN" -H "Content-Type: application/json" | ConvertFrom-Json)
$messages | Select-Object content,timestamp,{$_.author.username}
```
### HttpClient
```PowerShell
$DISCORD_TOKEN = "MTE5NzE1NjM0NTM3NjQxMTcyOQ.XXXXXX.EzBF6RA9Kx_MSuhLW5elH1U-XXXXXXXXXXXXXX"
$DISCORD_CHANNEL_ID = "119403124XXXXXXXXXX"
$URL = "https://discordapp.com/api/channels/$DISCORD_CHANNEL_ID/messages"
$HttpClient = New-Object System.Net.Http.HttpClient
$HttpClient.DefaultRequestHeaders.Authorization = "Bot $DISCORD_TOKEN"
$response = $HttpClient.GetAsync($URL).Result
$messages = $response.Content.ReadAsStringAsync().Result
($messages | ConvertFrom-Json).content
```
### Button
```Bash
curl -X POST $URL \
  -H "Content-Type: application/json" \
  -H "Authorization: Bot $DISCORD_TOKEN" \
  -d '
  {
    "content": "Test text for button",
    "components": [
      {
        "type": 1,
        "components": [
          {
            "type": 2,
            "label": "Button",
            "style": 1,
            "custom_id": "button_click"
          }
        ]
      }
    ]
  }'
```
### Discord Net Webhook

```PowerShell
Add-Type -Path $(ls "$home\Documents\Discord.NET\*.dll").FullName
# https://discordapp.com/api/webhooks/<webhook_id>/<webhook_token> (Настроить канал - Интеграция)
$webhookId = 1197577280000000000
$webhookToken = "rs8AA-XXXXXXXXXXX_Vk5RUI4A6HuSGhpCCTepq25duwCwLXasfv6u23a7XXXXXXXXXX"
$messageContent = "Test dotNET"
$client = New-Object Discord.Webhook.DiscordWebhookClient($webhookId, $webhookToken)
$client.SendMessageAsync($messageContent).Wait()
```

### Discord Net WebSocket

```PowerShell
$DiscordAssemblies = $(ls "$home\Documents\Discord.NET\*.dll").FullName
foreach ($assembly in $DiscordAssemblies) {
    Add-Type -Path $assembly
}
$DISCORD_TOKEN = "MTE5NzE1NjM0NTM3NjQxMTcyOQ.XXXXXX.EzBF6RA9Kx_MSuhLW5elH1U-XXXXXXXXXXXXXX"
$Client = New-Object Discord.WebSocket.DiscordSocketClient
$Client.Add_MessageReceived({
    param($message)
    if ($message.Author.Id -ne $Client.CurrentUser.Id) {
        Write-Host ("Received message from " + $message.Author.Username + ": " + $message.Content)
        if ($message.Content.Contains("ping")) {
            $message.Channel.SendMessageAsync("pong").GetAwaiter().GetResult()
        }
    }
})
$Client.LoginAsync([Discord.TokenType]::Bot, $DISCORD_TOKEN).GetAwaiter().GetResult()
#$Client.StartAsync().Wait()
$Client.StartAsync().GetAwaiter().GetResult()
$Client.ConnectionState

[console]::ReadKey($true)
$Client.LogoutAsync().GetAwaiter().GetResult()
$Client.Dispose()
```
