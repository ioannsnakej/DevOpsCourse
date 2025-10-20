<h2>Цель: Практически применить изученные темы: функции, работу с
файлами и директориями, а также шаблонизатор Jinja</h2>
Задание:
<ol>
<li>Напишите функцию multiply_numbers(), которая принимает два
аргумента и возвращает их произведение. Затем вызовите эту функцию
и выведите результат на экран.</li> 

    def multipy_numbers(first_number, second_number):
      return first_number*second_number
   
    print(multipy_numbers(2,3))
***
    python3 multiply.py 
    6
<li>Создайте файл test.txt и запишите в него строку "Это тестовый файл для
домашнего задания по программированию". Затем откройте этот файл в
режиме чтения, прочитайте его содержимое и выведите на экран.</li>

    file_name='test.txt'
    
    with open(file_name, 'w+') as f:
        f.write("Это тестовый файл для домашнего задания по программированию")
        f.readline()
        f.close()
***
    python3 testtxt.py
    cat test.txt
    Это тестовый файл для домашнего задания по программированию
<li>Создайте пустую директорию mydir в текущей рабочей директории.
Затем перейдите в эту директорию и создайте в ней три пустых файла:
file1.txt, file2.txt и file3.txt. Наконец, выведите список файлов в
директории на экран.</li>

    import os
    
    def touch_file(dir, *files):
        if not os.path.isdir(dir):
            os.mkdir(dir)
    
        os.chdir(dir)
        print("PWD:", os.getcwd())
    
        for file in files:
            open(file, 'a').close()
            print(f"Создан файл: {file}")
    
        os.chdir("..")
        print("PWD:", os.getcwd())
    
    touch_file("mydir", "file1.txt", "file2.txt", "file3.txt")
***
    ls
    hw25.py  multiply.py  test.txt  testtxt.py  touchfile.py
***
    python3 touchfile.py 
    PWD: /home/ivan/python/mydir
    Создан файл: file1.txt
    Создан файл: file2.txt
    Создан файл: file3.txt
    PWD: /home/ivan/python
***
    ls
    hw25.py  multiply.py  mydir  test.txt  testtxt.py  touchfile.py
    ls mydir/
    file1.txt  file2.txt  file3.txt
<li>Создайте шаблон template.html, который будет содержать HTML-код
для отображения списка пользователей. Шаблон должен использовать
цикл for для перебора элементов списка, и выводить имя и email
каждого пользователя. Затем создайте список пользователей в виде
списка словарей, передайте его в шаблон и отобразите результат на
экране.</li> 
</ol>
