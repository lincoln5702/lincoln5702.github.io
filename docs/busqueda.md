http://searcher.htb/ found when http://ip address is submitted to the browser.
now updating the /etc/hosts on the by adding the 
ip address searcher.htb
allows browser opening

nmap results
![](attachments/busqueda%20nmap.png)

running a gobuster returns 
/search


on the bottom of the website listed as :
made with flask and searcher 2.4.0 
searching the searcher 2.4.0 gives github pages related to the exploit regarding searcher 2.4.0
running the exploit returns shell 

and just stabilised the shel
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
control +z 
stty raw -echo && fg


cd'ed to ~
cat user.txt and returned me the user flag







