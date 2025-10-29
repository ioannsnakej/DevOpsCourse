<h2>Цель: Изучение основ DevOps, применение Python в автоматизации
процессов DevOps, использование библиотек Python для обработки
данных, а также изучение методологии CI/CD и инструментов,
используемых в этом процессе.</h2>
Задание:
<ol>
<li>Создайте учетную запись или настройте интеграцию между выбранным
инструментом чата и системой контроля версий (например, GitLab или
GitHub).</li>
<li>Создайте простой пайплайн CI/CD с использованием выбранного
инструмента контроля версий и интеграции с чатом. Например,
настройте оповещения о сбоях сборок и автоматическое
восстановление после сбоя.</li>
<li>Создайте пример использования ChatOps в инфраструктурном коде с
использованием инструмента такого как Terraform, например,
настройте оповещения о создании/обновлении ресурсов
инфраструктуры в чате.</li>
<li>Создайте пример использования ChatOps в автоматизации
развертывания приложений с использованием инструмента такого как
Ansible, например, настройте оповещения о успешном развертывании
приложения в чате.</li>
<li>Продемонстрируйте вашу настройку ChatOps на практике, показав
результаты интеграции в чате и автоматические действия, выполняемые
на основе оповещений.</li>
</ol>

<h3>Выбор приложения</h3>
За основу беру проект из домашки по docker compose - https://github.com/ioannsnakej/bookstore/tree/main
<h3>Установка и первоначальная настройка Jenkins</h3>
Устанавливаю Java и Jenkins по инструкции - https://www.jenkins.io/doc/book/installing/linux/#debianubuntu
<h3>Написание Jenkinsfile</h3>
На своей ВМ:

    cd bookstore
    vim Jenkinsfile
Пишем в корне проекта Jenkinsfile:

    pipeline {
      agent any
      parameters {
        booleanParam(name: 'RUN_TEST', defaultValue: 'true') 
        string(name: 'TAG', defaultValue: 'latest')
        gitParameter(type: 'PT-BRANCH', name: 'REVISION', branchFilter: 'origin/(.*)', defaultValue: 'main', selectedValue: 'DEFAULT')
      }
    
      environment {
        GIT_NAME="ivankhodyrev"
        PRJ_NAME="bookstore"
        GIT_URL="https://github.com/ioannsnakej/bookstore.git"
        TOKEN=credentials('docker_token')
      }
    
      stages {
        stage('Clone repo') {
          steps {
            script {
              sh """
                if [ -d ${env.PRJ_NAME}/.git ]; then
                  echo "Repo exists. Updating..."
                  cd ${env.PRJ_NAME}
                  git checkout ${params.REVISION}
                  git pull
                else
                  echo "Clone repo"
                  git clone ${env.GIT_URL}
                fi
              """
            }
          }
        }
    
        stage('Build image') {
          steps {
            script {
              sh """
                cd ${env.PRJ_NAME}
                docker build -t ${env.GIT_NAME}/${env.PRJ_NAME}:${params.TAG} .
                docker login -u ${env.GIT_NAME} -p ${env.TOKEN}
                docker push ${env.GIT_NAME}/${env.PRJ_NAME}:${params.TAG}
                docker logout
              """
            }
          }
        }
    
        stage('Run test') {
          when {
            expression { params.RUN_TESTS }
          }
          steps {
            script {
              sh """
                cd ${ennv.PRJ_NAME}
                echo "App ready"
              """
            }
          }
        }
      }
        
    }
***
    git status
    git add .
    git commit -m "Add Jenkinsfile"
    git push origin main
<h3>Настройки личного Docker Hub</h3>
Регистрируемся на dockerhub - https://hub.docker.com/

Добавляем ТОКЕН jenkins-pipeline - https://app.docker.com/accounts/ivankhodyrev/settings/personal-access-tokens 

Копируем созданный ТОКЕН. Добавляем его в Jenkins credentials: kind - secret text, secret - наш jenkins-pipeline, id - docker_token, description - docker_token.

Это необходимо, чтобы jenkins мог пушить наш образ в удаленный docker hub.
<h3>Добавление ключа Jenkins в GitHub</h3>

    sudo =u jenkins -i
    ssh-keygen -t ed25519
    cat ~/.ssh/id_ed25519.pub
Копируем вывод. Переходим в наш проект в GitHub: https://github.com/ioannsnakej/bookstore/ - settings - deploy keys. И добавляем наш ключ.
<h3>Создание item в Jenkins</h3>
В браузере переходим на https://github.com/ioannsnakej/bookstore/

Нажимаем "Создать item"

вводим имя- я выбрал bookstore-build

выбираем тип Pipeline

В открывшемся окне заполняем примерно так:

![addpipeline](/lesson_29/screenshots/addpipeline.png)

У поля "Credentials" нажимаем "+Add" - "Jenkins":

Заполняем примерно так:

![addgithubssh](/lesson_29/screenshots/addgithubssh.png)

![addgithubssh_key](/lesson_29/screenshots/addgithubssh_key.png)

после этих манипуляций в поле "Credentials" выбираем созданное "git (github_ssh)" и сохраняем.
<h3>Запуск и проверка</h3>
Переходим в нашу сборку http://localhost:8080/job/bookstore-build/ и нажимаем "Собрать с параметрами"

![run](/lesson_29/screenshots/run.png)

Успех:

![done](/lesson_29/screenshots/done.png)

Репозиторий на dockerhub тоже создался - https://hub.docker.com/repository/docker/ivankhodyrev/bookstore/general

Добавляю в stage('Build') скрипт:

    script {
               sh """
                  curl -s -X POST https://api.telegram.org/bot${env.TG_BOT_TOKEN}/sendMessage \
                  -d chat_id=${env.TG_CHAT_ID} \
                  -d parse_mode=Markdown \
                  -d text="🏃Начата сборка проекта ${env.PRJ_NAME}"
                """
            }
Добавляю в конце Jenkinsfile раздел post:

     post {
        success {
          script {
            sh """
              curl -s -X POST https://api.telegram.org/bot${env.TG_BOT_TOKEN}/sendMessage \
              -d chat_id=${env.TG_CHAT_ID} \
              -d parse_mode=Markdown \
              -d text="✅Success! Проект:${env.PRJ_NAME}"
            """
          }
        }
    
        failure {
          script {
            sh """
                  curl -s -X POST https://api.telegram.org/bot${env.TG_BOT_TOKEN}/sendMessage \
                  -d chat_id=${env.TG_CHAT_ID} \
                  -d parse_mode=Markdown \
                  -d text="❌Failed! Проект:${env.PRJ_NAME}"
                """
          }
        }
      }
  Запускаю и смотрю уведомления в ТГ:

  ![notify](/lesson_29/screenshots/notify.png)


