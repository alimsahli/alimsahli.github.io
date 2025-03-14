---
title: Securinets Tekup CTF Kareem:Tajin
date: 2025-03-13 19:00:00 +0800
categories: [Forensics]
tags: [CTF,Securinets]
image:
  path: /images/ctfKareem/Forensics.png
---
## TASK 

  <img src="/images/ctfKareem/tajin/desc.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

## Solve
we have a random packets, lets start analysing them, we can a familier type of data in a packet its JWT token.

<img src="/images/ctfKareem/tajin/p1.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

<img src="/images/ctfKareem/tajin/p2.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Maybe we need to crack it and get the secret, It may come handy later.

we can use this website to crack it [website](https://jwt-cracker.online/)

<img src="/images/ctfKareem/tajin/p3.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

After craccking the secret is “babygirl” maybe we need this , we keep analysing the pcap.
Filtring by the largest packets we can find a TCP that contain large data.

<img src="/images/ctfKareem/tajin/p4.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

Decrypting it we can see the content of this file

<img src="/images/ctfKareem/tajin/p5.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

```

files = []

for file in os.listdir():
    if os.path.isfile(file):
        files.append(file)

word = "REDACTED"
key = hashlib.sha256(word.encode()).digest()
fernet_key = base64.urlsafe_b64encode(key[:32]) 

cipher = Fernet(fernet_key)
for file in files:
    with open(file, "rb") as thefile:
        contents = thefile.read()
    contents_encrypted = cipher.encrypt(contents)
    with open(file, "wb") as thefile:
        thefile.write(contents_encrypted)

```
Analysing this python script we can see that this script is using fernet to encrypt some files using a word to create a key, this word is the secret we found earlier in the jwt.

Now we need to find the files or their encrypted content so we can reverse it and get the flag.
Here we have all tcp packets in the pcap file.

<img src="/images/ctfKareem/tajin/p6.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

we need to create a script using scapy to extract the data from these packets 

```python
from scapy.all import rdpcap, TCP, Raw

def extract_tcp_data_to_txt(pcap_file, output_txt="extracted_tcp_stream_0.txt"):
    packets = rdpcap(pcap_file)  # Read packets from PCAP
    extracted_text = ""

    for pkt in packets:
        if pkt.haslayer(TCP) and pkt.haslayer(Raw):
            extracted_text += pkt[Raw].load.decode(errors="ignore")  # Extract TCP payload

    with open(output_txt, "w", encoding="utf-8") as output_file:
        output_file.write(extracted_text)

    print(f"{output_txt}")

extract_tcp_data_to_txt("capture.pcap")

```
we will get the ecrypted files content with other missleading strings 

<img src="/images/ctfKareem/tajin/p7.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

we need to extarct these strings to decrypt them with the revesed program we will create.

```
gAAAAABnzwQW7g6m_JQ9x064KtmII_9Vvve10VfvDl4h63ObtgxZt8hwbjhiRTkGvZ41MboJ_RZFjhribtkXDZxAm3N_aS9JVEgSkQx731BsbWQQVTL7P1fSviUBZCUCW-Y1JdYP1IVE
gAAAAABnzwQWp09ajpEFQ9Ad-7aiCE4H8B2igOihBN5hxSY4B06KdQ6cDEUSwMlzWwAwgLnE0g-NnpRwDR9bErLAS3nmVzGq68lBOYFT-9ayXSmoSWn_Mtc=
gAAAAABnzwQW4vWWuVowVljCnQaJI0uPLj3uZswx5QQRB2PMXK9JrggOzAMERaYKu0YAXaQthjEtVxRYziazsVwSpInRY8o7ezRW3lS6J58kHFoJEpgShQ4=
gAAAAABnzwQWxZ-oI4GMoK-QvIelxxIpS6lzj5yKkcVXGOqD8PBr1v4cXBjqrxHfwgcmn4euxTWAfBDxhvnhlM5GWDJ5Zv0Le7OUsrsHlYDkOgfJPC__HyenRDPGAO1Om0MKi-sykdWB
```
here is the decyption script 

```python
import hashlib
import base64
from cryptography.fernet import Fernet, InvalidToken


word = "babygirl"
key = hashlib.sha256(word.encode()).digest()
fernet_key = base64.urlsafe_b64encode(key[:32])
cipher = Fernet(fernet_key)


encrypted_strings = [
    "gAAAAABnzwQW7g6m_JQ9x064KtmII_9Vvve10VfvDl4h63ObtgxZt8hwbjhiRTkGvZ41MboJ_RZFjhribtkXDZxAm3N_aS9JVEgSkQx731BsbWQQVTL7P1fSviUBZCUCW-Y1JdYP1IVE",  
    "gAAAAABnzwQWp09ajpEFQ9Ad-7aiCE4H8B2igOihBN5hxSY4B06KdQ6cDEUSwMlzWwAwgLnE0g-NnpRwDR9bErLAS3nmVzGq68lBOYFT-9ayXSmoSWn_Mtc=",
    "gAAAAABnzwQW4vWWuVowVljCnQaJI0uPLj3uZswx5QQRB2PMXK9JrggOzAMERaYKu0YAXaQthjEtVxRYziazsVwSpInRY8o7ezRW3lS6J58kHFoJEpgShQ4=",
    "gAAAAABnzwQWxZ-oI4GMoK-QvIelxxIpS6lzj5yKkcVXGOqD8PBr1v4cXBjqrxHfwgcmn4euxTWAfBDxhvnhlM5GWDJ5Zv0Le7OUsrsHlYDkOgfJPC__HyenRDPGAO1Om0MKi-sykdWB"
]


for encrypted_string in encrypted_strings:
    try:
        decrypted_contents = cipher.decrypt(encrypted_string.encode())
        print(decrypted_contents.decode())
    except InvalidToken:
        print("Decryption failed: Invalid key or corrupted data.")


```
<img src="/images/ctfKareem/tajin/p8.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

## Flag

```
Securinets{Y0u_S4avEd_yoUr_F1le5}
```