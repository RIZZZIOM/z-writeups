
Link to machine : https://www.hackthebox.com/machines/linkvortex

![](IMAGES/x.png)

# RECONNAISSANCE

I performed an **nmap** aggressive scan to find running ports and services.

![](IMAGES/1.png)

I added the machine domain to my */etc/hosts* file for name resolution.

![](IMAGES/2.png)

# FOOTHOLD

Since the server had an **http** service running, I visited the website from my browser.

![](IMAGES/3.png)

I then performed a directory bruteforce using **ffuf** to find other files and directories.

![](IMAGES/4.png)

The directory bruteforce revealed *robots.txt* file. This file could reveal more interesting files or directories.

![](IMAGES/5.png)

I accessed the paths revealed in the *robots.txt* file and landed on a login page.

![](IMAGES/6.png)

![](IMAGES/7.png)

I tried logging in using a default mail and common password but failed.

![](IMAGES/8.png)

Since I had no other leads, I tried bruteforcing subdomains.

![](IMAGES/9.png)

I found a subdomain and added it to my *hosts* file.

![](IMAGES/10.png)

I visited the newly discovered subdomain and found a message stating the site was under construction.

![](IMAGES/11.png)

I again performed a directory bruteforce to find directories and files in the subdomain and found a git repository.

![](IMAGES/12.png)

![](IMAGES/13.png)

I downloaded **GitHack** from github and ran it on the target to download the entire repository on my local system.

![](IMAGES/14.png)

![](IMAGES/15.png)

It contained a folder and docker configuration file.

![](IMAGES/16.png)

I inspected the files and found the password for the admin email account.

![](IMAGES/17.png)

I used this credential to log into the web application.

![](IMAGES/18.png)

I then used **wappalizer** to identify information about the CMS version and the tech stack being used.

![](IMAGES/19.png)

I searched available exploits for the CMS version and downloaded a python exploit.

![](IMAGES/20.png)

![](IMAGES/21.png)

I downloaded the exploit and tried reading the */etc/passwd* file.

![](IMAGES/22.png)

Since I was able to read the file, I tried reading the configuration file of the CMS. I found the path of this file from the Dockerfile.

![](IMAGES/23.png)

Here, I found a username and password of another user.

![](IMAGES/24.png)

I tried logging in using these credentials and got shell access on the target.

![](IMAGES/25.png)

Finally I captured the user flag from its home directory.

![](IMAGES/26.png)

# PRIVILEGE ESCALATION

I viewed my **sudo** privileges and found that I was allowed to run a particular command as sudo without any password.

![](IMAGES/27.png)

I viewed the allowed bash script to see what it does.

![](IMAGES/28.png)

This script checks if the input is a *.png*. If it isn't, the script exits with an error. Then it checks if the file is a **symlink**. If it is, the script reads the actual target. If the target points to */etc* or */root*, the link is deleted, else it is moved to */var/quarantined/*. If the **CHECK_CONTENT** variable is set to true, it tries to display the content of the quarantined file.

Hence, I created a link to the root flag on my home directory.

![](IMAGES/29.png)

I then linked this link to another png file on my home directory.

![](IMAGES/30.png)

Finally, I executed the script and passed my png file to get the contents of the root flag. This worked because the CHECK_CONTENT flag would have been true when the script read the source of the png file (which was a file linked to the root flag).

![](IMAGES/31.png)

That's it from my side, Until next time :)

---
