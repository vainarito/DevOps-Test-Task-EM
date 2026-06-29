# EM test task – Dockerized Web Application

Простой веб-сервис, состоящий из двух контейнеров:

- **Backend** – Python HTTP-сервер (встроенный `http.server`), возвращающий строку `"Hello from Effective Mobile!"`.
- **Nginx** – обратный прокси, принимающий запросы извне и перенаправляющий их в бэкенд.

---

## Структура проекта

```text
.
├── backend/
│   ├── Dockerfile        # Сборка Python-образа с app.py
│   └── app.py            # Минимальный HTTP-сервер
├── nginx/
│   └── nginx.conf        # Полная конфигурация Nginx (проксирование + заголовки)
├── docker-compose.yml    # Описание двух сервисов и общей сети
└── README.md
````


##  Запуск

Запустить приложение:

```bash
docker-compose up --build -d
```

После успешного запуска веб-сервис будет доступен по адресу:

```text
http://localhost
```

Остановить приложение:

```bash
docker-compose down
```

---

## Проверка работоспособности

Открыть браузер или выполнить:

```bash
curl http://localhost
```

Ожидаемый ответ:

```text
Hello from Effective Mobile!
```

---

##  Конфигурация

### Backend

* Собирается из `./backend/Dockerfile`
* Использует образ `python:3.14-alpine` (минимальный размер)
* Слушает порт `8080` внутри контейнера
* Запускается от непривилегированного пользователя `appuser`
* Порт `8080` не проброшен на хост - бэкенд доступен только внутри сети Docker

---

### Nginx

* Использует образ `nginx:alpine`
* Проксирует все запросы с `/` на:

```text
http://backend:8080
```

по имени сервиса внутри Docker-сети.

Передаёт следующие заголовки:

* `Host`
* `X-Real-IP`
* `X-Forwarded-For`
* `X-Forwarded-Proto`

Полная конфигурация задаётся через монтирование файла:

```text
./nginx/nginx.conf
```

в:

```text
/etc/nginx/nginx.conf
```

в режиме `read-only`.

Единственная точка входа - порт `80` на хосте.

---

## Сеть

Создана пользовательская Docker-сеть:

```text
app-network
```

с драйвером:

```text
bridge
```

Контейнеры взаимодействуют по именам сервисов:

* `backend` - IP бэкенда
* `nginx` - IP прокси

---



