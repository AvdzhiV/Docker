# Simple Docker

Введение в докер. Разработка простого докер-образа для собственного сервера.

## Contents

1. [Chapter I](#chapter-i) \
    1.1. [Готовый докер](#part-1-готовый-докер) \
    1.2. [Операции с контейнером](#part-2-операции-с-контейнером) \
    1.3. [Мини веб-сервер](#part-3-мини-веб-сервер) \
    1.4. [Свой докер](#part-4-свой-докер) \
    1.5. [Dockle](#part-5-dockle) \
    1.6. [Базовый Docker Compose](#part-6-базовый-docker-compose)

## Chapter I

## Part 1. Готовый докер

- Выкачиваем образ nginx командой `docker pull nginx` \
![1.1](img/part1-pull.png)

- Проверка наличия образа nginx командой `docker images`
- Запуск образа командой `docker run -d [image_id|repository]`
- Проверка запуска командой `docker ps` \
![1.2](img/part1-images-run-ps.png)

- Просмотр информации о контейнере командой `docker inspect [container_id|container_name]` \
![1.3](img/part1-inspect.png)
![1.4](img/part1-inspect1.png)

> размер контейнера - 186722098,\
запампленные порты - 80,\
ip контейнера - 172.17.0.1.

- Остановка докер образа командой `docker stop [container_id|container_name]`
- Проверка наличия остановленного образа командой `docker ps` \
![1.5](img/part1-stop.png)

- Запуск образа с портами 80 и 443 в контейнере, замапленных на такие же порты на локальной машине, \
![1.6](img/part1-ports.png)

- Проверка через браузер по адресу `localhost:80`. Доступна стартовая страница **nginx** \
![1.7](img/part1-localhost.png)

- Перезапуск образа командой `docker restart gneg2` \
![1.8](img/part1-restart.png)

## Part 2. Операции с контейнером

- Используем контейнер из прошлого раздела и смотрим его конфигурационный файл через команду `sudo docker exec gneg2 cat /etc/nginx/nginx.conf` \
![2.1](img/part2-conf.png)

- Копируем конфигурационный файл из докера через команду `sudo docker cp gneg2:/etc/nginx/nginx.conf ./nginx.conf` \
![2.2](img/part2-copy.png)

- Настраиваем в нем по пути ***/status*** отдачу страницы статуса сервера **nginx**.
![2.3](img/part2-status.png)

- Копируем его обратно в докер через команду `sudo docker cp ./nginx.conf gneg2:/etc/nginx/nginx.conf`
- Перезапускаем **nginx** внутри докер-образа через команду `sudo docker exec` \
![2.4](img/part2-push.png)

- Проверяем адрес `localhost:80/status` \
![2.5](img/part2-lh-status.png)

- Экспортируем контейнер в файл *container.tar* через команду `docker export -o container gneg2`\
![2.6](img/part2-export.png)

- Останавливаем контейнер через команду `docker stop gneg2`
- Удаляем образ через `docker rmi -f nginx`, не удаляя перед этим контейнер
- Удаляем остановленный контейнер через команду `docker rm gneg2` \
![2.7](img/part2-delete.png)

- Импортируем контейнер обратно через команду `docker import -с 'CMD ["nginx", "-g", "daemon off;"]' container.tar gneg_legacy`
- Запускаем импортированный контейнер через команду `docker run --name gneg3 -d -p 80:80 -p 443:443 gneg_legacy` \
![2.8](img/part2-import.png)

- Проверяем, что по адресу `localhost:80/status` отдается страничка со статусом сервера **nginx**
![2.9](img/part2-check-status.png)

## Part 3. Мини веб-сервер

- Создаем контейнер для нашего мини сервера и заходим в него \
![3.1](img/part3-create-server.png)

- Устанавливаем все необходиемое в контейнер \
![3.2](img/part3-server-stuffing.png)

- Настраиваем конфигурационный файл `nginx.conf` \
![3.3](img/part3-nginx-conf.png)

- Создаем файл main.c после чего пишем код и компилируем его \
![3.4](img/part3-main-c.png)

- Компилируем main.c командой `gcc main -lfcgi -o hello` \
![3.5](img/part3-build-main.png)

- Проверяем работоу нашего мини-сервера в браузере \
![3.6](img/part3-browser.png)

## Part 4. Свой докер

- Создаем Dockerfile для нашего образа \
![4.1](img/part4-create-dockerfile.png)

- Создаем скрипт для нашего образа который используется для настройки среды контейнера \
![4.2](img/part4-create-script.png)

- Собираем образ командой `docker build -t mini_server:latest .` \
![4.3](img/part4-build-docker.png)

- Проверяем наличие образа командой `docker images` \
![4.4](img/part4-check-image.png)

- Запускаем образ командой `docker run -d -p 80:81 -v /home/maximisc/nginx/nginx.conf:/etc/nginx/nginx.conf -d --name serv mini_server:latest` \
![4.5](img/part4-docker-run.png)

- Проверяем работу нашего мини-сервера в браузере \
![4.6](img/part4-check-browser.png)

- Дописываем проксирование странички `/status` в конфигурационный файл `./nginx/nginx.conf` \
![4.7](img/part4-add-status-page.png)

- Перезапускаем контейнер командой `docker restart serv` \
![4.8](img/part4-restart-container.png)

- Проверяем работу страницы ***localhost/status*** в браузере \
![4.9](img/part4-check-status.png)

## Part 5. Dockle

- Устанавливаем Dockle \
![5.1](img/part5-install-dockle.png)

- Сканируем образ командой `dockle mini_server` \
![5.2](img/part5-dockle.png)

- Дописываем Dockerfile для исправления ошибок \
![5.3](img/part5-dockerfile.png)

## Part 6. Базовый Docker Compose

- Изменяем файл `scrpt.sh` \
![6.1](img/part6-scrpt.png)

- Изменяем конфигурационный файл `nginx.conf` \
![6.2](img/part6-nginx-conf.png)

- Убираем все лишнее в `Dockerfile` \
![6.3](img/part6-dockerfile.png)

- Собираем образ командой `sudo docker-compose build` \
![6.4](img/part6-compose-build.png)

- Запускаем образ командой `sudo docker-compose up` \
![6.5](img/part6-compose-up.png)

- Проверяем в браузере \
![6.5](img/part6-check-browser-1.png)
![6.6](img/part6-check-browser-2.png)
