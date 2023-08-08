 releavant is medium rated ctf room on tryhackme. 

### enumeration:
##### nmap scan:

![](attachments/relevant%20(medium)%20nmap1.png)
nmap detailed scan:

![](attachments/relevant%20(medium)%20detailed%201.png)


![](attachments/relevant%20(medium)%20detailed%202.png)

which shows port 139,445 running the smb and  port 80 runing the microsoft IIs windows server

    smbclient -L 10.10.103.69 


![](attachments/relevant%20(medium)%20smb%20enum.png)

now running
```
smbclient //10.10.103.69/nt4wrksv
```

which has passwords.txt file
get passwords.txt

which has base64 encoded passwords.txt
- Qm9iIC0gIVBAJCRXMHJEITEyMw==
- QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk

which gives 
- Bob - !P@$$W0rD!123
Bill - Juw4nnaM4n420696969!/$$

smb version: 3.1.1

![](attachments/relevant%20(medium)smbversion.png)


gobuster command 
```
gobuster dir -u http://10.10.168.75:49663/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  -t 50 --no-error
```
