To access the machine, click on the link given below:
https://tryhackme.com/room/vulnnetactive

# SCANNING

I performed an **nmap** aggressive scan on the target to find open ports and the services running on them.

![](IMAGES/1.png)

# FOOTHOLD

I found **redis** to be running so I searched online for ways I could interact with the service and found this article: https://hackviser.com/tactics/pentesting/services/redis

![](IMAGES/2.png)

![](IMAGES/3.png)

I looked for ways I could exploit this and found this article. It seems I could relay my ntlm credentials through smb.

![](IMAGES/4.png)

I started responder on my interface

![](IMAGES/5.png)

I then tried accessing a share on my system and found the NLTM hash on responder.

![](IMAGES/6.png)

![](IMAGES/7.png)

I saved the hash and cracked it using **john**.

![](IMAGES/8.png)

I then listed the shares using the newly discovered credentials.

![](IMAGES/9.png)

I connected to the server using my credentials and accessed `Enterprise-Share`

![](IMAGES/10.png)

I then downloaded the ps1 script and it seemed like it deleted the contents of the Documents directory.

![](IMAGES/11.png)

I replaced the contents with a reverse shell and uploaded it back into the system.

![](IMAGES/12.png)

![](IMAGES/13.png)

![](IMAGES/14.png)

I soon got a reverse shell.

![](IMAGES/15.png)

I navigated to Desktop and found the user flag.

![](IMAGES/16.png)

# PRIVILEGE ESCALATION

I then uploaded **PowerUp** for local enumeration.

![](IMAGES/17.png)

![](IMAGES/18.png)

I created a temp directory and shifted the script to it.

![](IMAGES/19.png)

Finally, I executed the script and found interesting configs. I seemed to have the **SeImpersonatePrivilege**.

![](IMAGES/20.png)

This could be used to launch a potato attack and gain administrative access. So I tried exploiting it using **EfsPotato**.

![](IMAGES/21.png)

![](IMAGES/22.png)

![](IMAGES/23.png)

I tried executing the payload but it kept failing.

![](IMAGES/24.png)

So I created an msfvenom payload to get a meterpreter shell.

![](IMAGES/25.png)

I uploaded the payload through smb and executed it to get a reverse meterpreter shell on metasploit.

![](IMAGES/26.png)

![](IMAGES/27.png)

![](IMAGES/28.png)

After getting a shell, I used **`getsystem`** command to automatically escalate privilege and gain NT AUTHORITY\SYSTEM access.

Finally, I captured the root flag from Administrator's desktop.

![](IMAGES/29.png)

---
