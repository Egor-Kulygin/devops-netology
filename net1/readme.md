# Домашнее задание к занятию "3.6. Компьютерные сети, лекция 1"
### 1.	Работа c HTTP через телнет.
Подключитесь утилитой телнет к сайту stackoverflow.com telnet stackoverflow.com 80
```
vagrant@vagrant:~$ telnet stackoverflow.com 80
Trying 151.101.129.69...
Connected to stackoverflow.com.
Escape character is '^]'.
GET /questions HTTP/1.0
HOST: stackoverflow.com
HTTP/1.1 301 Moved Permanently
cache-control: no-cache, no-store, must-revalidate
location: https://stackoverflow.com/questions
x-request-guid: 71512326-0865-4a82-a65d-39de70fd006c
feature-policy: microphone 'none'; speaker 'none'
content-security-policy: upgrade-insecure-requests; frame-ancestors 'self' https://stackexchange.com
Accept-Ranges: bytes
Date: Mon, 23 May 2022 19:39:18 GMT
Via: 1.1 varnish
Connection: close
X-Served-By: cache-hel1410024-HEL
X-Cache: MISS
X-Cache-Hits: 0
X-Timer: S1653334758.138764,VS0,VE109
Vary: Fastly-SSL
X-DNS-Prefetch-Control: off
Set-Cookie: prov=dae37e52-2885-a127-6a2e-71a6d29123b4; domain=.stackoverflow.com; expires=Fri, 01-Jan-2055 00:00:00 GMT; path=/; HttpOnly

Connection closed by foreign host.
```
В ответе укажите полученный HTTP код, что он означает?
`HTTP/1.1 301 Moved Permanently` -  Код перенаправления "301 Moved Permanently" протокола передачи гипертекста (HTTP) показывает, что запрошенный ресурс был окончательно перемещён в URL, указанный в заголовке Location (en-US).

### 2.	Повторите задание 1 в браузере, используя консоль разработчика F12.
```
Request URL: http://stackoverflow.com/
Request method: GET
Status code: 307 Internal Redirect
Referrer policy: strict-origin-when-cross-origin
Cross-Origin-Resource-Policy: Cross-Origin
Location: https://stackoverflow.com/
Non-Authoritative-Reason: HSTS
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.67 Safari/537.36
```
Дольше всего грузился сам `stackoverflow.com`

### 3.	Какой IP адрес у вас в интернете?
```
vagrant@vagrant:~$ wget -qO- eth0.me
5.18.144.121
```
### 4. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS? Воспользуйтесь утилитой whois
`vagrant@vagrant:~$ sudo apt install whois` - устанавливаем whois
```
vagrant@vagrant:~$ whois -h whois.radb.net 5.18.144.121
route:          5.18.0.0/16
descr:          Z-Telecom  
origin:         AS41733
mnt-by:         ZTELECOM-MNT
mnt-by:         RAID-MNT
notify:         ripe@ertelecom.ru
created:        2012-04-27T12:43:39Z
last-modified:  2015-07-09T12:47:34Z
source:         RIPE
remarks:        ****************************
remarks:        * THIS OBJECT IS MODIFIED
remarks:        * Please note that all data that is generally regarded as personal
remarks:        * data has been removed from this object.
remarks:        * To view the original object, please query the RIPE Database at:
remarks:        * http://www.ripe.net/whois
remarks:        ****************************
```
` Z-Telecom ` - провайдер
` AS41733` -  автономная система
### 5.  Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS? Воспользуйтесь утилитой traceroute
` sudo apt install traceroute` установка traceroute
```
vagrant@vagrant:~$  traceroute -AnI 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  10.0.2.2 [*]  0.516 ms  0.422 ms  0.375 ms
 2  192.168.2.1 [*]  2.752 ms  2.707 ms  2.622 ms
 3  10.196.52.1 [*]  3.101 ms  3.039 ms  4.127 ms
 4  5.19.0.182 [AS41733]  4.069 ms  3.997 ms  4.500 ms
 5  188.234.131.158 [AS9049]  4.453 ms  4.394 ms  4.663 ms
 6  72.14.214.138 [AS15169]  4.607 ms  3.281 ms  2.960 ms
 7  172.253.76.91 [AS15169]  3.074 ms  6.359 ms  4.523 ms
 8  74.125.244.181 [AS15169]  5.543 ms  5.256 ms  8.191 ms
 9  72.14.232.84 [AS15169]  7.888 ms  7.218 ms  6.149 ms
10  142.251.61.219 [AS15169]  7.996 ms  7.778 ms  8.357 ms
11  142.250.238.181 [AS15169]  8.903 ms  8.683 ms  8.540 ms
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  8.8.8.8 [AS15169]  5.984 ms  5.940 ms  5.896 ms
```
### 6. Повторите задание 5 в утилите mtr. На каком участке наибольшая задержка - delay? 
```
vagrant@vagrant:~$ mtr -z -s 100 8.8.8.8
                                                My traceroute  [v0.93]
vagrant (10.0.2.15)                                                                          2022-05-26T22:43:03+0000
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                                                             Packets               Pings
 Host                                                                      Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. AS???    _gateway                                                       0.0%     5    1.1   1.1   0.4   2.5   0.8
 2. AS???    192.168.2.1                                                    0.0%     5    1.3   2.4   1.3   3.8   1.1
 3. AS???    10.196.52.1                                                    0.0%     5    4.0   6.2   2.7  14.2   4.6
 4. AS41733  5x19x0x182.static-business.spb.ertelecom.ru                    0.0%     5    2.8   4.5   2.3   8.2   2.3
 5. AS9049   net131.234.188-158.ertelecom.ru                                0.0%     5    3.3   4.0   3.2   6.9   1.6
 6. AS15169  72.14.214.138                                                  0.0%     5    3.7   3.8   2.9   4.9   0.8
 7. AS15169  172.253.76.91                                                  0.0%     5    5.5   3.8   3.1   5.5   1.0
 8. AS15169  74.125.244.181                                                 0.0%     5    3.7   4.0   3.2   5.1   0.9
 9. AS15169  72.14.232.84                                                   0.0%     5    4.1   4.4   3.6   6.2   1.1
10. AS15169  142.251.61.219                                                 0.0%     5    7.7   8.5   7.2  11.1   1.7
11. AS15169  142.250.238.181                                                0.0%     5    8.8   9.1   8.7  10.2   0.6
12. (waiting for reply)
13. (waiting for reply)
14. (waiting for reply)
15. (waiting for reply)
16. (waiting for reply)
17. (waiting for reply)
18. (waiting for reply)
19. (waiting for reply)
20. (waiting for reply)
21. AS15169  dns.google                                                     0.0%     4    8.6   7.5   6.3   8.7   1.2
```
Avg — среднее время задержки;
` AS???    10.196.52.1                                                    0.0%     5    4.0   6.2   2.7  14.2   4.6` самая большая задержка
### 7. Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи? воспользуйтесь утилитой dig
```
vagrant@vagrant:~$ dig +short NS dns.google
ns2.zdns.google.
ns4.zdns.google.
ns3.zdns.google.
ns1.zdns.google.
```
А запись
```
vagrant@vagrant:~$ dig +short A dns.google
8.8.8.8
8.8.4.4
```
### 8. Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP? воспользуйтесь утилитой dig
```
vagrant@vagrant:~$ dig -x 8.8.8.8
; <<>> DiG 9.16.1-Ubuntu <<>> -x 8.8.8.8
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 45236
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;8.8.8.8.in-addr.arpa.          IN      PTR
;; ANSWER SECTION:
8.8.8.8.in-addr.arpa.   5336    IN      PTR     dns.google.
;; Query time: 0 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Thu May 26 22:56:12 UTC 2022
;; MSG SIZE  rcvd: 73
```
`8.8.8.8.in-addr.arpa.   5336    IN      PTR     dns.google.` - итог

Для второго ip
```
vagrant@vagrant:~$ dig -x 8.8.4.4
; <<>> DiG 9.16.1-Ubuntu <<>> -x 8.8.4.4
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 24530
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;4.4.8.8.in-addr.arpa.          IN      PTR
;; ANSWER SECTION:
4.4.8.8.in-addr.arpa.   7013    IN      PTR     dns.google.
;; Query time: 0 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Thu May 26 22:57:27 UTC 2022
;; MSG SIZE  rcvd: 73
```

`4.4.8.8.in-addr.arpa.   7013    IN      PTR     dns.google.` - итог
