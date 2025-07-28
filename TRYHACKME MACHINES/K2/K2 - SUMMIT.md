
# SCANNING

I performed an **nmap** aggressive scan on the target to find open ports and services running on them.

![](IMAGES/SUMMIT/1.png)

# FOOTHOLD

I used the usernames found on middle camp to enumerate valid users on this system.

![](IMAGES/SUMMIT/2.png)

I then tried the passwords and hash that I had found on **middle camp** and **base camp** against *j.smith* user and found that the **middle camp** administrator hash was valid.

![](IMAGES/SUMMIT/3.png)

I then accessed the machine using **evil-winrm**.

![](IMAGES/SUMMIT/4.png)

I found another user on the system called *o.armstrong*.

![](IMAGES/SUMMIT/5.png)

The `C:\` directory contained an interesting directory called *Scripts*.

![](IMAGES/SUMMIT/6.png)

It contained a script that copied the contents from *o.armstrong*'s desktop to documents.

![](IMAGES/SUMMIT/7.png)

This was likely a scheduled task. If I could modify this, I could execute commands as *o.armstrong*. So, I looked at my permissions and found that I had privileges on the *Scripts* folder. I could replace the script with a custom one which would allow me to execute commands of my choice.

![](IMAGES/SUMMIT/8.png)

So, I renamed the original script and tried adding a reverse shell payload in a new *backup.bat* script. However, I was blocked by antivirus.

![](IMAGES/SUMMIT/9.png)

Instead of getting a reverse shell, I tried capturing the NTLM hash by making the user try and access a share. While accessing the share, the user would authenticate with their NTLM hash and hence I could capture it.

![](IMAGES/SUMMIT/10.png)

I started responder and after some time, received *o.armstrong*'s NTLM hash.

![](IMAGES/SUMMIT/11.png)
![](IMAGES/SUMMIT/12.png)

I saved the hash in a file and cracked it using **john**.

![](IMAGES/SUMMIT/13.png)

![](IMAGES/SUMMIT/14.png)

Finally, I accessed the system as *o.armstrong*.

![](IMAGES/SUMMIT/15.png)

I then captured the user flag from *Desktop*.

![](IMAGES/SUMMIT/16.png)

# PRIVILEGE ESCALATION

I used *o.armstrong*'s credentials to enumerate the system with **bloodhound**.

![](IMAGES/SUMMIT/17.png)

I discovered that our user *o.armstrong* had **GenericWrite** permission over the *K2ROOTDC.K2.THM* account.

![](IMAGES/SUMMIT/18.png)
![](IMAGES/SUMMIT/19.png)

Hence, I could exploit this using **resource based constraint delegation**.

![](IMAGES/SUMMIT/20.png)

Hence, I first created a computer account. And then gave it permission to for delegation. This account could act on behalf of other accounts (even domain admins).

![](IMAGES/SUMMIT/21.png)

I then requested a service ticket (TGS) to access `cifs` of *k2rootdc.k2.thm* on behalf of administrator and exported the ticket to the **KRB5CCNAME** variable.

![](IMAGES/SUMMIT/22.png)

Finally, I performed DC-SYNC / dumped secrets using the kerberos ticket and found the administrator hash. 

![](IMAGES/SUMMIT/23.png)
![](IMAGES/SUMMIT/24.png)

I then accessed the target as *administrator* and captured the root flag.

![](IMAGES/SUMMIT/25.png)

That's it from my side!
Until next time :)

---
