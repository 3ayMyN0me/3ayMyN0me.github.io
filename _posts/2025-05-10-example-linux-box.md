---
title: "Sample Box — Easy Linux Foothold to Root"
date: 2025-05-10
platform: HackTheBox
difficulty: Easy
os: Linux
ip: 10.10.11.42
tools: [nmap, gobuster, Burp Suite, linpeas]
tags: [web, lfi, sudo, privilege-escalation, enumeration]
description: >
  A walkthrough of an easy Linux target: an exposed web app leaks credentials
  through a local file inclusion bug, leading to SSH access and a trivial sudo
  misconfiguration for root.
---

> This is an **example writeup** showing the format. Delete this file once you've
> written your own — duplicate it as a starting template. Everything below
> demonstrates the styling: headings, code blocks, callouts, and images.

## Reconnaissance

I always start with a full TCP port scan, then a targeted service/version scan on the open ports.

```bash
# Quick discovery of open ports
nmap -p- --min-rate 5000 -T4 10.10.11.42 -oN scans/all-ports.txt

# Targeted service + script scan
nmap -p 22,80 -sCV 10.10.11.42 -oN scans/services.txt
```

Two services are exposed: OpenSSH on `22` and an Apache web server on `80`. The SSH version is current and unlikely to be the way in, so the web server is where I'll focus.

<div class="callout tip"><span class="ic">TIP</span><p>Never stop at the first scan. A <code>--min-rate</code> sweep is fast but lossy — re-run a full scan in the background while you start enumerating, in case a port was missed.</p></div>

## Web enumeration

Directory brute-forcing reveals a hidden admin area.

```bash
gobuster dir -u http://10.10.11.42/ \
  -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt \
  -x php,txt -t 50 -o scans/gobuster.txt
```

The `/admin` panel includes files via an unsanitised `page` parameter — a classic **Local File Inclusion**:

```
http://10.10.11.42/admin/index.php?page=../../../../etc/passwd
```

The response leaks the system user list. More usefully, the application config is readable and contains a reused database password.

![Screenshot of the LFI response showing /etc/passwd]({{ '/assets/img/example-lfi.png' | relative_url }})

## Foothold

Spraying the recovered password against the SSH users from `/etc/passwd` succeeds for one account:

```bash
ssh devuser@10.10.11.42
# devuser@target:~$ id
# uid=1001(devuser) gid=1001(devuser) groups=1001(devuser)
```

First flag captured.

## Privilege escalation

Running `sudo -l` shows the account may run a backup script as root — and that script calls `tar` with a wildcard, which is exploitable via the well-known checkpoint trick.

```bash
sudo -l
# (root) NOPASSWD: /usr/bin/backup.sh
```

<div class="callout crit"><span class="ic">ROOT</span><p>The wildcard <code>tar</code> pattern lets a local user pass <code>--checkpoint-action</code> as a crafted filename, executing an arbitrary command as root. Dropping a SUID shell or reverse shell here yields full compromise.</p></div>

After triggering the script, I have a root shell and the final flag.

## Takeaways

- LFI is rarely the end goal — it's a credential-discovery primitive. Always pivot from "I can read files" to "what secret does this file contain."
- `sudo -l` is the single highest-value command on any Linux foothold. Run it first.
- Wildcard injection in privileged scripts remains shockingly common in CTF and real environments alike.
