---
title: Securinets Tekup mini-CTF:PCAP Master
date: 2025-01-01 01:00:00 +0800
categories: [Forensics]
tags: [CTF,Securinets]
image:
  path: /images/tekup/sectekup.jpg
---
## TASK 

  <img src="/images/tekup/pcap_master/task.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

## Netcat Questions
Q1:What's the victim's IP address?

Q2:What's the victim's MAC address?

Q3:What's the attacker's IP address?

Q4:What's the attacker's MAC address?

Q5:What's the executable file name?

Q6:What's the family name of this malware?

## Solve
let's open the file on Wireshark and start looking for suspicious activities 
First let's see if there is any POST or GET request using this filter 

```
http.request.method==GET or http.request.method==POST
``` 
  <img src="/images/tekup/pcap_master/putin.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

As we can see we have three GET requests one of them is for an executable file but first we need to confirm if this file is malicious 
we can export the file by clicking on file > Export Objects > HTTP > (choose putingods.exe) > Save
After downloading the file we need to get it's hash to look for it on virustotal
```bash
md5sum putingods.exe
```
```
991129c128ccee33d434849d79d28434  putingods.exe
```
  <img src="/images/tekup/pcap_master/vtotal.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Now since we confirmed that this is the malicious file we are looking for we need to go back to wireshark to get the victim's IP & MAC address and the attacker's IP & MAC address

  <img src="/images/tekup/pcap_master/mac.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Now we have all netcat answers except for the family name of the malware, we need to get back to virustotal to get it 

  <img src="/images/tekup/pcap_master/name.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

We can see the family name is RedLine and its a Trojan 

## Flag

  <img src="/images/tekup/pcap_master/flag.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

```
Securinets{Y0U_4R3_M4LW4R3_M4ST3R}
```

