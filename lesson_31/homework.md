for GitLab CI needs:
1. [Repo](https://gitlab.com/khodyrev-ivan/diplom/-/tree/main?ref_type=heads)
2. Runner
3. .gitlab-ci.yml

Создаем машину для gitlab runner

Устанавливаем gitlab runner по [инструкции](https://docs.gitlab.com/runner/install/linux-repository/) на нашу новую машину:

    curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
***
    sudo apt install gitlab-runner
