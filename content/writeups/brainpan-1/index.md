---
title: "Brainpan 1"
date: 2025-01-01
hideDate: true
draft: false
tags: ["others", "practice", "medium"]
categories: ["writeups"]
summary: "Reverse engineer a Windows executable, find a buffer overflow and exploit it on a Linux machine."
ShowToc: true
TocOpen: false
platformLabel: "TryHackMe"
cover:
  image: "00-card.png"
  alt: "Brainpan 1"
  relative: true
---

Reverse engineer a Windows executable, find a buffer overflow and exploit it on a Linux machine.


## **Deploy and compromise the machine**

Brainpan is perfect for OSCP practice and has been highly recommended to complete before the exam. Exploit a buffer overflow vulnerability by analyzing a Windows _exe_cutable on a Linux machine. If you get stuck on this machine, don't give up (or look at writeups), just try harder. 

  
All credit to [superkojiman](https://www.vulnhub.com/entry/brainpan-1,51/) - This machine is used here with the explicit permission of the creator <3

###### Answer the questions below

Deploy the machine.

Gain initial access

Escalate your privileges to root.

![nmap command](nmap command.png)
![nmap scan](nmap scan.png)
![port 9999 check](port 9999 check.png)
![port 10000 check](port 10000 check.png)
![gobuster port 10000](gobuster port 10000.png)
![brianpan exe](brianpan exe.png)
![brainpan export to win](brainpan export to win.png)
![fuzzing](fuzzing.png)
![pattern create 900](pattern create 900.png)
![offset](offset.png)
![byterray](byterray.png)
![badchar unmodfied](badchar unmodfied.png)
![jump point](jump point.png)
![msfvenom payload](msfvenom payload.png)
![buffer script local](buffer script local.png)
![msfvenom payload 2](msfvenom payload 2.png)
![payload script + shell machine](payload script + shell machine.png)
![shell](shell.png)
![sudo -l](sudo -l.png)
![root](root.png)
