# Edumate – Development Guide

## 🧭 Overview

Edumate consists of multiple services:

- **Frontend** (Node.js)
- **Backend** (Spring Boot)
- **NLP Service** (Python / FastAPI)
- **Database** (PostgreSQL via Docker)

Each service can be developed independently, but the recommended way to run the full system locally is via Docker Compose.

---

## 📦 Repository Structure

### Option A (recommended for dev setup)

All repositories are separate:

```
edumate-frontend
edumate-backend
edumate-nlp-service
edumate-dev (infrastructure)
```

`edumate-dev` contains shared Docker configuration.

---

## 🚀 Running the whole system (recommended)

### 1. Clone all repositories in one directory

```bash
git clone https://github.com/edumate-app/edumate-backend
git clone https://github.com/edumate-app/edumate-frontend
git clone https://github.com/edumate-app/edumate-nlp-service
git clone https://github.com/edumate-app/edumate-dev
```

---

### 2. Start infrastructure

Go to `edumate-dev`:

```bash
cd edumate-dev
docker compose up --build
```

This will start:

- PostgreSQL (`localhost:5432`)
- Backend (Spring Boot) (`localhost:8080`)
- NLP Service (FastAPI) (`localhost:8000`)

---

## 🧠 NLP Service (Python)

### Run standalone (development mode)

```bash
cd edumate-nlp-service
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate

pip install -r requirements.txt

uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

---

### Docker run

```bash
docker build -t edumate-nlp .
docker run -p 8000:8000 edumate-nlp
```

---

## 🖥 Backend (Spring Boot)

### Run locally

```bash
cd edumate-backend
./mvnw spring-boot:run
```

Backend runs on:

```
http://localhost:8080
```

### Environment variables

```env
NLP_URL=http://localhost:8000
DB_URL=jdbc:postgresql://localhost:5432/edumate
```

---

## 🌐 Frontend (Node.js)

### Run locally

```bash
cd edumate-frontend
npm install
npm run dev
```

Frontend runs on:

```
http://localhost:3000
```

---

## 🐳 Docker Compose (full stack)

From `edumate-dev`:

```yaml
services:
  postgres:
    image: postgres:16
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: edumate
    ports:
      - "5432:5432"

  backend:
    build: ../edumate-backend
    ports:
      - "8080:8080"
    depends_on:
      - postgres
      - nlp-service

  nlp-service:
    build: ../edumate-nlp-service
    ports:
      - "8000:8000"

  frontend:
    build: ../edumate-frontend
    ports:
      - "3000:3000"
```

---

## 🔗 Service Communication

- Frontend → Backend: `http://localhost:8080`
- Backend → NLP: `http://nlp-service:8000` (Docker)
- Backend → NLP (local dev): `http://localhost:8000`

---

## 🧪 Useful commands

### Rebuild everything

```bash
docker compose up --build
```

### Stop all services

```bash
docker compose down
```

### Reset database

```bash
docker volume rm edumate_postgres_data
```

---

## 🧩 Notes

- Services communicate via Docker network when running in Compose
- Each service can still be run independently
- NLP service should stay stateless
- Backend is the main orchestrator of business logic

---
