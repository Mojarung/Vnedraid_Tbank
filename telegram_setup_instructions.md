# 🚀 Telegram News Parser - Инструкции по настройке

## 📋 Что это такое?

Парсер новостей из Telegram каналов, который:
- Собирает сообщения из указанных каналов
- Фильтрует по ключевым словам
- Автоматически категоризирует новости
- Скачивает медиа-контент (по желанию)
- Сохраняет в JSON и базу данных

## 🛠️ Установка зависимостей

### 1. Обновите requirements.txt

Добавьте в ваш `requirements.txt`:

```
telethon==1.36.0
aiofiles==23.2.1
```

### 2. Установите пакеты

```bash
pip install telethon aiofiles
```

## 🔑 Получение API ключей Telegram

### Шаг 1: Регистрация приложения

1. Зайдите на https://my.telegram.org/auth
2. Войдите используя ваш номер телефона
3. Перейдите в **"API Development Tools"**
4. Создайте новое приложение:
   - **App title**: `News Parser`
   - **Short name**: `news_parser` 
   - **URL**: оставьте пустым
   - **Platform**: Desktop
   - **Description**: `News parsing application`

### Шаг 2: Сохраните данные

Вы получите:
- `api_id` (число, например: 12345678)
- `api_hash` (строка, например: "abcd1234efgh5678...")

## ⚙️ Настройка

### 1. Создайте файл конфигурации

Создайте файл `.env` в корне проекта:

```env
# Telegram API credentials
TELEGRAM_API_ID=12345678
TELEGRAM_API_HASH=your_api_hash_here
TELEGRAM_PHONE_NUMBER=+7XXXXXXXXXX

# Channels to monitor (comma-separated)
TELEGRAM_CHANNELS=@breakingmash,@RIANovosti,@tass_agency,@rbc_news,@vedomosti

# Keywords for filtering (comma-separated)
TELEGRAM_KEYWORDS=экономика,финансы,рынок,биржа,валюта,инвестиции,банк,ЦБ,доллар,рубль

# Exclude keywords (comma-separated)  
TELEGRAM_EXCLUDE_KEYWORDS=реклама,спам,розыгрыш

# Parsing settings
TELEGRAM_MAX_MESSAGES=100
TELEGRAM_HOURS_BACK=24
TELEGRAM_DOWNLOAD_MEDIA=false
```

### 2. Создайте простой запускающий скрипт

Создайте файл `run_telegram.py`:

```python
import asyncio
import os
from dotenv import load_dotenv
from telegram_news_parser import TelegramParserConfig, parse_telegram_news

# Загрузка переменных окружения
load_dotenv()

async def main():
    # Конфигурация
    config = TelegramParserConfig(
        api_id=int(os.getenv("TELEGRAM_API_ID")),
        api_hash=os.getenv("TELEGRAM_API_HASH"),
        phone_number=os.getenv("TELEGRAM_PHONE_NUMBER"),
        channels=os.getenv("TELEGRAM_CHANNELS", "").split(","),
        keywords=os.getenv("TELEGRAM_KEYWORDS", "").split(","),
        exclude_keywords=os.getenv("TELEGRAM_EXCLUDE_KEYWORDS", "").split(","),
        max_messages=int(os.getenv("TELEGRAM_MAX_MESSAGES", "100")),
        hours_back=int(os.getenv("TELEGRAM_HOURS_BACK", "24")),
        download_media=os.getenv("TELEGRAM_DOWNLOAD_MEDIA", "false").lower() == "true"
    )
    
    try:
        # Запуск парсинга
        news_list = await parse_telegram_news(config)
        print(f"🎉 Успешно получено {len(news_list)} новостей!")
        
        # Показать примеры
        for i, news in enumerate(news_list[:5], 1):
            print(f"\n📰 Новость {i}:")
            print(f"   📺 Канал: {news.channel_title}")
            print(f"   📅 Дата: {news.date.strftime('%Y-%m-%d %H:%M')}")
            print(f"   📁 Категория: {news.category}")
            print(f"   📝 Текст: {news.text[:150]}...")
            
    except Exception as e:
        print(f"❌ Ошибка: {e}")

if __name__ == "__main__":
    asyncio.run(main())
```

## 🚀 Первый запуск

### 1. Проверьте файлы

Должны быть созданы:
- `telegram_news_parser.py` ✅
- `.env` с вашими данными ✅
- `run_telegram.py` ✅

### 2. Запустите парсер

```bash
python run_telegram.py
```

### 3. Авторизация

При первом запуске:
1. Telegram отправит код на ваш номер
2. Введите код в консоли
3. Если включена 2FA, введите пароль
4. Сессия сохранится в файле `telegram_parser.session`

## 📺 Рекомендуемые каналы

### Новости и политика
```
@breakingmash      # Mash (популярные новости)
@RIANovosti        # РИА Новости
@tass_agency       # ТАСС
@rt_russian        # RT на русском
@bbcrussian        # BBC Русская служба
@kremlinpoolcom    # Кремлевский пул
@meduzaproject     # Медуза
```

### Экономика и финансы
```
@rbc_news          # РБК
@vedomosti         # Ведомости  
@kommersant        # Коммерсант
@forbesrussia      # Forbes Russia
@finobzor          # Финансовый обзор
```

### Технологии
```
@rusbase           # RusBase
@vc_news           # VC.ru
@habr_com          # Хабр
@tproger           # Типичный программист
```

## 🔍 Настройка фильтров

### Экономические ключевые слова:
```
экономика, финансы, рынок, биржа, валюта, инвестиции, банк, ЦБ, 
доллар, рубль, евро, нефть, газ, акции, облигации, фонд, кредит,
инфляция, ВВП, бюджет, налог, торговля, экспорт, импорт
```

### Технологические ключевые слова:
```
технологии, IT, интернет, стартап, цифровизация, ИИ, 
искусственный интеллект, блокчейн, криптовалюта, программирование,
разработка, софт, приложение, сайт, платформа
```

### Исключения (что НЕ парсить):
```
реклама, спам, розыгрыш, конкурс, промокод, скидка, акция, 
подписаться, лайк, репост
```

## 📊 Результаты

После запуска создаются файлы:

### 1. JSON с новостями
`telegram_news_YYYYMMDD_HHMMSS.json`

Пример структуры:
```json
[
  {
    "channel_username": "@breakingmash",
    "channel_title": "Mash",
    "message_id": 123456,
    "text": "Центробанк поднял ключевую ставку до 21%...",
    "date": "2024-01-15T10:30:00+00:00",
    "views": 15420,
    "forwards": 89,
    "media_urls": [],
    "media_type": null,
    "author": null,
    "tags": ["экономика", "ЦБ"],
    "category": "экономика"
  }
]
```

### 2. Логи
`telegram_parser.log`

Содержит подробную информацию о процессе:
```
2024-01-15 10:30:15 - INFO - ✅ Telegram клиент успешно инициализирован  
2024-01-15 10:30:16 - INFO - 📺 Парсинг канала: Mash (@breakingmash)
2024-01-15 10:30:18 - INFO - ✅ Канал @breakingmash: получено 15 новостей
2024-01-15 10:30:20 - INFO - 💾 Новости сохранены в файл: telegram_news_20240115_103020.json
2024-01-15 10:30:21 - INFO - 📊 === СТАТИСТИКА ПАРСИНГА ===
2024-01-15 10:30:21 - INFO - 📝 Всего обработано: 45
2024-01-15 10:30:21 - INFO - 🚫 Отфильтровано: 23  
2024-01-15 10:30:21 - INFO - 💾 Сохранено: 22
```

## ⚡ Автоматизация

### 1. Создайте scheduler.py для периодического запуска:

```python
import asyncio
import schedule
import time
from run_telegram import main as parse_main

def run_parser():
    """Запуск парсера"""
    try:
        asyncio.run(parse_main())
        print("✅ Парсинг завершен")
    except Exception as e:
        print(f"❌ Ошибка: {e}")

# Настройка расписания
schedule.every(1).hours.do(run_parser)        # Каждый час
# schedule.every().day.at("09:00").do(run_parser)  # Каждый день в 9:00

if __name__ == "__main__":
    print("🔄 Запуск планировщика...")
    run_parser()  # Запуск сразу
    
    while True:
        schedule.run_pending()
        time.sleep(60)
```

### 2. Запустите планировщик:

```bash
python scheduler.py
```

## 🚨 Возможные проблемы

### ❌ Ошибка авторизации
```
SessionPasswordNeededError: Two-steps verification is enabled
```
**Решение**: Введите пароль 2FA когда система попросит

### ❌ Приватный канал
```
ChannelPrivateError: Channel is private  
```
**Решение**: Подпишитесь на канал через обычный Telegram

### ❌ Rate Limit
```
FloodWaitError: Too many requests
```
**Решение**: Парсер автоматически ждет, уменьшите `max_messages`

### ❌ Нет доступа к каналу
Убедитесь что:
- Канал существует
- Ваш аккаунт не забанен
- Канал не заблокирован в вашей стране

## 🔧 Интеграция с FastAPI

Добавьте в `app/api/telegram.py`:

```python
from fastapi import APIRouter, BackgroundTasks
from telegram_news_parser import TelegramParserConfig, parse_telegram_news

router = APIRouter(prefix="/telegram", tags=["telegram"])

@router.post("/parse")
async def start_parsing(background_tasks: BackgroundTasks):
    """Запуск парсинга в фоне"""
    config = TelegramParserConfig(
        api_id=int(os.getenv("TELEGRAM_API_ID")),
        api_hash=os.getenv("TELEGRAM_API_HASH"),
        # ... остальная конфигурация
    )
    
    background_tasks.add_task(parse_telegram_news, config)
    return {"message": "Парсинг запущен"}

@router.get("/news")
async def get_news(limit: int = 50):
    """Получение новостей"""
    # Здесь логика получения из БД
    pass
```

## 📈 Мониторинг

Следите за:
- Размером логов (`telegram_parser.log`)
- Количеством ошибок в статистике
- Актуальностью API ключей
- Доступностью каналов

## 🔒 Безопасность

⚠️ **ВАЖНО:**
- Никогда не коммитьте `.env` в git
- Добавьте `.env` в `.gitignore`
- Регулярно обновляйте зависимости
- Не делитесь API ключами

## 🆘 Поддержка

При проблемах проверьте:
1. Логи в файле `telegram_parser.log`
2. Правильность API ключей в `.env`
3. Доступность интернета
4. Статус Telegram API

---

**Готово! 🎉 Теперь у вас есть полнофункциональный парсер Telegram новостей.** 