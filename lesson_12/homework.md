1. В консольном режиме создать в домашнем каталоге подкаталог:
/фамилия_студента, где в дальнейшем будут храниться все рабочие
файлы студента.

2. В любом текстовом редакторе (например, vim: vim 1.c) написать
программу 1.c, выводящую на экран фразу "HELLO Ubuntu".
Компилировать полученную программу компилятором gcc: gcc 1.c –o
1.exe. Запустить полученный файл 1.exe на выполнение: ./1.exe

  Создаю папку и файл:
  
  <img width="1155" height="322" alt="image" src="https://github.com/user-attachments/assets/44a663d0-abcf-40ec-9b14-a5510ec6b252" />

  <img width="504" height="336" alt="image" src="https://github.com/user-attachments/assets/ec4e53ce-e43c-4dc2-a4c4-0ba112d0e91d" />

  Компилирую с помощью gcc и запускаю 1.exe:

  <img width="524" height="144" alt="image" src="https://github.com/user-attachments/assets/bf904efb-7509-4cbc-8efa-4eb564d7bcfb" />

3. Написать скрипт, выводящий на консоль и в файл все аргументы
командной строки.

  Создаю файл:

  <img width="523" height="70" alt="image" src="https://github.com/user-attachments/assets/794ec8fa-c4e2-4411-84b5-fd64fea54ddc" />

  Пишу сам скрипт:

  <img width="711" height="367" alt="image" src="https://github.com/user-attachments/assets/f811c633-d8a2-461e-aa44-4cf266aa5bf2" />

  Проверяю его работу в терминале vscode:

  <img width="604" height="340" alt="image" src="https://github.com/user-attachments/assets/6d6f81f7-2cf7-43b2-b60c-ff1733e09c2e" />

  Проверяю в терминале:

  <img width="772" height="312" alt="image" src="https://github.com/user-attachments/assets/262a1c85-9dcc-4bd4-bc64-eb5fe0266362" />

4. Написать скрипт, выводящий в файл (имя файла задаётся
пользователем в качестве первого аргумента командной строки) имена
всех файлов с заданным расширением (третий аргумент командной
строки) из заданного каталога (имя каталога задаётся пользователем в
качестве второго аргумента командной строки).

  Создаю скрипт:
  
  <img width="597" height="118" alt="image" src="https://github.com/user-attachments/assets/fab5b1e0-935e-4e1d-bd55-20f4f61c963b" />

  <img width="860" height="436" alt="image" src="https://github.com/user-attachments/assets/24424406-e9d7-48c5-8c02-da3087abec68" />

  Проверки:

  <img width="764" height="528" alt="image" src="https://github.com/user-attachments/assets/661e3bf8-2668-4321-9c39-73a2517705f3" />

  <img width="1171" height="159" alt="image" src="https://github.com/user-attachments/assets/621c69f0-5586-4248-afdf-444a06b6d57e" />

  <img width="949" height="167" alt="image" src="https://github.com/user-attachments/assets/fcf10a1a-cfb7-4171-a010-1d0e3924dc6c" />

5. Написать скрипт с использованием цикла for, выводящий на консоль
размеры и права доступа для всех файлов в заданном каталоге и всех
его подкаталогах (имя каталога задается пользователем в качестве
первого аргумента командной строки).

  Текст скрипта:

  <img width="701" height="526" alt="image" src="https://github.com/user-attachments/assets/b96c2bb7-c54c-4b33-bce1-e15bf1bf83bd" />

  Запуски:

  <img width="1235" height="941" alt="image" src="https://github.com/user-attachments/assets/3ffa0a08-dcd3-46bd-a101-77c3c59a71ff" />

  <img width="849" height="294" alt="image" src="https://github.com/user-attachments/assets/865f291e-6807-4d3f-a99f-b6a22344360f" />

  
6. Написать скрипт для поиска заданной пользователем строки во всех
файлах заданного каталога и всех его подкаталогах (строка и имя
каталога задаются пользователем в качестве первого и второго
аргумента командной строки). На консоль выводятся полный путь и
имена файлов, в содержимом которых присутствует заданная строка, и
их размер. Если к какому-либо каталогу нет доступа, необходимо
вывести соответствующее сообщение и продолжить выполнение

    Создал скрипт, но без проверки на sudo, наколхозил из предыдущего скрипта) Извините:
   
    <img width="742" height="643" alt="image" src="https://github.com/user-attachments/assets/b4466d82-c90d-48cc-ab64-3c47208f02d2" />

    Проверяю:

   <img width="862" height="308" alt="image" src="https://github.com/user-attachments/assets/f2dc47ed-d893-4f1a-81b6-39da8a9264dd" />


  












