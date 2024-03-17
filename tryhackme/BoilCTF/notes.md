# Boiler CTF
- https://tryhackme.com/room/boilerctf2

# Info 

## IP = $IP

## Creds

- basterd:superduperp@$$
- stoner

# Port

## Port Scanning
```
PORT      STATE SERVICE
21/tcp    open  ftp
80/tcp    open  http
10000/tcp open  snet-sensor-mgmt
55007/tcp open  unknown
```

## 21
```
21/tcp    open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:$IP
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
```

### Anonymous

- _.info.txt_
    - `Whfg jnagrq gb frr vs lbh svaq vg. Yby. Erzrzore: Rahzrengvba vf gur xrl!`
    - *ROT 13: * `Just wanted to see if you find it. Lol. Remember: Enumeration is the key!`


## 80
```
80/tcp    open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-robots.txt: 1 disallowed entry 
|_/
```

### `gobuster`
```
/.hta                 (Status: 403) [Size: 292]
/.htpasswd            (Status: 403) [Size: 297]
/.htaccess            (Status: 403) [Size: 297]
/index.html           (Status: 200) [Size: 11321]
/joomla               (Status: 301) [Size: 315] [--> http://$IP/joomla/]
/manual               (Status: 301) [Size: 315] [--> http://$IP/manual/]                                                                        
/robots.txt           (Status: 200) [Size: 257]
/server-status        (Status: 403) [Size: 301]
```

### _robots.txt_
```
/tmp
/.ssh
/yellow
/not
/a+rabbit
/hole
/or
/is
/it
```

- not found any of this files

### _/joomla_

- login form

### gobuster _/joomla_

- http://$IP/joomla/administrator/
- login form

- http://$IP/joomla/_test/
- maybe vulnerable 

### joomla version
- https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/joomla
- 3.6 / 3.8 / 3.1 ?

### exploiting sar2HTMLshell
- https://raw.githubusercontent.com/AssassinUKG/sar2HTML/main/sar2HTMLshell.py
- got a shell!

### searching in the server
- _/var/www/configuration.php_
```
public $user = 'joomlauser';
public $password = 'passwordz';
```

### searching in server
- log.txt
```
$ cat log.txt
Aug 20 11:16:26 parrot sshd[2443]: Server listening on 0.0.0.0 port 22.
Aug 20 11:16:26 parrot sshd[2443]: Server listening on :: port 22.
Aug 20 11:16:35 parrot sshd[2451]: Accepted password for basterd from 10.1.1.1 port 49824 ssh2 #pass: superduperp@$$
Aug 20 11:16:35 parrot sshd[2451]: pam_unix(sshd:session): session opened for user pentest by (uid=0)
Aug 20 11:16:36 parrot sshd[2466]: Received disconnect from 10.10.170.50 port 49824:11: disconnected by user
Aug 20 11:16:36 parrot sshd[2466]: Disconnected from user pentest 10.10.170.50 port 49824
Aug 20 11:16:36 parrot sshd[2451]: pam_unix(sshd:session): session closed for user pentest
Aug 20 12:24:38 parrot sshd[2443]: Received signal 15; terminating.
```

- configuration.php


## 10000
```
10000/tcp open  http    MiniServ 1.930 (Webmin httpd)
|_http-server-header: MiniServ/1.930
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
```

## 55007
```
55007/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e3abe1392d95eb135516d6ce8df911e5 (RSA)
|   256 aedef2bbb78a00702074567625c0df38 (ECDSA)
|_  256 252583f2a7758aa046b2127004685ccb (ED25519)
```