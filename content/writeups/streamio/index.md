---
title: "StreamIO — Hack The Box"
date: 2026-09-18
draft: false
tags: ["hackthebox", "windows", "active-directory", "medium", "vhost-fuzzing", "sqli", "lfi", "firefox-creds", "acl-abuse", "laps"]
categories: ["writeups"]
summary: "StreamIO chains vhost fuzzing, MSSQL UNION-based SQLi, Hydra password spraying, an LFI leaking PHP source, Firefox stored credentials via firepwd, and finishes with a BloodHound WriteOwner → group membership → LAPS read on the DC."
ShowToc: true
TocOpen: false
cover:
  image: "00-card.png"
  alt: "StreamIO — Hack The Box"
  relative: true
---

| | |
|---|---|
| **Platform** | Hack The Box |
| **Difficulty** | Medium |
| **OS** | Windows |
| **Key techniques** | Vhost fuzzing, UNION SQLi (MSSQL), Hydra spray, LFI → PHP source, `firepwd` on Firefox `key4.db`, `WriteOwner` → LAPS read |

---

## TL;DR

The main site is a video-streaming front, but the interesting attack
surface is a second vhost (`watch.streamio.htb`) surfaced through
`ffuf -H "Host: FUZZ.streamio.htb"`. The primary site's `search.php`
is UNION-injectable against MSSQL; extracting `information_schema` and
the users table yields hashes that crack to a password list. Hydra sprays
that list at the `watch.streamio.htb` login and returns an admin combo.
The admin panel exposes an LFI in an `?include=` parameter — wrapping it
in `php://filter/convert.base64-encode/resource=master.php` leaks the
PHP source and, with it, credentials for the streamio_backend database.
Dumping that DB gives credentials for `nikk39`, who has WinRM on the DC.
`nikk39`'s user profile stores a Firefox `key4.db`/`logins.json` pair;
`firepwd.py` decrypts it and yields `yoshihide`. BloodHound then shows
`yoshihide` has `WriteOwner` on the `Core Staff` group, which has
`ReadLAPSPassword` on the DC — the four-step ACL abuse chain reads the
LAPS password and lands `Administrator` on the DC.

---

## Recon

```bash
sudo nmap -p- -sCV <TARGET_IP>
```

![nmap](01-nmap.png)

- **53** — DNS
- **80 / 443** — HTTP/HTTPS (`streamio.htb`)
- **88** — Kerberos
- **135 / 139 / 445** — SMB / RPC
- **389 / 636 / 3268 / 3269** — LDAP (domain `streamio.htb`)

The site on 443:

![streamio.htb front](02-443-site.png)
![About us](03-about-us.png)

Directory brute-force did not surface much:

![ffuf directory attempt](04-ffuf.png)

**Vhost fuzzing** yielded `watch.streamio.htb`:

![vhost watch.streamio.htb](05-vhost-watch.png)
![search page on watch](06-search.png)

---

## SQL Injection — UNION on search.php

The `search.php` parameter was UNION-injectable against a MSSQL
backend. Standard cheatsheet path — probe version, count columns, walk
`information_schema`:

![SQLi cheatsheet reference](07-sqli-cheat.png)
![Version probe](08-version.png)
![UNION SELECT](09-union-select.png)
![Column count](10-columns.png)
![information_schema](11-information-schema.png)
![List databases](12-select-databases.png)
![Select streamio db](13-select-streamio-db.png)
![Users table columns](14-select-users.png)
![Users table dump](15-users-table.png)
![Password hashes](16-user-hashes.png)

Cracked with hashcat:

```bash
hashcat -m 0 hashes.txt /usr/share/wordlists/rockyou.txt
```

![Cracked](17-hashcat-cracked.png)
![Password list](18-passwords.png)

---

## Foothold — password spray with Hydra

Built a username list from the users table and sprayed the cracked
passwords against the `watch.streamio.htb` login form:

![Accounts collected](19-accounts.png)
![Spray plan](20-password-spray.png)

```bash
hydra -L users.txt -P passwords.txt watch.streamio.htb \
  https-post-form '/login.php:username=^USER^&password=^PASS^:Invalid'
```

![Hydra hit](21-hydra-brute.png)

Landed the admin panel:

![Admin panel](22-admin-panel.png)

---

## LFI → PHP source → DB creds → RCE

The admin panel exposed an `?include=` parameter — LFI:

![LFI parameter](23-lfi.png)

Wrapped in `php://filter/convert.base64-encode/resource=master.php` to
read source rather than execute:

![master.php through filter](24-master-php.png)
![base64 body](26-base64.png)
![Source decoded](25-master-php-source.png)

DB credentials in the source:

![DB admin creds](27-db-admin.png)

MSSQL / template-eval path to code execution:

![Exploit command](28-exploit-command.png)
![Eval endpoint](29-eval.png)
![Request replayed](32-request.png)

Reverse shell:

![Reverse shell payload](30-reverse-shell.png)
![Shell landed](31-shell.png)

---

## Lateral — nikk39

`nikk39`'s profile surfaced material worth pivoting on:

![Nikk39 interesting](33-nikk-interesting.png)
![Nikk's passwords](34-passwords-nikk.png)

WinRM as nikk39:

```bash
evil-winrm -i streamio.htb -u nikk39 -p '<pw>'
```

![Logged in as nikk](35-logged-in-nikk.png)
![user flag](41-user-flag.png)

---

## Lateral — nikk39 → yoshihide (Firefox `key4.db`)

A saved Firefox profile in `AppData\Roaming\Mozilla\Firefox\Profiles\…`
had `key4.db` + `logins.json`:

![Firefox creds folder](36-firefox-creds.png)
![Downloading key4.db](37-download-key4.png)

Decrypted with `firepwd.py`:

```bash
git clone https://github.com/lclevy/firepwd
python3 firepwd.py
```

![firepwd repo](38-firepwd.png)
![firepwd run](39-firepwd-run.png)
![Decrypted logins](40-logins.png)

Yields credentials for `yoshihide`:

![yoshihide creds](42-yoshihide.png)
![yoshihide account](43-yoshihide-account.png)

---

## Privesc — `WriteOwner` → LAPS

BloodHound as `yoshihide`:

![BloodHound collect](44-bloodhound-collect.png)

`yoshihide` has `WriteOwner` on `Core Staff` (which reads LAPS on the DC):

![Core Staff ownership path](46-writeowner-core-staff.png)

Upload PowerView and run the four-step chain:

![PowerView upload](45-powerview-upload.png)

```powershell
$Cred = New-Object System.Management.Automation.PSCredential(
    'streamio\yoshihide',
    (ConvertTo-SecureString '<pwd>' -AsPlainText -Force))

Set-DomainObjectOwner -Identity 'Core Staff' -OwnerIdentity yoshihide -Cred $Cred
Add-DomainObjectAcl   -TargetIdentity 'Core Staff' -PrincipalIdentity yoshihide -Rights All -Cred $Cred
Add-DomainGroupMember -Identity 'Core Staff' -Members jdgodd -Cred $Cred
```

![Added jdgodd to Core Staff](47-adding-to-group.png)
![Valid jdgodd creds](48-valid-jdgodd.png)

Read LAPS over LDAP (works even when WinRM refuses because `jdgodd` is
not in `Remote Management Users`):

```bash
bloodyAD --host 10.10.11.158 -d streamio.htb -u jdgodd -p '<pw>' get search \
  --filter '(ms-mcs-admpwdexpirationtime=*)' \
  --attr ms-mcs-admpwd,ms-mcs-admpwdexpirationtime
```

![LAPS password](49-laps-password.png)

Log in as local Administrator on the DC:

```bash
evil-winrm -i streamio.htb -u Administrator -p '<LAPS_pw>'
```

![Administrator on DC](50-connect-dc.png)
![root flag](51-root-flag.png)

---

## Lessons Learned

- **Vhost fuzzing changes the box completely.** A single unnoticed
  `Host:` header on Hack The Box regularly hides the entire attack
  surface (`watch.streamio.htb` here).
- **UNION SQLi has a fixed sequence — do not fuzz it.** Version →
  columns → `information_schema.tables` → `columns` → dump. Same order
  every time, whether the backend is MySQL, MSSQL, or PostgreSQL. Only
  the syntax quotes change.
- **LFI + `php://filter` = source disclosure.** Any `?include=` /
  `?page=` / `?file=` parameter that behaves like an include gets the
  base64 filter treatment first — source code beats blind LFI every
  time.
- **Saved browser credentials live on disk.** Firefox `key4.db` +
  `logins.json` → `firepwd.py`. Chrome `Login Data` + `Local State` →
  DPAPI. Neither one gets audited in most environments.
- **`WriteOwner` is not lateral — it is total.** Own → grant self →
  add member → read the protected attribute. When the target group
  can read LAPS, that is the DC.
- **Different LDAP and WinRM permission surfaces.** New members of a
  group can read LDAP-side attributes (`ms-mcs-admpwd`) immediately,
  but WinRM checks `Remote Management Users` separately. If WinRM
  refuses, try LDAP with `bloodyAD` before troubleshooting the
  credential.

---


---

## Remediation

- **Fuzz your own vhosts before an attacker does.** Enforce a strict
  vhost whitelist on the web server (nginx `server_name` / IIS
  host-header binding) so unlisted vhosts return `404` instead of
  serving `watch.streamio.htb`. `ffuf`/`gobuster -mode vhost` are
  cheap smoke tests to run on a schedule.
- **Parameterise every SQL query.** MSSQL, MySQL and PostgreSQL all
  support parameterised queries or prepared statements — using them
  removes UNION-based extraction as a class of vulnerability. Do not
  rely on `escape()` helpers.
- **Turn off `allow_url_include` and consider disabling PHP filter
  wrappers** so `php://filter/read=convert.base64-encode` cannot leak
  application source. Explicit allowlists of includable files beat
  blacklists on the parameter value.
- **Never save credentials in Firefox on shared / production hosts.**
  If the workflow requires it, use a master password (properly
  configured key3/key4) — the master-password key derivation slows
  `firepwd.py` down enough to be useful.
- **Tighten AD ACLs on LAPS-readable groups.** `WriteOwner` on a group
  that can read `ms-mcs-admpwd` is the same as DA to an attacker.
  Least privilege on Tier-0 group ownership; alert on ownership
  changes.

---

## Tools used

- `nmap`, `ffuf`, `gobuster` (vhost mode)
- Burp Suite
- Custom Python for UNION SQLi extraction
- `hydra`
- `evil-winrm`, `nxc`
- `firepwd.py`
- `bloodhound-python` + BloodHound GUI
- `bloodyAD` (owner + member manipulation)
- Impacket (`smbserver.py`)

---

**Also on GitHub:** [this write-up in my security portfolio (methodology & cheat sheets)](https://github.com/debasjan/security-portfolio/blob/main/writeups/hackthebox/streamio.md)
