Link to machine : https://tryhackme.com/r/room/tomghost

# RECONAISSANCE

I performed an **nmap** aggressive scan to find open ports and the services running on the target. It also performs script scans making the recon process more efficient.

![](IMAGES/1.png)

# FOOTHOLD

The **nmap** scan revealed a tomcat server running on the target so I accessed it through my browser.

![](IMAGES/2.png)

I also bruteforced directories to increase my attack surface.

![](IMAGES/3.png)

I discovered the manager console but was denied access to it.

![](IMAGES/4.png)

![](IMAGES/5.png)

Since I did not know much about AJP, I read about it on **hacktricks**. I found that the tomcat server might be vulnerable to CVE-2020-1938.

![](IMAGES/6.png)

![](IMAGES/7.png)

Hence, I looked for exploits and found some in **exploit-db**

![](IMAGES/8.png)

I downloaded and ran the exploit to get a username and password.

![](IMAGES/9.png)

I tested if the credentials could be used for **ssh** and finally used **ssh** to get shell access on the target.

![](IMAGES/10.png)

![](IMAGES/11.png)

I found a **gpg** encrypted file and a **gpg** key.

![](IMAGES/12.png)

I also found the user flag in *merlin*'s home directory.

![](IMAGES/13.png)

# PRIVILEGE ESCALATION

I downloaded **linpeas** to do privilege escalation checks but found nothing interesting.

![](IMAGES/14.png)

![](IMAGES/15.png)

Since I had an encrypted file that I hadn't analyzed, I transferred the file and the key to my local system.

![](IMAGES/16.png)

![](IMAGES/17.png)

I imported the **gpg** key and tried decrypting the **encrypted** file, however, I was asked for a passphrase.

![](IMAGES/18.png)

Since I did not have a passphrase, I attempted to crack it using **john**. I first converted the key file to **john** format and then cracked it to find the password.

![](IMAGES/19.png)

After finding the valid password, I decrypted the file and found credentials for *merlin*.

![](IMAGES/20.png)

I switched user to *merlin*

![](IMAGES/21.png)

Then I looked for my **sudo** privileges and found I was allowed to execute **zip** as root.

![](IMAGES/22.png)

I visited **gtfobins** and found a way to exploit the **sudo** privileges to get a privileged access.

![](IMAGES/23.png)

I used the method shown in **gtfobins** to get root access.

![](IMAGES/24.png)

Finally I captured the root flag from the */root* directory.

![](IMAGES/25.png)

That's it from my side.
Happy hacking :)

---