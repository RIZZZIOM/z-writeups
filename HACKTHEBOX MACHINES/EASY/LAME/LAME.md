![[IMAGES/x.png]]
# GETTING STARTED

Lame is a beginner level machine, requiring only one exploit to obtain root access. It was the first machine published on Hack The Box and was often the first machine for new users prior to its retirement.

> [!NOTE] 
> This writeup documents the steps that successfully led to pwnage of the machine. It does not include the dead-end steps encountered during the process. This is just my take on pwning the machine and you are welcome to choose a different path.

| MACHINE | IP          |
| ------- | ----------- |
| kali    | 10.10.14.34 |
| lame    | 10.10.10.3  |

# RECONNAISSANCE

I started by performing a service version and default script scan on the target using **nmap**.

![[IMAGES/1.png]]
![[IMAGES/2.png]]

# CAPTURING USER FLAG

I looked for exploits for *vsftpd 2.3.4* but didn't find any that worked on the target. So, I tried looking for exploits for the *SMB* version revealed through the script scan.

![[IMAGES/3.png]]

Hence, I looked for ways to exploit the CVE and found a tool on GitHub: [https://github.com/amriunix/CVE-2007-2447](https://github.com/amriunix/CVE-2007-2447).

I downloaded this onto my system and ran it.

![[IMAGES/4.png]]

I started a listener and then executed the script.

```bash

rlwrap nc -lnvp 8080
```

![[IMAGES/5.png]]

![[IMAGES/6.png]]

I directly gained *root* access. Next I spawned a TTY shell for better usability.

![[IMAGES/7.png]]

Finally, I looked into the directories in */home* and found the user flag in */home/makis*.

![[IMAGES/8.png]]

# CAPTURING ROOT FLAG

Since I was a root user, I went into the *root* directory and found the final flag as well.

![[IMAGES/9.png]]

# CLOSURE

Here's how I pwned the machine:
- Gained root access by exploiting the *SMB* vulnerability.
- Captured the user flag from */home/makis*.
- Captured the root flag from */root*.

That's it from my side :) Happy hacking!

------------------------------------------------------------------------------------