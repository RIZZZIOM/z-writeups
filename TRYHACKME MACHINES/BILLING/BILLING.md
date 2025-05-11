To access the machine, click on the link given below:
- https://tryhackme.com/room/billing

# SCANNING

I performed an **nmap** aggressive scan on the target to find open ports, os information, service versions and to run a default nse script scan.

![](IMAGES/1.png)

# INITIAL ACCESS

The target had a web server running so I accessed the web page through my browser.

![](IMAGES/2.png)

The *`robots.txt`* file also did not contain any new endpoint.

![](IMAGES/3.png)

I also tried accessing the asterisk call manager port.

![](IMAGES/4.png)

Since I did not get any leads, I started the **metasploit** framework and looked for exploits related to *asterisk* or *mbilling*.

![](IMAGES/5.png)

Since there was a well ranked exploit for the *mbilling* service, I decided to give it a try.

![](IMAGES/6.png)

![](IMAGES/7.png)

I configured the required options and ran the exploit. Luckily, I got a reverse meterpreter shell.

![](IMAGES/8.png)

I spawned a **pty bash** shell and enumerated information about the current user and os kernel.

![](IMAGES/9.png)

I then looked for the flag and found it in *magnus* user's home directory.

![](IMAGES/10.png)

# PRIVILEGE ESCALATION

I looked for **sudo** permissions and found out that I could run a particular command as sudo without a password.

![](IMAGES/11.png)

I looked like banning and unbanning IPs.

![](IMAGES/12.png)

This was the first time I came across such an attack vector. So I searched online and found this guide that showed how it could be used for privilege escalation:
- https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/sudo/sudo-fail2ban-client-privilege-escalation/

![](IMAGES/13.png)

I copied the steps shown and managed to add an **suid** bit on **`/bin/bash`**.

![](IMAGES/14.png)

Finally, I executed `/bin/bash` in privileged mode and found the root flag inside the **`/root`** directory.

![](IMAGES/15.png)

---
