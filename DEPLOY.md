# Lombard — Deploy Guide

## 1. Supabase (PostgreSQL + Storage)

### Создать проект
1. Зайти на [supabase.com](https://supabase.com) → **New project**
2. Organization: выбрать или создать
3. Name: `lombard`
4. Database Password: сгенерировать сильный пароль, сохранить в менеджер паролей
5. Region: `West EU (Ireland)` — ближайший к Польше
6. Нажать **Create new project** (ждать ~2 мин)

### Получить ключи
После создания: **Project Settings → API**
- `Project URL` → `SUPABASE_URL`
- `anon public` → `SUPABASE_KEY`
- `service_role` → `SUPABASE_SERVICE_ROLE_KEY` (только для бэкенда, не на фронтенд!)

### Database URL (прямое подключение)
**Project Settings → Database → Connection string → URI**
Использовать для `DATABASE_URL` в Railway (выбрать `Session mode` если через PgBouncer).

### Настроить Storage bucket
**Storage → New bucket**
- Name: `lombard-files`
- Public: нет (private)
- Policies: настроить под роли (сотрудник / менеджер)

---

## 2. Railway (FastAPI бэкенд)

### Создать проект
1. Зайти на [railway.app](https://railway.app) → **New Project**
2. **Deploy from GitHub repo** → выбрать репозиторий `lombard`
3. Railway автоматически определит Python и запустит деплой

### Переменные окружения
Railway Dashboard → проект → **Variables** → добавить:

| Переменная | Значение |
|---|---|
| `DATABASE_URL` | Connection string из Supabase (п. 1) |
| `TELEGRAM_BOT_TOKEN` | Токен из @BotFather |
| `TELEGRAM_WEBHOOK_SECRET` | Случайная строка ≥32 символов |
| `SUPABASE_URL` | URL из Supabase |
| `SUPABASE_KEY` | anon key из Supabase |
| `SUPABASE_SERVICE_ROLE_KEY` | service_role key |
| `SECRET_KEY` | `openssl rand -hex 32` |
| `ENVIRONMENT` | `production` |
| `DEBUG` | `false` |

> Railway автоматически инжектирует `PORT` — не нужно добавлять вручную.

### Настроить Telegram webhook
После деплоя получить URL приложения (вида `https://lombard-xxx.up.railway.app`).

```bash
curl "https://api.telegram.org/bot<TOKEN>/setWebhook?url=https://lombard-xxx.up.railway.app/webhook&secret_token=<WEBHOOK_SECRET>"
```

---

## 3. Локальная разработка

### Первый запуск
```bash
# 1. Скопировать переменные
cp .env.example .env
# Отредактировать .env — заполнить реальные значения

# 2. Запустить БД
docker-compose up db -d

# 3. Запустить API с hot-reload
docker-compose up api

# Или только БД локально, FastAPI запускать напрямую:
uvicorn app.main:app --reload
```

### Только БД (без Docker для API)
```bash
docker-compose up db -d

# Создать виртуальное окружение
python -m venv venv
venv\Scripts\activate      # Windows
source venv/bin/activate   # Linux/Mac

pip install -r requirements.txt
uvicorn app.main:app --reload
```

### Остановить
```bash
docker-compose down          # остановить контейнеры
docker-compose down -v       # + удалить данные БД (осторожно!)
```

---

## 4. Миграции (Alembic)

```bash
# Создать миграцию после изменения моделей
alembic revision --autogenerate -m "описание изменений"

# Применить миграции
alembic upgrade head

# Откатить последнюю
alembic downgrade -1
```

---

## 5. Чеклист перед продакшеном

- [ ] `ENVIRONMENT=production`, `DEBUG=false`
- [ ] `SECRET_KEY` — уникальный, не из примера
- [ ] Telegram webhook установлен и проверен
- [ ] Supabase Storage policies настроены
- [ ] `.env` добавлен в `.gitignore`
- [ ] Миграции применены на продакшн БД
- [ ] Railway health check проходит (`/health` эндпоинт)
