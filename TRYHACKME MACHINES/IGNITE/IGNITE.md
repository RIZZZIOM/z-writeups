https://tryhackme.com/room/ignite

# SCANNING

I ran an **nmap** aggressive scan on the target to identify the open ports and services running.

![](IMAGES/1.png)

# FOOTHOLD

The server only had port 80 open. So I accessed it on my browser and found a CMS called Fuel.

![](IMAGES/2.png)

When I scrolled to the bottom, I found the admin endpoint and credentials to log in.

![](IMAGES/3.png)

I then visited the endpoint and logged in as admin

![](IMAGES/4.png)

![](IMAGES/5.png)

![](IMAGES/6.png)

I didn't find anything useful in the CMS so I looked for exploits related to the CMS version.

![](IMAGES/7.png)

I found and downloaded a python exploit that provided RCE.

![](IMAGES/8.png)

I used the RCE to get a reverse shell.

![](IMAGES/9.png)

![](IMAGES/10.png)

![](IMAGES/11.png)

After getting a reverse shell, I captured the user flag from the **www-data** user's home directory.

![](IMAGES/12.png)

# PRIVILEGE ESCALATION #1

I transferred LinPEAS on the target to look for privilege escalation vectors.

![](IMAGES/13.png)

![](IMAGES/14.png)

I then ran the script

![](IMAGES/15.png)

It found hardcoded credentials inside a backup file.

![](IMAGES/16.png)

I manually visited the file and found that the password belonged to the root user.

![](IMAGES/17.png)

![](IMAGES/18.png)

I switched to root user and captured the root flag from `/root`.

![](IMAGES/19.png)

# PRIVILEGE ESCALATION #2

I transferred linux exploit suggester on the target and ran it.

![](IMAGES/20.png)

The script identified a target to be vulnerable to CVE-2021-4034 so I downloaded the exploit related to it.

![](IMAGES/21.png)

![](IMAGES/22.png)

I compiled the exploit on the target and ran it to get a root shell

![](IMAGES/23.png)

![](IMAGES/24.png)

Finally, I captured the root flag from `/root`.

![](IMAGES/25.png)

That's it from my side!
Until next time :)

---
