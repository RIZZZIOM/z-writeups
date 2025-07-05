https://tryhackme.com/room/allsignspoint2pwnage

# SCANNING

I scanned the target using **nmap** to find open ports and various service information.

![](IMAGES/1.png)
![](IMAGES/2.png)

# FOOTHOLD

The **nmap** script scan revealed that the **ftp** server running on the target allowed anonymous login, so I connected to the server and found a txt file. I downloaded the text file on my local system.

![](IMAGES/3.png)

The text contained a notice regarding a file share on the target for image upload and management.

![](IMAGES/4.png)

I then listed out the shares on the target and found the `images$` share.

![](IMAGES/5.png)

I accessed the share and found a bunch of images.

![](IMAGES/6.png)

I then accessed the web application. My guess was that the images on the web page was being loaded from the `images$` share. This also meant that this share would be accessible through the browser as well.

![](IMAGES/7.png)

I then fuzzed for files using **ffuf** and found a bunch of endpoints but when I visited those endpoints, I didn't find anything useful.

![](IMAGES/8.png)

![](IMAGES/9.png)

I then fuzzed for directories and found out that the target was hosting the application on a **xampp** server. I also found the `images` directory that contained the images shown on the web page.

![](IMAGES/10.png)

![](IMAGES/11.png)

Since I had access over the `images$` share, I could upload a malicious file and execute it through the browser. I uploaded **pentestmonkey**'s **php-reverse-shell** through the share.

![](IMAGES/12.png)

![](IMAGES/13.png)

refreshing the `images` webpage reflected the uploaded payload.

![](IMAGES/14.png)

I then started a **netcat** listener.

![](IMAGES/15.png)

However, when I tried executing the payload, it failed.

![](IMAGES/16.png)

I tried various payloads and finally got a reverse shell using **PHP Ivan Sincek**'s payload. I copied the code after configuring the appropriate listener IP and port.

![](IMAGES/17.png)

I removed the previous reverse shell payload and created a new one. I pasted the code and transferred it to the target using the `images$` share.

![](IMAGES/18.png)

![](IMAGES/19.png)

Finally, I executed the payload through the web application and got a reverse shell.

![](IMAGES/20.png)

![](IMAGES/21.png)

I then captured the user flag from *sign*'s Desktop.

![](IMAGES/22.png)

# PRIVILEGE ESCALATION

I then viewed the internal shares and found another interesting share , `Installs$` that I had missed.

![](IMAGES/23.png)

I visited the share and found some interesting files.

![](IMAGES/24.png)

The `Install_www_and_deploy.bat` file seemed unusual so I read its contents and found *administrator*'s password.

![](IMAGES/25.png)

I also looked for saved credentials in the windows logon registry hives and found the password for the user *sign*.

![](IMAGES/26.png)
![](IMAGES/27.png)

The room had a task that required us to find the password of *ultravnc*; I found this password in the configuration file of **ultravnc** stored under `C:\Program Files\uvnc bvba\UltraVNC`.

![](IMAGES/28.png)

![](IMAGES/29.png)

This seemed to be encoded. So I downloaded the appropriate decoder from: https://aluigi.altervista.org/pwdrec.htm

![](IMAGES/30.png)

![](IMAGES/31.png)

This decoder required the configuration file to be passed as argument, so I uploaded it on the target using the `images$` share.

![](IMAGES/32.png)

I then ran the decoder with the configuration file as the argument and found the password.

![](IMAGES/33.png)

While enumeration, I also found that I had `SeImpersonatePrivilege` enabled, I could use this to get **NT AUTHORTIY** access.

![](IMAGES/34.png)

To exploit this privilege, I would require the **printspoofer** payload. So I uploaded it on the target using the smb share.

https://github.com/itm4n/PrintSpoofer

![](IMAGES/35.png)

I then used **Printspoofer** to spawn a new shell as **nt authority\system**.

![](IMAGES/36.png)

Finally, I captured the admin flag from *administrator*'s Desktop.

![](IMAGES/37.png)

---
