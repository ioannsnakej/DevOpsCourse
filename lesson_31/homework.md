for GitLab CI needs:
1. [Repo](https://gitlab.com/khodyrev-ivan/diplom/-/tree/main?ref_type=heads)
2. Runner
3. .gitlab-ci.yml

Создаем машину для gitlab runner

Устанавливаем gitlab runner по [инструкции](https://docs.gitlab.com/runner/install/linux-repository/) на нашу новую машину:

    curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
***
    sudo apt install gitlab-runner
Далее его неообходимо [зарегистрировать](https://docs.gitlab.com/runner/register/) - т.е. подключить к GitLab

GitLab->Repo->Settings->CI/CD->Runners->Create project runner

Задаем имя тега, жмем create, выбираем ОС Linux, копируем команду и запускаем ее на нашей машине с runnerом

    gitlab-runner register  
    --url https://gitlab.com  
    --token glrt-lTnR0IL2v8cw-MXwYaxDo286MQpwOjE5cDd0dAp0OjMKdTpoNHhochg.01.1j15tpb12
![notify](/lesson_31/screens/gitlab-runner_register.png)
Копируем конфиг в /home/gitlab-runner/.gitlab-runner:

    sudo mkdir /home/gitlab-runner/.gitlab-runner
    sudo cp /home/devops/.gitlab-runner/config.toml /home/gitlab-runner/.gitlab-runner/config.toml
    sudo chown -R gitlab-runner:gitlab-runner /home/gitlab-runner
Правим конфиг:

    privileged = true
    volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"] -
    монтируем докер сокет в контейнер, иначе джобы не запустятся
Пишем systemd unit:

    /etc/systemd/system/diplom-runner.service
***
    [Unit]
    Description="Docker gitlab-runner"
    
    [Service]
    Type=simple
    User=gitlab-runner
    Group=gitlab-runner
    WorkingDirectory=/home/gitlab-runner
    ExecStart=/usr/bin/gitlab-runner run --working-directory /home/gitlab-runner --config /home/gitlab-runner/.gitlab-runner/config.toml
    Restart=always
    
    [Install]
    WantedBy=multi-user.target
***
    sudo systemctl daemon-reload
    sudo systemctl start diplom-runner.service
    sudo systemctl status diplom-runner.service
    sudo systemctl enable diplom-runner.service
Далее нам надо на runner сгенерировать ключи и скопировать публичный ключ в файл пользователя gitlab-runner .ssh/authorized_keys на целевую машину:

    sudo su - gitlab-runner
    ssh-keygen -t ed25519
    cat .ssh/id_ed25519.pub

Предварительная настройка пользователя на целевой машине:

    sudo adduser gitlab-runner
    #в файл /etc/sudoers прописываем строку gitlab-runner ALL=(ALL:ALL) NOPASSWD:ALL
    sudo visudo
    su - gitlab-runner
    mkdir .ssh
    #вставляем скопированный публичный ключ в .ssh/authorized_keys
    vim .ssh/authorized_keys

Далее необходимо в gitlab добавить приватный ключ пользователя gitlab-runner с машины с раннером:
GitLab->Repo->Settings->CI/CD->Variables->add variable:
Type:File
Visibility: Visible
KEY:SSH_KEY
VALUE: /home/gitlab-runner/.ssh/id_ed25519
