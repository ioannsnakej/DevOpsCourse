Генерирую самоподписанный сертификат, попутно создаю директорию под него:

     sudo openssl req -newkey rsa:4096 -nodes -sha256 -keyout /etc/nginx/ssl/certs/domain.key -x509 -addext "subjectAltName = IP:192.168.56.1" -days 365 -out /etc/nginx/ssl/certs/domain.crt
меняю nginx config и apache2 config:

    sudo vim /etc/nginx/sites-available/lesson_15_hw
***
    server {
      listen 443 ssl;
      server_name tms.by;
      ssl_certificate /etc/nginx/ssl/certs/domain.crt;
      ssl_certificate_key /etc/nginx/ssl/certs/domain.key;
    
      include /etc/nginx/ssl/ssl.conf;
       HSTS
      add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    
      access_log /var/log/nginx/lesson_15_access.log;
      error_log /var/log/nginx/lesson_15_error.log;
    
      root /var/www/lesson_15_hw/;
      index index.html;
    
      location / {
              #try_files $uri $uri/ = 404;
        proxy_pass http://192.168.56.1:8080;
        proxy_redirect off;
      }
    }
    
    server {
      listen 80;
      server_name tms.by;
      return 301 https://$host$request_uri;
    }

***
    sudo vim /etc/apache2/sites-available/lesson_15_hw.conf 
***
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
создаю ssl/ssl.conf:

    sudo vim /etc/nginx/ssl/ssl.conf 
***

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers EECDH+AESGCM:EDH+AESGCM;
тестирую и перезапускаю вебсерверы:

    sudo nginx -t
    sudo nginx -s reload
    sudo systemctl restart apache2.service 
Пробую заходить на сайт.





