<h2>Задание 1: Запуск контейнера с использованием docker run</h2>
Цель этого задания - запустить контейнер с использованием команды docker
run и ознакомиться с ее основными параметрами.

Шаги:
<ul>
<li>Найдите на Docker Hub образ, который вы хотите запустить на вашей
машине.</li>
<li>Используя команду docker run, запустите контейнер на основе этого
образа. Добавьте флаги, чтобы установить имя контейнера,
перенаправить порты, установить переменные окружения и т.д.
Используйте команду docker ps, чтобы убедиться, что контейнер
запущен.</li>
<li>Остановите контейнер, используя команду docker stop, и удалите его,
используя команду docker rm.</li>
</ul>

Устанавлию необходимые пакеты:

    sudo apt update
    sudo apt install curl software-properties-common ca-certificates apt-transport-https -y
Добавляю официальный репозиторий с помощью gpg-ключа

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
    https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

Устанавливаю docker:

    sudo apt update && sudo apt install docker-ce docker-ce-cli containerd.io -y

Добавляю пользователя в группу docker:

    sudo usermod -aG docker ivan
    newgrp docker
Проверяю версию:

    docker -v
    Docker version 28.5.0, build 887030f

Ищу официальный образ nginx с Docker Hub:

    docker search nginx --filter "is-official=true"
***
    NAME      DESCRIPTION                STARS     OFFICIAL
    nginx     Official build of Nginx.   21008     [OK]
Запускаю контейнер:

    docker run -d -it --name=my_nginx -p 8001:80 \
    -v /var/www/lesson_15_hw:/var/www/lesson_15_hw \
    -e SERVER_NAME=tms.by nginx:latest
***
    docker ps
***
    CONTAINER ID   IMAGE          COMMAND                  CREATED              STATUS              PORTS                                     NAMES
    b260749eff88   nginx:latest   "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:8001->80/tcp, [::]:8001->80/tcp   my_nginx
Останавливаю и удаляю контейнер:

    docker stop b260749eff88 
    docker rm b260749eff88 

<h2>Задание 2: Оптимизация использования дискового пространства</h2>
Цель этого задания - ознакомиться с командами docker inspect, docker images
и docker system prune, и использовать их для оптимизации использования
дискового пространства на вашем хосте.

Шаги:
<ul>
<li>Используя команду docker images, выведите список всех образов,
установленных на вашем хосте.</li>
<li>Используя команду docker inspect, выведите информацию о размере
каждого образа и его слоях. Найдите образы, которые занимают много
места на диске, и определите, какие слои образа занимают больше
всего места.</li>
<li>Если вы обнаружили образы, которые больше не нужны, удалите их,
используя команду docker rmi.</li>
<li>Используйте команду docker system prune для удаления ненужных
образов, контейнеров, томов и сетей, которые больше не используются
вашими приложениями.</li>
<li>После выполнения этих действий проверьте использование дискового
пространства на вашем хосте и убедитесь, что вы освободили
достаточно места.</li>
</ul>

