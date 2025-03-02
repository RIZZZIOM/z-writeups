Welcome to my writeup where I am gonna be pwning **Thompson** machine from **TryHackMe**. This challenge has two flags, and our goal is to capture both. Let’s get started!

# GETTING STARTED

To access the challenge, click on the link given below:
https://tryhackme.com/r/room/bsidesgtthompson

> [!NOTE] 
> This writeup documents the steps that successfully led to pwnage of the machine. It does not include the dead-end steps encountered during the process (which were numerous). This is just my take on pwning the machine and you are welcome to choose a different path.

# RECONNAISSANCE

I performed an **nmap** aggressive scan to find open ports and the services running on them.

![](IMAGES/1.png)

# FOOTHOLD

I accessed the web application running on port 8080 through my web browser.

![](IMAGES/2.png)

The ajp page was inaccessible.

![](IMAGES/3.png)

I searched online and found an article on AJP in **hacktricks**.

![](IMAGES/4.png)

I bruteforced directories on the web application using **ffuf** to increase my attack surface.

![](IMAGES/5.png)

I then accessed the newly discovered endpoints and found a login panel.

![](IMAGES/6.png)

![](IMAGES/7.png)

![](IMAGES/8.png)

I also bruteforced files on the web app to find anything interesting.

![](IMAGES/9.png)

I found an xml file so I accessed it but found nothing interesting.

![](IMAGES/10.png)

I then tried logging in with default credentials *admin:admin*.

![](IMAGES/11.png)

The authentication failed, however, I got a valid username and password for the login panel.

![](IMAGES/12.png)

Hence I logged in using those credentials.

![](IMAGES/13.png)

I got access to the manager panel. This seemed to control the pages present on the web app.

![](IMAGES/14.png)

I was also allowed to upload my own code for a page.

![](IMAGES/15.png)

![](IMAGES/16.png)

![](IMAGES/17.png)

I searched online and found a way to get an RCE from this manager on **hacktricks**.

![](IMAGES/18.png)

![](IMAGES/19.png)

I generated an **msfvenom** payload and uploaded it on the web app.

![](IMAGES/20.png)

![](IMAGES/21.png)

![](IMAGES/22.png)

Finally, I started a **netcat** listener and executed the payload to receive a reverse shell.

![](IMAGES/23.png)

After getting shell access, I captured the user flag from *jack*'s home directory.

![](IMAGES/24.png)

# PRIVILEGE ESCALATION

After getting the user flag, I looked at other files present in the home directory and found a bash script and a txt file. The bash script seemed to execute the **id** command and save the output in the txt file.

![](IMAGES/25.png)

I viewed the cronjobs and found the bash script was being executed as root user.

![](IMAGES/26.png)

Hence I visited **revshells** to get a reverse shell payload and added the payload at the end of the bash script.

![](IMAGES/27.png)

![](IMAGES/28.png)

I started a **netcat** listener and after a while, received a connection as root user.

![](IMAGES/29.png)

Finally, I captured the root flag from the */root* directory.

![](IMAGES/30.png)

Happy Hacking :)

---
