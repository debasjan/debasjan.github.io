---
title: "Dev"
date: 2025-01-01
hideDate: true
draft: false
tags: ["others", "practice", "easy"]
categories: ["writeups"]
summary: "We're working on gaining root access to a machine called Dev from TCM Security. It's not widely available or discussed elsewhere, making it a great starting point for beginners in penetration testing."
ShowToc: true
TocOpen: false
platformLabel: "TCM Security"
cover:
  image: "00-card.png"
  alt: "Dev"
  relative: true
---

We're working on gaining root access to a machine called Dev from TCM Security. It's not widely available or discussed elsewhere, making it a great starting point for beginners in penetration testing.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfgt_ghVkJzNZlkdx6At9HovILN6dMsVFiaT7J4UXCBf73LNxVDqMbRBn60eatvr84s1WyXGDHlS1BpRHThOBKHprvh6TvJFa8DkR8FYzGG1oZS8NZr0-zvP73L2yC3FeJnI5aIog?key=6wzMd5hNles5zyDlVRHfGx6e)

## Check network integration

We need to login on the machine:

- Login: root
- Password: tcm

No we need to setup DHCP

```bash
dhclient
ip a
```

Check the ip adres of the target machine

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdwVjRcA5YLXgkrexCfR1wNwc_NJQ23L2FIjDBmII8Oz5N0vTIh7QcoShfwFwGxOb8q5oT8Wa786NltecADs56X2wFU3qNv887BIqoMRKLnZiwmtw5zZNkCu9-a4W2ttizxbI05cA?key=6wzMd5hNles5zyDlVRHfGx6e)

No we can run nmap scan on vulnerable machine

## Nmap

Use following command to scan the target IP address.

```bash
nmap -A -T4 -p- 192.168.100.129
```

- nmap initiates scan.
- -A enables OS detection, version detection, script scanning, and traceroute. It's an aggressive scan by combining several advanced features.
- -T4 sets the timing template to "4", which is more aggressive and faster than the default.
- -p- specifies that Nmap should scan all 65535 TCP ports on the target host. This is useful for discovering open ports across the entire range.
- 192.168.100.129 is the target IP address for the scan.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfXAImQrBEPH2aHOjMogy94fDdRwkcCzurTbVmOhle9Fh905YkOztmrmNQgVLDdnjsRRoFecKpunjMeJTI1HrqFM0Y9ZunS-Nn2Cx-em2BT5OUIIuovBhc-Ss1TN_HZLo3Yo-Uv?key=6wzMd5hNles5zyDlVRHfGx6e)

## Analyzing Scan Results:

- Ports:
	- 80: HTTP - Apache httpd 2.4.38 (Debian)
		- Bolt - Installation error
	- 2049: NFS - 3-4 (RPC #100003)
	- 8080: HTTP - Apache httpd 2.4.38 (Debian)

## Port 80

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXecJa-tfJrAurPojBL32oDlHjPNeidu5xBYKHJQN4XiGSpQnC8bbmBKoi8TkW4SgSbJWExfYIHHGkiCcPHyanC17PwGrcKZe4jNin6_6TUwRUJcaVAk-I3jR_skznD-CXhQ0H8O?key=6wzMd5hNles5zyDlVRHfGx6e)

I have runned gobuster on port 80 to see any direcortys on the website

Command:

```bash
gobuster dir -u http://192.168.100.129:80 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXclRkpVps2PpMV6d-Jk-gxA33ja1QIyl-VMfYUmsNdYlyCfCOBzSJytzq6BuALfR2R6ge9eNhcuxVwSz-ctSCdIAVFxdmiJ9KBOcbW_pBopAz9MpsF3c_FFoRaORnNdOKtv138mJA?key=6wzMd5hNles5zyDlVRHfGx6e)

#### Let's check /server-status:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcRtOx0pvUl8rQ-6kRY8-0VLghT1OMz9mvrLgNQxhK2iaFWdTPEVo0preHuS63oh_XNzWiK5mOGxGStJA3QnIM9N_UlZNGjYpbx-fWGwA2PB2d-cKcD77xZMPaKLnV02GktGP3vyg?key=6wzMd5hNles5zyDlVRHfGx6e)

#### Let's check /extensions:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXean6GK4Of7qKWPigdQDRQFtCUvV_Vb4QPC1lNv4RtIqh-CP6BPxXvPOybhagHN4vguZxiSVR1bcuRUi_S5BZm4R9mbaxOrTfHb19105JAfe1jeN2MDQDpFHEcSOn53jzwpEwkLmg?key=6wzMd5hNles5zyDlVRHfGx6e)

#### Check /vendor:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXf5Hhud6B7F8lv6RqX86fN3weqW2KAvboi8u_xOhuXIAgl5aYUFvzcDDfdQyPtUeDCqiYJ_YvVh1jZo4vDnK--l-KHRUHaw7rkz5kKhBLqNs4rESY3z8cLmPxeYibsa3mi0dGGMXw?key=6wzMd5hNles5zyDlVRHfGx6e)

#### Index /src:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdNIp1YirCQflRTrG83q82YDTx0cgHEkaN70W4CrRI59ZnBxmCjtrHmjYRVV93VeYdFWIRmspobM9--M3MwO0Awu7HwssvGv3MBFoTGVnlmBuEdfJ9lGqLqsInxO1A1RDvdMXsSYQ?key=6wzMd5hNles5zyDlVRHfGx6e)

#### Check index /app:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcQxWtm9QPFz0yvkVrf72M51bHgRU8jDe2wB7lyONN0t9hsgWFUrCS0XnYm-nAqg26LKNdZPtg6U2m8d90iwvNHox04yFj82XfIpt0Dwl7PYEVolzmN-8hq-1FN08hm2cwMtMd6dQ?key=6wzMd5hNles5zyDlVRHfGx6e)

Let's look what is in the config folder:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfbn97y9vFylQznwkMs19d4QyMM_MrEzCaR1LQ64xgEefHSnP9nbjSEIObrOjZ5jTM_Msqk9-bMt6kmcBrI0Fx9SKPMi7ixAkfCYSa0r-1C7mjjCCqu2acmTvTb_o_7dSs1PPiW?key=6wzMd5hNles5zyDlVRHfGx6e)

We can see some of .yaml config's.

I found possible password in the config.yml file.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfEtHKWEVhiNRRW-yeRzAk9NCrLQFdXa3Yphu9rxstbHsoaBb7TsTetucviIy2qqJOWffJWBdRQhYrlwTZwxkUREgDjRKs8uNokVCFQCtNIDlFq43SRN1zQQN4p1xgsUzQO-lRm?key=6wzMd5hNles5zyDlVRHfGx6e)

Let's try to open /app/database:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXej4CT_WfjcNdlnbo4NWRuub4Y7UsRTqmQ0OqRKKrLSKGDeu2KjV6V1k39Rb_g45p3l6Cg4aLbwbT_TxY8fMTB-q3lAkRR6cyT9ZjCHkilbg5sQUpWm_gxxEUKLk7Rj4s1QUh6NSg?key=6wzMd5hNles5zyDlVRHfGx6e)

Unlucky nothing.

## Port 8080

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfnhoK-1-n8MpoaWfnyRKgm9jEreJjKoju9QzEo1_t_ek-RLPXMGpXB966R9Ez8to609ScvUEjyqGX8pnHxPn6nVyabrfqwIM2Jxwcx4WV_XmGD7sP0BYTrhtDpRD-3amUTi141?key=6wzMd5hNles5zyDlVRHfGx6e)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfWNwAjIiE_4aqCzeoXyGvFF3J4l4yRRFoR69BPZDSSiuCLm2McL7OkYFGMn6QODwIYhLFMZMZOg-At_dH7LfQRTX0YEFOpBNbOgvYBF3nYqILbUJt55QhqAkp2-7Hi5u_mnff7?key=6wzMd5hNles5zyDlVRHfGx6e)

I'm gonna run Fuzz scan on port 8080 to see if we can get more dictionaries on this site

Command:
```bash
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://192.168.100.129:8080/FUZZ
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeQ9KxxmYLpupn9_qQT9Eqzo6fqBXSeFmChF-GiP8k7A7EboUr-sCo53tC8Ln9IwR3rlh2_rC9i-qPR-qc5L0B39ihgDRf6FAHNX5ThGXVvoCH3LIJgzMaD2Pe9__wULMPf_QaxHw?key=6wzMd5hNles5zyDlVRHfGx6e)

Here we can se an extra dictionarie and that is a /dev

Let's go to this dictionary

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXff8HEGrGNafOi5-TH5S6d2cX7VM8RoLE9d62R_Yb1ONlK4vQyh26_-jBYWnPjpJd7EdquW1F6X-GuyqieFuElQtMrviqgG6k1nf69jaSuszmWIp880P6JngHzZyG9-pBezcx4ebQ?key=6wzMd5hNles5zyDlVRHfGx6e)

We can look around, but nothing interesting.

What is a common vulnerability with webpages that we can exploit within the URL?

Google is our go-to. Whenever we learn about a new service or program we should be slapping it into google and adding the word exploit or vulnerability on the end. In this case it appears Boltwire 6.03 has a Local File Inclusion vulnerability.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdX41w6NMxQHis4Io5pBH_xHJdeDgWJrikfIDttrndgCp9chjqFZsQU5aYyW8C4a5k_bzquutvm046eDNkqksEfoYpsSh-DMCqZhLsnlNouR4t7OqX8o3mW_7XM8MOqlNGHu-QOtw?key=6wzMd5hNles5zyDlVRHfGx6e)

We currently don’t know the version of Boltwire being used, but we can try the LFI and see. Below is the URL we use in the browser, for this to work you must be an authenticated user, which means you need to make an account.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdrPLmzTnpkfmn5d6aZLfETaow3KObCuX3xdkKLVDqWkEQgBV7fmaW4ZM-EPPQ8YbaASDqTCcYSQcRiDaeQrYuPiDDfoluKawGHBp3TKGtTFAB1uWi8TigxgCXGJnG7zWKMa5GW0g?key=6wzMd5hNles5zyDlVRHfGx6e)

LFI it is a method of using the website to navigate through the host server, normal practise it to make it so that these types of URLs are sanitised. However, if not, we can use directory traversal (../) to move back to root, and then go into etc/passwd to be given the list of users.

We gonna paste this into our link:

- index.php?p=action.search&action=../../../../../../../etc/passwd
    

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfKE5wlZ0Mv_QTsUPFv11VTXak1qVFPlfU4mszi-tIgmZKoEKsFfd4GLUFMlVauefRRhvFFawYC7DfvRjLvxPCxqFojFRo9uOQeSk_BNCgUdjvBc6EGcaprkzEzUvHXYUAlD_1kwg?key=6wzMd5hNles5zyDlVRHfGx6e)

If you go through the list you will find a username at the bottom jeanpaul, that will be a username we make note of.

## Port 2049

Let's check what is in the NFS (Network File Share)

Command:
```bash
showmount -e 192.168.100.129
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXchX92-wCPRjJaR7BH4XoextYG_Mwyk-kDlIOBaXGQCTM1CYYZHxZGofUDFOdGRuKbUxdKG6yeruVJhzPy27W50t9v3-KMkwB3EtpwhdtOIZIO4Bw91JpnjMmWTPp6twrQCi4DiCw?key=6wzMd5hNles5zyDlVRHfGx6e)

Firts i'm going to make a directory for mount

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXf-Myf27efu5ZyYv70ibiWiCXsyudsFLZoc1tIspuY9bn3leC6mxRKp3bv45hYTzD2g8j2SyIPzX81o6D5-WSkUxYr99lWevm6fLe960WqfFfa0r1yp6MPfpdhgjXcUmgRuC7Po9w?key=6wzMd5hNles5zyDlVRHfGx6e)

I'm gonna now mount this to directory on my attack machine

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcvFEFgB61eFnMmoz3WlUOnGG-E8Rgdx8Um3Mpx5WMVm15LTuZQRuQQf_QPBtcJ0GVF7EgnLxRKSPGjcJ3yxbK5tSsrMtkLX4098qpYe8vurN1uW-kUaQosZWxNz5uyRD9x9lfb?key=6wzMd5hNles5zyDlVRHfGx6e)

- - t we need to set the type. It is nfs
- Then target ip adres with the file we want to mount
- As last directory where we want to set the file

Let's go now to this directory:

```bash
cd /mnt/Dev
ls
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcM-ph2GrYSuKX4VEoKG-2_BuaAyTO0wZgTQLkTOzxZ10rXlH4bADI5vvXD2RrnK4ANv5xhxnhmTWhraLsuxzMOAAsMg0t23zobTvHijQpGDQJ2sPxhyGls6kf7gATMo9EZZ_848A?key=6wzMd5hNles5zyDlVRHfGx6e)

Let's try to unzip this file

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcVjRh6WSqcRV2I8phgvVrtzzoSsX2vEDokbC4O3O49ZWgiqZkRCoWE3HoqDJtiR8moTx5ufXFIIMxAJg-jjKj9aUsZ8y9Ub1SSsy8MI5O77JsnFHD6F4QZHU18DhNQAN8DQyw-Zg?key=6wzMd5hNles5zyDlVRHfGx6e)

Unfortunely we need a password to unzip this file

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdUfIXgDnQUg2RFEI0n2Yo9IjSxdO3YdS86hq5_6GE_xWbxkUby10qqAVQPFYF-F5Zr-QwlXVv-mRALTsd796j1rSAx-BdLKkM8zC1lmKcDQp3eqErLIGfKCLiC7tlbVy56B3ukmg?key=6wzMd5hNles5zyDlVRHfGx6e)

We gonna try to crack this file and see if we can maybe get in there

Command:
```bash
fcrackzip -v -u -D -p /usr/share/wordlists/rockyou.txt save.zip
```

- - v for verbose, we want to have verbosity here and see all the output
- - u means unzipping the files
- - D We gonna using a dictionary attack
- - p We gonna using a file in order to attack
- Wordlist rockyou.txt

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfxd0gKtByNNx7ErLDDPYGpaFLsW63oWvw2jba5o7ezh5yFbmO24ahFbxFWbmQhwovROhOSi-75qxRIuMmOQSuK4V1GKB7g9UMOJX-ZY2vJSSNUTyaOOHIawdKOnEJ3cQ7oWClTQA?key=6wzMd5hNles5zyDlVRHfGx6e)

Let's try now to open this file

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfmgUrbJRw-nqWM7yBaN8N7iWSik1HihWMhPPukxBKsIASF4trbMNuMNWGNyMUeBMXosbUAQ4JhCA3QKd6323rxzbfhaQZ1HUkqZXk-IoFYklMi0mrLk9ff3selziOyNS5xZz_BoA?key=6wzMd5hNles5zyDlVRHfGx6e)

Let's see what is in the id_rsa

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdh8ReJMy1rNlSvQWbSpwj2B0YVV17DV4-E39dIzMkTMElSla6Z6x3TYqApR3FlC3JLjo16Hfm1umjrKszeZwBBtQZR0QAoGvMxGhXYSBdYjAGnGdiqk2wLcv_Gae1ActJfOStd?key=6wzMd5hNles5zyDlVRHfGx6e)

We get a private key in here

Let's see what is in the txt file

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdMwW0oBFsj-R1JHGUASj1Eidy9to9yd9_rXriIlMN78grZJT0Q8QkmUz9ERgGCrpM13YM-hAcvtPpPdTljtG15uSa8Ru5QVVrVVGEjIzQ14O0TTbd_kRSzVJzexcLltCgI03EJ_A?key=6wzMd5hNles5zyDlVRHfGx6e)

## SSH

We now have an RSA key, which is a commonly used form of Asymmetric encryption for SSH (public/private key), so let’s try it out!

You will need to attempt this in the same directory as the id_rsa key, as that will be used.

We know a username jeanpaul from the LFI vulnerability and the note.txt was signed by jp so we will try ssh with him.

```bash
ssh -i id_rsa jeanpaul@192.168.100.129
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdOmAbaqtick0rTbUTr0-B1A2-WiYkkgF6yB7bWEl3pKqOk_G_K1KmPLIrP4fNIDMNt5M7aZJ9pEJmkDSaPFyEWEgyOFnHHc8yV3TAzgwZMxRA41BWa23fuicifklZwDKuwB3ZAWA?key=6wzMd5hNles5zyDlVRHfGx6e)

Looking back at our notes we have I_love_java from the config file. Using that we get in.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfDqejJ1AOXJ3ADsjYYerCzWQeLDUfgXrrUk5RTEFytVtXIStRr49lf6Rj_4-04awYjbLdqqDOmh4GAQ9HcvqCNyNNWrpjWhvKWid2kAdGIWY8_T7tg-dZtb9v_pLKIFJXPyI3_?key=6wzMd5hNles5zyDlVRHfGx6e)

We are in!

We are now in the machine via ssh, running whoami confirms we are jeanpaul and not yet root. We can also see if we have sudo by using the command sudo -l which will show us when (or if ) we can use sudo.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcnHcuJOzV7g_jlwEVgr6hpimxRzLbhAAkj9zIBTBAGisjTxTaYxAFgu6sG8nKrxxz7vPAwJMDuX2TGF8oHjYeeaO9gCn5g9cJChntA48nZh_WkPLBprqvYqG5jB7QLjMc4ymJ-Yg?key=6wzMd5hNles5zyDlVRHfGx6e)

We can run sudo zip witout the password

We want now to abuse that feature and be able to escalate into root

Go to website:

![Logo](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdFvzit5rC8LbAIBW-zLS6F4D6s5KxRxQwLs2bqphozg5E9fR-rvQ9FPz2ii4WYfwYUlPQ74WPpGmuLfMXGsgkMZm0A9WKrh89JnFfYJlk8w3q53UfrIs5zx-zeZmtM7PrYUdRVvA?key=6wzMd5hNles5zyDlVRHfGx6e)[GTFOBins](https://gtfobins.github.io/)

Great websie for escalations. We are going to select suda and scrolling down for zip.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXejvvEHwPjpM0XQzqVXwbg2AS7uXbJQdKEVlJQp6IZj0cxH_nPHspyOblyVt7pDpKbTPEBq8hAhAfM6d2z9dtdQ52EQ1tTlsGIEJD5iRdp-bhzxD6fcT77AKJsh7XlBrZPlTO4Hmg?key=6wzMd5hNles5zyDlVRHfGx6e)

Here is how we use sudo as a zip in order to get a root privilages

Copy this commands

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfbJPk290r5UX1GeqoJlPiRgbNAnhAZJDWgovdBOEhw2j_3sjeaMzA4GCXqz0TI8zFOtiOTK1AZeXQWhYaLT03FL4LyO67UfkNFBOySKE7peRhjKnK1_69i-wrj-gNy9-t0gbWrtQ?key=6wzMd5hNles5zyDlVRHfGx6e)

We are now root!

Sudo runs as elevated, we droped into a shell

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdqI-R1IViXVRVNIU-7fAsVqOtKoYs-ojR2lYQxRQq3kmtnwSVkMQFYyZni9BO8zaRJ_6e1F42bwxh7PnGw3Em3g15JqMLw0BgeET8lD1DZAe9m01hOlJr6twSAwAR2DmaeqID6Jg?key=6wzMd5hNles5zyDlVRHfGx6e)