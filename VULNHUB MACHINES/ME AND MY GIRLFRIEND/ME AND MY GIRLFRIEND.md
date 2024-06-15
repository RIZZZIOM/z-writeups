# GETTING STARTED

To download the *Me And My Girlfriend* vm, click [here](https://www.vulnhub.com/entry/me-and-my-girlfriend-1,409/)

**DISCLAIMER**
> This writeup documents the steps that successfully led to pwnage of the machine. It does not include the dead-end steps encountered during the process (which were numerous). I recommend attempting to solve the lab independently. If you find yourself stuck on a phase for more than a day, you may refer to the writeups for guidance. Please note that this is just one approach to capturing all the flags, and there are alternative methods to solve the machine.

# RECONNAISSANCE

To identify the target, I performed an **nmap** network scan.

```bash

┌──(root㉿kali)-[~/ctf/meandmygf]
└─# nmap -sn 192.168.1.0/24                              
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-14 11:30 EDT
Nmap scan report for RTK_GW (192.168.1.1)
Host is up (0.0014s latency).
MAC Address: F8:C4:F3:D0:63:13 (Shanghai Infinity Wireless Technologies)
Nmap scan report for gfriEND (192.168.1.17)
Host is up (0.00011s latency).
MAC Address: 00:0C:29:75:EC:F9 (VMware)
Nmap scan report for kali (192.168.1.12)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 3.61 seconds
```

After finding the target IP to be *192.168.1.17*, I performed an **nmap** aggressive scan to find its ports and services.

![[IMAGES/1.png]]

# CAPTURING FLAG 1

Since port 80 was running, I used **curl** to fetch information about the page and also accessed it on the browser.

![[IMAGES/2.png]]

![[IMAGES/3.png]]

The site had an *HTML comment* that gave me a hint on how it could be accessed. I had to use the *X-Forwarded-For* header and use the localhost IP, i.e., 127.0.0.1, to get the intended result. This is because:
- '*Sorry This Site Can Only Be Accessed Local*' - it can only be accessed through localhost, i.e., 127.0.0.1.
- The commented part gives me a hint that this has something to do with the *X-Forwarded-For* header.

Hence, I started Burp Suite and configured it as my proxy using **[FoxyProxy](https://addons.mozilla.org/en-US/firefox/addon/foxyproxy-standard/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search)**.

Then I turned on my **Burp** Proxy to intercept the request and tried accessing the website. I configured **Burp** to add the following header in all the requests made to the target.

```http

X-Forwarded-For: 127.0.0.1
```

I went to **Proxy** and clicked on **Proxy settings**; then searched for **match and replace**.

![[IMAGES/5.png]]

I checked the *Only apply to in-scope items* and clicked on **Add**. Then, I added the header in the following manner:

![[IMAGES/6.png]]

Finally, I clicked on **OK** and closed the settings. Then, I went to the **HTTP history** tab under **Proxy** and right-clicked on the request made to the target URL, then selected **Add to scope**.

![[IMAGES/11.png]]

With this, I was done with the settings. I then accessed the target again and got access to the web page.

![[IMAGES/7.png]]

I clicked on *Register* and registered myself with the username *zbot* and password *pass123*.

![[IMAGES/8.png]]

I then logged in using these credentials.

![[IMAGES/9.png]]

The **Profile** page had an option to change the password.

![[IMAGES/10.png]]

I turned on my **Burp** Proxy and refreshed this page to intercept the request.

![[IMAGES/12.png]]

I sent the request to **Repeater** for inspection.

![[IMAGES/13.png]]

To get a response from the server, I clicked on the **Send** button.

The response showed me my password in plaintext format.

![[IMAGES/14.png]]

Also, upon inspecting the URL, I found that it fetched my details based on an ID.

![[IMAGES/15.png]]

So, if I changed the *user_id* parameter, I could perform **IDOR**.

>IDOR stands for "Insecure Direct Object References." Let's break it down into a simple analogy to understand it better.
>
>Imagine you're using a photo storage website where you can upload and view your photos. Each photo has a unique number, like a code, to identify it. Normally, you should only be able to see your photos, not someone else's.
>
>However, let's say there's an IDOR vulnerability on this website. This means if you change the photo's unique number in the website's address bar to a different number, you could end up seeing someone else's photo, even though you're not supposed to.
>
>In technical terms, IDOR happens when a website or application doesn't properly check if a user is allowed to access a particular piece of data. So, just by changing a small part of a request (like the number in the URL), someone can access data they shouldn't be able to, like other people's photos, documents, or personal information.
>
>It's like having a key that can accidentally open doors it’s not supposed to, just because the locks weren’t set up securely.

Hence, I changed the ID value and got multiple user credentials. (This could also be done through the browser using *Inspect Element*.)

![[IMAGES/16.png]]

| id  | username       | password    |
| --- | -------------- | ----------- |
| 1   | eweuhtandingan | skuyatuh    |
| 2   | aingmaung      | qwerty!!!   |
| 3   | sundatea       | indONEsia   |
| 4   | sedihaingmah   | cedihhihihi |
| 5   | alice          | 4lic3       |
Since the box had *SSH* service enabled, I tried brute-forcing it for the correct credentials using **hydra**.

```bash

vim passwords.list
#inserted the passwords
vim users.list
#inserted the usernames
```

![[IMAGES/17.png]]

Hence, I logged in using these credentials.

![[IMAGES/18.png]]

I looked at the files and directories and found the first flag inside the *.my_secret* folder.

![[IMAGES/19.png]]

Hence I captured the first flag.

![[IMAGES/20.png]]

I also found another note there

![[IMAGES/21.png]]

![[IMAGES/22.png]]
# CAPTURING FLAG 2

I moved into the */tmp* directory. Then, I downloaded the **[Linux Smart Enumeration](https://github.com/diego-treitos/linux-smart-enumeration)** script on my PC and transferred it to my target.

![[IMAGES/23.png]]

![[IMAGES/24.png]]

Finally, I executed the script.

![[IMAGES/25.png]]

![[IMAGES/26.png]]

I found out I could execute the following command without a password as sudo. To verify it, I checked my privilege with the **sudo** command.

![[IMAGES/27.png]]

To find a way to exploit this, I went to **[GTFOBins](https://gtfobins.github.io/)** and searched for **php**.

![[IMAGES/28.png]]

I found a way to exploit PHP if it was able to run as sudo. Hence, I ran the script and just changed the **CMD="/bin/sh"** to **CMD=/bin/bash** to get a **bash** shell.

![[IMAGES/29.png]]

- **`CMD="/bin/bash"`**: This sets a variable called `CMD` to the value `/bin/bash`, which is the path to the bash shell.
- **`sudo`**: This part of the command runs the following command with superuser (root) privileges.
- **`php -r`**: This tells PHP to run the code provided as a string.
- **`system('$CMD');`**: This PHP code runs the command stored in the `CMD` variable, which is `/bin/bash`, using the system function.

Hence I got root access. Now I navigate to the *root* directory and capture the final flag.

![[IMAGES/30.png]]

# CLOSURE

I successfully pwned the system and captured the flags. Here's how it went:
- After gaining access to the site, I discovered an **IDOR** vulnerability and used it to retrieve usernames and passwords.
- I used those credentials to **SSH** into the target system.
- I found the first flag in a hidden folder called *.my_secret*.
- I discovered that the user could execute **PHP** as sudo without a password.
- I used **GTFOBins** to find a way to exploit this and gain root access.
- Finally, I captured the second flag in the *root* directory.

That's it from my side. Until next time! :)

![[IMAGES/31.png]]

------------------------------------------------------------------------------------