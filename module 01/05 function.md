З
### Лабораторная работа  «Функции и модульная структура скриптов»

**Цель работы:** Научиться создавать функции, передавать им аргументы, возвращать значения, использовать локальные переменные и управлять выполнением скрипта с помощью `exit`.

**Теоретическая справка:**

**Определение функции:**
```bash
# Способ 1
function_name() {
    команды
}

# Способ 2
function function_name {
    команды
}
```

**Вызов функции:**
```bash
function_name          # Без аргументов
function_name arg1 arg2  # С аргументами
```

**Передача аргументов в функцию:**
- `$1`, `$2`, ... — позиционные параметры
- `$@` — все аргументы
- `$#` — количество аргументов

**Локальные переменные:**
```bash
local var_name="value"  # Переменная видна только внутри функции
```

**Возврат значений:**
- `return N` — возвращает числовой код (0-255), используется для статуса
- `echo "value"` — вывод значения для захвата `$(function_name)`

**Коды выхода:**
- `exit 0` — успешное завершение
- `exit 1-255` — ошибка (разные коды для разных ошибок)

---

### Варианты заданий

#### Вариант 1 (Менеджер файлов и бэкапов)

Напишите скрипт для управления файлами и бэкапами, который:

1. Содержит следующие функции:
   - `check_directory()` — проверяет существование директории, создаёт если нет
   - `get_file_info()` — выводит информацию о файле (размер, права, дата)
   - `create_backup()` — создает бэкап директории
   - `cleanup_old_backups()` — удаляет бэкапы старше N дней
   - `show_statistics()` — показывает статистику по файлам
   - `show_menu()` — отображает главное меню

2. Использует глобальные переменные для конфигурации:
   - `BACKUP_DIR` — директория для бэкапов
   - `RETENTION_DAYS` — количество дней хранения
   - `LOG_FILE` — файл для логирования

3. Каждая функция должна возвращать код выхода:
   - `0` — успех
   - `1` — ошибка (с сообщением)

4. Использует `read` для интерактивного взаимодействия.
5. Использует циклы для обработки файлов.
6. Основная программа — бесконечное меню с выходом.

**Пример вывода:**
```
$ ./file_manager.sh /home/user/docs
✅ Директория /home/user/docs существует

=== МЕНЕДЖЕР ФАЙЛОВ ===
1. Показать информацию о файле
2. Создать бэкап
3. Очистить старые бэкапы
4. Показать статистику
5. Выход

Выберите действие: 1
Введите имя файла: file.txt
Файл: file.txt
  Размер: 1024 байт
  Права: -rw-r--r--
  Дата: 2026-06-22 10:00:00
```

---

#### Вариант 2 (Калькулятор с функциями)

Напишите скрипт-калькулятор с функциями, который:

1. Содержит функции для каждой операции:
   - `add()` — сложение
   - `subtract()` — вычитание
   - `multiply()` — умножение
   - `divide()` — деление
   - `power()` — возведение в степень
   - `modulo()` — остаток от деления

2. Функция `validate_number()` — проверяет, является ли строка числом.

3. Функция `get_user_input()` — запрашивает числа и оператор.

4. Функция `show_history()` — показывает историю вычислений.

5. Функция `clear_history()` — очищает историю.

6. Каждая функция возвращает результат через `echo`, а статус через `return`.

7. Использует массивы для хранения истории.

8. Основная программа — интерактивный режим с командами.

**Пример вывода:**
```
$ ./calc_functions.sh
Калькулятор (введите 'help' для справки)
> 10 + 5
Результат: 15
> 10 / 3
Результат: 3.33
> history
1: 10 + 5 = 15
2: 10 / 3 = 3.33
> help
Доступные команды:
  <число> <оператор> <число> - вычисление
  history - показать историю
  clear - очистить историю
  exit - выход
```

---

#### Вариант 3 (Мониторинг системы)

Напишите скрипт мониторинга системы, который:

1. Содержит функции:
   - `get_cpu_usage()` — возвращает загрузку CPU
   - `get_ram_usage()` — возвращает использование RAM
   - `get_disk_usage()` — возвращает использование диска
   - `get_top_processes()` — возвращает топ-5 процессов по CPU
   - `check_process()` — проверяет, запущен ли процесс
   - `send_alert()` — отправляет уведомление (просто выводит сообщение)
   - `monitor()` — основная функция мониторинга

2. Использует глобальные переменные для порогов:
   - `CPU_THRESHOLD=80`
   - `RAM_THRESHOLD=90`
   - `DISK_THRESHOLD=10`

3. Функция `monitor()` использует бесконечный цикл с интервалом.

4. При превышении порога вызывается `send_alert()`.

5. Использует `trap` для корректного завершения.

6. Все функции возвращают коды выхода:
   - `0` — норма
   - `1` — предупреждение
   - `2` — критично

**Пример вывода:**
```
$ ./monitor_functions.sh
Запуск мониторинга (Ctrl+C для выхода)...

[10:00:01] CPU: 45% | RAM: 60% | DISK: 75% ✅
[10:00:06] CPU: 85% | RAM: 62% | DISK: 75% ⚠️
⚠️ ВНИМАНИЕ: высокая загрузка CPU: 85%
```

---

#### Вариант 4 (Управление пользователями)

Напишите скрипт для управления пользователями с функциями:

1. Содержит функции:
   - `user_exists()` — проверяет существование пользователя
   - `create_user()` — создает пользователя
   - `delete_user()` — удаляет пользователя
   - `get_user_info()` — показывает информацию о пользователе
   - `change_password()` — меняет пароль
   - `list_users()` — показывает список пользователей
   - `check_root()` — проверяет права root

2. Использует локальные переменные внутри функций.

3. Каждая функция возвращает код выхода:
   - `0` — успех
   - `1` — пользователь не найден
   - `2` — недостаточно прав
   - `3` — другие ошибки

4. Основная программа — меню с выбором действий.

5. В случае ошибки выводит сообщение и возвращает соответствующий код.

**Пример вывода:**
```
$ ./user_manager_functions.sh
⚠️ ВНИМАНИЕ: Некоторые операции требуют прав root

=== УПРАВЛЕНИЕ ПОЛЬЗОВАТЕЛЯМИ ===
1. Создать пользователя
2. Удалить пользователя
3. Информация о пользователе
4. Сменить пароль
5. Список пользователей
6. Выход

Выберите действие: 3
Введите имя пользователя: root
Пользователь root найден:
  UID: 0
  GID: 0
  Домашняя директория: /root
  Оболочка: /bin/bash
```


---

### Подсказки и рекомендации

1. **Объявление функций в начале:**
   ```bash
   # Определяем все функции в начале скрипта
   function_name() {
       # тело функции
   }
   # Основная программа в конце
   ```

2. **Локальные vs глобальные переменные:**
   ```bash
   GLOBAL_VAR="global"  # Глобальная

   test_func() {
       local local_var="local"  # Локальная
       echo "$GLOBAL_VAR $local_var"
   }
   ```

3. **Возврат значения из функции:**
   ```bash
   # Способ 1: через echo (для данных)
   get_name() {
       echo "John Doe"
   }
   name=$(get_name)

   # Способ 2: через return (для статуса)
   check_file() {
       if [[ -f "$1" ]]; then
           return 0
       else
           return 1
       fi
   }
   if check_file "/etc/passwd"; then
       echo "Файл существует"
   fi
   ```

4. **Передача аргументов:**
   ```bash
   sum() {
       local result=$(( $1 + $2 ))
       echo "$result"
   }
   result=$(sum 5 10)
   ```

5. **Использование массива для результатов:**
   ```bash
   get_stats() {
       local file="$1"
       local lines=$(wc -l < "$file")
       local errors=$(grep -c "ERROR" "$file")
       echo "$lines $errors"
   }
   read lines errors <<< "$(get_stats "log.txt")"
   ```

---

### Эталонное решение (Вариант 1)

```bash
#!/usr/bin/env bash

# Лабораторная работа №7, Вариант 1
# Тема: Функции и модульная структура
# Менеджер файлов и бэкапов

# --- Глобальные переменные ---
BACKUP_DIR="${HOME}/backups"
RETENTION_DAYS=7
LOG_FILE="/tmp/file_manager.log"
TARGET_DIR=""

# --- Функция логирования ---
log_message() {
    local message="$1"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    echo "[$timestamp] $message" >> "$LOG_FILE"
}

# --- Функция проверки директории ---
check_directory() {
    local dir="$1"
    
    if [[ -z "$dir" ]]; then
        echo "Ошибка: не указана директория"
        return 1
    fi
    
    if [[ -d "$dir" ]]; then
        echo "✅ Директория $dir существует"
        log_message "Директория $dir существует"
        return 0
    else
        echo "⚠️ Директория $dir не существует"
        read -p "Создать директорию? (y/n): " CREATE
        
        if [[ "$CREATE" == "y" || "$CREATE" == "Y" ]]; then
            mkdir -p "$dir"
            if [[ $? -eq 0 ]]; then
                echo "✅ Директория создана: $dir"
                log_message "Создана директория: $dir"
                return 0
            else
                echo "❌ Ошибка создания директории"
                log_message "Ошибка создания директории: $dir"
                return 1
            fi
        else
            echo "❌ Операция отменена"
            return 1
        fi
    fi
}

# --- Функция получения информации о файле ---
get_file_info() {
    local file="$1"
    
    if [[ ! -f "$file" ]]; then
        echo "❌ Файл не существует: $file"
        return 1
    fi
    
    echo "📄 Файл: $(basename "$file")"
    echo "  Полный путь: $file"
    
    # Размер
    local size=$(stat -c%s "$file" 2>/dev/null || stat -f%z "$file" 2>/dev/null)
    echo "  Размер: $size байт"
    
    # Права доступа
    local perms=$(stat -c%A "$file" 2>/dev/null || stat -f%Sp "$file" 2>/dev/null)
    echo "  Права: $perms"
    
    # Дата изменения
    local mtime=$(stat -c%y "$file" 2>/dev/null | cut -d. -f1)
    echo "  Дата изменения: ${mtime:-недоступно}"
    
    # Владелец
    local owner=$(stat -c%U "$file" 2>/dev/null || echo "недоступно")
    echo "  Владелец: $owner"
    
    # Тип файла
    if [[ -L "$file" ]]; then
        echo "  Тип: символическая ссылка"
        local target=$(readlink "$file")
        echo "  Ссылка на: $target"
    elif [[ -f "$file" ]]; then
        echo "  Тип: обычный файл"
    elif [[ -d "$file" ]]; then
        echo "  Тип: директория"
    fi
    
    return 0
}

# --- Функция создания бэкапа ---
create_backup() {
    local source_dir="$1"
    
    if [[ ! -d "$source_dir" ]]; then
        echo "❌ Директория не существует: $source_dir"
        return 1
    fi
    
    # Создаем директорию для бэкапов
    if [[ ! -d "$BACKUP_DIR" ]]; then
        mkdir -p "$BACKUP_DIR"
        if [[ $? -ne 0 ]]; then
            echo "❌ Ошибка создания директории бэкапов"
            return 1
        fi
    fi
    
    local timestamp=$(date '+%Y%m%d_%H%M%S')
    local backup_name="backup_${timestamp}.tar.gz"
    local backup_path="${BACKUP_DIR}/${backup_name}"
    
    echo "📦 Создание бэкапа: $backup_name"
    log_message "Начало создания бэкапа: $backup_name"
    
    # Создаем архив
    tar -czf "$backup_path" -C "$(dirname "$source_dir")" "$(basename "$source_dir")" 2>/dev/null
    
    if [[ $? -eq 0 && -f "$backup_path" ]]; then
        local size=$(du -h "$backup_path" | cut -f1)
        echo "✅ Бэкап создан: $backup_path"
        echo "📊 Размер: $size"
        log_message "Бэкап создан: $backup_name ($size)"
        return 0
    else
        echo "❌ Ошибка создания бэкапа"
        log_message "Ошибка создания бэкапа: $backup_name"
        return 1
    fi
}

# --- Функция очистки старых бэкапов ---
cleanup_old_backups() {
    local days="$1"
    
    if [[ ! -d "$BACKUP_DIR" ]]; then
        echo "ℹ️ Директория бэкапов не существует"
        return 0
    fi
    
    echo "🧹 Поиск бэкапов старше $days дней..."
    log_message "Начало очистки бэкапов старше $days дней"
    
    # Находим старые бэкапы
    local old_backups=$(find "$BACKUP_DIR" -name "backup_*.tar.gz" -type f -mtime +"$days" 2>/dev/null)
    
    if [[ -z "$old_backups" ]]; then
        echo "✅ Старых бэкапов не найдено"
        return 0
    fi
    
    local count=$(echo "$old_backups" | wc -l)
    echo "Найдено старых бэкапов: $count"
    
    read -p "Удалить старые бэкапы? (y/n): " CONFIRM
    
    if [[ "$CONFIRM" != "y" && "$CONFIRM" != "Y" ]]; then
        echo "ℹ️ Очистка отменена"
        return 0
    fi
    
    local deleted=0
    echo "$old_backups" | while read -r file; do
        rm -f "$file"
        if [[ $? -eq 0 ]]; then
            ((deleted++))
            log_message "Удален бэкап: $(basename "$file")"
        fi
    done
    
    echo "✅ Удалено бэкапов: $deleted"
    log_message "Очистка завершена, удалено: $deleted"
    return 0
}

# --- Функция показа статистики ---
show_statistics() {
    local dir="$1"
    
    if [[ ! -d "$dir" ]]; then
        echo "❌ Директория не существует: $dir"
        return 1
    fi
    
    echo "📊 Статистика директории: $dir"
    echo "----------------------------------------"
    
    local files=0
    local dirs=0
    local total_size=0
    local hidden_files=0
    
    # Используем for для перебора элементов
    for item in "$dir"/* "$dir"/.*; do
        # Пропускаем . и ..
        if [[ "$(basename "$item")" == "." || "$(basename "$item")" == ".." ]]; then
            continue
        fi
        
        if [[ -f "$item" ]]; then
            ((files++))
            local size=$(stat -c%s "$item" 2>/dev/null || stat -f%z "$item" 2>/dev/null)
            total_size=$((total_size + size))
            
            # Проверка на скрытый файл
            if [[ "$(basename "$item")" == .* ]]; then
                ((hidden_files++))
            fi
        elif [[ -d "$item" ]]; then
            ((dirs++))
        fi
    done
    
    echo "  📄 Обычных файлов: $files"
    echo "  📁 Директорий: $dirs"
    echo "  👁️ Скрытых файлов: $hidden_files"
    echo "  💾 Общий размер: $((total_size / 1024)) КБ"
    
    # Проверка на пустую директорию
    if [[ $files -eq 0 && $dirs -eq 0 ]]; then
        echo "  ⚠️ Директория пуста"
    fi
    
    return 0
}

# --- Функция показа меню ---
show_menu() {
    echo ""
    echo "========================================="
    echo "    МЕНЕДЖЕР ФАЙЛОВ"
    echo "    Директория: $TARGET_DIR"
    echo "========================================="
    echo "1. Показать информацию о файле"
    echo "2. Создать бэкап"
    echo "3. Очистить старые бэкапы"
    echo "4. Показать статистику"
    echo "5. Выход"
    echo ""
}

# --- Основная программа ---

# Проверка аргументов
if [[ $# -ne 1 ]]; then
    echo "Использование: $0 <директория>"
    exit 1
fi

TARGET_DIR="$1"

# Проверка директории
if ! check_directory "$TARGET_DIR"; then
    echo "Ошибка при проверке директории"
    exit 1
fi

log_message "Запуск программы с директорией: $TARGET_DIR"

# Главный цикл
while true; do
    show_menu
    read -p "Выберите действие (1-5): " CHOICE
    
    case $CHOICE in
        1)
            echo ""
            read -p "Введите имя файла: " FILENAME
            get_file_info "${TARGET_DIR}/${FILENAME}"
            if [[ $? -eq 1 ]]; then
                echo "Ошибка при получении информации о файле"
            fi
            ;;
        2)
            echo ""
            create_backup "$TARGET_DIR"
            ;;
        3)
            echo ""
            read -p "Введите количество дней: " DAYS
            if [[ ! "$DAYS" =~ ^[0-9]+$ ]]; then
                echo "❌ Ошибка: введите число!"
                continue
            fi
            cleanup_old_backups "$DAYS"
            ;;
        4)
            echo ""
            show_statistics "$TARGET_DIR"
            ;;
        5)
            echo "Выход из программы"
            log_message "Программа завершена"
            exit 0
            ;;
        *)
            echo "❌ Неверный выбор!"
            ;;
    esac
    
    echo ""
    read -p "Нажмите Enter для продолжения..."
done
```

---

### Решения для остальных вариантов

#### Вариант 2 (Калькулятор с функциями)

```bash
#!/usr/bin/env bash

# Лабораторная работа №7, Вариант 2
# Тема: Калькулятор с функциями

# --- Глобальные переменные ---
HISTORY=()
HISTORY_MAX=100

# --- Функции операций ---

# Сложение
add() {
    local result=$(echo "$1 + $2" | bc -l)
    echo "$result"
}

# Вычитание
subtract() {
    local result=$(echo "$1 - $2" | bc -l)
    echo "$result"
}

# Умножение
multiply() {
    local result=$(echo "$1 * $2" | bc -l)
    echo "$result"
}

# Деление
divide() {
    if [[ $(echo "$2 == 0" | bc) -eq 1 ]]; then
        echo "Ошибка: деление на ноль!" >&2
        return 1
    fi
    local result=$(echo "scale=2; $1 / $2" | bc -l)
    echo "$result"
    return 0
}

# Возведение в степень
power() {
    local result=$(echo "$1 ^ $2" | bc -l)
    echo "$result"
}

# Остаток от деления
modulo() {
    local result=$(echo "$1 % $2" | bc)
    echo "$result"
}

# --- Вспомогательные функции ---

# Проверка числа
validate_number() {
    local num="$1"
    if [[ "$num" =~ ^-?[0-9]+(\.[0-9]+)?$ ]]; then
        return 0
    else
        echo "❌ Ошибка: '$num' не является числом!" >&2
        return 1
    fi
}

# Получение пользовательского ввода
get_user_input() {
    local input=""
    local num1=""
    local operator=""
    local num2=""
    
    while true; do
        read -p "> " input
        
        # Проверка команд
        case "$input" in
            exit|quit|q)
                echo "EXIT"
                return 1
                ;;
            history|hist|h)
                echo "HISTORY"
                return 0
                ;;
            clear|c)
                echo "CLEAR"
                return 0
                ;;
            help|\?)
                echo "HELP"
                return 0
                ;;
        esac
        
        # Разбор выражения
        read -r num1 operator num2 <<< "$input"
        
        if [[ -z "$num1" ]]; then
            echo "⏳ Введите выражение (например: 5 + 3)" >&2
            continue
        fi
        
        if [[ -z "$operator" || -z "$num2" ]]; then
            echo "❌ Ошибка: требуется 3 аргумента (число оператор число)" >&2
            continue
        fi
        
        # Проверка чисел
        if ! validate_number "$num1" || ! validate_number "$num2"; then
            continue
        fi
        
        # Возвращаем результат
        echo "$num1|$operator|$num2"
        return 0
    done
}

# Показать историю
show_history() {
    if [[ ${#HISTORY[@]} -eq 0 ]]; then
        echo "📋 История пуста"
        return 0
    fi
    
    echo "=== ИСТОРИЯ ВЫЧИСЛЕНИЙ ==="
    local count=0
    for entry in "${HISTORY[@]}"; do
        ((count++))
        printf "  %3d: %s\n" "$count" "$entry"
    done
}

# Очистить историю
clear_history() {
    HISTORY=()
    echo "✅ История очищена"
}

# Показать справку
show_help() {
    cat << EOF
Доступные команды:
  <число> <оператор> <число> - выполнить вычисление
  history, hist, h           - показать историю
  clear, c                   - очистить историю
  help, ?                    - показать справку
  exit, quit, q              - выход

Поддерживаемые операторы:
  +  - сложение
  -  - вычитание
  *  - умножение
  /  - деление
  ^  - возведение в степень
  %  - остаток от деления

Примеры:
  10 + 5
  2 ^ 10
  10 / 3
EOF
}

# Выполнить вычисление
calculate() {
    local num1="$1"
    local operator="$2"
    local num2="$3"
    local result=""
    
    case "$operator" in
        +)
            result=$(add "$num1" "$num2")
            ;;
        -)
            result=$(subtract "$num1" "$num2")
            ;;
        '*')
            result=$(multiply "$num1" "$num2")
            ;;
        /)
            result=$(divide "$num1" "$num2")
            local status=$?
            if [[ $status -ne 0 ]]; then
                return $status
            fi
            ;;
        ^)
            result=$(power "$num1" "$num2")
            ;;
        %)
            result=$(modulo "$num1" "$num2")
            ;;
        *)
            echo "❌ Неизвестный оператор: $operator" >&2
            return 1
            ;;
    esac
    
    # Форматирование результата
    if [[ $result != *"Ошибка"* ]]; then
        result=$(echo "$result" | sed 's/\.0*$//')
    fi
    
    echo "$result"
    return 0
}

# --- Основная программа ---

echo "========================================="
echo "    КАЛЬКУЛЯТОР (с функциями)"
echo "    Введите 'help' для справки"
echo "========================================="

while true; do
    # Получаем ввод
    input_result=$(get_user_input)
    local status=$?
    
    case "$input_result" in
        EXIT)
            echo "👋 До свидания!"
            exit 0
            ;;
        HISTORY)
            show_history
            continue
            ;;
        CLEAR)
            clear_history
            continue
            ;;
        HELP)
            show_help
            continue
            ;;
    esac
    
    if [[ $status -ne 0 || -z "$input_result" ]]; then
        continue
    fi
    
    # Разбираем результат
    IFS='|' read -r num1 operator num2 <<< "$input_result"
    
    # Вычисляем
    echo -n "Результат: "
    result=$(calculate "$num1" "$operator" "$num2")
    local calc_status=$?
    
    if [[ $calc_status -eq 0 ]]; then
        echo "$result"
        # Сохраняем в историю
        entry="$num1 $operator $num2 = $result"
        HISTORY+=("$entry")
        if [[ ${#HISTORY[@]} -gt $HISTORY_MAX ]]; then
            HISTORY=("${HISTORY[@]:1}")
        fi
    else
        echo "$result"
    fi
    
    echo ""
done
```

---

#### Вариант 3 (Мониторинг системы)

```bash
#!/usr/bin/env bash

# Лабораторная работа №7, Вариант 3
# Тема: Мониторинг системы с функциями

# --- Глобальные переменные ---
CPU_THRESHOLD=80
RAM_THRESHOLD=90
DISK_THRESHOLD=10
INTERVAL=5
LOG_FILE="/tmp/system_monitor.log"

# --- Функции ---

# Логирование
log_message() {
    local msg="$1"
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $msg" | tee -a "$LOG_FILE"
}

# Получить загрузку CPU
get_cpu_usage() {
    local idle=$(top -bn1 | grep "Cpu(s)" | awk '{print $8}' | cut -d'%' -f1)
    if [[ -z "$idle" ]]; then
        idle=$(mpstat 1 1 2>/dev/null | awk 'END {print 100 - $NF}')
    fi
    local usage=$(echo "100 - $idle" | bc 2>/dev/null | cut -d. -f1)
    echo "${usage:-0}"
}

# Получить использование RAM
get_ram_usage() {
    local total=$(free -m | awk '/^Mem:/ {print $2}')
    local used=$(free -m | awk '/^Mem:/ {print $3}')
    if [[ -z "$total" || "$total" -eq 0 ]]; then
        echo "0"
        return
    fi
    local usage=$((used * 100 / total))
    echo "$usage"
}

# Получить использование диска
get_disk_usage() {
    local usage=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')
    echo "${usage:-0}"
}

# Получить топ-5 процессов
get_top_processes() {
    echo "📊 Топ-5 процессов по CPU:"
    ps aux --sort=-%cpu | head -6 | tail -5 | while read -r line; do
        local cpu=$(echo "$line" | awk '{print $3}')
        local cmd=$(echo "$line" | awk '{print $11}' | cut -d'/' -f3 | cut -c1-20)
        printf "  %6s%% - %s\n" "$cpu" "$cmd"
    done
}

# Проверить процесс
check_process() {
    local proc_name="$1"
    if pgrep -x "$proc_name" >/dev/null 2>&1; then
        return 0
    else
        return 1
    fi
}

# Отправить уведомление
send_alert() {
    local level="$1"
    local message="$2"
    local color=""
    
    case "$level" in
        WARNING)
            color="\033[33m"  # Желтый
            ;;
        CRITICAL)
            color="\033[31m"  # Красный
            ;;
        *)
            color="\033[0m"
            ;;
    esac
    
    echo -e "${color}⚠️ $message\033[0m"
    log_message "ALERT [$level]: $message"
}

# Основная функция мониторинга
monitor() {
    log_message "Запуск мониторинга (интервал: ${INTERVAL}с)"
    echo "Запуск мониторинга (Ctrl+C для выхода)..."
    echo ""
    
    local iteration=0
    
    while true; do
        ((iteration++))
        
        # Получаем данные
        local cpu_usage=$(get_c
