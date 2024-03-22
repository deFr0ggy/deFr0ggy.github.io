---
title: "TryHackMe - Agent T"
date: 2024-03-22
categories:
  - CTF
tags:
  - CTF
  - TryHackMe
---

In this writeup we are going to solve Agent T from TryHackMe.

![](/assets/THM-AgentT/image.png)

### NMAP Scan

Starting with the initial NMAP scan, only single port can be observed to be open. 

![](/assets/THM-AgentT/image%20copy.png)

### Port 80 Enumeration

On assessing the web application in the browser we can see that we are already logged into the application as an admin user. 

![](/assets/THM-AgentT/image%20copy%202.png)

Enumerating the web application by itself yields no results. 

### Enumerating Server Responses

Taking it a step forward and using burp suite to look for potential information which can lead us to our target was the `PHP Version` being revealed in the server responses.


![](/assets/THM-AgentT/image%20copy%203.png)


### Searching For Exploit

Exploit-DB yields a promising result. 

![](/assets/THM-AgentT/image%20copy%204.png)

Taking a look onto the exploit code, we can observe that the vulnerability exists in the `User-Agent` which can lead to a Remote Code Execution. 

![](/assets/THM-AgentT/image%20copy%205.png)

### Exploitation

Either, we can use the same exploit or we can go with another exploit which provides us with the reverse shell straight away.

`https://github.com/flast101/php-8.1.0-dev-backdoor-rce/blob/main/revshell_php_8.1.0-dev.py`


![](/assets/THM-AgentT/image%20copy%206.png)

Finally, we get the reverse shell.

![](/assets/THM-AgentT/image%20copy%207.png)

As we are the root user itself, we can simply search for the flag and we are finished.

![](/assets/THM-AgentT/image%20copy%208.png)

---

### Manual Exploitation

By reading through the exploit details and the article, we observed that `User-Agentt` is the header value which is backdoored and it works when the value starts withj `zerodium`.

![](/assets/THM-AgentT/image%20copy%209.png)

Trying to replicate the same by using the user-agent and its value. Nothing is returned.

![](/assets/THM-AgentT/image%20copy%2010.png)

However, by adding anything else to the end of the user-agent value we see the same error/result as in the initial screenshot of the malicious code.

![](/assets/THM-AgentT/image%20copy%2011.png)

Now, we will try to use the `system()` command to see if we can get the contents of the current working directory.

![](/assets/THM-AgentT/image%20copy%2012.png)

We can see that it indeed works. We can finish here by directly reading the flag from `/flag.txt`. 

---


