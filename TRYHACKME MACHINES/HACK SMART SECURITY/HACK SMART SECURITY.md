Link to machine : https://tryhackme.com/room/hacksmartersecurity

# SCANNING

I performed an **nmap** aggressive scan to reveal open ports and the services running on them.

![](IMAGES/1.png)

![](IMAGES/2.png)

![](IMAGES/3.png)

# FOOTHOLD

The **nmap** scan revealed a web server running on port 80 and port 1311. So I accessed them using my browser. 

![](IMAGES/4.png)

I found a dell emc login panel.

![](IMAGES/5.png)

I also got it's version.

![](IMAGES/6.png)

I searched for exploits for the management system and found a file read vulnerability.

![](IMAGES/7.png)

![](IMAGES/8.png)

I read about the vulnerability and found that I was allowed to read files from the system.

![](IMAGES/9.png)

I found a PoC on github that I downloaded.

![](IMAGES/10.png)

I then ran the exploit and tried reading the default configuration file on IIS servers.

![](IMAGES/11.png)

![](IMAGES/12.png)

Since that was successful, I read the **web.config**. This is present in the **inetpub/wwwroot** folder.

![](IMAGES/13.png)

Here, I found the password so I used it to log in using **ssh**.

![](IMAGES/14.png)

Finally, I captured the user flag from Desktop.

![](IMAGES/15.png)

![](IMAGES/16.png)

# PRIVILEGE ESCALATION

I downloaded and tried running **winPEAS**, **PowerUp** but both of them got blocked by firewall.

![](IMAGES/17.png)

![](IMAGES/18.png)

![](IMAGES/19.png)

I then downloaded **PrivescCheck** and ran it to get attack paths.

![](IMAGES/20.png)

![](IMAGES/21.png)

![](IMAGES/22.png)

Here I found a binary with **Unquoted Service Path**. I queried the service using **sc.exe** to validate the findings.

![](IMAGES/23.png)

I then looked for my permissions on the path and found I had full control over the service folder.

![](IMAGES/24.png)

![](IMAGES/25.png)

At first, I created and tried exploiting an **msfvenom** payload but it got blocked by firewall. So, I created a simple binary using **c** to add my current user to the local administrators group.

![](IMAGES/26.png)

![](IMAGES/27.png)

I downloaded this payload on the target. I then stopped the service, replaced the original service binary with my payload, started the service and then reloaded the system.

![](IMAGES/28.png)

![](IMAGES/29.png)

![](IMAGES/30.png)

![](IMAGES/31.png)

![](IMAGES/32.png)

![](IMAGES/33.png)

I then exited the **ssh** session and started a new one. I was successfully added to the local administrators group.

![](IMAGES/34.png)

I then captured the root flag from *Administrator*'s Desktop.

![](IMAGES/35.png)

![](IMAGES/36.png)

![](IMAGES/37.png)

![](IMAGES/38.png)

That's it from my side!
Until next time :)

---
