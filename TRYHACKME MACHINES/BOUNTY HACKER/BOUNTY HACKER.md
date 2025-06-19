To access the challenge, click on the link given below:
- https://tryhackme.com/room/cowboyhacker

# SCANNING

I scanned the target using **nmap** and found 3 services running:
- FTP
- SSH
- HTTP

![](IMAGES/1.png)

The default script scan also found that the FTP server allowed *anonymous* access.

# FOOTHOLD

I visited the website and found potential usernames. Besides that, I found nothing special. Even directory and file fuzzing yielded no results.

![](IMAGES/2.png)

I then moved onto FTP and logged in as an *anonymous* user. I then listed the contents and found 2 *txt* files.

![](IMAGES/3.png)

I downloaded both the files on my local system to view what's inside them.

![](IMAGES/4.png)

The *'task.txt'* file revealed 2 potential usernames:
- Vicious
- lin

The *locks.txt* file seemed like a wordlist.

![](IMAGES/5.png)

I then used **hydra** and found a valid **ssh** password from the *'locks.txt'* wordlist for the user *lin*.

![](IMAGES/6.png)

I then accessed the target using **ssh** and captured the user flag from *lin*'s Desktop.

![](IMAGES/7.png)

# PRIVILEGE ESCALATION

Since I had the password, I looked at *lin*'s **sudo** privileges. Here, I found *lin* was allowed to execute **tar** as root.

![](IMAGES/8.png)

I checked **GTFObins** to see if this binary could be directly exploited and found a way to spawn a **bash** shell.

![](IMAGES/9.png)

I referred to the command in **GTFObins** to spawn a **bash** shell as *root*.

![](IMAGES/10.png)

Finally, I captured the root flag from */root* directory.

![](IMAGES/11.png)

That's it from my side!
Until next time :)

---
