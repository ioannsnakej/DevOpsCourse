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
***

    [ubuntu]
    server1 ansible_host=192.168.56.3 ansible_user=devops ansible_ssh_private_key_file=/home/ivan/.ssh/id_ed25519 
    server2 ansible_host=192.168.56.4 ansible_user=devops ansible_ssh_private_key_file=/home/ivan/.ssh/id_ed25519 
    server3 ansible_host=192.168.56.5 ansible_user=devops ansible_ssh_private_key_file=/home/ivan/.ssh/id_ed25519
Создал конфиг:

    nano ansible.cfg
***

    [defaults]
    inventory         = ./hosts
    host_key_checking = false
    remote_user       = devops
    private_key_file  = /home/ivan/.ssh/id_ed25519
Проверил:

    ansible all -m ping
