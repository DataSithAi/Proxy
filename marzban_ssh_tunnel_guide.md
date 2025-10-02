# Установка Marzban без домена с доступом через SSH-туннель

![Сложность](https://img.shields.io/badge/Сложность-Средняя-yellow?style=for-the-badge)
![Время](https://img.shields.io/badge/Время-30--40_мин-blue?style=for-the-badge)
![ОС](https://img.shields.io/badge/ОС-Ubuntu_|_Debian-orange?style=for-the-badge)
![Статус](https://img.shields.io/badge/Статус-Работает-success?style=for-the-badge)
![Версия](https://img.shields.io/badge/Marzban-Latest-brightgreen?style=for-the-badge)

---

# 🎯 О проекте Marzban
Marzban - это мощный инструмент управления прокси-серверами с современным веб-интерфейсом, работающий на базе Xray-core.

- ✨ Основные возможности:
- 🌐 Встроенный веб-интерфейс
- 🔄 Поддержка протоколов: Vmess, VLESS, Trojan, Shadowsocks
- 👥 Управление множеством пользователей
- 📊 Мониторинг трафика и статистики
- 🤖 Интеграция с Telegram Bot
- 🔗 Автоматическая генерация ссылок подписок
- 🛡️ Поддержка TLS и REALITY

## Содержание

1. [Критические ограничения](#Критические-ограничения)
2. [Предварительная подготовка](#Предварительная-подготовка)
3. [Предварительные требования](#Предварительные-требования)
4. [Подготовка системы](#Подготовка-системы)
5. [Установка Marzban](#Установка-Marzban)
6. [Настройка без SSL и доменного имени](#Настройка-без-SSL-и-доменного-имени)
7. [Создание администратора](#Создание-администратора)
8. [Подключение через SSH туннель](#Подключение-через-SSH-туннель)
9. [Настройка подключений](#Настройка-подключений)
10. [Ручное создание конфигураций](#Ручное-создание-конфигураций)
11. [Полезные команды](Полезные-команды)
12. [Устранение проблем](Устранение-проблем)
13. [Поддержка и сообщество](Поддержка-и-сообщество)
14. [Заключение](Заключение)

---

## 🚨 Критические ограничения

> **⚠️ ВНИМАНИЕ:** Прочитайте это перед началом установки!

```diff
- Автоматические ссылки подписок НЕ РАБОТАЮТ
- Конфигурации придется выдавать ВРУЧНУЮ
- Панель доступна ТОЛЬКО через SSH-туннель
- Рекомендуется ТОЛЬКО для тестирования (не для продакшин)
```

### Что НЕ будет работать:
- ❌ Автоматическое обновление конфигураций через подписки
- ❌ Удобные QR-коды для быстрого подключения
- ❌ Массовая рассылка конфигураций пользователям
- ❌ Доступ к панели без активного SSH-соединения

### Что БУДЕТ работать:
- ✅ Панель управления через SSH-туннель
- ✅ Создание пользователей и управление ими
- ✅ Все протоколы (VLESS, Trojan, VMess, Shadowsocks)
- ✅ Ручная выдача конфигураций клиентам
- ✅ Мониторинг трафика и статистики

### 💡 Рекомендация

Если вам нужна **полноценная работа** с автоматическими подписками:
- Используйте вариант [**с доменом и SSL-сертификатом**](#альтернатива)
- Бесплатный домен (DuckDNS) + самоподписанный SSL
- Это займет столько же времени, но даст все возможности

---

## ✅ Предварительная подготовка

### Чеклист перед началом:

- [ ] Чистая установка Ubuntu/Debian на сервере
- [ ] Root-доступ или sudo права
- [ ] SSH-доступ к серверу (проверен)
- [ ] SSH-клиент на локальном компьютере
- [ ] IP-адрес сервера записан
- [ ] Минимум 30-40 минут свободного времени
- [ ] Понимание, что подписки работать не будут

## ⚙️ Предварительные требования

### 🖥️ Системные требования:

| Компонент | Минимум | Рекомендуется |
|-----------|---------|---------------|
| **CPU** | 1 ядро | 2 ядра |
| **RAM** | 512 MB | 1 GB+ |
| **Диск** | 2 GB | 5 GB+ |
| **ОС** | Ubuntu 18.04+ | Ubuntu 22.04 |
| **Сеть** | 1 Мбит/с | 10+ Мбит/с |

### 🌐 Сетевые требования:

- **Открытые порты**: 80, 443, 8000
- **Стабильное интернет-соединение**

### 📝 Что нужно подготовить:
- IP-адрес вашего сервера
- SSH-доступ к серверу

---

## 🛠️ Подготовка системы

### Шаг 1: Обновление системы

```bash
# Подключитесь к серверу
ssh root@IP_СЕРВЕРА

# Обновите систему
sudo apt update && sudo apt upgrade -y

# Установите необходимые пакеты
sudo apt install curl wget git nano -y
```

**Что делают эти команды:**
- `apt update` - обновляет информацию о доступных пакетах
- `apt upgrade -y` - обновляет все установленные пакеты (флаг -y автоматически подтверждает)
- `apt install` - устанавливает утилиты для работы

---

## 📦 Установка Marzban

### Шаг 2: Автоматическая установка

```bash
# Установка Marzban с базой данных SQLite (рекомендуется)
sudo bash -c "$(curl -sL https://github.com/Gozargah/Marzban-scripts/raw/master/marzban.sh)" @ install
```

> 💡 **Альтернативные варианты:**
> ```bash
> # С MySQL
> sudo bash -c "$(curl -sL https://github.com/Gozargah/Marzban-scripts/raw/master/marzban.sh)" @ install --database mysql
>```

>```bash
> # С MariaDB  
> sudo bash -c "$(curl -sL https://github.com/Gozargah/Marzban-scripts/raw/master/marzban.sh)" @ install --database mariadb
> ```

**Что происходит:**
- Скачивается официальный скрипт установки
- Устанавливается Docker и Docker Compose
- Создаются необходимые директории
- Устанавливается Marzban в Docker контейнере
- Запускается служба автоматически

### Включаем автозапуск Marzban при загрузке системы
```bash
sudo systemctl enable docker
```

### Шаг 3: Остановка после установки

```bash
# Останавливаем Marzban для настройки
marzban down
```
**Результат:**
```
[+] Running 1/1
 ✔ Container marzban-marzban-1  Removed
```

---

## ⚙️ Настройка без SSL и доменного имени

### Шаг 4: Редактирование конфигурации

```bash
# Открываем конфигурационный файл
sudo nano /opt/marzban/.env
```

### Шаг 5: Настройка порта и пути к панели. Базовая конфигурация без SSL

Замените содержимое на следующее (с вашими изменениями):

```env
# ========================================
# ОСНОВНЫЕ НАСТРОЙКИ MARZBAN БЕЗ SSL
# ========================================

# 🌐 Хост и порт
# 127.0.0.1 = доступ только локально (безопасно)
UVICORN_HOST="127.0.0.1"
# Порт панели управления (выберите свой)
# Популярные варианты: 8080, 9000, 7777, 3000
UVICORN_PORT=8080

# UVICORN_HOST = "0.0.0.0"
# UVICORN_PORT = 8000
# ALLOWED_ORIGINS=http://localhost,http://localhost:8000,http://example.com

## We highly recommend add admin using `marzban cli` tool and do not use
## the following variables which is somehow hard codded infrmation.
# SUDO_USERNAME = "admin"
# SUDO_PASSWORD = "admin"

# ========================================
# 🔒 SSL НАСТРОЙКИ - НЕ ИСПОЛЬЗУЮТСЯ!
# ========================================
# ВАЖНО: Не раскомментируйте эти строки!
# UVICORN_UDS: "/run/marzban.socket"
# UVICORN_SSL_CERTFILE = "/var/lib/marzban/certs/example.com/fullchain.pem"
# UVICORN_SSL_KEYFILE = "/var/lib/marzban/certs/example.com/key.pem"
# UVICORN_SSL_CA_TYPE = "public"

# ========================================
# URL НАСТРОЙКИ
# ========================================
# Базовый URL (не работает для подписок, но нужен системе)
SUB_BASE_URL=http://127.0.0.1:8080

# ========================================
# 🎛 ПУТЬ К ПАНЕЛИ УПРАВЛЕНИЯ
# ========================================
# Придумайте свой уникальный путь!
# Примеры: /my-admin/, /control-2024/, /secret-panel/
DASHBOARD_PATH="/my-admin-panel/"

# DASHBOARD_PATH = "/dashboard/"

# ========================================
# 📁 НАСТРОЙКИ XRAY
# ========================================
XRAY_JSON = "/var/lib/marzban/xray_config.json"
# XRAY_SUBSCRIPTION_URL_PREFIX = "https://example.com"
# XRAY_SUBSCRIPTION_PATH = "sub"
# XRAY_EXECUTABLE_PATH = "/usr/local/bin/xray"
# XRAY_ASSETS_PATH = "/usr/local/share/xray"
# XRAY_EXCLUDE_INBOUND_TAGS = "INBOUND_X INBOUND_Y"
# XRAY_FALLBACKS_INBOUND_TAG = "INBOUND_X"


# TELEGRAM_API_TOKEN = 123456789:AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
# TELEGRAM_ADMIN_ID = 987654321, 123456789
# TELEGRAM_LOGGER_CHANNEL_ID = -1234567890123
# TELEGRAM_DEFAULT_VLESS_FLOW = "xtls-rprx-vision"
# TELEGRAM_PROXY_URL = "http://localhost:8080"

# DISCORD_WEBHOOK_URL = "https://discord.com/api/webhooks/xxxxxxx"

# CUSTOM_TEMPLATES_DIRECTORY="/var/lib/marzban/templates/"
# CLASH_SUBSCRIPTION_TEMPLATE="clash/my-custom-template.yml"
# SUBSCRIPTION_PAGE_TEMPLATE="subscription/index.html"
# HOME_PAGE_TEMPLATE="home/index.html"

# V2RAY_SUBSCRIPTION_TEMPLATE="v2ray/default.json"
# V2RAY_SETTINGS_TEMPLATE="v2ray/settings.json"

# SINGBOX_SUBSCRIPTION_TEMPLATE="singbox/default.json"
# SINGBOX_SETTINGS_TEMPLATE="singbox/settings.json"

# MUX_TEMPLATE="mux/default.json"

## Enable JSON config for compatible clients to use mux, fragment, etc. Default False.
# USE_CUSTOM_JSON_DEFAULT=True
## Your preferred config type for different clients
## If USE_CUSTOM_JSON_DEFAULT is set True, all following programs will use the JSON config
# USE_CUSTOM_JSON_FOR_V2RAYN=False
# USE_CUSTOM_JSON_FOR_V2RAYNG=True
# USE_CUSTOM_JSON_FOR_STREISAND=False
# USE_CUSTOM_JSON_FOR_HAPP=False

## Set headers for subscription
# SUB_PROFILE_TITLE = "Susbcription"
# SUB_SUPPORT_URL = "https://t.me/support"
# SUB_UPDATE_INTERVAL = "12"

## External config to import into v2ray format subscription
# EXTERNAL_CONFIG = "config://..."

# ========================================
# БАЗА ДАННЫХ
# ========================================
SQLALCHEMY_DATABASE_URL = "sqlite:////var/lib/marzban/db.sqlite3"
# SQLALCHEMY_POOL_SIZE = 10
# SQLIALCHEMY_MAX_OVERFLOW = 30

# ========================================
# ДОПОЛНИТЕЛЬНЫЕ НАСТРОЙКИ (опционально)
# ========================================

## Custom text for STATUS_TEXT variable
# ACTIVE_STATUS_TEXT = "Active"
# EXPIRED_STATUS_TEXT = "Expired"
# LIMITED_STATUS_TEXT = "Limited"
# DISABLED_STATUS_TEXT = "Disabled"
# ONHOLD_STATUS_TEXT = "On-Hold"

### Use negative values to disable auto-delete by default
# USERS_AUTODELETE_DAYS = -1
# USER_AUTODELETE_INCLUDE_LIMITED_ACCOUNTS = false

## Customize all notifications
# NOTIFY_STATUS_CHANGE = True
# NOTIFY_USER_CREATED = True
# NOTIFY_USER_UPDATED = True
# NOTIFY_USER_DELETED = True
# NOTIFY_USER_DATA_USED_RESET = True
# NOTIFY_USER_SUB_REVOKED = True
# NOTIFY_IF_DATA_USAGE_PERCENT_REACHED = True
# NOTIFY_IF_DAYS_LEFT_REACHED = True
# NOTIFY_LOGIN = True

## Whitelist of IPs/hosts to disable login notifications
# LOGIN_NOTIFY_WHITE_LIST = '1.1.1.1,127.0.0.1'

### for developers
# DOCS=True
# DEBUG=True

# If You Want To Send Webhook To Multiple Server Add Multi Address
# WEBHOOK_ADDRESS = "http://127.0.0.1:9000/,http://127.0.0.1:9001/"
# WEBHOOK_SECRET = "something-very-very-secret"
# NOTIFY_DAYS_LEFT=3,7
# NOTIFY_REACHED_USAGE_PERCENT=80,90

# VITE_BASE_API="https://example.com/api/"
# JWT_ACCESS_TOKEN_EXPIRE_MINUTES = 1440

# JOB_CORE_HEALTH_CHECK_INTERVAL = 10
# JOB_RECORD_NODE_USAGES_INTERVAL = 30
# JOB_RECORD_USER_USAGES_INTERVAL = 10
# JOB_REVIEW_USERS_INTERVAL = 10
# JOB_SEND_NOTIFICATIONS_INTERVAL = 30

```

#### Примеры безопасных настроек:

**Вариант 1 (рекомендуемый):**
```env
UVICORN_PORT=8080
SUB_BASE_URL=http://127.0.0.1:8080
DASHBOARD_PATH="/secret-admin-2024/"
```

**Вариант 2:**
```env
UVICORN_PORT=9000
SUB_BASE_URL=http://127.0.0.1:9000
DASHBOARD_PATH="/control-xyz/"
```

**Вариант 3:**
```env
UVICORN_PORT=7777
SUB_BASE_URL=http://127.0.0.1:7777
DASHBOARD_PATH="/my-lenovo-panel/"
```

#### Важные правила:

- **UVICORN_PORT** - выберите порт от 1024 до 65535
- **SUB_BASE_URL** - порт должен совпадать с UVICORN_PORT
- **DASHBOARD_PATH** - обязательно начинать и заканчивать слешем `/`
- Используйте сложные пути для дополнительной безопасности

**Сохранение:**
- Нажмите `Ctrl+S`
- Нажмите `Ctrl+X`

---

## 👤 Создание администратора

### Шаг 6: Запуск Marzban

```bash
# Запускаем Marzban
marzban up --no-logs
```

### Шаг 7: Создание администратора

```bash
# Создаем администратора
marzban cli admin create --sudo
```

**Интерактивный ввод:**
```
Username: admin
Password: ********
Repeat Password: ********
✔ Admin created successfully
```

**Рекомендации по паролю:**
- Минимум 12 символов
- Используйте буквы, цифры и спецсимволы
- Не используйте словарные слова
- Пароль должен быть сложным

---

## 🔒 Подключение через SSH туннель

### Шаг 8: Создание SSH-туннеля

#### На Windows (PowerShell или CMD):
```bash
ssh -L 8080:127.0.0.1:8080 root@IP_СЕРВЕРА
```

**Если вы изменили порт, используйте свой порт:**
```bash
# Для порта 8080:
ssh -L 8080:127.0.0.1:8080 root@IP_СЕРВЕРА

# Для порта 9000:
ssh -L 9000:127.0.0.1:9000 root@IP_СЕРВЕРА

# Для порта 7777:
ssh -L 7777:127.0.0.1:7777 root@IP_СЕРВЕРА
```

#### На Linux/Mac:
```bash
ssh -L 8080:127.0.0.1:8080 root@IP_СЕРВЕРА
```

#### Объяснение команды:
- `-L 8080:127.0.0.1:8080` - перенаправляет локальный порт на удаленный
- Первый порт (8080) - локальный порт на вашем компьютере
- Второй порт (8080) - порт на сервере (должен совпадать с UVICORN_PORT)
- `root@IP_СЕРВЕРА` - ваши данные для подключения

### Шаг 9: Доступ к панели

1. **Оставьте SSH-соединение активным**
2. Откройте браузер на вашем компьютере
3. Перейдите по адресу в зависимости от вашего порта и пути:
   ```
   http://localhost:8080/my-admin-panel/
   ```
   Или если вы использовали другие настройки:
   ```
   http://localhost:9000/control-xyz/
   http://localhost:7777/my-lenovo-panel/
   ```
4. Войдите с данными администратора (введите логин и пароль котрые вы создали)

**Важно:** При закрытии SSH-соединения доступ к панели пропадет.

Принцип работы SSH-туннеля

```
┌─────────────┐         SSH Tunnel        ┌─────────────┐
│   Ваш ПК    │◄──────────────────────────►│   Сервер    │
│ localhost   │   Зашифрованное соединение │ 127.0.0.1   │
│   :8080     │                            │   :8080     │
└─────────────┘                            └─────────────┘
      ▲                                           ▲
      │                                           │
   Браузер                                   Marzban Panel
```

### Автоматизация туннеля (по желанию)

**Создайте скрипт для Windows (tunnel.bat):**
```batch
@echo off
echo ================================================
echo   Подключение к панели Marzban
echo ================================================
echo.
echo Подключаюсь к серверу...
ssh -o ServerAliveInterval=60 -L 8080:127.0.0.1:8080 root@YOUR_SERVER_IP
echo.
echo Соединение закрыто.
pause
```

**Создайте скрипт для Linux/Mac (tunnel.sh):**
```bash
#!/bin/bash
echo "================================================"
echo "   Подключение к панели Marzban"
echo "================================================"
echo ""
echo "Подключаюсь к серверу..."
ssh -o ServerAliveInterval=60 -L 8080:127.0.0.1:8080 root@YOUR_SERVER_IP
echo ""
echo "Соединение закрыто."
```

**Сделайте скрипт исполняемым:**
```bash
chmod +x tunnel.sh
./tunnel.sh
```

---

## 🔧 Настройка подключений

По умолчанию в Marzban доступно создание подключений только по протоколу **Shadowsocks**. Для добавления других протоколов выполните следующие шаги:

### Шаг 10: Добавление протоколов VLESS, TROJAN, VMESS

#### 1️⃣ Открытие настроек Xray

1. В панели Marzban в **правом верхнем углу** найдите значок **⚙️ (шестеренка)**
2. Нажмите на него
3. Откроется окно "Core Settings" или "Настройки ядра"

#### 2️⃣ Замените конфигурацию

1. В поле "Конфигурация" **удалите всё содержимое**
2. **Вставьте** следующий код:

```json
{
  "log": {
    "loglevel": "warning"
  },
  "routing": {
    "rules": [
      {
        "ip": [
          "geoip:private"
        ],
        "outboundTag": "BLOCK",
        "type": "field"
      }
    ]
  },
  "inbounds": [
    {
      "tag": "VMESS TCP NOTLS",
      "listen": "0.0.0.0",
      "port": 445,
      "protocol": "vmess",
      "settings": {
        "clients": []
      },
      "streamSettings": {
        "network": "tcp",
        "tcpSettings": {},
        "security": "none"
      },
      "sniffing": {
        "enabled": true,
        "destOverride": [
          "http",
          "tls",
          "quic"
        ]
      }
    },
    {
      "tag": "TROJAN TCP NOTLS",
      "listen": "0.0.0.0",
      "port": 444,
      "protocol": "trojan",
      "settings": {
        "clients": []
      },
      "streamSettings": {
        "network": "tcp",
        "tcpSettings": {},
        "security": "none"
      },
      "sniffing": {
        "enabled": true,
        "destOverride": [
          "http",
          "tls",
          "quic"
        ]
      }
    },
    {
      "tag": "VLESS TCP REALITY",
      "listen": "0.0.0.0",
      "port": 443,
      "protocol": "vless",
      "settings": {
        "clients": [],
        "decryption": "none"
      },
      "streamSettings": {
        "network": "tcp",
        "tcpSettings": {},
        "security": "reality",
        "realitySettings": {
          "show": false,
          "dest": "github.com:443",
          "xver": 0,
          "serverNames": [
            "github.com"
          ],
          "privateKey": "вставить сюда privateKey",
          "shortIds": [
            "вставить сюда shortIds"
          ]
        }
      },
      "sniffing": {
        "enabled": true,
        "destOverride": [
          "http",
          "tls",
          "quic"
        ]
      }
    },
    {
      "tag": "Shadowsocks TCP",
      "listen": "0.0.0.0",
      "port": 1080,
      "protocol": "shadowsocks",
      "settings": {
        "clients": [],
        "network": "tcp,udp"
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "tag": "DIRECT"
    },
    {
      "protocol": "blackhole",
      "tag": "BLOCK"
    }
  ]
}
```

#### 3️⃣ Генерация ключей для VLESS TCP REALITY

**НЕ ЗАКРЫВАЙТЕ окно браузера!**

Перед сохранением конфигурации необходимо сгенерировать **privateKey** и **shortIds** для протокола REALITY:

В терминале на сервере введите следующие команды:

```bash
# Генерация privateKey
docker exec marzban-marzban-1 xray x25519

# Генерация shortIds
openssl rand -hex 8
```

**Пример вывода:**

```bash
# Результат команды x25519:
Private key: gK3C9TVRXZ_Zp-rRCi3bkp3HdS6RJX8Xz7lJ_Nk1234
Public key: 7vCwRJ_2sB8JmzY-K5P7ZxQpS3hN6Vt4Rl9F_Ab5678

# Результат команды openssl rand:
a1b2c3d4e5f67890
```

#### 4️⃣ Вставка ключей в конфигурацию

В блоке **VLESS TCP REALITY** замените:
- `"вставить сюда privateKey"` → `"gK3C9TVRXZ_Zp-rRCi3bkp3HdS6RJX8Xz7lJ_Nk1234"`
- `"вставить сюда shortIds"` → `"a1b2c3d4e5f67890"`

⚠️Нужно вставить свои ключи которые вы сгенирировали в терминале

#### 5️⃣ Применение настроек

1. Нажмите кнопку **"Сохранить"**
2. Нажмите кнопку **"Перезагрузить ядро"**
3. **Закройте** окно настроек
4. **Перезагрузите** страницу браузера

### ✅ Результат

Теперь в Marzban доступны следующие протоколы:
- ✨ **VLESS TCP REALITY** (порт 443) - рекомендуется
- 🔥 **TROJAN TCP NOTLS** (порт 444) - рекомендуется
- ⚡ **VMESS TCP NOTLS** (порт 445)
- 🛡️ **Shadowsocks TCP** (порт 1080)

> 💡 **Рекомендация**: Используйте **VLESS** или **TROJAN** - они стабильно работают во всех регионах и на всех устройствах.

### Шаг 11: Открытие портов на сервере

```bash
# Откройте порты для протоколов
sudo ufw allow 443/tcp
sudo ufw allow 444/tcp
sudo ufw allow 445/tcp
sudo ufw allow 1080/tcp
sudo ufw allow 22/tcp
sudo ufw enable
```

---

## 💪 Ручное создание конфигураций

Поскольку подписки не работают, конфигурации нужно создавать вручную.

### Шаг 12: Создание пользователя

1. В панели перейдите в раздел "Users"
2. Нажмите "Add User"
3. Заполните данные:
   - Username: `user1`
   - Data Limit: установите лимит трафика
   - Expire Date: дата окончания
4. Выберите протокол (например, VLESS или Trojan)
5. Нажмите "Add"

### Экспорт через QR-код

1. В панели нажмите на пользователя
2. Найдите кнопку **"QR Code"**
3. Отсканируйте QR-код клиентским приложением
4. Конфигурация импортируется автоматически

### Шаг 13: Получение конфигурации

1. Нажмите на созданного пользователя
2. В разделе "Subscription" увидите конфигурационные ссылки
3. **НЕ используйте ссылку подписки** - она не будет работать
4. Используйте **QR-код** или **скопируйте прямую ссылку** типа:
   ```
   vless://uuid@IP_СЕРВЕРА:443?security=none&type=tcp#user1
   ```
   или
   ```
   trojan://password@IP_СЕРВЕРА:444?security=none&type=tcp#user1
   ```


## 📚 Полезные команды

### Управление Marzban

```bash
# Запуск
marzban up

# Остановка
marzban down

# Перезапуск
marzban restart

# Просмотр логов
marzban logs

# Статус
marzban status
```

### Управление SSH-туннелем

```bash
# Создание туннеля в фоне (Linux/Mac)
ssh -f -N -L 8000:127.0.0.1:8000 root@IP_СЕРВЕРА

# Просмотр активных туннелей
ps aux | grep ssh

# Закрытие туннеля
kill [PID]
```

Принцип работы SSH-туннеля

```
┌─────────────┐         SSH Tunnel        ┌─────────────┐
│   Ваш ПК    │◄──────────────────────────►│   Сервер    │
│ localhost   │   Зашифрованное соединение │ 127.0.0.1   │
│   :8080     │                            │   :8080     │
└─────────────┘                            └─────────────┘
      ▲                                           ▲
      │                                           │
   Браузер                                   Marzban Panel
```



## 🔧 Устранение проблем

### ❌ Панель не открывается

```bash
# Проверьте что Marzban запущен
marzban status

# Проверьте логи
marzban logs

# Проверьте что SSH-туннель активен
# На Windows:
netstat -an | findstr "8000"

# На Linux/Mac:
lsof -i :8000
```

### ❌ Подключения не работают

```bash
# Проверьте открыты ли порты
sudo ufw status

# Проверьте конфигурацию Xray
cat /var/lib/marzban/xray_config.json

# Перезагрузите ядро в панели
# Настройки → Перезагрузить ядро
```

### ❌ SSH-туннель обрывается

```bash
# Используйте опции для поддержания соединения
ssh -o ServerAliveInterval=60 -o ServerAliveCountMax=3 -L 8000:127.0.0.1:8000 root@IP_СЕРВЕРА
```

---

## 📞 Поддержка и сообщество

- 📖 **Документация**: [Marzban Docs](https://gozargah.github.io/marzban)
- 💬 **Telegram**: [Marzban Support](https://t.me/gozargah_marzban)
- 🐙 **GitHub**: [Marzban Repository](https://github.com/Gozargah/Marzban)
- 🔧 **Issues**: [Bug Reports](https://github.com/gozargah/marzban/issues)

## 📝 Заключение

Поздравляем! Вы успешно установили и настроили **Marzban** с защищённым ssh-тунелем.

### ✅ Что вы получили:
- ✨ Работающую панель управления прокси
- 🔒 ssh-тунель соединение
- 👤 Административный доступ к системе
- 🌐 Возможность создавать и управлять пользователями

### 🚀 Следующие шаги:
1. Изучите интерфейс панели управления
2. Создайте первых пользователей
3. Настройте протоколы под ваши нужды
4. Рассмотрите настройку Telegram Bot
5. Настройте регулярные резервные копии

**Удачного использования Marzban!** 🎉

---
