- https://tryhackme.com/room/backtrack

# SCANNING

I performed an **nmap** aggressive scan on the target and found a bunch of open ports.

![](IMAGES/1.png)

# FOOTHOLD

I found a **tomcat** landing page on port 8080 a service called *Aria 2 WebUI* running on port 8888.

![](IMAGES/2.png)

![](IMAGES/3.png)

A simple google search about *Aria 2 WebUI* revealed a path traversal vulnerability in it.

![](IMAGES/4.png)

![](IMAGES/5.png)

I was able to read local files using this. The `/etc/passwd` file revealed the users present on the system.

![](IMAGES/6.png)

![](IMAGES/7.png)

I then found the path of the **Tomcat** configuration file and read it.

![](IMAGES/8.png)

This file revealed the username and password for the tomcat manager.

![](IMAGES/9.png)

However, when I tried accessing the manager panel using the credentials, something went wrong.

![](IMAGES/10.png)

![](IMAGES/11.png)

I was able to access the *Server-Status* endpoint which meant that the credentials were indeed valid.

![](IMAGES/12.png)

I referred to the following articles:
- https://www.hackingarticles.in/tomcat-penetration-testing/
- https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/tomcat/index.html?highlight=tomcat#msfvenom-reverse-shell

I used them as a reference to generate a malicious payload that I could upload for a reverse shell.

![](IMAGES/13.png)

![](IMAGES/14.png)

I also found a way to upload the file through command line through **stack overflow**.

![](IMAGES/15.png)

Finally, I uploaded the payload and accessed it to get a reverse shell.

![](IMAGES/16.png)

![](IMAGES/17.png)

![](IMAGES/18.png)

I got a shell as **tomcat**, did not have the permissions to access the contents inside the other user directories.

![](IMAGES/19.png)

I switched back to my home directory and found the first flag.

![](IMAGES/20.png)

# LATERAL MOVEMENT

I listed my **sudo** privileges and found that I could run a binary on a bunch of **Yml** files. One interesting thing about the allowed command is the **wildcard (`*`)** denoting all **yml** files. I could use backtracks to point to any other **yml** file of my choice.

![](IMAGES/21.png)

**GTFOBins** had a way to exploit this to escalate privilege.

![](IMAGES/22.png)

I followed the methods described on **GTFOBins** and spawned a shell as **wilbur**.

![](IMAGES/23.png)

![](IMAGES/24.png)
> note: I used backtracks to point to the new **yml** file that I had created.

![](IMAGES/25.png)

I spawned an interactive **bash** shell.

![](IMAGES/26.png)

After getting shell access as **wilbur**, I read the contents inside the home directory and found a note that contained the credentials of **orville** for a custom web app that was running locally.

![](IMAGES/27.png)

I listed the active ports and found port **80** on listening state.

![](IMAGES/28.png)

I also found my credentials in a hidden file called *`.just_in_case.txt`*

![](IMAGES/29.png)

I then connected to the target using these credentials to get a better shell.

![](IMAGES/30.png)

![](IMAGES/31.png)

I also performed Local port forwarding using these credentials.

![](IMAGES/32.png)

I then accessed the web application through my browser.

![](IMAGES/33.png)

I accessed the login panel and logged in using the credentials that I had discovered earlier.

![](IMAGES/34.png)

I tried uploading a reverse shell but failed to do so due to some restrictions.

![](IMAGES/35.png)

![](IMAGES/36.png)

I then created a simple web shell and tried various bypass techniques and finally managed to upload the file using **double extensions**.

![](IMAGES/37.png)

![](IMAGES/38.png)

I then tried executing a command but failed.

![](IMAGES/39.png)

I then tried uploading the file outside it's intended directory.

![](IMAGES/40.png)

However, it did not work.

![](IMAGES/41.png)

**Double URL encoding** the backtracks bypassed the restrictions and allowed me to upload the file outside the *uploads* directory.

![](IMAGES/42.png)

Finally, I was able to execute commands.

![](IMAGES/43.png)

I then used **nc** to get a reverse shell from the target.

![](IMAGES/44.png)

Since the application was running as **orville**, I got a shell as that user.

![](IMAGES/45.png)

I then captured the second flag from the home directory.

![](IMAGES/46.png)

# PRIVILEGE ESCALATION

There was a zip file so I transferred it on my local system.

![](IMAGES/47.png)

When I uncompressed the file, I realized it was only a backup of the web application.

![](IMAGES/48.png)

I did not find anything useful on the target. One thing that I noticed when I uncompressed the backup was that it contained the malicious **php** file that I had uploaded to get a shell as **orville**. This meant that the backups were being made periodically. 

To monitor the background tasks, I download **pspy** and transferred it onto the system.

![](IMAGES/49.png)

![](IMAGES/50.png)

Running **pspy** revealed something interesting...

![](IMAGES/51.png)

Multiple commands were being executed as root and then the user was changed to **orville**.

![](IMAGES/52.png)

When root switched to **Orville** using the command `su - orville`, it created a new shell for orville. The `-` in the command basically acts like a full login for **Orville**.

So, the root shell doesn't actually end. It just runs in the background while **Orville**’s shell spawns in the foreground. I found an article that spoke about this privesc vector: https://www.errno.fr/TTYPushback.html

Instead of exiting **Orville**’s shell (as this could close the entire session), we could use this technique to send a `sigstop` signal, to pause the **orville** shell and switch back to the original root shell that is running in the background.

I created the following **python** payload to add an **SUID** bit on the `/bin/bash` binary and transferred it on the target.

![](IMAGES/53.png)

I then added the command to execute this in the **`.bashrc`** file so that it could be executed when the root user switched to **orville**.

![](IMAGES/54.png)

After some time, an SUID bit was added to the `/bin/bash` binary.

![](IMAGES/55.png)

Finally, I executed **bash** in privileged mode and got root access.

![](IMAGES/56.png)

I then captured the final flag from root user's home directory.

![](IMAGES/57.png)

I also found the root credentials inside the **manage.py** file present in the `/root` directory.

![](IMAGES/58.png)

That's it from my side!
Until next time :)

---
