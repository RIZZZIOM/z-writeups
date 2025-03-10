Link to machine : https://tryhackme.com/room/blueprint

# SCANNING

I performed an **nmap** aggressive scan on the target to find open ports and services running on them.

![](IMAGES/1.png)

![](IMAGES/2.png)

![](IMAGES/3.png)

# FOOTHOLD

I accessed the web service running on the target and found a directory listing of a content management system.

![](IMAGES/4.png)

![](IMAGES/5.png)

![](IMAGES/6.png)

I looked for exploits related to the cms and found a couple on exploit db.

![](IMAGES/7.png)

![](IMAGES/8.png)

I then downloaded the exploits and configured parameters in them.

![](IMAGES/9.png)

![](IMAGES/10.png)

![](IMAGES/11.png)

![](IMAGES/12.png)

![](IMAGES/13.png)

I then ran the exploit and got shell as **NT Authority**.

![](IMAGES/14.png)

I then captured the root flag from *Administrator*'s Desktop.

![](IMAGES/15.png) 

![](IMAGES/16.png)

Now that I had full control on the target, I downloaded the hashes from registry hives and extracted them on my system using **impacket-secretsdump**

https://security.stackexchange.com/questions/38518/how-to-get-an-nt-hash-from-registry

![](IMAGES/17.png)

![](IMAGES/18.png)

![](IMAGES/19.png)

![](IMAGES/20.png)

Finally I cracked the hash using **crackstation**

![](IMAGES/21.png)

---
