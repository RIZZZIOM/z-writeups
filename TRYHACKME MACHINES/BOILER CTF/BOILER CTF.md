Link to machine : https://tryhackme.com/room/boilerctf2

# RECONNAISSANCE

I performed an **nmap** aggressive scan to identify the open ports, services and run default script scan.

![](IMAGES/1.png)

![](IMAGES/2.png)

# FOOTHOLD

I accessed the **ftp** server and found a txt file. I downloaded it and viewed the contents inside.

![](IMAGES/3.png)

![](IMAGES/4.png)

It was rot13. This decodes to `Just wanted to see if you find it. Lol. Remember: Enumeration is the key!`. I then accessed the web application on port 80 and 10000.

![](IMAGES/5.png)

Port 10000 had a login panel.

![](IMAGES/6.png)

I then viewed *robots.txt* and found multiple endpoints.

![](IMAGES/7.png)

I also ran a directory bruteforce scan using **ffuf** to find more directories.

![](IMAGES/8.png)

**JOOMLA** seemed interesting so I accessed it.

![](IMAGES/9.png)

I bruteforced files present in the **joomla** directory.

![](IMAGES/10.png)

![](IMAGES/11.png)

I viewed the different files present in the directory but found nothing interesting at first.

![](IMAGES/12.png)

The `_files` endpoint had an encoded text. However, decoding it revealed nothing important.

![](IMAGES/13.png)

![](IMAGES/14.png)

I continued looking at the files present in the *joomla* directory and found the version of a web based tool in the source code of `_test`.

![](IMAGES/15.png)

![](IMAGES/16.png)

I looked for exploits and found an **RCE**.

![](IMAGES/17.png)

![](IMAGES/18.png)

![](IMAGES/19.png)

I used the exploit to execute os commands on the target and found the username and password of a user in the log file.

![](IMAGES/20.png)

![](IMAGES/21.png)

This exploit also allowed us to spawn an interactive shell as **www-data**.

![](IMAGES/22.png)

![](IMAGES/23.png)

I used the credentials found from the log file to get shell access on the target and spawned a pty shell using **python**.

![](IMAGES/24.png)

I analyzed the **backup.sh** script and found the credentials of another user.

![](IMAGES/25.png)

![](IMAGES/26.png)

I quickly switched user and was able to capture the first flag from *stoner*'s home directory.

![](IMAGES/27.png)

![](IMAGES/28.png)

# PRIVILEGE ESCALATION

I then looked for binaries with **suid** bit set and found the **find** command.

![](IMAGES/29.png)

Since this was uncommon, I visited **gtfobins** and found a way to exploit this in order to get root access.

![](IMAGES/30.png)

![](IMAGES/31.png)

After getting root access, I was able to capture the root flag from the */root* directory.

![](IMAGES/32.png)

That's it from my side :)
Happy hacking!

---
