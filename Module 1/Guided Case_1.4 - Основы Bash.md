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
