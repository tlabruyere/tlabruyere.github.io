---
layout: post
title: Recon with nmap
categories: Notes
author: tlabruyere
tags: Notes, nmap
---

Nmap is an open-source network scanner that enumerates ports available on a target device. It has the capability to fingerprint and run basic tools against a target to understand the security of the target.

Typical example:

```bash
nmap -sC -sV -oN nmap/initial
```

`-sC` - Performs a script scan using the default set of scripts.
`-sV` - Probe open ports to determine service/version info
`-oN` - Direct to file

