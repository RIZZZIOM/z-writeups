To access the machine, click on the link given below:
- https://tryhackme.com/room/internal

# RECONNAISSANCE
I performed an **nmap** aggressive scan on the target to find open ports and the services running on them.

![](IMAGES/1.png)

# FOOTHOLD
I mapped the domain *internal.thm* with the IP in my *host* file and accessed the web application through my browser.

![](IMAGES/2.png)

I then used **ffuf** to fuzz for hidden directories and found a wordpress installation.

![](IMAGES/3.png)

The `/blog` endpoint also pointed towards **wordpress**

![](IMAGES/4.png)

Since, it was **wordpress**, I tried accessing the *wp-login* endpoint. I tried common username passwords but couldn't log in.

![](IMAGES/5.png)

The `/wordpress` endpoint pointed to a page that did not exist.

![](IMAGES/6.png)

I then went back to the `/blog` endpoint and viewed a blog that was posted. The author could be a valid user.

![](IMAGES/7.png)

![](IMAGES/8.png)

I then fuzzed for hidden files using **ffuf** but found nothing interesting.

![](IMAGES/9.png)

I switched back to the *wp-login* endpoint and verified if *admin* was a valid username.

![](IMAGES/10.png)

With no other leads, I tried bruteforcing the *admin* password using **hydra** and found it.

![](IMAGES/11.png)

I then logged in to access the **Wordpress** dashboard.

![](IMAGES/12.png)

![](IMAGES/13.png)

With access to the **wordpress** dashboard, I could get a reverse shell. I referred to **hacktricks** and got a reverse shell by changing the *404.php* template with a **pentestmonkey**'s php reverse shell and calling the endpoint to trigger the payload.

![](IMAGES/14.png)

![](IMAGES/15.png)

![](IMAGES/16.png)

![](IMAGES/17.png)

# PRIVILEGE ESCALATION : 1

After receiving the shell, I viewed the */home* directory and found a user called *aubreanna*. However, I did not have the privilege to view the contents inside it.

![](IMAGES/18.png)

I then viewed the wordpress installation directory.

![](IMAGES/19.png)

The config file often contains sensitive information, so I viewed the *wp-config* file and found the **mysql** credentials.

![](IMAGES/20.png)

![](IMAGES/21.png)

I then logged into the server and looked at the contents present inside the *wordpress* database.

![](IMAGES/22.png)

![](IMAGES/23.png)

However, I found nothing except the *admin* user's hash.

![](IMAGES/24.png)

I then looked for uncommon binaries with **SUID** bit set

![](IMAGES/25.png)

I found **pkexec** and decided to give the **PwnKit** exploit a try. If it worked, I could directly get *root* access.

![](IMAGES/26.png)

I also explored other folders and found the credentials for *aubreanna* in a text file inside the */opt* directory.

![](IMAGES/27.png)

I then downloaded the **PwnKit** exploit and gave it executable permissions.

![](IMAGES/28.png)

Upon executing the exploit, I got root shell.

![](IMAGES/29.png)

With root access, I could read the contents of both, user flag and root flag.

![](IMAGES/30.png)

However, this privesc vector was unintended.

# PRIVILEGE ESCALATION : 2

I logged in to the system as *aubreanna* using **ssh**.

![](IMAGES/31.png)

![](IMAGES/32.png)

I found a note inside my home directory that said there was a jenkins service running internally on some IP.

![](IMAGES/33.png)

I viewed my IPs and realized that the **jenkins** server was likely running on an internal machine and not locally.

![](IMAGES/34.png)

So, I performed a local port forward to be able to access **jenkins** hosted on the internal network from my local port 8888 through the compromised machine.

![](IMAGES/35.png)

I then accessed **jenkins** through my browser.

![](IMAGES/36.png)

I fuzzed for hidden directories and found a bunch of interesting endpoints.

![](IMAGES/37.png)

However, none of them contained anything special. They just threw a *404 not found* error.

![](IMAGES/38.png)

With no other leads, I looked for default credentials and found a username.

![](IMAGES/39.png)

I tried the username with common passwords but failed to log in.

![](IMAGES/40.png)

I then brute forced the password using **hydra** from the **rockyou** wordlist.

![](IMAGES/41.png)

After logging in, I was lost. So I referred to **hackingarticles** and found a way to execute **groovy** scripts.

![](IMAGES/42.png)

![](IMAGES/43.png)

I visited **revshells** and configured a **groovy** script that I could run on the **jenkins** server and receive a reverse shell on my **netcat** listener.

![](IMAGES/44.png)

After executing the script, I received a reverse shell.

![](IMAGES/45.png)

I then explored the system and found root user's password inside a text file in the */opt* directory.

![](IMAGES/46.png)

With the *root* password, I could simply switch my user for a privileged access.

![](IMAGES/47.png)

That's it from my side!
Until next time :)

---
