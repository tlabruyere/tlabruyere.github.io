---
layout: post
title: Recon with nmap
categories: Notes
author: tlabruyere
tags: notes, nmap
---

Nmap is an open-source network scanner that enumerates ports available on a target device. It has the capability to fingerprint and run basic tools against a target to understand the security of the target.

Typical example:

```bash
nmap -sC -sV -oN nmap/initial $IP
```

- `-sV`	- Attempts to determine the version of the services running
- `-p`  - <x> or -p-	Port scan for port <x> or scan all ports
- `-Pn` - Disable host discovery and just scan for open ports
- `-A`  - OS and version detection, executes in-build scripts for further enumeration 
- `-sC` - with the default nmap scripts
- `-v`  - Verbose mode
- `-sU` - UDP port scan
- `-sS` - TCP SYN port scan
- `-oN` - Direct to file