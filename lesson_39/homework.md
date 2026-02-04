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
## Подключить провайдера:

    nano ~/.terraformrc
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



