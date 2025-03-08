
LINK TO MACHINE: https://app.hackthebox.com/machines/Cap

![](IMAGES/x.png)

# SCANNING

I performed an **nmap** aggressive scan to find open ports and the services running on them.

![](IMAGES/1.png)

# FOOTHOLD

The **nmap** scan identified an **http** server running so I accessed it using my browser and got a dashboard.

![](IMAGES/2.png)

I explored the application and in the meantime, performed a scan to find directories using **ffuf**.

![](IMAGES/3.png)

The *data* directory seemed interesting. It contained various capture files.

![](IMAGES/4.png)

![](IMAGES/5.png)

![](IMAGES/6.png)

I downloaded the packet capture files.

![](IMAGES/7.png)

![](IMAGES/8.png)

![](IMAGES/9.png)

![](IMAGES/10.png)

The file *0.pcap* contained the **ftp** credentials.

![](IMAGES/11.png)

![](IMAGES/12.png)

I used those credentials to log into the **ftp** server and found the user flag there.

![](IMAGES/13.png)

I downloaded the user flag on my system and read the contents.

![](IMAGES/14.png)

![](IMAGES/15.png)

I checked if the existing credentials could also be used for **ssh** and found that the credentials had been reused.

![](IMAGES/16.png)

I log into the server using **ssh**.

![](IMAGES/17.png)

![](IMAGES/18.png)

# PRIVILEGE ESCALATION

I viewed binaries with **capabilities** and found **python**.

![](IMAGES/19.png)

I searched **GTFObins** and found a way to exploit this for privilege escalation.

![](IMAGES/20.png)

I followed the commands and got **root** access. I then captured the root flag from */root*

![](IMAGES/21.png)

That's it from my side! Until next time :)

---
