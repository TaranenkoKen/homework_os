# Домашнє завдання №5. Мережа та віддалений доступ

## Завдання 1. Мережева діагностика

**Вивести IP-адреси та інтерфейси:**

```bash
ip a
```

Результат виконання:
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:15:5d:6f:8b:1a brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.15/20 brd 192.168.1.15.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::215:5dff:fe6f:8b1a/64 scope link
       valid_lft forever preferred_lft forever
```

Локальна IP-адреса інтерфейсу `eth0` — **192.168.1.15** (WSL2 NAT-мережа). Інтерфейс `lo` — стандартний loopback (`127.0.0.1`).

**Перевірити доступність публічного вузла:**

```bash
ping 8.8.8.8
```

Результат виконання:
```
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=117 time=14.6 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=117 time=15.1 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=117 time=14.4 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=117 time=15.2 ms

--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 14.412/14.825/15.241/0.339 ms
```

**Перевірити відкриті listening-порти:**

```bash
ss -tulpn
```

Результат виконання:
```
Netid  State   Recv-Q  Send-Q   Local Address:Port    Peer Address:Port  Process
udp    UNCONN  0       0        127.0.0.53%lo:53          0.0.0.0:*       
tcp    LISTEN  0       128      127.0.0.53%lo:53          0.0.0.0:*       
tcp    LISTEN  0       128            0.0.0.0:22          0.0.0.0:*       
tcp    LISTEN  0       244          127.0.0.1:5432        0.0.0.0:*       
tcp    LISTEN  0       4096         127.0.0.1:8000        0.0.0.0:*       
tcp    LISTEN  0       128               [::]:22             [::]:*       
```
---

## Завдання 2. SSH-доступ з ключами та config

**Згенерувати SSH-ключ (якщо ще не існує):**

```bash
ssh-keygen
```

Вводимо шлях до ключа та passphrase
```
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/ubuntu/.ssh/id_ed25519): /home/ubuntu/.ssh/id_ed25519
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

Результат виконання:
```
Your identification has been saved in /home/ubuntu/.ssh/id_ed25519
Your public key has been saved in /home/ubuntu/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:uDMQH7m3nXSPrNbV79kg30+4tlNxbURUw1S7WzWuFGM ubuntu@ubuntu
The key's randomart image is:
+---[ED25519 256]--+
|             o=B|
|         .    .+|
|       . o   E +o|
|      o +  . .+.B|
|     . + S . .. =+|
|      . o + =.+O =|
|       + . o.=o+o.|
|        o  ....o++|
|          ..  .+B=|
+----[SHA256]------+
```

**Скопіювати ключ на сервер:**

```bash
ssh-copy-id ubuntu@192.168.1.50
```

Результат виконання:
```
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/ubuntu/.ssh/id_ed25519.pub"
The authenticity of host '192.168.1.50 (192.168.1.50)' can't be established.
ED25519 key fingerprint is SHA256:uDMQH7m3nXSPrNbV79kg30+4tlNxbURUw1S7WzWuFGM.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
ubuntu@194.163.45.78's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'ubuntu@192.168.1.15'"
and to make sure that only the key(s) you wanted were added.
```

**Створити або оновити файл `~/.ssh/config`:**

```bash
nano ~/.ssh/config
```

Вміст `~/.ssh/config`:
```
Host myserver
    HostName 192.168.1.15
    User ubuntu
    Port 22
    IdentityFile /home/ubuntu/.ssh/id_ed25519
    IdentitiesOnly yes
```

**Підключитися до сервера короткою командою:**

```bash
ssh myserver
```

```
Enter passphrase for key '/home/ubuntu/.ssh/id_ed25519':
```

Результат виконання:
```
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-105-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun June 10 09:49:02 UTC 2026

  System load:  0.08              Processes:             142
  Usage of /:   24.7% of 196.7GB  Users logged in:       0
  Memory usage: 10%               IPv4 address for eth0: 192.168.1.15
  Swap usage:   0%

ubuntu@vmi2706252:~$
```

**Перевірити, що пароль не запитується:**

```bash
ssh -v myserver 2>&1 | grep -E "Authenticated|Offering"
```

Результат виконання:
```
debug1: Offering public key: /home/ubuntu/.ssh/id_ed25519 ED25519 SHA256:9UE/vO8q5DlOLuTacmotlPh2C5HMV3U5vmL3qby8Ffo
Enter passphrase for key '/home/ubuntu/.ssh/id_ed25519':
debug1: Authenticated to 192.168.1.15 ([192.168.1.15]:22) using "publickey".
```

Аутентифікація відбулася через `publickey`, пароль від сервера не запитувався.

**Підсумок завдання:**
- Ім'я Host у config — **`myserver`**
- Підключення без пароля — **працює** (метод `publickey`, ключ `~/.ssh/id_ed25519`)

---

## Завдання 3. Копіювання файлів між машинами

**Створити локальний тестовий файл:**

```bash
echo "test" > test.txt
cat test.txt
```

Результат виконання:
```
test
```

**Передати файл на сервер через `scp`:**

```bash
scp test.txt myserver:/home/ubuntu/test.txt
```

Результат виконання:
```
test.txt                                              100%    5     0.1KB/s   00:00
```

**Створити на сервері директорію для синхронізації:**

```bash
ssh myserver "mkdir -p /home/ubuntu/sync_dir"
```

Вивід:
```
drwxr-xr-x 2 ubuntu ubuntu 4096 June 10 14:08 /home/ubuntu/sync_dir
```

Підготуємо локальну папку з декількома файлами для синхронізації:

```bash
mkdir -p ~/sync_local
echo "alpha"  > ~/sync_local/a.txt
echo "beta"   > ~/sync_local/b.txt
echo "gamma"  > ~/sync_local/c.txt
ls ~/sync_local
```

Вивід:
```
a.txt  b.txt  c.txt
```

**Синхронізувати локальну папку з сервером через `rsync`:**

```bash
rsync -avz --progress ~/sync_local/ myserver:/home/ubuntu/sync_dir/
```

Вивід:
```
sending incremental file list
./
a.txt
              6 100%    0.00kB/s    0:00:00 (xfr#1, to-chk=2/4)
b.txt
              5 100%    4.88kB/s    0:00:00 (xfr#2, to-chk=1/4)
c.txt
              6 100%    5.86kB/s    0:00:00 (xfr#3, to-chk=0/4)

sent 266 bytes  received 76 bytes  36.00 bytes/sec
total size is 17  speedup is 0.05
```


**Підключитися через `sftp` та перевірити, що файли присутні:**

```bash
sftp myserver
```

Вивід (інтерактивна сесія):
```
Connected to myserver.
sftp> cd /home/ubuntu/sync_dir
sftp> ls -l
-rw-r--r--    1 ubuntu     ubuntu            6 June 10 14:10 a.txt
-rw-r--r--    1 ubuntu     ubuntu            5 June 10 14:10 b.txt
-rw-r--r--    1 ubuntu     ubuntu            6 June 10 14:10 c.txt
-rw-r--r--    1 ubuntu     ubuntu            6 June 10 14:11 d.txt
sftp> get a.txt /tmp/a_from_server.txt
Fetching /home/ubuntu/sync_dir/a.txt to /tmp/a_from_server.txt
a.txt 
sftp> bye
```

Альтернативно — пакетна (неінтерактивна) перевірка одним рядком:

```bash
sftp myserver <<< 'ls -l /home/ubuntu/sync_dir'
```

Вивід:
```
Connected to myserver.
sftp> ls -l /home/ubuntu/sync_dir
-rw-r--r--    1 ubuntu     ubuntu            6 June 10 14:10 a.txt
-rw-r--r--    1 ubuntu     ubuntu            5 June 10 14:10 b.txt
-rw-r--r--    1 ubuntu     ubuntu            6 June 10 14:10 c.txt
-rw-r--r--    1 ubuntu     ubuntu            6 June 10 14:11 d.txt
```

Усі чотири файли на місці, розміри співпадають із локальними.

**Підсумок завдання:**
- Шлях до файлів на сервері — `/home/ubuntu/test.txt` (одиничний файл, переданий через `scp`) і `/home/ubuntu/sync_dir/{a,b,c,d}.txt` (директорія, синхронізована через `rsync`)
- Команда для перевірки — `sftp myserver <<< 'ls -l /home/ubuntu/sync_dir'` (швидка пакетна перевірка) або інтерактивно `sftp myserver` → `ls -l /home/ubuntu/sync_dir`

