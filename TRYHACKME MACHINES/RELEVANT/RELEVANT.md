Link to machine : https://tryhackme.com/room/relevant

# RECONAISSANCE

I performed an **nmap** aggressive scan on the target to identify open ports and services running on the target while also performing script scan.

![](IMAGES/1.png)

![](IMAGES/2.png)

I also ran a vulnerability scan on **smb** using **nmap** and found it was vulnerable to **CVE-2017-0143**

![](IMAGES/3.png)
# FOOTHOLD

I accessed the two **http** servers revealed by **nmap** scan and found a default IIS landing page.

![](IMAGES/4.png)

![](IMAGES/5.png)

Since the **http** pages had nothing interesting, I enumerated **smb** and found an interesting share.

![](IMAGES/6.png)

I connected to the share and found a password file.

![](IMAGES/7.png)

This file contained base64 encoded credentials.

![](IMAGES/8.png)

I tried using them to get access using rdp but failed. I then looked into the vulnerability that I had found earlier while reconnaissance.

![](IMAGES/9.png)

The vulnerability lead to remote code execution. So to exploit it, I created an **aspx** payload using **msfvenom**.

![](IMAGES/10.png)

I then uploaded the payload on the **smb**server.

![](IMAGES/11.png)

From my past experience, I found that sometimes smb shares are hosted on the web server. I tried accessing the share on both the servers and succeeded on the one running on port **49663**. I then started a **netcat** listener and executed the payload through **http** to get a reverse shell.

![](IMAGES/12.png)

![](IMAGES/13.png)

With shell access, I was able to capture the user flag from *Bob*'s Desktop.

![](IMAGES/14.png)

![](IMAGES/15.png)

# PRIVILEGE ESCALATION

I then downloaded **winPEAS** on the target and ran it to find misconfigurations that could used for privilege escalation.

![](IMAGES/16.png)

![](IMAGES/17.png)

However, I found nothing interesting. Next I looked at my privileges and found I had **SeImpersonatePrivilege** enabled.

![](IMAGES/18.png)

Users with this privilege enabled could potentially escalate to admin using **dirty potato** or **spoolspoof** exploits. I referred to the below article and downloaded the **printspoofer** exploit.

![](IMAGES/19.png)

![](IMAGES/20.png)


![](IMAGES/21.png)

![](IMAGES/22.png)

I transferred the exploit from my local system to the target.

![](IMAGES/23.png)

Finally, I ran the exploit to spawn a **powershell** session as **nt authority**.

![](IMAGES/24.png)

![](IMAGES/25.png)

I then captured the root flag from *Administrator*'s Desktop.

![](IMAGES/26.png)

That's it from my side!
Happy hacking

---
