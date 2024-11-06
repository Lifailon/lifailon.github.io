---
title: "Threads"
author: "Lifailon"
date: "2024-03-14T03:00:00+03:00"
---

## Jobs

`Get-Job` получение списка задач\
`Start-Job` запуск процесса\
`Stop-Job` остановка процесса\
`Suspend-Job` приостановка работы процесса\
`Resume-Job` восстановление работы процесса\
`Wait-Job` ожидание вывода команды\
`Receive-Job` получение результатов выполненного процесса\
`Remove-Job` удалить задачу

``` powershell
function Start-PingJob ($Network) {
    $RNetwork = $Network -replace "\.\d{1,3}$","."
    foreach ($4 in 1..254) {
        $ip = $RNetwork+$4
        # Создаем задания, забираем 3-ю строку вывода и добавляем к выводу ip-адрес
        (Start-Job {"$using:ip : "+(ping -n 1 -w 50 $using:ip)[2]}) | Out-Null
    }
    while ($True) {
        $status_job = $(Get-Job).State[-1] # забираем статус последнего задания (задания выполняются по очереди сверху вниз)
        if ($status_job -like "Completed") { # проверяем задание на выполнение
            $ping_out = Get-Job | Receive-Job # если выполнено, забираем вывод всех заданий
            Get-Job | Remove-Job -Force # удаляем задания
            break # завершаем цикл
        }
    }
    $ping_out
}
```

`Start-PingJob -Network 192.168.3.0`\
`$(Measure-Command {Start-PingJob -Network 192.168.3.0}).TotalSeconds`
60 Seconds

## ThreadJob

`Install-Module -Name ThreadJob`\
`Get-Module ThreadJob -list`\
`Start-ThreadJob {ping ya.ru} | Out-Null` создать фоновую задачу\
`Get-Job | Receive-Job -Keep` отобразить и не удалять вывод\
`(Get-Job).HasMoreData` если False, то вывод команы удален\
`(Get-Job)[-1].Output` отобразить вывод последней задачи

``` powershell
function Start-PingThreadJob ($Network) {
    $RNetwork = $Network -replace "\.\d{1,3}$","."
    foreach ($4 in 1..254) {
        $ip = $RNetwork+$4
        $(Start-ThreadJob {
            "$using:ip : " + $(ping -n 1 -w 50 $using:ip)[2]
        }) | Out-Null
    }
    while ($True) {
        $status_job = $(Get-Job).State[-1]
        if ($status_job -like "Completed") {
            $ping_out = Get-Job | Receive-Job
            Get-Job | Remove-Job -Force
            break
        }
    }
    $ping_out
}
```

`Start-PingThreadJob -Network 192.168.3.0`\
`$(Measure-Command {Start-PingThread -Network 192.168.3.0}).TotalSeconds`
24 Seconds

## PoshRSJob

Install-Module -Name PoshRSJob

``` powershell
function Start-PingRSJob ($Network) {
    $RNetwork = $Network -replace "\.\d{1,3}$","."
    foreach ($4 in 1..254) {
        $ip = $RNetwork+$4
        $(Start-RSJob {
            "$using:ip : " + $(ping -n 1 -w 50 $using:ip)[2]
        }) | Out-Null
    }
    while ($True) {
        $status_job = $(Get-RSJob).State -notcontains "Running" # проверяем, что массив не содержит активных заданий
        if ($status_job) {
            $ping_out = Get-RSJob | Receive-RSJob
            Get-RSJob | Remove-RSJob
            break
        }
    }
    $ping_out
}
```

`Start-PingRSJob -Network 192.168.3.0`\
`$(Measure-Command {Start-PingRSJob -Network 192.168.3.0}).TotalSeconds`
10 Seconds

## Parallel

``` powershell
# Import function from GitHub to current session
$module = "https://raw.githubusercontent.com/RamblingCookieMonster/Invoke-Parallel/master/Invoke-Parallel/Invoke-Parallel.ps1"
Invoke-Expression $(Invoke-RestMethod $module)
```

Get-Help Invoke-Parallel -Full

``` powershell
function Start-PingInvokeParallel ($Network) {
    $RNetwork = $Network -replace "\.\d{1,3}$","."
    1..254 | ForEach-Object {$srvList += @($RNetwork+$_)}
    Invoke-Parallel -InputObject $srvList -ScriptBlock {
        "$_ : " + $(ping -n 1 -w 50 $_)[2]
    }
}
```

`Start-PingInvokeParallel -Network 192.168.3.0`\
`$(Get-History)[-1].Duration.TotalSeconds` 7 seconds

``` powershell
$array_main  = 1..10 | ForEach-Object {"192.168.3.$_"}
Invoke-Parallel -InputObject $(0..$($array_main.Count-1)) -ScriptBlock {
    Foreach ($n in 1..100) {
        Start-Sleep -Milliseconds 100
        Write-Progress -Activity $($array_main[$_]) -PercentComplete $n -id $_
    }
} -ImportVariables
```

## ForEach-Object-Parallel

``` powershell
function Start-PingParallel ($Network) {
    $RNetwork = $Network -replace "\.\d{1,3}$","."
    1..254 | ForEach-Object -Parallel {
        "$using:RNetwork.$_ : " + $(ping -n 1 -w 50 "$using:RNetwork$_")[2]
    } -ThrottleLimit 254
}
```

`Start-PingParallel -Network 192.168.3.0`\
`$(Get-History)[-1].Duration.TotalSeconds` 2 seconds

``` powershell
function Start-TestConnectParallel (
        $Network,
        [switch]$Csv
    ) {
    $RNetwork = $Network -replace "\.\d{1,3}$","."
    if ($csv) {
        "Address,Status,Latency"
    }
    1..254 | ForEach-Object -Parallel {
        $test = Test-Connection "$using:RNetwork$_" -Count 1 -TimeoutSeconds 1
        if ($using:csv) {
            "$($using:RNetwork)$_,$($test.Status),$($test.Latency)"
        } else {
            $test
        }
    } -ThrottleLimit 254
}
```

`Start-TestConnectParallel -Network 192.168.3.0 -Csv | ConvertFrom-Csv`\
`$(Get-History)[-1].Duration.TotalSeconds` 3 seconds

``` powershell
$array_main  = 1..10 | ForEach-Object {"192.168.3.$_"}
$(0..$($array_main.Count-1)) | ForEach-Object -Parallel {
    Foreach ($n in 1..100) {
        Start-Sleep -Milliseconds 100
        $array_temp = $using:array_main
        Write-Progress -Activity $($array_temp[$_]) -PercentComplete $n -id $_
    }
} -ThrottleLimit $($array_main.Count)
```
