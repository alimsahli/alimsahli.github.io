---
title: Securinets isetZg:The Engima Of The Mirror Maze 
date: 2024-12-29 18:36:00 +0800
categories: [CTF]
tags: [CTF,Securinets,Cryptography]
image:
  path: /images/isetzgctf/pdp.png
---
## TASK 

  <img src="/images/isetzgctf/mirror.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

## Handout 

```
The Enigma of the Mirror Maze
You find yourself lost in a labyrinth of mirrors, where each step seems to distort your perception. The walls shimmer with an eerie glow, and strange inscriptions appear before you. A cryptic message is etched into the reflective surface: “Only the true reflection will reveal the path forward.”

As you wander deeper, you begin to feel disoriented. A cold shiver runs down your spine. Then, a sudden whisper echoes in the maze: “Solve the puzzle of the mirrors, for it holds the key to the exit.”

You turn around, and a hidden door slowly opens. Inside, you see an ancient desk with a peculiar symbol — a mirrored cipher. On the desk is a parchment with this strange sequence of letters: “KUGENTLUIPKGCQCKQESUVUMETN”

A strange image on the wall above the desk catches your attention. It shows an intricate arrangement of mirrors, each with a letter reflecting off it:

EACGUFMOZYKDPWSLQRVNBHTXI
You are beginning to make sense of it. But before you can act, the lights flicker and an ominous voice whispers again, “To escape, remember that what you see in the mirror is not always the truth. Look for what lies beneath the surface.”

Suddenly, the puzzle becomes clear. The cipher is a simple reflection – you need to figure out how the letters in the cipher reflect across the grid. But there's more to this: the message you need is hidden in plain sight, if only you understand the mirrored pattern.

```
## Identifying The Cipher

To identify the cipher used im going to use this online tool [dcode](https://www.dcode.fr/cipher-identifier)

  <img src="/images/isetzgctf/cipher.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

## Decrypting the message 

As we can see it's most likely to be Two-square Cipher, But in this case we only have a encrypted message and a matrice string. So its has to be PlayFair Cipher.

  <img src="/images/isetzgctf/message.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />

and like that we have sucessfully decrypted the message, Now we can submit it .


  <img src="/images/isetzgctf/mrflag.png" alt="Securinets" style="width: auto; height: auto; margin-right: 10%;" />
