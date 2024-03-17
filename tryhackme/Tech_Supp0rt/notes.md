# Link
- https://tryhackme.com/room/techsupp0rt1

# Dados
## IP = $IP
## Creds
- admin:Scam2021
- support:
- scamsite:ImAScammerLOL!123!

## Versions
- WordPress/5.7.2

# Port Scanning
## Ports: 22,80,445,139

# Port Analysing
- 22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
- 80/tcp  open  http        Apache httpd 2.4.18 ((Ubuntu))
- 139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
- 445/tcp open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)

## Port 80
### Gobuster

- /phpinfo.php
- /wordpress/index.php/index.php/about/
- /subrion/panel/

### Web analysing

- $IP/wordpress/wp-admin/
- http://$IP/wordpress/wp-login.php?redirect_to=http%3A%2F%2F$IP%2Fwordpress%2Fwp-admin%2F&reauth=1
	- login page
- http://$IP/wordpress/wp-includes

### wpscan
- user: support

### burp (login page)

log=support&pwd=admin&wp-submit=Log+In&redirect_to=http%3A%2F%2F$IP%2Fwordpress%2Fwp-admin%2F&testcookie=1

### decrypt code for "admin"
- https://gchq.github.io/CyberChef/#recipe=From_Base58('123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz',true/disabled)Magic(3,false,false,'')&input=N3NLdm50WGRQRUpheGF6Y2U5UFhpMjR6YUZyTGlLV0Nr
- pass: Scam2021

### using the exploit on /subrion/panel/
- https://www.exploit-db.com/exploits/49876


## Port 445
Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\$IP\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: IPC Service (TechSupport server (Samba, Ubuntu))
|     Users: 1
|     Max Users: <unlimited>
|     Path: C:\tmp
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\$IP\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\var\lib\samba\printers
|     Anonymous access: <none>
|     Current user access: <none>
|   \\$IP\websvr: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\home\scamsite\websvr
|     Anonymous access: READ/WRITE
|_    Current user access: READ/WRITE

### Entering in the \websvr share -> has a "enter.txt" file
- "Subrion creds" -> some CMS
- using a exploit: https://www.exploit-db.com/exploits/49876
- manual rev-shell: http://$IP/subrion/uploads/rev.phar
	- https://vk9-sec.com/subrion-cms-4-2-1-arbitrary-file-upload-authenticated-2018-19422/

# Priv Esc 
## font: using exploit in Subrion CMS
## font: https://vk9-sec.com/subrion-cms-4-2-1-arbitrary-file-upload-authenticated-2018-19422/

## Using LinEnum
### DB Creds
define( 'DB_NAME', 'wpdb' );
define( 'DB_USER', 'support' );
define( 'DB_PASSWORD', 'ImAScammerLOL!123!' );
define( 'DB_HOST', 'localhost' );

### interesting files
-------- /usr/bin/gettext.sh 
-------- /var/www/html/subrion/backup/.htaccess


## TODO
- MySQl (with creds)
- cronjobs