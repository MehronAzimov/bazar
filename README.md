# Базар-навигатор

Приложение для поиска магазинов и товаров на базаре: схема рядов, маршрут, чат с продавцом.

Документы: `docs/TZ.md`, `docs/PZ.md`, `docs/PLAN.md`. Правила работы — `CLAUDE.md`.

## Запуск

1. Скопировать `.env.example` в `.env` и при необходимости поменять значения (например, `DB_PORT`, если 5433 тоже занят).
2. Запустить зависимости:
   ```
   docker compose up -d postgres
   ```
   > Известная проблема: образ `minio/minio` сейчас не скачивается анонимно ни с Docker Hub, ни с quay.io/ghcr.io («pull access denied»). Сервис `minio` оставлен в `docker-compose.yml` по ТЗ; разберёмся с ним в шаге 1.4 (модуль media), пока он не нужен.
3. Запустить бэкенд:
   ```
   cd backend && ./gradlew bootRun
   ```
4. Проверить:
   - здоровье приложения — http://localhost:8080/actuator/health
   - Swagger — http://localhost:8080/swagger-ui.html

## Тесты

```
cd backend && ./gradlew test
```

Интеграционные тесты поднимают PostgreSQL через Testcontainers — нужен запущенный Docker.

> На macOS с Docker Desktop (текущая настройка) Testcontainers иногда не находит правильный docker-сокет и падает с `Could not find a valid Docker environment` или `client version ... too old`. Если это случилось, запусти тесты так:
> ```
> DOCKER_HOST="unix://$HOME/Library/Containers/com.docker.docker/Data/docker.raw.sock" \
> TESTCONTAINERS_RYUK_DISABLED=true \
> ./gradlew test
> ```
> `TESTCONTAINERS_RYUK_DISABLED=true` отключает автоочистку контейнеров (Ryuk не может примонтировать нестандартный путь к сокету) — после тестов проверяй `docker ps` и убирай зависшие контейнеры вручную при необходимости.
