1. Установите и настройте Nginx. Создайте html-страничку, где будет указана
ваша фамилия и тема урока. Настройте конфигурацию nginx для отображения
страницы по ссылке http://tms.by

  nginx был установлен в предыдущих домашках:

    nginx -v
  создал index.html в /var/www/lesson_15_hw:

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
  создаю nginx config, проверяю, делаю символическую ссылку:

    server {
      listen 80;
      server_name tms.by;
      access_log /var/log/nginx/lesson_15_access.log;
      error_log /var/log/nginx/lesson_15_error.log;
    
      root /var/www/lesson_15_hw/;
      index index.html;
    
      location / {
        try_files $uri $uri/ = 404;
            #proxy_pass http://192.168.0.1:8080;
            #proxy_redirect off;
      }
    }
  ***
  
    sudo ln -s /etc/nginx/sites-available/lesson_15_hw /etc/nginx/sites-enabled/
  перезапускаю nginx:

    sudo nginx -s reload
  добавил домен tms.by в файл /etc/hosts

    127.0.0.1 localhost
    127.0.1.1 VM1-Ubuntu
    192.168.56.1 tms.by
    
    # The following lines are desirable for IPv6 capable hosts
    ::1     ip6-localhost ip6-loopback
    fe00::0 ip6-localnet
    ff00::0 ip6-mcastprefix
    ff02::1 ip6-allnodes
    ff02::2 ip6-allrouters
  пробую заходить:

    http://tms.by/
2. Настройте связку Nginx + Apache. Nginx выступает в качестве reverse proxy

       sudo systemctl stop nginx

   устанавливаю apache2:

       sudo apt install -y apache2
   пришлось поковыряться в гугле и чатегпт, так как apache2 не хотел ни в какую запускаться (пишу для истории, мб, когда-нибудь этот опыт пригодится):

   Кратко, что делал и что помогло в итоге:

   1. проверил конфиг apache2 (146 строку) - sudo nano -l /etc/apache2/apache2.conf (146 IncludeOptional mods-enabled/*.load)
   2. проверил содержимое /etc/apache2/mods-enabled/access_compat.load (LoadModule access_compat_module /usr/lib/apache2/modules/mod_access_compat.so)
   3. попробовал отыскать данный модуль:  ls /usr/lib/apache2/modules/ | grep access_compat ls: невозможно получить доступ к '/usr/lib/apache2/modules/': Нет такого файла или каталога
   4. переустановил apache2 sudo apt install —reinstall apache2 и проверил конфиг: sudo apachectl configtest apache2: Syntax error on line 146 of /etc/apache2/apache2.conf: Syntax error on line 2 of /etc/apache2/mods-enabled/access_compat.load: Cannot load /usr/lib/apache2/modules/mod_access_compat.so into server: /usr/lib/apache2/modules/mod_access_compat.so: cannot open shared object file: No such file or directory
   5. повторно переустановил командой sudo apt install --reinstall apache2 apache2-bin

   меняю порт на 8080 в /etc/apache2/ports.conf:

        # If you just change the port or add more ports here, you will likely also
        # have to change the VirtualHost statement in
        # /etc/apache2/sites-enabled/000-default.conf
        
        Listen 8080
        
        <IfModule ssl_module>
                Listen 443
        </IfModule>
        
        <IfModule mod_gnutls.c>
                Listen 443
        </IfModule>

   запускаю nginx:

        sudo systemctl start nginx
   создал конфиг apache2:

        <VirtualHost 192.168.56.1:8080>
        ServerName localhost
        DocumentRoot /var/www/lesson_15_hw
      
        ErrorLog /var/log/apache2/lesson_15_hw_error.log
      
        <Directory /var/www/lesson_15_hw>
          Options Indexes FollowSymLinks
          AllowOverride All
          Require all granted
        </Directory>
      
        </VirtualHost>

    Активирую виртуальный хост:

       sudo a2ensite lesson_15_hw.conf

   меняю конфиг nginx:

        server {
          listen 80;
          server_name tms.by;
        
          access_log /var/log/nginx/lesson_15_access.log;
          error_log /var/log/nginx/lesson_15_error.log;
        
          root /var/www/lesson_15_hw/;
          index index.html;
        
          location / {
            proxy_pass http://192.168.56.1:8080;
          }
        }

   перезапускаю nginx и apache2:

       sudo nginx -s reload
       sudo systemctl restart apache2.service 
   Проверяю:

        http://tms.by/
   











      



     

  




  
