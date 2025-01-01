---
title: Securinets Tekup mini-CTF:PCAP Master Revenge
date: 2025-01-01 03:00:00 +0800
categories: [Forensics]
tags: [CTF,Securinets]
image:
  path: /images/tekup/Forensics.png
---
## TASK 

  <img src="/images/tekup/PCAP_Master_Revenge/task.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

## Netcat Questions
Q1: Did you find credentials? What's the user

Q2: Now give me his password

Q3: What was the first command he ran

Q4: What's the malicious threat category

## Solve
let's open the file on Wireshark and start looking for teh answers
Looking into the traffic we can see in line 24 an FTP request with a USER 

  <img src="/images/tekup/PCAP_Master_Revenge/user.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Right click > Folllow > TCP Stream 

  <img src="/images/tekup/PCAP_Master_Revenge/three.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Now we can answer the three first questions
For the last question "What's the malicious threat category" we need to understand what happend after this connection

  <img src="/images/tekup/PCAP_Master_Revenge/data.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

we can see too many FTP Data packets, we need to Follow TCP Stream to understand more 

  <img src="/images/tekup/PCAP_Master_Revenge/coockies.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

This type of data is a bit familiar its browser coockies, now we can connect dots, this is a browser coockies stealer who send them via FTP 

  <img src="/images/tekup/PCAP_Master_Revenge/ftp.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

These type of keyloggers and stealers are categorized as "Trojans"
Now we can answer all the netcat questions

## Flag

  <img src="/images/tekup/PCAP_Master_Revenge/flag.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

```
Securinets{Y0U_4R3_UnD3f3T3D}
```
