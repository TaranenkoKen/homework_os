# Домашнє завдання №6. Bash-скрипт бекапу логів

## Скрипт `backup.sh`

### Код скрипта backup.sh

```bash
#!/bin/bash

# Перевірка кількості аргументів
if [ "$#" -ne 2 ]; then
    echo "Usage: ./backup.sh <log_dir> <backup_dir>"
    exit 1
fi

LOG_DIR="$1"
BACKUP_DIR="$2"

# Перевірка існування каталогів
if [ ! -d "$LOG_DIR" ] || [ ! -d "$BACKUP_DIR" ]; then
    echo "Usage: ./backup.sh <log_dir> <backup_dir>"
    exit 1
fi

LOCK_FILE="/tmp/backup.lock"

# Захист від паралельного запуску
if [ -e "$LOCK_FILE" ]; then
    echo "Backup already running"
    exit 1
fi

# Створення lock-файлу
touch "$LOCK_FILE"

# Видалення lock-файлу при завершенні скрипта
trap "rm -f $LOCK_FILE" EXIT

# Формування імені архіву
TIMESTAMP=$(date +"%Y-%m-%d_%H-%M")
ARCHIVE_NAME="logs_backup_${TIMESTAMP}.tar.gz"
ARCHIVE_PATH="${BACKUP_DIR}/${ARCHIVE_NAME}"

# Створення архіву
tar -czf "$ARCHIVE_PATH" -C "$LOG_DIR" .

# Перевірка результату
if [ $? -ne 0 ]; then
    echo "Backup failed"
    exit 2
fi

echo "Backup created: $(realpath "$ARCHIVE_PATH")"
```

### Короткий опис роботи скрипта

Скрипт приймає два аргументи: каталог з логами та каталог для збереження резервних копій.

Перевіряє, що передано рівно два аргументи.  
Переконується, що обидва аргументи є існуючими каталогами.  
Використовує lock-файл /tmp/backup.lock для захисту від одночасного запуску кількох копій скрипта.  
Створює архів усіх файлів із каталогу логів у форматі:  
logs_backup_YYYY-MM-DD_HH-MM.tar.gz.  
Зберігає архів у каталозі резервних копій.  
Якщо архівація завершується помилкою — виводить повідомлення Backup failed і завершується з кодом 2.  
Якщо архівація успішна — виводить повний шлях до створеного архіву.  

### Перевірка роботи

1. Невірна кількість аргументів
```bash
./backup.sh
```

Результат:
```bash
Usage: ./backup.sh <log_dir> <backup_dir>
```

2. Неіснуючий каталог
```bash
./backup.sh /tmp/logs /tmp/not_exists
```

Результат:
```bash
Usage: ./backup.sh <log_dir> <backup_dir>
```

3. Успішне створення бекапу
```bash
mkdir -p logs backup
echo "test log" > logs/app.log

./backup.sh logs backup
```

Результат:
```bash
Backup created: /home/ubuntu/backup/logs_backup_2025-05-20_14-30.tar.gz
```

4. Перевірка lock-файлу
```bash
touch /tmp/backup.lock
./backup.sh logs backup
```

Результат:
```bash
Backup already running
```
