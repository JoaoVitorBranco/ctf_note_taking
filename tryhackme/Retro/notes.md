# Link
- https://tryhackme.com/r/room/retro

# Info

## IP = $IP

## Creds
- wade:parzival

# Ports

## Port Scanning
```bash
PORT     STATE SERVICE
80/tcp   open  http
3389/tcp open  ms-wbt-server
```

## 80
```bash
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-title: IIS Windows Server
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
```

### `gobuster`
```
/retro                (Status: 301) [Size: 149] [--> http://$IP/retro/]
```

### http://$IP:80/retro

- login page
    - wordpress
- trying hydra for _wade_ account (verified account)

### `hydra`
- dead end

### comment in _http://$IP/retro/index.php/2019/12/09/ready-player-one/_
- parzival is the password

## 3389
```
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: RETROWEB
|   NetBIOS_Domain_Name: RETROWEB
|   NetBIOS_Computer_Name: RETROWEB
|   DNS_Domain_Name: RetroWeb
|   DNS_Computer_Name: RetroWeb
|   Product_Version: 10.0.14393
|_  System_Time: 2024-04-06T21:42:29+00:00
|_ssl-date: 2024-04-06T21:42:34+00:00; +1s from scanner time.
| ssl-cert: Subject: commonName=RetroWeb
| Not valid before: 2024-04-05T21:38:18
|_Not valid after:  2024-10-05T21:38:18
```

### Login via _rdp_
- `xfreerdp /u:wade /v:$IP`

## Reverse Shell in logged Wordpress
- https://www.hackingarticles.in/wordpress-reverse-shell/
- http://$IP/retro/wp-admin/theme-editor.php?file=404.php&theme=90s-retro
- https://github.com/pentestmonkey/php-reverse-shell
- must change a little bit the rev-shell file for running correctly on windows server
- https://github.com/d4t4s3c/Offensive-Reverse-Shell-Cheat-Sheet/blob/master/reverse-shell.bat
- didn-t work... trying another thing: https://www.hackingarticles.in/get-reverse-shell-via-windows-one-liner/
    - "Batch File" part

# PrivEsc

- Using CVE-2017-0213
- https://github.com/SecWiki/windows-kernel-exploits/tree/master/CVE-2017-0213