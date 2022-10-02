# Домашнее задание к занятию "3.8. Компьютерные сети, лекция 3"
### 1.	Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP
```
vagrant@vagrant:~$ telnet route-views.routeviews.org
Trying 128.223.51.103...
Connected to route-views.routeviews.org.
Username: rviews
route-views>show ip route 5.18.147.121
Routing entry for 5.18.147.0/24
  Known via "bgp 6447", distance 20, metric 0
  Tag 6939, type external
  Last update from 64.71.137.241 6w3d ago
  Routing Descriptor Blocks:
  * 64.71.137.241, from 64.71.137.241, 6w3d ago
      Route metric is 0, traffic share count is 1
      AS Hops 3
      Route tag 6939
      MPLS label: none
route-views>show bgp 5.18.147.121
BGP routing table entry for 5.18.147.0/24, version 2388122190
Paths: (22 available, best #13, table default)
  Not advertised to any peer
  Refresh Epoch 1
  3267 9049 41733
    194.85.40.15 from 194.85.40.15 (185.141.126.1)
      Origin incomplete, metric 0, localpref 100, valid, external
      path 7FE00A184098 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  57866 1299 9049 41733
    37.139.139.17 from 37.139.139.17 (37.139.139.17)
      Origin IGP, metric 0, localpref 100, valid, external
      Community: 1299:30000 57866:100 65100:1299 65103:3 65104:31
      unknown transitive attribute: flag 0xE0 type 0x20 length 0x30
        value 0000 E20A 0000 0064 0000 0513 0000 E20A
              0000 0065 0000 0064 0000 E20A 0000 0067
              0000 0003 0000 E20A 0000 0068 0000 001F

      path 7FE11728BDF0 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3333 1257 1299 9049 41733
    193.0.0.56 from 193.0.0.56 (193.0.0.56)
      Origin incomplete, localpref 100, valid, external
      Community: 1257:50 1257:51 1257:2000 1257:3528 1257:4103
      path 7FE09D67FA58 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  4901 6079 1299 9049 41733
    162.250.137.254 from 162.250.137.254 (162.250.137.254)
      Origin incomplete, localpref 100, valid, external
      Community: 65000:10100 65000:10300 65000:10400
      path 7FE03C119890 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
---
And more
```
### 2.	Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.
```
vagrant@vagrant:~$ sudo modprobe -v dummy numdummies=2
insmod /lib/modules/5.4.0-91-generic/kernel/drivers/net/dummy.ko numdummies=0 numdummies=2
vagrant@vagrant:~$ sudo ip addr add 192.168.20.10/24 dev dummy0
vagrant@vagrant:~$ ip a show type dummy
3: dummy0: <BROADCAST,NOARP> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 96:b9:bf:82:1c:0f brd ff:ff:ff:ff:ff:ff
    inet 192.168.20.10/24 scope global dummy0
       valid_lft forever preferred_lft forever
4: dummy1: <BROADCAST,NOARP> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether f2:f5:a7:38:2a:c9 brd ff:ff:ff:ff:ff:ff 
vagrant@vagrant:~$ sudo ip route add 192.168.10.0/24 via 10.0.2.1
vagrant@vagrant:~$ sudo ip route add 192.168.10.0/24 dev eth0
vagrant@vagrant:~$ ip -br route
default via 10.0.2.2 dev eth0 proto dhcp src 10.0.2.15 metric 100
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15
10.0.2.2 dev eth0 proto dhcp scope link src 10.0.2.15 metric 100
192.168.10.0/24 via 10.0.2.1 dev eth0
192.168.20.0/24 dev dummy0 proto kernel scope link src 192.168.20.10
```
### 3.	Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров.
```
vagrant@vagrant:~$ sudo ss -ltpn
State    Recv-Q   Send-Q      Local Address:Port       Peer Address:Port   Process
LISTEN   0        4096        127.0.0.53%lo:53              0.0.0.0:*       users:(("systemd-resolve",pid=663,fd=13))
LISTEN   0        128               0.0.0.0:22              0.0.0.0:*       users:(("sshd",pid=740,fd=3))
LISTEN   0        128                  [::]:22                 [::]:*       users:(("sshd",pid=740,fd=4))```

```
Dns - `53`
Ssh - `22`
### 4. Проверьте используемые UDP сокеты в Ubuntu, какие протоколы и приложения используют эти порты?
```
vagrant@vagrant:~$ sudo ss -lupn
State    Recv-Q   Send-Q      Local Address:Port       Peer Address:Port   Process
UNCONN   0        0           127.0.0.53%lo:53              0.0.0.0:*       users:(("systemd-resolve",pid=663,fd=12))
UNCONN   0        0          10.0.2.15%eth0:68              0.0.0.0:*       users:(("systemd-network",pid=661,fd=19))
```

### 5.  Используя diagrams.net, создайте L3 диаграмму вашей домашней сети или любой другой сети, с которой вы работали.
https://github.com/Egor-Kulygin/devops-netology/blob/main/Net3/1.png

