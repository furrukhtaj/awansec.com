---
layout: post
title:  "Linux Privilege Escalation"
date:   2018-11-09 10:00:00 +1100
categories: [offensive, security, privilege-escalation]
permalink: /:title:output_ext
comments: true
---
{% assign colr = "#009900" %}
<br>
\-- This is the ugliest post as I haven't put much thought into it.<br>
\-- There are a couple things I do for Linux Privilege Escalation:
* ```sudo -l``` If it doesn't ask for a password, we will be presented with the commands/executables we can run as root.
* Check permissions on shadow and passwd files

    ```ls -l /etc/passwd```<br>
    ```ls -l /etc/shadow```

* Check for kernel version: ```uname -a```

\-- [g0tmi1k’s Blog](http://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/)<br>
\-- Run the [LinuxPrivChecker Python Script](http://www.securitysift.com/download/linuxprivchecker.py)

    uname -a
    cat /etc/issue
    cat /etc/*-release
    mount
    sudo -l
    ps aux
    ls -l /etc/cron*

\-- Find starting at root (/), SGID or SUID, not Symbolic links, only 3 folders deep, list with more detail and hide any errors (e.g. permission denied)

    find / -perm -g=s -o -perm -4000 ! -type l -maxdepth 3 -exec ls -ld {} ;2>/dev/null

<br>
#### <span style="color:{{ colr }}">Other Resources:</span>
<br>
**[BeRoot Project](https://github.com/AlessandroZ/BeRoot)**
<br>
https://github.com/mubix/post-exploitation/wiki/Linux-Post-Exploitation-Command-List<br>
https://github.com/sheimo/oscp/blob/master/linux-template.md
