# Домашнее задание к занятию "3.4. Операционные системы, лекция 2"
### 1.	На лекции мы познакомились с node_exporter. В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter:поместите его в автозагрузку,предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на systemctl cat cron),удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
`wget https://github.com/prometheus/node_exporter/releases/download/v1.3.0/node_exporter-1.3.0.linux-amd64.tar.gz`  
`tar node_exporter-1.3.0.linux-amd64.tar.gz` - распаковываем архив  
`cp -a node_exporter-1.3.0.linux-amd64/node_exporter /usr/local/bin/` - копируем в /usr/local/bin/  
`cat > /etc/systemd/system/node_exporter.service`   - создаем файл  
```
[Unit]
Description=Node Exporter
[Service]
EnvironmentFile=/etc/default/node_exporter
ExecStart=/usr/local/bin/node_exporter $OPTIONS
[Install]
WantedBy=multi-user.target
```
`systemctl daemon-reload` - Перезагрузите конфигурацию диспетчера systemd.  
`systemctl enable node_exporter` - Добавляем в автозагрузку  
`systemctl start node_exporter` - запускаем  
`systemctl status node_exporter` - Проверим работу службы:  



### 2.	Ознакомьтесь с опциями node_exporter и выводом /metrics по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.
```
TYPE node_cpu_seconds_total counter
node_cpu_seconds_total{cpu="0",mode="idle"} 76.35
node_cpu_seconds_total{cpu="0",mode="iowait"} 1.15
node_cpu_seconds_total{cpu="0",mode="system"} 3.72
node_cpu_seconds_total{cpu="0",mode="user"} 2.08
node_cpu_seconds_total{cpu="1",mode="idle"} 68.72
node_cpu_seconds_total{cpu="1",mode="iowait"} 7.38
node_cpu_seconds_total{cpu="1",mode="system"} 4.34
node_cpu_seconds_total{cpu="1",mode="user"} 1.22
```
```
node_network_receive_drop_total{device="eth0"} 0
node_network_receive_drop_total{device="lo"} 0
node_network_receive_errs_total{device="eth0"} 0
node_network_receive_errs_total{device="lo"} 0
```
```
# HELP node_memory_Active_bytes Memory information field Active_bytes.
# TYPE node_memory_Active_bytes gauge
node_memory_Active_bytes 2.04660736e+08
# HELP node_memory_Active_file_bytes Memory information field Active_file_bytes.
# TYPE node_memory_Active_file_bytes gauge

```
```
# HELP node_disk_read_bytes_total The total number of bytes read successfully.
# TYPE node_disk_read_bytes_total counter
node_disk_read_bytes_total{device="dm-0"} 4.49537024e+08
node_disk_read_bytes_total{device="sda"} 4.59433984e+08
# HELP node_disk_read_time_seconds_total The total number of seconds spent by all reads.
# TYPE node_disk_read_time_seconds_total counter
node_disk_read_time_seconds_total{device="dm-0"} 73.69200000000001
node_disk_read_time_seconds_total{device="sda"} 48.049

# HELP node_disk_write_time_seconds_total This is the total number of seconds spent by all writes.
# TYPE node_disk_write_time_seconds_total counter
node_disk_write_time_seconds_total{device="dm-0"} 20.692
node_disk_write_time_seconds_total{device="sda"} 5.405
# HELP node_disk_writes_completed_total The total number of writes completed successfully.
# TYPE node_disk_writes_completed_total counter
node_disk_writes_completed_total{device="dm-0"} 4781
node_disk_writes_completed_total{device="sda"} 1307

```

### 3.	Установите в свою виртуальную машину Netdata. Воспользуйтесь готовыми пакетами для установки (sudo apt install -y netdata). После успешной установки:в конфигурационном файле /etc/netdata/netdata.conf в секции [web] замените значение с localhost на bind to = 0.0.0.0,добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте vagrant reload:config.vm.network "forwarded_port", guest: 19999, host: 19999
Зашел с локального компьютера `http://localhost:19999/#menu_system_submenu_cpu;theme=slate`
```
Total CPU utilization (all cores). 100% here means there is no CPU idle time at all.  
You can get per core usage at the CPUs section and per application usage at the Applications Monitoring section.  
Keep an eye on iowait (0,00%). If it is constantly high, your disks are a bottleneck and they slow your system down.  
An important metric worth monitoring, is softirq  (0,49%). A constantly high percentage of softirq may indicate network driver issues.
```
```
Total Disk I/O, for all physical disks. You can get detailed information about each disk at the Disks section and per application Disk usage at the Applications Monitoring section. Physical are all the disks that are listed in /sys/block, but do not exist in /sys/devices/virtual/block.
```
### 4.	Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
Да можно.  
```
vagrant@vagrant:$ dmesg | grep virtuali  
[    0.004795] CPU MTRRs all blank - virtualized system.  
[    0.039812] Booting paravirtualized kernel on KVM  
[    4.916004] systemd[1]: Detected virtualization oracle.  
```
### 5.  Как настроен sysctl fs.nr_open на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (ulimit --help)?
```
vagrant@vagrant:$ sysctl fs.nr_open  
fs.nr_open = 1048576  
```
`nr_open` - максимальное число окрытых дескрипторов, которые может использовать процесс (системное ограничение)  
лимит на пользователя , максимальное количество открытых файловых дескриптеров.  
```
vagrant@vagrant:$ ulimit -n
1024
```
### 6. Запустите любой долгоживущий процесс (не ls, который отработает мгновенно, а, например, sleep 1h) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через nsenter. Для простоты работайте в данном задании под root (sudo -i). Под обычным пользователем требуются дополнительные опции (--map-root-user) и т.д.
```
vagrant@vagrant:$ sudo -i  
root@vagrant:~# unshare -f --pid --mount-proc sleep 1h  
root@vagrant:~# ps -aux | grep sleep  
root        1467  0.0  0.0   5480   528 pts/0    T    18:57   0:00 unshare -f --pid --mount-proc sleep 1h  
root        1468  0.0  0.0   5476   532 pts/0    S    18:57   0:00 sleep 1h  
root        1474  0.0  0.0   6432   740 pts/0    S+   18:58   0:00 grep --color=auto sleep  
```
`root@vagrant:# sudo nsenter --target 1468 --pid –mount` «проваливаемся в процесс» в namespace  
```
root@vagrant:/# ps aux  
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND  
root           1  0.0  0.0   5476   532 pts/0    S    18:57   0:00 sleep 1h  
root           2  0.0  0.4   7236  4068 pts/0    S    18:58   0:00 -bash  
root          13  0.0  0.3   8892  3496 pts/0    R+   18:59   0:00 ps aux  
```
Тут мы видим что процесс думает что он является самостоятельным. Запущен PID 1  
### 7.  Найдите информацию о том, что такое :(){ :|:& };:. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (это важно, поведение в других ОС не проверялось). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов dmesg расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?
`:(){ :|:& };:` это fork bomb  
```
:(){
 :|:&
};:
```
это функция, которая параллельно запускает два своих экземпляра, а каждый запускает ещё по два и т.д. Тем самым забивая все доступные процессы пользователя.  
```
root@vagrant:# dmesg | grep fork  
[ 3400.601389] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-4.scope  
```
`root@vagrant:# systemctl cat user-1000.slice` смотрим что делает user-1000.slice  
```
 /usr/lib/systemd/system/user-.slice.d/10-defaults.conf
  SPDX-License-Identifier: LGPL-2.1+

  This file is part of systemd.

  systemd is free software; you can redistribute it and/or modify it
  under the terms of the GNU Lesser General Public License as published by
  the Free Software Foundation; either version 2.1 of the License, or
  (at your option) any later version.
[Unit]
Description=User Slice of UID %j
Documentation=man:user@.service(5)
After=systemd-user-sessions.service
StopWhenUnneeded=yes
[Slice]
TasksMax=33%
```
Если превышает количество задач 33% то StopWhenUnneeded будет отчищать активные процессы  
Если мы изменим параметр `TasksMax=infinity` тогда система будет постоянно перегружена.  

Так же можно повысить или понизить лимиты процессов командой `ulimit –u` 
