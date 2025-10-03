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
  <img width="1147" height="229" alt="image" src="https://github.com/user-attachments/assets/e90a92c4-631c-4512-9f29-3d6ab1384b59" />

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

  <img width="1226" height="521" alt="image" src="https://github.com/user-attachments/assets/1c643225-c447-4e25-a46c-d5e51d6d7d23" />
  <img width="1204" height="215" alt="image" src="https://github.com/user-attachments/assets/8f5eccad-7255-47c3-9d1e-301cfbd2c615" />
!Так как произошла заминочка с запуском 192.168.56.4, пришлось запустить скрипт повторно с одним аргументом.

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
  <img width="1807" height="622" alt="image" src="https://github.com/user-attachments/assets/283e2b92-6aef-473a-a487-d5b785e8b5f2" />

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

<img width="1877" height="484" alt="image" src="https://github.com/user-attachments/assets/1e495d3a-8bde-4777-8d69-b794dfd65942" />
<img width="1877" height="476" alt="image" src="https://github.com/user-attachments/assets/c4823cbe-148b-415d-bddf-908c1a61dd3c" />

!На третьем сервере не отработало, так понимаю, потому что я после создания машины не стал настраивать права пользователю devops

Накинул прав на третьем сервере devops и запустил повторно:

<img width="1831" height="880" alt="image" src="https://github.com/user-attachments/assets/f227d5e8-a1ed-4524-9496-62f73b5f3a91" />
