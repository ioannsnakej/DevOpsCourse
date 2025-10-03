1. Определить путевое имя рабочего каталога. Как обозначается корневой
каталог? Какое путевое имя получили (относительное или
абсолютное)?

    Корневой каталог обозначается "/"
      
    Получил абсолютное путевое имя /home/ivan - полный путь от корня
    
    <img width="298" height="196" alt="image" src="https://github.com/user-attachments/assets/7106a6ea-a2a9-4f79-83fd-5b9040727d5d" />

2. Создать в начальном каталоге два подкаталога. Просмотреть
содержимое рабочего каталога. Просмотреть содержимое
родительского каталога, не переходя в него.

    <img width="872" height="264" alt="image" src="https://github.com/user-attachments/assets/a6efb3c8-3f5a-4296-972a-b1c49d7df509" />

3. Перейти в системный каталог. Просмотреть его содержимое.
Просмотреть содержимое начального каталога. Вернуться в начальный
каталог.

    <img width="901" height="442" alt="image" src="https://github.com/user-attachments/assets/ab23f748-1fbc-46cf-812b-ed42eee956dd" />

    <img width="841" height="617" alt="image" src="https://github.com/user-attachments/assets/ac2b2610-ec9f-4243-8509-6ae3394d9039" />

    <img width="367" height="122" alt="image" src="https://github.com/user-attachments/assets/0f74372b-6c45-471a-8ed2-82327b79bdc1" />

4. Удалить созданные ранее подкаталоги.

    <img width="817" height="213" alt="image" src="https://github.com/user-attachments/assets/ec7325a0-7a38-4a6e-9d16-3c55e8b2f1c9" />

5. Получить информацию по командам ls и cd с помощью утилиты man.
Изучить структуру man-документа

    <img width="869" height="491" alt="image" src="https://github.com/user-attachments/assets/e0881879-4786-496d-b57b-16db4b61cda3" />

    <img width="829" height="883" alt="image" src="https://github.com/user-attachments/assets/9e83d750-3c34-4d99-b245-1004f5821516" />

6. Получить краткую информацию по командам ls и cd с помощью
команды whatis и apropos. В чем различие?

   <img width="899" height="319" alt="image" src="https://github.com/user-attachments/assets/99606b60-df4c-47da-a6b1-b73ec4292cc6" />

   <img width="899" height="620" alt="image" src="https://github.com/user-attachments/assets/f2ab3cf7-df4e-4a17-9622-e66bcfedb54a" />

   whatis - Выводит краткое описание команды. Использует базу данных, которая содержит краткое описание всех команд, доступных в системе. Команда ищет в базе данных соответствующую запись для указанной команды и выводит её на экран/

   apropos - Находит все команды, связанные с заданным ключевым словом. Выводит на экран краткие описания команд, содержащие строку, переданную ей в качестве аргумента.

7. То же, что и в п.5, только с помощью команды info.

    Команда info в Linux — утилита, которая читает документацию, хранящуюся в формате Info. Страницы документов Info создаются с помощью инструментов Texinfo, могут связываться с другими страницами и создавать меню для навигации. 

    <img width="549" height="86" alt="image" src="https://github.com/user-attachments/assets/43cce127-f893-4fe9-a8ad-351705cfd67e" />

    <img width="770" height="352" alt="image" src="https://github.com/user-attachments/assets/45a39c4d-1c7b-4afe-818d-6cefbd83e3ea" />

8. Создайте в домашнем каталоге следующую структуру подкаталогов
(существующие каталоги не удаляйте!)

    <img width="1009" height="866" alt="image" src="https://github.com/user-attachments/assets/b3f4727a-4db5-43c7-b85f-75894835f8bd" />

   <img width="297" height="102" alt="image" src="https://github.com/user-attachments/assets/13c960b2-961d-4ef2-b979-4ae815233be8" />

9. Выведите первые и последние 13 строк файла /etc/group.

    <img width="820" height="633" alt="image" src="https://github.com/user-attachments/assets/867d9d6e-b144-4889-bc69-697c21f49d9d" />

10. Увеличить диск на ВМ на 5ГБ

    В настройках ВМ увеличиваем диск на 5ГБ (ctrl+d -> выбираем наш диск -> свойства -> внизу меняем с 25 на 30 ГБ -> Применить)

    <img width="1911" height="1041" alt="image" src="https://github.com/user-attachments/assets/ff472317-40e1-4c69-a61e-c6642bcf93a8" />

    Проверяем через lsblk

    <img width="718" height="536" alt="image" src="https://github.com/user-attachments/assets/4c2b193d-6fa6-47ef-a542-b4b1dac15b36" />

    Увеличиваем раздел /dev/sda2 утилитой growpart

    <img width="1013" height="725" alt="image" src="https://github.com/user-attachments/assets/6737ff24-a848-47e3-95c2-20b6b4e33a55" />

    Увеличил размер файловой системы утилитой resize2fs

    <img width="987" height="834" alt="image" src="https://github.com/user-attachments/assets/702bd526-c124-452f-ab8a-e6c8692d2282" />




    










