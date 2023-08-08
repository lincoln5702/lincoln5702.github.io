![](attachments/Root%20me%20ctf%20first%20nmap%20scan.png)

![](attachments/Root%20me%20ctf%20nmap%20detailed%20scan.png)

/panel file found from gobuster
![](attachments/Root%20me%20ctf%20gobuster.png)

file upload vulnerability is found 
uploading the php reverse shell
however php not allowed so i changed the file name to php-reverse-shell.txt.php  which is also not allowed
however  php-reverse-shell.phtml is allowed to upload
and setting up the netcat listener i got the shell on my machine 


PRIVILEGE ESCALATION:
sudo -l and sudo bash asks for password
running find / -type f -perm -04000 2>/dev/null
gives unusual python with suid bit set
again digging on teh gtfobins gives result :

    python -c 'import os; os.execl("/bin/sh", "sh", "-p

which gave us root shell and the rootflag
