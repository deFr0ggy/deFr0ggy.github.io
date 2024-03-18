---
title: "TryHackMe - TomGhost"
date: 2024-03-18
categories:
  - CTF
tags:
  - CTF
  - TryHackMe
---

In this writeup we are going to solve TomGhost from TryHackMe in preparation for the PNPT exam.

![](/assets/THM-TomGhost/image%20copy%202.png)

### NMAP Scan

Starting with the initial NMAP scan, a number of ports are revealed. Probably the ones running Apache will be vulnerable.

![](/assets/THM-TomGhost/image.png)

### Apache Tomcat

Checking the ports in the browser and navigating through the page, i observed nothing to be working so probably, we have to go with a exploit.

![](/assets/THM-TomGhost/image%20copy.png)

### GhostCat

An exploit in AJP, and same is running on 8009 port on this machine. 

![](/assets/THM-TomGhost/image%20copy%203.png)

Using the exploit available within the Kali Linux to validate and read the `WEB-INF/web.xml` and finding the credentials of the first user.

![](/assets/THM-TomGhost/image%20copy%204.png)

### SSH Login

Logging into the user account and observing 2 new files which should be taken in to account. 

![](/assets/THM-TomGhost/image%20copy%205.png)

![](/assets/THM-TomGhost/image%20copy%206.png)

### Copying Files

Copying the files to the local machine using SCP. 

![](/assets/THM-TomGhost/image%20copy%207.png)

### Converting & Cracking

In order to crack the .ASC we need to have it converted first with `GPG2JOHN` and then run john with `rockyou.txt` to crack the password. 

![](/assets/THM-TomGhost/image%20copy%208.png)

### Decrypting The PGP

Using the cracked password, we can now import the ASC file via gpg.

![](/assets/THM-TomGhost/image%20copy%209.png)

### Changing The User

With the new password, able to login to the second account.

![](/assets/THM-TomGhost/image%20copy%2010.png)

### Privilige Escalation

Checking for the SUID binaries. 

![](/assets/THM-TomGhost/image%20copy%2011.png)

Using GTFOBins to look for an exploit.

![](/assets/THM-TomGhost/image%20copy%2012.png)

Running the exploit/command and gaining root.

![](/assets/THM-TomGhost/image%20copy%2013.png)

---
