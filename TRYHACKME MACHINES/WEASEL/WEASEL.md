Link to machine : https://tryhackme.com/room/weasel

# RECONAISSANCE

I performed an **nmap** aggressive scan on the target to reveal the open ports and services running on them.

![](IMAGES/1.png)

![](IMAGES/2.png)

# FOOTHOLD

The nmap scan revealed multiple services running like **ssh**, **rpc**, **smb**, **http**, **winrm** etc.

I started enumeration with **http**. I accessed it on my browser and found a jupyter notebook login page.

![](IMAGES/3.png)

I also checked the other port that was running **http** just to leave out nothing.

![](IMAGES/4.png)

I then looked for hidden files and directories using **ffuf** on the jupyter notebook page.

![](IMAGES/5.png)

![](IMAGES/6.png)

I found an *api* endpoint but found nothing interesting their.

![](IMAGES/7.png)

I then enumerated **smb** and listed the shares on the target.

![](IMAGES/8.png)

There was an interesting share called *datasci-team*. I then used **smbclient** to connect to the share and found the jupyter token. I could use this token to log into the jupyter notebook. So I downloaded this token.

![](IMAGES/9.png)

![](IMAGES/10.png)

I pasted the token and used it to set the password as **password**

![](IMAGES/11.png)

I found a bunch of files. The *workbook* file seemed interesting so I opened it.

![](IMAGES/12.png)

I was allowed to run **python** code in this workbook.

![](IMAGES/13.png)

So I added a new code block and a simple **python** code to get a reverse shell.

![](IMAGES/14.png)

I started a listener and executed the code to get a reverse shell from the target.

![](IMAGES/15.png)

I found an **ssh** private key of the *dev-datasci-lowpriv* user.

![](IMAGES/16.png)

I saved this private key on my local system and used it to log in through **ssh**.

![](IMAGES/17.png)

Finally, I got the user flag from *Desktop*.

![](IMAGES/18.png)

![](IMAGES/19.png)

# PRIVILEGE ESCALATION

To enumerate misconfigurations, I downloaded **PrivescCheck** and ran it on the system.

![](IMAGES/20.png)

It discovered and extracted the **winlogon** credentials.

![](IMAGES/21.png)

It then found a vulnerability that could be used to escalate our privilege.

![](IMAGES/22.png)

I referred to the below article to use the **AlwaysinstallElevated** policy to get admin access : 
https://www.hackingarticles.in/windows-privilege-escalation-alwaysinstallelevated/


Hence, I created a payload using **msfvenom**.

![](IMAGES/23.png)

I downloaded this payload on the system and started a **netcat** listener on my local machine.

![](IMAGES/24.png)

I ran the payload with **msiexec** as *dev-datasci-lowpriv* user.

![](IMAGES/25.png)

Finally, I got a reverse shell as **nt authority system**.

![](IMAGES/26.png)

I then captured the root flag from *Administrator*'s Desktop.

![](IMAGES/27.png)

![](IMAGES/28.png)

That's it from my side! Until next time :)

---