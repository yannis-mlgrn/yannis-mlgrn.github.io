---
layout: post
title: TryHackMe Challenge - The Game
categories: [TryHackMe, Game Hacking]
tags: [Reverse, game hacking]
date: 2025-10-01 14:34 +0200
description: The Game challenge write-up
---

![alt text](/assets/img/posts/tryhackme-room-the-game/logo.png){: w="400" h="400" }

Welcome to **The Game** challenge, a very easy game hacking challenge.

I was on linux and unable to run it because it was a `.exe` game.

My first step was to perform a test to check if the flag was hardcoded directly into the executable (.exe) file.
To do this, I used the strings command, which is a utility that searches through a binary file for readable text strings. 
By running strings on the .exe, I could easily identify if the flag appeared as a plain text string within the binary code of the executable.

```bash
$ strings Tetrix.exe | grep "THM"
, FEATURE_ARITHMETIC
ATTENUATION_LOGARITHMIC
PATHFINDING_ALGORITHM_ASTAR
-4PTHMM
THM{I_*****************ALL}
```

The flag: `THM{I_*****************ALL}`

We found the flag, so it was not a tricky game hacking challenge :) .