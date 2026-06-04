# Проектная работа дисциплины «Docker-контейнеризация и хранение данных»

## Контейнеризация фронтенда и бэкенда

TODO: в README.md есть команды запуска

Запускаем руками, без `docker compose`:

Пересобираем образы с нуля в директориях с Dockerfile:

```
docker build --no-cache -t backend .
docker build --no-cache -t frontend .
```

Создаем сеть для связи между контейнерами и запускаем оба контейнера:

```
docker network create docker-network
docker run -d --name backend --network docker-network -p 8081:8081 backend
docker run -d --name frontend --network docker-network -p 80:80 frontend
```

В браузере:
```
http://localhost/momo-store/catalog
```

## Оптимизация размера образов 

TODO: использованы multi-stage сборки и лёгкие образы для обоих компонентов, README.md содержит обоснование и размеры итоговых образов

##  Конфигурируемость контейнеров

TODO: контейнеры гибко настраиваются через переменные окружения и build-аргументы, конфигурация чётко описана