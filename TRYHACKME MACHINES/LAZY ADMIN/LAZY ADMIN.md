
Link to machine : https://tryhackme.com/room/lazyadmin

# SCANNING

I performed an **nmap** aggressive scan to find open ports and the services running on the target.

![](IMAGES/1.png)

# FOOTHOLD

The **nmap** scan revealed an http server, so I accessed the web page using my browser.

![](IMAGES/2.png)

I then looked for hidden directories using **ffuf**.

![](IMAGES/3.png)

![](IMAGES/4.png)

Since the */contact* directory had some content, I looked for other directories inside it.

![](IMAGES/5.png)

I discovered a login panel and a couple of directory lists.

![](IMAGES/6.png)

This folder seemed to have the backend codes.

![](IMAGES/7.png)

It contained the service version.

![](IMAGES/8.png)

There was a folder called *mysql_backup* which could be of interest.

![](IMAGES/9.png)

The folder contained an **sql** file. I downloaded it on my system and extracted the contents to find a set of credentials.

![](IMAGES/10.png)

![](IMAGES/11.png)

I was able to crack the hash using **crackstation**.

![](IMAGES/12.png)

I then logged in using the credentials.

![](IMAGES/13.png)

The dashboard contained the version of the CMS that was being used. So I used for exploits available using **searchsploit**.

![](IMAGES/14.png)

![](IMAGES/15.png)

There seemed to be a file upload vulnerability, so I downloaded **pentest monkey's** php reverse shell code.

![](IMAGES/16.png)

The *media center* had a file upload functionality, so I tried uploading the **php** file. 

![](IMAGES/17.png)

![](IMAGES/18.png)

However, due to security reasons, I wasn't able to upload it on the target. I then tried using an alternate extension like **.phtml**.

![](IMAGES/19.png)

![](IMAGES/20.png)

I was able to bypass the security measure using an alternate extension. Next I started a **netcat** listener and executed the payload to get a reverse shell.

![](IMAGES/21.png)

I then captured the user flag from *itguy*'s home directory.

![](IMAGES/22.png)

![](IMAGES/23.png)

# PRIVILEGE ESCALATION

I also found a **mysql** login credential so I connected to the server using it.

![](IMAGES/24.png)

![](IMAGES/25.png)

However, I found nothing interesting. Next I looked for my **sudo** privileges and found that I was allowed to run a perl script.

![](IMAGES/26.png)

I viewed the contents of the perl script, it executed a bash script that I was allowed to modify.

![](IMAGES/27.png)

So, I modified the bash script and then ran the **perl** script as **sudo**.

![](IMAGES/28.png)

![](IMAGES/29.png)

After getting root access, I captured the root flag from my *home* directory.

![](IMAGES/30.png)

That's it from my side!
Happy hacking :)

---
