---
title: "TryHackMe - Anonymous"
date: 2024-03-18
categories:
  - CTF
tags:
  - CTF
  - TryHackMe
---

In this writeup we are going to solve Anonymous from TryHackMe in preparation for the PNPT exam.

![](/assets/THM-Anonymous/image.png)

### NMAP Scan

Starting with the initial NMAP scan we can observe that a number of ports are open including FTP, SSH, SMB etc. 

![](/assets/THM-Anonymous/image%20copy%202.png)

### Validating FTP Access

The first thing which i always try to do is to check for the default logins and specially with the FTP, anonymous logins. Trying the same, we can observe that we actually can login using the same. 

![](/assets/THM-Anonymous/image%20copy.png)

### Navigating Through The File System

Navigating through the file system, we can observe a to_do.txt which actuallyt tells that the admin is required to disable the FTP anonymous login. 

![](/assets/THM-Anonymous/image%20copy%203.png)

Checking the `clean.sh` script, which checks for the temp files and writes the output to the `removed_files.log` file. 

![](/assets/THM-Anonymous/image%20copy%204.png)

### Reverse Shell Using Clean.sh & Uploading Via FTP

So, we will edit the bash script on local machine to make a BASH reverse shell and upload the same file onto the FTP server.

![](/assets/THM-Anonymous/image%20copy%205.png)

Right after uploading the shell script, we recevied a reverse shell connection.

![](/assets/THM-Anonymous/image%20copy%206.png)

### Privilige Escalation

Running the following commands returns the SUID binaries.

`find / -perm -u=s -type f 2>/dev/null`

A number of items are flagged. Let's try using linpeas and try to find the same data. 

![](/assets/THM-Anonymous/image%20copy%207.png)

We have indeed an interesting entry which matches the previous results.

![](/assets/THM-Anonymous/image%20copy%208.png)

### Escalate

Now, we will be using GTFOBins to do some magic. 

![](/assets/THM-Anonymous/image%20copy%209.png)

---
