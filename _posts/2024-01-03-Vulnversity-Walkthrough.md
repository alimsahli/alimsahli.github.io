---
title: Vulnversity Walkthrough
date: 2024-01-03 11:33:00 +0800
categories: [Walkthrough]
tags: [TryHackMe]
---

## Task 2 Reconnaissance 

start nmap scan 
'''bash
nmap -sV 10.10.160
'''
  <img src="/images/vulnv/vuln1.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

### Q1: Scan the box; how many ports are open?
Answer: 6

### Q2: What version of the squid proxy is running on the machine?

Answer: 3.5.12

### Q3: How many ports will Nmap scan if the flag -p-400 was used?

Answer: 400

### Q4: What is the most likely operating system this machine is running?

Answer: Ubuntu

### Q5: What port is the web server running on?

Answer: 3333

### Q6: What is the flag for enabling verbose mode using Nmap?

Answer: -v

## Task 3 Locating directories using Gobuster

Download gobuster :
'''bash
sudo apt-get install gobuster
'''
Download Directory wordlist from dirbuster github :
'''bash
wget https://github.com/daviddias/node-dirbuster/blob/master/lists/directory-list-1.0.txt
'''
Run gobuster :
'''bash
gobuster dir -u http://[Machine_IP]:3333 -w directory-list-1.0.txt
'''

  <img src="/images/vulnv/vuln2.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

### Q1: What is the directory that has an upload form page?

Answer: /internal/

## Task 4 Compromise the Webserver

### Q1: What common file type you’d want to upload to exploit the server is blocked? Try a couple to find out.

Answer: .php

Intercept the POST request with burpsuite

  <img src="/images/vulnv/vuln3.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />
