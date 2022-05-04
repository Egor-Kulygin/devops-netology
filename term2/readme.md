# Домашнее задание к занятию "3.2. Работа в терминале, лекция 2"
### 1.	
 cd - это команда которая выполняется напрямую в оболочке shell
При вызове её как внешней, она бы запускалась в отдельном процессе и не поменяла бы директорию для нас.  
### 2.	
grep <some_string> <some_file> -c «согласно мануалу»

Useless Use of Wc -l  
This is my personal favorite. There is actually a whole class of "Useless Use of (something) | grep (something) |  
(something)" problems but this one usually manifests itself in scripts riddled by useless backticks and pretzel logic.  
Anything that looks like  
	something | grep '..*' | wc -l  
can usually be rewritten like something along the lines of  
	something | grep -c .   # Notice that . is better than '..*'  


### 3.	
systemd  
pstree –p (команда для просмотра дерева процессов)  

### 4.	
ls -l 2>/dev/pts/0  
vagrant  tty1         2022-05-03 15:13  
vagrant  pts/0        2022-05-03 15:14 (10.0.2.2)  

ls -l >/dev/pts/0 (для визуальной проверки сделал вывод на другой терминал  

### 5.  
cat <file1 >file2  пример команды  
vagrant@vagrant: ~$ cat >file1  - создаем файл1  
test111  
vagrant@vagrant: ~$ cat >file2 - создаем файл2  
test222  
vagrant@vagrant: ~$ cat file1 - Проверяем  
test111  
vagrant@vagrant: ~$ cat file2  - Проверяем  
test222  
vagrant@vagrant: ~$ cat <file1 >file2   перезаписываем  
vagrant@vagrant: ~$ cat file1 - Проверяем  
test111  
vagrant@vagrant: ~$ cat file2 - Проверяем  
test111  
### 6.
Да получится,  
ls -l >/dev/tty1  
или  
echo test >/dev/tty1  
### 7.  
Выполните команду bash 5>&1. К чему она приведет? Что будет, если вы выполните echo netology > /proc/$$/fd/5? Почему так происходит?  
bash 5>&1 – все данные дескриптора 5 перенаправляем на stdout вывод  
echo netology > /proc/$$/fd/5 – оправляем данные в дескриптор 5 (а он уже выводит данные на stdout  

### 8.  
ls -l 5>&2 2>&1 1>&5 | wc –l  
Создаем промежуточный дескриптор 5  

### 9.	
environ  - это набор переменных окружения  
printenv  
env  
(только с разделением переменных)  

### 10.  
/proc/<PID>/cmdline - / proc / [pid] / cmdline - это файл только для чтения, который содержит полную информацию о процессе из командной строки.  
 /proc/<PID>/exe. - это ссылка на полное имя выполняемого файла для программы (процесса)  
### 11.  
grep sse /proc/cpuinfo  
cat /proc/cpuinfo  
sse4_2 hypervisor  
### 12.  
При выполнении ssh localhost 'tty' мы посылаем запрос команды по ssh, при таком запросе нам не выделяется pty. Поэтому и пишется что его нет.  
ssh –t localhost 'tty'  

-t принудительно выделяет терминал  
-t      Force pseudo-terminal allocation.  This can be used to execute arbitrary screen-based programs on a remote machine,  
  which can be very useful, e.g. when implementing menu services.  Multiple -t options force tty allocation, even if ssh has no local tty.  
### 13.  
reptyr 1137  
[-] Timed out waiting for child stop.  
Unable to attach to pid 1137: Operation not permitted  
The kernel denied permission while attaching. If your uid matches  
the target's, check the value of /proc/sys/kernel/yama/ptrace_scope.  
For more information, see /etc/sysctl.d/10-ptrace.conf  

echo "0"|sudo tee /proc/sys/kernel/yama/ptrace_scope  

reptyr 1137  
[-] Timed out waiting for child stop.  

### 14.	
tee linux принимает данные из одного источника и может сохранять их на выходе в нескольких местах.  
Sudo дает необходимые права для записи в файл  
