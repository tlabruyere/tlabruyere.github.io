# Privilege Escalation

## Linux Privilege Escalation

There exist script that test a system for various privilege exploit

Methods:

1. Kernel Exploits 

    Find a bug in the kernel, you can gain privilege execution on a system such a the dirtycow exploit.

    Dirty COW is a bug reported in 2016 (CVE-2016-5195). It is "A race condition was found in the way the Linux kernel's memory subsystem handled the copy-on-write (COW) breakage of private read-only memory mappings. An unprivileged local user could use this flaw to gain write access to otherwise read-only memory mappings and thus increase their privileges on the system." (RH)

1. Stored Passwords on disk or in history

1. Weak file Permissions on /etc/shadow

    If normal users can read `/etc/shadow`, then password crackers can try to guess the password. `unshadow` is an example of this where you collect `/etc/shadow` and `/etc/passwd` and it will try to crack the password. 

1. Old ssh keys

1. Abusing SUID programs

    `sudo -l` prints programs that the normal user can run as root. If those programs can execute other programs, those programs will be ran at root level.
    
    Bad Programs:
    - Editors such as vi
    - python
    - ...
