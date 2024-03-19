---
title: "TryHackMe - ConvertMyVideo"
date: 2024-03-19
categories:
  - CTF
tags:
  - CTF
  - TryHackMe
---

In this writeup we are going to solve ConvertMyVideo from TryHackMe in preparation for the PNPT exam.

![](/assets/THM-ConvertMyVideo/image.png)

### NMAP Scan

Starting with the initial NMAP scan, we observed 2 ports to be open i.e. 80 and 22 i.e. HTTP and SSH respectively. 

![](/assets/THM-ConvertMyVideo/image%20copy.png)

### Web Server Enumeration

Opening up the IP in the browser displays us the page to convert the video, so assuming that this only selects the YT urls. 

![](/assets/THM-ConvertMyVideo/image%20copy%202.png)

Checking the code of the `main.js` file yields the same results. However, the YT path till `watch?v=` remains the same and then it takes the YT id value. 

![](/assets/THM-ConvertMyVideo/image%20copy%203.png)

Trying to look for any potential hidden paths. We observe that there is an admin portal.

![](/assets/THM-ConvertMyVideo/image%20copy%204.png)

We need to have the username and password in order to login. 

![](/assets/THM-ConvertMyVideo/image%20copy%205.png)

### Testing ConvertMyVideo Functionality

As there is no other important item to look at probably the YT functionality is our way in. 

Just trying an ls payload breaks the syntax. Thus, we need to play around with it to get the reverse shell most probably.

![](/assets/THM-ConvertMyVideo/image%20copy%206.png)

Searching over the internet, we found that this issue was fixed and this is due to the reason that some characters can not be handled directly on the linux machine due to python limitations. This, these should ideally be encoded if we really want to go through it. 

![](/assets/THM-ConvertMyVideo/image%20copy%207.png)

### Exploitation

As we know that the underlying operating system is LINUX, probably there is a WGET command. Lets try to host python server on the local machine and try to access it via the functionality. 

There were tons of things which were executed to try to bypass to get the files onto the server. The only solution for this case was to use ${IFS} i.e. Internal Field Saparator. We had to use this as by default its value is set to a SPACE and if we put the regular space we are not able to make the call success. 

![](/assets/THM-ConvertMyVideo/image%20copy%208.png)

Once the file has been downloaded on the server, using the same technique we can have it executed. Thus, gaining a successful reverse shell.

![](/assets/THM-ConvertMyVideo/image%20copy%209.png)

Now, searching for sensitive information on the machine to escalate our priviliges. We observe a hidden file which includes the username and password for the user which is able to login using the admin portal.

![](/assets/THM-ConvertMyVideo/image%20copy%2010.png)

Cracking the password, we have it in the clear text. 

![](/assets/THM-ConvertMyVideo/image%20copy%2011.png)

Checking the `index.php` file, it gives us a hint of a file in the downloads directory?

![](/assets/THM-ConvertMyVideo/image%20copy%2012.png)

The file is named as `clean.sh` and it basically deleted the `Downloads` folder.

![](/assets/THM-ConvertMyVideo/image%20copy%2013.png)

On overwriting the file, we were able to overwrite it successfully and thus, gaining a root access. This was one of the ways.

![](/assets/THM-ConvertMyVideo/image%20copy%2014.png)



The other method was to login into the user account which was found hidden in the file. 

![](/assets/THM-ConvertMyVideo/image%20copy%2017.png)

On clicking the `Clean Downloads` we can see the command being executed. 

![](/assets/THM-ConvertMyVideo/image%20copy%2018.png)

Changing the commands to the `Reverse Shell` we can easily get the `root` shell.

![](/assets/THM-ConvertMyVideo/image%20copy%2019.png)

Well, we can verify the `clean.sh` script as well by checking the `cronjobs` of the root user.

![](/assets/THM-ConvertMyVideo/image%20copy%2016.png)

---
