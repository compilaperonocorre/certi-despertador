# 🛎️ certi-despertador
> El certificado que te avisa antes de que te des cuenta. Monitoreo centralizado, correlación de cadenas de confianza y alertas proactivas. Nunca más un `CERT_EXPIRED` en producción.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Docker](https://img.shields.io/badge/Docker-ready-blue)]()
[![FastAPI](https://img.shields.io/badge/FastAPI-0.110+-green)]()
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16+-blue)]()

## 📦 ¿Qué es?
`certi-despertador` es una aplicación web para **inventario, monitoreo y alertas de certificados X.509 públicos**. Extrae metadatos, reconstruye cadenas de confianza, organiza por proyecto/CA, genera reportes técnicos para reemisión y notifica por email/webhook antes de que venza. **Nunca almacena claves privadas.**

## 🚀 Arquitectura
```
┌─────────────────┐      ┌──────────────────┐      ┌─────────────────┐
│   Frontend UI   │─────▶│   Backend API    │─────▶│   PostgreSQL    │
│ (React/Next.js) │      │ (FastAPI + Uvicorn)│      │   (Relacional)  │
└─────────────────┘      └────────┬─────────┘      └─────────────────┘
                                │
                         ┌──────▼──────┐
                         │ Task Queue  │────▶ Email/Slack/Webhook
                         │ (Celery + Redis)│
                         └─────────────┘
```

## 📋 Funcionalidades Clave
- 🔍 Upload `.pem`/`.p7b`/`.zip` → parsing automático (solo certificados públicos)
- 🔗 Correlación automática de `Root → Intermedia → Leaf` (cadena de confianza)
- 📅 Alertas multitemporal (`30/15/7/3/1` días) vía email, Slack o webhook
- 📊 Reportes técnicos para reemisión (`SAN`, key size, `EKU`, algoritmos, `issuer`)
- 🏷️ Proyectos, tags, comentarios, estado del ciclo de vida
- 🛡️ Zero private key policy, RBAC, audit log, cifrado en tránsito/reposo

## 🐳 Instalación Rápida (Docker)
```bash
# 1. Clona y configura
git clone https://github.com/compilaperonocorre/certi-despertador.git
cd certi-despertador
cp .env.example .env
docker compose up -d --build
```
🌐 Frontend: `http://localhost:3000`  
📡 API: `http://localhost:8000/docs`  

## 🔧 Alias CLI (Sugerencia)
```bash
alias cdi="docker compose exec web /app/cli/certi-despertador"
# Ejemplo: cdi alert --days 7 --format json
```


## 📄 Licencia
MIT
```

---
## 🐳 `docker-compose.yml`
```yaml
version: '3.9'
services:
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: certi_db
      POSTGRES_USER: ${DB_USER:-certi}
      POSTGRES_PASSWORD: ${DB_PASS:-secret}
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U $$POSTGRES_USER"]
      interval: 5s
      timeout: 3s
      retries: 5

  redis:
    image: redis:7-alpine
    command: redis-server --requirepass ${REDIS_PASS:-celery_secret}
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 3s
      retries: 5

  api:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      DATABASE_URL: postgresql://${DB_USER:-certi}:${DB_PASS:-secret}@db:5432/certi_db
      REDIS_URL: redis://:${REDIS_PASS:-celery_secret}@redis:6379/0
      SECRET_KEY: ${SECRET_KEY:-dev_secret_change_me}
      SMTP_HOST: ${SMTP_HOST:-smtp.gmail.com}
      SMTP_PORT: ${SMTP_PORT:-587}
      SMTP_USER: ${SMTP_USER:-}
      SMTP_PASS: ${SMTP_PASS:-}
    depends_on:
      db: { condition: service_healthy }
      redis: { condition: service_healthy }
    volumes:
      - ./backend:/app
    command: uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload

  worker:
    build: ./backend
    environment:
      REDIS_URL: redis://:${REDIS_PASS:-celery_secret}@redis:6379/0
      DATABASE_URL: postgresql://${DB_USER:-certi}:${DB_PASS:-secret}@db:5432/certi_db
    depends_on:
      redis: { condition: service_healthy }
    command: celery -A app.celery_app worker --loglevel=info -Q alerts,parsing

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    environment:
      NEXT_PUBLIC_API_URL: http://api:8000/api/v1
    depends_on:
      - api

volumes:
  pgdata:
```

---
## 🔑 `.env.example`
```env
DB_USER=certi
DB_PASS=certi_secure_pass_2024
REDIS_PASS=celery_secure_pass_2024
SECRET_KEY=your_super_secret_key_for_jwt
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASS=your_app_password
ALERT_THRESHOLD_DAYS=30,15,7,3,1
DEFAULT_TIMEZONE=UTC
```

---
## ✅ Siguientes pasos listos para ejecutar
```bash
mkdir -p certi-despertador/{backend,frontend,docs,scripts}
cd certi-despertador
git init
# Copia README.md, docker-compose.yml, .env.example
git add .
git commit -m "🛎️ init: prvi commit de certi-despertador"
```

