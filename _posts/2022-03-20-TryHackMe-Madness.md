---
title: "TryHackMe - Madness"
date: 2022-03-20
categories:
  - CTF
tags:
  - CTF
  - TryHackMe
---

In this write-up we are going to be solving Madness from TryHackMe.

![](/assets/Madness-THM/banner.png)

As always, we will begin with Nmap Scan to find possible open ports. 

## Nmap Scan

We can observe that there are 2 ports open. 

| Port Number  | Service Type |
|---|---|
| 80 | HTTP  |
| 22 | SSH |

![](/assets/Madness-THM/1.png)

## 80 - HTTP

On opening the IP Address over port 80 we are greeted with Apache Default Page.

![](/assets/Madness-THM/2.png)

On checking the source of the page, we can find a comment that,

```
They will never find me!
```

Just above that there is a picture named `thm.jpg`.

![](/assets/Madness-THM/3.png)

We can also observe that picture seems to be corrupted although it is added to the page.

![](/assets/Madness-THM/5.png)

Initially, checking the file we can find that the Magic Bytes are of PNG file though the file itself it JPEG/JPG. So, we can find the Magic Bytes using the below site.

![](/assets/Madness-THM/6.png)

Opening the image in `GHex`, changing the bytes and saving the file.

![](/assets/Madness-THM/7.png)

We can finally open the picture :) and are also able to find the hidden directory.

![](/assets/Madness-THM/8.png)

On opening the directory we are greeted with another web-page. 

![](/assets/Madness-THM/9.png)

Checking the source gives us a hint that `secret is within the range i.e. 0-99`

![](/assets/Madness-THM/10.png)

It means that there can be a parameter? Well Yes! there is indeed a `?secret=` parameter, which accepts integers.

![](/assets/Madness-THM/11.png)

We can automate this task using bash.

![](/assets/Madness-THM/13.png)

and on valid integer we are able to find the potential password.

![](/assets/Madness-THM/12.png)


## Stego Stuff

Using the password we recently found, we can try extracting any potential embedded files from the image we just corrected.

![](/assets/Madness-THM/15.png)

The hidden files, gives us a username. Tried checking it over SSH and it didn't worked.

![](/assets/Madness-THM/16.png)

## ROT 13

Checking the username over ROT13 we can observe that the actual username is `john` which have been rotated 13 times.

![](/assets/Madness-THM/17.png)

Though, we were not able to crack the password for John as well. Maybe, the password is hidden somewhere else! 

## Downloading Other Image

So, there was another image provided with the room on TryHackMe. on downloading it and opening it using `eog` it looked something like this.

![](/assets/Madness-THM/19.png)

## Stego Again

Can we try to get any potential embedded files from this image? Well Yes! 

Now, we have the password for the John account.

![](/assets/Madness-THM/18.png)


## Logging In As John

Using the password we just found, we can log into the John account.

![](/assets/Madness-THM/22.png)

## Finding SUID Binaries

On checking for SUID binaries we can find some abnormal binaries which are not commonly found on end system.

![](/assets/Madness-THM/23.png)

## Finding Exploit

On quick google search, we can find `Local Privilige Escalation` exploit for this binary.

![](/assets/Madness-THM/24.png)

## Running Exploit

Creating a file on end system named `shell.sh` and adding the exploit code using `nano`. 

![](/assets/Madness-THM/25.png)

Adding executable permission using `chmod +x shell.sh` and finally running it makes us `root`.

![](/assets/Madness-THM/26.png)

## Getting Flags
Well, you got to find them by yourself now :) 