---
title: "TryHackMe - LazyAdmin"
date: 2024-03-17
categories:
  - CTF
tags:
  - CTF
  - TryHackMe
---

In this writeup we are going to solve LazyAdmin from TryHackMe in preparation for the PNPT exam.

![](/assets/THM-LazyAdmin/image.png)

### NMAP Scan

As usual, we will begin with the initial NMAP scan to find the open ports and running/exposed services.

![](/assets/THM-LazyAdmin/image%20copy.png)

Only 2 ports can be observed, 22 - SSH and 80 - HTTP.

### Web Enumeration

On opening the IP address in the browser, we can observe that its a default apache page. 

Tried looking for /admin, /login, /readme.html, /backup, /download, /robots.txt but none yielded any result.

![](/assets/THM-LazyAdmin/image%20copy%202.png)

### Directory Enumeration/Fuzzing

As we were not able to observed any potential PATH, the best was to run GOBUSTER, FEROXBUSTER or any other directory fuzzer of your choice. 

Which in our case, our tool was able to return some accessible directories which we were not able to guess before. 

![](/assets/THM-LazyAdmin/image%20copy%203.png)

/content is accessible, opening the same n the URL browser yields the backend CMS details which will come handy later on for the exploitation.

![](/assets/THM-LazyAdmin/image%20copy%204.png)

Accessing /inc directory yields directory listing which includes a database backup, downloading and checking the contents reveals a username and password.

![](/assets/THM-LazyAdmin/image%20copy%205.png)

### Cracking The Password

As we have the hash of the password, it is easy to crack the hash using online tools, johntheripper or hashcat. 

![](/assets/THM-LazyAdmin/image%20copy%206.png)

### Logging Into The CMS

The login page was observed to be on `/content/as` path. Using the username from the backup file and password as cracked, we can log into the application. 

![](/assets/THM-LazyAdmin/image%20copy%207.png)

### Navigating Website for Shell Upload

There are multiple section where files can be uploaded, although ADS section allow us to directly add the code. 

Thus, copying and modifying the PHP shell as per our needs and uploading the same. 

`/usr/share/webshells/php/php-reverse-shell.php`

![](/assets/THM-LazyAdmin/image%20copy%208.png)


### Reverse Shell

Listening for the reverse connection and triggering our shell via directory listing gives us a reverse shell.

![](/assets/THM-LazyAdmin/image%20copy%209.png)


### Stabalizing The Shell

After getting the reverse shell, we need to stabalize the shell, as python is installed so we will use `pty` library to get our bash shell prompt.

![](/assets/THM-LazyAdmin/image%20copy%2010.png)

### Users Flag

The user flag is under user's home directory. 

### Privilige Escalation

Checking for the sudo priviliges and what commands/scripts can be executed by the current user as sudo, we can observe that there is a PERL script which we should be able to take advantage of. 

![](/assets/THM-LazyAdmin/image%20copy%2011.png)

Checking the contents of the scipt we observe that there is another file under `/etc` directory which is actually executed via this perl script. 

![](/assets/THM-LazyAdmin/image%20copy%2013.png)

### Overwriting The Copy.sh & Getting Root

Now, as we can overwrite the file, we will move `/bin/bash` to the `copy.sh` script and then execute it via perl using the sudo command as we dont require the password for it. 

![](/assets/THM-LazyAdmin/image%20copy%2014.png)

Finally, we have the root and now we can read the root flag.

![](/assets/THM-LazyAdmin/image%20copy%2015.png)

---
