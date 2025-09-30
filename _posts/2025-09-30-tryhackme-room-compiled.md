---
layout: post
title: TryHackMe Room — Compiled
categories:
- TryHackMe
- Reverse Engineering
tags:
- Reverse
description: A simple tryHackMe challenge write-up
date: 2025-09-30 12:37 +0200
---
![alt text](/assets/img/posts/tryhackme-room-compiled/logo.svg){: w="400" h="400" }

Hi, Today I'll present you a simple reverse engineering challenge from TryHackme named **Compiled**.
In this challenge, you have to reverse engineering the binary and find out the correct password.

First, let's download the binary and execute it :

```bash
$ ls
Compiled-1688545393558.Compiled

$ chmod +x Compiled-1688545393558.Compiled  

$ ./Compiled-1688545393558.Compiled 
Password: test
Try again!# 
```
{: .nolineno }

To find how the binary works, let's use ghidra to disassemble it !

![alt text](/assets/img/posts/tryhackme-room-compiled/ghidra.png){: w="700" h="400" }

So, what does this code: 
The main function get the input and store the string between `DoYouEven` and `CTF` `local_28` variable and compare it with `_init` string.

Explaination about the scanf function : 

The line:

```c
scanf("DoYouEven%sCTF", local_28);
```

may look like it expects the full input to be something like: `DoYouEven<your_input>CTF`..

However, due to how `scanf` and the `%s` specifier work, this is not strictly required.

Although `scanf("DoYouEven%sCTF", local_28);` expects to match "CTF" after reading the input, it doesn't have to. 
As long as the input starts with "DoYouEven" and %s captures a valid value like "_init", scanf will store it in `local_28` before failing to match the final "CTF". This partial match is enough, since the program only checks the content of `local_28` afterward.

So, to validate the following condition :
```c
iVar1 = strcmp(local_28,"_init"); if (iVar1 == 0) { printf("Correct!"); }
```

The password need to :
- begin by "DoYouEven"
- Contain "_init"   


Finally, the password should be : `DoYouEven_init`

```bash
$ ./Compiled-1688545393558.Compiled
Password: DoYouEven_init
Correct!
```

