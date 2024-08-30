
```markdown
# 📜 Инструкция по созданию сервиса автозагрузки скриптов на Аврора ОС и Linux
```

Данная инструкция поможет вам создать сервис, который будет автоматически запускать скрипты при старте системы на Аврора ОС или любой другой Linux-системе.

## 🚀 Шаг 1. Создание директории для основного скрипта

Для начала создайте директорию, в которой будет размещён основной скрипт. В будущем, возможно, потребуется переместить его в более подходящее место.

```bash
mkdir -p /usr/local/bin
```

## ✍️ Шаг 2. Создание основного скрипта автозагрузки

Создайте основной скрипт, который будет запускать все скрипты из директории `/home/defaultuser/Desktop`.

```bash
vi /usr/local/bin/run-startup-scripts.sh
```

Добавьте в скрипт следующий код:

```bash
#!/bin/bash

# Указываем директорию со скриптами
SCRIPT_DIR="/home/defaultuser/Desktop"

# Проверяем наличие директории и запускаем скрипты
if [ -d "$SCRIPT_DIR" ]; then
    echo "Директория $SCRIPT_DIR найдена. Запуск скриптов..."
    for script in "$SCRIPT_DIR"/*.sh; do
        if [ -x "$script" ]; then
            echo "Запуск скрипта: $script"
            sh "$script"
        else
            echo "Скрипт $script не является исполняемым"
        fi
    done
else
    echo "Директория $SCRIPT_DIR не найдена."
fi
```

Сделайте скрипт исполняемым:

```bash
chmod +x /usr/local/bin/run-startup-scripts.sh
```

## 🛠️ Шаг 3. Создание unit-файла systemd для автозагрузки скриптов

Для автоматического запуска созданного скрипта при старте системы создайте unit-файл для systemd:

```bash
vi /etc/systemd/system/startup-scripts.service
```

Добавьте в файл следующее содержимое:

```ini
[Unit]
Description=Сервис для автозагрузки скриптов
After=syslog.target network.target

[Service]
ExecStart=/bin/sh /usr/local/bin/run-startup-scripts.sh
WorkingDirectory=/home/defaultuser/Desktop
Restart=on-failure
RestartSec=5s
User=root

[Install]
WantedBy=multi-user.target
```

## ⚙️ Шаг 4. Активация сервиса

После создания unit-файла, активируйте сервис командой:

```bash
systemctl daemon-reload
systemctl enable startup-scripts.service
systemctl start startup-scripts.service
```

## 📝 Шаг 5. Добавление скриптов в автозагрузку

Чтобы добавить скрипт в автозагрузку, создайте его в каталоге `/home/defaultuser/Desktop`:

```bash
vi /home/defaultuser/Desktop/test-script.sh
```

Пример содержимого скрипта:

```bash
#!/bin/sh
# Скрипт для записи логов в файл
echo "Тестовый лог" >> /home/defaultuser/Desktop/test-script.log
```

Сделайте его исполняемым:

```bash
chmod +x /home/defaultuser/Desktop/test-script.sh
```

Теперь этот скрипт будет автоматически запускаться при старте системы вместе с другими скриптами, которые находятся в каталоге `/home/defaultuser/Desktop`.

---

📌 **Примечание:** Данная инструкция подходит для Аврора ОС и других Linux-дистрибутивов. С помощью этих шагов вы сможете настроить удобную и гибкую систему автозагрузки скриптов на вашем сервере или рабочей станции.
```

Эта версия структурирована с использованием заголовков, эмодзи для улучшения читаемости, и форматирования кода для удобного восприятия. Вы можете сохранить её в файле `README.md` и разместить на GitHub для удобства использования и распространения.
