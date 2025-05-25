To access the machine, click on the link given below:
- https://tryhackme.com/room/blaster

# RECONNAISSANCE

I performed an **nmap** aggressive scan on the target to find open ports and the services running on them.

![](IMAGES/1.png)

# FOOTHOLD

The nmap scan revealed a web application to be running on port 80, so I accessed it on my browser.

![](IMAGES/2.png)

It was a default IIS landing page so I fuzzed for hidden directories and found one called *retro*.

![](IMAGES/3.png)

I visited the directory and found a blogging application.

![](IMAGES/4.png)

The author of the blog could be a user in the system so I kept note of it.

![](IMAGES/5.png)

A comment on one of the blogs had a potential password.

![](IMAGES/6.png)

I checked if the username and password were valid and found that I could use them to access the target through **rdp**.

![](IMAGES/7.png)

I used **xfreerdp** to access the machine.

![](IMAGES/8.png)

![](IMAGES/9.png)

I found user.txt in the Desktop.

![](IMAGES/10.png)

# PRIVILEGE ESCALATION

The Desktop had another application called hhupd.

![](IMAGES/11.png)

I searched online for exploits and found articles for privilege escalation through UAC bypass.

![](IMAGES/12.png)

link to article: https://justinsaechao23.medium.com/cve-2019-1388-windows-certificate-dialog-elevation-of-privilege-4d247df5b4d7

![](IMAGES/13.png)

I followed the steps given in the article to escalate my privilege to administrator:
- Right click on the application and select run as Administrator

![](IMAGES/14.png)

- A dialogue box appears. Click the *show information...* link.

![](IMAGES/15.png)

- A web browser will automatically open after clicking on the hyperlink. If not, click on the link again. The url should be of Verisign.

![](IMAGES/16.png)

![](IMAGES/17.png)

- Save the page in the following way:
	- click on save
	- ignore any warnings
	- a dialogue box will appear 
	- within the file explorer, navigate to `C:\Windows\System32` and open `cmd.exe`

![](IMAGES/18.png)

![](IMAGES/19.png)

![](IMAGES/20.png)

- I spawned a shell as NT Authority System.

![](IMAGES/21.png)

- I then captured the root flag from Administrator's Desktop.

![](IMAGES/22.png)

I wanted to get a meterpreter shell. So I started the **metasploit** framework.

![](IMAGES/23.png)

I used the following exploit to get a reverse meterpreter shell by executing a payload on the target:
`exploit/multi/script/web_delivery`

![](IMAGES/24.png)

![](IMAGES/25.png)

![](IMAGES/26.png)

![](IMAGES/27.png)

![](IMAGES/28.png)

# SUMMARY
Here's a short summary of how I solved **Blaster**:
- I discovered a blogging application on the IIS web server
- I found a username and password in the blogs
- I captured user.txt from Wade's Desktop
- I found an application called **hhupd** on the Desktop and looked for related exploits.
- I used the application to bypass UAC and spawn a shell as NT Authority System
- I migrated the shell to a meterpreter session using the `web_delivery` exploit in **metasploit** framework.
- I captured the root flag from Administrator's Desktop.

That's it from my side!
Until next time :)

---
