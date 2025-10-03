1. Установите и настройте Nginx. Создайте html-страничку, где будет указана
ваша фамилия и тема урока. Настройте конфигурацию nginx для отображения
страницы по ссылке http://tms.by

  nginx был установлен в предыдущих домашках:

  <img width="1257" height="484" alt="image" src="https://github.com/user-attachments/assets/60ff9a6e-d424-4eee-a97d-033638c91abf" />

  создал index.html в /var/www/lesson_15_hw:

  <img width="933" height="591" alt="image" src="https://github.com/user-attachments/assets/048179bf-0a13-442b-90a3-a5360e38fae4" />

  создаю nginx config, проверяю, делаю символическую ссылку:

  <img width="927" height="554" alt="image" src="https://github.com/user-attachments/assets/af7ab481-314a-4725-9198-fe10aeadb5fa" />

  перезапускаю nginx, через sudo nginx -s reload:

  <img width="688" height="85" alt="image" src="https://github.com/user-attachments/assets/c63396a9-1c05-4fec-a3f3-cca69455a50a" />
  
  добавил домен tms.by в файл /etc/hosts

  <img width="781" height="739" alt="image" src="https://github.com/user-attachments/assets/3b2474e0-9c03-4370-98be-d5bc584d9349" />

  пробую заходить:

  <img width="943" height="659" alt="image" src="https://github.com/user-attachments/assets/25d5d963-3f2d-4ecf-adfd-4155e96b07dc" />


2. Настройте связку Nginx + Apache. Nginx выступает в качестве reverse proxy

   устанавливаю apache2:

   <img width="943" height="630" alt="image" src="https://github.com/user-attachments/assets/3e87512a-e4bb-449a-8a2e-c42c4d87099d" />

   пришлось поковыряться в гугле и чатегпт, так как apache2 не хотел ни в какую запускаться (пишу для истории, мб, когда-нибудь этот опыт пригодится):

   <img width="1829" height="305" alt="image" src="https://github.com/user-attachments/assets/1f27f891-1f76-4355-beaa-6af642776fd4" />

   Кратко, что делал и что помогло в итоге:

   1. проверил конфиг apache2 (146 строку) - sudo nano -l /etc/apache2/apache2.conf (146 IncludeOptional mods-enabled/*.load)
   2. проверил содержимое /etc/apache2/mods-enabled/access_compat.load (LoadModule access_compat_module /usr/lib/apache2/modules/mod_access_compat.so)
   3. попробовал отыскать данный модуль:  ls /usr/lib/apache2/modules/ | grep access_compat ls: невозможно получить доступ к '/usr/lib/apache2/modules/': Нет такого файла или каталога
   4. переустановил apache2 sudo apt install —reinstall apache2 и проверил конфиг: sudo apachectl configtest apache2: Syntax error on line 146 of /etc/apache2/apache2.conf: Syntax error on line 2 of /etc/apache2/mods-enabled/access_compat.load: Cannot load /usr/lib/apache2/modules/mod_access_compat.so into server: /usr/lib/apache2/modules/mod_access_compat.so: cannot open shared object file: No such file or directory
   5. повторно переустановил командой sudo apt install --reinstall apache2 apache2-bin
  
   Работает:

   <img width="1615" height="430" alt="image" src="https://github.com/user-attachments/assets/084a21b4-6999-4eba-8513-3cb88a6782f6" />

   меняю порт на 8080 в /etc/apache2/ports.conf:

   <img width="840" height="380" alt="image" src="https://github.com/user-attachments/assets/6b3824cf-0277-49be-8c2c-729d2eb3a1e0" />

   проверил, что он на 8080:

   <img width="915" height="470" alt="image" src="https://github.com/user-attachments/assets/23c4c4f5-ae0a-4a66-9c28-fc3e5b27c53a" />

   запускаю nginx:

   <img width="872" height="165" alt="image" src="https://github.com/user-attachments/assets/f6fa1546-0e81-476b-9813-fe8024b70cc1" />

   создал конфиг apache2:

   <img width="900" height="406" alt="image" src="https://github.com/user-attachments/assets/0ca8d356-5ccf-49e5-9b56-52a7aebd0bd9" />


    Активирую виртуальный хост:

   <img width="646" height="215" alt="image" src="https://github.com/user-attachments/assets/81296454-0ccb-4246-aee3-2b879c30aa2a" />

   меняю конфиг nginx:

   <img width="848" height="385" alt="image" src="https://github.com/user-attachments/assets/e6c9c9cb-5c38-4003-9d4b-dd1abc62a73a" />

   перезапускаю nginx:

   <img width="683" height="188" alt="image" src="https://github.com/user-attachments/assets/5422ccbe-75f8-4715-b011-104cf153c169" />

   Проверяю:

   <img width="947" height="797" alt="image" src="https://github.com/user-attachments/assets/edf34fd7-5d8b-4e96-886c-043a98a98ed5" />











      



     

  




  
