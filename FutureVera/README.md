# 🛰️ FutureVera Enumeration CTF Writeup

## 🔍 Challenge Overview
- **Target:** futurevera.thm (10.113.131.173)
- **Category:** Web Enumeration
- **Difficulty:** Easy
- **Goal:** Find and capture the flag

---
Hello there,
I am the CEO and one of the co-founders of futurevera.thm. In Futurevera, we believe that the future is in space. We do a lot of space research and write blogs about it. We used to help students with space questions, but we are rebuilding our support.
Recently blackhat hackers approached us saying they could takeover and are asking us for a big ransom. Please help us to find what they can takeover.
Our website is located at https://futurevera.thm
---

## 🚀 1. Initial Recon

### 👇 Add host entry
```bash
echo "10.113.131.173 futurevera.thm" | sudo tee -a /etc/hosts
```
🔎 Nmap Scan
```
nmap -sC -sV futurevera.thm
```
Result:

- Port 80 → HTTP
- Port 443 → HTTPS

## 📂 2. Directory Enumeration
Gobuster Basic
```
gobuster dir -u https://futurevera.thm -k -w /usr/share/wordlists/dirb/common.txt
```

Only static directories found (assets, css, js).

Larger Wordlist:
```
gobuster dir -u https://futurevera.thm -k -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

No additional directories.

## 🌐 3. Subdomain Enumeration
Host Header Fuzzing
```
ffuf -u https://futurevera.thm \
-H "Host: FUZZ.futurevera.thm" \
-w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt \
-k -fs 4605 -fw 1511
```

Interesting Results:

Subdomain	                Status
blog	                     421
support	                   421


## 📋 4. Explore Subdomains
Blog Subdomain

Add to hosts:
```
10.113.131.173 blog.futurevera.thm
```
Open in browser → a simple blog was found
No hidden flags yet.

Support Subdomain
```
10.113.131.173 support.futurevera.thm
```
Contains a “support redesign” message.


## 🔐 5. SSL Certificate Inspection

⚠️ Firefox (View Certificate → Subject Alt Names) revealed:

secrethelpdesk934752.support.futurevera.thm

This was not in wordlists — typical enumeration oversight.

Add it to /etc/hosts:
```
10.113.131.173 secrethelpdesk934752.support.futurevera.thm
```

### ⚠️ 6. Accessing the Hidden Subdomain

HTTPS failed due to SNI mismatch → use HTTP (port 80):

http://secrethelpdesk934752.support.futurevera.thm

🎯 Flag Found:

flag{beea0d6edfcee06a59b83fb50ae81b2f}
