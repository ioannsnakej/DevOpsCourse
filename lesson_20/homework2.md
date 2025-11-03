Роль для настройки jenkins-агента на машине 192.168.56.5

Создаю структуру и инициализирую роль:

    cd ansible/
    mkdir roles
    cd roles/
    ansible-galaxy init setup-jenkins-agent

Общая структура в итоге выглядит следующим образом:

    .
    ├── ansible.cfg
    ├── hosts
    ├── playbooks
    │   └── setup-jenkins-agent.yml
    └── roles
        └── setup-jenkins-agent
            ├── defaults
            │   └── main.yml
            ├── files
            │   └── jenkins_key.pub
            ├── handlers
            │   └── main.yml
            ├── meta
            │   └── main.yml
            ├── README.md
            ├── tasks
            │   ├── create_user.yml
            │   ├── install_docker.yml
            │   ├── install_java.yml
            │   ├── main.yml
            │   ├── prepare.yml
            │   └── setup_agent.yml
            ├── templates
            ├── tests
            │   ├── inventory
            │   └── test.yml
            └── vars
                └── main.yml

Начинаем с простого, пишем файлы:

<ol>
<li>hosts:
    
    [jenkins-agent]
    192.168.56.5 ansible_user=devops ansible_ssh_private_key_file=~/.ssh/id_ed25519
</li>
<li>ansible.cfg:

    [defaults]
    inventory         = ./hosts
    host_key_checking = false
    remote_user       = devops
    roles_path = ./roles
</li>
</ol>

Далее зададим переменную для установки java:

    vim roles/setup-jenkins-agent/vars/main.yml 
***

    #SPDX-License-Identifier: MIT-0
    ---
    # vars file for setup-jenkins-agent
    java_package: openjdk-21-jre
Далее пишем таски:

<ol>
<li>
instal_java - устанавливает java на нашего агента, без нее не выполнится pipeline
    
    vim roles/setup-jenkins-agent/tasks/install_java.yml 
***

    ---
    - name: Apt update
      apt:
        update_cache: yes
    
    - name: Install java
      apt:
        name: "{{ java_package }}"
        state: present
        update_cache: yes
</li>
<li>
create_user - добавляем пользователя jenkins, который будет выполнять pipeline

    vim roles/setup-jenkins-agent/tasks/create_user.yml 
***

    ---
    - name: Create jenkins user
      user:
        name: jenkins
        shell: /bin/bash
        create_home: yes
        groups: docker
        append: yes
</li>
<li>
install_docker - устанавливаем docker, он нам понадобится для сборки

    vim roles/setup-jenkins-agent/tasks/install_docker.yml
***

    ---
    - name: Apt update
      apt:
        update_cache: yes
    
    - name: Install package for Docker
      apt:
        name:
          - ca-certificates
          - curl
          - apt-transport-https
          - software-properties-common
          - lsb-release
        state: present
        update_cache: yes    
    
    - name: Add Docker repo key
      shell: |
        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/docker.gpg
      args:
        creates: /etc/apt/trusted.gpg.d/docker.gpg
    
    - name: Add Docker repo
      apt_repository:
        repo: deb https://download.docker.com/linux/ubuntu {{ ansible_distribution_release }} stable
        state: present
    
    - name: Install Docker and compose
      apt:
        name:
          - docker-ce
          - docker-ce-cli
          - containerd.io
          - docker-compose-plugin
        state: present
        update_cache: yes
      notify:
        - Enable docker
        - Restart docker 
</li>
<li>
setup_agent - роль добавляет публичный ключ jenkins с master в файл authorized_key на агенте

    vim roles/setup-jenkins-agent/tasks/setup_agent.yml
***

    ---
    - name: Add pub-key jenkins
      authorized_key:
        user: jenkins
        state: present
        key: "{{ lookup('file', '{{ role_path }}/files/jenkins_key.pub') }}"
</li>
<li>
prepare - роль устанавливает пакет gnupg2, чтобы в во время исполнения роли install_docker у нас добавился gpg-ключ (вообще, можно было и в роли install_docker прописать, но я в параллель с персмотром урока 20 делал, там была показана такая практика)

    vim roles/setup-jenkins-agent/tasks/prepare.yml
***

    ---
    - name: Install additional packages
      package:
        name: gnupg2
</li>
<li>
main - здесь мы просто добавляем все наши таски

    vim roles/setup-jenkins-agent/tasks/main.yml 
***

    #SPDX-License-Identifier: MIT-0
    ---
    # tasks file for setup-jenkins-agent
    - include_tasks: prepare.yml
    - include_tasks: install_java.yml
    - include_tasks: install_docker.yml
    - include_tasks: create_user.yml
    - include_tasks: setup_agent.yml
</li>
</ol>

Чтобs наша таска setup_agent отработала, создаем file в который копируем наш публичный ключ пользователя jenkins:

    sudo cp /var/lib/jenkins/.ssh/id_ed25519.pub roles/setup-jenkins-agent/files/jenkins_key.pub

Добавляем таски Restart и Enable в handlers/main.yml:

    vim roles/setup-jenkins-agent/handlers/main.yml 
***

    #SPDX-License-Identifier: MIT-0
    ---
    # handlers file for setup-jenkins-agent
    - name: Restart docker
      systemd:
        name: docker
        state: restarted
    
    - name: Enable docker
      systemd:
        name: docker
        state: enabled
Пишем наш playbok:

    mkdir playbooks
    vim playbooks/setup-jenkins-agent.yml
***

    - hosts: all
      become: true
      roles:
       - ../roles/setup-jenkins-agent
Запускаем:

    ansible-playbook -i hosts playbooks/setup-jenkins-agent.yml --diff
