# Домашнее задание к занятию "3.9. Элементы безопасности информационных систем"
### 1.	Установите Bitwarden плагин для браузера. Зарегестрируйтесь и сохраните несколько паролей.
https://vault.bitwarden.com/
установлено в браузере
### 2.	Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.
### 3.	Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.
`sudo apt install apache2`
```
vagrant@vagrant:~$ sudo systemctl status apache2
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2022-10-02 22:00:45 UTC; 29s ago
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 2809 (apache2)
      Tasks: 55 (limit: 1071)
     Memory: 5.8M
     CGroup: /system.slice/apache2.service
             ├─2809 /usr/sbin/apache2 -k start
             ├─2810 /usr/sbin/apache2 -k start
             └─2811 /usr/sbin/apache2 -k start
vagrant@vagrant:~$ sudo openssl req -x509 -nodes -days 365
Generating a RSA private key
.......................................................................+++++
.............+++++
writing new private key to 'privkey.pem'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:RU
State or Province Name (full name) [Some-State]:SPB
Locality Name (eg, city) []:SPB
Organization Name (eg, company) [Internet Widgits Pty Ltd]:netology
Organizational Unit Name (eg, section) []:netology
Common Name (e.g. server FQDN or YOUR name) []:192.168.1.10
Email Address []:elfunnamed@list.ru
-----BEGIN CERTIFICATE-----
MIID5TCCAs2gAwIBAgIUQguZIqN8BeYhnVkwvZf5Q7PuC+8wDQYJKoZIhvcNAQEL
BQAwgYExCzAJBgNVBAYTAlJVMQwwCgYDVQQIDANTUEIxDDAKBgNVBAcMA1NQQjER
MA8GA1UECgwIbmV0b1xvZ3kxETAPBgNVBAsMCG5ldG9sb2d5MQ0wCwYDVQQDDARF
Z29yMSEwHwYJKoZIhvcNAQkBFhJlbGZ1bm5hbWVkQGxpc3QucnUwHhcNMjIxMDAy
MjIwNzIwWhcNMjMxMDAyMjIwNzIwWjCBgTELMAkGA1UEBhMCUlUxDDAKBgNVBAgM
A1NQQjEMMAoGA1UEBwwDU1BCMREwDwYDVQQKDAhuZXRvbG9neTERMA8GA1UECwwI
bmV0b2xvZ3kxDTALBgNVBAMMBEVnb3IxITAfBgkqhkiG9w0BCQEWEmVsZnVubmFt
ZWRAbGlzdC5ydTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKuM9yh0
V0YaGQd2I1igWnJx/RuD5cjsMiHMnInyU8skelZmijWRHcN7w1jRmngvmg3G1BAX
T20ltclPqu34PB5YQJmx3+7F1+8HzH422YBAlnmMWZhOdMq1ZLzPR0GU+js6uQuf
W5NSAugT8iiNbUrx83GoQCADACMa1DpmMxZIJnVat+r9RKYMKDhNMGcWpb+kRCH+
vRSRaOSpPbUQFcuaPJwcBSfMzTcD1ci0MC6UApP/7D5ndqitOiD7kqneyjoZPxuB
dDRguDvEYu/yBn2eySjERBrav6hfX09ul6pbuoJ2Nl4VMdOCIS1FDHeFwsp8vrdA
Fj6V/oJTkai6CsUCAwEAAaNTMFEwHQYDVR0OBBYEFDY+H0J9/FjJwtQGbUt7b7TO
IgbKMB8GA1UdIwQYMBaAFDY+H0J9/FjJwtQGbUt7b7TOIgbKMA8GA1UdEwEB/wQF
MAMBAf8wDQYJKoZIhvcNAQELBQADggEBAGaTm/Qxfj4XEfqZ5xg1xNZf1k6FwnqH
4mUIGBmxBlWKM12SfDIu4ldt7URKDdU/39PDMsUWkiorGzBom7PoDgqulsojcDIZ
5q+PQWtK+AenBXuxankNzfya/M/tQpdbIn58WJjtcWwmA9FY/0R/GTIA5knjBU/Z
AspZr+oDIv+UfkNDp+GE4l7zcya+bpefr6OeLEvbCfdjlKKFj7ckUKDRxStXR4kC
mU6As1ihzA3O8zkYDOmiWeWwj5BV7goFtCIqTPrmqc98OAMM3+oOBHEABDazauPo
06Rxjv2+ricQm0kI7zqHJaRtzhXo5YWhAmRVVm+/TzIxfKz69GHwJTg=
-----END CERTIFICATE-----
vagrant@vagrant:~$  sudo nano /etc/apache2/sites-available/192.168.1.10.conf

<VirtualHost *:443>
   ServerName 1192.168.1.10
   DocumentRoot /var/www/192.168.1.10

   SSLEngine on
   SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
   SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
</VirtualHost>

vagrant@vagrant:~$ sudo mkdir /var/www/192.168.1.10
vagrant@vagrant:~$ sudo nano /var/www/192.168.1.10/index.html
vagrant@vagrant:~$ sudo a2ensite 192.168.1.10.conf
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.2.1. Set the 'ServerName' directive globally to suppress this message
Syntax OK
vagrant@vagrant:~$ sudo systemctl reload apache2

```




### 4. Проверьте на TLS уязвимости произвольный сайт в интернете (кроме сайтов МВД, ФСБ, МинОбр, НацБанк, РосКосмос, РосАтом, РосНАНО и любых госкомпаний, объектов КИИ, ВПК ... и тому подобное).
`sudo apt install testssl`
```
vagrant@vagrant:~/testssl.sh$ ./testssl.sh -U --sneaky https://netology.ru/
 Testing vulnerabilities

 Heartbleed (CVE-2014-0160)                not vulnerable (OK), no heartbeat extension
 CCS (CVE-2014-0224)                       not vulnerable (OK)
 Ticketbleed (CVE-2016-9244), experiment.  not vulnerable (OK), no session tickets
 ROBOT                                     not vulnerable (OK)
 Secure Renegotiation (RFC 5746)           OpenSSL handshake didn't succeed
 Secure Client-Initiated Renegotiation     not vulnerable (OK)
 CRIME, TLS (CVE-2012-4929)                not vulnerable (OK)
```

### 5.  Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.
```
vagrant@vagrant:~$ systemctl status sshd.service
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2022-10-02 19:39:13 UTC; 2h 51min ago
       Docs: man:sshd(8)
             man:sshd_config(5)
   Main PID: 740 (sshd)
      Tasks: 1 (limit: 1071)
     Memory: 5.4M
     CGroup: /system.slice/ssh.service
             └─740 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups

Oct 02 19:39:10 vagrant systemd[1]: Starting OpenBSD Secure Shell server...
Oct 02 19:39:13 vagrant sshd[740]: Server listening on 0.0.0.0 port 22.
Oct 02 19:39:13 vagrant sshd[740]: Server listening on :: port 22.
Oct 02 19:39:13 vagrant systemd[1]: Started OpenBSD Secure Shell server.
Oct 02 19:41:19 vagrant sshd[1054]: Accepted publickey for vagrant from 10.0.2.2 port 62474 ssh2: RSA SHA256:TLF6nlNnrd/yCEZYawnP9hJz6aysiLUQiOQKSreAdKA
Oct 02 19:41:19 vagrant sshd[1054]: pam_unix(sshd:session): session opened for user vagrant by (uid=0)
vagrant@vagrant:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/vagrant/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/vagrant/.ssh/id_rsa
Your public key has been saved in /home/vagrant/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:Q+X9PT2Pb1zWNTfD86VzYoL3CDqDiqHj/jaCjRaVtZE vagrant@vagrant
The key's randomart image is:
+---[RSA 3072]----+
|      .   .      |
|     E   o .     |
|    o o . . . .  |
|   o . .     . B=|
|  .     S  .  .+&|
| .       .o o =.X|
|.oo    . . o =.*o|
|++.=  . +   . ..o|
|=+=.o.   o     ..|
+----[SHA256]-----+
vagrant@vagrant:~$ ssh-copy-id vagrant@192.168.1.10

/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/vagrant/.ssh/id_rsa.pub"
The authenticity of host '192.168.1.10 (192.168.1.10)' can't be established.
ECDSA key fingerprint is SHA256:/rZjeIEeI4GXObGdc6iTj7LjwLh3TA1bwOPXEHLrkaM.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
vagrant@192.168.1.10's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@192.168.1.10'"
and check to make sure that only the key(s) you wanted were added.

vagrant@linux1:~$ ssh vagrant@192.168.1.10
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun 02 Oct 2022 07:41:19 PM UTC

  System load:  0.14               Processes:             130
  Usage of /:   12.8% of 30.88GB   Users logged in:       0
  Memory usage: 20%                IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%                IPv4 address for eth1: 192.168.1.10
  ```
### 6.  Переименуйте файлы ключей из задания 5. Настройте файл конфигурации SSH клиента, так чтобы вход на удаленный сервер осуществлялся по имени сервера.
```
vagrant@vagrant:~$ mv /home/vagrant/.ssh/id_rsa /home/vagrant/.ssh/wagrant2_rsa
vagrant@vagrant:~$ touch ~/.ssh/config && chmod 600 ~/.ssh/config
vagrant@vagrant:~$ nano .ssh/config

Host vagrant 2
     HostName 192.168.1.10
     User vagrant
     IdentityFile ~/.ssh/vagrant2_rsa
     
vagrant@ vagrant:~$ ssh vagrant2
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun 02 Oct 2022 08:22:12 PM UTC

  System load:  0.14               Processes:             130
  Usage of /:   14.3% of 30.88GB   Users logged in:       0
  Memory usage: 20%                IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%                IPv4 address for eth1: 192.168.1.10

```
### 7.  Соберите дамп трафика утилитой tcpdump в формате pcap, 100 пакетов. Откройте файл pcap в Wireshark.
```
vagrant@ vagrant:~$ sudo tcpdump -c 100 -w test100.pcap -i eth1
tcpdump: listening on eth1, link-type EN10MB (Ethernet), snapshot length 262144 bytes
100 packets captured
117 packets received by filter
0 packets dropped by kernel
```

