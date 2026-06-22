
### Лабораторная работа «Оператор case и обработка множественного выбора»

**Цель работы:** Научиться использовать оператор `case` для обработки множества вариантов выбора, освоить шаблоны и паттерны, а также комбинировать `case` с другими конструкциями для создания интерактивных скриптов.

**Теоретическая справка:**

Оператор `case` в Bash используется для множественного ветвления на основе сравнения строки с шаблонами.

**Синтаксис:**
```bash
case $переменная in
    шаблон1)
        команды
        ;;
    шаблон2|шаблон3)
        команды
        ;;
    *)
        команды (выполняется, если ничего не подошло)
        ;;
esac
```

**Специальные символы в шаблонах:**
- `*` — любая строка (ноль или более символов)
- `?` — ровно один любой символ
- `[...]` — любой символ из набора (например, `[0-9]`)
- `|` — ИЛИ (разделяет несколько шаблонов)

**Примеры шаблонов:**
- `start|begin|go)` — совпадает с "start", "begin" или "go"
- `[Yy]|[Yy][Ee][Ss])` — совпадает с "y", "Y", "yes", "YES" и т.д.
- `*.txt)` — совпадает с любой строкой, заканчивающейся на ".txt"
- `[0-9][0-9][0-9])` — совпадает с трёхзначным числом

**Важно:** В `case` используется сопоставление с шаблоном **имён файлов** (globbing), а не регулярных выражений!

---

### Варианты заданий

#### Вариант 1 (Системный администратор)

Напишите скрипт для управления службами, который:
1. Принимает два аргумента: имя службы и действие (`start`, `stop`, `restart`, `status`).
2. Проверяет, что переданы оба аргумента. Если нет — выводит справку.
3. Использует `case` для обработки действий:
   - `start` — запускает службу (`systemctl start`)
   - `stop` — останавливает службу (`systemctl stop`)
   - `restart` — перезапускает службу (`systemctl restart`)
   - `status` — показывает статус службы (`systemctl status`)
4. Обрабатывает вариант, когда служба не указана.
5. Проверяет, существует ли служба (используйте `systemctl list-unit-files | grep`).
6. Выводит цветные сообщения о результате операции (используйте `echo -e` с кодами ANSI).

**Пример вывода:**
```
$ ./service_manager.sh nginx start
Запуск службы nginx...
Служба nginx успешно запущена

$ ./service_manager.sh sshd status
Статус службы sshd:
● sshd.service - OpenSSH server daemon
   Active: active (running)

$ ./service_manager.sh none restart
Ошибка: служба none не найдена!
```

---

#### Вариант 2 (Калькулятор с расширенными возможностями)

Напишите скрипт-калькулятор, который:
1. Принимает два числа и оператор в любом порядке (поддерживает `+`, `-`, `*`, `/`, `^`, `%`).
2. Использует `case` для обработки операторов.
3. Для каждого оператора выполняет соответствующую операцию:
   - `+` — сложение
   - `-` — вычитание
   - `*` — умножение
   - `/` — деление (с проверкой на ноль)
   - `^` — возведение в степень (используйте `bc`)
   - `%` — остаток от деления
4. Если оператор не распознан — выводит сообщение об ошибке и список доступных операторов.
5. Для деления проверяет, что делитель не равен нулю.
6. Для возведения в степень использует `bc -l` для поддержки дробных чисел.

**Пример вывода:**
```
$ ./calc_extended.sh 10 / 3
10 / 3 = 3.33

$ ./calc_extended.sh 2 ^ 10
2 ^ 10 = 1024

$ ./calc_extended.sh 10 % 3
10 % 3 = 1
```

---

#### Вариант 3 (Анализатор логов)

Напишите скрипт для анализа лог-файлов, который:
1. Принимает путь к лог-файлу и команду (`info`, `error`, `warning`, `count`).
2. Проверяет, что файл существует. Если нет — выводит сообщение об ошибке.
3. Использует `case` для обработки команд:
   - `info` — показывает все строки с `INFO`
   - `error` — показывает все строки с `ERROR`
   - `warning` — показывает все строки с `WARNING`
   - `count` — подсчитывает общее количество строк и количество строк каждого уровня
4. Для команд `info`, `error`, `warning` использует `grep` для фильтрации.
5. Для команды `count` использует `grep -c` для каждого уровня и выводит статистику.
6. Обрабатывает ситуацию, когда лог-файл пустой.

**Пример лог-файла:**
```
2026-06-22 10:00:01 INFO Система запущена
2026-06-22 10:05:23 WARNING Высокая нагрузка
2026-06-22 10:10:45 ERROR Не удалось подключиться к БД
```

**Пример вывода:**
```
$ ./log_analyzer.sh app.log error
2026-06-22 10:10:45 ERROR Не удалось подключиться к БД

$ ./log_analyzer.sh app.log count
Всего строк: 15
  INFO: 8
  WARNING: 5
  ERROR: 2
```

---

#### Вариант 4 (Интерактивное меню управления пользователями)

Напишите скрипт с интерактивным меню для управления пользователями, который:
1. Использует `case` внутри цикла `while true` (или `until`) для создания меню.
2. Меню должно содержать пункты:
   1. Создать пользователя
   2. Удалить пользователя
   3. Показать информацию о пользователе
   4. Сменить пароль пользователя
   5. Показать список пользователей
   6. Выход
3. Каждый пункт меню реализован в отдельной функции.
4. Для ввода данных использует `read -p`.
5. Проверяет наличие прав root для операций создания/удаления/смены пароля.
6. Использует `case` для обработки выбора пользователя.
7. Обрабатывает некорректный ввод (буквы вместо цифр).

**Пример вывода:**
```
=== УПРАВЛЕНИЕ ПОЛЬЗОВАТЕЛЯМИ ===
1. Создать пользователя
2. Удалить пользователя
3. Показать информацию о пользователе
4. Сменить пароль пользователя
5. Показать список пользователей
6. Выход

Выберите пункт (1-6): 3
Введите имя пользователя: root
Пользователь root:
  UID: 0
  GID: 0
  Домашняя директория: /root
  Оболочка: /bin/bash
```




### Подсказки и рекомендации

1. **Проверка на соответствие шаблону:**
   ```bash
   case "$VAR" in
       [0-9]*)
           echo "Начинается с цифры"
           ;;
       [A-Z]*)
           echo "Начинается с заглавной буквы"
           ;;
   esac
   ```

2. **Обработка нескольких значений:**
   ```bash
   case "$ANSWER" in
       y|Y|yes|YES|Yes)
           echo "Подтверждено"
           ;;
       n|N|no|NO|No)
           echo "Отклонено"
           ;;
       *)
           echo "Неверный ввод"
           ;;
   esac
   ```

3. **Проверка целого числа:**
   ```bash
   case "$NUM" in
       ''|*[!0-9]*)
           echo "Не число"
           ;;
       *)
           echo "Число: $NUM"
           ;;
   esac
   ```

4. **Обработка опций командной строки:**
   ```bash
   while [[ $# -gt 0 ]]; do
       case "$1" in
           -f|--file)
               FILE="$2"
               shift 2
               ;;
           -d|--debug)
               DEBUG=1
               shift
               ;;
           *)
               echo "Неизвестная опция: $1"
               exit 1
               ;;
       esac
   done
   ```

---

### Эталонное решение (Вариант 1)

```bash
#!/usr/bin/env bash

# Лабораторная работа №5, Вариант 1
# Тема: Управление службами с использованием case

# Цвета для вывода
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

# Функция вывода справки
show_help() {
    echo "Использование: $0 <имя_службы> <действие>"
    echo ""
    echo "Действия:"
    echo "  start    - Запустить службу"
    echo "  stop     - Остановить службу"
    echo "  restart  - Перезапустить службу"
    echo "  status   - Показать статус службы"
    echo "  enable   - Включить автозагрузку"
    echo "  disable  - Отключить автозагрузку"
    echo ""
    echo "Примеры:"
    echo "  $0 nginx start"
    echo "  $0 sshd status"
}

# Проверка количества аргументов
if [[ $# -ne 2 ]]; then
    echo -e "${RED}Ошибка: требуется 2 аргумента${NC}"
    show_help
    exit 1
fi

SERVICE="$1"
ACTION="$2"

# Функция проверки существования службы
check_service() {
    if systemctl list-unit-files --type=service | grep -q "^${SERVICE}\.service"; then
        return 0
    elif systemctl list-unit-files | grep -q "^${SERVICE}\.service"; then
        return 0
    else
        return 1
    fi
}

# Проверка существования службы
if ! check_service; then
    echo -e "${RED}❌ Ошибка: служба '$SERVICE' не найдена!${NC}"
    echo ""
    echo "Доступные службы (первые 10):"
    systemctl list-unit-files --type=service | head -10
    exit 1
fi

# Основной case для обработки действий
case "$ACTION" in
    start)
        echo -e "${YELLOW}Запуск службы $SERVICE...${NC}"
        if systemctl start "$SERVICE" 2>/dev/null; then
            echo -e "${GREEN}✅ Служба $SERVICE успешно запущена${NC}"
        else
            echo -e "${RED}❌ Ошибка при запуске службы $SERVICE${NC}"
            exit 1
        fi
        ;;
        
    stop)
        echo -e "${YELLOW}Остановка службы $SERVICE...${NC}"
        if systemctl stop "$SERVICE" 2>/dev/null; then
            echo -e "${GREEN}✅ Служба $SERVICE остановлена${NC}"
        else
            echo -e "${RED}❌ Ошибка при остановке службы $SERVICE${NC}"
            exit 1
        fi
        ;;
        
    restart)
        echo -e "${YELLOW}Перезапуск службы $SERVICE...${NC}"
        if systemctl restart "$SERVICE" 2>/dev/null; then
            echo -e "${GREEN}✅ Служба $SERVICE перезапущена${NC}"
        else
            echo -e "${RED}❌ Ошибка при перезапуске службы $SERVICE${NC}"
            exit 1
        fi
        ;;
        
    status)
        echo -e "${YELLOW}Статус службы $SERVICE:${NC}"
        echo ""
        systemctl status "$SERVICE" 2>/dev/null || echo -e "${RED}Ошибка получения статуса${NC}"
        ;;
        
    enable)
        echo -e "${YELLOW}Включение автозагрузки для $SERVICE...${NC}"
        if systemctl enable "$SERVICE" 2>/dev/null; then
            echo -e "${GREEN}✅ Автозагрузка для $SERVICE включена${NC}"
        else
            echo -e "${RED}❌ Ошибка при включении автозагрузки${NC}"
            exit 1
        fi
        ;;
        
    disable)
        echo -e "${YELLOW}Отключение автозагрузки для $SERVICE...${NC}"
        if systemctl disable "$SERVICE" 2>/dev/null; then
            echo -e "${GREEN}✅ Автозагрузка для $SERVICE отключена${NC}"
        else
            echo -e "${RED}❌ Ошибка при отключении автозагрузки${NC}"
            exit 1
        fi
        ;;
        
    *)
        echo -e "${RED}❌ Ошибка: неизвестное действие '$ACTION'${NC}"
        echo ""
        show_help
        exit 1
        ;;
esac

exit 0
```

---

### Решения для остальных вариантов

#### Вариант 2 (Калькулятор с расширенными возможностями)

```bash
#!/usr/bin/env bash

# Лабораторная работа №5, Вариант 2
# Тема: Калькулятор с case

# Функция проверки числа (целое или дробное)
is_number() {
    case "$1" in
        ''|*[!0-9.]*)
            return 1
            ;;
        *)
            return 0
            ;;
    esac
}

# Проверка количества аргументов
if [[ $# -ne 3 ]]; then
    echo "Ошибка: требуется 3 аргумента"
    echo "Использование: $0 <число1> <оператор> <число2>"
    echo "Операторы: +, -, *, /, ^, %"
    exit 1
fi

NUM1="$1"
OPERATOR="$2"
NUM2="$3"

# Проверка чисел
if ! is_number "$NUM1"; then
    echo "Ошибка: '$NUM1' не является числом!"
    exit 1
fi

if ! is_number "$NUM2"; then
    echo "Ошибка: '$NUM2' не является числом!"
    exit 1
fi

# Обработка оператора с помощью case
case "$OPERATOR" in
    +)
        RESULT=$(echo "$NUM1 + $NUM2" | bc -l)
        ;;
    -)
        RESULT=$(echo "$NUM1 - $NUM2" | bc -l)
        ;;
    '*')
        RESULT=$(echo "$NUM1 * $NUM2" | bc -l)
        ;;
    /)
        # Проверка деления на ноль
        if [[ $(echo "$NUM2 == 0" | bc) -eq 1 ]]; then
            echo "Ошибка: деление на ноль!"
            exit 1
        fi
        RESULT=$(echo "scale=2; $NUM1 / $NUM2" | bc -l)
        ;;
    ^)
        RESULT=$(echo "$NUM1 ^ $NUM2" | bc -l)
        ;;
    %)
        RESULT=$(echo "$NUM1 % $NUM2" | bc)
        ;;
    *)
        echo "Ошибка: неподдерживаемый оператор '$OPERATOR'"
        echo "Доступные операторы: +, -, *, /, ^, %"
        exit 1
        ;;
esac

# Форматирование результата (убираем лишние нули)
RESULT=$(echo "$RESULT" | sed 's/\.0*$//')

echo "$NUM1 $OPERATOR $NUM2 = $RESULT"
exit 0
```

---

#### Вариант 3 (Анализатор логов)

```bash
#!/usr/bin/env bash

# Лабораторная работа №5, Вариант 3
# Тема: Анализатор логов с case

# Проверка аргументов
if [[ $# -ne 2 ]]; then
    echo "Ошибка: требуется 2 аргумента"
    echo "Использование: $0 <лог-файл> <команда>"
    echo "Команды: info, error, warning, count"
    exit 1
fi

LOG_FILE="$1"
COMMAND="$2"

# Проверка существования файла
if [[ ! -f "$LOG_FILE" ]]; then
    echo "Ошибка: файл $LOG_FILE не существует!"
    exit 1
fi

# Проверка, не пустой ли файл
if [[ ! -s "$LOG_FILE" ]]; then
    echo "Предупреждение: лог-файл пустой!"
    exit 0
fi

# Обработка команд с помощью case
case "$COMMAND" in
    info)
        echo -e "\033[0;34m=== INFO записи ===\033[0m"
        grep -i "INFO" "$LOG_FILE" || echo "INFO записи не найдены"
        ;;
        
    error)
        echo -e "\033[0;31m=== ERROR записи ===\033[0m"
        grep -i "ERROR" "$LOG_FILE" || echo "ERROR записи не найдены"
        ;;
        
    warning)
        echo -e "\033[0;33m=== WARNING записи ===\033[0m"
        grep -i "WARNING" "$LOG_FILE" || echo "WARNING записи не найдены"
        ;;
        
    count)
        echo "=== Статистика лога ==="
        TOTAL=$(wc -l < "$LOG_FILE")
        INFO_COUNT=$(grep -ci "INFO" "$LOG_FILE")
        ERROR_COUNT=$(grep -ci "ERROR" "$LOG_FILE")
        WARNING_COUNT=$(grep -ci "WARNING" "$LOG_FILE")
        
        echo "Всего строк: $TOTAL"
        echo "  INFO: $INFO_COUNT"
        echo "  ERROR: $ERROR_COUNT"
        echo "  WARNING: $WARNING_COUNT"
        ;;
        
    *)
        echo "Ошибка: неизвестная команда '$COMMAND'"
        echo "Доступные команды: info, error, warning, count"
        exit 1
        ;;
esac

exit 0
```

---

#### Вариант 4 (Интерактивное меню пользователей)

```bash
#!/usr/bin/env bash

# Лабораторная работа №5, Вариант 4
# Тема: Интерактивное меню с case

# Проверка прав root
if [[ $EUID -ne 0 ]]; then
    echo -e "\033[33m⚠️  ВНИМАНИЕ: Некоторые операции требуют прав root\033[0m"
    echo ""
fi

# Функции для пунктов меню
create_user() {
    echo ""
    read -p "Введите имя пользователя: " USERNAME
    
    if [[ -z "$USERNAME" ]]; then
        echo -e "\033[31mОшибка: имя пользователя не может быть пустым\033[0m"
        return
    fi
    
    if id "$USERNAME" &>/dev/null; then
        echo -e "\033[31mОшибка: пользователь $USERNAME уже существует\033[0m"
        return
    fi
    
    if [[ $EUID -ne 0 ]]; then
        echo -e "\033[31mОшибка: требуются права root\033[0m"
        return
    fi
    
    useradd -m -s /bin/bash "$USERNAME"
    if [[ $? -eq 0 ]]; then
        echo -e "\033[32m✅ Пользователь $USERNAME создан\033[0m"
    else
        echo -e "\033[31mОшибка при создании пользователя\033[0m"
    fi
}

delete_user() {
    echo ""
    read -p "Введите имя пользователя: " USERNAME
    
    if [[ -z "$USERNAME" ]]; then
        echo -e "\033[31mОшибка: имя пользователя не может быть пустым\033[0m"
        return
    fi
    
    if ! id "$USERNAME" &>/dev/null; then
        echo -e "\033[31mОшибка: пользователь $USERNAME не существует\033[0m"
        return
    fi
    
    if [[ $EUID -ne 0 ]]; then
        echo -e "\033[31mОшибка: требуются права root\033[0m"
        return
    fi
    
    read -p "Вы уверены, что хотите удалить $USERNAME? (y/n): " CONFIRM
    case "$CONFIRM" in
        y|Y|yes|Yes)
            userdel -r "$USERNAME"
            echo -e "\033[32m✅ Пользователь $USERNAME удален\033[0m"
            ;;
        *)
            echo "Операция отменена"
            ;;
    esac
}

show_user_info() {
    echo ""
    read -p "Введите имя пользователя: " USERNAME
    
    if [[ -z "$USERNAME" ]]; then
        echo -e "\033[31mОшибка: имя пользователя не может быть пустым\033[0m"
        return
    fi
    
    if ! id "$USERNAME" &>/dev/null; then
        echo -e "\033[31mПользователь $USERNAME не найден\033[0m"
        return
    fi
    
    echo -e "\033[36mПользователь $USERNAME:\033[0m"
    echo "  UID: $(id -u "$USERNAME")"
    echo "  GID: $(id -g "$USERNAME")"
    echo "  Домашняя директория: $(eval echo ~"$USERNAME")"
    echo "  Оболочка: $(getent passwd "$USERNAME" | cut -d: -f7)"
    
    # Проверка, залогинен ли пользователь
    if who | grep -q "^$USERNAME "; then
        echo "  Статус: \033[32mзалогинен\033[0m"
    else
        echo "  Статус: \033[33mне залогинен\033[0m"
    fi
}

change_password() {
    echo ""
    read -p "Введите имя пользователя: " USERNAME
    
    if [[ -z "$USERNAME" ]]; then
        echo -e "\033[31mОшибка: имя пользователя не может быть пустым\033[0m"
        return
    fi
    
    if ! id "$USERNAME" &>/dev/null; then
        echo -e "\033[31mПользователь $USERNAME не найден\033[0m"
        return
    fi
    
    if [[ $EUID -ne 0 ]]; then
        echo -e "\033[31mОшибка: требуются права root\033[0m"
        return
    fi
    
    passwd "$USERNAME"
}

show_user_list() {
    echo ""
    echo -e "\033[36m=== Список пользователей системы ===\033[0m"
    echo "Локальные пользователи:"
    getent passwd | grep -E ":/bin/bash$|:/bin/sh$|:/bin/zsh$" | cut -d: -f1 | sort
}

# Главное меню
while true; do
    echo ""
    echo -e "\033[1;36m=== УПРАВЛЕНИЕ ПОЛЬЗОВАТЕЛЯМИ ===\033[0m"
    echo "1. Создать пользователя"
    echo "2. Удалить пользователя"
    echo "3. Показать информацию о пользователе"
    echo "4. Сменить пароль пользователя"
    echo "5. Показать список пользователей"
    echo "6. Выход"
    echo ""
    
    read -p "Выберите пункт (1-6): " CHOICE
    
    case "$CHOICE" in
        1)
            create_user
            ;;
        2)
            delete_user
            ;;
        3)
            show_user_info
            ;;
        4)
            change_password
            ;;
        5)
            show_user_list
            ;;
        6)
            echo "До свидания!"
            exit 0
            ;;
        *)
            echo -e "\033[31mОшибка: неверный выбор. Введите число от 1 до 6.\033[0m"
            ;;
    esac
    
    echo ""
    read -p "Нажмите Enter для продолжения..."
done
```

