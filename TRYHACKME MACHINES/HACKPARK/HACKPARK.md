To access the machine, click on the link given below:
- https://tryhackme.com/room/hackpark

# SCANNING

I performed an **nmap** scan to find open ports and the services running on the target.

![](IMAGES/1.png)

# FOOTHOLD

The nmap revealed 2 open ports. So I viewed the web application running on port 80 through my browser.

![](IMAGES/2.png)

The clown shown on the web page could be related to the target, so I did a google search and found his name.

![](IMAGES/3.png)

Inspecting the source code revealed the version of the blogging application that was running.

![](IMAGES/4.png)

**searchsploit** revealed an RCE and directory traversal vulnerability related to this particular version.

![](IMAGES/5.png)

Before moving forward with the exploit, I visited the *robots.txt* endpoint that was discovered by **nmap**. Here, I found some new endpoints.

![](IMAGES/6.png)

I accessed the endpoints but found nothing interesting.

![](IMAGES/7.png)

![](IMAGES/8.png)

I fuzzed for directories found some interesting endpoints.

![](IMAGES/9.png)

Accessing the *admin* endpoint redirected me to a login page.

![](IMAGES/10.png)

The home page contained a post with the author name. Clicking on it revealed a username called 'admin'.

![](IMAGES/11.png)

I then sent a request from the login panel and captured the request on **Burp Suite**.

![](IMAGES/12.png)

I then used **hydra** to bruteforce the password of the admin user from the *rockyou.txt* wordlist.

![](IMAGES/13.png)

After finding the password, I logged into the application.

![](IMAGES/14.png)

I was then able to access the admin panel.

![](IMAGES/15.png)

The About section also revealed the identity of the user running the application.

![](IMAGES/16.png)

I now download the RCE exploit.

![](IMAGES/17.png)

I read the description of the exploit on **Exploit-DB**.

![](IMAGES/18.png)

I also edited the exploit and added my local address to get a reverse shell.

![](IMAGES/19.png)

I then followed the instructions given in the description to get upload my payload.

![](IMAGES/20.png)

![](IMAGES/21.png)

![](IMAGES/22.png)

![](IMAGES/23.png)

![](IMAGES/24.png)

![](IMAGES/25.png)

After uploading the payload, I accessed the endpoint and got a reverse shell on my **netcat** listener.

![](IMAGES/26.png)

![](IMAGES/27.png)

# PRIVILEGE ESCALATION

The shell was unstable so I created a *temp* directory to hold payloads.

![](IMAGES/28.png)

I then created a payload for a **meterpreter** shell using **msfvenom** and hosted it locally on an **http** server.

![](IMAGES/29.png)

I downloaded this payload on the target and executed it to get a stable reverse shell on my **metasploit** listener.

![](IMAGES/30.png)

![](IMAGES/31.png)

![](IMAGES/32.png)

![](IMAGES/33.png)

After getting access, I backgrounded my session and used the **local_exploit_suggester** post module to get post exploitation modules.

![](IMAGES/34.png)

![](IMAGES/35.png)

![](IMAGES/36.png)

Since the post module detected UAC related vulnerability, I could directly exploit this from my **meterpreter** session. So I entered my session and used the **getsystem** command to escalate my privilege.

![](IMAGES/37.png)

After getting *NT AUTHORITY\SYSTEM* access, I captured the user flag from *jeff*'s Desktop and the root flag from *Administrator*'s Desktop.

![](IMAGES/38.png)

![](IMAGES/39.png)

After capturing the flag, I migrated my shell to a 64 bit service.

![](IMAGES/40.png)

I then uploaded **PowerUp**.

![](IMAGES/41.png)

From the unprivileged shell, when I ran the **PowerUp** module and **Invoke-AllChecks** command, I found the administrator password.

![](IMAGES/42.png)

![](IMAGES/43.png)

That's it from my side, until next time !

---
