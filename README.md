# Lombard Automation — Anonymized Case Study

Full automation of a 3-branch pawnshop network. Built as a freelance project for a client in Poland.

**Author:** [Artem Kholomyanskiy](https://github.com/Kholomyanskiy) — EVAI Consulting  
**Stack:** Python · FastAPI · PostgreSQL · Telegram Bot · Supabase · Railway

---

## What was automated

| Process | Before | After |
|---------|--------|-------|
| Item intake | Paper forms | Telegram bot — staff enters item data, photos attached |
| Valuation | Manual lookup | Structured workflow with category templates |
| Client notifications | Phone calls | Automated Telegram messages at each status change |
| Branch reporting | Manual Excel | Auto-generated daily/weekly reports per branch |
| Manager dashboard | None | Web UI with real-time status across 3 branches |

---

## Architecture

```
Telegram Bot (staff-facing)
    ↓
FastAPI Backend
    ↓
PostgreSQL (Supabase)
    ↓
Web UI (manager dashboard)
```

- **Telegram bot** — field staff interface for intake, status updates, photo uploads
- **FastAPI** — REST API, business logic, webhook handler
- **PostgreSQL** — items, clients, transactions, branches, staff
- **Supabase Storage** — item photos
- **Railway** — deployment (auto-deploy from GitHub)

---

## Stack details

- Python 3.12, FastAPI, Alembic (migrations)
- python-telegram-bot 21.x
- PostgreSQL 16 via Supabase
- Docker + docker-compose for local dev
- Railway for production

---

## Running locally

```bash
cp .env.example .env
# Fill in .env with your values

docker-compose up db -d
uvicorn app.main:app --reload
```

See `DEPLOY.md` for full deployment guide (Supabase + Railway).

---

## Files in this repo

```
docker-compose.yml    Local dev setup (PostgreSQL + FastAPI)
.env.example          Required environment variables
DEPLOY.md             Production deployment guide
```

Full ANSS specification: [anss-standard/examples/lombard-pro.md](https://github.com/Kholomyanskiy/anss-standard/blob/master/examples/lombard-pro.md)

---

## Results

- Staff onboarding: 1 day (Telegram bot, no training needed)
- Item intake time: −70%
- Missed client notifications: eliminated
- Manager visibility: real-time across all branches

---

## Similar projects

Interested in automating your business? → [AI Automation Decision System](https://aimethodology.gumroad.com/)
