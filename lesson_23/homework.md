<h2>Задание 1: Запуск экземпляров приложения с подключением томов и
сети</h2>

Цель этого задания - ознакомиться с основами работы с Docker volumes и Docker
network. Также вы научитесь использовать команду docker logs

Шаги:
<ul>
<li>Найдите на Docker Hub образ, который вы хотите запустить на вашей
машине.</li>
<li>Используя команду docker run, запустите контейнер на основе этого
образа. Добавьте флаги, чтобы установить имя контейнера,
перенаправить порты, установить переменные окружения и т.д.
Используйте команду docker ps, чтобы убедиться, что контейнер
запущен.</li>
<li>Создайте Docker volume с помощью команды docker volume create.</li>
<li>Запустите контейнер, используя команду docker run, и подключите
созданный Docker volume к контейнеру.</li>
<li>Используйте команду docker network create для создания новой Docker
network.</li>
</ul>
  
Опционально:
<ul>
<li>Создайте второй контейнер из того же образа, используя команду docker run,
и подключите его к созданной Docker network.</li>
<li>Внутри первого контейнера, используя утилиту nano, создайте текстовый
файл в подключенном Docker volume.</li>
<li>Внутри первого контейнера, используя утилиту logger, запишите несколько
сообщений в созданный текстовый файл.</li>
<li>Используйте команду docker logs для просмотра логов первого контейнера и
убедитесь, что сообщения были успешно записаны в лог-файл.</li>
<li>Используйте команду docker exec для выполнения команды во втором
контейнере и проверьте, что созданный текстовый файл доступен из второго
контейнера.</li>
<li>Используйте команду docker stop для остановки обоих контейнеров.</li>
<li>Используйте команду docker rm для удаления контейнеров и команду docker
rmi для удаления образа.</li>
<li>Используйте команду docker volume rm для удаления Docker volume и
команду docker network rm для удаления Docker network</li>
</ul>

Беру образ nginx и запускаю контейнер:

    docker run -d -it --name my-nginx -p 8001:80 nginx
***
    curl 127.0.0.1:8001
***    
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to nginx!</title>
    <style>
    html { color-scheme: light dark; }
    body { width: 35em; margin: 0 auto;
    font-family: Tahoma, Verdana, Arial, sans-serif; }
    </style>
    </head>
    <body>
    <h1>Welcome to nginx!</h1>
    <p>If you see this page, the nginx web server is successfully installed and
    working. Further configuration is required.</p>
    
    <p>For online documentation and support please refer to
    <a href="http://nginx.org/">nginx.org</a>.<br/>
    Commercial support is available at
    <a href="http://nginx.com/">nginx.com</a>.</p>
    
    <p><em>Thank you for using nginx.</em></p>
    </body>
    </html>
Создаю Docker volume:

    docker volume create my-nginx-data
Подключаю volume (запускаю новый контейнер):

    docker run -d -it --name my-nginx-ws -p 8002:80 -v my-nginx-data:/usr/share/nginx/html nginx
***
    curl 127.0.0.1:8002
***
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to nginx!</title>
    <style>
    html { color-scheme: light dark; }
    body { width: 35em; margin: 0 auto;
    font-family: Tahoma, Verdana, Arial, sans-serif; }
    </style>
    </head>
    <body>
    <h1>Welcome to nginx!</h1>
    <p>If you see this page, the nginx web server is successfully installed and
    working. Further configuration is required.</p>
    
    <p>For online documentation and support please refer to
    <a href="http://nginx.org/">nginx.org</a>.<br/>
    Commercial support is available at
    <a href="http://nginx.com/">nginx.com</a>.</p>
    
    <p><em>Thank you for using nginx.</em></p>
    </body>
    </html>
Копирую index.html из 15 домашки внутрь контейнера:

    docker cp /var/www/lesson_15_hw/. my-nginx-ws:/usr/share/nginx/html/
    curl 127.0.0.1:8002
***
    <!DOCTYPE html>
    <html>
        <head>
    	<meta charset="UTF-8">
            <title>lesson_15_hw-WebServers</title>
        </head>
        <body>
            <h1>WebServers</h1>
            <h2>Ходырев Иван</h2>
            <p>Веб-сервер — сервер, принимающий HTTP-запросы от клиентов, обычно веб-браузеров, и выдающий им HTTP-ответы, как правило, вместе с HTML-страницей, изображением, файлом, медиа-потоком или другими данными.</p>
    	<p>Веб-сервером называют как программное обеспечение, выполняющее функции веб-сервера, так и непосредственно компьютер (см.: Сервер (аппаратное обеспечение)), на котором это программное обеспечение работает.</p>
    	<p>Клиент, которым обычно является веб-браузер, передаёт веб-серверу запросы на получение ресурсов, обозначенных URL-адресами. Ресурсы — это HTML-страницы, изображения, файлы, медиа-потоки или другие данные, которые необходимы клиенту. В ответ веб-сервер передаёт клиенту запрошенные данные. Этот обмен происходит по протоколу HTTP.</p>
        </body>
    </html>
Создаю docker network:
    
    docker network create my-net
    2e9c80abe3fe4aba0b83e3ae9ef5311657fe2bcbe843d3055d98547db260f35c
***
    docker network ls
***
    NETWORK ID     NAME      DRIVER    SCOPE
    e3cd68b1b61f   bridge    bridge    local
    3d12f69c9395   host      host      local
    2e9c80abe3fe   my-net    bridge    local
    8fb9e4a99355   none      null      local
Запускаю контейнер в этой сети:

    docker run -d -it --name my-nginx-ws02 --network my-net -p 8004:80 -v my-nginx-data:/usr/share/nginx/html nginx
Подключаюсь к первому контейнеру, устанавливаю nano, создаю файл, пишу сообщения с помощью logger:

    docker exec -it my-nginx-ws01 bash
    apt update && apt install -y nano
    nano file.txt
В официальном образу nginx нет syslog, logger не работает.
Добавил через echo строки, но они не фиксируются в docker logs:

    echo 'Message 1 from my-nginx-ws01' >> /usr/share/nginx/html/file.txt
    echo 'Message 2 from my-nginx-ws01' >> /usr/share/nginx/html/file.txt
Выхожу:

    exit
Нашел, что эти команды запишут изменения в файл и добавят строки в docker logs:

    docker exec -it my-nginx-ws01 bash -c 'msg="message 3 from my-nginx-ws01"; echo "$msg" | tee -a /usr/share/nginx/html/file.txt > /proc/1/fd/1'
    docker exec -it my-nginx-ws01 bash -c 'msg="message 4 from my-nginx-ws01"; echo "$msg" | tee -a /usr/share/nginx/html/file.txt > /proc/1/fd/1'

Проверяю лог:

    docker logs my-nginx-ws01 | tail -5
***
    2025/10/05 18:06:42 [notice] 1#1: start worker processes
    2025/10/05 18:06:42 [notice] 1#1: start worker process 22
    2025/10/05 18:06:42 [notice] 1#1: start worker process 23
    message 3 from my-nginx-ws01
    message 4 from my-nginx-ws01
Подключаюсь ко второму контейнеру и проверяю файл:

    docker exec -it my-nginx-ws02 bash
    cat /usr/share/nginx/html/file.txt 
***
    this is file for my-nginx-ws01
    Message 1 from my-nginx-ws01
    Message 2 from my-nginx-ws01
    message 3 from my-nginx-ws01
    message 4 from my-nginx-ws01
<img width="636" height="387" alt="image" src="https://github.com/user-attachments/assets/614b2423-7382-4bd3-ad1d-8aedaa45d566" />

Останавливаю контейнеры:

    docker stop my-nginx-ws01
    docker stop my-nginx-ws02
Удаляю контейнеры:

    docker rm my-nginx-ws01
    docker rm my-nginx-ws02
Удаляю образ:

    docker rmi nginx:latest
Удаляю volume:

    docker volume rm my-nginx-data 
