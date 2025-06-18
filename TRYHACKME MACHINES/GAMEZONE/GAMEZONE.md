To access the machine, click on the link given below:
- https://tryhackme.com/room/gamezone

# SCANNING

I performed an **nmap** aggressive scan to find open ports, services running and run default **nse** scripts.

![](IMAGES/1.png)

# FOOTHOLD

I accessed the web server through my browser.

![](IMAGES/2.png)

I then did a google reverse image search to find the name of the background character. This could be a valid username.

![](IMAGES/3.png)

![](IMAGES/4.png)


I bypassed the login mechanism using **sql** injection.

![](IMAGES/5.png)

I then got access to a page with search functionality.

![](IMAGES/6.png)

I searched for something and captured the request on **Burp Suite**.


![](IMAGES/7.png)

I wanted to test this for **SQL** injection as the login panel was also vulnerable to it. So, I added an asterisk after the value of `searchItem`.

![](IMAGES/8.png)

I then saved the request to a file and used **sqlmap** to dump database information.

![](IMAGES/9.png)

![](IMAGES/10.png)

![](IMAGES/11.png)

After finding the database information, I dumped its contents.

![](IMAGES/12.png)

I found the password hash for the user **agent47**.

![](IMAGES/13.png)

I then cracked the hash on **Crackstation**.

![](IMAGES/14.png)

Alternately, you could also crack the hash locally. For that, first identify the hash type using **hash-identifier** and then crack it using **john**.

![](IMAGES/15.png)

![](IMAGES/16.png)

After getting the credentials, I accessed the target using **ssh**.

![](IMAGES/17.png)

Finally, I captured the user flag from **agent47**'s home directory.

![](IMAGES/18.png)

# PRIVILEGE ESCALATION

I then used **netstat** to view connections and found that port 10000 was on listening state.

![](IMAGES/19.png)

I forwarded that port to my linux's port 10000 and performed an **nmap** scan on it.

![](IMAGES/20.png)

It was running **http** so I tried accessing it from my browser.

![](IMAGES/21.png)

However, access for the IP that I used while forwarding the port was denied.

![](IMAGES/22.png)

So I reforwarded the port using `Localhost` instead of using the LAN IP of the target.

![](IMAGES/23.png)

I then accessed the service running on port 10000 through my browser.

![](IMAGES/24.png)

I tried logging in using the **ssh** credential of the user '**agent47**'.

![](IMAGES/25.png)

I used **searchsploit** to look for exploits related to the **webmin** version running on the target.

![](IMAGES/26.png)

I looked at the **webmin** service and found that it was being run as **root**.

![](IMAGES/27.png)

So, if I exploited the service, I could gain root access. Hence, I started **metasploit** and selected the exploit related to the **webmin** version.

![](IMAGES/28.png)

I configured the necessary options.

![](IMAGES/29.png)

![](IMAGES/30.png)

![](IMAGES/31.png)

After rechecking the configuration, I ran the exploit.

![](IMAGES/32.png)

![](IMAGES/33.png)

I got a shell as **root** and captured the root flag from `/root` directory.

![](IMAGES/34.png)

---
