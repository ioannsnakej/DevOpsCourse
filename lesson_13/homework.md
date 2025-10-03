1. Слово — это последовательность из букв (русских или английских), внутри которой
могут быть дефисы.
На вход даётся текст, посчитайте, сколько в нём слов.
PS. Задача решается в одну строчку.

        #!/bin/bash
        
        if [ $# -ne 1 ]; then
            echo "Usage: $0 file_name"
            exit 1
        fi
        
        FILE_NAME="$1"
        echo "$(wc -w < ${FILE_NAME})"

2. Добавить ко всем файлам формата log timestamp в качестве названия.
Должно получиться filename_{timestamp}.log
Для всех файлов с расширением Py добавьте в конец названия хэш
коммита.

        #!/bin/bash
        #set -x
        if [ $# -ne 1 ];then
            echo "Usage: $0 dir_name"
            exit
        fi    
        
        
        timestamp=$(date +%Y%m%d_%H%M%S)
        commit_hash=$(git rev-parse --short HEAD)
        DIR_NAME="$1"
        
        rename_files() {
            local dir="$1"
            local ext="$2"
            local suffix="$3"
        
            find "${dir}" -name "*.${ext}" | while IFS= read -r file;do
                basename="${file%.$ext}"
                new_name="${basename}_${suffix}.$ext"
                mv "${file}" "${new_name}"
            done
        }
        
        rename_files "${DIR_NAME}" "log" "${timestamp}"
        rename_files "${DIR_NAME}" "py" "${commit_hash}"

3.  Довольно распространённая ошибка ошибка — это повтор слова.
Вот в предыдущем предложении такая допущена. Необходимо
исправить каждый такой повтор (слово, один или несколько
пробельных символов, и снова то же слово).

        #!/bin/bash
        
        if [ $# -ne 1 ];then
            echo "Usage: $0 file_name"
            exit 1
        fi
        file_name="$1"
        
        sed -E 's/\b([[:alpha:]]+)([[:space:]]+\1)+\b/\1/g' "${file_name}" > duplicate_fix.txt
        
        # sed - читает входной файл построчно и применяет выражение
        # -E - включает расширенные регулярные выражения
        # 's/шаблон/замена/g' - команда замены;  g - глобально по строке
        # \b -граница слова
        # ([[:alpha:]]+) - группа1: одно или более букв, где "+" - один или больше
        # [[:space:]]+\1)+ - группа2: описывающая повторы:
            # [[:space:]]+ - один или несколько пробельных символов
            # \1 - повтор того же слова, что было в группе1
            # (..)+ - "пробел+то же слово" может повторяться один или более раз
        # \b - граница слова после последнего повторения
        # \1 - заменяем всю цепочку одним экземпляром
        #echo "$(uniq "${file_name}" >> duplicate_fix.txt)"







  





  
