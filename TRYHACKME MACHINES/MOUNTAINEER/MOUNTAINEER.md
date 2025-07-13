- https://tryhackme.com/room/mountaineerlinux

# SCANNING

I performed an **nmap** aggressive scan and found 2 open ports on the target.

![](IMAGES/1.png)

# FOOTHOLD

I accessed the web application running on the target and landed on a default **nginx** page.

![](IMAGES/2.png)

I fuzzed it for hidden directories and found a directory called *wordpress*.

![](IMAGES/3.png)

I accessed the page and noticed that the resources weren't being loaded properly.

![](IMAGES/4.png)

I viewed the *network* tab in my developer's tab to see the requests made by the application and found the domain that was mapped to it.

![](IMAGES/5.png)

I mapped the domain to the target IP in my */etc/hosts* file and refreshed the page.

![](IMAGES/6.png)

I then fuzzed for other files in the directories and confirmed that this was a wordpress installation.

![](IMAGES/7.png)

The `wp-login.php` endpoint was accessible which could allow me to access the dashboard and potentially get a reverse shell.

![](IMAGES/8.png)

I then enumerated plugins and users using **wpscan**.

![](IMAGES/9.png)

![](IMAGES/10.png)

![](IMAGES/11.png)

![](IMAGES/12.png)

![](IMAGES/13.png)

**wpscan** found a bunch of users and interesting plugins. The version of *modern-events-calendar* plugin seemed to have some vulnerabilities.

![](IMAGES/14.png)

I found an unauthenticated exploit so I downloaded it on my local system and read its documentation on **exploit-db**.

![](IMAGES/15.png)

![](IMAGES/16.png)

I ran the exploit but didn't get anything useful.

![](IMAGES/17.png)

I then booted the **metasploit** framework and searched for modules related to that plugin.

![](IMAGES/18.png)

Here, I found an auxiliary scanner that exploited an **sql injection** vulnerability to query username, passwords. I added the required settings and ran the scanner to find the admin credentials.

![](IMAGES/19.png)

![](IMAGES/20.png)

However, these credentials did not work.

![](IMAGES/21.png)

I then fuzzed for directories and noticed something unusual. **Wordpress** stores image in the `wp-content` directory. There was a separate folder called **`images`**.  This could mean that it probably was an alias that pointed to the actual folder inside `wp-content`.

![](IMAGES/22.png)

I referred to **hacktricks** and found a potential misconfiguration on the target.

![](IMAGES/23.png)

When I tried the exploiting this, it worked and I was able to read local files.

![](IMAGES/24.png)

Reading the `/etc/passwd` file disclosed more users.

![](IMAGES/25.png)

I then read the configuration file but did not find anything useful.

![](IMAGES/26.png)

I then googled for interesting files that I could read inside the directory and tried looking for files inside `/etc/nginx/sites-available/`.

![](IMAGES/27.png)

I used **ffuf** and found a file called `default`.

![](IMAGES/28.png)

Reading the file revealed a new subdomain.

![](IMAGES/29.png)

I added the subdomain in my `/etc/hosts` file and accessed the endpoint.

![](IMAGES/30.png)

I tried the admin credentials but failed to log in using it. I then tried default credentials with the discovered usernames.

![](IMAGES/31.png)

Finally, I logged in using **`k2:k2`**

![](IMAGES/32.png)

I found a mail that contained a password.

![](IMAGES/33.png)

Another mail was about the password that was revealed.

![](IMAGES/34.png)

I tried the password with the `k2` user and managed to log in through the `wp-login` endpoint.

![](IMAGES/35.png)

With valid credentials, I could now try getting remote code execution through the rce module for the vulnerable plugin on **metasploit**.

![](IMAGES/36.png)

I configured the options and ran the exploit to get a **meterpreter** shell.

![](IMAGES/37.png)

I got a shell as *www-data*.

![](IMAGES/38.png)

# LATERAL MOVEMENT

I then enumerated the contents present inside the user directories and discovered that the flag was located inside the *kangchenjunga*'s home directory.

![](IMAGES/39.png)

I found a file called *ToDo.txt* inside *nanga*'s home directory which contained an interesting message.

![](IMAGES/40.png)

I also found a **keepass** file inside *lhotse*'s home directory.

![](IMAGES/41.png)

I downloaded the file locally and tried opening it. However, it was password protected.

![](IMAGES/42.png)

![](IMAGES/43.png)

I used **keepass2john** to convert it into **john** crackable format and tried cracking the password using *rockyou.txt*, however, it was taking quite some time to crack it.

![](IMAGES/44.png)

With the password cracking running in the background, I tried switching to the user **k2** using the credentials I had previously discovered. I successfully, switched using **`k2:k2`**. After switching the user, I decided to view the mail because of what the *ToDo.txt* said.

![](IMAGES/45.png)

I found some interesting information that could be used to create a custom password list.

![](IMAGES/46.png)

Hence, I downloaded **CUPP** and ran it in interactive mode.

![](IMAGES/47.png)

I filled in the information that was available to me and generated a custom wordlist.

![](IMAGES/48.png)

![](IMAGES/49.png)

I then restarted password cracking with **john** using the custom wordlist and found the password.

![](IMAGES/50.png)

The password allowed me to view the contents inside the file where I found user credentials.

![](IMAGES/51.png)

![](IMAGES/52.png)

![](IMAGES/53.png)

Since, *kangchenjunga* had the local flag, I verified the credentials and logged into the target using **ssh**.

![](IMAGES/54.png)

![](IMAGES/55.png)

Finally, I captured the local flag.

![](IMAGES/56.png)

# PRIVILEGE ESCALATION

My directory also contained a **`.bash_history`** file which contained command history.

![](IMAGES/57.png)

I read the file and found the root credentials.

![](IMAGES/58.png)

I then switched to root user and captured the root flag from the *`/root`* directory.

![](IMAGES/59.png)

![](IMAGES/60.png)

That's it from my side !
Until next time :)

---
