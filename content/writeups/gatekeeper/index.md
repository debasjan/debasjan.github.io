---
title: "Gatekeeper"
date: 2025-01-01
hideDate: true
draft: false
tags: ["others", "practice", "medium"]
categories: ["writeups"]
summary: "Deploy the machine when you are ready to release the Gatekeeper."
ShowToc: true
TocOpen: false
platformLabel: "TryHackMe"
cover:
  image: "00-card.png"
  alt: "Gatekeeper"
  relative: true
---

## **Approach the Gates**

Deploy the machine when you are ready to release the Gatekeeper.

## **Defeat the Gatekeeper and pass through the fire.**

Defeat the Gatekeeper to break the chains.  But beware, fire awaits on the other side.  

###### Answer the questions below

Locate and find the User Flag.  
{H4lf_W4y_Th3r3}

Locate and find the Root Flag
{Th3_M4y0r_C0ngr4tul4t3s_U}

![nmap scan](nmap-scan.png)

![nmap scan 2](nmap-scan-2.png)


![smb enum](smb-enum.png)

![test application](test-application.png)
![fuzzing](fuzzing.png)

![offset](offset.png)

![bad char](bad-char.png)

![buffer script shell](buffer-script-shell.png)

![gatekeeper machine shell](gatekeeper-machine-shell.png)
![user flag](user-flag.png)


![firefox.lnk](firefox-lnk.png)![firefox profile copy to shared and mounted share](firefox-profile-copy-to-shared-and-mounted-share.png)

![share](share.png)


![shared files](shared-files.png)
![firefox decrypt script](firefox-decrypt-script.png)
![firefox credentials](firefox-credentials.png)
![root flag](root-flag.png)
