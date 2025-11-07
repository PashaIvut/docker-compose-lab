# ЛР: Redis + Rocket Counter. От `docker run` к `docker compose` и масштабированию

## Выполнение лабораторной работы

### Часть 1. Запуск вручную (контейнеры `docker run`)

#### Шаг 1: Создание Redis

```bash
docker run -d --name redis redis
```
<img width="969" height="311" alt="image" src="https://github.com/user-attachments/assets/011f9861-01df-4333-a4a4-4a8906d70c7c" />

**Проверка:**
```bash
docker ps
```
<img width="1195" height="88" alt="image" src="https://github.com/user-attachments/assets/37feebe7-adff-46c4-a6f0-33e05c30d54e" />


#### Шаг 2: Создание приложения rocketcounter

```bash
docker run -d --name rocketcounter --link redis:redis -p 8085:5000 rotorocloud/rocket-counter
```
<img width="1449" height="382" alt="image" src="https://github.com/user-attachments/assets/12c41af5-3516-49ed-9ac7-a1d9ac33870d" />

**Проверка:**
```bash
docker ps
curl -s localhost:8085 | head
```
<img width="1704" height="114" alt="image" src="https://github.com/user-attachments/assets/1204ffaa-b1da-495e-80e3-5625e5b3b8bf" />
<img width="1377" height="677" alt="image" src="https://github.com/user-attachments/assets/88d78689-201a-4611-a6f4-4ea5f54f6e94" />

Откроем в браузере `http://localhost:8085` и нажмем **GET A ROCKET** несколько раз, чтобы убедиться, что счетчик работает.
<img width="991" height="647" alt="image" src="https://github.com/user-attachments/assets/34422e50-438f-4716-bc37-2539c35e9cf4" />
<img width="1163" height="613" alt="image" src="https://github.com/user-attachments/assets/02b44f42-e66c-4fc0-aaff-1cde1cd0eea1" />
Число ракет растет!


#### Шаг 3: Удаление контейнеров

```bash
docker stop redis rocketcounter
docker rm redis rocketcounter
```
<img width="906" height="161" alt="image" src="https://github.com/user-attachments/assets/57f7ff59-b44d-4865-9dc7-07cd22eb3f90" />
<img width="878" height="103" alt="image" src="https://github.com/user-attachments/assets/d3ea771a-7d1f-4286-a6f2-51600bb9d38e" />


### Часть 2. Описание стека в `docker-compose.yml`
Файл docker-compose.yml создан.
#### Запуск стека

```bash
docker compose up -d
```
<img width="1884" height="179" alt="image" src="https://github.com/user-attachments/assets/df3f7a7f-948c-40e0-b74d-6113207efdf2" />

**Проверка:**
```bash
docker compose ps
curl -s localhost:8085 | head
```
<img width="1883" height="172" alt="image" src="https://github.com/user-attachments/assets/155e04e7-5ed9-46fc-b9d3-87ca35b41cb9" />
<img width="1378" height="681" alt="image" src="https://github.com/user-attachments/assets/7a1f94f7-6335-4234-a7e2-404169ae91e6" />


### Часть 3. Масштабирование приложения

#### Шаг 1: Изменение публикации портов на динамическую

Изменим в `docker-compose.yml` секцию `ports` для сервиса `rocketcounter`:

```yaml
ports:
  - "0:5000"   
```

#### Шаг 2: Масштабирование

```bash
docker compose up -d --scale rocketcounter=2
docker compose ps
```
<img width="1885" height="175" alt="image" src="https://github.com/user-attachments/assets/94bfd130-d729-4c6c-af58-5bcb67c76087" />

**Проверка:** В выводе `docker compose ps` у обоих экземпляров `rocketcounter` разные хост-порты.
<img width="1896" height="226" alt="image" src="https://github.com/user-attachments/assets/4319be12-d5c3-4d94-9a5b-8c3756b2fce6" />

Откроем оба URL в браузере и убедимся, что счётчик общий. 
Не сообразил, как доказать, что счетчик общий, но процесс проверки такой:
1. Нажимаем "GET A ROCKET!" на первом экземпляре несколько раз — счетчик увеличивается.
2. Обновим вторую вкладку — счетчик будет таким же. И если снова нажать "GET A ROCKET!", то счетчик уже увеличится от числа, которое было на счетчике в первом экземпляре.
3. Аналогично нажмем "GET A ROCKET!" на втором экземпляре — счетчик увеличится.
4. Обновим первую вкладку — счетчик будет таким же.

---

### Часть 4. Полное уничтожение стека

```bash
docker compose down -v
```
<img width="1897" height="205" alt="image" src="https://github.com/user-attachments/assets/efd48ea7-ee65-4ef7-8b95-ecd548e62309" />

**Проверка:**
```bash
docker ps -a | grep -E 'rocketcounter|redis'
docker network ls | grep rocketcounter
```
Для проверки пришлось немного изменить команды, тк в Windows PowerShell нет grep.
Фильтр на "redis" показывает один контейнер, но это не тот, который мы создавали в этой лабораторной. Этот нужен мне для других целей)
Уничтожение прошло успешно!
<img width="1580" height="222" alt="image" src="https://github.com/user-attachments/assets/bf1391eb-fa53-46f8-b866-87da0a6dbdeb" />






