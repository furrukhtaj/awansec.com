---
layout: post
title:  "OSCP Review"
date:   2018-11-07 10:00:00 +1100
categories: [oscp, hacking, offensive, security]
permalink: /:title:output_ext
comments: true
---

<!--super easy color assignment for all headings-->
{% assign colr = "#009900" %}
<br>
**TL;DR:** It was a long 7 month journey but on **3rd of November** I passed and became an **OSCP** on my 2nd attempt.

<br>
#### <span style="color:{{ colr }}">Background:</span>
\-- Having a Bachelors' and a Masters' degree in Telecommunication Engineering, I had a good foundation knowlege of TCP/IP stack, programming/scripting languages and the stamina to self-study and do a lot of research (*this is very important for the PWK course*).<br>
\-- Also, I have worked as a Network Engineer, SOC Engineer and a Resident Security Engineer managing and supporting firewalls from different vendors. What this means is that I have experience being part of a blue team and no experience in pentesting whatsoever. I never even did a CTF challenge.<br>
\-- So, basically I went from 0 skills to 1337 h4x0r in 7 months. All you need to do is **Try Harder**

<br>
#### <span style="color:{{ colr }}">About PWK and OSCP:</span>
\-- Penetration Testing with Kali Linux([PWK](https://www.offensive-security.com/information-security-training/penetration-testing-training-kali-linux/)) is the course you take to prepare and sit the exam for Offensive Security Certified Professional ([OSCP](https://www.offensive-security.com/information-security-certifications/oscp-offensive-security-certified-professional/)).<br>
\-- Once you pay the fee and get your starting date and time, you will get the VPN connection info and links to download the study guide (**380 pages**) and videos totaling 7 hours 28 minutes.<br>

**Note:** The course doesn't start right away. You would have to wait 5-10 days for the next slot once you pay the course fee. So, time it accordingly. Same goes for the exam. As it's a tough and a popular one, it's not easy to book a time of your choosing but you could get lucky :)

<br>
#### <span style="color:{{ colr }}">Slow Internet Connection = VPN Issues !!!</span>
\-- As I live on the edge of my closest exchange, I have a very slow internet connection (yeah, this is still a thing in **Australia**)<br>
\-- I had my doubts of doing anything in the labs or the exam as I was getting a latency of 400ms on an average. I checked with the offsec team but since it's a user issue, they can't do anything.<br>
\-- Thankfully it doesn't negatively affect on what I did in the labs or the exam. It actually helped me come up with a good strategy leading to scripting my own enumeration scripts.

<br>
#### <span style="color:{{ colr }}">Total Study Time:</span>
\-- I planned to study and practice everyday for 3-4 hours and 8 hours on the weekends.<br>
\-- Not everything goes according to plan as there were days when I sat for 1 hour to not doing anything the for a couple of days.<br>
\-- Each person is different in picking up things. So, don't feel bad if you take longer to understand a topic and definitely don't skip anything just because you have seen it before.

<br>
#### <span style="color:{{ colr }}">OSCP Lab:</span>
\-- The team @ **offsec** has designed the lab mimicking a real world network where we have lazy admins, poor security practices, DMZ's etc.<br>
\-- Once you get the VPN details, you are presented with a /24 network and you have to find your way in. Doesn't matter where you start from.<br>
\-- I took my time to understand each topic in the Study Guide and did all the exercises for the lab report.<br>
\-- I bought the 3 month lab time along with the course and extended it for another month.<br>
\-- So, in the span of 4 months, I was able to get Admin/root level access to the following (including the big 3):
* alice, phoenix, mike, bob, barry, payday, ralph, pain, alpha, humble, gh0st, Master, leftturn, core, Sufferance, sean

**Note:** If you plan to buy an extension for the labs, make sure to wait for the current one to expire. This way you will get a complimentary **Exam** attempt. Check OffSec's [FAQ](https://www.offensive-security.com/faq/)

<br>
#### <span style="color:{{ colr }}">First Attempt:</span>
\-- Since I was able to root **pain**, **sufferance** and **humble**, I thought I was ready to hit the exam, but was wrong.<br>
\-- The exam VPN info, crendentials, machines to target, their info and the points requirement are emailed at the exact time of exam start data and time.<br>
\-- I got mine at 8am but logged in 15minutes prior because of the new requirement for remote proctoring.<br>
\-- Logged into the remote screen sharing software and the webcam software. The proctor checked the room and once all checks were done (VPN connection, room scanning etc) I started at around 8:15am<br>
\-- I started with the Buffer Overflow machine because I liked this topic a lot in the course and had a good grip on it too and ran my enumeration script for the other boxes.<br>
\-- Overconfidence led me to overlook a few basic things and it took me a very long time to finish this box.<br>

<p align="center"><img style="width: 30%; height: 30%" src="/images/oscp_buffer.jpg"></p>

\-- Once it was done, I took a quick break and went for the other boxes.<br>
\-- I was able to get some info, a few bypasses but nothing useful that could be used to even get an initial foothold **:(** <br>
\-- I took very few breaks and only slept for 2 hours during the exam. This was a mistake as I wasn't resting enough to clear my mind in order to think differently.<br>
\-- At the end, I failed miserably with only having rooted 1 box.<br>
\-- Nonetheless, I compiled and submitted my exam and lab report.

<p align="center"><img style="width: 30%; height: 30%" src="/images/ysnp.png"></p>

#### <span style="color:{{ colr }}">Time to re-think and prepare:</span>
\-- After my first horrible attempt, I took 2 weeks off and booked the exam for 1st of November.<br>
\-- So, I had almost 50 days to prepare for the next attempt.<br>
\-- Going back to the notes for the first attempt, I knew where I was lacking i.e Web Applictions and Enumeration.<br>
\-- Keeping this in mind, I started researching on the best way to enumerate for basic application info and more enumeration on the applications found.<br>
\-- Since my internet connection is really bad, I had a come up with a decent way to run **nmap** to get me the results faster than before.<br>
\-- Usually, when you see a script on github and anywhere else shared on the internet, they don't talk about the latency and just run all the things from nmap against all the machines. I didn't have that priviledge.<br>
\-- This made me come up with a simple approach.<br>
* Scan a system for open ports
* Do a full scan on the open ports

\-- Also, I had to use the the **-min-rtt-timeout 500ms** option of nmap otherwise, it would say that the host is down.

<br>
#### <span style="color:{{ colr }}">Extra Practice:</span>
\-- Since the lab time had ended and I rooted the toughest boxes in the OSCP lab, I decided to go after VulnHub, HTB and in the last few weeks AttackDefence launched.<br>
* **VulnHub:** Kioptrix 1-4, FristiLeaks, Stapler, PwnLab, Kioptrix 2014, Brainpan 1, MrRobot, Vulnix, VulnOS, Wintermute, SickOS1.2<br>
* **HTB:** Jerry, Access, Active, Bounty, SecNotes<br>

\-- A little bit of AttackDeffence for Linux Privilege Escalation and Ippsec videos on HTB walkthroughs, mainly for Windows.

**Note:** Since the OSCP exam has a limit of using Metasploit on only 1 system, I only used it in the labs for the purpose of completing some exercises. Don't rely on it at all. People have made some very cool scripts that are OSCP friendly like the [AutoBlue-MS17-010](https://github.com/3ndG4me/AutoBlue-MS17-010).

<br>
#### <span style="color:{{ colr }}">Second & Final Attempt:</span>
\-- Approaching the exam day, I prepared notes, scripts, practiced Buffer Overflow even more to get the method right.<br>
\-- Main thing to look for in BO are the bad characters. Trying to do it faster might lead you to miss a few in there.<br>
\-- Don't just count the number of characters but how many unique ones are there. Oh...I digress, back to what I did in the exam :)<br>
\-- In my previous exam attempt I had issues copy/pasting from Host (Windows) to Guest VM (Kali Linux) because of the screen sharing software. Keeping this in mind, I made a local website for all my notes so that I can access them from within the Kali VM. (You can do it in a text file but I like fancier things)<br>
\-- Exam Start time was 9am and I logged in at 8:45am for the pre-checks.<br>
\-- Read through the instructions of all the machines and the [OSCP Exam Guide](https://support.offensive-security.com/#!oscp-exam-guide.md)<br>
\-- Started with my favourite exploit and ran my [enumeration](https://github.com/furrukhtaj/Enumerator) script on the rest.<br>
\-- I kept on taking breaks and slept of 3hrs at night. Taking breaks is very important to relax and move away from your system. This allows you to think in a different way or that 'thing' you are looking for might become obvious when you come back even after a short break **:)**<br>
\-- Got 65 points by 4am at which point I was almost certain that I passed. This is because I completed the lab report which can get you 5 extra points.<br>
\-- Took another 1hour break and by 6am I got 75 points. yaay...Passed!!! and only 1 machine was left.<br>
\-- Since I achcieved the passing marks, I went back to all the rooted machines and reviewed the notes to makes sure all the steps have been written properly with lots of screenshots.<br>
\-- I almost re-did all the machines to make sure initial foothold and privilege escalation works just the way I noted down.<br>
\-- At 8:45am, the VPN dropped at which point I had to stop working on the 5th machine.<br>
\-- All was not over yet because now there is the task of writing the Exam report for which I followed OffSec's [template](https://www.offensive-security.com/pwk-online/PWKv1-REPORT.odt)<br>
\-- Submitted the report at 10:30PM on 2nd of November.<br>
\-- After waiting for almost 31hours (this was really painful), I got the good news **:)**

<br>
#### <span style="color:{{ colr }}">Tips for the Exam:</span>
\-- Although I have mentioned a few in my review (more like my 7month story above), but here is what I would advice:
* Try to stay calm and take rest every 2-3 hours. You have plenty of time.
* Don't forget to start Burp Suite before you start browsing the web applications.
* Prepare the Exam report template beforehand so you only focus on pasting in the reults later.
* **ENUMERATE** ... it's not just about running a script or a tool. If it's a web application. Check all the links and source-code for each. Sometimes the answer is in front of you.
* Check robots.txt for web applications (yeah, thanks captain obvious but people tend to overlook basic things under pressure)
* If you find a username on a shared drive (NFS/Samba), try the same as password on FTP/SSH/Web services. It might just work
* Again, Burp Suite is your friend. It's easier to paste in a bigger payload then the web browsers address bar.
* Rotate machines every 3-4 hours (only if you get stuck) to clear your mind.
* Screenshot the main steps like, when you get reverse shell, login via SSH, getting a proof for local user and then the root user with hostname and ip address
    * ```whoami && hostname && cat filename.txt && ip addr```
* Don't over complicate things. Everything in the exam is taught in the course.
* The PWK course and the Labs teach you all the things you need to pass the exam. All that research that goes into understanding each topic and how to root a lab system gives you a ton of experience.
* While working in the labs, don't just start by asking in the forums or on IRC channel. Try it yourself first, when you feel like you can't get anywhere, **Try Harder**.

<p align="center"><img style="width: 30%; height: 30%" src="/images/problems.png"></p>

<br>
#### <span style="color:{{ colr }}">Compiled Exploits:</span>
\-- [Abatchy](https://github.com/abatchy17/WindowsExploits)<br>
\-- [SecWiki](https://github.com/SecWiki/windows-kernel-exploits)

<br>
#### <span style="color:{{ colr }}">Notes:</span>
\-- I used **OneNote** for course, lab and exam and the main points that helped me through the exam are as follows:
* [Enumeration]({% post_url 2018-11-08-enumeration %})
* [Buffer Overflow]({% post_url 2018-11-08-buffer-overflow %})
* [Linux Privilege Escalation]({% post_url 2018-11-09-linux-priv-esc %})
* [Windows Privilege Escalation]({% post_url 2018-11-08-windows-priv-esc %})
* [Escaping and Spawning Interactive TTY Shells]({% post_url 2018-11-08-escaping-and-spawning-interactive-shells %})
* [SQLi]({% post_url 2018-11-08-sql-injection %})
* [Reverse Shell 1337 Sh33t]({% post_url 2018-11-09-reverse-shell %})
* [File Transfer]({% post_url 2018-11-09-file-transfer %})
* [Exploiting SUID Executables for Privilege Escalation]({% post_url 2018-11-09-exploiting-suid %})
* [Creating Metasploit Paylods]({% post_url 2018-11-09-metasploit-payload %})


<br>
#### <span style="color:{{ colr }}">Other Resources</span>
\-- If you want to start from scratch, following blogs have a very good list of resources:<br>
[Jack Halon's OSCP Review](https://jhalon.github.io/OSCP-Review/)<br>
[Abatchy's blog on Preparing for OSCP](https://www.abatchy.com/2017/03/how-to-prepare-for-pwkoscp-noob)
