# Отчёт по лабораторной работе №5
## Задание1 
Предположим, у вас есть задача автоматизировать проверку формата файлов при коммите с использованием Git Hooks.

Перед каждым коммитом необходимо автоматически проверять, чтобы все .txt файлы в репозитории соответствовали определенному формату.

Создайте bash-скрипт, который будет выполнять проверку того, что коммитится файл формата .txt и в файле присутствует какой-то текст (например, в конце файла есть подпись автора, неважно как она выглядит, главное чтобы была какая-нибудь проверка содержимого файла).
Далее поместите этот скрипт в нужную папку и проверьте, что перед каждым коммитом проходит проверка, например, добавьте вывод о результате проверки в консоль.
За этот функционал отвечает Git Hooks, там сказазно как автоматизировать такую проверку.

## Задание2
Предположим, у вас есть задача на создание новой функциональности для вашего проекта с использованием Git Flow.
Давайте рассмотрим конкретный пример.
В примере важен не сам проект и его код (его тут вообще как такового нет), а принцип работы Git Flow.

## Ход работы 
### Задание 1 - Автоматизация проверки формата файлов при коммите

1) По умолчанию Git хранит хуки в папке `.git/hooks`. Поэтому нам необходимо отредактировать файл `pre-commit`, который находится в данной папке.
   Перемещаемся в папку `hooks` и открываем файл `pre-commit`:
   ```
   cd .git/hooks
   nano pre-commit
   ```
2) Следующим шагом необходимо написать bash-скрипт, который будет проверять файл при коммитах.
   Суть нашего скрипта заключается в том, что при коммите текстовых файлов(с раширением '.txt') он будет проверять количество символов в этих файлах.
   Если количество символов не больше 10, то коммит файла будет успешно совершён.
   А если количество символов будет превышать допустимое значение, то будет выведено сообщение об ошибке и коммит будет прерван.\
   Код bash-скрипта:
   ```
   #!/bin/bash

   max_chars=10
   
   files=$(git diff --cached --name-only --diff-filter=ACM | grep '\.txt$')
   
   if [ -z "$files" ]; then
       echo "В коммит добавляются только .txt файлы!" 
       exit 1
   fi
   
   for file in $files; do
           if [[ -f "$file" ]]; then
                   char_count=$(wc -m < "$file")
   
                   if (( char_count > max_chars )); then
                           echo "Файл '$file' содержит $char_count символов, что превышает лимит $max_chars !" 
                           exit 1
                   fi
           else
                   echo "Файл '$file' не найден!" 
                   exit 1
           fi
   done
   
   echo "Все файлы прошли проверку."
   exit 0
   ```
   
`max_chars=10` - Задаём лимит на количество символов в файле, который можно закоммитить. В данном случае, это 10 символов.

`git diff --cached --name-only --diff-filter=ACM`:\
   Получает список всех файлов, подготовленных для коммита (стадия индексирования).\
   Флаг `--diff-filter=ACM`:\
   A – добавленные файлы.\
   C – копированные файлы.\
   M – модифицированные файлы.

   `--name-only` - Выводит только имена файлов.

   `grep '\.txt$'` - Оставляет только файлы с расширением .txt.\
   Регулярное выражение \.txt$ указывает на файлы, заканчивающиеся на .txt.\
   После выполнения этой команды переменная $files будет содержать список .txt файлов, которые находятся в индексе.

`[ -z "$files" ]` - Проверяет, пуста ли переменная $files.\
Если переменная пуста, значит, .txt файлов нет.

`echo "В коммит добавляются только .txt файлы!"` - Выводит сообщение об ошибке.

`exit 1` - Завершает выполнение скрипта с кодом возврата 1 (ошибка).

`for file in $files; do` - Цикл для проверки каждого файла 

`if [[ -f "$file" ]]; then` - Проверка существования файла

Если файл не найден:\
`echo "Файл '$file' не найден!"` - Выводит сообщение об ошибке, и скрипт завершает выполнение.

`char_count=$(wc -m < "$file")` - Подсчёт символов в файле

`wc -m` - Подсчитывает количество символов в файле.

`(( char_count > max_chars ))` - Проверяет, превышает ли количество символов в файле значение переменной $max_chars.\
Если превышает:
1. Выводится сообщение об ошибке.
2. Коммит останавливается.

`echo "Все файлы прошли проверку."` - Если все файлы соответствуют требованиям, выводится сообщение об успешной проверке.

`exit 0` - Завершает выполнение скрипта с кодом возврата 0 (успешное выполнение).

### Задание 2 - Использование Git Flow в проекте

В результате всех проделанных действий для задания 2 получилось такое дерево для ветки `main`:

![img.png](files_for_lab5/img.png)

И такое для ветки `develop`:

![img_1.png](files_for_lab5/img_1.png)