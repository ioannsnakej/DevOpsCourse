<h2>Задание:</h2>
<ol>
<li>Написать скрипт, который принимает на вход список IP-адресов и
проверяет их доступность с помощью ping-запросов. Результаты
проверки сохраняются в отдельный файл.</li>

    from ping3 import verbose_ping
    from contextlib import redirect_stdout
    file_input = open('hosts', 'r')
    file_out = open('ping_out', 'w')
    
    with file_input:
        hosts = file_input.read().splitlines()
        
    with file_out, redirect_stdout(file_out):
        for host in hosts:
            print(f"Host: {host}")
            print(verbose_ping(host))
    file_input.close()
    file_out.close()
Содержимое hosts:

    192.168.56.2
    192.168.56.3
    192.168.56.4
    192.168.56.5
    ya.ru
    google.com
Запуск:

    sudo python3 try_ping.py
Содержимое файла ping_out после выполнения:
    
    Host: 192.168.56.2
    ping '192.168.56.2' ... Error
    ping '192.168.56.2' ... Error
    ping '192.168.56.2' ... Error
    ping '192.168.56.2' ... Error
    None
    Host: 192.168.56.3
    ping '192.168.56.3' ... Error
    ping '192.168.56.3' ... Error
    ping '192.168.56.3' ... Error
    ping '192.168.56.3' ... Error
    None
    Host: 192.168.56.4
    ping '192.168.56.4' ... Error
    ping '192.168.56.4' ... Error
    ping '192.168.56.4' ... Error
    ping '192.168.56.4' ... Error
    None
    Host: 192.168.56.5
    ping '192.168.56.5' ... 1ms
    ping '192.168.56.5' ... 0ms
    ping '192.168.56.5' ... 0ms
    ping '192.168.56.5' ... 0ms
    None
    Host: ya.ru
    ping 'ya.ru' ... 54ms
    ping 'ya.ru' ... 54ms
    ping 'ya.ru' ... 50ms
    ping 'ya.ru' ... 54ms
    None
    Host: google.com
    ping 'google.com' ... 63ms
    ping 'google.com' ... 60ms
    ping 'google.com' ... 63ms
    ping 'google.com' ... 63ms
    None

<li>Написать скрипт, который принимает на вход строку и выводит на
экран количество букв в верхнем регистре, количество букв в нижнем
регистре, количество цифр и количество символов пунктуации.</li>
<li>Написать скрипт, который принимает на вход два списка и выводит на
экран элементы, которые присутствуют в обоих списках.</li>

    first_list = ["Ivan", "khodyrev", 28, "Novosibirsk"]
    second_list = ["ivan", "khodyrev", 28, "nsk"]
    
    similar_list=[]
    
    for element in first_list:
        if element in second_list:
            similar_list.append(element)
    
    print(similar_list)

Вывод:

    ['khodyrev', 28]
<li>Написать скрипт, который принимает на вход массив чисел и сортирует
его в порядке убывания.</li>
<li>Написать скрипт, который принимает на вход кортеж и проверяет, все
ли его элементы являются уникальными.</li>
<li>Написать скрипт, который принимает на вход список файлов и находит
файлы, имена которых содержат определенную подстроку.</li>
<li>Написать скрипт, который принимает на вход две строки и выводит на
экран все символы, которые встречаются в обеих строках.</li>
<li>Написать скрипт, который принимает на вход список чисел и находит
медиану этого списка.</li>
<li>Написать скрипт, который принимает на вход строку и заменяет в ней
все гласные буквы на символ "-".</li>
<li>Написать скрипт, который принимает на вход два списка и находит
общие элементы, а затем создает новый список, содержащий только
уникальные элементы</li>
</ol>

