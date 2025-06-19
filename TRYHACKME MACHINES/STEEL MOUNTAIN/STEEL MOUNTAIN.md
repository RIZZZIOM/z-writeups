To access the machine, click on the link given below:
- https://tryhackme.com/room/steelmountain

# SCANNING

I performed an **nmap** aggressive scan on the target to get a comprehensive list of details including open ports, running services, configurations etc.

![](IMAGES/1.png)

![](IMAGES/2.png)

# FOOTHOLD

I visited the web applications that were hosted on the target and found an **HTTP File Server** running on port 8080.

![](IMAGES/3.png)

![](IMAGES/4.png)

I then searched for exploits related to this specific version of the File server and found some.

![](IMAGES/5.png)

Since there was a **Metasploit** version of the exploit, I started the **Metasploit** framework and selected the exploit.

![](IMAGES/6.png)

I then configured the appropriate parameters.

![](IMAGES/7.png)

![](IMAGES/8.png)

Finally, I ran the exploit and got a reverse shell.

![](IMAGES/9.png)

I initially gained a 32-bit shell, so I migrated to a 64 bit process to get a 64 bit shell.

![](IMAGES/10.png)

I then captured the user flag from *bill*'s Desktop.

![](IMAGES/11.png)

![](IMAGES/12.png)

# PRIVILEGE ESCALATION

After capturing the user flag, I downloaded **PowerUp.ps1** on my local system and transferred it to the target for local enumeration.

![](IMAGES/13.png)

![](IMAGES/14.png)

Executing **`Invoke-AllChecks`** revealed an **Unquoted Service Path** misconfiguration on a service running as Local System.

![](IMAGES/15.png)

To exploit the misconfiguration, I first created an **exe** file with the same name as the service directory with a space in its name.

![](IMAGES/16.png)

I then uploaded this application to the parent directory of the directory with the space in its name.

![](IMAGES/17.png)

Finally, I restarted the service using **sc**.

![](IMAGES/18.png)

I started a reverse shell listener on another instance of **metasploit** and got a reverse shell.

![](IMAGES/19.png)

![](IMAGES/20.png)

![](IMAGES/21.png)

Since I had **nt authority** privileges, I captured the root flag from *Administrator*'s Desktop.

![](IMAGES/22.png)

![](IMAGES/23.png)

---
