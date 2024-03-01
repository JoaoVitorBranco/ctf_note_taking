# Questions

## What does the 3-letter acronym FTP stand for?
- file transfer protocol

## Which port does the FTP service listen on usually?
- 21

## What acronym is used for the secure version of FTP?

- sftp

## What is the command we can use to send an ICMP echo request to test our connection to the target?
- ping

## From your scans, what version is FTP running on the target?
- `nmap -p21 $IP -sV`

## From your scans, what OS type is running on the target?
- `sudo nmap -p21 $IP -O`

## What is the command we need to run in order to display the 'ftp' client help menu?
- `ftp -h`

## What is username that is used over FTP when you want to log in without having an account?
- `anonymous`

## What is the response code we get for the FTP message 'Login successful'?
- 230

## There are a couple of commands we can use to list the files and directories available on the FTP server. One is dir. What is the other that is a common way to list files on a Linux system.
- `ls`

## What is the command used to download the file we found on the FTP server?
- `get`

## Submit root flag
- `get flag.txt` and check the flag