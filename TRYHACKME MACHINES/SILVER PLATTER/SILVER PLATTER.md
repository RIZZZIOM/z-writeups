Link to machine: https://tryhackme.com/room/silverplatter

# SCANNING

I performed an **nmap** aggressive scan to find open ports and services running on them.

![](IMAGES/1.png)

# FOOTHOLD

The **nmap** scan discovered a web server running on the target. So I accessed it using my browser.

![](IMAGES/2.png)

The contact page revealed a username that could be used later.

![](IMAGES/3.png)

Reading the source code revealed another directory.

![](IMAGES/4.png)

![](IMAGES/5.png)

However, even that endpoint revealed nothing interesting. Hence, I then moved onto the other port where an **http** proxy was running. I looked for directories using **ffuf** and found 2 new endpoints.

![](IMAGES/6.png)

One endpoint wasn't accessible, however the other one redirected us to a login panel.

![](IMAGES/7.png)

![](IMAGES/8.png)

I used the username from the contact page and tried logging in using default credentials but failed.

![](IMAGES/9.png)

I also tried *Get a new password* but found no interesting functionality.

![](IMAGES/10.png)

Hence, I then looked for **cve**'s associated with the name of the cms 'silverpeas' and found an **authentication bypass** vulnerability.

![](IMAGES/11.png)

![](IMAGES/12.png)

I could bypass authentication by simply removing the password field. So I captured the login request using **burp suite** and removed the password field to log into the web app.

![](IMAGES/13.png)

![](IMAGES/14.png)

I had a notification of a message sent to me by my manager.

![](IMAGES/15.png)

I explored the application.

![](IMAGES/16.png)

![](IMAGES/17.png)

I recalled reading about a **broken access control** vulnerability on this cms so decided to try it out.

![](IMAGES/18.png)

![](IMAGES/19.png)

![](IMAGES/20.png)

I managed to get the ssh credentials by exploiting **IDOR** vulnerability. I then used it to connect to the target using **ssh**.

![](IMAGES/21.png)

Finally, I captured the user flag.

![](IMAGES/22.png)

# PRIVILEGE ESCALATION

Running the **id** command revealed that the user *tim* was part of the **adm** group. This group is used for monitoring purpose.

![](IMAGES/23.png)

![](IMAGES/24.png)

Hence my user would have access to the system **logs**. I looked at the authentication logs in */var/log/* directory and extracted passwords from it.

![](IMAGES/25.png)

![](IMAGES/26.png)

I checked if the password that I found was a valid credential for **ssh** using **hydra**.

![](IMAGES/27.png)

I then logged in as the new user.

![](IMAGES/28.png)

I then checked the **sudo** privileges of this user and found that I was allowed to run anything as **root** and had no restrictions.

![](IMAGES/29.png)

Hence I simply spawned a **bash** shell as root using **sudo** and captured the root flag from */root* directory.

![](IMAGES/30.png)

That's it from my side!
Happy hacking :)

---
