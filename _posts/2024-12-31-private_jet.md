---
title: Securinets Tekup mini-CTF:Private Jet
date: 2024-12-31 19:00:00 +0800
categories: [CTF]
tags: [CTF,Securinets,OSINT]
image:
  path: /images/tekup/sectekup.jpg
---
## TASK 

  <img src="/images/tekup/privatejet/task.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

## Solve 
The go to website to look for details about planes especially private jets is the FAA (Federal Aviation Administration) website [Link](https://registry.faa.gov/aircraftinquiry/search/nnumberinquiry)

  <img src="/images/tekup/privatejet/site.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

now we put the given jet number to see it's details

  <img src="/images/tekup/privatejet/number.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

We need to know the owner name to complete the flag, we can just google it !

  <img src="/images/tekup/privatejet/bill.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

And like that we have all the parts of the flag

## Flag

```
Securinets{Gulfstream_Aerospace_Corp_G650ER_2018_Bill_Gates}
```