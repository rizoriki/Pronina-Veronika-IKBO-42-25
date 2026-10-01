# Практическое занятие №1. Введение, основы работы в командной строке

П.Н. Советов, РТУ МИРЭА

Научиться выполнять простые действия с файлами и каталогами в Linux из командной строки. Сравнить работу в командной строке Windows и Linux.

## Задача 1

Вывести отсортированный в алфавитном порядке список имен пользователей в файле passwd (вам понадобится grep).

***Ответ:***
```
[veronka@localhost ~]$ cut -d : -f1 /etc/passwd | sort
adm
avahi
bin
chrony
colord
daemon
dbus
dnsmasq
ftp
games
geoclue
gluster
gnome-remote-desktop
halt
lp
mail
nm-openconnect
nm-openvpn
nobody
nslcd
openvpn
operator
pipewire
polkitd
postfix
root
rpc
rtkit
sddm
shutdown
sshd
sync
systemd-coredump
systemd-oom
systemd-resolve
systemd-timesync
tcpdump
tss
unbound
user1
vboxadd
veronka
```

## Задача 2

Вывести данные /etc/protocols в отформатированном и отсортированном порядке для 5 наибольших портов, как показано в примере ниже:

```
[root@localhost etc]# cat /etc/protocols ...
142 rohc
141 wesp
140 shim6
139 hip
138 manet
```

***Ответ:***
```
[veronka@localhost ~]$ awk '!/^#/ && NF {print $2, $1}' /etc/protocols | sort -nr | head -5
142 rohc
141 wesp
140 shim6
139 hip
138 manet
```

## Задача 3

Написать программу banner средствами bash для вывода текстов, как в следующем примере (размер баннера должен меняться!):

```
[root@localhost ~]# ./banner "Hello from RTU MIREA!"
+-----------------------+
| Hello from RTU MIREA! |
+-----------------------+
```

Перед отправкой решения проверьте его в ShellCheck на предупреждения.

***Ответ:***
```
[veronka@localhost Рабочий стол]$ ./banner "Hello from RTU MIREA!"
+-----------------------+
| Hello from RTU MIREA! |
+-----------------------+
[veronka@localhost Рабочий стол]$ ./banner "i love my cat"
+---------------+
| i love my cat |
+---------------+
```

***Программа banner:***
```
if [ $# -eq 0 ]; then
    echo "Usage: $0 <text>" >&2
    exit 1
fi

text="$*"
len=${#text}
width=$((len + 2))

printf -v dashes '%*s' "$width" ''
dashes=${dashes// /-}

printf '+%s+\n' "$dashes"
printf '| %s |\n' "$text"
printf '+%s+\n' "$dashes"
```

## Задача 4

Написать программу для вывода всех идентификаторов (по правилам C/C++ или Java) в файле (без повторений).

Пример для hello.c:

```
h hello include int main n printf return stdio void world
```

***Ответ:***
```
[veronka@localhost Рабочий стол]$ grep -oE '[a-zA-Z_][a-zA-Z0-9_]*' meowing | sort -u | paste -sd ' '
cout endl include int iostream main MEOW namespace return std using
```

***Файл meowing.cpp:***
```
#include <iostream>
using namespace std;
int main()
{
    cout << "MEOW" << endl;
    return 0;
}
```

## Задача 5

Написать программу для регистрации пользовательской команды (правильные права доступа и копирование в /usr/local/bin).

Например, пусть программа называется reg:

```
./reg banner
```

В результате для banner задаются правильные права доступа и сам banner копируется в /usr/local/bin.

***Ответ:***
```
[veronka@localhost Рабочий стол]$ touch coma
[veronka@localhost Рабочий стол]$ nano coma
[veronka@localhost Рабочий стол]$ chmod +x coma
[veronka@localhost Рабочий стол]$ ./coma banner
[sudo] пароль для veronka: 
[veronka@localhost Рабочий стол]$ ls -l /usr/local/bin/banner
-rwxr-xr-x. 1 root root 249 окт  1 19:48 /usr/local/bin/banner
```

***Файл coma.sh:***
```
#!/bin/bash
if [ $# -ne 1 ]; then
    echo "usage: $0 <file>" >&2
    exit 1
fi

if [ ! -f "$1" ]; then
    echo "file $1 is not found" >&2
    exit 1
fi

sudo install -m 755 "$1" /usr/local/bin/
```

## Задача 6

Написать программу для проверки наличия комментария в первой строке файлов с расширением c, js и py.

***Решение:***
```
[veronka@localhost Рабочий стол]$ nano coment
[veronka@localhost Рабочий стол]$ chmod +x coment
[veronka@localhost Рабочий стол]$ mkdir task6 && cd task6
[veronka@localhost task6]$ echo "//meow" > file.c
[veronka@localhost task6]$ echo 'console.log("i <3 my cat");' > file1.js
[veronka@localhost task6]$ echo "#woof" > file2.py
[veronka@localhost ~]$ cd 'Рабочий стол' && ./coment task6
```
***Файл coment.sh:***
```
#!/bin/bash
dir="${1:-.}"

find "$dir" -type f \( -name '*.c' -o -name '*.js' -o -name '*.py' \) | while IFS= read -r f; do
    first=$(head -n 1 "$f")
    case "$f" in
        *.c|*.js) pattern='^[[:space:]]*(//|/\*)' ;;
        *.py)     pattern='^[[:space:]]*#' ;;
    esac
    if [[ $first =~ $pattern ]]; then
        echo "$f: there is comment in 1st row"
    else
        echo "$f: there is not comment in 1st row"
    fi
done
```
***Ответ:***
```
task6/file.c: there is comment in 1st row
task6/file2.py: there is comment in 1st row
task6/file1.js: there is not comment in 1st row
```

## Задача 7

Написать программу для нахождения файлов-дубликатов (имеющих 1 или более копий содержимого) по заданному пути (и подкаталогам).

***Решение:***
```
[veronka@localhost Рабочий стол]$ nano findDouble
[veronka@localhost Рабочий стол]$ chmod +x findDouble
[veronka@localhost Рабочий стол]$ mkdir task7
[veronka@localhost Рабочий стол]$ echo meow > task7/a.txt
[veronka@localhost Рабочий стол]$ cp task7/a.txt task7/b.txt
[veronka@localhost Рабочий стол]$ echo woof > task7/c.txt
[veronka@localhost Рабочий стол]$ ./findDouble task7
```
***Файл findDouble.sh:***
```
#!/bin/bash
if [ $# -ne 1 ]; then
    echo "Usage: $0 <dir>" >&2
    exit 1
fi

find "$1" -type f -exec md5sum {} + | sort | uniq -w32 --all-repeated=separate
```
***Ответ:***
```
ad606d6a24a2dec982bc2993aaaf9160  task7/a.txt
ad606d6a24a2dec982bc2993aaaf9160  task7/b.txt
```

## Задача 8

Написать программу, которая находит все файлы в данном каталоге с расширением, указанным в качестве аргумента и архивирует все эти файлы в архив tar.

## Задача 9

Написать программу, которая заменяет в файле последовательности из 4 пробелов на символ табуляции. Входной и выходной файлы задаются аргументами.

## Задача 10

Написать программу, которая выводит названия всех пустых текстовых файлов в указанной директории. Директория передается в программу параметром. 

## Полезные ссылки

Линукс в браузере: https://bellard.org/jslinux/

ShellCheck: https://www.shellcheck.net/

Разработка CLI-приложений

Общие сведения

https://ru.wikipedia.org/wiki/Интерфейс_командной_строки
https://nullprogram.com/blog/2020/08/01/
https://habr.com/ru/post/150950/

Стандарты

https://www.gnu.org/prep/standards/standards.html#Command_002dLine-Interfaces
https://www.gnu.org/software/libc/manual/html_node/Argument-Syntax.html
https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap12.html

Реализация разбора опций

Питон

https://docs.python.org/3/library/argparse.html#module-argparse
https://click.palletsprojects.com/en/7.x/
