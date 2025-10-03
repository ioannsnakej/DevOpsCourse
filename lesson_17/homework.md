Задание 1:
  Вводные данные
  
  Есть таблица анализов Analysis:
  
  ● an_id — ID анализа;
  
  ● an_name — название анализа;
  
  ● an_cost — себестоимость анализа;
  
  ● an_price — розничная цена анализа;
  
  ● an_group — группа анализов.
  
  Есть таблица групп анализов Groups:
  
  ● gr_id — ID группы;

  ● gr_name — название группы;
  
  ● gr_temp — температурный режим хранения.
  
  Есть таблица заказов Orders:
  
  ● ord_id — ID заказа;
  
  ● ord_datetime — дата и время заказа;
  
  ● ord_an — ID анализа.
  
  Формулировка: вывести название и цену для всех анализов, которые
  продавались 5 февраля 2020 и всю следующую неделю

  Устанавливаю mariadb:
  
    sudo apt update && sudo apt install -y mariadb-server

  устанавливаю mysql secure:

    sudo mysql_secure_installation

  и устанавливаю пароль для root в процессе установки
  
  Прописываю пользователя и пароль в файл /etc/mysql/debian.cnf

  подключаюсь к mysql:

    sudo mysql --defaults-file=/etc/mysql/debian.cnf

  создаю базу данных и подключаюсь:

     create database medcenter;
     use medcenter;
  создаю таблицы:

    create table groups (gr_id int autoincrement primary key, gr_name varchar(100), gr_temp varchar(255));
    create table analysys (an_id int autoincrement primary key, an_name varchar(100), an_cost int, an_price int, an_group int, foreign key (an_group) references groups(gr_id));
    create table orders (ord_id int autoincrement primary key, ord_datetime datetime, ord_an int, foreign key (ord_an) references analysys(an_id));
  Добавляю значения в таблицы:

    insert into groups (gr_name, gr_temp) values ('clinic','+18-20C'), ('PCR','+18-20C'), ('BG', '+18-20C');
    insert into analysis (an_name, an_cost, an_price, an_group) values ('ABOgroupinBlood',250,450,3 ),('OAK',150,500,1),('PCR',200,800, 2);
    insert into orders (ord_datetime,ord_an) values ('2020-02-01 09:00:00',1),('2020-02-02 14:30:00',2),('2020-02-03 11:15:00',3)..;
  пишу селект:

    select an_name, an_price from analysis 
      -> join orders o on an_id=o.ord_an
      -> where o.ord_datetime between '2020-02-05 00:00:00' and '2020-02-12 23:59:59';
***
    +-----------------+----------+
    | an_name         | an_price |
    +-----------------+----------+
    | ABOgroupinBlood |      450 |
    | ABOgroupinBlood |      450 |
    | OAK             |      500 |
    | OAK             |      500 |
    | OAK             |      500 |
    | PCR             |      800 |
    | PCR             |      800 |
    | PCR             |      800 |
    +-----------------+----------+


Задание 2(опционально):
Используя left join, напишите запрос, который будет выводить список всех
студентов и названий их курсов, которые они изучают. Если у студента нет
курсов, то вместо названия курса нужно выводить NULL. Для этого вам
необходимо связать таблицы "Студенты" и "Курсы".

  Создаю базу:

    create database StudentCourses;
    use StudentCourses;
  Создаю таблицы:

    create table courses (course_id int auto_increment primary key, course_name varchar(155));
    create table students (student_id int auto_increment primary key, student_name varchar(255), cr_course int, foreign key (cr_course) references courses(course_id));
  Добавляю значения:

    INSERT INTO courses (course_name) VALUES ("DevOps-enginer"),("Front-end"),("Back-end"),("UX/UI-designer"),("System administrator"),("Business analyst");
    INSERT INTO students (student_name, cr_course) VALUES ("Tom Searl",1), ("Mitch Lucker",3), ("Alex Koehler",5), ("Chris Fronzak",3), ("Ramin Niroomand", NULL), ("Austin Carlile", NULL), ("Jason Aalon Butler", NULL),("Sam Carter", 4), ("Vic Fuentes", 6);
  Пишу селект:

    select student_id, student_name, c.course_name from students left join courses c on cr_course=c.course_id;
  ***
    
      +------------+--------------------+----------------------+
    | student_id | student_name       | course_name          |
    +------------+--------------------+----------------------+
    |          1 | Tom Searl          | DevOps-enginer       |
    |          2 | Mitch Lucker       | Back-end             |
    |          3 | Alex Koehler       | System administrator |
    |          4 | Chris Fronzak      | Back-end             |
    |          5 | Ramin Niroomand    | NULL                 |
    |          6 | Austin Carlile     | NULL                 |
    |          7 | Jason Aalon Butler | NULL                 |
    |          8 | Sam Carter         | UX/UI-designer       |
    |          9 | Vic Fuentes        | Business analyst     |
    |         10 | Tony Mahony        | NULL                 |
    +------------+--------------------+----------------------+

Задание 3:
  Шаги:
  1. Создайте бэкап базы данных. Для этого используйте команду
  "mysqldump" для создания полного дампа базы данных. Сохраните файл
  дампа в безопасном месте, таком как внешний жесткий диск или облачное
  хранилище.
  2. Измените какие-либо данные в базе данных, например, добавьте новую
  таблицу или обновите информацию в существующей таблице.
  3. Восстановите базу данных из бэкапа, чтобы вернуть ее в исходное
  состояние. Для этого используйте команду "mysql" и укажите имя базы
  данных и файл дампа для восстановления.
  4. Убедитесь, что база данных была восстановлена успешно, проверив
  данные и таблицы в базе данных.
  5. Создайте скрипт, который будет автоматически создавать бэкап базы
  данных и отправлять его на удаленный сервер для хранения. Например, вы
  можете использовать инструмент "cron" для регулярного создания бэкапов и
  передачи их на удаленный сервер по расписанию

  1. Создаю скрипт для создания дампа (переписываю с урока):

          1 #!/usr/bin/env bash                                                                  
          2 
          3 #Проверка, что переданы параметры (опция и имя бд)
          4 if [ "$#" -ne 2 ]; then
          5   echo "Usage: $0 --create|--restore <dbname>"
          6   exit 1
          7 fi
          8 
          9 #Проверка, что скрипт запущен под sudo
         10 if [ $(id -u) -ne 0 ]; then
         11   echo "Usage: sudo $0 --create|--restore <dbname>"
         12   exit 1
         13 fi
         14 
         15 DB_NAME="$2"
         16 BKP_DIR="/opt/backups"
         17 DEFS="--defaults-file=/etc/mysql/debian.cnf"
         18 
         19 #Проверка, что директория для бэкапов существует
         20 if [ ! -d "${BKP_DIR}" ]; then
         21   echo "Dir does not exist, creating."
         22   mkdir -p "${BKP_DIR}"
         23 fi
         24 
         25 TIMESTAMP=$(date +%s)
         26 
         27 case "$1" in
         28   "--create") mysqldump ${DEFS} ${DB_NAME} > "${BKP_DIR}/${TIMESTAMP}-${DB_NAME}.sql"
         29   ;;
         30   "--restore") read -p "Set backup name: " restored
         31         mysql ${DEFS} -e "drop database if exists ${DB_NAME};"
         32         mysql ${DEFS} -e "create database ${DB_NAME};"
         33         mysql ${DEFS} ${DB_NAME} < ${BKP_DIR}/${restored}
         34   ;;
         35   *) echo "No such option"
         36   ;;
         37 esac


  




  

  



  


  












