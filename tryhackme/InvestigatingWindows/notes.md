# Info
- *IP*: $IP
- *Username*: Administrator
- *Password*: letmein123!

# Task

## Question 1 : _Whats the version and year of the windows machine?_
```cmd
C:\Users\Administrator> systeminfo
(...)
```
- flag{XXXX}

## Question 2 : _Which user logged in last?_
```powershell
PS C:\Users\Administrator> Get-LocalUser | Select Name, Lastlogon
(...)
```

- flag{XXXX}

## Question 3 : _When did John log onto the system last?_
```powershell
PS C:\Users\Administrator> Get-LocalUser | Select *
(...)
```

- 03/02/2019 5:48:32 PM

## Question 4 : _What IP does the system connect to when it first starts?_

- Check the terminal that opens when the machine starts
- flag{XXXX}

## Question 5 : _What two accounts had administrative privileges (other than the Administrator user)?_
```cmd
C:\Users\Administrator>net user Jenny
(...)

C:\Users\Administrator>net user Guest
(...)
```

- flag{XXXX}

## Question 6 : _Whats the name of the scheduled task that is malicous._
```powershell
PS C:\Users\Administrator> Get-ScheduledTask | Select TaskName
(...)
```

- flag{XXXX}

## Question 7 : _What file was the task trying to run daily?_
```cmd
C:\Users\Administrator>set taskName="Clean file system"

C:\Users\Administrator>schtasks /query /tn %taskName% /v /fo list
(...)
```

- flag{XXXX}

## Question 8 : _What port did this file listen locally for?_
```cmd
C:\Users\Administrator>set taskName="Clean file system"

C:\Users\Administrator>schtasks /query /tn %taskName% /v /fo list
(...)
```

- flag{XXXX}

## Question 9 : _When did Jenny last logon?_
```powershell
PS C:\Users\Administrator> Get-LocalUser | Select *
(...)
```

- flag{XXXX}

## Question 10 : _At what date did the compromise take place?_

- checking _Local Disk (C:)_, it has a folder called _inetpub_ (strange), that was modified in certain date...
- flag{XXXX}

## Question 11 : _During the compromise, at what time did Windows first assign special privileges to a new logon?_

- Event Viewer
- Create Custom View
- _Logged:_ Custom range ...
- _Events On_ isntead of _First Event_
- From 3/2/2019 4:00:00 PM to 3/2/2019 4:30:00 PM
- In _Event Logs_, select Windows Logs/Security
- Create the Custom View
- Look for _Task Category: Security Group Management_ 
- flag{XXXX}

## Question 12 : _What tool was used to get Windows passwords?_

- A terminal popups repeatedly... the file executed is in /TMP folder
- Open _C:\TMP_ and check _mim-out_
- flag{XXXX}

## Question 13 : _What was the attackers external control and command servers IP?_

- Checking _C:\Windows\System32\drivers\etc\hosts_, a IP appears to be _google.com_ IP... but isn't
- To check, it's possible to `ping google.com` from my machine and check
- flag{XXXX}

## Question 14 : _What was the extension name of the shell uploaded via the servers website?_

- Inside folder _C:\inetpub\wwwroot_, the most suspecious extension is _.jsp_ (instead of _.gif_)
- flag{XXXX}

## Question 15 : _What was the last port the attacker opened?_

- Access _Windows FIrewall with Advanced Security_
- Check _Inbound Rules_
- Check _Filter By Group\Rules Without a Group_
- Double-click in _Allow outside connections for development_
- Check the _Protocols and Ports_ tab
- flag{XXXX}

## Question 16 : _Check for DNS poisoning, what site was targeted?_

- Check _question 13_
- flag{XXXX}
