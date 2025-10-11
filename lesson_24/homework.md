<h2>Задание 1: Создание Dockerfile для приложения веб-сервера</h2>
Вам необходимо написать Dockerfile для создания контейнера с приложением
веб-сервера на основе образа Ubuntu 20.04. Приложение должно быть
запущено на порту 8080 и должно отдавать статические файлы из каталога
/app/static.

Шаги, которые необходимо выполнить:
<ol>
<li>Создайте новый файл Dockerfile в пустой директории на вашем
локальном компьютере.</li>
<li>Напишите инструкцию FROM, которая указывает базовый образ
Ubuntu 20.04.</li>
<li>Установите необходимые зависимости с помощью инструкции RUN.</li>
Установите пакеты nginx и curl, а также создайте каталог /app/static.
<li>Скопируйте файл конфигурации nginx из вашего локального каталога
внутрь контейнера с помощью инструкции COPY.</li>
<li>Скопируйте статические файлы из каталога /app/static на вашем
локальном компьютере внутрь контейнера с помощью инструкции COPY.</li>
<li>Используйте инструкцию EXPOSE для открытия порта 8080.</li>
<li>Используйте инструкцию CMD для запуска команды nginx с указанием
пути к файлу конфигурации, который вы скопировали на шаге 4.</li>
<li>Сохраните файл Dockerfile и соберите образ с помощью команды docker
build.</li>
<li>Запустите контейнер из образа с помощью команды docker run и
проверьте, что веб-сервер отдает статические файлы из каталога /app/static на
порту 8080</li>
</ol>

Создаю локальную папку и перехожу в нее:

    mkdir docker
    cd docker/

Создаю Dockerfile:

    vim Dockerfile
***
    FROM ubuntu:20.04
    RUN apt-get -y update && apt-get -y install nginx curl
    RUN rm -f /etc/nginx/sites-enabled/default
    WORKDIR /app/static
    COPY static.conf /etc/nginx/conf.d/default.conf
    COPY index.html .
    EXPOSE 8080/tcp
    CMD ["nginx", "-g", "daemon off;"]

>[!NOTE]
>До RUN rm -f /etc/nginx/sites-enabled/default дошел опытным путем.
>Получал дефолтную страницу, понял, что беда в дефолтном файле.
>Сходил на контейнер, удалил файл, убедился, что получаю свой html

Создаю nginx.conf:

    vim static.conf
***
    server {
      listen 80;
      server_name 0.0.0.0;
      
      root /app/static/;
      index index.html;
    
      location / {
        try_files $uri $uri/ =404;
      }
    }
Копирую html из 15 домашки:

    sudo cp /var/www/lesson_15_hw/index.html ~/docker/index.html

Собираю образ и запускаю контейнер:

    docker build . -t my-static
    docker run -d -it --name my-static -p 8080:80 my-static:latest 
Курлю:

    curl 127.0.0.1:8080
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

<h2>Задание 2 – развертывание приложения с помощью Docker-compose</h2>

Шаги, которые необходимо выполнить:
<ol>
<li>Создайте новый файл docker-compose.yml в пустой директории на
вашем локальном компьютере.</li>
<li>Напишите инструкцию version в версии 3.</li>
<li>Определите сервис для базы данных PostgreSQL. Назовите его "db".
Используйте образ postgres:latest, задайте переменные окружения
POSTGRES_USER, POSTGRES_PASSWORD и POSTGRES_DB для
установки пользовательского имени, пароля и имени базы данных
соответственно.</li>
<li>Определите сервис для веб-сервера на основе образа NGINX. Назовите
его "web". Используйте образ nginx:latest. Определите порт, на котором
должен работать сервер, с помощью инструкции ports. Задайте путь к
файлам конфигурации NGINX внутри контейнера, используя
инструкцию volumes.</li>
<li>Определите ссылку на сервис базы данных в сервисе веб-сервера.
Используйте инструкцию links.</li>
<li>Сохраните файл docker-compose.yml и запустите приложение с
помощью команды docker-compose up.</li>
<li>Проверьте, что приложение работает, перейдя в браузере на
localhost:80</li>
</ol>

создаю директорию под приложение:

    mkdir app24
    cd app24
создаю приложение:

    vim db.py
    vim init_db.sql
    vim app.py
создаю Dockerfile по уроку:

    FROM python:3.12-slim
    RUN apt-get update && apt-get install -y curl iproute2
    WORKDIR /bookstore
    COPY . .
    RUN pip install --no-cache-dir -r requirements.txt
    ENV PYTHONPATH=/bookstore
    ENTRYPOINT ["python", "app.py"]
создаю compose.yml по уроку:

    services:
      bookstore:
        build: .
        restart: always
        environment:
          DATABASE_URL: "postgresql://${DB_USER}:${DB_PASS}@${DB_HOST}:5432/${DB_NAME}"
        env_file: .env
        depends_on:
          db:
            condition: service_healthy
        healthcheck:
          test: ["CMD", "curl", "-f", "http:/localhost:5000/books"]
          interval: 30s
          timeout: 10s
          retries: 3
          start_period: 5s
    
      db:
        image: postgres:17
        restart: always
        volumes:
          - pg_data:/var/lib/postgresql/data
          - ./init_db.sql:/docker-entrypoint-initdb.d/init_db.sql
        environment:
          POSTGRES_DB: ${DB_NAME}
          POSTGRES_USER: ${DB_USER}
          POSTGRES_PASSWORD: ${DB_PASS}
        healthcheck:
          test: ["CMD-SHELL", "sh -c 'pg_isready -U ${DB_USER} -d ${DB_NAME}'"]
          interval: 30s
          timeout: 10s
          retries: 5
    
      web:
        image: nginx:alpine
        restart: always
        ports:
          - 80:80
        volumes:
          - ./bookstore.conf:/etc/nginx/conf.d/default.conf
        depends_on:
          bookstore:
            condition: service_healthy
    
    volumes:
      pg_data:

создаю bookstore.conf для nginx:

    upstream backend {
      server bookstore:5000;
    }
    
    server {
      listen 80;
      server_name _;
    
      location / {
        proxy_pass http://backend;
      }
    }

структура app24:

    .
    ├── app.py
    ├── bookstore.conf
    ├── compose.yml
    ├── db.py
    ├── Dockerfile
    ├── .dockerignore
    ├── .env
    ├── init_db.sql
    └── requirements.txt
    
    1 directory, 9 files

запускаю и проверяю:

    docker compose up -d
    docker compose ps
***
    NAME                IMAGE             COMMAND                  SERVICE     CREATED          STATUS                    PORTS
    app24-bookstore-1   app24-bookstore   "python app.py"          bookstore   12 minutes ago   Up 12 minutes (healthy)   
    app24-db-1          postgres:17       "docker-entrypoint.s…"   db          12 minutes ago   Up 12 minutes (healthy)   5432/tcp
    app24-web-1         nginx:alpine      "/docker-entrypoint.…"   web         12 minutes ago   Up 11 minutes             0.0.0.0:80->80/tcp, [::]:80->80/tcp
<img width="1562" height="140" alt="image" src="https://github.com/user-attachments/assets/3d118fdf-fe66-4f74-8db6-766a7f190c74" />

***
    curl -s 127.0.0.1:80/books | jq .
***
    [
      {
        "author": "Ник Перумов",
        "id": 1,
        "price": 628.0,
        "title": "Эльфийский клинок"
      },
      {
        "author": "Ник Перумов",
        "id": 2,
        "price": 180.0,
        "title": "Черное копье"
      },
      {
        "author": "Роджер Желязны",
        "id": 3,
        "price": 984.0,
        "title": "Хроники Амбера Том I"
      },
      {
        "author": "Роджер Желязны",
        "id": 4,
        "price": 1126.0,
        "title": "Хроники Амбера II"
      },
      {
        "author": "Урсула Ле Гуин",
        "id": 5,
        "price": 1240.0,
        "title": "Волшебник Земноморья"
      },
      {
        "author": "Дж.Р.Р.Толкин",
        "id": 6,
        "price": 324.0,
        "title": "Властелин колец: Хранители кольца"
      },
      {
        "author": "Дж.Р.Р.Толкин",
        "id": 7,
        "price": 351.0,
        "title": "Властелин колец: Две твердыни"
      },
      {
        "author": "Дж.Р.Р.Толкин",
        "id": 8,
        "price": 344.0,
        "title": "Властелин колец: Возвращение короля"
      },
      {
        "author": "Дж.Р.Р.Толкин",
        "id": 9,
        "price": 281.0,
        "title": "Сильмаллирион"
      },
      {
        "author": "Дж.Р.Р.Толкин",
        "id": 10,
        "price": 296.0,
        "title": "Дети Хурина"
      },
      {
        "author": "Аркадий и Борис Стругацкие",
        "id": 11,
        "price": 404.0,
        "title": "Понедельник начинается в субботу"
      },
      {
        "author": "Аркадий и Борис Стругацкие",
        "id": 12,
        "price": 561.0,
        "title": "Трудно быть богом"
      },
      {
        "author": "Виктор Пелевин",
        "id": 13,
        "price": 461.0,
        "title": "Чапаев и Пустота"
      },
      {
        "author": "Чак Паланик",
        "id": 14,
        "price": 666.0,
        "title": "Бойцовский клуб"
      },
      {
        "author": "Аристотель",
        "id": 15,
        "price": 333.0,
        "title": "Метафизика"
      },
      {
        "author": "Чарльз Буковски",
        "id": 16,
        "price": 532.0,
        "title": "Женщины"
      },
      {
        "author": "Чарльз Буковски",
        "id": 17,
        "price": 522.0,
        "title": "Почтамт"
      },
      {
        "author": "Чарльз Буковски",
        "id": 18,
        "price": 488.0,
        "title": "Хлеб с ветчиной"
      },
      {
        "author": "Чарльз Буковски",
        "id": 19,
        "price": 392.0,
        "title": "Голливуд"
      },
      {
        "author": "Чарльз Буковски",
        "id": 20,
        "price": 432.0,
        "title": "Фактотум"
      },
      {
        "author": "Франц Кафка",
        "id": 21,
        "price": 612.0,
        "title": "Процесс"
      }
    ]
