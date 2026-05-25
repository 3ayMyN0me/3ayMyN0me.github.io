---
layout: page
title: About
permalink: /about/
---

I'm **{{ site.author }}**, a security practitioner focused on offensive security — penetration testing, vulnerability research, and the kind of hands-on exploitation you only learn by breaking into deliberately vulnerable machines. This site is my public lab notebook: every target I work, documented end to end, from first `nmap` scan to root shell.

I keep these notes for two reasons. First, writing an attack up forces me to actually understand it. Second, it's a transparent record of how I think under a real engagement — methodology, dead ends, and the reasoning that got me to the flag.

<div class="stat-grid">
  <div class="stat"><div class="n">{{ site.posts | size }}</div><div class="l">machines documented</div></div>
  <div class="stat"><div class="n">∞</div><div class="l">cups of coffee</div></div>
  <div class="stat"><div class="n">OSCP</div><div class="l">in progress</div></div>
</div>

## What I work on

<div class="skill-cols">
  <div>
    <h4>// recon &amp; enum</h4>
    <ul>
      <li>nmap / rustscan</li>
      <li>ffuf / gobuster / feroxbuster</li>
      <li>BloodHound / ldapsearch</li>
      <li>Burp Suite</li>
    </ul>
  </div>
  <div>
    <h4>// exploitation</h4>
    <ul>
      <li>Web (SQLi, SSTI, LFI, deserialization)</li>
      <li>Active Directory attack paths</li>
      <li>Public CVE chaining</li>
      <li>Custom PoC scripting (Python)</li>
    </ul>
  </div>
  <div>
    <h4>// post-exploitation</h4>
    <ul>
      <li>Linux &amp; Windows privesc</li>
      <li>linpeas / winpeas / pspy</li>
      <li>Pivoting &amp; tunneling</li>
      <li>Credential hunting</li>
    </ul>
  </div>
</div>

## Methodology

Every writeup on this site follows the same arc, because real engagements do too: **enumerate exhaustively → find the foothold → escalate → document.** I treat the boring enumeration phase as the most important one — the foothold is almost always hiding in something I almost skipped.

## Get in touch

I'm open to opportunities in penetration testing, red teaming, and security research.
Reach me at **[{{ site.email }}](mailto:{{ site.email }})**{% if site.linkedin %}, connect on **[LinkedIn]({{ site.linkedin }})**{% endif %}{% if site.github %}, or browse my code on **[GitHub]({{ site.github }})**{% endif %}.

> **Disclaimer.** Everything here is performed against machines I am explicitly authorised to test — lab platforms, intentionally vulnerable VMs, and CTF targets. Nothing here is intended to enable unauthorised access to systems you do not own or have permission to test.
