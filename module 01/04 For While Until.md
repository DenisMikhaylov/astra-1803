
### Лабораторная работа №6: «Циклы и управление потоком выполнения»

**Цель работы:** Научиться использовать циклы `for`, `while`, `until` для автоматизации задач, комбинировать их с переменными и пользовательским вводом, а также управлять выполнением циклов с помощью `break` и `continue`.

**Теоретическая справка:**

**Цикл `for`** — используется для перебора списка элементов:
```bash
# Перебор списка
for переменная in список; do
    команды
done

# Числовой диапазон
for i in {1..10}; do
    echo "Шаг $i"
done

# Стиль C
for ((i=0; i<10; i++)); do
    echo "i = $i"
done
```

**Цикл `while`** — выполняется, пока условие истинно:
```bash
while условие; do
    команды
done
```

**Цикл `until`** — выполняется, пока условие ложно (противоположность `while`):
```bash
until условие; do
    команды
done
```

**Управление циклами:**
- `break` — немедленный выход из цикла
- `break N` — выход из N вложенных циклов
- `continue` — переход к следующей итерации
- `continue N` — переход к следующей итерации внешнего цикла

**Чтение ввода:** `read -p "Подсказка" переменная`

---

### Варианты заданий

#### Вариант 1 (Менеджер файлов и резервного копирования)

Напишите скрипт для управления файлами и создания резервных копий, который:

1. Принимает директорию как аргумент командной строки.
2. Проверяет, существует ли директория. Если нет — создаёт её.
3. Используя цикл `for`, перебирает все файлы в директории и поддиректориях (рекурсивно).
4. Для каждого файла выводит:
   - Имя файла
   - Размер (в байтах, КБ, МБ)
   - Тип файла (обычный, директория, симлинк)
5. Подсчитывает общее количество файлов и общий размер.
6. Использует цикл `while` для создания меню с выбором действий:
   - 1. Создать бэкап всех файлов (архив `.tar.gz`)
   - 2. Найти и удалить файлы старше N дней
   - 3. Найти и удалить пустые файлы
   - 4. Показать статистику
   - 5. Выход
7. Использует `break` для выхода из меню и `continue` для возврата к началу цикла при ошибках.
8. Использует цикл `until` для запроса корректного ввода от пользователя.

**Пример вывода:**
```
$ ./file_manager.sh /home/user/docs
Статистика директории /home/user/docs:
  Всего файлов: 45
  Всего директорий: 12
  Общий размер: 256 МБ

=== МЕНЮ УПРАВЛЕНИЯ ФАЙЛАМИ ===
1. Создать бэкап
2. Удалить файлы старше N дней
3. Удалить пустые файлы
4. Показать статистику
5. Выход

Выберите действие (1-5): 2
Введите количество дней: 7
Найдено файлов старше 7 дней: 5
Удалить их? (y/n): y
Файлы удалены.
```

---

#### Вариант 2 (Интерактивный калькулятор с историей)

Напишите скрипт-калькулятор с историей вычислений, который:

1. Использует цикл `while true` для бесконечного меню.
2. В каждой итерации:
   - Запрашивает у пользователя выражение (например, `5 + 3`)
   - Вычисляет результат (поддерживает `+`, `-`, `*`, `/`, `^`)
   - Сохраняет выражение и результат в массив истории
3. Использует `break` при вводе команды `exit` или `quit`.
4. Использует `continue` при некорректном вводе (повтор запроса).
5. Использует цикл `until` для проверки корректности ввода чисел.
6. Использует цикл `for` для вывода истории вычислений.
7. Добавляет команды:
   - `history` — показать все вычисления
   - `clear` — очистить историю
   - `exit` — выход

**Пример вывода:**
```
$ ./calc_with_history.sh
Калькулятор (введите 'exit' для выхода)
> 10 + 5
Результат: 15
> 2 ^ 10
Результат: 1024
> history
1: 10 + 5 = 15
2: 2 ^ 10 = 1024
> clear
История очищена
> exit
До свидания!
```

---

#### Вариант 3 (Мониторинг системы с уведомлениями)

Напишите скрипт мониторинга системы, который:

1. Использует цикл `while` для непрерывного мониторинга с интервалом в 5 секунд.
2. Проверяет:
   - Загрузку CPU (если > 80% — предупреждение)
   - Использование памяти (если > 90% — предупреждение)
   - Свободное место на диске (если < 10% — предупреждение)
   - Запущенные процессы (список топ-5 по CPU)
3. При обнаружении проблем показывает уведомление (вывод сообщения).
4. Использует `break` для выхода по нажатию `Ctrl+C` (ловушка `trap`).
5. Использует цикл `for` для перебора процессов и вычисления их загрузки.
6. Использует `continue` для пропуска проверок, если система не загружена (load average < 0.5).
7. Использует цикл `until` для ожидания запуска критического процесса (если он не запущен).

**Пример вывода:**
```
$ ./system_monitor.sh
Запуск мониторинга системы (Ctrl+C для выхода)...

[10:00:01] CPU: 45% | RAM: 60% | DISK: 75%
[10:00:06] CPU: 85% ⚠️ ВНИМАНИЕ: высокая загрузка CPU!
[10:00:06] Топ процессов по CPU:
  1. 25.5% - /usr/bin/firefox
  2. 18.2% - /usr/bin/gnome-shell
  3. 12.1% - /usr/bin/chrome
...
```

---

#### Вариант 4 (Игра "Угадай число")

Напишите скрипт-игру "Угадай число", который:

1. Генерирует случайное число от 1 до 100.
2. Использует цикл `while` для запроса попыток пользователя.
3. Использует `break` при правильном угадывании.
4. Использует `continue` для пропуска нечисловых значений.
5. Использует цикл `until` для запроса диапазона сложности (легкий 1-10, средний 1-100, сложный 1-1000).
6. Использует цикл `for` для вывода истории попыток в конце игры.
7. Подсчитывает количество попыток и выводит статистику.
8. Добавляет подсказки "Больше" или "Меньше".

**Пример вывода:**
```
$ ./guess_game.sh
Выберите сложность:
1. Легкий (1-10)
2. Средний (1-100)
3. Сложный (1-1000)
Ваш выбор: 2

Я загадал число от 1 до 100.
Попытка 1: 50
Меньше!
Попытка 2: 25
Больше!
Попытка 3: 37
Поздравляю! Вы угадали число 37 за 3 попытки!

История попыток:
1: 50 (Меньше)
2: 25 (Больше)
3: 37 (Правильно)
```

---

#### Вариант 5 (Обработчик логов и отчетов)

Напишите скрипт для анализа лог-файлов и генерации отчетов, который:

1. Использует цикл `for` для обработки нескольких лог-файлов, переданных как аргументы или найденных в директории.
2. Для каждого файла использует цикл `while read` для построчного анализа.
3. Подсчитывает:
   - Общее количество строк
   - Количество строк с `ERROR`
   - Количество строк с `WARNING`
   - Количество строк с `INFO`
   - Уникальные IP-адреса (если есть)
4. Использует цикл `until` для ожидания, пока лог-файл не станет доступным для чтения.
5. Использует `break` для выхода при обнаружении критической ошибки.
6. Использует `continue` для пропуска пустых строк.
7. Создает отчет с суммарной статистикой по всем файлам.
8. Использует цикл `while` для интерактивного меню работы с отчетами.

**Пример вывода:**
```
$ ./log_processor.sh /var/log/*.log
Обработка файлов: 3 файла

Файл 1: /var/log/syslog
  Всего строк: 1523
  Ошибок (ERROR): 45
  Предупреждений (WARNING): 89
  Информационных (INFO): 234

Файл 2: /var/log/auth.log
  Всего строк: 897
  Ошибок (ERROR): 12
  Предупреждений (WARNING): 34

Файл 3: /var/log/nginx/access.log
  Всего строк: 4562
  Уникальных IP: 125

=== ОБЩИЙ ОТЧЕТ ===
Всего строк: 6982
Всего ошибок: 57
Всего предупреждений: 123
Всего информационных: 234

Сохранить отчет в файл? (y/n): y
Отчет сохранен в report_20260622.txt
```

---

### Требования к оформлению

1. **Имя скрипта:** `lab_loops_variantN.sh` (где N — номер варианта).
2. **Шебанг:** `#!/usr/bin/env bash`.
3. **Комментарии:** Каждый логический блок должен сопровождаться комментариями.
4. **Обработка ошибок:** Все возможные ошибки должны обрабатываться.
5. **Коды возврата:** Успешное выполнение — `exit 0`, ошибки — `exit 1`, `exit 2` и т.д.
6. **Чистота вывода:** Без лишней отладочной информации.
7. **Обязательно использовать:** `for`, `while`, `until`, `break`, `continue`.

---


### Подсказки и рекомендации

1. **Бесконечный цикл с break:**
   ```bash
   while true; do
       read -p "Введите команду: " CMD
       case "$CMD" in
           exit|quit)
               break
               ;;
           *)
               echo "Выполняем: $CMD"
               ;;
       esac
   done
   ```

2. **Проверка числа с until:**
   ```bash
   until [[ "$INPUT" =~ ^[0-9]+$ ]]; do
       read -p "Введите число: " INPUT
       [[ ! "$INPUT" =~ ^[0-9]+$ ]] && echo "Ошибка: введите число!"
   done
   ```

3. **Continue для пропуска ошибок:**
   ```bash
   for file in *.txt; do
       if [[ ! -f "$file" ]]; then
           continue  # Пропускаем, если файла нет
       fi
       echo "Обработка: $file"
   done
   ```

4. **Чтение файла построчно:**
   ```bash
   while IFS= read -r line; do
       # Обработка строки
       case "$line" in
           *ERROR*)
               ((error_count++))
               ;;
       esac
   done < "$log_file"
   ```

5. **Таймаут в цикле:**
   ```bash
   while true; do
       # Проверка условий
       sleep 5  # Пауза 5 секунд
   done
   ```

6. **Счетчик итераций:**
   ```bash
   COUNTER=0
   while [[ $COUNTER -lt 10 ]]; do
       ((COUNTER++))
       echo "Итерация $COUNTER"
   done
   ```

---

### Эталонное решение (Вариант 1)

```bash
#!/usr/bin/env bash

# Лабораторная работа №6, Вариант 1
# Тема: Циклы и управление потоком (Менеджер файлов)

# --- Конфигурация ---
BACKUP_DIR="${HOME}/backups"
LOG_FILE="/tmp/file_manager.log"

# --- Функции ---

# Функция логирования
log_message() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" >> "$LOG_FILE"
}

# Функция получения размера файла в человекочитаемом виде
human_readable_size() {
    local size=$1
    if [[ $size -gt 1048576 ]]; then
        echo "$((size / 1048576)) МБ"
    elif [[ $size -gt 1024 ]]; then
        echo "$((size / 1024)) КБ"
    else
        echo "${size} байт"
    fi
}

# Функция подсчета статистики
calculate_stats() {
    local dir="$1"
    local file_count=0
    local dir_count=0
    local total_size=0
    
    echo "Подсчет статистики для $dir..."
    
    # Используем for для перебора всех элементов
    for item in "$dir"/*; do
        if [[ -f "$item" ]]; then
            ((file_count++))
            total_size=$((total_size + $(stat -c%s "$item" 2>/dev/null || stat -f%z "$item" 2>/dev/null)))
        elif [[ -d "$item" ]]; then
            ((dir_count++))
        fi
    done
    
    echo "Статистика директории $dir:"
    echo "  Всего файлов: $file_count"
    echo "  Всего директорий: $dir_count"
    echo "  Общий размер: $(human_readable_size $total_size)"
    
    # Сохраняем в глобальные переменные для использования в других функциях
    TOTAL_FILES=$file_count
    TOTAL_DIRS=$dir_count
    TOTAL_SIZE=$total_size
}

# Функция создания бэкапа
create_backup() {
    local dir="$1"
    local timestamp=$(date '+%Y%m%d_%H%M%S')
    local backup_name="backup_${timestamp}.tar.gz"
    local backup_path="${BACKUP_DIR}/${backup_name}"
    
    echo "Создание бэкапа: $backup_name"
    log_message "Начало создания бэкапа: $backup_name"
    
    # Создаем директорию для бэкапов если её нет
    if [[ ! -d "$BACKUP_DIR" ]]; then
        mkdir -p "$BACKUP_DIR"
    fi
    
    # Создаем архив
    tar -czf "$backup_path" -C "$(dirname "$dir")" "$(basename "$dir")" 2>/dev/null
    
    if [[ $? -eq 0 ]]; then
        local size=$(du -h "$backup_path" | cut -f1)
        echo "✅ Бэкап создан: $backup_path"
        echo "📦 Размер: $size"
        log_message "Бэкап создан: $backup_name ($size)"
        return 0
    else
        echo "❌ Ошибка создания бэкапа!"
        log_message "Ошибка создания бэкапа: $backup_name"
        return 1
    fi
}

# Функция удаления файлов старше N дней
delete_old_files() {
    local dir="$1"
    local days="$2"
    
    echo "Поиск файлов старше $days дней в $dir..."
    
    # Находим файлы старше N дней
    local old_files=$(find "$dir" -type f -mtime +"$days" 2>/dev/null)
    
    if [[ -z "$old_files" ]]; then
        echo "Файлов старше $days дней не найдено."
        return 0
    fi
    
    local count=$(echo "$old_files" | wc -l)
    echo "Найдено файлов старше $days дней: $count"
    
    # Показываем список файлов
    echo "Список файлов:"
    echo "$old_files" | while IFS= read -r file; do
        echo "  - $(basename "$file") ($(human_readable_size $(stat -c%s "$file" 2>/dev/null || stat -f%z "$file" 2>/dev/null)))"
    done
    
    read -p "Удалить эти файлы? (y/n): " CONFIRM
    
    if [[ "$CONFIRM" == "y" || "$CONFIRM" == "Y" ]]; then
        echo "$old_files" | while IFS= read -r file; do
            rm -f "$file"
            if [[ $? -eq 0 ]]; then
                echo "Удален: $file"
                log_message "Удален файл: $file"
            fi
        done
        echo "✅ Удалено файлов: $count"
    else
        echo "Операция отменена."
    fi
}

# Функция удаления пустых файлов
delete_empty_files() {
    local dir="$1"
    
    echo "Поиск пустых файлов в $dir..."
    
    local empty_files=$(find "$dir" -type f -empty 2>/dev/null)
    
    if [[ -z "$empty_files" ]]; then
        echo "Пустых файлов не найдено."
        return 0
    fi
    
    local count=$(echo "$empty_files" | wc -l)
    echo "Найдено пустых файлов: $count"
    
    read -p "Удалить пустые файлы? (y/n): " CONFIRM
    
    if [[ "$CONFIRM" == "y" || "$CONFIRM" == "Y" ]]; then
        echo "$empty_files" | while IFS= read -r file; do
            rm -f "$file"
            if [[ $? -eq 0 ]]; then
                echo "Удален: $file"
                log_message "Удален пустой файл: $file"
            fi
        done
        echo "✅ Удалено пустых файлов: $count"
    else
        echo "Операция отменена."
    fi
}

# --- Проверка аргументов ---
if [[ -z "$1" ]]; then
    echo "Ошибка: не указана директория"
    echo "Использование: $0 <директория>"
    exit 1
fi

TARGET_DIR="$1"

# Проверка существования директории
if [[ ! -d "$TARGET_DIR" ]]; then
    echo "Директория $TARGET_DIR не существует. Создать? (y/n): "
    read -p "> " CREATE_DIR
    
    if [[ "$CREATE_DIR" == "y" || "$CREATE_DIR" == "Y" ]]; then
        mkdir -p "$TARGET_DIR"
        if [[ $? -eq 0 ]]; then
            echo "✅ Директория создана: $TARGET_DIR"
            log_message "Создана директория: $TARGET_DIR"
        else
            echo "❌ Ошибка создания директории!"
            exit 1
        fi
    else
        echo "Выход..."
        exit 0
    fi
fi

# --- Главный цикл меню ---

echo "========================================="
echo "    МЕНЕДЖЕР ФАЙЛОВ"
echo "    Директория: $TARGET_DIR"
echo "========================================="

# Подсчет статистики при старте
calculate_stats "$TARGET_DIR"

while true; do
    echo ""
    echo "=== МЕНЮ УПРАВЛЕНИЯ ФАЙЛАМИ ==="
    echo "1. Создать бэкап всех файлов"
    echo "2. Удалить файлы старше N дней"
    echo "3. Удалить пустые файлы"
    echo "4. Показать статистику"
    echo "5. Выход"
    echo ""
    
    read -p "Выберите действие (1-5): " CHOICE
    
    case "$CHOICE" in
        1)
            create_backup "$TARGET_DIR"
            ;;
        2)
            # Используем until для проверки корректности ввода
            days=""
            until [[ "$days" =~ ^[0-9]+$ ]]; do
                read -p "Введите количество дней: " days
                if [[ ! "$days" =~ ^[0-9]+$ ]]; then
                    echo "❌ Ошибка: введите число!"
                    continue
                fi
            done
            delete_old_files "$TARGET_DIR" "$days"
            ;;
        3)
            delete_empty_files "$TARGET_DIR"
            ;;
        4)
            calculate_stats "$TARGET_DIR"
            ;;
        5)
            echo "Выход из программы..."
            log_message "Программа завершена"
            break
            ;;
        *)
            echo "❌ Неверный выбор! Попробуйте снова."
            continue
            ;;
    esac
    
    # Пауза для чтения результата
    echo ""
    read -p "Нажмите Enter для продолжения..."
done

exit 0
```

---

### Решения для остальных вариантов

#### Вариант 2 (Интерактивный калькулятор с историей)

```bash
#!/usr/bin/env bash

# Лабораторная работа №6, Вариант 2
# Тема: Интерактивный калькулятор с историей

# --- Инициализация ---
HISTORY=()
HISTORY_INDEX=0
CALCULATOR_NAME="Bash Calculator v2.0"

# --- Функции ---

# Функция проверки числа
is_number() {
    [[ "$1" =~ ^-?[0-9]+(\.[0-9]+)?$ ]]
}

# Функция вычисления
calculate() {
    local num1="$1"
    local operator="$2"
    local num2="$3"
    local result=""
    
    case "$operator" in
        +)
            result=$(echo "$num1 + $num2" | bc -l)
            ;;
        -)
            result=$(echo "$num1 - $num2" | bc -l)
            ;;
        '*')
            result=$(echo "$num1 * $num2" | bc -l)
            ;;
        /)
            if [[ $(echo "$num2 == 0" | bc) -eq 1 ]]; then
                echo "Ошибка: деление на ноль!"
                return 1
            fi
            result=$(echo "scale=2; $num1 / $num2" | bc -l)
            ;;
        ^)
            result=$(echo "$num1 ^ $num2" | bc -l)
            ;;
        %)
            result=$(echo "$num1 % $num2" | bc)
            ;;
        *)
            echo "Неизвестный оператор: $operator"
            return 1
            ;;
    esac
    
    # Форматирование результата
    result=$(echo "$result" | sed 's/\.0*$//')
    echo "$result"
    return 0
}

# Функция показа истории
show_history() {
    if [[ ${#HISTORY[@]} -eq 0 ]]; then
        echo "История пуста."
        return
    fi
    
    echo "=== ИСТОРИЯ ВЫЧИСЛЕНИЙ ==="
    for i in "${!HISTORY[@]}"; do
        printf "%3d: %s\n" "$((i+1))" "${HISTORY[$i]}"
    done
}

# Функция очистки истории
clear_history() {
    HISTORY=()
    echo "История очищена."
}

# --- Основная программа ---

echo "========================================="
echo "    $CALCULATOR_NAME"
echo "    Введите 'help' для справки"
echo "========================================="

# Используем while true для бесконечного цикла
while true; do
    echo ""
    read -p "> " input
    
    # Проверка команд
    case "$input" in
        exit|quit|q)
            echo "До свидания!"
            break
            ;;
        help|h|?)
            echo "Доступные команды:"
            echo "  <число> <оператор> <число> - выполнить вычисление"
            echo "  history - показать историю"
            echo "  clear - очистить историю"
            echo "  exit - выход"
            echo "Поддерживаемые операторы: +, -, *, /, ^, %"
            continue
            ;;
        history|hist|h)
            show_history
            continue
            ;;
        clear|c)
            clear_history
            continue
            ;;
    esac
    
    # Парсинг выражения
    read -r num1 operator num2 <<< "$input"
    
    # Проверка на пустой ввод
    if [[ -z "$num1" ]]; then
        continue
    fi
    
    # Проверка количества аргументов
    if [[ -z "$operator" || -z "$num2" ]]; then
        echo "Ошибка: требуется 3 аргумента (число оператор число)"
        echo "Пример: 5 + 3"
        continue
    fi
    
    # Проверка чисел (используем until для демонстрации)
    until is_number "$num1"; do
        echo "Ошибка: '$num1' не является числом!"
        read -p "Введите первое число: " num1
    done
    
    until is_number "$num2"; do
        echo "Ошибка: '$num2' не является числом!"
        read -p "Введите второе число: " num2
    done
    
    # Вычисление
    echo -n "Результат: "
    result=$(calculate "$num1" "$operator" "$num2")
    
    if [[ $? -eq 0 ]]; then
        echo "$result"
        # Сохраняем в историю
        HISTORY+=("$num1 $operator $num2 = $result")
    else
        echo "$result"
        continue
    fi
    
    # Используем for для демонстрации (показываем последнюю запись)
    echo ""
    echo "Последние вычисления:"
    for ((i=${#HISTORY[@]}-3; i<${#HISTORY[@]}; i++)); do
        if [[ $i -ge 0 ]]; then
            echo "  ${HISTORY[$i]}"
        fi
    done
    
done

exit 0
```

---

#### Вариант 3 (Мониторинг системы с уведомлениями)

```bash
#!/usr/bin/env bash

# Лабораторная работа №6, Вариант 3
# Тема: Мониторинг системы с уведомлениями

# --- Конфигурация ---
INTERVAL=5
CPU_THRESHOLD=80
RAM_THRESHOLD=90
DISK_THRESHOLD=10
LOG_FILE="/tmp/system_monitor.log"

# --- Функции ---

# Функция логирования
log_message() {
    echo "[$(date '+%H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

# Функция получения загрузки CPU
get_cpu_usage() {
    # Используем top для получения загрузки
    local idle=$(top -bn1 | grep "Cpu(s)" | awk '{print $8}' | cut -d'%' -f1)
    local usage=$(echo "100 - $idle" | bc 2>/dev/null | cut -d. -f1)
    echo "${usage:-0}"
}

# Функция получения использования памяти
get_ram_usage() {
    local total=$(free -m | awk '/^Mem:/ {print $2}')
    local used=$(free -m | awk '/^Mem:/ {print $3}')
    local usage=$((used * 100 / total))
    echo "$usage"
}

# Функция получения свободного места на диске
get_disk_usage() {
    local usage=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')
    echo "${usage:-0}"
}

# Функция получения топ-5 процессов по CPU
get_top_processes() {
    echo "Топ процессов по CPU:"
    ps aux --sort=-%cpu | head -6 | tail -5 | while IFS= read -r line; do
        local cpu=$(echo "$line" | awk '{print $3}')
        local cmd=$(echo "$line" | awk '{print $11}')
        printf "  %6s%% - %s\n" "$cpu" "$cmd"
    done
}

# Функция проверки критических процессов
check_critical_process() {
    local process="$1"
    local found=0
    
    # Используем for для перебора процессов
    for pid in $(pgrep "$process" 2>/dev/null); do
        found=1
        break
    done
    
    if [[ $found -eq 1 ]]; then
        log_message "✅ Критический процесс $process запущен"
        return 0
    else
        log_message "❌ Критический процесс $process НЕ запущен!"
        return 1
    fi
}

# --- Основная программа ---

echo "========================================="
echo "    СИСТЕМНЫЙ МОНИТОРИНГ"
echo "    Интервал: $INTERVAL секунд"
echo "    Нажмите Ctrl+C для выхода"
echo "========================================="

log_message "Запуск мониторинга"

# Обработчик Ctrl+C
trap 'log_message "Мониторинг остановлен"; echo ""; echo "Мониторинг остановлен"; exit 0' INT TERM

# Проверка критических процессов (используем until для демонстрации)
log_message "Проверка критических процессов..."

critical_processes=("sshd" "cron" "rsyslog")

for process in "${critical_processes[@]}"; do
    until pgrep -x "$process" >/dev/null 2>&1; do
        log_message "⏳ Ожидание запуска процесса $process..."
        sleep 2
    done
    log_message "✅ Процесс $process запущен"
done

# Основной цикл мониторинга
iteration=0
while true; do
    ((iteration++))
    
    # Получение данных
    cpu_usage=$(get_cpu_usage)
    ram_usage=$(get_ram_usage)
    disk_usage=$(get_disk_usage)
    
    # Формирование строки состояния
    status_line="[$iteration] CPU: ${cpu_usage}% | RAM: ${ram_usage}% | DISK: ${disk_usage}%"
    
    # Проверка условий
    warnings=0
    
    # Проверка CPU
    if [[ $cpu_usage -gt $CPU_THRESHOLD ]]; then
        log_message "⚠️ ВНИМАНИЕ: высокая загрузка CPU: ${cpu_usage}% (порог: ${CPU_THRESHOLD}%)"
        ((warnings++))
    fi
    
    # Проверка RAM
    if [[ $ram_usage -gt $RAM_THRESHOLD ]]; then
        log_message "⚠️ ВНИМАНИЕ: высокая загрузка памяти: ${ram_usage}% (порог: ${RAM_THRESHOLD}%)"
        ((warnings++))
    fi
    
    # Проверка диска
    if [[ $disk_usage -gt $((100 - DISK_THRESHOLD)) ]]; then
        log_message "⚠️ ВНИМАНИЕ: мало свободного места на диске: ${disk_usage}% (свободно: $((100 - disk_usage))%)"
        ((warnings++))
    fi
    
    # Вывод статуса
    if [[ $warnings -gt 0 ]]; then
        echo -e "\033[31m$status_line ⚠️\033[0m"
        # Показываем топ процессов при проблемах
        get_top_processes
    else
        # Используем continue для пропуска подробного вывода при низкой загрузке
        if [[ $cpu_usage -lt 20 && $ram_usage -lt 50 ]]; then
            echo -e "\033[32m$status_line ✅\033[0m"
            continue  # Пропускаем вывод топ процессов при низкой загрузке
        fi
        echo "$status_line"
    fi
    
    # Пауза перед следующим циклом
    sleep $INTERVAL
done

exit 0
```

