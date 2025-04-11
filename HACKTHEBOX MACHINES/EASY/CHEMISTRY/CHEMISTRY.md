![](IMAGES/X.png)

To access the machine click on the link given below:
https://www.hackthebox.com/machines/chemistry

# RECONAISSANCE

I performed an **nmap** aggressive scan on the target to find open ports and the services running on them.

![](IMAGES/1.png)

# FOOTHOLD

The **nmap** scan revealed only 2 ports, hence I accessed the port running **http** service using **firefox**.

![](IMAGES/2.png)

I registered using `test`:`test` 

![](IMAGES/3.png)

The application allowed us to upload a **CIF** file.

![](IMAGES/4.png)

Since the **nmap** scan revealed the backend to be running on **python**, I tried uploading a **python** script but failed.

![](IMAGES/5.png)

![](IMAGES/6.png)

I then uploaded a **CIF** file to analyze the behavior. The application also contained a dummy CIF file for us to download, so I downloaded it.

![](IMAGES/7.png)

![](IMAGES/8.png)

![](IMAGES/9.png)

Since I found nothing of interest, I googled vulnerabilities that could be exploited and found interesting articles.

![](IMAGES/10.png)

Hence I downloaded the example **cif** file.

![](IMAGES/11.png)

I visited **revshells** and generated a simple **bash** payload.

![](IMAGES/12.png)

I modified the **CIF** file and added the payload in it.

![](IMAGES/13.png)

Finally I uploaded the malicious CIF file.

![](IMAGES/14.png)

I started a reverse shell listener using **netcat** and when I executed the CIF file, I got a reverse shell.

![](IMAGES/15.png)

I spawned a **tty** shell and exported my terminal for better shell functionality.

![](IMAGES/16.png)

I discovered the existence of an sqlite file along with a password.

![](IMAGES/17.png)

![](IMAGES/18.png)

Then I tried looking for the flag. I found it in the home directory of another user called **rosa**. However, since I did not have enough permissions, I could not access it.

![](IMAGES/19.png)

# PRIVILEGE ESCALATION

I then viewed the sqlite database file and found the md5 hashes of different users including **rosa**.

![](IMAGES/20.png)

![](IMAGES/21.png)

I visited **crackstation** and cracked the password hash.

![](IMAGES/22.png)

Finally, I used the credentials to log in as **rosa** and captured the user flag from the home directory.

![](IMAGES/23.png)

![](IMAGES/24.png)

After capturing the user flag, I downloaded and ran **linux smart enumeration** script to look for ways to escalate privilege, however the script found nothing interesting.

![](IMAGES/25.png)

I then looked at listening ports and found port **8080** bounded to localhost on listen mode.

![](IMAGES/26.png)

To access the internally bounded port, I performed port forwarding, binding port 1234 to the localhost port 8080 of the target.

![](IMAGES/27.png)

![](IMAGES/28.png)

After that, I was able to access the service running on port 8080.

![](IMAGES/29.png)

I performed an **nmap** scan on the service and found the service version.

![](IMAGES/30.png)

further footprinting revealed a **path traversal** vulnerability associated with this particular service version.

![](IMAGES/31.png)

![](IMAGES/32.png)

Since the vulnerability was path traversal, I looked for directories on the target using **ffuf**.

![](IMAGES/33.png)

I then found a PoC of the CVE on **github** and got a reference on how the vulnerability could be explotied. 

![](IMAGES/34.png)

![](IMAGES/35.png)

I then used **curl** and tried accessing the **/etc/passwd** file by exploiting the **path traversal** vulnerability.

![](IMAGES/36.png)

Since I was able to confirm the vulnerability, I exploited it to access the root flag from the **/root** directory.

![](IMAGES/37.png)

With that we successfully pwned the machine **chemistry** :)
Happy Hacking !

---
