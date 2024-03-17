# Link
- https://app.hackthebox.com/starting-point

# Questions

## What does the 3-letter acronym SMB stand for?
- Server Message Block

## What port does SMB use to operate at?
- 445

## What is the service name for port 445 that came up in our Nmap scan?
- microsoft-ds

## What is the 'flag' or 'switch' that we can use with the smbclient utility to 'list' the available shares on Dancing?
- `-L`

## How many shares are there on Dancing?
- `smbclient -L $IP`

## What is the name of the share we are able to access in the end with a blank password?
- The share without '$' as posfix

## What is the command we can use within the SMB shell to download the files we find?
- `get`

## Submit root flag
- `smbclient \\\\$IP\\WorkShares`
- `ls`
- `get James.P/flag.txt`