# GETTING STARTED

To access the challenge, click on the link given below:
https://tryhackme.com/r/room/dailybugle

> [!NOTE] 
> This writeup documents the steps that successfully led to pwnage of the machine. It does not include the dead-end steps encountered during the process (which were numerous). This is just my take on pwning the machine and you are welcome to choose a different path.

# RECONAISSANCE

I ran an **nmap** aggressive scan on the target to find open ports and the services running on them. It also performed a default script scan for additional information.

![](IMAGES/1.png)

# FOOTHOLD

The **nmap** scan revealed an **http** server on port **80** so I accessed it from my browser. 

![](IMAGES/2.png)

This seemed looked like a simple blog. The *Login Form* seemed interesting but before moving forward, I decided to check out other things. The **nmap** scan had also revealed a **robots.txt** file with a few directory paths so I decided to check that out.

![](IMAGES/3.png)

Out of all the paths, only the **`/administrator`** path revealed a **login** page. Rest all were just blank pages.

![](IMAGES/4.png)

After that, I ran an **nse** script on the server to find the answer to find more information. I ran the *http-enum.nse* script and found a file which contained the **joomla** version.

![](IMAGES/5.png)

I googled this version and found **sql** injection articles on it.

![](IMAGES/6.png)

I viewed the POC in **exploit-db** but didn't use it as the challenge asked us to use a python script instead.

![](IMAGES/7.png)

I checked another article which had a link to a github repository that contained a python code.

![](IMAGES/8.png)

![](IMAGES/9.png)

I downloaded the script on my system and ran it against the target to get the user credentials.

![](IMAGES/10.png)

The script revealed the hash for `jonah`. To find it's type, I search the **hashcat examples** page and found that it was a **bcrypt** hash.

![](IMAGES/11.png)

I then used **john** and found the password.

![](IMAGES/12.png)

I used the username revealed by the script and the cracked password to log into the **joomla administrative panel**

![](IMAGES/13.png)

I then looked for ways I could get an RCE from here and found a way in **hacktricks**.

![](IMAGES/14.png)

Hence, I first configured a **php** reverse shell payload on **revshells** with my IP and port.

![](IMAGES/15.png)

Then, as stated in the **hacktricks article**, I navigated to **Templates**. Here I found out that *`protostar`* was the default template being used on the web server.

![](IMAGES/16.png)

I then clicked on `Templates` and selected the `Protostar` template.

![](IMAGES/17.png)

Then, I selected the **index.php** code and pasted my reverse shell code. I then saved and closed this.

![](IMAGES/18.png)

Then I started an **nc** listener and visited the main page to get a reverse shell.

![](IMAGES/19.png)

I explored the directories that were present in `/var/www/html` and found a set of **mysql** credentials. I looked inside the **sql** server but found nothing useful. So, I just copied the password as it could be used in the future.

![](IMAGES/20.png)

Since the user flag wasn't in the `/var/www` directory, I tried checking the `/home` directory. However, when I tried going inside **jjameson**, I was denied from accessing it.

![](IMAGES/21.png)

I tried switching to this user with the password I had discovered earlier which luckily worked. 

![](IMAGES/22.png)

I then viewed inside the **jjameson** directory and found the user flag.

![](IMAGES/23.png)

# PRIVILEGE ESCALATION

I checked my **sudo** permissions by typing `sudo -l` and found that **jjameson** could execute `yum` as **sudo** without a password.

![](IMAGES/24.png)

I navigated to **gtfobins** and found a way to spawn an interactive shell as **root**.

![](IMAGES/25.png)

I simply copy pasted these commands on my terminal and spawned a **root** shell.

![](IMAGES/26.png)

![](IMAGES/27.png)

After becoming **root**, I captured the final flag from the `/root` directory.

![](IMAGES/28.png)

# CONCLUSION

Here's a short summary of how I pwned **daily bugle**:
- I discovered an administrator login panel on the server from the **robots.txt** file revealed in the **nmap** scan.
- I used the **http-enum** script to find the **joomla** version.
- The version was vulnerable to **sql** injection. I used a python POC script from [github](https://github.com/stefanlucas/Exploit-Joomla/tree/master) to get the username and hash for the panel.
- I cracked the hash using **john** and logged into the application.
- I added a **php** reverse shell script in the default template used by the application and got a reverse shell.
- I found the sql credentials in the `/var/www/html/configurations.php` file.
- I managed to switch to `jjameson` using the password found above and captured the user flag from `jjameson`'s home directory.
- I exploited **yum**'s **sudo** privilege to get **root** access and captured the final flag from `/root`.

That's it from my side!
Until next time :)

![](IMAGES/x.png)

---