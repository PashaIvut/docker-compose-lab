# ЛР: Redis + Rocket Counter. От `docker run` к `docker compose` и масштабированию

## Выполнение лабораторной работы

### Часть 1. Запуск вручную (контейнеры `docker run`)

#### Шаг 1: Создание Redis

```bash
docker run -d --name redis redis
```

**Проверка:**
```bash
docker ps
```

#### Шаг 2: Создание приложения rocketcounter

```bash
docker run -d --name rocketcounter --link redis:redis -p 8085:5000 rotorocloud/rocket-counter
```

**Проверка:**
```bash
docker ps
curl -s localhost:8085 | head
```

Откроем в браузере `http://localhost:8085` и нажмем **GET A ROCKET** несколько раз, чтобы убедиться, что счетчик работает.

#### Шаг 3: Удаление контейнеров

```bash
docker stop redis rocketcounter
docker rm redis rocketcounter
```

---

### Часть 2. Описание стека в `docker-compose.yml`

#### Запуск стека

```bash
docker compose up -d
```

**Проверка:**
```bash
docker compose ps
curl -s localhost:8085 | head
```

---

### Часть 3. Масштабирование приложения

#### Шаг 1: Изменение публикации портов на динамическую

Изменим в `docker-compose.yml` секцию `ports` для сервиса `rocketcounter`:

```yaml
ports:
  - "0:5000"     # хост выберет свободный порт автоматически
```

#### Шаг 2: Масштабирование

```bash
docker compose up -d --scale rocketcounter=2
docker compose ps
```

**Проверка:** В выводе `docker compose ps` у обоих экземпляров `rocketcounter` будут разные хост-порты.

Откроем оба URL в браузере и убедимся, что счётчик общий (хранится в одном Redis).

---

### Часть 4. Полное уничтожение стека

```bash
docker compose down -v
```

**Проверка:**
```bash
docker ps -a | grep -E 'rocketcounter|redis'
docker network ls | grep rocketcounter
```





