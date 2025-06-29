To access the machine, click on the link given below:
- https://tryhackme.com/room/skynet

# SCANNING

I performed an **nmap** scan on the target to find open ports and services running on it.

![](IMAGES/1.png)

# FOOTHOLD

I visited the web application running on the target through my browser.

![](IMAGES/2.png)

I then fuzzed for interesting directories using **ffuf** and found a directory called *squirrelmail*

![](IMAGES/3.png)

Visiting the endpoint revealed a login panel.

![](IMAGES/4.png)

I did a google search regarding the version of *squirrelmail* and found some articles that hinted towards a remote code execution vulnerability.

![](IMAGES/5.png)

I then enumerated smb running on the target using **enum4linux** and found a username and interesting shares.

![](IMAGES/6.png)

![](IMAGES/7.png)

The *anonymous* share seemed interesting, so I connected to it and found some text files.

![](IMAGES/8.png)

I downloaded these files onto my local system.

![](IMAGES/9.png)

The *attention.txt* file confirmed the existence of a user called *milesdyson*. *log1.txt* contained a wordlist.

![](IMAGES/10.png)

I tried brute forcing the password of *milesdyson* but failed.

![](IMAGES/11.png)

Since the wordlist only contained 31 words, I used them to try logging into *squirrelmail* as *milesdyson* and was successfully able to log in using the first password. 

![](IMAGES/12.png)

![](IMAGES/13.png)

![](IMAGES/14.png)

The email from *skynet* regarding SAMBA password reset seemed interesting so I opened it and found my SAMBA password.

![](IMAGES/15.png)

![](IMAGES/16.png)

I then accessed my share using these credentials and found a *notes* directory.

![](IMAGES/17.png)

The *notes* directory contained a file called *important.txt* so I downloaded it.

![](IMAGES/18.png)

![](IMAGES/19.png)

The text file revealed a new endpoint.

![](IMAGES/20.png)

I accessed the endpoint but did not find anything useful at first.

![](IMAGES/21.png)

I then fuzzed for directories and found the *administrator* endpoint.

![](IMAGES/22.png)

I accessed it and found a *Cuppa* CMS login panel.

![](IMAGES/23.png)

I searched for exploits related to it and found that it was vulnerable to file inclusion.

![](IMAGES/24.png)

I read the file and looked for the endpoint that was vulnerable.

![](IMAGES/25.png)

![](IMAGES/26.png)

After verifying that the endpoint existed, I tried the exploit and was successfully able to read the `/etc/passwd` file.

![](IMAGES/27.png)

![](IMAGES/28.png)

I also tried reading the configuration file and found the credentials for the database.

![](IMAGES/29.png)

![](IMAGES/30.png)

![](IMAGES/31.png)

I then created a **php** reverse shell and hosted it on an **http** server locally.

![](IMAGES/32.png)

I exploited the file inclusion vulnerability to include the **php** payload to get a reverse shell on my **netcat** listener.

![](IMAGES/33.png)

![](IMAGES/34.png)

![](IMAGES/35.png)

I then captured the user flag from *mikedyson*'s home directory.

![](IMAGES/36.png)

![](IMAGES/37.png)

# PRIVILEGE ESCALATION

I examined my home directory and found an interesting directory called *backups*. Inside the directory, there was a **tar** archive and a bash script.

![](IMAGES/38.png)

I viewed the script and found it was used to create a backup of contents inside `/var/www/html` and save it as an archive inside the *backup.tgz* file.

![](IMAGES/39.png)

I could use the **wildcard** to escalate my privilege if it could be executed as root. I checked the `/etc/crontab` file and found that the *root* user executed the **bash** script to create the backup.

![](IMAGES/40.png)

I referred the the following article to escalate my privilege by exploiting wildcard:
- https://www.hackingarticles.in/exploiting-wildcard-for-privilege-escalation/

![](IMAGES/41.png)

I copied a **netcat** reverse shell payload from **revhsells.com**.

![](IMAGES/42.png)

Finally, I followed the methods from the article and used the payload to get a reverse shell as *root* user on another **netcat** listener.

![](IMAGES/43.png)

![](IMAGES/44.png)

After gaining root access, I captured the final flag from `/root` directory.

![](IMAGES/45.png)

That's it from my side! Until next time :)

---