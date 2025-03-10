
Link to machine : https://tryhackme.com/room/cyberlensp6

# SCANNING

Upon receiving the target IP, I added it in my */etc/hosts* file for name resolution.

![](IMAGES/1.png)

I then performed an **nmap** aggressive scan to find open ports and the services running on them. 

![](IMAGES/2.png)

![](IMAGES/3.png)

# FOOTHOLD

I **nmap** scan revealed an **http** server running on port 80 and 61777 so I accessed them from my browser.

![](IMAGES/4.png)

![](IMAGES/5.png)

The server running on port 61777 revealed the apache version being used. A simple google search revealed an **RCE** vulnerability.

![](IMAGES/6.png)

I looked for exploits using **searchsploit** and found one on **metasploit**.

![](IMAGES/7.png)

I booted the **metasploit** framework and selected the exploit.

![](IMAGES/8.png)

![](IMAGES/9.png)

I configured the options and ran the exploit to get code execution.

![](IMAGES/10.png)

![](IMAGES/11.png)

![](IMAGES/12.png)

![](IMAGES/13.png)

I then captured the user flag from the Desktop.

![](IMAGES/14.png)

# PRIVILEGE ESCALATION

I then ran privilege escalation checks using **PowerUp** and **winPEAS**.

![](IMAGES/15.png)

![](IMAGES/16.png)

![](IMAGES/17.png)

![](IMAGES/18.png)

![](IMAGES/19.png)

![](IMAGES/20.png)

![](IMAGES/21.png)

![](IMAGES/22.png)

Since both of them revealed nothing of interest, I ran the local exploit suggester module in metasploit.

![](IMAGES/23.png)

![](IMAGES/24.png)

![](IMAGES/25.png)

I after getting a bunch of recommendations, I tried each one of them one after the other. Starting with the first exploit, I configured the required options.

Running it got me root access on the target.

![](IMAGES/26.png)

Finally I captured the root flag from the *Administrator's* Desktop.

![](IMAGES/27.png)

That's it from my side! Until next time :)

---
