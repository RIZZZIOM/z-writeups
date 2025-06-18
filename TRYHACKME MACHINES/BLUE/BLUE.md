To access the machine, click on the link given below:
- https://tryhackme.com/room/blue

# SCANNING

I performed an **nmap** scan to find open ports and the services running on them.

![](IMAGES/1.png)

I ran a vulnerable script scan for **SMB** and found that the target was vulnerable to **MS17-010**.

![](IMAGES/2.png)

# FOOTHOLD

I started metasploit and selected the exploit for this vulnerability.

![](IMAGES/3.png)

I then set the required configurations like listener IP, target IP, Payload etc and ran the exploit.

![](IMAGES/4.png)

![](IMAGES/5.png)

Finally, I got a shell.

![](IMAGES/6.png)

I backgrounded the shell using `CTRL + Z` and ran the `shell_to_meterpreter` post module to upgrade my shell to **meterpreter**.

![](IMAGES/7.png)

![](IMAGES/8.png)

Finally, I spawned a **meterpreter** session and got **NT AUTHORITY/SYSTEM** access on the target.

![](IMAGES/9.png)

I listed the running processes. Migrating to a legitimate process would make our exploit more stealthy so I migrated to **wininit.exe**.

![](IMAGES/10.png)

![](IMAGES/11.png)

Finally, I used **`hashdump`** to dump NTLM hashes from the target.

![](IMAGES/12.png)

I then cracked the hash for Jon using **Crackstation**.

![](IMAGES/13.png)

I then searched for all the flags that we had to capture and accessed them.

![](IMAGES/14.png)

![](IMAGES/15.png)

---
