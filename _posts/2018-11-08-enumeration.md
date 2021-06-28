---
layout: post
title:  "Enumeration"
date:   2018-11-08 10:00:00 +1100
categories: [enumeration, security]
permalink: /:title:output_ext
comments: true
---
<br>
Idea taken from [here](https://github.com/jivoi/pentest/blob/master/mix_port_scan.sh)

-----
**FTP**
```python
[>] Use nmap scripts for further enumeration or hydra for password attack, e.g
[=] nmap -n -sV -Pn -pPORT --script=ftp* -oN 'IP/ftp_PORT.nmap' IP
[=] hydra -L /usr/share/seclists/Usernames/Names/names.txt -P /usr/share/seclists/Passwords/CommonCreds/10k_most_common.txt -f -o IP/ftphydra -u IP -s PORT ftp
```
-----
**HTTP**
```python
[>] Use nmap scripts, nikto or gobuster for further HTTP enumeration, e.g
[=] nmap -n -sV -Pn -pPORT --script="http-brute,http-svn-enum,http-svn-info,http-git,http-userdir-enum,http-apache-negotiation,http-backup-finder,http-config-backup,http-default-accounts,http-methods,http-method-tamper,http-passwd,http-robots.txt,http-iis-webdav-vuln,http-vuln-cve2009-3960,http-vuln-cve2010-0738,http-vuln-cve2011-3368,http-vuln-cve2012-1823,http-vuln-cve2013-0156,http-waf-detect,http-waf-fingerprint,ssl-enum-ciphers,ssl-known-key" -oN 'IP/http_PORT.nmap' IP
[=] nmap -n -sV -Pn -pPORT --script=http-shellshock-spider -oN 'IP/_http_shellshock_PORT.nmap' IP
[=] nikto -h IP -p PORT | tee IP/nikto_PORT
[=] whatweb --color=never --no-errors "http://IP:PORT" | tee IP/whatweb_PORT
[=] wpscan --url http://10.11.x.x/wp --enumerate uap
[=] wpscan -u "http://IP" | tee IP/wpscan_PORT
[=] wpscan -u "http://IP" --wordlist /usr/share/seclists/Passwords/CommonCreds/10k-most-common.txt --username admin | tee IP/wpscan_brute_PORT[=] gobuster -l -x php,txt,apsx,asp,html -u "http://IP:PORT/" -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -t 10 | tee IP/gobuster_ext_PORT
[=] gobuster -u "http://IP:PORT/" -w /usr/share/seclists/Discovery/Web_Content/common.txt | tee IP/gobuster_common_PORT
[=] gobuster -u http://IP:PORT/ -w /usr/share/wordlists/dirb/big.txt
[=] gobuster -u "http://IP:PORT/" -w /root/wordlists/dicc.txt | tee IP/gobuster_dicc_PORT[=] gobuster -u "http://IP:PORT/" -w /root/wordlists/fuzz.txt | tee IP/gobuster_fuzz_PORT[=] medusa -U /usr/share/wordlists/metasploit/http_default_users.txt -P /usr/share/wordlists/metasploit/http_default_pass.txt -e ns -h IP - PORT -M http -m DIR:secret -f
[=] curl -i -X OPTIONS IP  (to check for HTTP options)
```
-----
**HTTPS**
```python
[>] Use nmap scripts, nikto or gobuster for further HTTPS enumeration, e.g
[=] nmap -n -sV -Pn -pPORT --script="http-brute,http-svn-enum,http-svn-info,http-git,ssl-heartbleed,http-userdir-enum,http-apache-negotiation,http-backup-finder,http-config-backup,http-default-accounts,http-methods,http-method-tamper,http-passwd,http-robots.txt,http-iis-webdav-vuln,http-vuln-cve2009-3960,http-vuln-cve2010-0738,http-vuln-cve2011-3368,http-vuln-cve2012-1823,http-vuln-cve2013-0156,http-waf-detect,http-waf-fingerprint,ssl-enum-ciphers,ssl-known-key" -oN 'IP/https_PORT.nmap' IP
[=] nmap -n -sV -Pn -pPORT --script=http-shellshock-spider -oN 'IP/http_shellshock_PORT.nmap' IP
[=] nikto -h IP -p PORT | tee IP/nikto_https_PORT.txt[=] whatweb --color=never --no-errors https://IP:PORT | tee IP/whatweb_https_PORT[=] wpscan -u https://IP | tee IP/wpscan_https_PORT
[=] wpscan --url https://10.11.x.x/wp --enumerate uap
[=] wpscan -u https://IP --wordlist /usr/share/seclists/Passwords/CommonCreds/10k_most_common.txt --username admin | tee IP/wpscan_https_brute_PORT
[=] gobuster -u https://IP:PORT/ -w /usr/share/seclists/Discovery/Web-Content/RobotsDisallowed-Top1000.txt | tee IP/gobuster_https_top1000_PORT
[=] gobuster -u https://IP:PORT/ -w /usr/share/seclists/Discovery/Web_Content/common.txt | tee IP/gobuster_https_common_PORT
[=] gobuster -u https://IP:PORT/ -w /root/wordlists/dicc.txt | tee IP/gobuster_https_dicc_PORT
[=] gobuster -u https://IP:PORT/ -w /root/wordlists/fuzz.txt | tee IP/gobuster_https_fuzz_PORT
[=] gobuster -u https://IP:PORT/ -w /usr/share/wordlists/dirb/big.txt
[=] medusa -U /usr/share/wordlists/metasploit/http_default_users.txt -P /usr/share/wordlists/metasploit/http_default_pass.txt -e ns -h IP - PORT -M http -m DIR:secret -f
```
-----
**CASSANDRA (9160)**
```python
[>] Use nmap scripts for further CASSANDRA enumeration, e.g
[=] nmap -n -sV -Pn -p PORT --script=cassandra* -oN 'IP/cassandra_PORT.nmap' IP
```
-----
**MONGODB (27017)**
```python
[>] Use nmap scripts for further MONGODB enumeration, e.g
[=] nmap -n -sV -Pn -p PORT --script=mongodb* -oN 'IP/mongodb_PORT.nmap' IP
```
-----
**ORACLE (1521)**
```python
[>] Use nmap scripts for further ORACLE enumeration, e.g
[=] nmap -n -sV -Pn -p PORT --script=oracle* -oN 'IP/oracle_PORT.nmap' IP
```
-----
**MYSQL**
```python
[>] Check out the server for web applications with sqli vulnerabilities
[>] Use nmap scripts for further MYSQL enumeration, e.g
[=] nmap -n -sV -Pn -p PORT --script=mysql* -oN 'IP/mysql_PORT.nmap' IP
```
-----
**MSSQL**
```python
[>] Use nmap scripts for further MSSQL enumeration, e.g
[=] nmap -n -sV -Pn -pPORT --script=ms-sql-ntlm-info,ms-sql-brute,ms-sql-empty-password,ms-sql-info,ms-sql-config,ms-sql-dump-hashes -oN IP/mssql_PORT.nmap IP
[=] nmap -n -Pn -pPORT --script=ms-sql-xp-cmdshell --script-args mssql.username=sa,mssql.password=password,mssql.instance-port=PORT,ms-sql-xp-cmdshell.cmd='ipconfig' -oN IP/mssql_cmdshell_PORT.nmap IP
```
-----
**SMB**
```python
[>] Use nmap scripts or enum4linux for further enumeration, e.g
[=] nmap -n -sV -Pn -pT:139,PORT,U:137 --script=smb-enum-shares,smb-enum-domains,smb-enum-groups,smb-enum-processes,smb-enum-sessions,smb-ls,smb-mbenum,smb-os-discovery,smb-print-text,smb-security-mode,smb-server-stats,smb-system-info,smb-vuln* --script-args=unsafe=1 -oN 'IP/smb_PORT.nmap' IP
[=] enum4linux IP | tee IP/enum4linux
[=] smbclient -L \\IP -I IP -N | tee IP/smbclient
[=] smbclient //10.10.10.97/new-share -I 10.10.10.97 -U username
[=] smbclient //10.10.10.97/c$ -I 10.10.10.97 -W domain_name -U Administrator
[=] nmblookup -A target
[=] smbclient //MOUNT/share -I target -N
[=] rpcclient -U "" target[=] nbtscan 192.168.1.0/24[=] smbclient -L //target_ip
[=] nmap -sU -sS --script=smb-enum-users -p U:137,T:139 target_ip- Windows: net use \TARGET\IPC$ "" /u:""- Linux: smbclient -L //192.168.99.131
https://www.madirish.net/59
```
-----
**NFS**
```python
rpcinfo -p IP
showmount IP -a or -d or -e

To mount an NFS share use the following after first creating a directory on your local machine:
[root@attacker~]#mount -t nfs 192.168.0.1:/export/home /local_dir
http://www.vulnerabilityassessment.co.uk/nfs.htm

**ruser (32823 )**

rusers -l IP
```
-----
**LDAP**
```python
[>] Use nmap scripts or ldapsearch for further LDAP enumeration, e.g
[=] nmap -n -sV -Pn -pPORT --script=ldap-search.nse -oN 'IP/ldap_PORT.nmap' IP
[=] ldapsearch -H ldap://IP -x -LLL -s base -b  supportedSASLMechanisms | tee IP/ldapsearch_PORT
```
-----
**RDP**
```python
[>] Use ncrackpassword cracking, e.g
[=] ncrack -vv --user Administrator -P /usr/share/wordlists/rockyou.txt rdp://IP
```
-----
**SMTP**
```python
[>] Use nmap scripts or smtp-user-enum for further SMTP enumeration, e.g
[=] nmap -n -sV -Pn -pPORT --script=smtp* -oN 'IP/smtp_PORT.nmap' IP
[=] smtp-user-enum -M VRFY -U /usr/share/seclists/Usernames/Names/names.txt -t IP -p PORT | tee PORT/smtp_enum_IP
```
-----
**SNMP**
```python
[>] Use nmap scripts, onesixtyone or snmwalk for further enumeration, e.g
[=] nmap -n -sU -sV -Pn -pU:PORT --script=snmp-sysdescr,snmp-info,snmp-netstat,snmp-processes -oN 'IP/snmp_PORT.nmap' IP
[=] onesixtyone -c public IP | tee IP/161[=] snmpwalk -c public -v1 IP | tee IP/snmpwalk
[=] snmpwalk -c public -v1 IP 1.3.6.1.4.1.77.1.2.25 | tee IP/snmp_users
[=] snmpwalk -c public -v1 IP 1.3.6.1.2.1.6.13.1.3 | tee IP/snmp_ports
[=] snmpwalk -c public -v1 IP 1.3.6.1.2.1.25.4.2.1.2 | tee IP/snmp_process
[=] snmpwalk -c public -v1 IP 1.3.6.1.2.1.25.6.3.1.2 | tee IP/snmp_software
```
-----
**SSH**
```python
[>] Use medusa or hydra (unreliable) for password cracking, e.g
[=] medusa -u root -P /usr/share/wordlists/rockyou.txt -e ns -h IP - PORT -M ssh -f
[=] medusa -U /usr/share/seclists/Usernames/top_shortlist.txt -P /usr/share/seclists/Passwords/CommonCreds/best110.txt -e ns -h IP - PORT -M ssh -f
[=] hydra -f -V -t 1 -l root -P /usr/share/wordlists/rockyou.txt -s PORT IP ssh
```
-----
**Telnet**
```python
[>] Use medusa or hydra (unreliable) for password cracking, e.g
[=] medusa -U /root/wordlists/mirai_username.list -P /root/wordlists/mirai_password.list -e ns -h IP - PORT -M telnet -t1 -f
```
-----
**DNS**
```python
[>] Use nmap scripts for further DNS enumeration, e.g[=] nmap -n -sV -Pn -pPORT --script=dns* -oN 'IP/dns_PORT.nmap' IP
```
