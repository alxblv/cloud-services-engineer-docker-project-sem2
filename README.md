# Проектная работа дисциплины «Docker-контейнеризация и хранение данных»

## Контейнеризация фронтенда и бэкенда

TODO: в README.md есть команды запуска

Запускаем руками, без `docker compose`:

Пересобираем образы с нуля в директориях с Dockerfile:

```
docker build --no-cache -t backend ./backend/
docker build --no-cache -t frontend ./frontend/
```

Создаем сеть для связи между контейнерами и запускаем оба контейнера:

```
docker network create docker-network
docker run -d --name backend --network docker-network -p 8081:8081 backend
docker run -d --name frontend --network docker-network -p 80:80 frontend
```

В браузере:
```
http://localhost/momo-store/
```

## Оптимизация размера образов 

TODO: использованы multi-stage сборки и лёгкие образы для обоих компонентов, README.md содержит обоснование и размеры итоговых образов

Обоснование: два образа - для сборки и для фактического запуска приложения, с первого на второй копируется только результат сборки (`curl` для healthcheck-ов на второй образ устанавливается с `--no-cache` флагом - индекс `apk` не сохраняется на диск)

```
➜  cloud-services-engineer-docker-project-sem2 git:(main) docker image ls | egrep "frontend|backend|IMAGE"
WARNING: This output is designed for human readability. For machine-readable output, please use --format.
IMAGE                                         ID             DISK USAGE   CONTENT SIZE   EXTRA
backend:latest                                90bb4bf27bb7       70.2MB         23.1MB   U    
frontend:latest                               e5198c708e64       97.6MB         27.1MB   U 
```

##  Конфигурируемость контейнеров

TODO: контейнеры гибко настраиваются через переменные окружения и build-аргументы, конфигурация чётко описана