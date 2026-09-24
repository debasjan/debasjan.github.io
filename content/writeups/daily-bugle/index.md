---
title: "Daily Bugle — TryHackMe"
date: 2025-01-01
hideDate: true
draft: false
tags: ["tryhackme", "medium"]
categories: ["writeups"]
summary: "Compromise a Joomla CMS account via SQLi, practise cracking hashes and escalate your privileges by taking advantage of yum."
ShowToc: true
TocOpen: false
cover:
  image: "00-card.png"
  alt: "Daily Bugle"
  relative: true
---

Compromise a Joomla CMS account via SQLi, practise cracking hashes and escalate your privileges by taking advantage of yum.

## **Deploy**

![daily bugle](daily-bugle.png)

###### Answer the questions below

Access the web server, who robbed the bank?
Spiderman

![scan](scan.png)

![port 80 web](port-80-web.png)


## **Obtain user and root**

![obtain user and root](obtain-user-and-root.png)
Hack into the machine and obtain the root user's credentials.

###### Answer the questions below

What is the Joomla version?
3.7.0

*Instead of using SQLMap, why not use a python script!*  

What is Jonah's cracked password?
spiderman123

What is the user flag?
27a260fe3cba712cfdedb1c86d80442e

What is the root flag?
eec3d53292b1821868266858d7fa6f79

![gobuster common](gobuster-common.png)
![gobuster medium](gobuster-medium.png)
![joomla](joomla.png)
![robots](robots.png)
```
cmseek -u http://(target)
```
![cmseek](cmseek.png)
![searchsploit joomla](searchsploit-joomla.png)

![joomla exploit](joomla-exploit.png)
![joomblah](joomblah.png)
![joomblah 2](joomblah-2.png)
![joomblah hash](joomblah-hash.png)
![cat hash](cat-hash.png)
![hahsid](hahsid.png)
![bcrypt](bcrypt.png)
![john cracked](john-cracked.png)
![joomla jonah](joomla-jonah.png)
![templates](templates.png)
![templates new file](templates-new-file.png)
![create php](create-php.png)
![reverse shell](reverse-shell.png)
![shell](shell.png)
![shell2](shell2.png)
![shell user](shell-user.png)

![upload linpeas](upload-linpeas.png)
![linpeas chmod](linpeas-chmod.png)
![password](password.png)
![password configuration](password-configuration.png)
![jjameson](jjameson.png)
![jjameson user flag](jjameson-user-flag.png)
![jjameson linpeas](jjameson-linpeas.png)
![bin yum](bin-yum.png)
![gtfo yum](gtfo-yum.png)
![root shell](root-shell.png)
![root flag](root-flag.png)
