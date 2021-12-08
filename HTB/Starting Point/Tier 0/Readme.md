# HTB - Starting points

## Tier 0 - beginner tips

### Check what is behind port

```https://www.speedguide.net/port.php?port=135```

### FTP (21/20) 

```bash
$ ftp <ip>
// Get - download file
```

### SMB (445)

```bash 
$ smbclient -L \\\\<ip>\\
$ smbclient \\\\<ip>\\<dir>
// Get - download file
```

### RDP (3389)

```bash
$ xfreerdp /u:JohnDoe /p:Pwd123! /w:1366 /h:768 /v:<ip>:<port>
```

### Web enumeration - Dir busting

```bash
$ gobuster dir -w /usr/share/wordlists/dirb/common.txt -u http://<IP>
```