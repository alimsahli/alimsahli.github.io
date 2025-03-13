---
title: Securinets Tekup CTF Kareem:Brika
date: 2025-01-01 02:00:00 +0800
categories: [Forensics]
tags: [CTF,Securinets]
image:
  path: /images/ctfKareem/Forensics.png
---
## TASK 

  <img src="/images/ctfKareem/brika/desc.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

## Netcat Questions
Q1:what's the compromised host IP@

Q2:List the suspicious DNS (example: DNS1_DNS2_DNS3)

Q3:which DNS has the most http traffic with the victim

Q4:In what file format the data is being exfiltrated

Q5:what's the IP@ this data is being sent to

Q6:what's the malware family name

Q7:what's the malware build date (dd-mm-yyyy)

Q8:what's the LID(Lumma ID)

Q9:what's the OS type and version of the infected system

Q10:what's the icon visible on the screenshot took from the system 

Q11:what's the md5 hash value of the stealed cookies file

## Solve
we can tell by the responses for the DNS query that the compromised host IP is 10.10.10.102

  <img src="/images/ctfKareem/brika/q2.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Q1 Answer
```
10.10.10.102
```

Now let's start by listing the domaine names from the pcap file using the filter

```
dns
```
  <img src="/images/ctfKareem/brika/q1.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Q2 Answer 
```
www.dropbox.com_ucdd5d6bc9869417d2bbd7f22955.dl.dropboxusercontent.com_teleportfilmona.online

```
Now to see wich DNS has the most http traffic with the victim we can use the filter

```
http.host==[dns]
```
  <img src="/images/ctfKareem/brika/q3.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />
Q3 Answer 
```
teleportfilmona.online
```

Now since we know that the data is being exfiltrated using http we can sort the result based on the packet size looking for the biggest packet size which will be the file being exfiltrated
  <img src="/images/ctfKareem/brika/q4.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

following the http stream we can see the the ascci code of the file being exfiltrated with the starting characters of the file being PK wich correspond to the zip file format

  <img src="/images/ctfKareem/brika/q4.4.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Q4 Answer 
```
zip
```
this zip file is being sent to the following IP address 
  <img src="/images/ctfKareem/brika/q5.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Q5 Answer 
```
104.21.11.40
```
Now we know that we are dealing with some type of stealer, we need to get the content of this zip file to know what this malware family name is. we can do this by changing the tcp steam data to raw and save it locally the we can use binwalk to extract its content 

  <img src="/images/ctfKareem/brika/q6.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

  <img src="/images/ctfKareem/brika/q6.6.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

  <img src="/images/ctfKareem/brika/q6.7.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

inside the file System.txt we can see a lot of informations about the stealer and the system

  <img src="/images/ctfKareem/brika/q6.8.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Q6 Answer 
```
LummaC2
```
Q7 Answer 
```
09-10-2023
```
Q8 Answer 
```
Zbbaau--Лакосте
```
Q9 Answer 
```
Windows 10 (10.0.19045)
```
We can also see a screenshot from the victim machine

  <img src="/images/ctfKareem/brika/q10.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Q10 Answer 
```
Recycle Bin
```
Now for the last part we need to find the packet wich exfiltrates the coockies from the system . we can do this by filtering the packets by the IP address of the victim machine and look for the packet that contains another zip file.

  <img src="/images/ctfKareem/brika/q11.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

we need to extract this zip file by doing the same process again 

  <img src="/images/ctfKareem/brika/q11.1.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

now to answer the last question we will calculate its md5 hash 

  <img src="/images/ctfKareem/brika/q111.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Q11 Answer 
```
b0c1133c8e8d4d7c359249d4e3208cb5
```
## Flag

  <img src="/images/ctfKareem/brika/flag.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />
  
Flag
```
Securinets{Y0u_D3serV3_4_Br1KA}
```