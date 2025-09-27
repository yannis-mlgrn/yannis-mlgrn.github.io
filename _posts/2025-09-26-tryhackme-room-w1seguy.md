---
layout: post
title: Tryhackme Challenge — W1seGuy
categories: [TryHackMe, Cryptography]
tags: [XOR,Crypto]
description: W1seGuy cryptography challenge write-up
math: true
---

![Logo](/assets/img/posts/2025-09-26-tryhackme-room-w1seguy/logo.png){: w="400" h="400" }

Welcome to **W1seGuy**, a beginner-friendly TryHackMe room focused on decryption. The room is classified as *easy* — you only need basic programming skills and practical, hands-on knowledge.

After downloading the source code and launching the machine, we can connect to the service on the provided TCP port:

```bash
$ telnet 10.10.10.241 1337
Trying 10.10.10.241...
Connected to 10.10.10.241.
Escape character is '^]'.
This XOR encoded text has flag 1: 361e1e4b0553373f5e01272e2771011662305b1623382103140e1a2a582010222a0000102e1c4208
What is the encryption key?

```
As we can see, the service returns an XOR‑encoded hex string. To proceed we need the key.
So let’s inspect the source code.

```python
def start(server):
    res = ''.join(random.choices(string.ascii_letters + string.digits, k=5))
    key = str(res)
    hex_encoded = setup(server, key)
    send_message(server, "This XOR encoded text has flag 1: " + hex_encoded + "\n")
```
The key is a 5 random ascii letters and digits long. After build this chain, it is encoded with an hexadecimal encryption.

```python
def setup(server, key):
    flag = 'THM{thisisafakeflag}' 
    xored = ""

    for i in range(0,len(flag)):
        xored += chr(ord(flag[i]) ^ ord(key[i%len(key)]))

    hex_encoded = xored.encode().hex()
    return hex_encoded
```

The setup fonction is used to encrypt the flag with a XOR encryption. The first element of the flag is XOR with the first element of the key and so on.
> e.g: flag: THM{test}, key: eD54dé684 \
> T ^ e \
>H ^ D \
>M ^ 5 \
> ...
{: .prompt-tip }

...

One important property of the XOR (exclusive OR) operation is its **reversibility**. This means that if you XOR a value with another value twice, you can recover the original value. Mathematically, this is expressed as:

$$
(str \oplus key) \oplus str = key
$$

So, to find the key we need to use the XOR operation between the outut given and the begin of our flag the we know `THM{`.
We'll find the 4 first characters of our key.

Let's use [cyberchef](https://cyberchef.org/)

The recipe to find the key: 
1. Use the 8 first characters of the output and XOR with `THM{`
2. Keep the 4 characters long output

![Logo](/assets/img/posts/2025-09-26-tryhackme-room-w1seguy/cyberchef1.png){: w="700" h="400" }

Afterwards, to find the flag: 

1. Decode the entire hex string to bytes (From Hex).

2. XOR the decoded bytes with the candidate key (length 5, repeating). If the last character of the decrypted plaintext is }, you likely found the correct 5th key character.

3. Verify you have a properly formatted flag.

![Logo](/assets/img/posts/2025-09-26-tryhackme-room-w1seguy/cyberchef2.png){: w="700" h="400" }

WE FIND THE FIRST FLAG ! 

Now, we can use the key found to get the second flag: 

```bash
[Sep 26, 2025 - 19:30:36 (CEST)] exegol-THM /workspace # telnet 10.10.10.241 1337             
Trying 10.10.10.241...
Connected to 10.10.10.241.
Escape character is '^]'.
This XOR encoded text has flag 1: 361e1e4b0553373f5e01272e2771011662305b1623382103140e1a2a582010222a0000102e1c4208
What is the encryption key? bVS0u
Congrats! That is the correct key! Here is flag 2: THM{Br***********************_nO?}
Connection closed by foreign host.
```

WE FIND THE SECOND FLAG !!