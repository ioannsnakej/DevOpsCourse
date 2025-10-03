<h2>Задание 1</h2>
host - 192.168.56.1
server - 192.168.56.5

Обновил список пакетов:

    sudo apt update
Установил пакет software-properties-common, который позволит добавлять репозитории:

    sudo apt install software-properties-common
Добавил репозиторий Ansible PPA:

    sudo apt-add-repository ppa:ansible/ansible
Обновил список пакетов:

    sudo apt update
Установил Ansible:

    sudo apt install ansible
<h2>Задание 2</h2>
Сгенерировал ssh-ключ и скопировал на server:

    ssh-keygen -t rsa -b 4096
    ssh-copy-id devops@192.168.56.5

  <img width="1218" height="785" alt="image" src="https://github.com/user-attachments/assets/f5c936c9-6431-45e5-851c-c7fa0e9ab07d" />
<h2>Задание 3</h2>
Создал директорию ansible и перешел в нее:

    mkdir ansible
    cd ansible
Создал файл hosts.txt:

    nano hosts.txt
<img width="1230" height="195" alt="image" src="https://github.com/user-attachments/assets/4aeb678f-11b7-4005-9f97-3349655480a6" />
<img width="1823" height="293" alt="image" src="https://github.com/user-attachments/assets/e3fb925d-9680-4444-88c5-46fb97648891" />

Создал конфиг:

    nano ansible.cfg
<img width="861" height="237" alt="image" src="https://github.com/user-attachments/assets/e15526ed-bb27-40be-a3f6-8ae632c6537e" />

Проверил:

    ansible all -m ping

<img width="1806" height="267" alt="image" src="https://github.com/user-attachments/assets/733ce2a2-6b93-480e-a519-4339ab31bb6c" />
