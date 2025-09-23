# 🚀 Полная инструкция по установке Marzban с самоподписанным SSL-сертификатом

## 📋 Содержание

1. [О проекте Marzban](#о-проекте-marzban)
2. [Предварительные требования](#предварительные-требования)
3. [Подготовка системы](#подготовка-системы)
4. [Создание SSL-сертификата](#создание-ssl-сертификата)
5. [Установка Marzban](#установка-marzban)
6. [Настройка SSL в Marzban](#настройка-ssl-в-marzban)
7. [Создание администратора](#создание-администратора)
8. [Доступ к панели управления](#доступ-к-панели-управления)
9. [Дополнительные настройки](#дополнительные-настройки)
10. [Устранение проблем](#устранение-проблем)
11. [Полезные команды](#полезные-команды)

---

## 🎯 О проекте Marzban

**Marzban** - это мощный инструмент управления прокси-серверами с современным веб-интерфейсом, работающий на базе **Xray-core**. 

### ✨ Основные возможности:
- 🌐 Встроенный веб-интерфейс
- 🔄 Поддержка протоколов: Vmess, VLESS, Trojan, Shadowsocks
- 👥 Управление множеством пользователей
- 📊 Мониторинг трафика и статистики
- 🤖 Интеграция с Telegram Bot
- 🔗 Автоматическая генерация ссылок подписок
- 🛡️ Поддержка TLS и REALITY

---

## ⚙️ Предварительные требования

### 🖥️ Системные требования:
- **ОС**: Ubuntu 18.04+, Debian 10+, CentOS 7+
- **RAM**: минимум 512 МБ (рекомендуется 1 ГБ+)
- **Диск**: минимум 2 ГБ свободного места
- **Root-доступ** или пользователь с sudo правами

### 🌐 Сетевые требования:
- **Домен или поддомен**, направленный на ваш сервер
- **Открытые порты**: 80, 443, 8000
- **Стабильное интернет-соединение**

### 📝 Что нужно подготовить:
- IP-адрес вашего сервера
- Доменное имя (например: `panel.example.com`)
- SSH-доступ к серверу

---

## 🛠️ Подготовка системы

### 1️⃣ Обновление системы

```bash
# Обновляем пакеты системы
sudo apt update && sudo apt upgrade -y

# Устанавливаем необходимые утилиты
sudo apt install curl wget socat git nano openssl ufw -y
```

### 2️⃣ Настройка переменной окружения

```bash
# Создаем переменную с вашим доменом (замените на свой)
export MARZBAN_DOMAIN="your-domain.example.com"

# Проверяем что переменная установлена
echo "Ваш домен: $MARZBAN_DOMAIN"
```

> ⚠️ **Важно**: Замените `your-domain.example.com` на ваш реальный домен!

---

## 🔒 Создание SSL-сертификата

### 3️⃣ Создание директории и генерация сертификата

```bash
# Создаем директорию для SSL сертификатов
sudo mkdir -p /opt/marzban/ssl

# Переходим в директорию
cd /opt/marzban/ssl

# Генерируем самоподписанный SSL-сертификат на год
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout marzban.key \
    -out marzban.crt \
    -subj "/C=RU/ST=Moscow/L=Moscow/O=MyOrganization/CN=$MARZBAN_DOMAIN"
```

### 4️⃣ Установка прав доступа

```bash
# Устанавливаем правильные права доступа
sudo chmod 600 /opt/marzban/ssl/marzban.key
sudo chmod 644 /opt/marzban/ssl/marzban.crt

# Проверяем созданные файлы
ls -la /opt/marzban/ssl/
```

**Ожидаемый результат:**
```
-rw-r--r-- 1 root root 1318 Sep 20 04:37 marzban.crt
-rw------- 1 root root 1704 Sep 20 04:37 marzban.key
```

---

## 📦 Установка Marzban

### 5️⃣ Автоматическая установка

```bash
# Установка Marzban с базой данных SQLite (рекомендуется)
sudo bash -c "$(curl -sL https://github.com/Gozargah/Marzban-scripts/raw/master/marzban.sh)" @ install
```

> 💡 **Альтернативные варианты:**
> ```bash
> # С MySQL
> sudo bash -c "$(curl -sL https://github.com/Gozargah/Marzban-scripts/raw/master/marzban.sh)" @ install --database mysql
> 
> # С MariaDB  
> sudo bash -c "$(curl -sL https://github.com/Gozargah/Marzban-scripts/raw/master/marzban.sh)" @ install --database mariadb
> ```

### 6️⃣ Остановка после установки

```bash
# Останавливаем Marzban для настройки SSL
marzban down
```

---

## ⚙️ Настройка SSL в Marzban

### 7️⃣ Копирование сертификатов

```bash
# Копируем сертификаты в рабочую директорию Marzban
sudo mkdir -p /var/lib/marzban/certs
sudo cp /opt/marzban/ssl/marzban.crt /var/lib/marzban/certs/
sudo cp /opt/marzban/ssl/marzban.key /var/lib/marzban/certs/

# Проверяем копирование
ls -la /var/lib/marzban/certs/
```

### 8️⃣ Редактирование конфигурации

```bash
# Открываем конфигурационный файл
sudo nano /opt/marzban/.env
```

**Добавьте или измените следующие параметры:**

```env
# 🌐 Настройки хоста и порта
UVICORN_HOST="0.0.0.0"
# Порт можно поменять(да же нужно)
UVICORN_PORT=8000
# ALLOWED_ORIGINS=http://localhost,http://localhost:8000,http://example.com

# URL настройки. Обязательно замените your-domain.example.com на ваш реальный домен!
SUB_BASE_URL=https://your-domain.example.com:8000

## We highly recommend add admin using `marzban cli` tool and do not use
## the following variables which is somehow hard codded infrmation.
# SUDO_USERNAME = "admin"
# SUDO_PASSWORD = "admin"

# UVICORN_UDS: "/run/marzban.socket"
# UVICORN_SSL_CERTFILE = "/var/lib/marzban/certs/example.com/fullchain.pem"
# UVICORN_SSL_KEYFILE = "/var/lib/marzban/certs/example.com/key.pem"
# UVICORN_SSL_CA_TYPE = "public"

# 🔒 SSL настройки
UVICORN_SSL_CERTFILE=/var/lib/marzban/certs/marzban.crt
UVICORN_SSL_KEYFILE=/var/lib/marzban/certs/marzban.key
UVICORN_SSL_CA_TYPE="private"

# DASHBOARD_PATH = "/dashboard/"

# 🎛 Скрытый путь к панели (придумайте свой путь)
DASHBOARD_PATH="/my-admin-panel/"

# 📁 Настройки Xray
XRAY_JSON="/var/lib/marzban/xray_config.json"
# Обязательно замените your-domain.example.com на ваш реальный домен!
XRAY_SUBSCRIPTION_URL_PREFIX="https://your-domain.example.com"
XRAY_SUBSCRIPTION_PATH="myconfig"
```

> ⚠️ **Обязательно замените** `your-domain.example.com` на ваш реальный домен!

**Сохранение файла:**
- Нажмите `Ctrl+S` для сохранения
- Нажмите `Ctrl+X` для выхода из редактора

---

## 👤 Создание администратора

### 9️⃣ Запуск Marzban с новой конфигурацией

```bash
# Запускаем Marzban с SSL
marzban up
```

Вы должны увидеть сообщение:
```
INFO: Uvicorn running on https://0.0.0.0:8000 (Press CTRL+C to quit)
```

### 🔟 Создание учетной записи администратора

Откройте **новый терминал** (или нажмите `Ctrl+C` для выхода из логов) и выполните:

```bash
# Создаем администратора с полными правами
marzban cli admin create --sudo
```

**Введите данные:**
- **Username**: `admin` (или любое другое имя)
- **Password**: `ваш_надежный_пароль`

---

## 🌐 Доступ к панели управления

### 1️⃣1️⃣ Настройка брандмауэра

```bash
# Открываем необходимые порты
sudo ufw allow 8000/tcp # Если меняли порт то меняем порт на свой
sudo ufw allow 80/tcp  
sudo ufw allow 443/tcp
sudo ufw enable

# Проверяем статус
sudo ufw status
```

### 1️⃣2️⃣ Доступ через браузер

1. **Откройте браузер** и перейдите по адресу:
   ```
   https://your-domain.example.com:8000/dashboard/
   ⚠️Если меняли порт то пишем свой порт,если был изменен путь к панели на скрытый путь то за место dashboard пишем свой путь.Обязательно замените your-domain.example.com на ваш реальный домен!
   ```

2. **Обход предупреждения SSL:**
   - **Chrome/Edge**: Нажмите "Advanced" → "Proceed to site (unsafe)"
   - **Firefox**: Нажмите "Advanced" → "Accept the Risk and Continue"
   - **Safari**: Нажмите "Advanced" → "Visit this website"

3. **Авторизация:**
   - Введите логин и пароль администратора
   - Нажмите "Login"

### 🎉 Поздравляем! 
Если вы видите панель управления Marzban - установка прошла успешно!

---

## 🔧 Настройка подключений

По умолчанию в Marzban доступно создание подключений только по протоколу **Shadowsocks**. Для добавления других протоколов выполните следующие шаги:

### 🎛️ Добавление протоколов VLESS, TROJAN, VMESS

#### 1️⃣ Откройте настройки Xray
1. В правом верхнем углу панели нажмите на значок **шестеренки** ⚙️
2. Откроются настройки подключений

#### 2️⃣ Замените конфигурацию
1. **Удалите** все содержимое из поля "Конфигурация"
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

Перед сохранением конфигурации необходимо сгенерировать **privateKey** и **shortIds** для протокола REALITY:

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

## 🔧 Дополнительные настройки

```bash
# Включаем автозапуск Marzban при загрузке системы
sudo systemctl enable docker
```

### 📊 Мониторинг логов

```bash
# Просмотр текущих логов
marzban logs

# Просмотр логов в реальном времени
marzban logs -f

# Остановка просмотра логов
# Нажмите Ctrl+C
```

### 🤖 Настройка Telegram Bot (опционально)

Добавьте в `/opt/marzban/.env`:

```env
# Telegram Bot настройки
TELEGRAM_API_TOKEN=123456789:AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
TELEGRAM_ADMIN_ID=987654321
```

### 💾 Резервное копирование

```bash
# Создание бэкапа
sudo tar -czf marzban-backup-$(date +%Y%m%d).tar.gz \
    /opt/marzban \
    /var/lib/marzban

# Восстановление из бэкапа
sudo tar -xzf marzban-backup-YYYYMMDD.tar.gz -C /
```

---

## 🔧 Устранение проблем

### ❌ Проблема: "SSL certificate file does not exist"

**Решение:**
```bash
# Проверьте наличие файлов
ls -la /var/lib/marzban/certs/

# Если файлов нет, скопируйте заново
sudo cp /opt/marzban/ssl/* /var/lib/marzban/certs/
```

### ❌ Проблема: "Certificate verification failed"

**Решение:**
```bash
# Убедитесь что в конфигурации указано:
# UVICORN_SSL_CA_TYPE="private"

sudo nano /opt/marzban/.env
marzban restart
```

### ❌ Проблема: "Port already in use"

**Решение:**
```bash
# Найдите процесс использующий порт
sudo lsof -i :8000

# Остановите конфликтующий процесс
sudo kill -9 [PID]

# Перезапустите Marzban
marzban restart
```

### ❌ Проблема: "Domain not accessible"

**Решение:**
1. Проверьте DNS настройки домена
2. Убедитесь что домен указывает на IP сервера
3. Проверьте настройки брандмауэра
4. Попробуйте доступ по IP: `https://YOUR_IP:8000/dashboard/`

---

## 📚 Полезные команды

### 🎛️ Управление Marzban

```bash
# Запуск
marzban up

# Остановка  
marzban down

# Перезапуск
marzban restart

# Просмотр статуса
marzban status

# Просмотр логов
marzban logs

# Помощь по командам
marzban --help
```

### 👥 Управление пользователями

```bash
# Создание администратора
marzban cli admin create --sudo

# Создание обычного администратора
marzban cli admin create

# Список администраторов
marzban cli admin list

# Помощь по CLI
marzban cli --help
```

### 🔄 Обновление Marzban

```bash
# Обновление до последней версии
sudo bash -c "$(curl -sL https://github.com/Gozargah/Marzban-scripts/raw/master/marzban.sh)" @ update
```

### 📁 Важные пути

| Назначение | Путь |
|------------|------|
| Конфигурация | `/opt/marzban/.env` |
| SSL сертификаты | `/var/lib/marzban/certs/` |
| База данных | `/var/lib/marzban/db.sqlite3` |
| Логи | `marzban logs` |
| Xray конфиг | `/var/lib/marzban/xray_config.json` |

---

## 🔐 Рекомендации по безопасности

### 🛡️ Основные меры:

1. **Смените пароли по умолчанию**
2. **Регулярно создавайте резервные копии**
3. **Обновляйте Marzban до последних версий**
4. **Ограничьте доступ к серверу через SSH**
5. **Используйте сложные пароли для администраторов**

### 🔒 Получение реального SSL-сертификата:

Для продакшен-использования рекомендуется получить бесплатный сертификат от Let's Encrypt:

```bash
# Установка Certbot
sudo apt install certbot -y

# Получение сертификата
sudo certbot certonly --standalone -d your-domain.example.com

# Сертификаты будут в:
# /etc/letsencrypt/live/your-domain.example.com/fullchain.pem
# /etc/letsencrypt/live/your-domain.example.com/privkey.pem
```

---

## 📞 Поддержка и сообщество

- 📖 **Документация**: [Marzban Docs](https://gozargah.github.io/marzban)
- 💬 **Telegram**: [Marzban Support](https://t.me/gozargah_marzban)
- 🐙 **GitHub**: [Marzban Repository](https://github.com/Gozargah/Marzban)
- 🔧 **Issues**: [Bug Reports](https://github.com/gozargah/marzban/issues)

---

## 📝 Заключение

Поздравляем! Вы успешно установили и настроили **Marzban** с самоподписанным SSL-сертификатом. 

### ✅ Что вы получили:
- ✨ Работающую панель управления прокси
- 🔒 SSL-шифрование соединения
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

> 💡 **Совет**: Добавьте эту инструкцию в закладки - она поможет вам в будущем при обновлениях или переустановке системы.