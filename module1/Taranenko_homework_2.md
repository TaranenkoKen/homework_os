# Домашнє завдання №2. Файлова система і права доступу

## Завдання 1. Ієрархія каталогів Linux
```bash
cd /
ls

cd /etc
ls

cd /home
ls
```

Результат:
![alt text](image-1.png)
![alt text](image-2.png)

## Завдання 2. Файли, каталоги та посилання

```bash
mkdir ~/lab2

cd ~/lab2

touch file.txt

cp file.txt file_copy.txt

mv file_copy.txt renamed.txt

ln file.txt file_hardlink.txt

ln -s file.txt file_softlink.txt

find /home -name "file.txt"
```

Результат:
![alt text](image-3.png)

## Завдання 3. Права доступу

```bash
ls -l file.txt

chmod 644 file.txt

chmod u+w file.txt

umask

umask 022
```

Результат:
![alt text](image-4.png)


## Завдання 4. Користувачі

```bash
sudo useradd -m trainee

sudo usermod -aG sudo trainee

getent passwd trainee
```

Результат:
![alt text](image-5.png)
![alt text](image-6.png)

