### Лабораторная работа : «Управление репозиториями в Debian»

**Цель работы:** Научиться создавать локальный APT-репозиторий, добавлять в него пакеты и подключать репозиторий к системе для установки пакетов.

**Теоретическая справка:**

**Что такое репозиторий?**
- Репозиторий — это хранилище пакетов программного обеспечения
- APT (Advanced Package Tool) — система управления пакетами в Debian
- Репозитории содержат индексные файлы и сами пакеты (.deb)

**Структура репозитория:**
```
/путь/к/репозиторию/
├── dists/
│   └── stable/
│       ├── main/
│       │   └── binary-amd64/
│       │       ├── Packages.gz
│       │       ├── Release
│       │       └── Release.gpg
│       └── contrib/
│           └── binary-amd64/
│               └── Packages.gz
└── pool/
    └── main/
        └── пакеты.deb
```

**Основные команды:**
```bash
# Список репозиториев
cat /etc/apt/sources.list
ls /etc/apt/sources.list.d/

# Обновление списка пакетов
apt update

# Установка пакета
apt install package-name

# Создание локального репозитория
apt-ftparchive packages . > Packages
gzip -c Packages > Packages.gz

# Добавление GPG ключа
apt-key add key.asc
```

---

### Задание

Создайте локальный APT-репозиторий, добавьте в него пакет (например, собранный или скачанный .deb-пакет) и подключите репозиторий к системе.

**Требования:**
1. Создать директорию для локального репозитория
2. Добавить хотя бы один .deb-пакет
3. Создать индексные файлы (Packages, Release)
4. Подключить репозиторий к системе (добавить в sources.list)
5. Обновить список пакетов и установить пакет из локального репозитория

---

### Варианты заданий

#### Вариант 1 (Базовый)
Создайте простой локальный репозиторий с одним пакетом, используя стандартные утилиты.

#### Вариант 2 (С несколькими пакетами)
Создайте репозиторий с несколькими пакетами (например, nginx, curl, htop).



### Эталонное решение (Вариант 1 - Базовый)

#### Шаг 1: Подготовка окружения

**Файл:** `setup_repo.sh`

```bash
#!/usr/bin/env bash

# ===============================================
# Создание локального APT-репозитория в Debian
# Версия: 1.0
# ===============================================

set -e  # Остановка при ошибке

# Переменные
REPO_NAME="local-repo"
REPO_DIR="/usr/local/$REPO_NAME"
REPO_DIST="stable"
REPO_COMPONENT="main"
REPO_ARCH="amd64"
PACKAGES_DIR="$REPO_DIR/pool/$REPO_COMPONENT"
DISTS_DIR="$REPO_DIR/dists/$REPO_DIST"
BINARY_DIR="$DISTS_DIR/$REPO_COMPONENT/binary-$REPO_ARCH"

# Цвета для вывода
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
RED='\033[0;31m'
NC='\033[0m'

echo -e "${GREEN}=========================================${NC}"
echo -e "${GREEN}   СОЗДАНИЕ ЛОКАЛЬНОГО APT-РЕПОЗИТОРИЯ${NC}"
echo -e "${GREEN}=========================================${NC}"
echo ""

# Проверка прав
if [[ $EUID -ne 0 ]]; then
    echo -e "${RED}❌ Запустите скрипт с правами root (sudo)${NC}"
    exit 1
fi

# Проверка необходимых пакетов
echo -e "${YELLOW}▶ Проверка необходимых пакетов...${NC}"

MISSING=""
for pkg in dpkg-dev apt-utils gnupg; do
    if ! dpkg -s "$pkg" &>/dev/null; then
        MISSING="$MISSING $pkg"
    fi
done

if [[ -n "$MISSING" ]]; then
    echo -e "${YELLOW}⚠️  Устанавливаем недостающие пакеты:${NC} $MISSING"
    apt update
    apt install -y $MISSING
fi

echo -e "${GREEN}✅ Все необходимые пакеты установлены${NC}"
echo ""

# Создание структуры директорий
echo -e "${YELLOW}▶ Создание структуры репозитория...${NC}"

mkdir -p "$PACKAGES_DIR"
mkdir -p "$BINARY_DIR"

echo -e "${GREEN}✅ Структура создана:${NC}"
echo "  Репозиторий: $REPO_DIR"
echo "  Пакеты: $PACKAGES_DIR"
echo "  Индексы: $BINARY_DIR"
echo ""

# Создание примера пакета (или скачивание существующего)
echo -e "${YELLOW}▶ Добавление пакета в репозиторий...${NC}"

# Создаем простой пакет-пример
cat > /tmp/hello-world.c << 'EOF'
#include <stdio.h>
int main() {
    printf("Hello, World from local repository!\n");
    return 0;
}
EOF

# Компилируем
gcc -o /tmp/hello-world /tmp/hello-world.c

# Создаем структуру для пакета
PKG_DIR="/tmp/hello-world-pkg"
mkdir -p "$PKG_DIR/usr/bin"
mkdir -p "$PKG_DIR/DEBIAN"

# Копируем бинарник
cp /tmp/hello-world "$PKG_DIR/usr/bin/"

# Создаем control-файл
cat > "$PKG_DIR/DEBIAN/control" << 'EOF'
Package: hello-world
Version: 1.0-1
Section: utils
Priority: optional
Architecture: amd64
Depends: libc6
Maintainer: Student <student@localhost>
Description: Simple Hello World program
 This is a demo package for local repository.
 It prints "Hello, World!" when executed.
EOF

# Создаем postinst-скрипт
cat > "$PKG_DIR/DEBIAN/postinst" << 'EOF'
#!/bin/sh
set -e
echo "Hello World package installed successfully!"
exit 0
EOF
chmod +x "$PKG_DIR/DEBIAN/postinst"

# Собираем .deb пакет
dpkg -b "$PKG_DIR" "$PACKAGES_DIR/hello-world_1.0-1_amd64.deb"

# Очистка временных файлов
rm -rf /tmp/hello-world* /tmp/hello-world-pkg

echo -e "${GREEN}✅ Пакет создан:${NC}"
ls -la "$PACKAGES_DIR/"
echo ""

# Создание индексных файлов
echo -e "${YELLOW}▶ Создание индексных файлов...${NC}"

# Переход в директорию репозитория
cd "$REPO_DIR"

# Создание Packages файла
dpkg-scanpackages --multiversion pool/ > "$BINARY_DIR/Packages"
cat "$BINARY_DIR/Packages" | gzip -9 > "$BINARY_DIR/Packages.gz"

# Создание Release файла
cat > "$DISTS_DIR/Release" << EOF
Origin: $REPO_NAME
Label: $REPO_NAME
Suite: $REPO_DIST
Codename: $REPO_DIST
Date: $(date -u '+%a, %d %b %Y %H:%M:%S UTC')
Architectures: $REPO_ARCH
Components: $REPO_COMPONENT
Description: Local APT repository for Debian
EOF

# Добавление MD5 и SHA* хешей
echo "" >> "$DISTS_DIR/Release"

# Добавление MD5 хешей
echo "MD5Sum:" >> "$DISTS_DIR/Release"
for file in "$BINARY_DIR/Packages" "$BINARY_DIR/Packages.gz"; do
    if [[ -f "$file" ]]; then
        md5=$(md5sum "$file" | awk '{print $1}')
        size=$(stat -c%s "$file")
        relpath="${file#$REPO_DIR/}"
        echo " $md5 $size $relpath" >> "$DISTS_DIR/Release"
    fi
done

# Добавление SHA1 хешей
echo "SHA1:" >> "$DISTS_DIR/Release"
for file in "$BINARY_DIR/Packages" "$BINARY_DIR/Packages.gz"; do
    if [[ -f "$file" ]]; then
        sha1=$(sha1sum "$file" | awk '{print $1}')
        size=$(stat -c%s "$file")
        relpath="${file#$REPO_DIR/}"
        echo " $sha1 $size $relpath" >> "$DISTS_DIR/Release"
    fi
done

# Добавление SHA256 хешей
echo "SHA256:" >> "$DISTS_DIR/Release"
for file in "$BINARY_DIR/Packages" "$BINARY_DIR/Packages.gz"; do
    if [[ -f "$file" ]]; then
        sha256=$(sha256sum "$file" | awk '{print $1}')
        size=$(stat -c%s "$file")
        relpath="${file#$REPO_DIR/}"
        echo " $sha256 $size $relpath" >> "$DISTS_DIR/Release"
    fi
done

echo -e "${GREEN} Индексные файлы созданы:${NC}"
ls -la "$BINARY_DIR/"
echo ""

# Подключение репозитория к системе
echo -e "${YELLOW}▶ Подключение репозитория к системе...${NC}"

# Добавление в sources.list
SOURCES_FILE="/etc/apt/sources.list.d/$REPO_NAME.list"
echo "deb [trusted=yes] file://$REPO_DIR $REPO_DIST $REPO_COMPONENT" > "$SOURCES_FILE"

echo -e "${GREEN} Репозиторий добавлен в:${NC} $SOURCES_FILE"
cat "$SOURCES_FILE"
echo ""

# Обновление списка пакетов
echo -e "${YELLOW}▶ Обновление списка пакетов...${NC}"
apt update

echo -e "${GREEN} Список пакетов обновлен${NC}"
echo ""

# Поиск пакета
echo -e "${YELLOW}▶ Поиск пакета в репозитории...${NC}"
apt-cache search hello-world

echo ""
echo -e "${GREEN}=========================================${NC}"
echo -e "${GREEN}   ✅ РЕПОЗИТОРИЙ СОЗДАН УСПЕШНО!${NC}"
echo -e "${GREEN}=========================================${NC}"
echo ""
echo "Информация о репозитории:"
echo "  Путь: $REPO_DIR"
echo "  Пакет: hello-world"
echo "  Версия: 1.0-1"
echo ""
echo "Установка пакета:"
echo "  apt install hello-world"
echo ""
echo "Запуск программы:"
echo "  hello-world"
echo ""
```

---

#### Шаг 2: Создание репозитория с несколькими пакетами

**Файл:** `create_repo_advanced.sh`

```bash
#!/usr/bin/env bash

# ===============================================
# Создание локального APT-репозитория (расширенный)
# ===============================================

set -e

# Переменные
REPO_DIR="/usr/local/apt-repo"
REPO_NAME="local-repo"
REPO_DIST="stable"
REPO_COMPONENT="main"

# Цвета
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m'

echo -e "${GREEN}=========================================${NC}"
echo -e "${GREEN}   СОЗДАНИЕ ЛОКАЛЬНОГО APT-РЕПОЗИТОРИЯ${NC}"
echo -e "${GREEN}=========================================${NC}"
echo ""

# Проверка прав
if [[ $EUID -ne 0 ]]; then
    echo "❌ Запустите с правами root"
    exit 1
fi

# Функция для добавления пакета
add_package() {
    local pkg_name="$1"
    local pkg_version="${2:-1.0-1}"
    
    echo -e "${YELLOW}▶ Добавление пакета: $pkg_name${NC}"
    
    # Создаем структуру для пакета
    local PKG_DIR="/tmp/${pkg_name}-pkg"
    mkdir -p "$PKG_DIR/usr/bin"
    mkdir -p "$PKG_DIR/DEBIAN"
    
    # Создаем простую программу
    cat > "$PKG_DIR/usr/bin/$pkg_name" << EOF
#!/bin/bash
echo "$pkg_name package from local repository!"
echo "Version: $pkg_version"
echo "Installed at: \$(date)"
EOF
    chmod +x "$PKG_DIR/usr/bin/$pkg_name"
    
    # Создаем control-файл
    cat > "$PKG_DIR/DEBIAN/control" << EOF
Package: $pkg_name
Version: $pkg_version
Section: utils
Priority: optional
Architecture: all
Maintainer: Student <student@localhost>
Description: $pkg_name demonstration package
 This is a demo package for local repository.
 It prints a message when executed.
EOF
    
    # Создаем postinst
    cat > "$PKG_DIR/DEBIAN/postinst" << EOF
#!/bin/sh
set -e
echo " $pkg_name installed successfully!"
exit 0
EOF
    chmod +x "$PKG_DIR/DEBIAN/postinst"
    
    # Собираем пакет
    dpkg -b "$PKG_DIR" "$REPO_DIR/pool/$REPO_COMPONENT/${pkg_name}_${pkg_version}_all.deb"
    
    # Очистка
    rm -rf "$PKG_DIR"
    
    echo -e "${GREEN} Пакет добавлен: ${pkg_name}_${pkg_version}_all.deb${NC}"
}

# Создание структуры
echo -e "${YELLOW}▶ Создание структуры репозитория...${NC}"
mkdir -p "$REPO_DIR/pool/$REPO_COMPONENT"
mkdir -p "$REPO_DIR/dists/$REPO_DIST/$REPO_COMPONENT/binary-amd64"
mkdir -p "$REPO_DIR/dists/$REPO_DIST/$REPO_COMPONENT/binary-i386"
mkdir -p "$REPO_DIR/dists/$REPO_DIST/$REPO_COMPONENT/binary-all"
echo -e "${GREEN} Структура создана${NC}"
echo ""

# Добавление пакетов
echo -e "${YELLOW}▶ Добавление пакетов...${NC}"
add_package "hello-world" "1.0-1"
add_package "hello-world" "1.1-1"
add_package "greeting" "1.0-1"
add_package "sysinfo" "1.0-1"
echo -e "${GREEN} Все пакеты добавлены${NC}"
echo ""

# Создание индексных файлов
echo -e "${YELLOW}▶ Создание индексных файлов...${NC}"

cd "$REPO_DIR"

# Создание Packages файлов
for arch in amd64 i386 all; do
    BIN_DIR="dists/$REPO_DIST/$REPO_COMPONENT/binary-$arch"
    dpkg-scanpackages --multiversion pool/ > "$BIN_DIR/Packages"
    cat "$BIN_DIR/Packages" | gzip -9 > "$BIN_DIR/Packages.gz"
    echo "   $arch: Packages.gz создан"
done

# Создание Release файла
cat > "dists/$REPO_DIST/Release" << EOF
Origin: $REPO_NAME
Label: $REPO_NAME
Suite: $REPO_DIST
Codename: $REPO_DIST
Date: $(date -u '+%a, %d %b %Y %H:%M:%S UTC')
Architectures: amd64 i386 all
Components: $REPO_COMPONENT
Description: Local APT repository with multiple packages
EOF

# Добавление хешей
for arch in amd64 i386 all; do
    BIN_DIR="dists/$REPO_DIST/$REPO_COMPONENT/binary-$arch"
    for file in "Packages" "Packages.gz"; do
        if [[ -f "$BIN_DIR/$file" ]]; then
            md5=$(md5sum "$BIN_DIR/$file" | awk '{print $1}')
            sha1=$(sha1sum "$BIN_DIR/$file" | awk '{print $1}')
            sha256=$(sha256sum "$BIN_DIR/$file" | awk '{print $1}')
            size=$(stat -c%s "$BIN_DIR/$file")
            relpath="$REPO_COMPONENT/binary-$arch/$file"
            
            # Добавляем в Release, если еще не добавлено
            if ! grep -q "$relpath" "dists/$REPO_DIST/Release"; then
                echo "MD5Sum:" >> "dists/$REPO_DIST/Release"
                echo " $md5 $size $relpath" >> "dists/$REPO_DIST/Release"
                echo "SHA1:" >> "dists/$REPO_DIST/Release"
                echo " $sha1 $size $relpath" >> "dists/$REPO_DIST/Release"
                echo "SHA256:" >> "dists/$REPO_DIST/Release"
                echo " $sha256 $size $relpath" >> "dists/$REPO_DIST/Release"
            fi
        fi
    done
done

echo -e "${GREEN} Индексные файлы созданы${NC}"
echo ""

# Подключение репозитория
echo -e "${YELLOW}▶ Подключение репозитория...${NC}"

SOURCES_FILE="/etc/apt/sources.list.d/$REPO_NAME.list"
echo "deb [trusted=yes] file://$REPO_DIR $REPO_DIST $REPO_COMPONENT" > "$SOURCES_FILE"

echo -e "${GREEN} Репозиторий добавлен в: $SOURCES_FILE${NC}"
cat "$SOURCES_FILE"
echo ""

# Обновление
echo -e "${YELLOW}▶ Обновление списка пакетов...${NC}"
apt update

echo ""
echo -e "${GREEN}=========================================${NC}"
echo -e "${GREEN}   РЕПОЗИТОРИЙ ГОТОВ К ИСПОЛЬЗОВАНИЮ${NC}"
echo -e "${GREEN}=========================================${NC}"
echo ""
echo "Доступные пакеты:"
apt-cache search hello-world greeting sysinfo
echo ""
echo "Установка:"
echo "  apt install hello-world"
echo "  apt install greeting"
echo "  apt install sysinfo"
echo ""
echo "Запуск программы:"
echo "  hello-world"
echo "  greeting"
echo "  sysinfo"
```

