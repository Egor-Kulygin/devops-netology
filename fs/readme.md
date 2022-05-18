# Домашнее задание к занятию "3.5. Файловые системы"
### 1.	Узнайте о sparse (разряженных) файлах.
По факту это выделяемое место, на физическом диске, которое «забронировано» под определённый файл. И может дополняться по мере поступления данных. Также свободное пространство внутри этого файла может быть использовано для фактической записи других файлов. Применяется в torrent, виртуализации и т.д
### 2.	Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?
Жесткая ссылка это ссылка на тот же самый файл, имеет одинаковый `inode` права будут идентичны.
### 3.	Сделайте vagrant destroy на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим: 
```
vagrant destroy
```
### 4. Используя fdisk, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.
```
Command (m for help): n «входим в подпрограмму добавления разделов»
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1  «Добавляем первый раздел»
First sector (2048-5242879, default 2048): 2048 «выбираем первый доступный сектор»
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-5242879, default 5242879): +2G «от «сектора» добавляем +2 гигабайта»
Created a new partition 1 of type 'Linux' and of size 2 GiB.
Command (m for help): n 
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2-4, default 2): 2 «добавляем второй раздел» 
First sector (4196352-5242879, default 4196352): 4196352 «выбираем первый доступный сектор»
Last sector, +/-sectors or +/-size{K,M,G,T,P} (4196352-5242879, default 5242879): 5242879 «до последнего»
Created a new partition 2 of type 'Linux' and of size 511 MiB.
Command (m for help): w «записываем изменения на диск»
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```
Можно еще раз зайти в `fdisk` и командой `p` вывести наши созданные разделы
```
Device     Boot   Start     End Sectors  Size Id Type
/dev/sdb1          2048 4196351 4194304    2G 83 Linux
/dev/sdb2       4196352 5242879 1046528  511M 83 Linux
```
### 5.  Используя sfdisk, перенесите данную таблицу разделов на второй диск. 
Можем сделать полный дамп устройсва атрибутом `-d`
```
vagrant@vagrant:~$ sudo sfdisk -d /dev/sdb | sudo sfdisk /dev/sdc
Checking that no-one is using this disk right now ... OK
Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Created a new DOS disklabel with disk identifier 0xb9053470.
/dev/sdc1: Created a new partition 1 of type 'Linux' and of size 2 GiB.
/dev/sdc2: Created a new partition 2 of type 'Linux' and of size 511 MiB.
/dev/sdc3: Done.
New situation:
Disklabel type: dos
Disk identifier: 0xb9053470
Device     Boot   Start     End Sectors  Size Id Type
/dev/sdc1          2048 4196351 4194304    2G 83 Linux
/dev/sdc2       4196352 5242879 1046528  511M 83 Linux
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```
Проверяем 
```
vagrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0 70.3M  1 loop /snap/lxd/21029
loop1                       7:1    0 55.4M  1 loop /snap/core18/2128
loop3                       7:3    0 55.5M  1 loop /snap/core18/2409
loop4                       7:4    0 44.7M  1 loop /snap/snapd/15534
loop5                       7:5    0 61.9M  1 loop /snap/core20/1434
loop6                       7:6    0 67.8M  1 loop /snap/lxd/22753
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm  /
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
└─sdb2                      8:18   0  511M  0 part
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
└─sdc2                      8:34   0  511M  0 part
```
### 6. Соберите mdadm RAID1 на паре разделов 2 Гб. 
vagrant@vagrant:~$ sudo mdadm -C /dev/md1 -l 1 -n 2 /dev/sdb1 /dev/sdc1
mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md1 started.
### 7.  Соберите mdadm RAID0 на второй паре маленьких разделов.
```
vagrant@vagrant:~$ sudo mdadm -C /dev/md0 -l 0 -n 2 /dev/sdb2 /dev/sdc2
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
vagrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 70.3M  1 loop  /snap/lxd/21029
loop1                       7:1    0 55.4M  1 loop  /snap/core18/2128
loop3                       7:3    0 55.5M  1 loop  /snap/core18/2409
loop4                       7:4    0 44.7M  1 loop  /snap/snapd/15534
loop5                       7:5    0 61.9M  1 loop  /snap/core20/1434
loop6                       7:6    0 67.8M  1 loop  /snap/lxd/22753
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part  /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
│ └─md1                     9:1    0    2G  0 raid1
└─sdb2                      8:18   0  511M  0 part
  └─md0                     9:0    0 1018M  0 raid0
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
│ └─md1                     9:1    0    2G  0 raid1
└─sdc2                      8:34   0  511M  0 part
  └─md0                     9:0    0 1018M  0 raid0
```
### 8. Создайте 2 независимых PV на получившихся md-устройствах.
Инициализируем Raid разделы 
```
vagrant@vagrant:~$ sudo pvcreate /dev/md1 /dev/md0
  Physical volume "/dev/md1" successfully created.
  Physical volume "/dev/md0" successfully created.
```
### 9. Создайте общую volume-group на этих двух PV.
```
vagrant@vagrant:~$ sudo vgcreate VolGroup /dev/md0 /dev/md1
  Volume group "VolGroup" successfully created
vagrant@vagrant:~$ sudo vgscan
  Found volume group "ubuntu-vg" using metadata type lvm2
  Found volume group "VolGroup" using metadata type lvm2
```
### 10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.
```
vagrant@vagrant:~$ sudo lvcreate -L 100M VolGroup /dev/md0
  Logical volume "lvol0" created.
vagrant@vagrant:~$ sudo lvs
  LV        VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lvol0     VolGroup  -wi-a----- 100.00m
  ubuntu-lv ubuntu-vg -wi-ao----  31.50g
```
### 11. Создайте mkfs.ext4 ФС на получившемся LV.
```
vagrant@vagrant:~$ sudo mkfs.ext4 /dev/VolGroup/lvol0
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 25600 4k blocks and 25600 inodes
Allocating group tables: done
Writing inode tables: done
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done
```
### 12. Смонтируйте этот раздел в любую директорию, например, /tmp/new.
```
vagrant@vagrant:~$ mkdir /tmp/new
vagrant@vagrant:~$ sudo mount /dev/VolGroup/lvol0 /tmp/new
```
### 13. Поместите туда тестовый файл, например `wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz`
```
vagrant@vagrant:~$ sudo wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz
--2022-05-18 19:56:57--  https://mirror.yandex.ru/ubuntu/ls-lR.gz
Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183, 2a02:6b8::183
Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 23249233 (22M) [application/octet-stream]
Saving to: ‘/tmp/new/test.gz.’
/tmp/new/test.gz.             100%[=================================================>]  22.17M  11.0MB/s    in 2.0s
2022-05-18 19:56:59 (11.0 MB/s) - ‘/tmp/new/test.gz.’ saved [23249233/23249233]
```
### 14. Прикрепите вывод lsblk.
```
vagrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 70.3M  1 loop  /snap/lxd/21029
loop1                       7:1    0 55.4M  1 loop  /snap/core18/2128
loop3                       7:3    0 55.5M  1 loop  /snap/core18/2409
loop4                       7:4    0 44.7M  1 loop  /snap/snapd/15534
loop5                       7:5    0 61.9M  1 loop  /snap/core20/1434
loop6                       7:6    0 67.8M  1 loop  /snap/lxd/22753
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part  /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
│ └─md1                     9:1    0    2G  0 raid1
└─sdb2                      8:18   0  511M  0 part
  └─md0                     9:0    0 1018M  0 raid0
    └─VolGroup-lvol0      253:1    0  100M  0 lvm   /tmp/new
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
│ └─md1                     9:1    0    2G  0 raid1
└─sdc2                      8:34   0  511M  0 part
  └─md0                     9:0    0 1018M  0 raid0
    └─VolGroup-lvol0      253:1    0  100M  0 lvm   /tmp/new
```
### 15. Протестируйте целостность файла:
`gzip -t /tmp/new/test.gz | echo $?`
```
vagrant@vagrant:~$ gzip -t /tmp/new/test.gz
vagrant@vagrant:~$ echo $?
0
```
### 16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1
```
sudo pvmove /dev/md0 /dev/md1
```
### 17. Сделайте --fail на устройство в вашем RAID1 md
```
vagrant@vagrant:~$ sudo mdadm /dev/md1 --fail /dev/sdb1
mdadm: set /dev/sdb1 faulty in /dev/md1
```
### 18. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.
```
vagrant@vagrant:~$ dmesg | grep md1
[11696.738224] md/raid1:md1: not clean -- starting background reconstruction
[11696.738226] md/raid1:md1: active with 2 out of 2 mirrors
[11696.738277] md1: detected capacity change from 0 to 2144337920
[11696.738432] md: resync of RAID array md1
[11707.350315] md: md1: resync done.
[23969.604448] md/raid1:md1: Disk failure on sdb1, disabling device.
               md/raid1:md1: Operation continuing on 1 devices.
```
### 19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:
```
 vagrant@vagrant:~$ gzip -t /tmp/new/test.gz
vagrant@vagrant:~$ echo $?
0
```
### 20.  Погасите тестовый хост, vagrant destroy.
```
vagrant@vagrant:~$ exit
logout
Connection to 127.0.0.1 closed.
C:\VirtualBox\netology>vagrant destroy
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Forcing shutdown of VM...
==> default: Destroying VM and associated drives...
C:\VirtualBox\netology>
```
