# 3.3. Операционные системы, лекция 1
### 1.	
```
strace /bin/bash -c 'cd /tmp' 2>&1 | grep /tmp
```
просматриваем системные вызовы, при выполнении программы `cd /tmp`, перенаправляем поток ошибок в поток вывода и ищем кто обращался к каталогу /tmp
```
chdir("/tmp")                           = 0
```
тот процесс который нам нужен
### 2.	
`strace file '/dev/tty' 2>&1 | grep openat` -  ищем что открывает системный вызов при выполнении

### 3.	
```
exec 3> newfile.txt
ping 8.8.8.8>&3
rm newfile.txt
lsof | grep deleted
ping      1278                        vagrant    1w      REG              253,0        0    1152681 /home/vagrant/newfile.txt (deleted)
echo “ ” >/ proc/1278 /fd/3
> /proc/1278 /fd/3
```
```
truncate -s 0 /proc/1278/fd/3
```
(truncate команда уменьшения размера файла до заданного значения)  скорее всего он не подойдет к заданию (перенаправление потоков), но работу свою выполнит.
### 4.	
Зомби процессы не занимают ресурсы кроме одного: PID – идентификатор процессов. Которые ограничены на каждого пользователя.

### 5.  
```
root@vagrant:/home/vagrant# dpkg -L bpfcc-tools | grep sbin/opensnoop
/usr/sbin/opensnoop-bpfcc
root@vagrant:/home/vagrant# sudo opensnoop-bpfcc
PID    COMM               FD ERR PATH
1      systemd            21   0 /proc/596/cgroup
796    vminfo              4   0 /var/run/utmp
618    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
618    dbus-daemon        20   0 /usr/share/dbus-1/system-services
618    dbus-daemon        -1   2 /lib/dbus-1/system-services
618    dbus-daemon        20   0 /var/lib/snapd/dbus-1/system-services/
626    irqbalance          6   0 /proc/interrupts
626    irqbalance          6   0 /proc/stat
```

### 6.

NOTES
Part of the utsname information is also accessible via
       /proc/sys/kernel/{ostype, hostname, osrelease, version, domainname}.
### 7.  
Чем отличается последовательность команд через ; и через && в bash? Например:  
`root@netology1:# test -d /tmp/some_dir; echo Hi` (после завершения test выполнится команда echo Hi) тоесть «;» разделяет выполнение команд.  
`root@netology1:# test -d /tmp/some_dir && echo Hi` (echo Hi выполнится только если команда test завершиться без ошибки)  
Есть ли смысл использовать в bash &&, если применить set -e?  
Похоже на то что можно использовать и && и set –e. Даже в циклах. Но в сценариях возможно использовать set –e было бы продуктивнее.  
### 8.  
`Set –e ` -  предписывает немедленно завершить работу, если какая-либо команда [1] имеет ненулевой статус выхода.  
То есть если в сценариях появится ошибка, а дальше выполнение будет без ошибок, тоо общий статус выполнения сценария будет «без ошибок»,  
тем самым мы можем пропустить ту или иную ошибку.  
`-u`  Считает неустановленные переменные ошибкой при подстановке  
`-x`  Печатать команды и их аргументы по мере их выполнения.  
`-o pipefail`  этот параметр предотвращает маскирование ошибок в конвейере.  
В случае сбоя какой-либо команды в конвейере этот код возврата будет использоваться как код возврата для всего конвейера.  
По умолчанию конвейер возвращает код последней команды, даже если она выполнена успешно.  
Итог `set -euxo pipefail` будет являться отличным способом выявления ошибок в сценариях 

### 9.	
```
vagrant@vagrant:~$ man ps
vagrant@vagrant:~$ ps -o stat
STAT
Ss
R+
```
Ss является самым частым процессом.
```
vagrant@vagrant:~$ ps -A -o stat | sort | uniq -c
      8 I
     37 I<
      1 R+
     25 S
      2 S+
      6 S<
      1 Sl
      1 SLsl
      2 SN
      1 S<s
     13 Ss
      1 Ss+
      6 Ssl
      1 STAT
```
дополнительные к основной заглавной буквы статуса процессов дают дополнительную информацию о процессе, к примеру лидер сессии (s)

`S `   interruptible sleep (waiting for an event to complete)(непрерываемый сон)  
`R `   running or runnable (on run queue)  

`s` является лидером сессии

