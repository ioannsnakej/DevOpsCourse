## Устанавливаем [yandex CLI](https://yandex.cloud/ru/docs/cli/quickstart?ysclid=ml7li4i1c4688798945):

    curl -sSL https://storage.yandexcloud.net/yandexcloud-yc/install.sh | bash
    source "/home/ivan/.bashrc"
## Настраиваем профиль ([токен](https://oauth.yandex.ru/verification_code)):

    yc init
![notify](/lesson_39/setup_profile.png)
## [Начало работы с Terraform](https://yandex.cloud/ru/docs/tutorials/infrastructure-management/terraform-quickstart):

[зеркало](https://hashicorp-releases.yandexcloud.net/terraform/) -> [terraform_1.14.2_linux_amd64.zip](https://hashicorp-releases.yandexcloud.net/terraform/1.14.2/terraform_1.14.2_linux_amd64.zip)

    wget https://hashicorp-releases.yandexcloud.net/terraform/1.14.2/terraform_1.14.2_linux_amd64.zip
    unzip terraform_1.14.2_linux_amd64.zip 
    sudo mv terraform /usr/bin/
## Подключаем провайдера:

    nano ~/.terraformrc

>[!NOTE]
>Файл .terraformrc должен располагаться в корне домашней папки пользователя, например, /home/user/ или /User/user/.

Вставить текст:

    provider_installation {
      network_mirror {
        url = "https://terraform-mirror.yandexcloud.net/"
        include = ["registry.terraform.io/*/*"]
      }
      direct {
        exclude = ["registry.terraform.io/*/*"]
      }
    }

## Добавляем сервисный аккаунт для работы с terraform:

![notify](/lesson_39/add_role.png)

    export YC_TOKEN=$(yc iam create-token --impersonate-service-account-id <идентификатор_сервисного_аккаунта>)
    export YC_CLOUD_ID=$(yc config get cloud-id)
    export YC_FOLDER_ID=$(yc config get folder-id)

## main.tf:

    terraform {
        required_providers {
            yandex = {
                source = "yandex-cloud/yandex"
            }
        }
        required_version = ">= 0.13"
    }
    
    provider "yandex" {
        zone = "ru-central1-b"
    }
- `source` — глобальный адрес источника провайдера.
- `required_version` — минимальная версия Terraform, с которой совместим провайдер.
- `provider` — название провайдера.
- `zone` — зона доступности, в которой по умолчанию будут создаваться все облачные ресурсы.

## Инициализирем провайдеров:

        terraform init
## Задаем переменные vars.tf:

    variable "default_image" {
        default = "fd8lcd9f54ldmonh1d72"
    }
    
    variable "default_subnet" {
        default = "e2lndehb1i9jh27626hj"
    }
    
    variable "disk_size" {
        default = "20"
    }
    
    variable "default_vcpu" {
        default = 2
    }
    
    variable "default_memory" {
        default = 2
    }
    
    variable "default_zone" {
        default = "ru-central1-b"
    }

- `default_image` - id образа ОС (выбрал ubuntu 24.04)
- `default_subnet` - id подсети (взял id default-ru-central1-b)
- `disk_size` - размер диска
- `default_vcpu` - число ядер
- `default_memory` - размер RAM
- `default_zone` - зона доступности
## Описываем загрузочный диск disk.tf:

    resource "yandex_compute_disk" "bookstore-app-boot-disk-1" {
        name = "bookstore-app-boot-disk-1"
        type = "network-hdd"
        zone = "${var.default_zone}"
        size = "${var.disk_size}"
        image_id = "${var.default_image}"
    }
- `name` - имя диска в облаке
- `type` - тип диска (hdd/ssd)
## Описываем instance.tf:

    resource "yandex_compute_instance" "bookstore-app" {
        name = "bookstore-app"
        hostname = "bookstore-app"
        platform_id = "standard-v3"
    
        resources {
            cores = var.default_vcpu
            memory = var.default_memory
        }
    
        boot_disk {
            disk_id = yandex_compute_disk.bookstore-app-boot-disk-1.id
        }
    
        network_interface {
            subnet_id = var.default_subnet
            nat = true
        }
    
        metadata = {
            user-data = "${file("./users.txt")}"
        }
    }

## Создаем users.txt:

    #cloud-config
    users:
        - name: gitlab-runner
          groups: sudo
          shell: /bin/bash
          suddo: 'ALL=(ALL) NOPASSWD:ALL'
          ssh_authorized_keys:
            - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOcmnxDHpiU2qDZU335W9Fwbk8HgQWTNYr/xZugjy6RZ gitlab-runner@gitlabci-worker
        - name: ivan
          groups: sudo
          shell: /bin/bash
          suddo: 'ALL=(ALL) NOPASSWD:ALL'
          ssh_authorized_keys:
            - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJrHp3K/A427dv9hhcKs1L1aG7VGP9326C0F90hNR5Z9 ioannsnakej@gmail.com
            - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDvsQZKumSke2Sz/vC8/wE1URjqwQ+HIIcw5zua5hh5Q ivan@gitlabci-worker

 Проверяем файлы конфигуурации на синтаксис:

     terraform validate
Смотрим план выполнения конфигурации:

    terraform plan
Выполняем:

    terraform apply
![notify](/lesson_39/provisioning.png)

![notify](/lesson_39/running.png)

![notify](/lesson_39/ssh_connect.png)

Удаляем:

    terraform destroy

![notify](/lesson_39/deleting.png)
