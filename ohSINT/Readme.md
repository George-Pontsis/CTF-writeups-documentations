# OhSINT — TryHackMe

**Room:** [OhSINT](https://tryhackme.com/room/ohsint)
**Category:** OSINT (Open Source Intelligence)
**Difficulty:** Easy
**Tools used:** `exiftool`, Google, X/Twitter, WiGLE.net, GitHub, WordPress, browser dev tools

## Overview

OhSINT is a beginner-friendly OSINT room that demonstrates how much information can be pulled from a single image file, and how that information can be chained together across the internet to build a full profile of a person. The room provides one image and asks a series of questions that must be answered by following the digital breadcrumbs left in the file's metadata.

---

## Task 1 — Investigating the image

**Question:** What information can you possibly get with just one image file?

Metadata embedded in an image (EXIF data) can reveal far more than the picture itself — GPS coordinates, timestamps, device information, and in this case, a copyright field containing a username.

### Step 1 — Extract metadata with exiftool

I downloaded the task file (`WindowsXP_1551719014755.jpg`) and ran `exiftool` against it:

```bash
cd ~/Pictures
exiftool WindowsXP_1551719014755.jpg
```

![exiftool output](Screenshots/01-exiftool-output.png)

The output revealed:
- **GPS Latitude:** 54 deg 17' 41.27" N
- **GPS Longitude:** 2 deg 15' 1.33" W
- **Copyright:** `OWoodflint`

Opening the image itself in Gwenview just showed a Windows XP–style "Bliss" wallpaper (green hills, blue sky) — a red herring, since the real clues were in the metadata, not the picture.

![image contents](Screenshots/02-image-contents.png)

---

### Q1: What is this user's avatar of?
**Answer:** `cat`

The `Copyright` field from the EXIF data (`OWoodflint`) looked like a username. Searching for it on Google led me to an X (Twitter) account, [@OWoodflint](https://x.com/OWoodflint), whose profile picture is a cat.

![google search leading to twitter](Screenshots/03-x-search-result.png)
![twitter profile](Screenshots/04-x-profile.png)

---

### Q2: What city is this person in?
**Answer:** `London`

On the same X profile, one of the two posts read:

> From my house I can get free wifi ;D
> Bssid: B4:5D:50:AA:86:41 - Go nuts!

A BSSID is the MAC address of a wireless access point, and access points can be geolocated using wardriving databases. I took this BSSID to [WiGLE.net](https://wigle.net) and searched it under **Network Search**.

![wigle search](Screenshots/05-wigle-search.png)

The result mapped the access point to a location in **London**.

![wigle map result](Screenshots/06-wigle-map.png)

---

### Q3: What is the SSID of the WAP he connected to?
**Answer:** `UnileverWiFi`

The same WiGLE.net query that resolved the location also returned the network's SSID in the results table.

---

### Q4: What is his personal email address?
**Answer:** `OWoodflint@gmail.com`

Going back to Google and searching the username `OWoodflint` more broadly turned up a GitHub profile with a repository called `people_finder`. Its README listed a contact email address.

![github repo readme](Screenshots/07-github-readme.png)

---

### Q5: What site did you find his email address on?
**Answer:** `Github`

The email address was published directly in the GitHub repository's README file, as shown above.

---

### Q6: Where has he gone on holiday?
**Answer:** `New York`

The GitHub README also linked to a personal WordPress blog. On the blog's author page, a post titled "Hey" stated:

> Im in New York right now, so I will update this site right away with new photos!

![wordpress blog](Screenshots/08-wordpress-blog.png)

---

### Q7: What is the person's password?
**Answer:** `pennYDr0pper.!`

Nothing on the rendered blog page hinted at a password. Right-clicking and selecting **View Page Source** revealed a hidden paragraph styled with white text (`color:#ffffff`) on a white background — invisible on the page itself but plainly visible in the raw HTML.

![page source password](Screenshots/09-page-source-password.png)

---

## Room Completed

![room completed 100%](Screenshots/10-room-completed.png)

---

## Key Takeaways

- **Metadata is often more revealing than the file itself.** A single innocuous-looking image can leak GPS coordinates, device info, and identifying strings via EXIF fields like `Copyright`.
- **BSSIDs are geolocatable.** Public wardriving databases like WiGLE.net can map a Wi-Fi access point's MAC address to a real-world location, even years after the original scan.
- **OSINT is about chaining small clues.** A username on one platform led to a Twitter/X account, which led to a GitHub profile, which led to a personal blog — each site contributing one piece of the puzzle.
- **Always check page source.** Sensitive information (like the hidden password here) can be concealed visually with CSS but remains fully readable in the raw HTML.
