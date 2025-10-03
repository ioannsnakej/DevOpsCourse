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

  и устанавливаю пароль для root

  <img width="801" height="269" alt="image" src="https://github.com/user-attachments/assets/2f0742fe-be48-4dd8-934f-f3ad559ed82e" />

  подключаюсь к mysql:

  <img width="824" height="279" alt="image" src="https://github.com/user-attachments/assets/46c16643-14a1-4c76-a507-dfdfa8e2e5ad" />

  создаю базу данных и подключаюсь:

  <img width="481" height="165" alt="image" src="https://github.com/user-attachments/assets/ce08c047-6ff0-4f67-aca1-b351b2e8f8a8" />

  создаю таблицы:

  <img width="911" height="398" alt="image" src="https://github.com/user-attachments/assets/9b031b1c-4ab6-4193-bf56-fdb5b3fa1a04" />

  Добавляю значения в таблицы:

  <img width="909" height="265" alt="image" src="https://github.com/user-attachments/assets/52bc0e24-f059-43cf-9f95-5e709c11d5fa" />

  <img width="913" height="460" alt="image" src="https://github.com/user-attachments/assets/27198031-f155-46db-829e-60a25c8ec0f3" />

  пишу селект:

  <img width="908" height="754" alt="image" src="https://github.com/user-attachments/assets/6a356c03-e653-4587-929e-e14dcfe7dda6" />

Задание 2(опционально):
Используя left join, напишите запрос, который будет выводить список всех
студентов и названий их курсов, которые они изучают. Если у студента нет
курсов, то вместо названия курса нужно выводить NULL. Для этого вам
необходимо связать таблицы "Студенты" и "Курсы".

  Создаю базу:

  <img width="857" height="318" alt="image" src="https://github.com/user-attachments/assets/0f76e6a3-5d79-4191-ac9f-e1c59398f7f9" />

  Создаю таблицы:

  <img width="1871" height="413" alt="image" src="https://github.com/user-attachments/assets/d1c8175c-9eb9-4d78-93dc-f332f60245f6" />

  Добавляю значения:

  <img width="1868" height="389" alt="image" src="https://github.com/user-attachments/assets/0c0b6c23-6cc1-47b4-a347-70ad87464f00" />

  Пишу селект:

  <img width="884" height="462" alt="image" src="https://github.com/user-attachments/assets/fc6f9aba-8893-4197-b8a3-56e9f3d0f039" />

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


  проверяю создание бэкапа:

  <img width="810" height="114" alt="image" src="https://github.com/user-attachments/assets/9c417d5e-e1c9-4d53-8923-9585ad2b7029" />

  кусок из файла бэкапа:

  <img width="902" height="647" alt="image" src="https://github.com/user-attachments/assets/67008462-dc45-40d7-b578-0e275ad5b9fe" />

  добавляю таблицу группы:

  <img width="905" height="425" alt="image" src="https://github.com/user-attachments/assets/649074ec-f510-44b7-8ce2-bc526ab1baf0" />

  запускаю скрипт с опцией --restore:

  <img width="773" height="152" alt="image" src="https://github.com/user-attachments/assets/0b491c48-97e8-460f-926c-2190029ba187" />

  проверяю бд:

  <img width="912" height="666" alt="image" src="https://github.com/user-attachments/assets/d633b11f-8a20-4c5f-a851-34d4cc71d60d" />

  добавляю расписание в crontab:

    sudo crontab -e

  <img width="1219" height="704" alt="image" src="https://github.com/user-attachments/assets/f2aa6be0-11d2-4ff3-862d-549cc278b19d" />

  проверяю:

  <img width="1189" height="897" alt="image" src="https://github.com/user-attachments/assets/bc12b998-d30d-4b16-af24-0ca1df88b7bb" />


  




  

  



  


  












