---
title: "My CEH Practical Experience!"
date: 2022-03-27
categories:
  - Certifications
tags:
  - EC-Council
  - CEH Practical
---

![](https://media-exp1.licdn.com/dms/image/C4D12AQEbfAiH3MfCUg/article-cover_image-shrink_720_1280/0/1617997407634?e=1652313600&v=beta&t=zR0Wv3XClRoxtO9sVqxu-fwpqeqEFi95ei3L3OsIxx8)

# INIT.D

Well, as you have already been searching about the CEH Practical exam and the experiences of others. Let's keep mine one within the boundaries of what actually you should expect in the exam and the things that you need to actually prepare in order to complete your exam. 

So, CEH Practical exam details are as follows. 

- 6 Hours to complete it
- No Cheating (Just to make it fair)
- Proctored Exam (EC Council Resource will be watching you!)
- Open Book Exam (You are allowed to surf the internet, check your notes and even check the CEH V11 Lab Manual)
- Grab your coffee(s) beforehand
- Have your governmental identity card/office card/student card to prove your identity.

Following systems are provided in order to complete your exam.

- Windows 2016 Machine
- Parrot OS Machine

> Well, if you are using any other linux machine like me? You got to start using the SUDO command! 

Both of the machines have active internet connection which means that you can download the tools you like. But trust, me those are not required. 

You got 20 Questions to answer within 6 hours and if you are able to answer 14 correct out of 20, well you've actually passed. `Congratulations!!!`

The main thing to note during the exam is that both VMs are slow are `TURTLE`. So, you got to turn both on at the same time and wait 5 minutes to respond back.

You are going to be getting a really `TOUGH` time just to get your hands on the VMs.

## deFr0ggy Way!

Now, the question here is. How I was able to crack the exam in just 2 hours?

Well, my technique was simple and that's it! 

- `Turn On` both VMs.
- While the VMs are turning on, read through all the questions.
- Get your mind set, whether to go first with Linux ones of Windows ones. 

Once the VMs were turned on, I have found Windows machine to be much more faster than Linx one. So, I started with the Windows challenges. 

Also, the most important thing to note is that `Usernames` and `Passwords` list is provided by default. Don't go for rockyou and other lists, you are going to waste your time. Everything is there, just don't go out of scope!

Now, the next question is what actuaally to prepare. So;

```
CEH iLabs + CEH V11 Lab Manual
```

### Tools?

#### 1. NMAP

- Get to know the basics of NMAP
- How to check alive hosts?
- How to scan for default ports?
- How to scan a particulat port only?
- How to grab banners using nmap?

#### 2. Hash Calculation

- Get to know the basics of Hash Calculation
- `HashCalc` to the rescue

#### 3. Hash Cracking

- Password list is already provided
- Use that only to crack the password
- If you got a lot of time then go with `John The Ripper` or `Hashcat`.
- If you don't have time just copy paste the hash over online cracking tools to save your time. 

#### 4. Bruteforcing

- If you got a lot of time then go with `HYDRA` as you will wither have to inspect element and then go to networks tab to grab the login path or will have to run the burp suite.
- If you got less time, go with the `WPSCAN` along with the already provided `Usernames` and `Passwords` file. 
- Use `HYDRA` to bruteforce FTP, SSH, SMB etc, with the already provided `Usernames` and `Passwords` list .

#### 5. Stego

- `OpenStego` on Windows Machine
- `StegHide` on Linux Machine
- [Future Boy Stego](https://futureboy.us/stegano/decinput.html)
- [Aperi Solve](https://aperisolve.fr/)

#### 6. Crypto

- `BcTectEncoder` on Windows Machine
- `VeraCrypt` on Windows Machine

#### 7. Sniffing

- Get yourself familiar with `Wireshark`
- Got to know how to find `GET/POST` requests
- Got to know how to follow the `HTTP Stream`
- The Wireshark `Statistics` tab is the key to few questions.

#### 8. Android Hacking

- Are there tools other than `ADB` for Android hacking?
- Got to know how to user `Terminal` and read files using `cat` command

#### 9. Services Enumeration and Exploitation

- FTP Enumeration - `hydra`, `ftp`
- SSH Enumeration - `hydra`, `ssh`
- SMB Enumeration - `smbclient`, `smbmap`

#### 10. SQL Injections

- For SQL-Injections go with `SQLMap`

#### 11. WordPress

- Got to know how to enumerate the users
- Got to know how to bruteforce user accounts
- Got to know where does the default login ppage exits.
- `WPScan` to the rescue

## Finally
Every CEH Practical Exam question is self-explanatory, it provides you with the hint about which tool to be used or which password to be used in order to open the file or to decrypt/encrypt it!


# Conclusion
I wish ya'all the very best of luck for your CEH Practical exams. 

Feel free to reach out to me in case you still got questions! 