1. Повторить шаги, указанные в приведенной статье:
https://andreyex.ru/linux/kak-otlazhivat-stsenarij-bash/.

myscript.sh:

    #!/bin/bash
    exec 5> debug_output.txt
    BASH_XTRACEFD="5"
    PS4='$LINENO: '
    set -x
    var="Привет мир!"
    # печать
    echo "$var"
    # альтернативный способ печати
    printf "%s\n" "$var"
    set +x 
    echo "Мой дом - это: $HOME"
Исправил на myscript1.sh:

    #!/bin/bash
    var="Привет мир"
    echo "$var"
    echo "Мой домашний каталог is=$HOME"
    echo "Мое имя is=$USER"

2. Повторите шаги, указанные в этой статье :
https://basis.gnulinux.pro/ru/latest/basis/30/30._bash_скрипты_№4.html

  По главам 27-30 создал скрипт

      #!/bin/bash
    
    if [ "$(id -u)" != 0 ]; then
      echo "root permissions required" >&2
      exit 1
    fi
    
    file=/var/users
    shell=/sbin/nologin
    oldIFS=$IFS
    
    create_user(){
      IFS=$oldIFS
      groupadd "${group}"
    
    
      # Sudoers
      if [ "${group}" = it ] || [ "${group}" = security ]; then
        if ! grep "%$group" /etc/sudoers; then
          cp /etc/sudoers{,.bkp}
          echo '%'$group' ALL=(ALL) ALL' >> /etc/sudoers
        fi
        shell=/bin/bash
      elif [ "$user" = admin ]; then
        if ! grep "$user" /etc/sudoers; then
          cp /etc/sudoers{,.bkp}
          echo $user' ALL=(ALL) ALL' >> /etc/sudoers
        fi
      fi
      mkdir -v /home/"${group}"
      useradd "${user}" -g "${group}" -b /home/"${group}" -s "${shell}"
    }
    
    
    # Check arguments
    if [ ! -z $2 ]; then
      user="$1"
      group="$2"
      echo Username: $user Group: $group
      create_user
    elif [ -f "${file}" ]; then
      IFS=$'\n'
      for line in $(cat "${file}"); do
        user=$(echo $line | cut -d' ' -f1)
        group=$(echo $line | cut -d' ' -f2)
        echo Username: $user Group: $group
        create_user
      done
      IFS=$oldIFS
    else
      echo "Welcome!"
      select option in "Add user" "Show users" "Exit"; do
        case $option in
          "Add user") read -p "Print username: " user
                      read -p "Print groupname: " group
                      create_user ;;
          "Show users") cut -d: -f1 /etc/passwd ;;
          "Exit") break ;;
          *) echo Wrong option ;;
        esac
      done
    fi

3. Оптимизировать код из задания 2

   Не уверен, что гожусь на оптимизацию кода, но позволил себе поправить Маэстро, как я вижу:
    1. Ввынес меню наверх, чтобы скрипт давал меню, если у нас не переданы параметры
    2. Добавил пункт меню для добавления пользователей из файла, чтобы это было опцией, а не подефолту скрипт шел искать файл
    3. Ну и как было предложено автором в одной из глав, добавил возможность дописать группу с клавиатуры, если у нас передан один параметр
  
   Вроде работает.
  
            #!/bin/bash
            
            if [ "$(id -u)" != 0 ]; then
              echo "root permissions required" >&2
              exit 1
            fi
            
            file=/var/users
            shell=/sbin/nologin
            oldIFS=$IFS
            
            create_user(){
              IFS=$oldIFS
              groupadd "${group}"
            
            
              # Sudoers
              if [ "${group}" = it ] || [ "${group}" = security ]; then
                if ! grep "%$group" /etc/sudoers; then
                  cp /etc/sudoers{,.bkp}
                  echo '%'$group' ALL=(ALL) ALL' >> /etc/sudoers
                fi
                shell=/bin/bash
              elif [ "$user" = admin ]; then
                if ! grep "$user" /etc/sudoers; then
                  cp /etc/sudoers{,.bkp}
                  echo $user' ALL=(ALL) ALL' >> /etc/sudoers
                fi
              fi
              mkdir -v /home/"${group}"
              useradd "${user}" -g "${group}" -b /home/"${group}" -s "${shell}"
            }
            
            
            # Check arguments
            if [ $# -eq 0 ]; then
              echo "Welcome!"
                select option in "Add users from File" "Add user" "Show users" "Exit"; do
                  case $option in
                    "Add users from File") if [ -f "${file}" ]; then
                                              IFS=$'\n'
                                              for line in $(cat "${file}"); do
                                                user=$(echo $line | cut -d' ' -f1)
                                                group=$(echo $line | cut -d' ' -f2)
                                                echo Username: $user Group: $group
                                                create_user
                                              done
                                              IFS=$oldIFS
                                            else 
                                              echo "File not found"
                                            fi ;;
                    "Add user") read -p "Print username: " user
                                read -p "Print groupname: " group
                                create_user ;;
                    "Show users") tail /etc/passwd ;;
                    "Exit") break ;;
                    *) echo Wrong option ;;
                  esac
                done
            elif [ ! -z $2 ]; then
              user="$1"
              group="$2"
              echo Username: $user Group: $group
              create_user
            elif [ ! -z $1 ]; then
              user="$1"
              read -p "Print groupname: " group
              echo Username: $user Group: $group
              create_user
            fi










