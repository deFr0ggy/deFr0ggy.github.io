---
title: "Proving Grounds - FunBoxEasyEnum"
date: 2022-03-19
categories:
  - blog
tags:
  - CTF
  - Offensive Security
  - Proving Grounds
---

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%202.31.06%20PM.png)

In this write-up we are going to be solving FunBoxEasyEnum from Offensive Security Proving Grounds. 

As always, we will begin with NMAP to find possible open ports. 

### NMAP Scan

We can observe that there are 2 ports open. 

| Port Number  | Service Name  | Service Version | 
|---|---|---| 
| 80 | HTTP  | Apache httpd 2.4.29  |
| 22 | SSH | OpenSSH 7.6p1 Ubuntu 4ubuntu0.3|



![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%202.33.09%20PM.png)

## Port 80 Enumeraiton

On opening the IP address in the browser, we are greeted with the Ubuntu Default Page. 

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%202.37.26%20PM.png)

In order to find directories we will be needing to perform directory enumerations. 

## Directory Enumeration

On enumerating the directory using dirsearch, we observed the following directories. 

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%202.43.50%20PM.png)

### JavaScript Directory

On trying to open the JavaScript directory, we are greeted with 403 Forbidden meaning that we don't have access to the following directory. 

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%202.44.14%20PM.png)


### PhpMyAdmin

On checking the PhpMyAdmin endpoint, we are greeted with the PhpMyAdmin Login Page. 

Tried checking the default user credentials here and they didn't seemed to be working. 

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%202.44.23%20PM.png)

### robots.txt

This endpoint just says that it allows 'Enum_this_Box" user agent for spidering. 

Tried supplying this value using CURL on JAVASCRIPT encdpoint and it didn't seemed to be working.

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%202.44.05%20PM.png)

## We are missing something! 

So, we have checked all the endpoints that we were able to find, but we still hasn't been able to get the initial access. 

Establishing the context, we know the following. 

- Robots.txt - We shall scan for .txt files
- index.php - We shall scan for .php files

So, using the dirsearch along with --suffix .php, we are noe able to find another endpoint. 

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%203.01.08%20PM.png)

## MINI.PHP

So, this is a MINI Upload Shell. 

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%203.02.20%20PM.png)

We can create our PHP Reverse Shell, upload it here and can have out netcat listener to get a reverse shell.

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%203.04.09%20PM.png)

## Upgrading Shell

Using Python3 we will upgrade our shell to work properly.

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%203.05.21%20PM.png)

## cat /etc/passwd

We can find a HASH within the /etc/passwd, normally the hashes are in /etc/shadow.

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%203.08.31%20PM.png)


Let's crack this hash.

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%203.09.59%20PM.png)

## Logging in as ORACLE

Now, using the cracked hash, we will log into the ORACLE user. 

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%203.10.14%20PM.png)

## Enumerating PhpMyAdmin

The config files for PhpMyAdmin are in `/etc/phpmyadmin`. We can observe that in `config-db.php` we have the username and password for the mysql.

On checking the MySQL nothing worthy was found.

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%203.15.38%20PM.png)

## SSH Bruteforce

We can gather the usernames from the /home directory, while we already have the password from PhpMyAdmin config file. We can use hydra to bruteforce other accounts to check if any user has this password set.

Which luckily, Karla account does.

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%203.20.11%20PM.png)

## Logging in as Karla

Using the username and cracked password, we will nbow log into Karla Account. 

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%203.20.47%20PM.png)

### Checking Karla Permissions

![](/assets/FunBoxEasyEnum/Screenshot%202022-03-19%20at%203.21.01%20PM.png)

## Finding Local Flag

> find / -name "local.txt" -type f 2>/dev/null

## Finding Proof Flag

> find / -name "proof.txt" -type f>/dev/null

## Finding Root Flag

> find / -name "root.txt" -type f>/dev/null
