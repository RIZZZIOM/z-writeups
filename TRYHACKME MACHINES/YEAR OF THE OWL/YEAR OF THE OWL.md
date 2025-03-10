
Link to machine : https://tryhackme.com/room/yearoftheowl

# SCANNING

I performed an **nmap** aggressive scan on the target to find a bunch of ports open.

![](IMAGES/1.png)

# FOOTHOLD

I enumerated the services running on the target but was unable to find anything interesting.

![](IMAGES/2.png)

I then tried performing a **udp** scan and found **snmp** to be open.

![](IMAGES/3.png)

I enumerated **snmp** using **snm-check** and found a username.

![](IMAGES/4.png)

I bruteforced the **smb** password of this user from *rockyou.txt* using **crackmapexec**.

![](IMAGES/5.png)

![](IMAGES/6.png)

I then enumerated the shares using this credential, but found nothing.

![](IMAGES/7.png)

I then checked if the credentials were valid for **winrm** and **rdp**.

![](IMAGES/8.png)

![](IMAGES/9.png)

I then used **winrm** to get shell access on the target.

![](IMAGES/10.png)

Finally I captured the user flag from *Jareth*'s *Desktop*.

![](IMAGES/11.png)

# PRIVILEGE ESCALATION

I downloaded and ran **winPEAS** to find misconfigurations.

![](IMAGES/12.png)

![](IMAGES/13.png)

![](IMAGES/14.png)

![](IMAGES/15.png)

I found a backup of the **sam** and **system**.

![](IMAGES/16.png)

![](IMAGES/17.png)

Hence, I copied these backups to the desktop from the recycle bin and downloaded them on my system.

![](IMAGES/18.png)

I then cracked the using **impacket-secretsdump** and found the Administrator hash.

![](IMAGES/19.png)

I then used the Administrator's LM hash to get shell access on the target using **winrm** and captured the root flag from *Desktop*.

![](IMAGES/20.png)

That's it from my side! Until next time.

---
