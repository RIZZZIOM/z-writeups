
To access the machine, click on the link given below:
https://tryhackme.com/room/enterprise

# SCANNING

I performed an **nmap** aggressive scan on the target to find open ports and the services running on them.

![](IMAGES/1.png)

![](IMAGES/2.png)

# FOOTHOLD

I accessed the web application running on port 80 and 7990.

![](IMAGES/3.png)

![](IMAGES/4.png)

I fuzzed hidden files and found a *robots.txt* file on the domain controllers web app. This seemed wierd so I accessed it through my browser.


![](IMAGES/5.png)

However, I did not get any useful information.

![](IMAGES/6.png)

I then looked for smb shares using **smbclient**.

![](IMAGES/7.png)

There were 2 interesting shares. So I first accessed the *docs* share and downloaded the files present in it.

![](IMAGES/8.png)

Both the documents were password protected, so I couldn't open it.

![](IMAGES/9.png)

![](IMAGES/10.png)

I then accessed the *Users* share.

![](IMAGES/11.png)

It had a bunch of files so I downloaded everything.

![](IMAGES/12.png)

I used the `find` command to look for files containing words like `history` or `secret` or something similar in their name and examined them.

```bash
find /root/thm/enterprise/APPADMIN/ -type f -name "*history*"
```

I found a credential inside a file called `Consolehost_history.txt`.

![](IMAGES/13.png)

![](IMAGES/14.png)

I tested the credential to see if I could use it for command execution but failed.

![](IMAGES/15.png)

The atlasian login page said they were shifting to github, so i looked for its github.

![](IMAGES/16.png)

I found the organization on github and viewed its peoples.

![](IMAGES/17.png)

![](IMAGES/18.png)

I accessed the user profile and viewed the uploaded script and found a new set of credentials.

![](IMAGES/19.png)

![](IMAGES/20.png)

These were valid credentials but it didn't allow us to execute commands. 

![](IMAGES/21.png)

I used these credentials to look for any other share that might be reserved for the user.

![](IMAGES/22.png)

I then enumerated the users and found  the credential of another user in its description.

![](IMAGES/23.png)

I tested these creds aswell but it didnt provide access to the system.

![](IMAGES/24.png)

I then looked for kerberoastable accounts and got the kerberoast hash for the user `bitbucket`.

![](IMAGES/25.png)

I then cracked the hash using **john**

![](IMAGES/26.png)

Finally, I accessed the machine using this credential. Through **rdp**.

![](IMAGES/27.png)

![](IMAGES/28.png)

I then accessed the user flag from the Desktop.

![](IMAGES/29.png)

# PRIVILEGE ESCALATION

I downloaded few tools for enumerating privilege escalation vectors.
- PowerView
- PowerUp
- Sharphound

![](IMAGES/30.png)

I first ran **sharphound** for comprehensive enumeration.

![](IMAGES/31.png)

I downloaded the zip created by sharphound through smb share and uploaded it on **bloodhound**.

![](IMAGES/32.png)

I found an interesting permission. The *Replication* user was allowed to perform **dc-sync**.

![](IMAGES/33.png)

I then ran **PowerUp** to look for misconfigurations in the local system.

![](IMAGES/34.png)

It discovered an **unquoted service path** vulnerability.

![](IMAGES/35.png)

I then verified my access on the path of the exe.

![](IMAGES/36.png)

![](IMAGES/37.png)

Since I had access, I created a payload using **msfvenom** and upload it on the vulnerable path.

![](IMAGES/38.png)

Finally, I started a listener on my local machine and restarted the service.

![](IMAGES/39.png)

Finally, I got a reverse shell as **nt authority\system**.

![](IMAGES/40.png)

I then captured the root flag from **Administrator's** desktop.

![](IMAGES/41.png)

---
