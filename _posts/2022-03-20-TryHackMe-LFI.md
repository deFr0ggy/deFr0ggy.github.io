---
title: "TryHackMe - LFI (Local File Inclusion)"
date: 2022-03-19
categories:
  - CTF
tags:
  - CTF
  - TryHackMe
---

In this write-up we are going to be solving LFI (Local File Inclusion) room from TryHackMe. We got to hack into the machine and also need to escalate out priviliges to become root.

![](/assets/LFI-THM/1.png)

As always, we will begin with Nmap Scan to find possible open ports. 

## Nmap Scan

We can observe that there are 4 ports open. 

| Port Number  |
|---|---|
| 80 | HTTP  |
| 22 | SSH |

![](/assets/LFI-THM/2.png)

## 80 - HTTP

On opening the IP Address over port 80 we are greeted with the application. 

![](/assets/LFI-THM/3.png)

While opening the article already written, we can observe the URL for possible LFI (Local File Inclusion)

![](/assets/LFI-THM/4.png)

## Exploiting LFI 

Using the following payload we can grab the `/etc/passwd`.

```
../../../etc/passwd
```

![](/assets/LFI-THM/5.png)

Also, using the same payload we can grab the `/etc/shadow` as well.

![](/assets/LFI-THM/6.png)

## Looking For Password

On taking a closer look, on `/etc/passwd` file, we can find the credentials for the `falconfest` user.

![](/assets/LFI-THM/8.png)

## Logging In - SSH

Using the found credentials, we will log into the `falconfeast` user account.

![](/assets/LFI-THM/9.png)

## Priv Esc

On checking the user permissions, we can observe that this user i.e. `falconfeast` can run `socat` as SUDO.

![](/assets/LFI-THM/10.png)

## GTFOBins

Searching GTFOBins, we can find the command to escalate our priviliges.

![](/assets/LFI-THM/11.png)

## Getting Root

Using the command, we can easily become root.

![](/assets/LFI-THM/12.png)

## Finding User Flag

```
find / -name "user.txt" -type f 2>/dev/null
```

## Finding Root Flag

```
find / -name "root.txt" -type f 2>/dev/null
```