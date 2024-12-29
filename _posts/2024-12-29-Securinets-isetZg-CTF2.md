---
title: Securinets isetZg:Compression Madness 
date: 2024-12-29 18:36:00 +0800
categories: [CTF]
tags: [CTF,Securinets,Forensics]
image:
  path: /images/isetzgctf/pdpp.png
---
## TASK 

  <img src="/images/isetzgctf/compression.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

## Handout 

The handout is a endless zip file, in this case i need to create a script to unzip everything automatically .

## Endless zip extraction

Here is the bash script i used

```bash
    unzip file.zip
    prev_file="200.zip"

    while [ -f "$prev_file" ]
    do
        echo "Contents of $prev_file:"
        unzip -l "$prev_file"

        curr_file=$(unzip -Z1 "$prev_file")
        unzip "$prev_file" -d "$(dirname "$prev_file")"
        rm "$prev_file"
        prev_file="$curr_file"

        if [ "$prev_file" = "stop.zip" ]; then
            break
        fi
    done
```
The result is two files one is password.txt and the other is another endless .tar files

  <img src="/images/isetzgctf/zipsh.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

The password.txt content :
```
Password : HalfWay400                                                                                                                                                                                                                                    
```
## Endless Tar extraction 

Here is the bash script i used

```bash
    prev_file="200.tar"

    while [ -f "$prev_file" ]
    do
        echo "Contents of $prev_file:"
        tar -tf "$prev_file" 
        curr_file=$(tar -tf "$prev_file" | head -n 1)
        mkdir -p temp
        tar -xf "$prev_file" -C temp
        prev_file="temp/$curr_file"
        if [ "$prev_file" = "stop.tar" ]; then
            break
        fi
    done
```
  <img src="/images/isetzgctf/tarsh.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

## Flag

Now we have the pdf and the password for it . 

  <img src="/images/isetzgctf/flagzip.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Now we only have to submit it 

  <img src="/images/isetzgctf/submit.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

