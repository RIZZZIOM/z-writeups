
# SCANNING

I performed an **nmap** aggressive scan on the target to find open ports and the services running on them. This time, the system was an Active Directory server.

![](IMAGES/MIDDLE%20CAMP/1.png)
![](IMAGES/MIDDLE%20CAMP/2.png)

I updated my host file with the domains.

![](IMAGES/MIDDLE%20CAMP/3.png)

# FOOTHOLD

From the **base camp**, I had recovered full names of 2 users, I used the **username-anarchy** tool to create a wordlist of potential usernames.
- https://github.com/urbanadventurer/username-anarchy

![](IMAGES/MIDDLE%20CAMP/4.png)

After creating a user list, I used **kerbrute** to bruteforce valid usernames.
- https://github.com/ropnop/kerbrute

![](IMAGES/MIDDLE%20CAMP/5.png)

I added these usernames to a list. I then used the user list and the passwords recovered from the **base camp** to bruteforce valid credentials using **netexec**.

![](IMAGES/MIDDLE%20CAMP/6.png)

I found the password for *r.bud* user.

![](IMAGES/MIDDLE%20CAMP/7.png)

I then enumerated other users on the machine.

![](IMAGES/MIDDLE%20CAMP/8.png)

I then verified if *r.bud* had the permissions to access the server using **winrm** or **rdp**.

![](IMAGES/MIDDLE%20CAMP/9.png)

I then connected to the machine using **evil-winrm**.

![](IMAGES/MIDDLE%20CAMP/10.png)

I found some notes in the *Documents* directory.

![](IMAGES/MIDDLE%20CAMP/11.png)

Based on the message, I could brute force the password of *james* (j.bold) by creating a custom wordlist. I used **crunch** to create a wordlist with "rockyou" and 1 special character and 1 number.

![](IMAGES/MIDDLE%20CAMP/12.png)
![](IMAGES/MIDDLE%20CAMP/13.png)

I then used the custom wordlist to bruteforce the password for *j.bold* user.

![](IMAGES/MIDDLE%20CAMP/14.png)

![](IMAGES/MIDDLE%20CAMP/15.png)

I then used **bloodhound** for a comprehensive enumeration and to visualize the domain information.

![](IMAGES/MIDDLE%20CAMP/16.png)

I found something interesting. Our user *j.bold* had **GenericAll** permission over *j.smith* user.

![](IMAGES/MIDDLE%20CAMP/17.png)

I then used **bloodyAD** to set a new password for *j.smith*.
- https://github.com/CravateRouge/bloodyAD

![](IMAGES/MIDDLE%20CAMP/18.png)

I then accessed the target as *j.smith*.

![](IMAGES/MIDDLE%20CAMP/19.png)

I found the user flag from *Desktop*.

![](IMAGES/MIDDLE%20CAMP/20.png)

# PRIVILEGE ESCALATION

I viewed my permissions as found that I had **SeBackupPrivilege** and **SeRestorePrivilege**. These could be used to create a backup of any file present on the system. So, I created a backup of the SAM and SYSTEM files and downloaded it on my local system.

![](IMAGES/MIDDLE%20CAMP/21.png)
![](IMAGES/MIDDLE%20CAMP/22.png)

I then used **impacket-secretsdump** to dump the contents and got the *administrator* NTLM hash.

![](IMAGES/MIDDLE%20CAMP/23.png)

I then used the hash to access the target as *administrator*.

![](IMAGES/MIDDLE%20CAMP/24.png)

Finally, I captured the root flag from *administrator*'s *Desktop*.

![](IMAGES/MIDDLE%20CAMP/25.png)

With this, I pwned the middle camp as well. So, I finally move on to the summit.

---
