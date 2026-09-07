Bash - командная оболочка и язык сценариев. Оболочка принимает команды из терминала, разбирает их и запускает нужные программы. 
Можно использовать:
1. Интерактивно - писать команду в терминал 
2. В виде скриптов - писать набор команд в файл и затем запускать файл, как программу.
## **Что такое Bash-скрипт**
Bash-скрипт - текстовый файл с набором команд, которые bash выполняет сверху вниз. 
Их используют для автоматизации одних и тех же действий. Обычно имеют расширение .sh. Само расширение не делает файл исполняемым: важны права доступа, строка с интерпретатором в начале файла. 
```
cat > sample.log << 'EOF'
2026-05-25 10:00:01 INFO user alice logged in from 192.168.1.10
2026-05-25 10:01:15 ERROR failed password for bob from 10.0.0.5
2026-05-25 10:02:41 INFO service nginx restarted
2026-05-25 10:04:02 WARN suspicious request from 172.16.4.20
2026-05-25 10:05:33 ERROR connection timeout from 10.0.0.5
2026-05-25 10:06:11 INFO backup completed
EOF
```
cat > sample.log - туда будут записаны log
<< 'EOF' считывание строк до конца файла
Во время ввода Bash может выводить приглашение **>**. Оно означает, что оболочка ожидает продолжение многострочного блока. После ввода строки **EOF** файл **sample.log** будет создан.
![[Pasted image 20260907213156.png]]

## **Текстовые редакторы**
Длинные команды предыдущим способом вводить неудобно. Существуют **vim nano** и **micro**
vim - один из самых известных консольных редакторов. 
nano - простой консольный редактор. 
micro - более современный консольный редактор. Обычно не установлен. 
### **Версия 1. Минимальный скрипт**
```
#!/bin/bash
# Shell-script v1

echo "my first shell script"
```
- **#!/bin/bash** - шебанг
- **# Shell-script v1** - комментарий
- **echo "my first shell script"** - команда, которая выводит текст в терминал
Первая строка называется shebang. Она нужна когда скрипт запускается как отдельная программа. ОС читает первую строку и сразу понимает, что нужно передать файл именно bash. Но мы можем запустить скрипт через Bash явно: `bash bash-lab.sh`. 
Сделаем скрипт исполняемым с помощью команды: `chmod +x bash-lab.sh` (иначе Permission denied). 
- **chmod** изменяет права доступа файла
- **+x** добавляет право на исполнение; (**x** означает "executable")
- **bash-lab.sh** - файл, для которого меняются права
### **Версия 2. Добавляем переменные**
```
#!/bin/bash
# Shell-script v2

script_name="bash-lab"
version="0.2"
default_file="sample.log"

echo "$script_name version $version"
echo "Default file: $default_file"
```
Переменная - это имя, за которым хранится значение. В переменной можно сохранить текст, число, имя файла или путь к нему. В нашем коде переменная **default_file** хранит имя лог-файла, а **version** - номер версии скрипта. В Bash переменная создаётся так: **name="value"**. Вокруг знака **=** не должно быть пробелов.
### **Версия 3. Принимаем аргументы командной строки**
```
#!/bin/bash
# Shell-script v3

script_name="bash-lab"
version="0.3"

target_file="$1"

echo "$script_name version $version"
echo "Script name: $0"
echo "Arguments count: $#"
echo "Target file: $target_file"
```
В этой версии используются специальные параметры Bash:
- **$0** - имя или путь, с помощью которого был запущен скрипт
- **$1** - первый аргумент скрипта
- **$#** - количество переданных аргументов
- В этом случае:
- **$0** содержит **./bash-lab.sh**
- **$1** содержит **sample.log**
- **$#** равно **1**
### **Версия 4. Проверяем аргументы через if**
```
#!/bin/bash
# Shell-script v4

script_name="bash-lab"
version="0.4"

target_file="$1"

if [ "$#" -eq 0 ]; then
    echo "Usage: $0 <log-file>"
    exit 1
fi

echo "$script_name version $version"
echo "Target file: $target_file"
```
**Как работает if**
Конструкция **if** позволяет выполнять команды только при соблюдении условия.
Общий вид:
```
if команда_проверки; then   
	 команды_если_проверка_успешна
fi
```
Если при невыполнении условия нужно выполнить другой набор команд, используют блок **else**:
```
if команда_проверки; then    
	команды_если_проверка_успешна
else    
	команды_если_проверка_неуспешна
fi
```
При запуске без аргументов: **./bash-lab.sh** значение **$#** равно **0**. Оператор **-eq** означает "равно" при сравнении целых чисел. Поэтому условие читается так: "если количество переданных аргументов равно нулю". Если условие выполняется, скрипт выводит подсказку и завершает работу.
Конструкция **[ ... ]** используется для проверки условий. Пробелы после `[` и перед `]` обязательны.
Правильно будет так: **[ "$#" -eq 0 ]**. Но в таком случае: **["$#" -eq 0]**, Bash не сможет правильно разделить команду проверки и её аргументы.
Основные операторы сравнения целых чисел:
- **-eq** - равно
- **-ne** - не равно
- **-gt** - больше
- **-lt** - меньше
- **-ge** - больше или равно
- **-le** - меньше или равно
- Внутри условия выполняется команда: **exit 1**
Она немедленно завершает работу скрипта. Число после **exit** называется кодом завершения.
По соглашению:
- **0** означает успешное завершение команды;
- любое другое число обычно означает, что команда завершилась с ошибкой или не смогла выполнить ожидаемое действие.
### **Версия 5. Проверяем существование файла**
```
#!/bin/bash
# Shell-script v5

script_name="bash-lab"
version="0.5"

target_file="$1"

if [ "$#" -eq 0 ]; then
    echo "Usage: $0 <log-file>"
    exit 1
fi

if [ ! -f "$target_file" ]; then
    echo "Error: file not found: $target_file"
    exit 1
fi

echo "$script_name version $version"
echo "Target file: $target_file"
echo "File exists"
```
Здесь появилась ещё одна проверка: **[ ! -f "$target_file" ]**
Оператор **-f** проверяет, существует ли по указанному пути обычный файл.
Символ **!** означает отрицание.
Полезные проверки файлов:
- **-f file** - существует обычный файл
- **-d dir** - существует директория
- **-e path** - путь существует
- **-r file** - файл доступен для чтения
- **-w file** - файл доступен для записи
- **-x file** - файл исполняемый
- **! условие** - отрицание условия
### **Версия 6. Получаем информацию о файле**
```
#!/bin/bash
# Shell-script v6

script_name="bash-lab"
version="0.6"

target_file="$1"

if [ "$#" -eq 0 ]; then
    echo "Usage: $0 <log-file>"
    exit 1
fi

if [ ! -f "$target_file" ]; then
    echo "Error: file not found: $target_file"
    exit 1
fi

line_count=$(wc -l < "$target_file")

echo "$script_name version $version"
echo "Target file: $target_file"
echo "Lines: $line_count"
```
Появилась новая строка: `**line_count=$(wc -l < "$target_file")**`
Команда **wc -l** считает строки.
![[Pasted image 20260907223926.png]]
Нам нужно сохранить **только число**, без имени файла. Для этого передадим содержимое файла команде **wc** через стандартный ввод: **wc -l < sample.log**. Тогда мы передадим просто число **6**. **Стандартный ввод** - это поток данных, который команда получает на вход.
### **Версия 7. Добавляем режимы работы через case**
```
#!/bin/bash
# Shell-script v7

script_name="bash-lab"
version="0.7"

mode="$1"
target_file="$2"

if [ "$#" -eq 0 ]; then
    echo "Usage: $0 <mode> <log-file>"
    echo "Modes: info, preview, scan, help"
    exit 1
fi

case "$mode" in
    help)
        echo "Usage: $0 <mode> <log-file>"
        echo "Modes: info, preview, scan, help"
        ;;
    info)
        line_count=$(wc -l < "$target_file")
        echo "$script_name version $version"
        echo "Target file: $target_file"
        echo "Lines: $line_count"
        ;;
    preview)
        echo "Preview mode is not implemented yet"
        ;;
    scan)
        echo "Scan mode is not implemented yet"
        ;;
    *)
        echo "Error: unknown mode: $mode"
        exit 1
        ;;
esac
```

Общий вид следующий:
```
case "$variable" in
    value1)
        команды
        ;;
    value2)
        команды
        ;;
    *)
        команды_по_умолчанию
        ;;
esac
```
В нашем скрипте Bash сравнивает значение переменной **mode** с вариантами **help**, **info**, **preview** и **scan**.
- **help)**, **info)** и другие варианты задают ветки обработки
- **;;** завершает блок команд выбранного варианта
- ***)**  - вариант по умолчанию, который срабатывает для любого другого значения
- **esac** завершает всю конструкцию **case**
### **Версия 8. Добавляем проверку второго аргумента**
```
#!/bin/bash
# Shell-script v8

script_name="bash-lab"
version="0.8"

mode="$1"
target_file="$2"

if [ "$#" -eq 0 ]; then
    echo "Usage: $0 <mode> <log-file>"
    echo "Modes: info, preview, scan, help"
    exit 1
fi

case "$mode" in
    help)
        echo "Usage: $0 <mode> <log-file>"
        echo "Modes: info, preview, scan, help"
        ;;
    info)
        if [ -z "$target_file" ]; then
            echo "Error: log file is required for mode: $mode"
            exit 1
        fi

        if [ ! -f "$target_file" ]; then
            echo "Error: file not found: $target_file"
            exit 1
        fi

        line_count=$(wc -l < "$target_file")
        echo "$script_name version $version"
        echo "Target file: $target_file"
        echo "Lines: $line_count"
        ;;
    preview)
        if [ -z "$target_file" ]; then
            echo "Error: log file is required for mode: $mode"
            exit 1
        fi

        if [ ! -f "$target_file" ]; then
            echo "Error: file not found: $target_file"
            exit 1
        fi

        echo "Preview mode is not implemented yet"
        ;;
    scan)
        if [ -z "$target_file" ]; then
            echo "Error: log file is required for mode: $mode"
            exit 1
        fi

        if [ ! -f "$target_file" ]; then
            echo "Error: file not found: $target_file"
            exit 1
        fi

        echo "Scan mode is not implemented yet"
        ;;
    *)
        echo "Error: unknown mode: $mode"
        exit 1
        ;;
esac
```
У нас появилась новая проверка: **[ -z "$target_file" ]**. Оператор **-z** проверяет, пуста ли строка. В этом случае условие читается так: "если имя лог-файла не передано". Кавычки вокруг **$target_file** важны.
### **Версия 9. Реализуем режим preview через while**
Режим **preview** должен выводить первые пять строк файла.
```
#!/bin/bash
# Shell-script v9

script_name="bash-lab"
version="0.9"

mode="$1"
target_file="$2"

if [ "$#" -eq 0 ]; then
    echo "Usage: $0 <mode> <log-file>"
    echo "Modes: info, preview, scan, help"
    exit 1
fi

case "$mode" in
    help)
        echo "Usage: $0 <mode> <log-file>"
        echo "Modes: info, preview, scan, help"
        ;;
    info)
        if [ -z "$target_file" ]; then
            echo "Error: log file is required for mode: $mode"
            exit 1
        fi

        if [ ! -f "$target_file" ]; then
            echo "Error: file not found: $target_file"
            exit 1
        fi

        line_count=$(wc -l < "$target_file")
        echo "$script_name version $version"
        echo "Target file: $target_file"
        echo "Lines: $line_count"
        ;;
    preview)
        if [ -z "$target_file" ]; then
            echo "Error: log file is required for mode: $mode"
            exit 1
        fi

        if [ ! -f "$target_file" ]; then
            echo "Error: file not found: $target_file"
            exit 1
        fi

        preview_count=0

        while IFS= read -r line; do
            preview_count=$((preview_count + 1))
            echo "$preview_count: $line"

            if [ "$preview_count" -ge 5 ]; then
                break
            fi
        done < "$target_file"
        ;;
    scan)
        if [ -z "$target_file" ]; then
            echo "Error: log file is required for mode: $mode"
            exit 1
        fi

        if [ ! -f "$target_file" ]; then
            echo "Error: file not found: $target_file"
            exit 1
        fi

        echo "Scan mode is not implemented yet"
        ;;
    *)
        echo "Error: unknown mode: $mode"
        exit 1
        ;;
esac
```
Общий вид цикла **while**:
```
while условие; do  
  команды
done
```
Проверка цикла выглядит так: **IFS= read -r line** Эта строка состоит из нескольких частей:
1. **IFS=** - временно задаёт пустое значение переменной **IFS** только для команды **read**
2. **read** - читает одну строку из стандартного ввода
3. **-r** - запрещает считать обратную косую черту `\\` специальным символом
4. **line** - имя переменной, в которую будет записана прочитанная строка
### **Версия 10. Выносим повторяющийся код в функции**
```
#!/bin/bash
# Shell-script v10

script_name="bash-lab"
version="0.10"

print_usage() {
    echo "Usage: $0 <mode> <log-file>"
    echo "Modes: info, preview, scan, help"
}

err() {
    echo "Error: $1"
    exit 1
}

require_log_file() {
    local mode="$1"
    local file="$2"

    if [ -z "$file" ]; then
        err "log file is required for mode: $mode"
    fi

    if [ ! -f "$file" ]; then
        err "file not found: $file"
    fi
}

show_info() {
    local file="$1"
    local line_count

    line_count=$(wc -l < "$file")

    echo "$script_name version $version"
    echo "Target file: $file"
    echo "Lines: $line_count"
}

show_preview() {
    local file="$1"
    local limit=5
    local counter=0
    local line

    while IFS= read -r line; do
        counter=$((counter + 1))
        echo "$counter: $line"

        if [ "$counter" -ge "$limit" ]; then
            break
        fi
    done < "$file"
}

mode="$1"
target_file="$2"

if [ "$#" -eq 0 ]; then
    print_usage
    exit 1
fi

case "$mode" in
    help)
        print_usage
        ;;
    info)
        require_log_file "$mode" "$target_file"
        show_info "$target_file"
        ;;
    preview)
        require_log_file "$mode" "$target_file"
        show_preview "$target_file"
        ;;
    scan)
        require_log_file "$mode" "$target_file"
        echo "Scan mode is not implemented yet"
        ;;
    *)
        err "unknown mode: $mode"
        ;;
esac
```
Объявление функции:
```
имя_функции() {
    команды
}
```
Функция получает аргументы так же, как скрипт. Однако внутри функции **$1**, **$2** и **$#** относятся к аргументам именно **этой функции,** а не к аргументам всего скрипта. 
```
mode="preview"target_file="sample.log"
```
Bash фактически вызовет функцию так: **require_log_file "preview" "sample.log".** После этого внутри функции значения специальных параметров будут следующими:
- **$1** - preview
- **$2** - sample.log
- **$#** - 2
```
local mode="$1"local file="$2"
```
После выполнения этих строк:
- локальная переменная **mode** содержит первый аргумент, на примере это **preview**;
- локальная переменная **file** содержит второй аргумент, на примере это **sample.log**.
Ключевое слово **local** создаёт переменную, которая существует **только внутри текущей функции.**
### **Версия 11. Используем цикл for в справке**
```
#!/bin/bash
# Shell-script v11

script_name="bash-lab"
version="0.11"

print_usage() {
    echo "Usage: $0 <mode> <log-file>"
    echo
    echo "Modes:"

    for mode_name in info preview scan help; do
        echo "- $mode_name"
    done
}

err() {
    echo "Error: $1"
    exit 1
}

require_log_file() {
    local mode="$1"
    local file="$2"

    if [ -z "$file" ]; then
        err "log file is required for mode: $mode"
    fi

    if [ ! -f "$file" ]; then
        err "file not found: $file"
    fi
}

show_info() {
    local file="$1"
    local line_count

    line_count=$(wc -l < "$file")

    echo "$script_name version $version"
    echo "Target file: $file"
    echo "Lines: $line_count"
}

show_preview() {
    local file="$1"
    local limit=5
    local counter=0
    local line

    while IFS= read -r line; do
        counter=$((counter + 1))
        echo "$counter: $line"

        if [ "$counter" -ge "$limit" ]; then
            break
        fi
    done < "$file"
}

mode="$1"
target_file="$2"

if [ "$#" -eq 0 ]; then
    print_usage
    exit 1
fi

case "$mode" in
    help)
        print_usage
        ;;
    info)
        require_log_file "$mode" "$target_file"
        show_info "$target_file"
        ;;
    preview)
        require_log_file "$mode" "$target_file"
        show_preview "$target_file"
        ;;
    scan)
        require_log_file "$mode" "$target_file"
        echo "Scan mode is not implemented yet"
        ;;
    *)
        err "unknown mode: $mode"
        ;;
esac
```
Общий вид цикла **for**:
```
for переменная in значение1 значение2 значение3; do   
	команды
done
```
После слова **in** указываем список значений, разделённых пробелами. Bash по очереди записывает каждое значение в переменную цикла и выполняет команды между **do** и **done**. В функции **print_usage** используется цикл:
```
for mode_name in info preview scan help; do    echo "- $mode_name"done
```
1. В первой итерации в переменную **mode_name** попадает значение **info**. Команда **echo** выводит **- info**.
2. Во второй итерации **mode_name** получает значение **preview**.
3. Затем цикл таким же образом обрабатывает **scan** и **help**.
4. После последнего значения цикл завершается.
### **Версия 12. Реализуем режим scan с помощью grep**
Реализуем поиск IP-подобных значений в лог-файле с помощью внешней команды **grep**.
```
#!/bin/bash
# Shell-script v12

script_name="bash-lab"
version="0.12"

print_usage() {
    echo "Usage: $0 <mode> <log-file>"
    echo
    echo "Modes:"

    for mode_name in info preview scan help; do
        echo "- $mode_name"
    done
}

err() {
    echo "Error: $1"
    exit 1
}

require_log_file() {
    local mode="$1"
    local file="$2"

    if [ -z "$file" ]; then
        err "log file is required for mode: $mode"
    fi

    if [ ! -f "$file" ]; then
        err "file not found: $file"
    fi
}

show_info() {
    local file="$1"
    local line_count

    line_count=$(wc -l < "$file")

    echo "$script_name version $version"
    echo "Target file: $file"
    echo "Lines: $line_count"
}

show_preview() {
    local file="$1"
    local limit=5
    local counter=0
    local line

    while IFS= read -r line; do
        counter=$((counter + 1))
        echo "$counter: $line"

        if [ "$counter" -ge "$limit" ]; then
            break
        fi
    done < "$file"
}

scan_ips() {
    local file="$1"

    grep -Eo '([0-9]{1,3}\.){3}[0-9]{1,3}' "$file"
}

mode="$1"
target_file="$2"

if [ "$#" -eq 0 ]; then
    print_usage
    exit 1
fi

case "$mode" in
    help)
        print_usage
        ;;
    info)
        require_log_file "$mode" "$target_file"
        show_info "$target_file"
        ;;
    preview)
        require_log_file "$mode" "$target_file"
        show_preview "$target_file"
        ;;
    scan)
        require_log_file "$mode" "$target_file"
        scan_ips "$target_file"
        ;;
    *)
        err "unknown mode: $mode"
        ;;
esac
```
В функции **scan_ips** выполняется команда: `grep -Eo '([0-9]{1,3}\.){3}[0-9]{1,3}' "$file"`
Команда **grep** ищет текст, который соответствует указанному шаблону. В этой команде:
- **-E** включает расширенные регулярные выражения
- **-o** выводит только найденный фрагмент, а не всю строку лога
- текст в одинарных кавычках - **регулярное выражение**
- **$file** - путь к файлу, который передаётся в функцию
```
Шаблон имеет вид: ([0-9]{1,3}\.){3}[0-9]{1,3}
1. [0-9] - одна цифра от 0 до 9
2. {1,3} - повторить предыдущий элемент от одного до трёх раз
3. [0-9]{1,3} - число длиной от одной до трёх цифр
4. \. - обычная точка
5. (...) - группа элементов
6. (...){3} - повторить группу три раза
```
### **Версия 13. Убираем повторы через pipe**
В лог-файле одно IP-подобное значение может встречаться много раз. Для отчёта полезнее получить список без повторов. Передадим вывод **grep** команде **sort -u**.
```
#!/bin/bash
	# Shell-script v13
	
	script_name="bash-lab"
	version="0.13"
	
	print_usage() {
	    echo "Usage: $0 <mode> <log-file>"
	    echo
	    echo "Modes:"
	
	    for mode_name in info preview scan help; do
	        echo "- $mode_name"
	    done
	}
	
	err() {
	    echo "Error: $1"
	    exit 1
	}
	
	require_log_file() {
	    local mode="$1"
	    local file="$2"
	
	    if [ -z "$file" ]; then
	        err "log file is required for mode: $mode"
	    fi
	
	    if [ ! -f "$file" ]; then
	        err "file not found: $file"
	    fi
	}
	
	show_info() {
	    local file="$1"
	    local line_count
	
	    line_count=$(wc -l < "$file")
	
	    echo "$script_name version $version"
	    echo "Target file: $file"
	    echo "Lines: $line_count"
	}
	
	show_preview() {
	    local file="$1"
	    local limit=5
	    local counter=0
	    local line
	
	    while IFS= read -r line; do
	        counter=$((counter + 1))
	        echo "$counter: $line"
	
	        if [ "$counter" -ge "$limit" ]; then
	            break
	        fi
	    done < "$file"
	}
	
	scan_ips() {
	    local file="$1"
	
	    grep -Eo '([0-9]{1,3}\.){3}[0-9]{1,3}' "$file" | sort -u
	}
	
	mode="$1"
	target_file="$2"
	
	if [ "$#" -eq 0 ]; then
	    print_usage
	    exit 1
	fi
	
	case "$mode" in
	    help)
	        print_usage
	        ;;
	    info)
	        require_log_file "$mode" "$target_file"
	        show_info "$target_file"
	        ;;
	    preview)
	        require_log_file "$mode" "$target_file"
	        show_preview "$target_file"
	        ;;
	    scan)
	        require_log_file "$mode" "$target_file"
	        scan_ips "$target_file"
	        ;;
	    *)
	        err "unknown mode: $mode"
	        ;;
	esac
```
В строке: `grep -Eo '([0-9]{1,3}.){3}[0-9]{1,3}' "$file" | sort -u` сначала **grep** находит IP-подобные значения и выводит каждое с новой строки. Затем **sort -u** получает эти строки на вход: **sort** сортирует строки как текст. Флаг **-u** удаляет повторяющиеся строки. Поэтому режим **scan** теперь выводит каждое найденное значение только один раз.
### Версия 14. Сохраняем отчёт в файлы
Скрипт уже выводит информацию в терминал. Добавим режим **report**, который сохранит результаты в файл **report.txt**. Сообщения об ошибках команд, которые выполняются при формировании отчёта, будут сохранены отдельно в **error.log**.
```
#!/bin/bash
# Shell-script: final version

script_name="bash-lab"
version="1.0"
report_file="report.txt"
error_file="error.log"

print_usage() {
    echo "Usage: $0 <mode> <log-file>"
    echo
    echo "Modes:"

    for mode_name in info preview scan report help; do
        echo "- $mode_name"
    done

    echo
    echo "Examples:"
    echo "  $0 info sample.log"
    echo "  $0 preview sample.log"
    echo "  $0 scan sample.log"
    echo "  $0 report sample.log"
}

err() {
    echo "Error: $1"
    exit 1
}

require_log_file() {
    local mode="$1"
    local file="$2"

    if [ -z "$file" ]; then
        err "log file is required for mode: $mode"
    fi

    if [ ! -f "$file" ]; then
        err "file not found: $file"
    fi

    if [ ! -r "$file" ]; then
        err "file is not readable: $file"
    fi
}

show_info() {
    local file="$1"
    local line_count

    line_count=$(wc -l < "$file")

    echo "$script_name version $version"
    echo "Target file: $file"
    echo "Lines: $line_count"
}

show_preview() {
    local file="$1"
    local limit=5
    local counter=0
    local line

    while IFS= read -r line; do
        counter=$((counter + 1))
        echo "$counter: $line"

        if [ "$counter" -ge "$limit" ]; then
            break
        fi
    done < "$file"
}

scan_ips() {
    local file="$1"

    grep -Eo '([0-9]{1,3}\.){3}[0-9]{1,3}' "$file" | sort -u
}

make_report() {
    local file="$1"

    {
        echo "Report for: $file"
        echo "Generated by: $script_name $version"
        echo "Lines: $(wc -l < "$file")"
        echo
        echo "Unique IP-like strings:"
        scan_ips "$file"
    } > "$report_file" 2> "$error_file"

    echo "Report saved to: $report_file"
    echo "Errors saved to: $error_file"
}

mode="$1"
target_file="$2"

if [ "$#" -eq 0 ]; then
    print_usage
    exit 1
fi

case "$mode" in
    help)
        print_usage
        ;;
    info)
        require_log_file "$mode" "$target_file"
        show_info "$target_file"
        ;;
    preview)
        require_log_file "$mode" "$target_file"
        show_preview "$target_file"
        ;;
    scan)
        require_log_file "$mode" "$target_file"
        scan_ips "$target_file"
        ;;
    report)
        require_log_file "$mode" "$target_file"
        make_report "$target_file"
        ;;
    *)
        err "unknown mode: $mode"
        ;;
esac
```
**Проверка читаемости файла**
В функции **require_log_file** появилась новая проверка:
```
if [ ! -r "$file" ]; then    err "file is not readable: $file"fi
```
Оператор **-r** проверяет, доступен ли файл для чтения текущему пользователю. Скрипт использует содержимое лог-файла в режимах **info**, **preview**, **scan** и **report**, поэтому существование файла само по себе недостаточно: у Bash должна быть возможность его прочитать.
В режиме **report** используется блок команд:

```
{    команда1    команда2    команда3} > report.txt 2> error.log
```

Фигурные скобки объединяют несколько команд в один блок. Перенаправления после закрывающей скобки применяются ко всему этому блоку:
- **> report.txt** записывает обычный вывод блока в файл **report.txt**
- **2> error.log** записывает поток ошибок блока в файл **error.log**
