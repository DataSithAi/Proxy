# VLESS REALITY Protocol - Принцип работы

![Protocol](https://img.shields.io/badge/Protocol-VLESS_REALITY-blue?style=for-the-badge)
![Xray Core](https://img.shields.io/badge/Xray--core-v1.8.0+-brightgreen?style=for-the-badge)
![License](https://img.shields.io/badge/License-MPL--2.0-orange?style=for-the-badge)
![Security](https://img.shields.io/badge/Security-Military_Grade-red?style=for-the-badge)
![Anti Censorship](https://img.shields.io/badge/Anti--Censorship-✓-success?style=for-the-badge)

---

## 📋 Содержание

- [Введение](#-введение)
- [Что такое VLESS](#-что-такое-vless)
- [Что такое REALITY](#-что-такое-reality)
- [Принцип работы](#-принцип-работы)
- [Технические детали](#-технические-детали)
- [Преимущества](#-преимущества)
- [Недостатки и ограничения](#-недостатки-и-ограничения)
- [Сравнение с другими протоколами](#-сравнение-с-другими-протоколами)
- [Настройка REALITY](#-настройка-reality)
- [Официальные ресурсы](#-официальные-ресурсы)
- [FAQ](#-faq)

---

## 🎯 Введение

**VLESS REALITY** — это революционный протокол обхода цензуры, разработанный командой **Project X** для **Xray-core**. Он представляет собой следующее поколение технологий антиблокировки, устраняя TLS-отпечатки сервера и решая проблемы с SNI блокировкой.

### Ключевые характеристики:

![No Domain](https://img.shields.io/badge/Требуется_домен-НЕТ-success?style=flat-square)
![No Certificate](https://img.shields.io/badge/Требуется_сертификат-НЕТ-success?style=flat-square)
![Perfect Forward Secrecy](https://img.shields.io/badge/Perfect_Forward_Secrecy-ДА-brightgreen?style=flat-square)
![Anti Detection](https://img.shields.io/badge/Обнаружение-НЕВОЗМОЖНО-red?style=flat-square)

---

## 🔷 Что такое VLESS

### Определение

**VLESS** (Very Lightweight Extended Shadow Socket) — это легковесный транспортный протокол без сохранения состояния, разработанный для Xray-core.

### Основные особенности:

```
┌─────────────────────────────────────────┐
│           VLESS Protocol                │
├─────────────────────────────────────────┤
│  ✓ Минимальные накладные расходы       │
│  ✓ Не сохраняет состояние              │
│  ✓ Разделение inbound/outbound         │
│  ✓ Поддержка UUID аутентификации       │
│  ✓ Поддержка множества транспортов     │
│  ✓ Совместимость с XTLS                │
└─────────────────────────────────────────┘
```

### Структура VLESS пакета:

```
┌──────────────┬──────────────┬──────────────┐
│   Version    │   UUID Auth  │   Payload    │
│   (1 byte)   │  (16 bytes)  │  (Variable)  │
└──────────────┴──────────────┴──────────────┘
```

**Преимущества над VMess:**
- 📉 Меньше накладных расходов (overhead)
- ⚡ Быстрее обработка пакетов
- 🔒 Более простая, но надежная криптография
- 🎯 Лучшая производительность

---

## 🌐 Что такое REALITY

### Революция в обходе цензуры

**REALITY** — это протокол безопасности, который **заменяет TLS** в стеке соединений Xray, используя SNI реальных веб-сайтов для маскировки трафика.

### Основная концепция:

```
┌──────────────────────────────────────────────────────────┐
│  Цензор видит подключение к: github.com                  │
│  На самом деле: Зашифрованное VPN соединение             │
│  Результат: Невозможно отличить от обычного HTTPS        │
└──────────────────────────────────────────────────────────┘
```

### Ключевые инновации:

#### 1. **Отсутствие собственного TLS сертификата**
- ❌ Не нужен домен
- ❌ Не нужен SSL/TLS сертификат
- ✅ Использует сертификат целевого сайта

#### 2. **Маскировка под реальный сайт**
- 🎭 Имитирует подключение к настоящему HTTPS сайту
- 🔍 Цензор видит легитимный трафик
- 🛡️ Невозможно заблокировать без блокировки целевого сайта

#### 3. **Perfect Forward Secrecy**
- 🔐 Каждая сессия использует уникальные ключи
- 🔑 Компрометация одного ключа не влияет на другие сессии
- 🛡️ Защита от атак записи трафика

---

## ⚙️ Принцип работы

### Архитектура REALITY

```
┌─────────────┐                    ┌──────────────┐                    ┌─────────────┐
│   Клиент    │◄──────────────────►│ REALITY      │◄──────────────────►│  Целевой    │
│   (VPN)     │   Замаскированный  │ Сервер       │   Настоящий TLS    │  сайт       │
│             │   трафик (TLS)     │ (Xray-core)  │   (Fallback)       │ (github.com)│
└─────────────┘                    └──────────────┘                    └─────────────┘
       │                                   │                                   │
       │  1. Client Hello (SNI)            │                                   │
       ├──────────────────────────────────►│                                   │
       │                                   │  2. Проверка handshake            │
       │                                   │                                   │
       │  3a. Valid client                 │                                   │
       │◄──────────────────────────────────┤                                   │
       │     Temporary cert                │                                   │
       │     (REALITY соединение)          │                                   │
       │                                   │                                   │
       │  3b. Invalid client               │  4. Fallback                      │
       │                                   ├──────────────────────────────────►│
       │                                   │  5. Real cert                     │
       │◄──────────────────────────────────┼───────────────────────────────────┤
       │     Перенаправление на реальный сайт                                  │
```

### Пошаговый процесс:

#### **Этап 1: Установка соединения (Handshake)**

1. **Клиент отправляет Client Hello:**
```
Client → Server: 
{
  "SNI": "github.com",
  "Fingerprint": "uTLS fingerprint",
  "Auth": "Short ID + Public Key"
}
```

2. **Сервер анализирует запрос:**
   - ✅ Проверяет Short ID (валидный клиент?)
   - ✅ Проверяет криптографическую подпись
   - ✅ Проверяет соответствие TLS fingerprint

#### **Этап 2: Принятие решения**

**Сценарий A: Валидный REALITY клиент** ✅
```
Server → Client:
{
  "Certificate": "Временный самоподписанный сертификат",
  "Status": "REALITY mode activated"
}
```
- Создается зашифрованный туннель
- Начинается передача VPN трафика
- Соединение неотличимо от настоящего TLS

**Сценарий B: Обычный браузер** 🌐
```
Server → Target Site (github.com):
{
  "Action": "Forward connection",
  "Mode": "Transparent proxy"
}

Target Site → Client:
{
  "Certificate": "Настоящий сертификат github.com",
  "Content": "Реальная веб-страница"
}
```
- Запрос перенаправляется на настоящий сайт
- Пользователь видит обычную веб-страницу
- Цензор видит легитимное HTTPS соединение

#### **Этап 3: Передача данных**

**Для REALITY клиента:**
```
┌──────────────────────────────────────┐
│  Encrypted VPN Data                  │
│  ┌────────────────────────────────┐  │
│  │ VLESS Protocol                 │  │
│  │  ┌──────────────────────────┐  │  │
│  │  │ User Traffic (HTTPS/TCP) │  │  │
│  │  └──────────────────────────┘  │  │
│  └────────────────────────────────┘  │
│  Wrapped in TLS-like encryption      │
└──────────────────────────────────────┘
```

---

## 🔬 Технические детали

### Компоненты REALITY

#### 1. **Public Key / Private Key пара**

**Генерация:**
```bash
xray x25519
```

**Вывод:**
```
Private key: gK3C9TVRXZ_Zp-rRCi3bkp3HdS6RJX8Xz7lJ_Nk1234
Public key: 7vCwRJ_2sB8JmzY-K5P7ZxQpS3hN6Vt4Rl9F_Ab5678
```

**Назначение:**
- 🔐 Private Key - хранится на сервере
- 🔑 Public Key - передается клиенту
- ✅ Используются для криптографической аутентификации

#### 2. **Short ID**

**Генерация:**
```bash
openssl rand -hex 8
```

**Вывод:**
```
a1b2c3d4e5f67890
```

**Назначение:**
- 🎫 Уникальный идентификатор клиента
- ⚡ Быстрая проверка валидности
- 🛡️ Защита от активного зондирования

#### 3. **Server Name (SNI)**

**Примеры целевых сайтов:**
```
✅ github.com
✅ www.microsoft.com
✅ www.apple.com
✅ www.cloudflare.com
✅ www.amazon.com
```

**Критерии выбора:**
- ✅ Крупный известный сайт
- ✅ Поддержка TLS 1.3
- ✅ Стабильная доступность
- ✅ Не может быть заблокирован цензором
- ❌ Не использовать правительственные сайты

#### 4. **uTLS (micro Transport Layer Security)**

**Что это:**
- 🎭 Имитация TLS fingerprint популярных браузеров
- 🦊 Firefox, Chrome, Safari, Edge
- 🔍 Неотличимо от настоящего браузера

**Доступные fingerprints:**
```
- chrome
- firefox  
- safari
- ios
- android
- edge
- 360
- qq
- random
- randomized
```

### Криптография

#### **Используемые алгоритмы:**

| Компонент | Алгоритм | Описание |
|-----------|----------|----------|
| **Обмен ключами** | X25519 | Elliptic Curve Diffie-Hellman |
| **Шифрование** | ChaCha20-Poly1305 | AEAD шифрование |
| **Аутентификация** | BLAKE3 | Хэш-функция |
| **TLS** | TLS 1.3 | Современный TLS |

#### **Perfect Forward Secrecy:**

```
Сессия 1: Key₁ → Перехвачен → Скомпрометирован
Сессия 2: Key₂ → НЕ затронут ✅
Сессия 3: Key₃ → НЕ затронут ✅
```

Каждая сессия использует уникальные эфемерные ключи.

---

## ✨ Преимущества

### 🎯 Для пользователя:

| Преимущество | Описание | Значимость |
|--------------|----------|------------|
| **Не нужен домен** | Работает на голом IP | ⭐⭐⭐⭐⭐ |
| **Не нужен сертификат** | Нет расходов на SSL | ⭐⭐⭐⭐⭐ |
| **Простая настройка** | Минимум параметров | ⭐⭐⭐⭐ |
| **Высокая скорость** | Минимальные накладные расходы | ⭐⭐⭐⭐⭐ |
| **Максимальная безопасность** | Military-grade encryption | ⭐⭐⭐⭐⭐ |

### 🛡️ Против цензуры:

#### **1. Невидимость для DPI (Deep Packet Inspection)**

```
Цензор анализирует пакет:
┌──────────────────────────────────┐
│ SNI: github.com          ✅      │
│ TLS Version: 1.3         ✅      │
│ Certificate: Valid       ✅      │
│ Fingerprint: Chrome      ✅      │
│ Timing: Normal           ✅      │
└──────────────────────────────────┘
Вердикт: Обычный HTTPS → Пропустить
```

#### **2. Устойчивость к активному зондированию**

```
Атакующий подключается без правильных ключей:
┌──────────────────────────────────┐
│ Попытка подключения              │
│ ↓                                │
│ Перенаправление на github.com    │
│ ↓                                │
│ Получение реального сайта        │
│ ↓                                │
│ Вывод: Обычный веб-сервер ✅     │
└──────────────────────────────────┘
```

#### **3. Защита от блокировки**

Чтобы заблокировать REALITY сервер, цензору придется:
- ❌ Заблокировать github.com (невозможно)
- ❌ Заблокировать весь TLS 1.3 трафик (невозможно)
- ❌ Заблокировать все IP адреса (невозможно)

### 🚀 Производительность:

| Метрика | REALITY | OpenVPN | WireGuard | Shadowsocks |
|---------|---------|---------|-----------|-------------|
| **Скорость** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Задержка** | 5-10ms | 20-50ms | 5-15ms | 10-20ms |
| **CPU нагрузка** | Низкая | Высокая | Средняя | Низкая |
| **Обход блокировок** | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐ |

---

## ⚠️ Недостатки и ограничения

### Технические ограничения:

#### 1. **Зависимость от целевого сайта**

```diff
- Если github.com недоступен → fallback не работает
- Если сайт изменит конфигурацию TLS → может потребоваться обновление
+ Решение: Использовать несколько запасных SNI
```

#### 2. **Требования к клиенту**

```
Клиент должен поддерживать:
✓ VLESS протокол
✓ REALITY
✓ uTLS fingerprinting  
✓ X25519 key exchange

Старые клиенты НЕ будут работать ❌
```

#### 3. **Сложность отладки**

```
Проблема: Соединение не работает
Причин может быть много:
- Неправильный Private Key
- Неправильный Short ID
- Неправильный SNI
- Блокировка целевого сайта
- Проблемы с uTLS
```

### Организационные ограничения:

- 📚 **Документация**: Меньше материалов чем для OpenVPN
- 👥 **Сообщество**: Меньше сообщество чем у WireGuard
- 🔧 **Поддержка**: Ограниченная поддержка в панелях управления

---

## 📊 Сравнение с другими протоколами

### REALITY vs VMess

| Характеристика | REALITY | VMess |
|----------------|---------|-------|
| **Скорость** | Быстрее (меньше overhead) | Медленнее |
| **Безопасность** | TLS 1.3 + Perfect Forward Secrecy | AES-128-GCM |
| **Обнаружение** | Практически невозможно | Возможно по паттернам |
| **Домен/Сертификат** | НЕ требуется | Требуется для TLS |
| **Сложность** | Средняя | Простая |

### REALITY vs Shadowsocks

| Характеристика | REALITY | Shadowsocks |
|----------------|---------|-------------|
| **Обход DPI** | Отлично | Хорошо |
| **Скорость** | Очень высокая | Высокая |
| **Активное зондирование** | Защищен | Уязвим |
| **Маскировка** | Под легитимный HTTPS | Под случайный трафик |
| **Сложность настройки** | Средняя | Простая |

### REALITY vs Trojan

| Характеристика | REALITY | Trojan |
|----------------|---------|--------|
| **Требуется домен** | НЕТ ✅ | ДА ❌ |
| **Требуется сертификат** | НЕТ ✅ | ДА ❌ |
| **Fallback** | На реальный сайт | На веб-сервер |
| **Обнаружение** | Невозможно | Возможно по сертификату |
| **Perfect Forward Secrecy** | ДА ✅ | НЕТ ❌ |

### REALITY vs WireGuard

| Характеристика | REALITY | WireGuard |
|----------------|---------|-----------|
| **Скорость** | Очень высокая | Максимальная |
| **Обход блокировок** | Отлично | Плохо |
| **Маскировка** | Под HTTPS | Легко обнаружим |
| **Сложность** | Средняя | Простая |
| **Use case** | Обход цензуры | Приватность |

---

## 🔧 Настройка REALITY

### Серверная конфигурация (Xray-core):

```json
{
  "inbounds": [
    {
      "port": 443,
      "protocol": "vless",
      "settings": {
        "clients": [
          {
            "id": "UUID-клиента",
            "flow": "xtls-rprx-vision"
          }
        ],
        "decryption": "none"
      },
      "streamSettings": {
        "network": "tcp",
        "security": "reality",
        "realitySettings": {
          "show": false,
          "dest": "github.com:443",
          "xver": 0,
          "serverNames": [
            "github.com"
          ],
          "privateKey": "ВАШ_PRIVATE_KEY",
          "shortIds": [
            "ВАШ_SHORT_ID"
          ]
        }
      }
    }
  ]
}
```

### Клиентская конфигурация:

```json
{
  "outbounds": [
    {
      "protocol": "vless",
      "settings": {
        "vnext": [
          {
            "address": "IP_СЕРВЕРА",
            "port": 443,
            "users": [
              {
                "id": "UUID-клиента",
                "encryption": "none",
                "flow": "xtls-rprx-vision"
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "tcp",
        "security": "reality",
        "realitySettings": {
          "show": false,
          "fingerprint": "chrome",
          "serverName": "github.com",
          "publicKey": "PUBLIC_KEY_СЕРВЕРА",
          "shortId": "SHORT_ID",
          "spiderX": "/"
        }
      }
    }
  ]
}
```

### Генерация ключей:

```bash
# Установка Xray-core
bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install

# Генерация X25519 ключей
xray x25519
# Вывод:
# Private key: [используйте на сервере]
# Public key: [используйте на клиенте]

# Генерация Short ID
openssl rand -hex 8
# Вывод: a1b2c3d4e5f67890

# Генерация UUID для клиента
cat /proc/sys/kernel/random/uuid
# Вывод: 550e8400-e29b-41d4-a716-446655440000
```

---

## 📚 Официальные ресурсы

### GitHub Репозитории:

[![Xray-core](https://img.shields.io/badge/GitHub-Xray--core-181717?style=for-the-badge&logo=github)](https://github.com/XTLS/Xray-core)

**Описание:** Основной репозиторий Xray-core  
**Звезды:** ![GitHub stars](https://img.shields.io/github/stars/XTLS/Xray-core?style=flat-square)  
**Лицензия:** MPL-2.0  
**Ссылка:** https://github.com/XTLS/Xray-core

---

[![REALITY](https://img.shields.io/badge/GitHub-REALITY-181717?style=for-the-badge&logo=github)](https://github.com/XTLS/REALITY)

**Описание:** Официальный репозиторий протокола REALITY  
**Звезды:** ![GitHub stars](https://img.shields.io/github/stars/XTLS/REALITY?style=flat-square)  
**Документация:** THE NEXT FUTURE  
**Ссылка:** https://github.com/XTLS/REALITY

---

[![Xray-examples](https://img.shields.io/badge/GitHub-Xray--examples-181717?style=for-the-badge&logo=github)](https://github.com/XTLS/Xray-examples)

**Описание:** Примеры конфигураций Xray  
**Звезды:** ![GitHub stars](https://img.shields.io/github/stars/XTLS/Xray-examples?style=flat-square)  
**Содержит:** Примеры REALITY настроек  
**Ссылка:** https://github.com/XTLS/Xray-examples

### Документация:

- 📖 [Официальная документация Xray](https://xtls.github.io/)
- 📖 [REALITY Protocol Documentation](https://github.com/XTLS/Xray-examples/blob/main/VLESS-TCP-XTLS-Vision-REALITY/REALITY.ENG.md)
- 📖 [VLESS Protocol Specification](https://github.com/XTLS/Xray-core/discussions/1515)

### Сообщества:

- 💬 [Telegram: Project X](https://t.me/projectXray)
- 💬 [Telegram: Xray 中文](https://t.me/projectXtls)
- 💬 [GitHub Discussions](https://github.com/XTLS/Xray-core/discussions)

---

## ❓ FAQ

### 1. Нужен ли мне домен для REALITY?

**Ответ:** ❌ НЕТ! Это главное преимущество REALITY. Вы используете SNI существующего сайта (например, github.com), но ваш сервер работает на обычном IP без домена.

### 2. Безопаснее ли REALITY чем Shadowsocks?

**Ответ:** ✅ ДА! REALITY обеспечивает:
- Perfect Forward Secrecy
- Защиту от активного зондирования  
- TLS 1.3 шифрование
- Невозможность обнаружения

### 3. Может ли цензор заблокировать REALITY?

**Ответ:** 🛡️ ОЧЕНЬ СЛОЖНО! Для блокировки цензору нужно:
- Заблокировать целевой сайт (github.com) — это затронет миллионы пользователей
- Различить REALITY трафик от обычного HTTPS — технически невозможно
- Заблокировать все IP адреса — нереально

### 4. Какой целевой сайт выбрать для SNI?

**Ответ:** Лучшие варианты:
- ✅ **github.com** - отличный выбор
- ✅ **www.microsoft.com** - крупный сайт
- ✅ **www.cloudflare.com** - CDN провайдер
- ✅ **www.apple.com** - никогда не заблокируют
- ❌ Избегайте правительственных сайтов
- ❌ Избегайте сайтов с нестабильной работой

### 5. Работает ли REALITY на мобильных устройствах?

**Ответ:** ✅ ДА! Поддерживается в:
- v2rayNG (Android)
- Hiddify (Android/iOS)
- NekoBox (Android)
- Streisand (iOS)

---

## 🎯 Заключение

### Почему REALITY - это будущее:

```
┌────────────────────────────────────────────────┐
│  ✅ Не требует домен и сертификат              │
│  ✅ Невозможно обнаружить цензурой             │
│  ✅ Максимальная скорость и безопасность       │
│  ✅ Perfect Forward Secrecy                    │
│  ✅ Защита от активного зондирования           │
│  ✅ Open Source и бесплатно                    │
│  ✅ Активное развитие и поддержка              │
└────────────────────────────────────────────────┘
```

---

*Последнее обновление: Октябрь 2024*

![Made with](https://img.shields.io/badge/Made_with-❤️_and_Code-red?style=for-the-badge)
![Fight Censorship](https://img.shields.io/badge/Fight-Censorship-orange?style=for-the-badge)
![Freedom](https://img.shields.io/badge/Internet-Freedom-brightgreen?style=for-the-badge)
![Open Source](https://img.shields.io/badge/Open-Source-blue?style=for-the-badge)

---

**Свободный интернет - это право каждого человека!** 🌍✨

```
╔═══════════════════════════════════════╗
║   "THE NEXT FUTURE IS REALITY"        ║
║         - Project X Team              ║
╚═══════════════════════════════════════╝
```