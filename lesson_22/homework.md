>[!NOTE]
>Решил вести домашки с минимальным использованием скриншотов, т.к. допустил ошибку, когда скопировал все домашки в одну репу и удалил старые репы - 
Все скрины стали 404, оно и понятно, ведь скрины это ссылки на атачменты
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

Вывожу список всех образов, установленных на хосте:

    docker images
***
    REPOSITORY    TAG       IMAGE ID       CREATED       SIZE
    nginx         latest    203ad09fc156   7 weeks ago   192MB
    hello-world   latest    1b44b5a3e06a   8 weeks ago   10.1kB
***
Вывожу с помощью docker inspect размер каждого образа:

    docker inspect --format='{{.Size}}' nginx
    192385293
***
    docker inspect --format='{{.Size}}' hello-world
    10072
Вывожу все слои каждого образа:

    docker inspect nginx --format='{{json .RootFS.Layers}}' | jq
***
    [
      "sha256:aca83606673032726b42f8e1396ceb979c32bfb26b602732baf699053e46b33e",
      "sha256:c9314274c8aefb8518681aa80b2352a1dd07a4f8bbca2709973472e9b441236e",
      "sha256:1acc971d8a3daa05e9e05d98d8b488da4a5b69805605d48e977532111ac18364",
      "sha256:fe540ea49339936f9ca0a0e958e623a3438137aa40d3fb4279f23b5d1b2ec87d",
      "sha256:cd35d865f504cb0528a5ca2995dd68e875f026f8e3c859a0ecf941f256a253b8",
      "sha256:4e8d3453aa66b2fe41872a4fd4050eb74e52ffdf8512a57beec063947e79bcd6",
      "sha256:131c1c486b1725d863010cbd1403e9173d791d7e83337bdb46095cbc97f9d27f"
    ]
***
    docker inspect nginx | jq -r '.[] | .GraphDriver'
***
    {
      "Data": {
        "LowerDir": "/var/lib/docker/overlay2/65ea5b95f9c0ac64fb22c563e211155402126985aef954638fea30e5dfbb8125/diff:/var/lib/docker/overlay2/b637512e875a29a612e829661b2a5276027a28f489083b91dbf9468efe1511a5/diff:/var/lib/docker/overlay2/50e01eb70e4ca730ced1ac166493b2a81f64475008fc2de70709b67ef9690c78/diff:/var/lib/docker/overlay2/b5c3a21770ba742001ec8549b550039e4f421fdea35ae6078231877ef92239f9/diff:/var/lib/docker/overlay2/945391b1d06e8084a30e64011b2b51625d4828a6e6841d136d6d6dd7e7597bc4/diff:/var/lib/docker/overlay2/9a825914e1102b91ae8a8df4c293401505df99bbc4330b3fdf3a70ece03167ec/diff",
        "MergedDir": "/var/lib/docker/overlay2/05a490c7df6585d20bc7ba23f60aedb8ed626314835ebd7a091e93693565e0ac/merged",
        "UpperDir": "/var/lib/docker/overlay2/05a490c7df6585d20bc7ba23f60aedb8ed626314835ebd7a091e93693565e0ac/diff",
        "WorkDir": "/var/lib/docker/overlay2/05a490c7df6585d20bc7ba23f60aedb8ed626314835ebd7a091e93693565e0ac/work"
      },
      "Name": "overlay2"
    }
***
    docker inspect hello-world --format='{{json .RootFS.Layers}}' | jq
***
    [
      "sha256:53d204b3dc5ddbc129df4ce71996b8168711e211274c785de5e0d4eb68ec3851"
    ]
***
    docker inspect hello-world | jq -r '.[] | .GraphDriver'
***
    {
      "Data": {
        "MergedDir": "/var/lib/docker/overlay2/c6c043609712b01595d9d38394133ea0d15c387880bf64607ab9606073bfc96c/merged",
        "UpperDir": "/var/lib/docker/overlay2/c6c043609712b01595d9d38394133ea0d15c387880bf64607ab9606073bfc96c/diff",
        "WorkDir": "/var/lib/docker/overlay2/c6c043609712b01595d9d38394133ea0d15c387880bf64607ab9606073bfc96c/work"
      },
      "Name": "overlay2"
    }
Вывожу размер всех слоев:

    docker history nginx
***
    IMAGE          CREATED       CREATED BY                                      SIZE      COMMENT
    203ad09fc156   7 weeks ago   CMD ["nginx" "-g" "daemon off;"]                0B        buildkit.dockerfile.v0
    <missing>      7 weeks ago   STOPSIGNAL SIGQUIT                              0B        buildkit.dockerfile.v0
    <missing>      7 weeks ago   EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
    <missing>      7 weeks ago   ENTRYPOINT ["/docker-entrypoint.sh"]            0B        buildkit.dockerfile.v0
    <missing>      7 weeks ago   COPY 30-tune-worker-processes.sh /docker-ent…   4.62kB    buildkit.dockerfile.v0
    <missing>      7 weeks ago   COPY 20-envsubst-on-templates.sh /docker-ent…   3.02kB    buildkit.dockerfile.v0
    <missing>      7 weeks ago   COPY 15-local-resolvers.envsh /docker-entryp…   389B      buildkit.dockerfile.v0
    <missing>      7 weeks ago   COPY 10-listen-on-ipv6-by-default.sh /docker…   2.12kB    buildkit.dockerfile.v0
    <missing>      7 weeks ago   COPY docker-entrypoint.sh / # buildkit          1.62kB    buildkit.dockerfile.v0
    <missing>      7 weeks ago   RUN /bin/sh -c set -x     && groupadd --syst…   118MB     buildkit.dockerfile.v0
    <missing>      7 weeks ago   ENV DYNPKG_RELEASE=1~bookworm                   0B        buildkit.dockerfile.v0
    <missing>      7 weeks ago   ENV PKG_RELEASE=1~bookworm                      0B        buildkit.dockerfile.v0
    <missing>      7 weeks ago   ENV NJS_RELEASE=1~bookworm                      0B        buildkit.dockerfile.v0
    <missing>      7 weeks ago   ENV NJS_VERSION=0.9.1                           0B        buildkit.dockerfile.v0
    <missing>      7 weeks ago   ENV NGINX_VERSION=1.29.1                        0B        buildkit.dockerfile.v0
    <missing>      7 weeks ago   LABEL maintainer=NGINX Docker Maintainers <d…   0B        buildkit.dockerfile.v0
    <missing>      7 weeks ago   # debian.sh --arch 'amd64' out/ 'bookworm' '…   74.8MB    debuerreotype 0.16
***
    docker history hello-world
***
    IMAGE          CREATED       CREATED BY                SIZE      COMMENT
    1b44b5a3e06a   8 weeks ago   CMD ["/hello"]            0B        buildkit.dockerfile.v0
    <missing>      8 weeks ago   COPY hello / # buildkit   10.1kB    buildkit.dockerfile.v0
Больше всего занимает слой:

    <missing>      7 weeks ago   RUN /bin/sh -c set -x     && groupadd --syst…   118MB     buildkit.dockerfile.v0
Удаляю ненужный образ hello-world:

    docker rmi hello-world
    docker images
***
    REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
    nginx        latest    203ad09fc156   7 weeks ago   192MB
Удаляю неиспользуемые образы, включая очистку неиспользуемых томов:

    docker system prune -a --volumes --force
***
    Deleted Images:
    untagged: nginx:latest
    untagged: nginx@sha256:8adbdcb969e2676478ee2c7ad333956f0c8e0e4c5a7463f4611d7a2e7a7ff5dc
    deleted: sha256:203ad09fc1566a329c1d2af8d1f219b28fd2c00b69e743bd572b7f662365432d
    deleted: sha256:a2d93a3b0194dbe86680b99ab30f521a9fa4de8843ee27213b23123d0dbae7ea
    deleted: sha256:b426570453c6490acac1f45f4032cf2dc658af43febbf00b6654b8abf17d38bd
    deleted: sha256:047437fbe863fe3b59732b312a0df38c9311e9dc6f1568a80bae116435d565f4
    deleted: sha256:7a5c11f06fbf611367e3c8240a2958d3a5f890622ec5e0593d9551f82c107d47
    deleted: sha256:b919da8edb70f9ddaead6c98269ba072038f78e5af1f5e337024e3c5a173c1cf
    deleted: sha256:7cad966cf84607ab3fccd44b932d8801e4008ea9e585e7c575407e65e451af19
    deleted: sha256:aca83606673032726b42f8e1396ceb979c32bfb26b602732baf699053e46b33e
    
    Total reclaimed space: 192.4MB
Проверяю использование дискового пространства:

    docker system df -v
***
    Images space usage:
    
    REPOSITORY   TAG       IMAGE ID   CREATED   SIZE      SHARED SIZE   UNIQUE SIZE   CONTAINERS
    
    Containers space usage:
    
    CONTAINER ID   IMAGE     COMMAND   LOCAL VOLUMES   SIZE      CREATED   STATUS    NAMES
    
    Local Volumes space usage:
    
    VOLUME NAME   LINKS     SIZE
    
    Build cache usage: 0B
    
    CACHE ID   CACHE TYPE   SIZE      CREATED   LAST USED   USAGE     SHARED
