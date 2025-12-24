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
[notify](/lesson_31/screens/gitlab-runner_register.png)
