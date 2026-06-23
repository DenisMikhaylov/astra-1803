### Лабораторная работа  «Создание простого systemd-сервиса»

**Цель работы:** Научиться создавать, настраивать и управлять простыми systemd-юнитами. Понять структуру service-файлов, научиться запускать, останавливать и включать сервисы в автозагрузку.

**Теоретическая справка:**

**Что такое systemd?**
systemd — это менеджер системных служб, который запускает процессы, управляет их зависимостями и отслеживает их состояние. Это стандарт в большинстве современных дистрибутивов Linux.

**Основные компоненты systemd:**
- **Юниты (Units)** — файлы конфигурации, описывающие службы (.service), таймеры (.timer), монтирования (.mount) и т.д.
- **Целевые юниты (Targets)** — группы юнитов (аналог runlevels)
- **Журнал (Journal)** — централизованное логирование

**Структура service-файла:**
```ini
[Unit]
Description=Описание службы
After=зависимости (после каких служб запускать)

[Service]
Type=тип_службы (simple, oneshot, forking, notify)
User=пользователь
Group=группа
ExecStart=команда_запуска
ExecStop=команда_остановки
Restart=политика_рестарта

[Install]
WantedBy=целевой_юнит (multi-user.target, graphical.target)
```

**Основные команды systemctl:**
- `systemctl start` — запустить службу
- `systemctl stop` — остановить службу
- `systemctl restart` — перезапустить службу
- `systemctl status` — показать статус
- `systemctl enable` — включить автозапуск
- `systemctl disable` — отключить автозапуск
- `systemctl daemon-reload` — перечитать конфигурацию

---

### Задание

Создайте простой systemd-сервис, который:
1. При запуске выводит в системный журнал сообщение с текущей датой и временем
2. При остановке выводит сообщение об остановке
3. Имеет понятное описание
4. Может быть включен в автозагрузку
5. Корректно обрабатывает сигналы

---

### Варианты заданий (усложнение)

#### Вариант 1 (Базовый)
Создайте простейший сервис, который при запуске пишет "Hello, systemd!" в журнал.

#### Вариант 2 (С сообщениями при запуске и остановке)
Создайте сервис, который пишет разные сообщения при запуске, остановке и перезагрузке.

#### Вариант 3 (С аргументами)
Создайте сервис, который принимает аргументы командной строки (например, имя пользователя) и выводит персонализированное приветствие.

#### Вариант 4 (С таймером)
Создайте сервис, который запускается по таймеру каждую минуту и пишет текущее время.

#### Вариант 5 (С конфигурационным файлом)
Создайте сервис, который читает настройки из конфигурационного файла и выводит приветствие с именем из конфига.

---

### Требования к оформлению

1. **Имя сервиса:** `hello.service` или `greeting.service`
2. **Путь размещения:** `/etc/systemd/system/`
3. **Скрипт (опционально):** `/usr/local/bin/hello-script.sh`
4. **Логирование:** Все сообщения должны попадать в systemd journal
5. **Комментарии:** Каждая секция должна быть прокомментирована

---

### Эталонные решения

#### Вариант 1 (Базовый)

**Самый простой способ** — использовать встроенную команду `echo` прямо в service-файле:

**Файл:** `/etc/systemd/system/hello.service`

```ini
[Unit]
Description=Hello Systemd Service
Documentation=https://www.freedesktop.org/wiki/Software/systemd/
After=network.target

[Service]
Type=simple
ExecStart=/bin/bash -c 'echo "Hello, systemd! $(date)"'
ExecStop=/bin/bash -c 'echo "Goodbye, systemd! $(date)"'
Restart=no

[Install]
WantedBy=multi-user.target
```

**Установка и запуск:**

```bash
# Перезагружаем конфигурацию systemd
sudo systemctl daemon-reload

# Включаем автозапуск
sudo systemctl enable hello.service

# Запускаем сервис
sudo systemctl start hello.service

# Проверяем статус
sudo systemctl status hello.service

# Смотрим логи
sudo journalctl -u hello.service -f
```

---

#### Вариант 2 (С сообщениями при запуске и остановке)

**Файл:** `/etc/systemd/system/greeting.service`

```ini
[Unit]
Description=Greeting Service with Messages
After=network.target
Wants=network.target

[Service]
Type=oneshot
RemainAfterExit=yes

# Команда запуска
ExecStart=/bin/bash -c 'echo "⏰ Сервис запущен: $(date)" | logger -t greeting'

# Команда остановки
ExecStop=/bin/bash -c 'echo "⏰ Сервис остановлен: $(date)" | logger -t greeting'

# Команда перезагрузки
ExecReload=/bin/bash -c 'echo "⏰ Конфигурация перезагружена: $(date)" | logger -t greeting'

# Политика рестарта
Restart=no

# Таймауты
TimeoutStartSec=5
TimeoutStopSec=5

[Install]
WantedBy=multi-user.target
```

**Тестирование:**

```bash
# Проверка конфигурации
sudo systemd-analyze verify /etc/systemd/system/greeting.service

# Запуск
sudo systemctl start greeting.service

# Проверка статуса
sudo systemctl status greeting.service

# Просмотр логов
sudo journalctl -u greeting.service -f
```

---

#### Вариант 3 (С аргументами)

**Файл скрипта:** `/usr/local/bin/greeting-script.sh`

```bash
#!/usr/bin/env bash

# Простой скрипт для systemd-сервиса

NAME="${1:-Anonymous}"
LOG_FILE="/var/log/greeting.log"

# Функция логирования
log_message() {
    local msg="$1"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    echo "[$timestamp] $msg"
    echo "[$timestamp] $msg" >> "$LOG_FILE"
    logger -t "greeting" "$msg"
}

# Проверка аргументов
case "${1:-help}" in
    start)
        log_message "Сервис запущен! Привет, $NAME!"
        exit 0
        ;;
    stop)
        log_message "Сервис остановлен! До свидания, $NAME!"
        exit 0
        ;;
    reload)
        log_message "Сервис перезагружен! Привет снова, $NAME!"
        exit 0
        ;;
    status)
        echo "Статус: Сервис работает для пользователя $NAME"
        echo "Последние логи:"
        tail -5 "$LOG_FILE" 2>/dev/null || echo "Логи не найдены"
        exit 0
        ;;
    *)
        echo "Использование: $0 {start|stop|reload|status} [имя]"
        exit 1
        ;;
esac
```

**Сделайте скрипт исполняемым:**
```bash
sudo chmod +x /usr/local/bin/greeting-script.sh
```

**Файл:** `/etc/systemd/system/greeting-args.service`

```ini
[Unit]
Description=Greeting Service with Arguments
Documentation=man:systemd(1)
After=network.target

[Service]
Type=oneshot
RemainAfterExit=yes

# Передаем аргументы в скрипт
Environment=USER_NAME=Student

# Скрипт с аргументами
ExecStart=/usr/local/bin/greeting-script.sh start ${USER_NAME}
ExecStop=/usr/local/bin/greeting-script.sh stop ${USER_NAME}
ExecReload=/usr/local/bin/greeting-script.sh reload ${USER_NAME}

# Политика рестарта
Restart=no

# Логирование
StandardOutput=journal
StandardError=journal
SyslogIdentifier=greeting-args

[Install]
WantedBy=multi-user.target
```

**Тестирование:**

```bash
# Запуск
sudo systemctl start greeting-args.service

# Проверка логов
sudo journalctl -u greeting-args.service -f

# Изменение переменной окружения
sudo systemctl set-environment USER_NAME=Professor
sudo systemctl reload greeting-args.service

# Проверка
sudo journalctl -u greeting-args.service -n 10
```

---

#### Вариант 4 (С таймером)

**Файл:** `/etc/systemd/system/hello-timer.service`

```ini
[Unit]
Description=Hello Timer Service
After=network.target

[Service]
Type=oneshot
# Скрипт будет выполнен однократно
ExecStart=/bin/bash -c 'echo "⏰ Время: $(date)" | logger -t hello-timer'

[Install]
WantedBy=multi-user.target
```

**Файл:** `/etc/systemd/system/hello-timer.timer`

```ini
[Unit]
Description=Hello Timer - runs every minute
Documentation=man:systemd.timer(5)

[Timer]
# Запускать каждую минуту
OnCalendar=*-*-* *:*:00

# Не запускать, если пропущен
Persistent=false

# Разрешить задержку (чтобы не нагружать систему)
RandomizedDelaySec=5

# Единоразовый запуск при старте
OnBootSec=10

# Запускать сразу после таймера
Unit=hello-timer.service

[Install]
WantedBy=timers.target
```

**Установка и запуск:**

```bash
# Включаем таймер
sudo systemctl enable hello-timer.timer
sudo systemctl start hello-timer.timer

# Проверяем таймер
sudo systemctl status hello-timer.timer
sudo systemctl list-timers hello-timer.timer

# Смотрим логи (каждую минуту)
sudo journalctl -u hello-timer.service -f
```

---

#### Вариант 5 (С конфигурационным файлом)

**Файл:** `/etc/default/hello-service`

```bash
# Конфигурация для hello-service
# Измените переменные для настройки

# Имя пользователя для приветствия
USER_NAME="World"

# Уровень логирования (info, warning, error)
LOG_LEVEL="info"

# Показывать дату? (true/false)
SHOW_DATE="true"

# Файл для логирования
LOG_FILE="/var/log/hello-service.log"
```

**Файл:** `/usr/local/bin/hello-config.sh`

```bash
#!/usr/bin/env bash

# Скрипт с поддержкой конфигурационного файла

CONFIG_FILE="/etc/default/hello-service"
LOG_FILE="/var/log/hello-service.log"

# Функция логирования
log_message() {
    local msg="$1"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    
    # Запись в файл
    echo "[$timestamp] $msg" >> "$LOG_FILE"
    
    # Запись в systemd journal
    logger -t "hello-config" "$msg"
    
    # Вывод в консоль (если интерактивно)
    if [[ -t 1 ]]; then
        echo "[$timestamp] $msg"
    fi
}

# Чтение конфигурации
load_config() {
    if [[ -f "$CONFIG_FILE" ]]; then
        source "$CONFIG_FILE"
        log_message "Конфигурация загружена из $CONFIG_FILE"
    else
        # Значения по умолчанию
        USER_NAME="World"
        LOG_LEVEL="info"
        SHOW_DATE="true"
        LOG_FILE="/var/log/hello-service.log"
        log_message "Конфигурация не найдена, используются значения по умолчанию"
    fi
}

# Функция запуска
start_service() {
    load_config
    
    local message="Hello, $USER_NAME!"
    if [[ "$SHOW_DATE" == "true" ]]; then
        message="$message (Запущено: $(date))"
    fi
    
    log_message "🚀 $message"
    log_message "  Уровень логирования: $LOG_LEVEL"
    log_message "  Лог-файл: $LOG_FILE"
    
    # Запись статуса
    echo "running" > /var/run/hello-service.pid
}

# Функция остановки
stop_service() {
    load_config
    
    local message="Goodbye, $USER_NAME!"
    if [[ "$SHOW_DATE" == "true" ]]; then
        message="$message (Остановлено: $(date))"
    fi
    
    log_message "🛑 $message"
    rm -f /var/run/hello-service.pid
}

# Функция перезагрузки
reload_service() {
    log_message "Перезагрузка конфигурации..."
    load_config
    log_message "Конфигурация обновлена"
}

# Функция показа статуса
status_service() {
    echo "=== Hello Service ==="
    
    if [[ -f /var/run/hello-service.pid ]]; then
        echo "Статус: РАБОТАЕТ"
    else
        echo "Статус: ОСТАНОВЛЕН"
    fi
    
    echo ""
    echo "Конфигурация:"
    if [[ -f "$CONFIG_FILE" ]]; then
        cat "$CONFIG_FILE" | sed 's/^/  /'
    else
        echo "  Конфигурационный файл не найден, используются значения по умолчанию"
    fi
    
    echo ""
    echo "Последние логи:"
    tail -5 "$LOG_FILE" 2>/dev/null | sed 's/^/  /' || echo "  Логи не найдены"
}

# Основная программа
mkdir -p "$(dirname "$LOG_FILE")" 2>/dev/null
touch "$LOG_FILE" 2>/dev/null

case "${1:-help}" in
    start)
        start_service
        exit 0
        ;;
    stop)
        stop_service
        exit 0
        ;;
    reload)
        reload_service
        exit 0
        ;;
    status)
        status_service
        exit 0
        ;;
    *)
        cat << EOF
Использование: $0 {start|stop|reload|status}

  start   - Запустить сервис
  stop    - Остановить сервис
  reload  - Перезагрузить конфигурацию
  status  - Показать статус

Конфигурация: $CONFIG_FILE
EOF
        exit 1
        ;;
esac
```

**Сделайте скрипт исполняемым:**
```bash
sudo chmod +x /usr/local/bin/hello-config.sh
```

**Файл:** `/etc/systemd/system/hello-config.service`

```ini
[Unit]
Description=Hello Service with Configuration
Documentation=man:systemd(1)
After=network.target
Wants=network.target

[Service]
Type=oneshot
RemainAfterExit=yes

# Переменные окружения
EnvironmentFile=-/etc/default/hello-service

# Команды
ExecStart=/usr/local/bin/hello-config.sh start
ExecStop=/usr/local/bin/hello-config.sh stop
ExecReload=/usr/local/bin/hello-config.sh reload

# Политика рестарта
Restart=no

# Таймауты
TimeoutStartSec=10
TimeoutStopSec=10

# Логирование
StandardOutput=journal
StandardError=journal
SyslogIdentifier=hello-config

[Install]
WantedBy=multi-user.target
```

---

### Универсальные команды для всех вариантов

```bash
# 1. Создание service-файла
sudo nano /etc/systemd/system/hello.service

# 2. Перезагрузка systemd (после создания/изменения)
sudo systemctl daemon-reload

# 3. Запуск сервиса
sudo systemctl start hello.service

# 4. Проверка статуса
sudo systemctl status hello.service

# 5. Просмотр логов
sudo journalctl -u hello.service -f

# 6. Включение автозапуска
sudo systemctl enable hello.service

# 7. Отключение автозапуска
sudo systemctl disable hello.service

# 8. Остановка сервиса
sudo systemctl stop hello.service

# 9. Перезапуск
sudo systemctl restart hello.service

# 10. Проверка конфигурации
sudo systemd-analyze verify /etc/systemd/system/hello.service
```

---

### Проверка работоспособности

Выполните следующие проверки для вашего сервиса:

```bash
# Проверка 1: Запуск
sudo systemctl start hello.service
sudo systemctl status hello.service | grep "Active:"

# Проверка 2: Логирование
sudo journalctl -u hello.service -n 10

# Проверка 3: Остановка
sudo systemctl stop hello.service
sudo systemctl status hello.service | grep "Active:"

# Проверка 4: Автозапуск
sudo systemctl enable hello.service
sudo systemctl is-enabled hello.service

# Проверка 5: Перезагрузка (проверяем, что после перезагрузки сервис активен)
sudo reboot
# После перезагрузки:
sudo systemctl status hello.service
```

---

### Пример успешного выполнения

```bash
$ sudo systemctl start hello.service

$ sudo systemctl status hello.service
● hello.service - Hello Systemd Service
   Loaded: loaded (/etc/systemd/system/hello.service; disabled; vendor preset: disabled)
   Active: active (exited) since Mon 2026-06-22 10:00:00 UTC; 1min ago
  Process: 12345 ExecStart=/bin/bash -c echo "Hello, systemd! $(date)" (code=exited, status=0/SUCCESS)
 Main PID: 12345 (code=exited, status=0/SUCCESS)
   CGroup: /system.slice/hello.service

Jun 22 10:00:00 server systemd[1]: Starting Hello Systemd Service...
Jun 22 10:00:00 server bash[12345]: Hello, systemd! Mon Jun 22 10:00:00 UTC 2026
Jun 22 10:00:00 server systemd[1]: Started Hello Systemd Service.

$ sudo journalctl -u hello.service -f
-- Logs begin at Mon 2026-06-22 10:00:00 UTC. --
Jun 22 10:00:00 server systemd[1]: Starting Hello Systemd Service...
Jun 22 10:00:00 server bash[12345]: Hello, systemd! Mon Jun 22 10:00:00 UTC 2026
Jun 22 10:00:00 server systemd[1]: Started Hello Systemd Service.
Jun 22 10:00:30 server systemd[1]: Stopping Hello Systemd Service...
Jun 22 10:00:30 server bash[12346]: Goodbye, systemd! Mon Jun 22 10:00:30 UTC 2026
Jun 22 10:00:30 server systemd[1]: Stopped Hello Systemd Service.
```
