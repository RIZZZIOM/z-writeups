# GETTING STARTED

To download Kioptrix L2, click [here](https://www.vulnhub.com/entry/kioptrix-level-11-2,23/)

**DISCLAIMER**
> This writeup documents the steps that successfully led to pwnage of the machine. It does not include the dead-end steps encountered during the process (which were numerous). I recommend attempting to solve the lab independently. If you find yourself stuck on a phase for more than a day, you may refer to the writeups for guidance. Please note that this is just one approach to capturing all the flags, and there are alternative methods to solve the machine.

------------------------------------------------------------------------------------
# RECONNAISSANCE

I kicked things off with a quick network scan using **nmap** to uncover the target's IP address.

```bash

┌──(root㉿kali)-[~/ctf/kioptrix-2]
└─# nmap -sn 192.168.1.0/24                               
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-04 10:46 EDT
Nmap scan report for RTK_GW (192.168.1.1)
Host is up (0.0039s latency).
MAC Address: F8:C4:F3:D0:63:13 (Shanghai Infinity Wireless Technologies)

Nmap scan report for 192.168.1.13
Host is up (0.00019s latency).
MAC Address: 00:0C:29:57:1F:50 (VMware)

Nmap scan report for kali (192.168.1.12)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 3.09 seconds
```

Once I pinpointed the target IP as _192.168.1.13_, I launched an aggressive **nmap** scan to uncover open ports, identify running services, and execute default scripts.

![[IMAGES/1.png]]
![[IMAGES/2.png]]
![[IMAGES/3.png]]
The _unauthorized_ label next to the SQL server indicates that remote login is disabled. This means the server can only be accessed locally.

------------------------------------------------------------------------------------
# INITIAL ACCESS
Noticing that port 80 was open, I navigated to it through my browser and found myself on a login page.

![[IMAGES/4.png]]

After trying a few default credentials, I tried performing an SQL injection using some simple payloads. When I entered the following, I bypassed the login panel:
- username - `admin' or 1=1#`
- password - password

![[IMAGES/5.png]]

![[IMAGES/6.png]]

This allowed me to ping a machine and showed the results in another page *pingit.php*

![[IMAGES/7.png]]

I decided to test the system by entering a command along with an IP address. Surprisingly, it worked!

The command I used was: `8.8.8.8; ls`

![[IMAGES/8.png]]

I checked for the presence of **nc** on the target by executing `which nc`, but it turned out netcat wasn't available. So, I quickly navigated to [revshells](https://www.revshells.com/), selected a bash payload, and set the listener IP and port.

![[IMAGES/9.png]]

Next, I started a netcat listener on my PC using **nc**.

```bash

┌──(root㉿kali)-[~/ctf/kioptrix-2]
└─# rlwrap nc -lnvp 443
listening on [any] 443 ...
```

Finally I execute the payload and got reverse shell

![[IMAGES/10.png]]

At this point, I only had daemon access, functioning as a service user. To fully pwn the machine, I needed to escalate my privileges to root.

While exploring, I discovered two users, _john_ and _harold_. However, I couldn't access their directories inside the _home_ page.

![[IMAGES/11.png]]

------------------------------------------------------------------------------------
# PRIVILEGE ESCALATION
I continued searching but found nothing special. So, I navigated to the _/tmp_ directory and ran the **Linux Smart Enumeration** script from my system.


![[IMAGES/12.png]]

![[IMAGES/13.png]]

I found the following SUID binaries, but unfortunately, they didn't seem exploitable.

![[IMAGES/14.png]]

I double-checked my kernel version using `uname -a`.

![[IMAGES/15.png]]

The **lse** script also revealed that the target was running **CentOS**, so I searched for exploits related to this using **searchsploit**.

![[IMAGES/16.png]]

I found one, so I downloaded and transferred it to my target.

![[IMAGES/17.png]]

![[IMAGES/18.png]]

Finally, I compiled and ran the exploit.

![[IMAGES/19.png]]

------------------------------------------------------------------------------------
# CLOSURE

And just like that, I owned the system!

Here's how it went down:
- First up, I spotted port 80 alive and kicking on the target.
- A quick visit revealed a tempting login screen.
- With some SQL injection finesse, I waltzed past the authentication and landed on a ping-performing page.
- Spotting a command injection flaw, I seized the chance for a slick reverse shell.
- To cap it off, I dove into the kernel's weaknesses, snagging that sweet root access.

![[IMAGES/20.png]]

That's it from my side :) Happy Hacking!