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
<img width="1215" height="705" alt="image" src="https://github.com/user-attachments/assets/4085747f-1c49-47d4-9e31-f90cb2725a47" />
<img width="1215" height="639" alt="image" src="https://github.com/user-attachments/assets/50d7b042-ff93-4328-8239-3f1f53cc678e" />


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
<img width="1214" height="218" alt="image" src="https://github.com/user-attachments/assets/0acfce6f-b766-4aa0-bb48-999a9384a8fa" />


## Завдання 3. Права доступу

```bash
ls -l file.txt

chmod 644 file.txt

chmod u+w file.txt

umask

umask 022
```

Результат:
<img width="1210" height="199" alt="image" src="https://github.com/user-attachments/assets/1a297e29-4518-45bb-835f-5ef0cedd9fbf" />




## Завдання 4. Користувачі

```bash
sudo useradd -m trainee

sudo usermod -aG sudo trainee

getent passwd trainee
```

Результат:
<img width="1228" height="153" alt="image" src="https://github.com/user-attachments/assets/4123b7e4-a309-4a1e-9cfa-d9e298fb0d6f" />
<img width="1211" height="44" alt="image" src="https://github.com/user-attachments/assets/704350fd-019e-497e-affa-b573bf2a17b6" />



