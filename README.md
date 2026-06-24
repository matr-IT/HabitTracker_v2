# Habit Tracker v2

Веб-приложение для отслеживания и управления привычками. Построено на Django REST Framework с использованием асинхронных задач через Celery.

## 📋 Требования

- **Python**: 3.13+
- **Docker**: 27.0+
- **Docker Compose**: 2.0+
- **Poetry** (опционально, для локальной разработки)

## 🗂️ Структура проекта

```
HabitTracker_v2/
├── config/                    # Конфигурация Django проекта
│   ├── settings.py           # Основные настройки
│   ├── urls.py               # Маршруты приложения
│   ├── asgi.py               # ASGI конфигурация
│   ├── celery.py             # Конфигурация Celery
│   └── wsgi.py               # WSGI конфигурация
├── habit_tracker/            # Основное приложение
│   ├── models.py             # Модели данных
│   ├── views.py              # API представления
│   ├── serializers.py        # Сериализаторы DRF
│   ├── pagination.py         # Пагинация
│   ├── tasks.py              # Celery задачи
│   └── tests.py              # Тесты
├── users/                    # Приложение для управления пользователями
│   ├── models.py             # Модель User
│   ├── views.py              # API представления
│   └── serializers.py        # Сериализаторы
├── docker-compose.yml        # Конфигурация Docker Services
├── Dockerfile                # Docker образ для приложения
├── requirements.txt          # Python зависимости
├── pyproject.toml            # Poetry зависимости
├── manage.py                 # Django управления скрипт
└── send_bot.py              # Bot отправки уведомлений
```

## 🚀 Быстрый старт (Docker)

### 1. Клонируйте репозиторий
```bash
git clone <repository-url>
cd HabitTracker_v2
```

### 2. Создайте файл `.env`
```bash
cp .env.example .env
```

Или вручную создайте `.env` файл с необходимыми переменными окружения:

```env
# PostgreSQL
POSTGRES_NAME=habittracker_db
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your_secure_password
POSTGRES_HOST=db
POSTGRES_PORT=5432

# Redis
REDIS_HOST=redis
REDIS_PORT=6379

# Django
DEBUG=False
SECRET_KEY=your-secret-key-here
ALLOWED_HOSTS=localhost,127.0.0.1

# JWT
JWT_SECRET_KEY=your-jwt-secret-key
JWT_ALGORITHM=HS256
JWT_ACCESS_TOKEN_LIFETIME=3600
JWT_REFRESH_TOKEN_LIFETIME=86400
```

### 3. Запустите приложение через Docker Compose
```bash
docker-compose up -d
```

Это запустит следующие сервисы:
- **web**: Django приложение (порт 8000)
- **db**: PostgreSQL база данных
- **redis**: Redis кэш и message broker для Celery
- **celery**: Celery worker для фоновых задач
- **celery-beat**: Celery Beat для периодических задач

### 4. Проверьте статус контейнеров
```bash
docker-compose ps
```

Все контейнеры должны быть в статусе **Up**.

### 5. Доступ к приложению
- **API**: http://localhost:8000/api/
- **Swagger документация**: http://localhost:8000/api/swagger/
- **ReDoc файла**: http://localhost:8000/api/redoc/

## 🛠️ Локальная разработка

### 1. Установите зависимости

**Вариант A: Используя Poetry**
```bash
poetry install
poetry shell  # Активируйте виртуальное окружение
```

**Вариант B: Используя pip**
```bash
python -m venv venv
source venv/bin/activate  # На Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### 2. Создайте файл `.env` в корне проекта
```env
DEBUG=True
SECRET_KEY=django-insecure-your-secret-key
ALLOWED_HOSTS=localhost,127.0.0.1

POSTGRES_NAME=habittracker_db
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_HOST=localhost
POSTGRES_PORT=5432

REDIS_HOST=localhost
REDIS_PORT=6379
```

### 3. Запустите PostgreSQL и Redis локально

**Используя Docker (рекомендуется):**
```bash
# Только база данных и Redis
docker-compose up -d db redis
```

**Или установите локально:**
- PostgreSQL: https://www.postgresql.org/download/
- Redis: https://redis.io/download

### 4. Примените миграции
```bash
python manage.py migrate
```

### 5. Создайте суперпользователя
```bash
python manage.py createsuperuser
```

Или используйте встроенную команду:
```bash
python manage.py csu  # Custom create superuser command
```

### 6. Запустите Django сервер разработки
```bash
python manage.py runserver
```

Приложение будет доступно на http://localhost:8000

### 7. (Опционально) Запустите Celery worker
В отдельном терминале:
```bash
celery -A config worker --loglevel=info
```

### 8. (Опционально) Запустите Celery Beat
В отдельном терминале:
```bash
celery -A config beat --loglevel=info
```

## 📦 Основные зависимости

- **Django 6.0.6** - Веб-фреймворк
- **Django REST Framework 3.17.1** - API фреймворк
- **django-redis 7.0.0** - Redis интеграция для кэширования сессий
- **celery 5.6.3** - Асинхронные задачи
- **django-celery-beat 2.9.0** - Периодические задачи
- **psycopg2-binary 2.9.12** - PostgreSQL адаптер
- **redis 8.0.0** - Redis клиент
- **djangorestframework-simplejwt 5.5.1** - JWT аутентификация
- **drf-yasg 1.21.15** - Swagger/OpenAPI документация
- **django-filter 25.2** - Фильтрация в API
- **django-cors-headers 4.9.0** - CORS поддержка
- **coverage 7.14.2** - Покрытие тестами

## 🧪 Тестирование

### Запустите все тесты
```bash
python manage.py test
```

### Запустите тесты с покрытием
```bash
coverage run --source='.' manage.py test
coverage report
coverage html  # Генерирует HTML отчет
```

### Запустите тесты конкретного приложения
```bash
python manage.py test habit_tracker
python manage.py test users
```

## 🗄️ Управление базой данных

### Создание миграций
```bash
python manage.py makemigrations
```

### Применение миграций
```bash
python manage.py migrate
```

### Просмотр статуса миграций
```bash
python manage.py showmigrations
```

### Откат миграций
```bash
python manage.py migrate app_name 0001  # Откат на определенную миграцию
```

## 🔐 Аутентификация

Приложение использует JWT (JSON Web Tokens) для аутентификации.

### Получение токена
```bash
curl -X POST http://localhost:8000/api/token/ \
  -H "Content-Type: application/json" \
  -d '{"username": "your_username", "password": "your_password"}'
```

### Использование токена
```bash
curl -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  http://localhost:8000/api/habits/
```

## 🐛 Решение проблем

### Контейнеры не стартуют
```bash
# Проверьте логи
docker-compose logs -f web

# Пересоберите образы
docker-compose build --no-cache

# Перезапустите все сервисы
docker-compose restart
```

### Ошибки миграции базы данных
```bash
# Избегайте стертых данных, но все же очистите БД
docker-compose down
docker volume rm habittracker_v2_pgdata

# Пересоздайте все
docker-compose up
```

### PostgreSQL недоступна
```bash
# Проверьте, что контейнер db работает
docker-compose logs db

# Убедитесь, что хост "db" резолвится (для Docker)
# Для локальной разработки используйте "localhost"
```

### Redis недоступна для Celery
```bash
# Проверьте Redis контейнер
docker-compose logs redis

# Убедитесь, что REDIS_HOST правильно установлен в .env
```

## 📚 API Документация

После запуска приложения, документация доступна здесь:

- **Swagger UI**: http://localhost:8000/api/swagger/
- **ReDoc**: http://localhost:8000/api/redoc/

## 🔄 Основные API эндпоинты

### Аутентификация
- `POST /api/token/` - Получить JWT токен
- `POST /api/token/refresh/` - Обновить токен
- `POST /api/users/register/` - Регистрация

### Привычки
- `GET /api/habits/` - Список всех привычек текущего пользователя
- `POST /api/habits/` - Создать новую привычку
- `GET /api/habits/{id}/` - Детали привычки
- `PUT /api/habits/{id}/` - Обновить привычку
- `DELETE /api/habits/{id}/` - Удалить привычку

### Пользователи
- `GET /api/users/` - Список пользователей (только администратор)
- `GET /api/users/me/` - Данные текущего пользователя
- `PUT /api/users/me/` - Обновить профиль

## 🚢 Деплой на продакшене

### Используя Docker Compose
1. Установите переменные окружения в `.env.prod`
2. Измените `DEBUG=False` в .env
3. Обновите `ALLOWED_HOSTS`
4. Используйте production веб-сервер (gunicorn):

```bash
docker-compose -f docker-compose.prod.yml up -d
```

### Используя Kubernetes
Добавьте YAML конфигурации для развертывания в Kubernetes.

## 📝 Логирование

Логи приложения:
```bash
# Django логи
docker-compose logs -f web

# Celery логи
docker-compose logs -f celery

# Celery Beat логи
docker-compose logs -f celery-beat

# PostgreSQL логи
docker-compose logs -f db
```

## 🤝 Контрибьютинг

1. Форкните репозиторий
2. Создайте ветку для своей фичи (`git checkout -b feature/amazing-feature`)
3. Коммитьте изменения (`git commit -m 'Add amazing feature'`)
4. Пушьте в ветку (`git push origin feature/amazing-feature`)
5. Откройте Pull Request

## 📄 Лицензия

Этот проект лицензирован под MIT лицензией - см. файл LICENSE для деталей.

## 👤 Автор

- **rybin** - rybin.32@gmail.com

## 📞 Поддержка

Если у вас есть вопросы или проблемы, пожалуйста, откройте Issue на GitHub.

