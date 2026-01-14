<h2>Цель: Изучение и практическое применение Jenkins pipeline, применение
Groovy и работа с DSL job.</h2>
<h3>Задание:</h3>
<ol>
  <li>Создайте новый Jenkins Pipeline, используя декларативный синтаксис.</li>
  <li>Определите агента для вашего Pipeline, который будет выполнять вашу
  сборку, тестирование и развертывание.</li>
  <li>Добавьте несколько этапов (stages) в ваш Pipeline, таких как "Build"
  (сборка), "Test" (тестирование) и "Deploy" (развертывание).</li>
  <li>В каждом этапе определите шаги (steps), которые должны быть
  выполнены, используя соответствующие Jenkins-плагины или команды,
  такие как sh для выполнения Shell-скриптов или git для работы с
  репозиторием Git.</li>
  <li>Добавьте условия выполнения (when) для определенных этапов на
  основе веток, изменений или других параметров, используя синтаксис
  Groovy и объект params.</li>
  <li>Добавьте использование параметров (parameters) в вашем Pipeline,
  таких как окружение (environment) или другие настраиваемые значения,
  и используйте их в шагах вашего Pipeline.</li>
  <li>Добавьте обработку ошибок (error handling) в ваш Pipeline, такую как
  проверка статуса выполнения шагов и обработка ошибок в случае
  возникновения проблем.</li>
  <li>Проверьте работу вашего Pipeline, запустив его на Jenkins и отслеживая
  выполнение этапов и шагов в интерфейсе Jenkins.</li>
  <li>Документируйте ваш Pipeline, описав его структуру, шаги, условия
  выполнения и параметры в документе или файле README.</li>
  <li>Напишите Groovy-скрипт, который автоматизирует процесс сборки и
  развертывания веб-приложения с использованием инструментов Jenkins
  и Docker. Требования:</li>
    <ol>
    <li type='a'>Скрипт должен быть написан на Groovy и запускаться в
    окружении Jenkins.</li>
    <li type='a'>Скрипт должен выполнять следующие шаги: Клонирование
    репозитория с веб-приложением из Git.</li>
    <li type='a'>Сборка приложения с помощью Gradle или Maven. Создание
    Docker-образа приложения.</li>
    <li type='a'>Запуск Docker-контейнера с созданным образом на сервере.
    Проверка доступности веб-приложения после развертывания.</li>
    <li type='a'>Скрипт должен обрабатывать ошибки и выводить
    соответствующие сообщения в случае возникновения проблем.</li>
    </ol>
  <li>Дополнительное задание (необязательное): Расширьте скрипт
  функциональностью удаления предыдущей версии Docker-контейнера
  перед развертыванием новой версии.</li>
  <li>Создание и выполнение задания на автоматическую генерацию отчета.
  Описание: Вы работаете в компании, занимающейся анализом данных,
  и вам необходимо автоматизировать процесс генерации отчетов на
  основе данных, хранящихся в базе данных. Для этого вы должны
  создать и выполнить задание на использование DSL для генерации
  отчета. Требования:</li>
    <ol>
    <li type='a'>Создайте DSL на одном из языков программирования (например,
    Python, Ruby, SQL), который позволит описать параметры отчета,
    такие как тип отчета, период, фильтры и другие необходимые
    настройки.</li>
    <li type='a'>Используя созданный DSL, создайте задание на генерацию
    отчета, указав необходимые параметры отчета.</li>
    <li type='a'>Выполните созданное задание и получите результаты генерации
    отчета.</li>
    <li type='a'>Обработайте результаты выполнения задания, например,
    выведите на экран информацию о сгенерированном отчете или
    сохраните его в файл.</li>
    <li type='a'>Добавьте обработку возможных ошибок, таких как отсутствие
    данных в базе данных или неправильные параметры отчета.</li>
    </ol>
</ol>

<h3>Подключаем jenkins агнета:</h3>
1. Запускаем в virtual-box нашу ВМ-агента в фоновом режиме:
  
  ![notify](/lesson_30/screenshots/start-vm.png)

2. Выполняем на основном хосте наш [playbook](https://github.com/ioannsnakej/ansible-roles-setup-jenkins/blob/main/playbooks/setup-jenkins-agent.yml) с ролью [setup-jenkins-agent](https://github.com/ioannsnakej/ansible-roles-setup-jenkins/tree/main/roles/setup-jenkins-agent)
3. В настройках нашего репозитория [bookstore](https://github.com/ioannsnakej/bookstore/) добавляем новый Deploy

       key title: jenkins-agent
       key: cat /home/ivan/agent_keys/192.168.56.5-jenkins.pub
>[!NOTE]
>ключ агента, который создался в результате выполнения таски [setup_agent](https://github.com/ioannsnakej/ansible-roles-setup-jenkins/blob/main/roles/setup-jenkins-agent/tasks/setup_agent.yml)
4. На нашем master открываем в браузере jenkins (http://localhost:8080/)
5. Добавляем jenkins credential:  Переходим в "Настроить Jenkins"->"Credentials"->"Global"->"Add Credentials"
>[!NOTE]
>это приватный ключ пользователя jenkins, лежит в /var/lib/jenkins/.ssh/id_ed25519

   ![notify](/lesson_30/screenshots/jenkins-credential.png)
6. Переходим в "Настроить Jenkins"->"Nodes"->"New Node"
>[!NOTE]
>Количество процессов-исполнителей: "2" = количеству числа ядер<br>
>Удалённая корневая директория: "/home/jenkins" - удаленная корневая директория jenkins
>Метки: "docker"<br>
>Использование: "Собирать только проекты с метками, совпадающими с этим узлом" - чтобы Jenkins не насовал чужих задач<br>
>Способ запуска: "Launch agents via SSH", Host: 192.168.56.5, Credentials: "jenkins (jenkins_ssh)"<br>
>Host Key Verification Strategy: "Non verifying Verification Strategy"

   ![notify](/lesson_30/screenshots/new-node.png)

   ![notify](/lesson_30/screenshots/new-node1.png)

   ![notify](/lesson_30/screenshots/new-node2.png)

7. Отключаем агента master. Надо открыть настройки и выставить "Количество проссеров-исполнителей": 0

    ![notify](/lesson_30/screenshots/master_off.png)

<h3>Подключаем jenkins таргет:</h3>

Аналогично, как подключали агента, так настраиваем и подключаем таргет. Только используем [playbook for target](https://github.com/ioannsnakej/ansible-roles-setup-jenkins/blob/main/playbooks/setup-jenkins-target.yml) с ролью [setup-jenkins-terget](https://github.com/ioannsnakej/ansible-roles-setup-jenkins/tree/main/roles/setup-jenkins-target/tasks)

![notify](/lesson_30/screenshots/jenkins-nodes.png)
<h3>Добавляем docker creds</h3>

Создаем новый Personal access token [dockerhub](https://hub.docker.com/repositories/ivankhodyrev) и добавляем его в Jenkins credentials:

![notify](/lesson_30/screenshots/docker_creds.png)
<h3>Создание и настройка shared-lib</h3>

Создаем [shared-lib](https://github.com/ioannsnakej/jenkins-shared-lib/tree/main) со скриптами groovy:

msgTmpl.md:

    ${prefix} ${jobName} #${buildNumber} Status: ${buildStatus} Job Link: ${buildUrl}
dockerBuild.groovy:

    def login() {
      withCredentials([usernamePassword(credentialsId: 'docker_creds', usernameVariable: 'username', passwordVariable: 'password')]) {
        sh "docker login -u ${username} -p ${password}"
      }
    }
    
    def build(String tag) {
      sh "docker build -t ${tag} ./"
    }
    
    def push(String tag) {
      sh "docker push ${tag}"
    }

notifyTelegram.groovy:

    def call(String prefix, String botToken, String chatId) {
      def jobName = env.JOB_NAME ?: "Unknow job"
      def buildNumber = env.BUILD_NUMBER ?: ""
      def buildStatus = currentBuild.currentResult ?: "UNKNOW"
      def buildUrl = env.BUILD_URL ?: ""
    
      def tmpl = libraryResource 'msgTmpl.md'
      def msg = tmpl
            .replace('${prefix}', prefix)
            .replace('${jobName}', jobName)
            .replace('${buildNumber}', buildNumber)
            .replace('${buildStatus}', buildStatus)
            .replace('${buildUrl}', buildUrl)
      sh """
        curl -s -X POST https://api.telegram.org/bot${botToken}/sendMessage \
        -d chat_id=${chatId} \
        -d parse_mode=Markdown \
        -d text="${msg.replace("\"", "\\\"")}"
      """
    }
Подключаем к нашему Jenkins:Настройки Jenkins->System->Global Trusted Pipeline Libraries

![notify](/lesson_30/screenshots/add-shared-lib.png)

![notify](/lesson_30/screenshots/add-shared-lib2.png)

<h3>Наши Креды, не буду на них сильно останавливаться, но мы используем их в наших pipelines (Например, креды бд)</h3>

![notify](/lesson_30/screenshots/credentials.png)

<h3>Пишем Jenkinsfile</h3>

Пишем [Jenkinsfile](https://github.com/ioannsnakej/bookstore/blob/main/Jenkinsfile)

    @Library('jenkins-shared-lib') _
    def GIT_URL = "git@github.com:ioannsnakej/bookstore.git"
    
    pipeline {
      agent {label 'docker'}
      parameters {
        gitParameter(type: 'PT_BRANCH', name: 'GIT_BRANCH', branchFilter: 'origin/(.*)', defaultValue: 'main', selectedValue: 'DEFAULT', sortMode: 'DESCENDING_SMART')
      }
    
      environment {
        DOCKER_REPO="ivankhodyrev/bookstore"
        TG_BOT_TOKEN=credentials('bot_token')
        TG_CHAT_ID=credentials('chat_id')
        DB_USER = credentials('db_user')
        DB_NAME = credentials('db_name')
        DB_HOST = credentials('db_host')
        DB_PASS = credentials('db_pass')
      }
    
      stages {
    
        stage('Checkout') {
          steps {
            git branch: "${params.GIT_BRANCH}", url: "${GIT_URL}", credentialsId: 'github_ssh'
          }
        }
    
        stage('Login to dockerhub') {
          steps {
            script {
              dockerBuild.login()
            }
          }
        }
    
        stage('Build docker image') {
          steps {
            script {
              dockerBuild.build(env.DOCKER_REPO)
            }
          }
        }
    
        stage('Test image') {
          steps {
            script {
              writeFile file: "./.env", text: """
                DB_USER=${DB_USER}
                DB_NAME=${DB_NAME}
                DB_HOST=${DB_HOST}
                DB_PASS=${DB_PASS}
              """
    
              sh """
                docker compose up -d 
                sleep 30
                curl -f localhost:8080/books
              """
            }
          }
          post {
            always {
              sh """
                docker compose down -v
              """
            }
          }
        }
    
        stage('Push image to dockerhub') {
          steps {
            script {
              dockerBuild.push(env.DOCKER_REPO)
            }
          }
        }
    
        stage('Call Deploy') {
          when {
            expression { return params.RUN_DEPLOY }
          }
          steps {
            script {
              build quietPeriod: 5, wait: false, job: 'deploy-app', parameters: [string(name: 'DOCKER_IMAGE', value: "${env.DOCKER_REPO}")]
            }
          }
        }
      }
    
      post {
        success {
          script {
            notifyTelegram("✅ Success build", env.TG_BOT_TOKEN, env.TG_CHAT_ID)
          }
        }
    
        failure {
          script {
            notifyTelegram("❌ Failed build!!!", env.TG_BOT_TOKEN, env.TG_CHAT_ID)
          }
        }
      }
    }
Пишем [Jenkinsfile-deploy](https://github.com/ioannsnakej/bookstore/blob/main/Jenkinsfile-deploy):

    @Library('jenkins-shared-lib') _
    
    def remote = [:]
    
    pipeline {
      agent { label 'target' }
      
      parameters {
        string (name: 'DOCKER_IMAGE', description: 'Image to deploy', defaultValue: 'ivankhodyrev/bookstore:latest')
      }
    
      environment {
        HOST = "192.168.56.3"
        TOKEN = credentials('docker_creds')
        PRJ_DIR = "/var/www/bookstore"
        GIT_URL = "git@github.com:ioannsnakej/bookstore.git"
        TG_BOT_TOKEN=credentials('bot_token')
        TG_CHAT_ID=credentials('chat_id')
        DB_USER = credentials('db_user')
        DB_NAME = credentials('db_name')
        DB_HOST = credentials('db_host')
        DB_PASS = credentials('db_pass')
      }
    
      stages {
    
        stage('Checkout') {
          steps {
            git branch: "main", url: "${GIT_URL}", credentialsId: 'github_ssh'
          }
        }
    
        stage('Add .env and compose.yml') {
          steps {
            script {
                writeFile file: "${PRJ_DIR}/.env", text: """
                DB_USER=${DB_USER}
                DB_NAME=${DB_NAME}
                DB_HOST=${DB_HOST}
                DB_PASS=${DB_PASS}
              """
              sh """
                cp compose.tmpl ${PRJ_DIR}
                cp -r nginx/ ${PRJ_DIR}
                cd ${PRJ_DIR}
                export DOCKER_IMAGE=${params.DOCKER_IMAGE}
                envsubst < compose.tmpl > compose.yml
              """
            }
          }
        }
    
        stage('Prepare Credentials') {
          steps {
            withCredentials([sshUserPrivateKey(credentialsId: 'jenkins_ssh', usernameVariable: 'username', keyFileVariable: 'private_key')]) {
              script {
                remote.name = env.HOST
                remote.host = env.HOST
                remote.user = "${username}"
                remote.identity = readFile "${private_key}"
                remote.allowAnyHosts = true
              }
            }
          }
        }
    
        stage('Deploy') {
          steps {
            script {
              sshCommand remote: remote, command: """
                set -x
                docker pull "${params.DOCKER_IMAGE}"
                cd "${env.PRJ_DIR}"
                docker compose down || true
                docker compose up -d
                docker compose ps
              """
            }
          }
        }
      }
    
      post {
        success {
          script {
            notifyTelegram("✅ Deploy success", env.TG_BOT_TOKEN, env.TG_CHAT_ID)
          }
        }
        failure {
          script {
            notifyTelegram("❌ Deploy failed", env.TG_BOT_TOKEN, env.TG_CHAT_ID)
          }
        }
      }
    }

<h3>Результат</h3>

![notify](/lesson_30/screenshots/items.png)

![notify](/lesson_30/screenshots/build-res.png)

![notify](/lesson_30/screenshots/deploy-res.png)

![notify](/lesson_30/screenshots/notify.png)
