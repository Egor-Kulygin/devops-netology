# Домашнее задание по лекции "Работа в терминале (лекция 1)"

### 5)
Для виртуальной машины выделено 1024мб оперативной памяти. 2 процессора  
Видеопамять 4 мб  
Сетевой адаптер  
Носитель на 64гб (похоже на динамический до 64гб)  


### 8)
 ignoreboth это сокращение для ignorespace and ignoredups,   
    ignorespace - не сохраняет команды начинающиеся с пробела,   
    ignoredups - не сохраняет команды, которые уже имееются в истории  
2152 начало описание команды History  
2158 HISTFILESIZE.  
p.s. строки могут быть разными, зависят от разрешения экрана или виртуальной машины.  


### 9) 
152 строка  
RESERVED WORDS  
       Reserved  words  are  words  that  have a special meaning to the  
       shell.  The following words are recognized as reserved when  un‐  
       quoted  and either the first word of a simple command (see SHELL  
       GRAMMAR below) or the third word of a case or for command:  
       ! case  coproc  do done elif else esac fi for function if in se‐  
       lect then until while { } time [[ ]]  
{} скобки зарезервированы для циклов  


### 10) 
touch {1..100000}  
100000 создать можно , но 300000 не получится  
Даже если прописать так:  
 touch {1..100000} {100001..300000}  
argument list too long  
6.4 Bash Conditional Expressions  

### 11)
Chapter 6: Bash Features  
-d file True if file exists and is a directory.  
Тоесть [[-d /tmp]] показывает параметр Истина (true) если существует каталог  


### 12) 
vagrant@vagrant:~ $ mkdir /tmp/new_path_dir/ (создаем каталог)  
vagrant@vagrant:~ $ cp /bin/bash /tmp/new_path_dir/ (копируем bash в каталог)  
vagrant@vagrant:~ $ PATH=/tmp/new_path_dir/:$PATH (присваиваем значение системной переменной)  
vagrant@vagrant:~ $ type -a bash (проверяем)  
bash is /tmp/new_path_dir/bash  
bash is /usr/bin/bash  
bash is /bin/bash  


### 13)
команда at используется для назначения одноразового задания на заданное время  
команда batch используются для  назначения одноразовых задач, которые должны выполняться, когда загрузка системы становится меньше 1,5.  
batch   executes  commands  when system load levels permit; in other words, when the load average drops below 1.5, or the value specified in the invocation of atd.  


### 14)
vagrant suspend  
