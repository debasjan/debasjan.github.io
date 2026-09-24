---
title: "Brainstorm — TryHackMe"
date: 2025-01-01
hideDate: true
draft: false
tags: ["tryhackme", "medium"]
categories: ["writeups"]
summary: "Reverse engineer a chat program and write a script to exploit a Windows machine."
ShowToc: true
TocOpen: false
cover:
  image: "00-card.png"
  alt: "Brainstorm"
  relative: true
---

Reverse engineer a chat program and write a script to exploit a Windows machine.

## **Deploy Machine and Scan Network**

![brainstorm scan](brainstorm-scan.png)

## **Accessing Files**

![braintstorm ftp](braintstorm-ftp.png)

## **Access**

After enumeration, you now must have noticed that the service interacting on the strange port is some how related to the files you found! Is there anyway you can exploit that strange service to gain access to the system? 

It is worth using a Python script to try out different payloads to gain access! You can even use the files to locally try the exploit. 

If you've not done buffer overflows before, check [this](https://tryhackme.com/room/bof1) room out!

###### Answer the questions below

Read the description.

After testing for overflow, by entering a large number of characters, determine the EIP offset.  

Now you know that you can overflow a buffer and potentially control execution, you need to find a function where ASLR/DEP is not enabled. Why not check the DLL file.  

Since this would work, you can try generate some shellcode - use msfvenom to generate shellcode for windows.  

After gaining access, what is the content of the root.txt file?
5b1001de5a44eca47eee71e7942a8f8a

![brainstorm scan](brainstorm-scan.png)
![braintstorm ftp](braintstorm-ftp.png)


![fuzzing brainstorm](fuzzing-brainstorm.png)

![eip down](eip-down.png)
![offset 2012 brainstorm](offset-2012-brainstorm.png)
![badchar](badchar.png)
![jmp point](jmp-point.png)
![payload](payload.png)
![sending payload](sending-payload.png)
![app running](app-running.png)
![shell](shell.png)
![run chatserver get shell](run-chatserver-get-shell.png)
![new payload](new-payload.png)
![send buffer](send-buffer.png)
![shell machine](shell-machine.png)
![root flag](root-flag.png)
