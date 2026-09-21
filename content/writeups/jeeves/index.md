---
title: "Jeeves — Hack The Box"
date: 2026-07-27
draft: false
tags: ["hackthebox", "windows", "easy"]
categories: ["writeups"]
summary: "Jeeves hides an unauthenticated Jenkins instance on a non-standard port. Groovy Script Console RCE gives a foothold, a cracked KeePass database leaks the Administrator's NTLM hash, and Pass-the-Hash lands SYSTEM — with the root flag tucked away in an NTFS alternate data stream."
ShowToc: true
TocOpen: false
cover:
  image: "00-card.png"
  alt: "Jeeves — Hack The Box"
  relative: true
---

| | |
|---|---|
| **Platform** | Hack The Box |
| **Difficulty** | Easy |
| **OS** | Windows |
| **Status** | ✅ Retired |
| **Key techniques** | Jenkins Script Console RCE (Groovy), KeePass cracking, Pass-the-Hash, NTFS alternate data stream |

---

## TL;DR

Jeeves runs an **unauthenticated Jenkins** instance hidden on a
non-standard path. Its Script Console runs arbitrary Groovy, so a reverse
shell gets me a foothold as `kohsuke`. In the user's files there's a
KeePass database (`CEH.kdbx`) — I exfiltrate it through a Jenkins job
workspace, crack it offline, and one entry holds the local
**Administrator's NTLM hash**. Pass-the-Hash with `psexec` gives SYSTEM,
and the root flag lives in an **NTFS alternate data stream**.

---

## Recon

```bash
sudo nmap -sCV -p- 10.129.228.112
```

![nmap scan](01-nmap.png)

Port 80 serves a fake "Ask Jeeves" search page. The interesting one is
**50000** (Jetty).

---

## Web Enumeration → Jenkins

Directory brute-forcing port 50000 revealed `/askjeeves`:

```bash
feroxbuster -u http://10.129.228.112:50000
```

![feroxbuster finding /askjeeves](02-feroxbuster.png)

`/askjeeves` is an **unauthenticated Jenkins** instance — no login at all:

![the Jenkins dashboard](03-jenkins.png)

---

## Foothold — Jenkins Script Console (Groovy RCE)

**Manage Jenkins → Script Console** runs arbitrary Groovy on the server. I
pasted a Groovy reverse shell and ran it:

![the Script Console](04-script-console.png)
![Groovy reverse shell payload](05-groovy-payload.png)
![pasting and running the payload](06-paste-run.png)

Caught a shell as `kohsuke` and read the user flag:

![shell as kohsuke](07-shell-kohsuke.png)
![user flag](08-user-flag.png)

---

## Lateral Movement — KeePass → Administrator hash

Found a KeePass database `CEH.kdbx` in kohsuke's files. To get it off the
box I created a Jenkins job and copied the file into its workspace so it
could be downloaded over HTTP:

![CEH.kdbx on the box](09-keepass-db.png)
![creating a Jenkins job](10-jenkins-job.png)
![copying the database into the workspace](11-copy-to-workspace.png)
![the database reachable in the workspace](12-kdbx-in-workspace.png)

### Cracking the KeePass DB

```bash
keepass2john CEH.kdbx > keepass.hash
hashcat -m 13400 keepass.hash /usr/share/wordlists/rockyou.txt
```

![keepass2john](13-keepass2john.png)
![hashcat cracking the master key](14-hashcat-keepass.png)
![cracked master password](15-master-password.png)

Opened the database with the cracked master password — one entry stores an
**NTLM hash** for the local Administrator, not a plaintext password:

![KeePass entries](16-keepass-entries.png)
![Administrator NTLM hash](17-admin-nthash.png)

---

## Privilege Escalation — Pass-the-Hash

No need to crack the NTLM hash — I passed it directly with `psexec`:

```bash
impacket-psexec administrator@10.129.228.112 -hashes <LM>:<NT>
```

![Pass-the-Hash with psexec → SYSTEM](18-pass-the-hash.png)

That gives a `NT AUTHORITY\SYSTEM` shell.

---

## Root flag — NTFS Alternate Data Stream

`root.txt` isn't a normal file — the Administrator desktop only has
`hm.txt`. `dir /r` reveals a hidden **ADS**, `hm.txt:root.txt:$DATA`:

```text
dir /r
#   hm.txt
#   hm.txt:root.txt:$DATA   <- the flag is in the stream
more < hm.txt:root.txt      # `type` can't read an ADS — use `more <`
```

![reading the flag from the alternate data stream](19-root-flag-ads.png)

---

## Lessons Learned

- Always dir-brute non-standard web ports — Jenkins was invisible behind
  the decoy on port 80 and only showed up on `50000/askjeeves`.
- An unauthenticated Jenkins Script Console is instant RCE via Groovy.
- KeePass databases crack with `keepass2john` + hashcat `-m 13400`; loot
  every entry — a stored NTLM hash is as good as a password.
- A hash found in loot means **Pass-the-Hash**, no cracking required.
- Flags/data can hide in NTFS alternate data streams: `dir /r` to spot
  them, `more <` to read them (`type` fails on streams).

---

## Remediation

- Never expose Jenkins unauthenticated; lock down the Script Console and
  place Jenkins behind authentication.
- Don't store credential databases on application servers.
- Rotate the Administrator password and don't stash its hash in a shared
  vault entry.

---

## Tools used

- `nmap`
- `feroxbuster`
- Jenkins Script Console (Groovy)
- `keepass2john`, `hashcat`
- Impacket (`psexec.py`)

---

**See also:** [Windows privilege escalation methodology](https://github.com/debasjan/security-portfolio/blob/main/methodology/windows-privesc.md)

---

**Machine:** [Hack The Box — Jeeves](https://www.hackthebox.com/machines/jeeves)

**Also on GitHub:** [this write-up in my security portfolio (methodology & cheat sheets)](https://github.com/debasjan/security-portfolio/blob/main/writeups/hackthebox/jeeves.md)
