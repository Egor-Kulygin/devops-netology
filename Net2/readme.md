# Домашнее задание к занятию "3.7. Компьютерные сети, лекция 2"
### 1.	Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?
Windows - ` Get-NetIPInterface`   
 ` netsh interface show interface`   
`Get-NetAdapter`   
` ipconfig /all`   
```
PS C:\Users\Admin> Get-NetIPInterface
ifIndex InterfaceAlias                  AddressFamily NlMtu(Bytes) InterfaceMetric Dhcp     ConnectionState PolicyStore
------- --------------                  ------------- ------------ --------------- ----     --------------- -----------
13      VirtualBox Host-Only Network    IPv6                  1500              25 Enabled  Connected       ActiveStore
12      Ethernet                        IPv6                  1500              35 Enabled  Connected       ActiveStore
1       Loopback Pseudo-Interface 1     IPv6            4294967295              75 Disabled Connected       ActiveStore
13      VirtualBox Host-Only Network    IPv4                  1500              25 Disabled Connected       ActiveStore
12      Ethernet                        IPv4                  1500              35 Enabled  Connected       ActiveStore
1       Loopback Pseudo-Interface 1     IPv4            4294967295              75 Disabled Connected       ActiveStore
```
Linux -` ip link show`  или `ip a`   
Через файл - `cat /proc/net/dev`   
```
vagrant@vagrant:~$ ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:b1:28:5d brd ff:ff:ff:ff:ff:ff
```
### 2.	Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?
Протокол `LLDP` (Link Layer Discovery Protocol) позволяет сетевым устройствам передавать информацию о себе и своих возможностях, а также получать эту информацию о соседних устройствах, подключенных по Ethernet портам.
`ip neigh show`   
`sudo apt update`   
`sudo apt install lldpd` - установка пакета   
`lldpcli show neighbors`   

### 3.	Какая технология используется для разделения L2 коммутатора на несколько виртуальных сетей? Какой пакет и команды есть в Linux для этого? Приведите пример конфига.
`VLAN ` (Virtual Local Area Network) — виртуальная локальная компьютерная сеть.   
`sudo apt install vlan` - установка vlan   
`modprobe --first-time 8021q` - установка модуля   
`modinfo 8021q` - проверяем что модуль установлен   
`Cat /proc/net/dev` - смотрим список доступных интерфейсов   
```
vagrant@vagrant:~$ cat /proc/net/dev
Inter-|   Receive                                                |  Transmit
 face |bytes    packets errs drop fifo frame compressed multicast|bytes    packets errs drop fifo colls carrier compressed
    lo:    6004      64    0    0    0     0          0         0     6004      64    0    0    0     0       0          0
  eth0: 11326897   10568    0    0    0     0          0         0   283859    2927    0    0    0     0       0          0
```
` sudo ip link add link eth0 name eth0.100 type vlan id 100` - создаем виртуальный порт lan (vlan)   
`Cat /proc/net/dev` - Проверяем, добавился ли наше новое устройство (можно проверить и `ip a`)   
```
vagrant@vagrant:~$ cat /proc/net/dev   
Inter-|   Receive                                                |  Transmit
 face |bytes    packets errs drop fifo frame compressed multicast|bytes    packets errs drop fifo colls carrier compressed
    lo:    6004      64    0    0    0     0          0         0     6004      64    0    0    0     0       0          0
eth0.100:       0       0    0    0    0     0          0         0        0       0    0    0    0     0       0          0
  eth0: 11336317   10694    0    0    0     0          0         0   290250    2993    0    0    0     0       0          0
```
`sudo ip addr add 192.168.100.2/24 dev enp1s0.100` - добавляем ip адрес и маску подсети   
` ip a ` - Проверяем получение интерфейсом адреса и маски.   
```
vagrant@vagrant:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:b1:28:5d brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic eth0
       valid_lft 81590sec preferred_lft 81590sec
    inet6 fe80::a00:27ff:feb1:285d/64 scope link
       valid_lft forever preferred_lft forever
3: eth0.100@eth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 08:00:27:b1:28:5d brd ff:ff:ff:ff:ff:ff
    inet 192.168.100.2/24 scope global eth0.100
```




### 4. Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.
`Bonding` – это объединение сетевых интерфейсов по определенному типу агрегации, Служит для увеличения пропускной способности и/или отказоустойчивость сети.   
Типы агрегации интерфейсов в Linux:   
`Mode-0(balance-rr) `– Данный режим используется по умолчанию. Balance-rr обеспечивается балансировку нагрузки и отказоустойчивость. В данном режиме сетевые пакеты отправляются “по кругу”, от первого интерфейса к последнему. Если выходят из строя интерфейсы, пакеты отправляются на остальные оставшиеся. Дополнительной настройки коммутатора не требуется при нахождении портов в одном коммутаторе. При разностных коммутаторах требуется дополнительная настройка.   
`Mode-1(active-backup) `– Один из интерфейсов работает в активном режиме, остальные в ожидающем. При обнаружении проблемы на активном интерфейсе производится переключение на ожидающий интерфейс. Не требуется поддержки от коммутатора.   
`Mode-2(balance-xor) `– Передача пакетов распределяется по типу входящего и исходящего трафика по формуле ((MAC src) XOR (MAC dest)) % число интерфейсов. Режим дает балансировку нагрузки и отказоустойчивость. Не требуется дополнительной настройки коммутатора/коммутаторов.   
`Mode-3(broadcast) `– Происходит передача во все объединенные интерфейсы, тем самым обеспечивая отказоустойчивость. Рекомендуется только для использования MULTICAST трафика.   
`Mode-4(802.3ad)` – динамическое объединение одинаковых портов. В данном режиме можно значительно увеличить пропускную способность входящего так и исходящего трафика. Для данного режима необходима поддержка и настройка коммутатора/коммутаторов.   
`Mode-5(balance-tlb)` – Адаптивная балансировки нагрузки трафика. Входящий трафик получается только активным интерфейсом, исходящий распределяется в зависимости от текущей загрузки канала каждого интерфейса. Не требуется специальной поддержки и настройки коммутатора/коммутаторов.   
`Mode-6(balance-alb) `– Адаптивная балансировка нагрузки. Отличается более совершенным алгоритмом балансировки нагрузки чем Mode-5). Обеспечивается балансировку нагрузки как исходящего так и входящего трафика. Не требуется специальной поддержки и настройки коммутатора/коммутаторов.   
`sudo nano /etc/netplan/01-netcfg.yaml` заходим в конфиг   
```
network:
    version: 2
    renderer: networkd
    ethernets:
        ens2f0: {}
        ens2f1: {}
    bonds:
        bond0:
            dhcp4: no
            interfaces:
            - eth0
            - eth0.100
            parameters:
                mode: active-backup
            addresses:
                - 192.168.100.3/24
            gateway4: 192.168.100.1
            mtu: 1500
            nameservers:
                addresses:
                    - 8.8.8.8
                    - 77.88.8.8
```

### 5.  Сколько IP адресов в сети с маской /29 ? Сколько /29 подсетей можно получить из сети с маской /24. Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.
`8` адресов - в сети с маской `/29`   
Из сети `/24` можно получить `32` подсетей `/29`   
```
10.10.10.0/29
10.10.10.8/29
10.10.10.16/29
10.10.10.24/29
10.10.10.32/29
10.10.10.40/29
10.10.10.48/29
10.10.10.56/29
10.10.10.64/29
10.10.10.72/29
10.10.10.80/29
10.10.10.88/29
10.10.10.96/29
10.10.10.104/29
10.10.10.112/29
10.10.10.120/29
10.10.10.128/29
```
### 6. Задача: вас попросили организовать стык между 2-мя организациями. Диапазоны 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 уже заняты. Из какой подсети допустимо взять частные IP адреса? Маску выберите из расчета максимум 40-50 хостов внутри подсети.
`100.64.0.0/26 ` - 62 хоста   
```
vagrant@vagrant:~$ ipcalc -b --split 50 100.64.0.0
Address:   100.64.0.0
Netmask:   255.255.255.0 = 24
Wildcard:  0.0.0.255
=>
Network:   100.64.0.0/24
HostMin:   100.64.0.1
HostMax:   100.64.0.254
Broadcast: 100.64.0.255
Hosts/Net: 254                   Class A

1. Requested size: 50 hosts
Netmask:   255.255.255.192 = 26
Network:   100.64.0.0/26
HostMin:   100.64.0.1
HostMax:   100.64.0.62
Broadcast: 100.64.0.63
Hosts/Net: 62                    Class A

Needed size:  64 addresses.
Used network: 100.64.0.0/26
Unused:
100.64.0.64/26
100.64.0.128/25
```

### 7.  Как проверить ARP таблицу в Linux, Windows? Как очистить ARP кеш полностью? Как из ARP таблицы удалить только один нужный IP?
Linux (Ubuntu)   
`ip neighbour show` - Таблица ARP   
`sudo ip neighbour del [ip address] dev [interface]` - удалить из таблицы ARP только один нужный IP   
`sudo ip neighbour flush all` - очистка таблицы ARP   
```
vagrant@vagrant:~$ ip neighbour show
10.0.2.2 dev eth0 lladdr 52:54:00:12:35:02 DELAY
10.0.2.3 dev eth0 lladdr 52:54:00:12:35:03 STALE
```
Windows   
`arp –a` - Таблица ARP   
`arp -d *` - очистка таблицы ARP   
`arp -d [ip address]` - удалить из таблицы ARP только один нужный IP   
