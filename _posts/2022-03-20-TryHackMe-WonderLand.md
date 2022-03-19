---
title: "TryHackMe - Wonderland"
date: 2022-03-20
categories:
  - CTF
tags:
  - CTF
  - TryHackMe
---

In this write-up we are going to be solving Wonderland. 

![](/assets/Wonderland-THM/1.png)

As always, we will begin with Nmap Scan to find possible open ports. 

## Nmap Scan

We can observe that there are 2 ports open. 

| Port Number  | Service Type |
|---|---|
| 80 | HTTP  |
| 22 | SSH |

![](/assets/Wonderland-THM/2.png)

## 80 - HTTP

On opening the IP Address over port 80 we are greeted with the application. 

![](/assets/Wonderland-THM/3.png)

Also, it asks us to follow the white rabbit.

## Stego Stuff

Downloading the Rabbit picture and extracting the embedded contents using steghide.

Indeed there is a file named `hint.txt`.

![](/assets/Wonderland-THM/4.png)

Though, the hint says to follow the RABBIT :)

## Directory Enumeration

Begining with Gobuster, we are greeted with two endpoints i.e. 

1. /img
2. /r

![](/assets/Wonderland-THM/9.png)

## 80 - HTTP

On checking the `/img` endpoint, we can find 2 more pictures.

![](/assets/Wonderland-THM/6.png)

![](/assets/Wonderland-THM/7.png)

![](/assets/Wonderland-THM/8.png)

## Going RABBIT

Starting with the `/r` endpoint, it started to click. That we need to follow the rabbit. 

![](/assets/Wonderland-THM/10.png)

It mean't that we need to open up the directory in the following way.

```
/r/a/b/b/i/t/
```

That's how, we had to follow the rabbit.

![](/assets/Wonderland-THM/11.png)

## Getting ALICE Password

On checking this page source code, we can find username and password for ALICE account which is potential SSH credentials.

![](/assets/Wonderland-THM/12.png)

## SSH Alice 

Using the found credentials, we can log into the Alice Account. 

![](/assets/Wonderland-THM/13.png)

## Alice Enumeration

On checking the permission Alice user has, we can find that she can run python on the provided python file. 

![](/assets/Wonderland-THM/14.png)

The python file, seems to be returning random lines of the poem. Although, it imported RANDOM library.

![](/assets/Wonderland-THM/15.png)

As this is not present in the current directory we can create a file named `random.py` with the following code. So that as soon as Alice runs her script, our random file will be called/imported.

![](/assets/Wonderland-THM/16.png)

Running the python script lands us in the rabbit shell.

![](/assets/Wonderland-THM/17.png)

## Rabbit Enumeration

As, we are landed in the Rabbit Shell, on checking the rabbit home directory we can find an executable.

![](/assets/Wonderland-THM/18.png)

In order to have it analyzed, I downloaded the file on my Kali Machine.

![](/assets/Wonderland-THM/19.png)

Looking into the strings we can see that `date` is being called, although there is no location provided.

![](/assets/Wonderland-THM/20.png)

So, we can create a file named `date` with the following code.

```
#!/bin/bash
echo "Hijacked"
/bin/bash
```

Then, we will add it to out $PATH variable and giving it the executable rights. On successful execution of the file, we are now landed in the Hatter shell.

![](/assets/Wonderland-THM/21.png)

## Hatter Enumeration

Checking the Hatter home foler, we can find his account password.

![](/assets/Wonderland-THM/23.png)

## Privilige Escalations

On checking the ID, we can observe that we are not fully `Hatter`, so in order to become `Hatter`, we can issue the following command along with the password we found.

```
su hatter
```

We can observe that IDs value changed for the user `Hatter` now. 

![](/assets/Wonderland-THM/26.png)

Moving further, checking the capabilities of the file. We can find that we can run perl and SUID bit is set for it.

![](/assets/Wonderland-THM/22.png)

Using GTFOBins, we can run the following command to become root. 

![](/assets/Wonderland-THM/27.png)

## Getting Flags
Well, you got to find them by yourself now :) 