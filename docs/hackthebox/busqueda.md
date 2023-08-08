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



privilege escalation

![](attachments/linpeas%20finding.png)

ss -ltnp for running process got the json base64 decoded file 

{"name":"Gitea: Git with a cup of tea","short_name":"Gitea: Git with a cup of tea","start_url":"http://gitea.searcher.htb/","icons":[{"src":"http://gitea.searcher.htb/assets/img/logo.png","type":"image/png","sizes":"512x512"},{"src":"http://gitea.searcher.htb/assets/img/logo.svg","type":"image/svg+xml","sizes":"512x512"}]}





