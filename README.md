# Kittygram

![Main Kittygram workflow](https://github.com/smouk5/kittygram_final/actions/workflows/main.yml/badge.svg)

## Описание

Kittygram — социальная сеть для публикации карточек котиков. Зарегистрированные пользователи могут:

- добавлять карточки своих питомцев с именем, годом рождения, цветом и фотографией
- редактировать и удалять свои карточки
- просматривать карточки других пользователей с постраничной навигацией

Неавторизованным пользователям доступен просмотр карточек в режиме чтения.

## Стек технологий

| Слой | Технологии |
|---|---|
| Backend | Python 3.9, Django 3.2, Django REST Framework, Djoser |
| Frontend | React, JavaScript (Node.js 18) |
| База данных | PostgreSQL 13 |
| Инфраструктура | Docker, Docker Compose, Nginx |
| CI/CD | GitHub Actions |
| Тесты | pytest, flake8, Jest |

## Как заполнить .env

Создайте файл `.env` в корне проекта на основе `.env.example`:

```bash
cp .env.example .env
```

Затем откройте `.env` и заполните переменные:

```env
# Django
SECRET_KEY=ваш_секретный_ключ       # см. команду генерации ниже
DEBUG=False                          # True только для локальной разработки
ALLOWED_HOSTS=127.0.0.1,localhost,ваш_домен

# PostgreSQL
POSTGRES_DB=kittygram
POSTGRES_USER=kittygram_user
POSTGRES_PASSWORD=kittygram_password
DB_HOST=db                           # имя сервиса в docker-compose
DB_PORT=5432

# Локальная разработка без PostgreSQL (опционально)
USE_SQLITE=False                     # поставьте True, чтобы использовать SQLite
```

Сгенерировать безопасный `SECRET_KEY`:

```bash
python3 -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

## Развёртывание проекта в контейнерах

**1. Клонировать репозиторий:**

```bash
git clone https://github.com/smouk5/kittygram_final.git
cd kittygram_final
```

**2. Заполнить `.env`** (см. раздел выше).

**3. Собрать и запустить контейнеры:**

```bash
docker compose up -d --build
```

**4. Применить миграции и собрать статику:**

```bash
docker compose exec backend python manage.py migrate
docker compose exec backend python manage.py collectstatic --noinput
docker compose exec backend cp -r /app/collected_static/. /backend_static/static/
```

**5. Создать суперпользователя (опционально):**

```bash
docker compose exec backend python manage.py createsuperuser
```

Проект будет доступен по адресу `http://localhost:8080/`.

## CI/CD

При пуше в **любую ветку** запускаются тесты backend (flake8 + Django tests) и frontend (Jest).

При пуше в **ветку `main`** дополнительно:

1. Собираются и публикуются Docker-образы на Docker Hub
2. Выполняется деплой на сервер по SSH
3. Приходит Telegram-уведомление об успешном деплое

### Необходимые GitHub Secrets

| Секрет | Описание |
|---|---|
| `DOCKER_USERNAME` | Логин на Docker Hub |
| `DOCKER_PASSWORD` | Пароль на Docker Hub |
| `HOST` | IP-адрес сервера |
| `USER` | Имя пользователя на сервере |
| `SSH_KEY` | Приватный SSH-ключ |
| `SSH_PASSPHRASE` | Пассфраза SSH-ключа |
| `TELEGRAM_TO` | ID Telegram-чата для уведомлений |
| `TELEGRAM_TOKEN` | Токен Telegram-бота |

## Автор

**Сергей Сакович**

- GitHub: [smouk5](https://github.com/smouk5)
- Docker Hub: [sergeo8](https://hub.docker.com/u/sergeo8)
