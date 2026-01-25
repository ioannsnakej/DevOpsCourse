for GitLab CI needs:
1. [Repo](https://gitlab.com/khodyrev-ivan/diplom)
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

Пишим наш [.gitlab-ci.yml](https://gitlab.com/khodyrev-ivan/diplom/-/blob/main/.gitlab-ci.yml?ref_type=heads):

    image: docker:28.4.0
    
    .ssh_setup:
      before_script:
        - apk update && apk add openssh-client rsync bash
        - eval "$(ssh-agent -s)"
        - mkdir -p ~/.ssh
        - chmod 700 ~/.ssh
        - cat "$SSH_KEY" | ssh-add -
        - echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config
    
    stages:
      - lint
      - build
      - test
      - prepare
      - deploy
      - notify
    
    variables:
      REGISTRY: https://index.docker.io/v1/
      DOCKER_REPO: ivankhodyrev/bookstore-app
      DOCKER_USER: ivankhodyrev
      TAG: ""
      SSH_OPT: -o StrictHostKeyChecking=no
      SSH_USER: gitlab-runner
      HOST: 192.168.56.6
      PRJ_DIR: /var/www/bookstore-app
      GIT_URL: https://gitlab.com/khodyrev-ivan/diplom.git
      API_URL: https://api.telegram.org/bot$TELEGRAM_BOT_TOKEN/sendMessage
      TELEGRAM_CHAT_ID: 940670214
      TIMEOUT: 5
      IMAGE_TAG: ${DOCKER_REPO}:${CI_PIPELINE_ID}
      DB_USER: bookstoreuser
      DB_NAME: bookstore
      DB_HOST: db
    
    linter-app:job:
      stage: lint
      image: python:3.12-slim
      before_script:
        - pip install pylint flask psycopg2-binary python-dotenv
      script:
        - pylint ./app/*.py
      allow_failure: true
      tags:
        - runner
    
    linter-docker:job:
      stage: lint
      image: hadolint/hadolint:latest-debian
      script:
        - hadolint Dockerfile
      allow_failure: true
      tags:
        - runner
    
    build:job:
      stage: build
      image:
        name: gcr.io/kaniko-project/executor:v1.9.0-debug
        entrypoint: [""]
      script:
        - echo "{\"auths\":{\"$REGISTRY\":{\"username\":\"$DOCKER_USER\",\"password\":\"$DOCKER_TOKEN\"}}}" > /kaniko/.docker/config.json
        - /kaniko/executor --context=$CI_PROJECT_DIR --dockerfile=$CI_PROJECT_DIR/Dockerfile --destination=${IMAGE_TAG}
      tags:
        - runner
    
    test:job:
      stage: test
      needs: ["build:job"]
      image: 
        name: ${IMAGE_TAG}
        entrypoint: [""] 
      variables:
        POSTGRES_DB: mydb
        POSTGRES_USER: postgres
        POSTGRES_PASSWORD: postgres
        POSTGRES_AUTH_METHOD: trust
      services:
        - name: postgres:16
          alias: db
      before_script:
        - pip install -r ./app/requirements.txt
      script:
        - pytest -v --disable-warnings --maxfail=1 ./app/tests_app.py | tee bookstore-tests_${CI_PIPELINE_ID}.log
      artifacts:
        paths:
          - bookstore-tests_${CI_PIPELINE_ID}.log
      tags:
        - runner
    
    prepare:job:
      stage: prepare
      extends: .ssh_setup
      script:
        - |
          if [ -f .env ] && grep 'DB_PASS' .env; then
            DB_PASS=$(grep 'DB_PASS' .env | cut -d= -f2 | tr -d '\"')
          else
            DB_PASS=$(openssl rand -base64 9 | tr -dc 'A-Za-z0-9' | head -c12)
          fi
          ssh ${SSH_USER}@${HOST} "
            test -d /var/www || sudo mkdir -p /var/www;
            sudo chown -R gitlab-runner:gitlab-runner /var/www;
            test -d ${PRJ_DIR} || git clone ${GIT_URL} ${PRJ_DIR};
            cd ${PRJ_DIR};
            git checkout main;
            git pull;
            cd ${PRJ_DIR};
            {
              echo "APP_IMAGE=${IMAGE_TAG}"
              echo "DB_USER=${DB_USER}"
              echo "DB_NAME=${DB_NAME}"
              echo "DB_HOST=${DB_HOST}"
              echo "DB_PASS=${DB_PASS}"
            } > .env
          "
      tags:
        - runner
    
    
    deploy-compose:
      stage: deploy
      needs: ["prepare:job"]
      extends: .ssh_setup
      script:
        - |
          ssh ${SSH_USER}@${HOST} "cd ${PRJ_DIR}; 
          sudo docker compose up -d"
      tags:
        - runner
    
    notify-tg:
      stage: notify
      image: curlimages/curl
      before_script: |
          export URL=${API_URL}
          export TEXT="Deploy Project: $CI_PROJECT_NAME%0ABranch: $CI_COMMIT_REF_NAME%0APipeline: $CI_PIPELINE_URL%0AUser: $GITLAB_USER_NAME"
      script:
        - curl -s --max-time $TIMEOUT -d "chat_id=$TELEGRAM_CHAT_ID&disable_web_page_preview=1&text=$TEXT" $URL > /dev/null
      tags:
        - runner

Скриншоты:

Сборка:

![notify](/lesson_31/screens/gitlabci_build.png)

Уведомление в тг:

![notify](/lesson_31/screens/notify_tg.png)

Приложение в браузере:

![notify](/lesson_31/screens/app_in_browser.png)
