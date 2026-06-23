
### Лабораторная работа : «Управление устройствами с помощью udev»

**Цель работы:** Научиться создавать правила udev для управления устройствами, добавлять альтернативные имена для дисков, понимать механизм работы udev и уметь диагностировать проблемы.

**Теоретическая справка:**

**Что такое udev?**
udev — это менеджер устройств для ядра Linux, который динамически создает и удаляет файлы устройств в каталоге `/dev/`. Он позволяет:
- Создавать постоянные имена для устройств
- Устанавливать права доступа к устройствам
- Запускать скрипты при подключении/отключении устройств
- Управлять символическими ссылками

**Основные компоненты udev:**
- **Правила (Rules)** — файлы в `/etc/udev/rules.d/` и `/lib/udev/rules.d/`
- **Свойства (Properties)** — атрибуты устройств, используемые для идентификации
- **Действия (Actions)** — события, на которые реагирует udev (add, remove, change)

**Структура правила udev:**
```
[ключи_для_матчинга] [ключи_для_действий]
```

**Основные ключи для идентификации устройств:**
- `SUBSYSTEM` — подсистема (block, net, tty, etc.)
- `KERNEL` — имя устройства в ядре (sda, sdb, eth0, etc.)
- `ATTR{name}` — атрибут устройства
- `ENV{key}` — переменная окружения
- `ID_SERIAL` — серийный номер устройства
- `ID_MODEL` — модель устройства
- `DEVPATH` — путь в /sys

**Ключи для действий:**
- `NAME` — имя файла устройства
- `SYMLINK` — символическая ссылка
- `OWNER`, `GROUP`, `MODE` — права доступа
- `RUN` — выполнить скрипт
- `LABEL` — метка для перехода
- `GOTO` — переход к метке

**Важные команды:**
```bash
# Просмотр информации об устройстве
udevadm info -a -n /dev/sda

# Мониторинг событий udev
udevadm monitor

# Проверка правил
udevadm test /sys/block/sda

# Перезагрузка правил
udevadm control --reload-rules

# Триггер для применения правил
udevadm trigger
```

---

### Задание

Создайте правило udev, которое добавляет альтернативное имя для диска `/dev/sda`.

**Требования:**
1. Альтернативное имя должно быть `/dev/mydisk`
2. Имя должно быть постоянным (не зависит от порядка подключения)
3. Правило должно быть документировано
4. Должна быть проверка работоспособности
5. Правило должно корректно применяться при загрузке системы

---

### Варианты заданий

#### Вариант 1 (Базовый)
Создайте простое правило, которое создает символическую ссылку `/dev/mydisk` для `/dev/sda` без учета уникальных идентификаторов.

#### Вариант 2 (По серийному номеру)
Создайте правило, которое идентифицирует диск по серийному номеру и создает постоянное имя.

#### Вариант 3 (По модели)
Создайте правило, которое создает имя диска на основе модели и серийного номера.


### Эталонные решения

#### Вариант 1 (Базовый)

**Файл:** `/etc/udev/rules.d/99-mydisk.rules`

```bash
# Правило udev для создания альтернативного имени диска
# Создает символическую ссылку /dev/mydisk на /dev/sda

# Идентифицируем устройство по подсистеме и имени
SUBSYSTEM=="block", KERNEL=="sda", SYMLINK+="mydisk"

# Альтернативный вариант с более строгой идентификацией
# SUBSYSTEM=="block", KERNEL=="sda", ACTION=="add", SYMLINK+="mydisk"
```

**Инструкция по установке:**

```bash
# 1. Создаем файл правила
sudo nano /etc/udev/rules.d/99-mydisk.rules

# 2. Вставляем содержимое и сохраняем

# 3. Перезагружаем правила
sudo udevadm control --reload-rules

# 4. Применяем правила
sudo udevadm trigger

# 5. Проверяем, что ссылка создалась
ls -la /dev/mydisk
# Должно показать: lrwxrwxrwx 1 root root 3 ... /dev/mydisk -> sda

# 6. Проверяем, что ссылка ведет на правильное устройство
ls -la /dev/mydisk /dev/sda
# Оба должны ссылаться на одно устройство

# 7. Проверяем, что диск доступен по новому имени
sudo fdisk -l /dev/mydisk
```

---

#### Вариант 2 (По серийному номеру)

**Шаг 1: Получение информации об устройстве**

```bash
# Получаем информацию о диске
udevadm info -a -n /dev/sda | grep -E "ID_SERIAL|ID_MODEL" | head -5

# Или
sudo smartctl -i /dev/sda | grep "Serial Number"

# Или более детально
udevadm info --query=property --name=/dev/sda | grep -E "ID_SERIAL|ID_MODEL|ID_VENDOR"
```

**Файл:** `/etc/udev/rules.d/99-mydisk-serial.rules`

```bash
# Правило udev для создания альтернативного имени диска по серийному номеру
# Это гарантирует, что имя останется постоянным даже при переподключении

# Идентифицируем устройство по серийному номеру
# Замените XXXXXXXXXXXXXX на реальный серийный номер вашего диска

# Вариант с использованием ID_SERIAL
SUBSYSTEM=="block", ENV{ID_SERIAL}=="Samsung_SSD_860_EVO_XXXXXXXXXXXX", SYMLINK+="mydisk"

# Или более строгий вариант
SUBSYSTEM=="block", ENV{ID_VENDOR}=="Samsung", ENV{ID_MODEL}=="SSD_860_EVO", ENV{ID_SERIAL}=="XXXXXXXXXXXX", SYMLINK+="mydisk"

# Создание дополнительных ссылок для удобства
SUBSYSTEM=="block", ENV{ID_SERIAL}=="Samsung_SSD_860_EVO_XXXXXXXXXXXX", SYMLINK+="mydisk", SYMLINK+="ssd", SYMLINK+="disk0"

# Альтернативный вариант с использованием ID_WWN (World Wide Name)
# SUBSYSTEM=="block", ENV{ID_WWN}=="0xXXXXXXXXXXXXXXXX", SYMLINK+="mydisk"
```

**Инструкция по установке:**

```bash
# 1. Получаем серийный номер
SERIAL=$(udevadm info --query=property --name=/dev/sda | grep ID_SERIAL | cut -d= -f2)
echo "Серийный номер: $SERIAL"

# 2. Создаем правило с подстановкой серийного номера
sudo bash -c "cat > /etc/udev/rules.d/99-mydisk-serial.rules << EOF
SUBSYSTEM==\"block\", ENV{ID_SERIAL}==\"$SERIAL\", SYMLINK+=\"mydisk\"
EOF"

# 3. Перезагружаем правила
sudo udevadm control --reload-rules

# 4. Применяем правила
sudo udevadm trigger

# 5. Проверяем
ls -la /dev/mydisk

# 6. Проверяем информацию об устройстве по новому имени
udevadm info --query=property --name=/dev/mydisk
```

---

#### Вариант 3 (По модели и серийному номеру)

**Файл:** `/etc/udev/rules.d/99-disk-by-model.rules`

```bash
# Правило udev для создания имени диска на основе модели и серийного номера

# Получаем информацию о диске и создаем комбинированное имя
# Использование ID_MODEL и ID_SERIAL для создания понятного имени

# Создаем имя в формате: model_serial
SUBSYSTEM=="block", ENV{ID_MODEL}=="Samsung_SSD_860_EVO", ENV{ID_SERIAL}=="XXXXXXXXXXXX", \
    SYMLINK+="disk/samsung-ssd-860-evo", \
    SYMLINK+="disk/by-serial/$env{ID_SERIAL}", \
    SYMLINK+="disk/by-model/$env{ID_MODEL}"

# Создаем несколько ссылок для удобства
SUBSYSTEM=="block", ENV{ID_MODEL}=="Samsung_SSD_860_EVO", ENV{ID_SERIAL}=="XXXXXXXXXXXX", \
    SYMLINK+="mydisk", \
    SYMLINK+="primary", \
    SYMLINK+="samsung"

# Для LVM дисков
SUBSYSTEM=="block", ENV{ID_FS_TYPE}=="LVM2_member", ENV{ID_SERIAL}=="XXXXXXXXXXXX", \
    SYMLINK+="lvm/root", \
    SYMLINK+="lvm/system"

# Для дисков с определенной файловой системой
SUBSYSTEM=="block", ENV{ID_FS_TYPE}=="ext4", ENV{ID_SERIAL}=="XXXXXXXXXXXX", \
    SYMLINK+="fs/ext4-root"
```

**Скрипт для автоматического создания правил:**

```bash
#!/usr/bin/env bash

# Скрипт для автоматического создания правил udev для диска

echo "========================================="
echo "   СОЗДАНИЕ ПРАВИЛ udev ДЛЯ ДИСКА"
echo "========================================="

# Проверка прав
if [[ $EUID -ne 0 ]]; then
    echo "Запустите с правами root (sudo)"
    exit 1
fi

# Запрос устройства
read -p "Введите устройство (например, /dev/sda): " DEVICE

if [[ ! -b "$DEVICE" ]]; then
    echo "Устройство $DEVICE не существует!"
    exit 1
fi

# Получение информации
echo "▶ Получение информации об устройстве..."
MODEL=$(udevadm info --query=property --name="$DEVICE" | grep ID_MODEL | cut -d= -f2)
SERIAL=$(udevadm info --query=property --name="$DEVICE" | grep ID_SERIAL | cut -d= -f2)
VENDOR=$(udevadm info --query=property --name="$DEVICE" | grep ID_VENDOR | cut -d= -f2)

if [[ -z "$SERIAL" ]]; then
    echo "Не удалось получить серийный номер!"
    echo "   Будет использован KERNEL для идентификации"
    KERNEL=$(basename "$DEVICE")
else
    KERNEL=""
fi

echo ""
echo "Информация об устройстве:"
echo "  Устройство: $DEVICE"
echo "  Модель: $MODEL"
echo "  Серийный номер: $SERIAL"
echo "  Производитель: $VENDOR"
echo ""

# Запрос имени для ссылки
read -p "Введите желаемое имя для ссылки (например, mydisk): " SYMLINK_NAME

if [[ -z "$SYMLINK_NAME" ]]; then
    echo "Имя не может быть пустым!"
    exit 1
fi

# Создание правила
RULES_DIR="/etc/udev/rules.d"
RULES_FILE="$RULES_DIR/99-$SYMLINK_NAME.rules"

echo "Создание правила в $RULES_FILE..."

if [[ -n "$SERIAL" ]]; then
    # Правило по серийному номеру
    cat > "$RULES_FILE" << EOF
# Правило udev для диска $DEVICE
# Создает ссылку /dev/$SYMLINK_NAME
# Модель: $MODEL
# Серийный номер: $SERIAL

SUBSYSTEM=="block", ENV{ID_SERIAL}=="$SERIAL", SYMLINK+="$SYMLINK_NAME"

# Дополнительные ссылки для удобства
SUBSYSTEM=="block", ENV{ID_SERIAL}=="$SERIAL", SYMLINK+="disk/by-serial/$SERIAL"
SUBSYSTEM=="block", ENV{ID_SERIAL}=="$SERIAL", SYMLINK+="disk/by-model/$MODEL"
EOF
else
    # Правило по имени ядра (не рекомендуется для постоянных имен)
    KERNEL=$(basename "$DEVICE")
    cat > "$RULES_FILE" << EOF
# Правило udev для диска $DEVICE
# ВНИМАНИЕ: Используется имя ядра, это может быть непостоянно!

SUBSYSTEM=="block", KERNEL=="$KERNEL", SYMLINK+="$SYMLINK_NAME"
EOF
    echo "Используется имя ядра, которое может измениться!"
fi

echo "Правило создано!"

# Перезагрузка правил
echo "▶ Перезагрузка правил udev..."
udevadm control --reload-rules
udevadm trigger

# Проверка
echo "Проверка создания ссылки..."
sleep 1

if [[ -L "/dev/$SYMLINK_NAME" ]]; then
    echo "Ссылка создана: /dev/$SYMLINK_NAME -> $(readlink /dev/$SYMLINK_NAME)"
else
    echo "Ссылка не создана!"
    echo "   Проверьте логи: journalctl -f"
    exit 1
fi

echo ""
echo "========================================="
echo "   ГОТОВО!"
echo "   Ссылка: /dev/$SYMLINK_NAME"
echo "   Правило: $RULES_FILE"
echo "========================================="
```

---

