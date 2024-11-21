> Welcome to my writeup where I am gonna be pwning the **Retro** machine from **TryHackMe**. This challenge has two flags, and our goal is to capture both. Let’s get started!

# GETTING STARTED

To access the challenge, click on the link given below:
https://tryhackme.com/r/room/retro

> [!NOTE] 
> This writeup documents the steps that successfully led to pwnage of the machine. It does not include the dead-end steps encountered during the process (which were numerous). This is just my take on pwning the machine and you are welcome to choose a different path.

# RECONNAISSANCE

I performed an **nmap** aggressive scan to find open ports and services running on the target.

![](IMAGES/1.png)

# FOOTHOLD

The machine had an http service and rdp running on it. I started of by visiting the webpage through my browser.

![](IMAGES/2.png)

It was just a default landing page. So I performed directory bruteforce using **ffuf** to find other directories and files.

![](IMAGES/3.png)

The **ffuf** scan revealed a page called `/retro`. So I visited it. This page looked like a blog on retro games.

![](IMAGES/4.png)

I FUZZED `/retro` using **ffuf** to find interesting files or directories.

![](IMAGES/5.png)

The **ffuf** scan discovered a **wordpress** login panel. I tried logging in using a random username and password and got an error saying *invalid username*. 

![](IMAGES/6.png)

![](IMAGES/7.png)

This error mechanism made it easy to find valid users. I navigated to the `/retro`'s home page and looked for potential usernames. Since the blogs were written by **Wade**, I could try using it as a **username**. I clicked on **Wade** to find information about the author.

![](IMAGES/8.png)

![](IMAGES/9.png)

I also tried using this username in the login panel. And received an error of *invalid password*.

![](IMAGES/10.png)

In the **author's** page, I found downloadable **RSS** data.

![](IMAGES/11.png)

I downloaded and viewed the data. One of the files revealed the password of **Wade**.

![](IMAGES/12.png)

![](IMAGES/13.png)

I logged in and got access to the **wp-admin** panel.

![](IMAGES/14.png)

I referred to **hacktricks** for ways to get an RCE from wp-admin. 

![](IMAGES/15.png)

> **note:** Since the target is a windows machine, I used a cross platform php-reverse-shell from the below github repo.

![](IMAGES/16.png)

I added my reverse shell payload in the **404.php** template so that I could execute it by causing an error.

![](IMAGES/17.png)

After updating the php code, I started a reverse shell listener using **nc** and triggered the payload by trying to navigate to a non-existent path.

![](IMAGES/18.png)

This gave me a reverse shell.

![](IMAGES/19.png)

Alternatively, I also found out that **wade** reused his password. The same credentials could also be used with **rdp**.

![](IMAGES/20.png)

**rdp** would provide a graphical access to the system making interaction much easier. So I connected to the target using **xfreerdp**.

![](IMAGES/21.png)

![](IMAGES/22.png)

On the **Desktop**, I found the **user.txt** file.

![](IMAGES/23.png)

# PRIVILEGE ESCALATION

The desktop had **chrome** so I opened it. It had a **cve** in bookmarks so I read about the vulnerability on my local system.

![](IMAGES/24.png)

![](IMAGES/25.png)

![](IMAGES/26.png)

This vulnerability allowed privilege escalation so I searched for POCs so that I could follow the steps to get administrator access.

![](IMAGES/27.png)

I found this **github** repo with a POC and tried following the steps.

![](IMAGES/28.png)

The recycle bin contained an application that could be vulnerable. So I restored it and followed the steps from the POC.

![](IMAGES/29.png)

![](IMAGES/30.png)

![](IMAGES/31.png)

![](IMAGES/32.png)

![](IMAGES/33.png)

![](IMAGES/34.png)

I got stuck here as I didn't get an option to choose a browser. 
- So I referred to https://muirlandoracle.co.uk/2020/01/06/retro-write-up/ and restarted the machine. 
- Before the exploit, I initialized both chrome and edge browsers. However, this time as well it didn't work. 
- Lastly, I manually navigated to `https://www.verisign.com/repository/CPS` but that didn't work aswell.

As a last resort, I tried looking for kernel exploits.

![](IMAGES/35.png)

I found a couple of exploits for the windows build running on the target.

![](IMAGES/36.png)

I visited the **`COM Aggregate Priv Exec`** page on **exploit-db**.

![](IMAGES/37.png)

I looked for that particular cve's POC.

![](IMAGES/38.png)

I then found a **github** repo that contained a binary that could escalate my privileges.

![](IMAGES/39.png)

So I downloaded the binary on my local system and transferred it to the target machine.

![](IMAGES/40.png)

![](IMAGES/41.png)

![](IMAGES/42.png)

Upon execution, the exploit spawned another instance of **cmd** as **administrator**.

![](IMAGES/43.png)

![](IMAGES/44.png)

I captured the **root** flag from **Administrator's** Desktop.

![](IMAGES/45.png)

# CONCLUSION

Here's a brief summary of how I pwned **retro**:
- **nmap** scan revealed a webserver running on the target along with **rdp**.
- Directory fuzzing revealed a gaming blog page and a **login** panel.
- Further reconnaissance on the `/retro` revealed a potential username `Wade`. Files present on Wade's profile also revealed his password.
- I then tried these credentials with **rdp** and got a graphical access on the system.
- I captured the user flag from Desktop.
- I tried exploiting the bookmarked cve but it failed due to a bug.
- I then exploited the kernel and got **administrative** access.
- Finally I captured the root flag from **administrator's** desktop.

![](IMAGES/x.png)

That's it from my side !
Until next time :)

---