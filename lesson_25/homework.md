Цель: Познакомиться с основами языка программирования Python и его
типами данных, а также научиться создавать простые программы на Python. В
ходе выполнения заданий вы установите Python на свой компьютер, изучите
историю языка Python, типы данных Python, а также научитесь работать со
строками, числами и переменными в Python.
<h2>Задание: (Пункты 1-5 обязательные, далее - любое задание на выбор)</h2>
<ol>
<li>Изучите историю Python</li>
<li>Установите Python по шагам описанных в документации</li>
    
    sudo apt update && sudo apt install python3.12 python3-pip -y
<li>Изучите типы данных Python</li>
<li>Напишите программу, которая выводит на экран фразу "Привет, мир!".</li>

    print("Привет, мир!")
<li>Напишите программу, которая выводит на экран результат вычисления
2+2.</li>

    print(2+2)
<li>Напишите программу, которая запрашивает у пользователя его имя и
выводит на экран сообщение "Привет, [имя]!".</li>

    user_name = input("My name is: ")
    print(f"Привет, {user_name}!")
<li>Напишите программу, которая выводит на экран числа от 1 до 10.</li>

    print(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
***
    for num in range(1,11):
        print(num)
***   
    print(list(range(1,11)))
<li>Напишите программу, которая запрашивает у пользователя его возраст
и выводит на экран сообщение "Ваш возраст [возраст] лет".</li>

    user_age = input("My age: ")
    print(f"Your age: {user_age}")
<li>Напишите программу, которая запрашивает у пользователя два числа и
выводит на экран их произведение.</li>

    first_number = input("Enter firts number: ")
    second_number = input("Enter second number: ")
    print(int(first_number) * int(second_number))
<li>Напишите программу, которая запрашивает у пользователя слово и
выводит на экран его первую букву.</li>

    user_word = input("Enter your word: ")
    print(user_word[0])
<li>Напишите программу, которая запрашивает у пользователя целое число
и выводит на экран его квадрат.</li>

    user_number = input("Enter int number: ")
    print(int(user_number) ** 2)
<li>Напишите программу, которая выводит на экран таблицу умножения на
число 5.</li>

    num_for_table = 5
    multiplication_table = {}
    for num in range(0, 11):
        multiplication_table[num] = num * num_for_table
    print(multiplication_table)
***
    num_for_table = 5
    multiplication_table = {}
    for num in range(0, 11):
        key = f"{num} * {num_for_table}"
        value = num * num_for_table
        multiplication_table[key] = value
    print(multiplication_table)
<li>Напишите программу, которая запрашивает у пользователя два числа и
выводит на экран их среднее арифметическое</li>
</ol>

    print(int(input("Enter second number: ")) * int(input("Enter first number: ")) / 2)
Весь код домашки:

    print("Привет, мир!")
    print(2+2)
    
    user_name = input("My name is: ")
    print(f"Привет, {user_name}!")
    
    print(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
    
    for num in range(1,11):
        print(num)
    
    print(list(range(1,11)))
    
    user_age = input("My age: ")
    print(f"Your age: {user_age}")
    
    first_number = input("Enter firts number: ")
    second_number = input("Enter second number: ")
    print(int(first_number) * int(second_number))
    
    user_word = input("Enter your word: ")
    print(user_word[0])
    
    user_number = input("Enter int number: ")
    print(int(user_number) ** 2)
    
    num_for_table = 5
    multiplication_table = {}
    for num in range(0, 11):
        key = f"{num} * {num_for_table}"
        value = num * num_for_table
        multiplication_table[key] = value
    print(multiplication_table)
    
    print(int(input("Enter second number: ")) + int(input("Enter first number: ")) / 2)
Запуск программы:

    cd python
    python3 hw25.py
Вывод терминала:

    Привет, мир!
    4
    My name is: Hurin
    Привет, Hurin!
    1 2 3 4 5 6 7 8 9 10
    1
    2
    3
    4
    5
    6
    7
    8
    9
    10
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    My age: 28
    Your age: 28
    Enter firts number: 6
    Enter second number: 7
    42
    Enter your word: Turin
    T
    Enter int number: 4
    16
    {'0 * 5': 0, '1 * 5': 5, '2 * 5': 10, '3 * 5': 15, '4 * 5': 20, '5 * 5': 25, '6 * 5': 30, '7 * 5': 35, '8 * 5': 40, '9 * 5': 45, '10 * 5': 50}
    Enter second number: 8
    Enter first number: 9
    8.5
