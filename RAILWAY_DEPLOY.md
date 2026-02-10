# Railway deploy (site + bot)

## 1) Что уже подготовлено в коде
- Один процесс поднимает Flask API + frontend и Telegram-бота в фоне (`site/backend/app.py`).
- Добавлен healthcheck endpoint: `GET /healthz`.
- Добавлен `railway.json` с `startCommand` и `healthcheckPath`.
- Все ключевые настройки идут через переменные окружения.

## 2) Обязательные переменные в Railway
```env
BOT_TOKEN=...
BOT_USERNAME=your_bot_username
SITE_URL=https://your-app.up.railway.app
PORT=5000
ADMIN_CHAT_ID=123456789
```

## 3) Рекомендуемые переменные
```env
ADMIN2_CHAT_ID=0
NICKNAME_ADMIN=tr2bel
CHAT_BOT_LINK=https://t.me/your_chat
INSTRUCTION_LINK=https://telegra.ph/your_instruction
PROCENT=5
NUMBER_QIWI=+79000000000
TOKEN_QIWI=
DATABASE_PATH=/data/db.db
```

> Для сохранения данных между рестартами подключите **Railway Volume** и используйте путь вроде `/data/db.db`.

## 4) Быстрый порядок деплоя
1. `New Project` → `Deploy from GitHub`.
2. Выберите репозиторий.
3. Добавьте переменные из блока выше.
4. Подключите Volume (опционально, но желательно).
5. Сделайте Redeploy.

## 5) Настройка Web App в BotFather
1. `@BotFather` → `/mybots` → ваш бот.
2. `Bot Settings` → `Menu Button` → `Web App`.
3. URL: `https://your-app.up.railway.app/app`.

## 6) Проверка
- `https://your-app.up.railway.app/healthz` → должно вернуть `{ "ok": true, ... }`.
- Откройте `/app` через Telegram.
- Если прилетает 401, нажмите «Авторизоваться через бота» и завершите deep-link поток.

## 7) Частые проблемы
- **BOT_USERNAME не задан** → добавьте переменную `BOT_USERNAME` без `@`.
- **409 conflict у бота** → второй экземпляр с тем же токеном уже запущен (выключите локальный).
- **401 в браузере** → Mini App должен открываться из Telegram либо через web-session после deep-link авторизации.
