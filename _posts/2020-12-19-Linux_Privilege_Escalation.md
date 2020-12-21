# Privilege Escalation

## Linux Privilege Escalation

There exist script that test a system for various privilege exploit

Methods:

1. Kernel Exploits 

    Find a bug in the kernel, you can gain privilege execution on a system such a the dirtycow exploit.

    Dirty COW is a bug reported in 2016 (CVE-2016-5195). It is "A race condition was found in the way the Linux kernel's memory subsystem handled the copy-on-write (COW) breakage of private read-only memory mappings. An unprivileged local user could use this flaw to gain write access to otherwise read-only memory mappings and thus increase their privileges on the system." (RH)

1. Stored Passwords on disk or in history

    Example:
    
    Openvpn uses config files to connect to the vpn. The config file can contain a link to files containing authentication credentials. 

1. Weak file Permissions on /etc/shadow

    If normal users can read `/etc/shadow`, then password crackers can try to guess the password. `unshadow` is an example of this where you collect `/etc/shadow` and `/etc/passwd` and it will try to crack the password. 

1. Old ssh keys

1. Abusing SUID programs

    Find SUID programs: 

    1. `sudo -l` prints programs that the normal user can run as root. If those programs can execute other programs, those programs will be ran at root level.

    1. List all programs on disk with perms of 4000 (suid)

    ```bash
    find / -user root -perm /4000 2>/dev/null
    ```

    Methods:

    Suid programs have the potential to be abused by bad actors to escalate privileges. [GTFObins](https://gtfobins.github.io) is a resource that documents ways to misuse binaries.
    
    1. Example of programs that could potentially spawn shells:
        - Editors such as vi
        - python
        - bash (shells)
        - nmap
        - ...

    1. Abusing file reads with Suid applications

        ```bash
        sudo apache2 -f /etc/shadow
        ```

    1. Abusing Suid applications with LD_PRELOAD

    ```c
    #include <stdio.h>
    #include <sys/types.h>
    #include <stdlib.h>

    void _init() {
        unsetenv("LD_PRELOAD");
        setgid(0);
        setuid(0);
        system("/bin/bash");
    }
    ```



1.  