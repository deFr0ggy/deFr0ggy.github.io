---
title: "Chopper - An Automated Website Secuirity Headers Analyzer"
date: 2022-03-13
categories:
  - blog
tags:
  - Tools
  - Python
  - Web Hacking
---

In recent times, while performing security assessments of web applications, it has always been a challenge to keep an eye on HTTP Security Headers. Just to ensure they are properly set on the server side and are added into the response all the time. It is also very hectic, if you are working with teams and they keep on fixing the security headers and you are required to manually test them everytime. Compared to SecurityHeaders.com website, which is one the finest so far, the application URL is required to be supplied and it checks whether the HTTP Security Headers are present in the response on not.

There are internal application and also sometimes, you won’t have access to the internet during an engagement/regular testing. For the same, I have always felt the need of a tool which can fix this issue. Thus, Chopper came into existence during my free time.

![](/assets/chopper/ChopperBanner.png)

Chopper is a python script to scrape HTTP Headers from the requests. All you need is to supply a valid domain name and it will automatically check for security related headers, thus saving much of your time.
Currently Chopper is able to check the following headers and flags.

1. Content-Security-Policy
2. X-XSS-Protection
3. X-Frame-Headers
4. X-Content-Type
5. Strict-Transport-Security
6. Referrer-Policy
7. Feature-Policy
8. Cache-Control Policy
9. Access-Control-Allow-Origin
10. Access-Control-Allow-Credentials
11. HttpOnly Flag
12. Secure Flag

Chopper also provides with the complete list of headers. Thus, providing a better view of all the headers.
Testing it against the applications, I have found it to be working like a charm. Suppose we have the following web application and we are required to check which of the HTTP Security Headers are present.

> http://testhtml5.vulnweb.com/#/popular

Running Chopper will yield the following results in which we can clearly observe the missing security headers as well as the complete headers which were present in the response.

![](/assets/chopper/ChopperDemo.png)

Thus, Chopper saves a lot of time, all we need is to run it against the application we really want to test and it does not exploits anything.

