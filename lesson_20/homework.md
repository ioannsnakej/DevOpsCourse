<h3>Цель: настроить Ansible окружение на вашей машине, чтобы вы могли
использовать его для автоматизации управления конфигурацией и
развертывания приложений на удаленных серверах.</h3>
<h2>Задание: Настройка Ansible окружения</h2>
host- 192.168.56.1

Server1- 192.168.56.3
Server2- 192.168.56.4
Server3- 192.168.56.5
<h4>Шаги:</h4>
1. Установите Ansible на вашу локальную машину. В зависимости от
операционной системы, которую вы используете, установка может
отличаться. Инструкции по установке Ansible можно найти на официальном
сайте: https://docs.ansible.com/ansible/latest/installation_guide/index.html

Ansible был установлен в предыдущем занятии. Просто убедился в этом:

    ansible --version

2. Создайте инвентарный файл, который содержит информацию о
серверах, которые вы планируете управлять с помощью Ansible.

Перешел в папку ansible и поправил hosts.txt с прошлого занятия:

    cd ansible/
    vim hosts
***
    [ubuntu]
    server1 ansible_host=192.168.56.3 ansible_user=devops ansible_ssh_private_key_file=/home/ivan/.ssh/id_ed25519 
    server2 ansible_host=192.168.56.4 ansible_user=devops ansible_ssh_private_key_file=/home/ivan/.ssh/id_ed25519 
    server3 ansible_host=192.168.56.5 ansible_user=devops ansible_ssh_private_key_file=/home/ivan/.ssh/id_ed25519
Удалил старые rsa ключи:

    sudo rm ~/.ssh/id_rs*

Сгенирировал новые ed25519 ssh-ключи:

    ssh-keygen -t ed25519

Написал минискрипт для копирования ключа на сервера и запустил его:

    vim ssh_copy.sh
***
    #!/bin/bash
    if [ $# -eq 0 ]; then
      echo "Usage: $0 ip_server"
      exit 1
    fi
    
    SSHPATH=~/.ssh/id_ed25519.pub
    USER=devops
    
    for ip in "$@"; do
      ssh-copy-id -i ${SSHPATH} ${USER}@${ip}
    done
***
    bash ssh_copy.sh 192.168.56.3 192.168.56.4 192.168.56.5

3. Создайте файл конфигурации Ansible, который будет содержать
глобальные настройки Ansible. Файл конфигурации Ansible может быть
расположен в нескольких местах, включая каталоги /etc/ansible и ~/.ansible/.

Правлю ansible.cfg с прошлого занятия:

    vim ansible.cfg
***
    [defaults]
    inventory         = ./hosts
    host_key_checking = false
    remote_user       = devops
    private_key_file  = /home/ivan/.ssh/id_ed25519

На всякий случай проверяю:

    ansible all -m ping

4. Создайте простой плейбук Ansible, который будет выполнять простую
задачу на одном из ваших серверов. Например, вы можете создать плейбук,
который устанавливает пакеты на сервере.

Создаю простой плейбок из примера:

    vim playbook.yml
***
     ---
      - name: Install packages
         hosts: all
         become: true
         tasks:
           - name: Install Nginx
             apt:
               name: nginx
               state: present   
Запускаю:

    ansible-playbook playbook.yml
