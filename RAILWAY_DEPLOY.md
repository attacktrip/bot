# Railway deploy (site + bot)

## 1) Что уже подготовлено в коде
- Добавлен production `Dockerfile` (Python 3.11 + `python -m pip install -r requirements.txt`), чтобы исключить ошибку `pip: command not found` в сборке.
- Один процесс поднимает Flask API + frontend и Telegram-бота в фоне (`site/backend/app.py`).
- Добавлен healthcheck endpoint: `GET /healthz`.
- Добавлен `railway.json`, который **принудительно выбирает builder `DOCKERFILE`** (без Nixpacks) и healthcheck `GET /healthz`.
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
1. В `railway.json` уже зафиксирован builder `DOCKERFILE`, поэтому Railway берёт `Dockerfile` из корня репозитория.
2. `New Project` → `Deploy from GitHub`.
3. Выберите репозиторий.
4. Добавьте переменные из блока выше.
5. Подключите Volume (опционально, но желательно).
6. Сделайте Redeploy.

## 5) Настройка Web App в BotFather
1. `@BotFather` → `/mybots` → ваш бот.
2. `Bot Settings` → `Menu Button` → `Web App`.
3. URL: `https://your-app.up.railway.app/app`.

## 6) Проверка
- `https://your-app.up.railway.app/healthz` → должно вернуть `{ "ok": true, ... }`.
- Откройте `/app` через Telegram.
- Если прилетает 401, нажмите «Авторизоваться через бота» и завершите deep-link поток.

## 7) Частые проблемы

- Если видите в логах `ENV NIXPACKS_PATH` и `RUN pip install -r requirements.txt`, значит Railway всё ещё собирает через Nixpacks. Проверьте, что в сервисе подключён актуальный `railway.json` (builder `DOCKERFILE`) и сделайте **Redeploy without cache**.
- Если в логах сборки есть `pip: command not found`, убедитесь что Railway собирает текущий `Dockerfile` из корня репозитория (а не старый кэш/другой Root Directory).
- **BOT_USERNAME не задан** → добавьте переменную `BOT_USERNAME` без `@`.
- **409 conflict у бота** → второй экземпляр с тем же токеном уже запущен (выключите локальный).
- **401 в браузере** → Mini App должен открываться из Telegram либо через web-session после deep-link авторизации.
