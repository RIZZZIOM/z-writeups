Link to machine : https://tryhackme.com/room/yueiua

# RECONNAISSANCE

I performed an **nmap** aggressive scan to find open ports and the services running on them.

![](IMAGES/1.png)

# FOOTHOLD

The **nmap** scan revealed a web application running so I accessed it through my browser.

![](IMAGES/2.png)

There was a form field so I analyzed the server response by entering test data.

![](IMAGES/3.png)

![](IMAGES/4.png)

I then used **ffuf** to find hidden directories on the web app.

![](IMAGES/5.png)

I accessed the newly discovered directory but found nothing.

![](IMAGES/6.png)

I then found hidden files using **ffuf**.

![](IMAGES/7.png)

I accessed the files and found nothing at first. However, when I tried passing command through common variables on *index.php*, I received a url base64 encoded response.

![](IMAGES/8.png)

![](IMAGES/9.png)

![](IMAGES/10.png)

![](IMAGES/11.png)

![](IMAGES/12.png)

Hence, I was able to execute os commands on the target. I viewed the source code of *index.php* using this.

![](IMAGES/13.png)

![](IMAGES/14.png)

I then checked if the machine had **netcat** so that I could try and initiate a reverse shell connection.

![](IMAGES/15.png)

![](IMAGES/16.png)

I then visited **revshells** and copied an **nc mkfifo** command to get a reverse shell. Upon execution, I received a shell on my **netcat** listener.

![](IMAGES/17.png)

![](IMAGES/18.png)

![](IMAGES/19.png)

After spawning a pty shell, I found a passphrase that was base64 encoded.

![](IMAGES/20.png)

Decoding it revealed a password.

![](IMAGES/21.png)

I found the user from the */home* directory and tries switching to it using the password.

![](IMAGES/22.png)

However, I failed.

![](IMAGES/23.png)

I then looked deeper and found some images inside the *assets* directory.

![](IMAGES/24.png)

I downloaded the images on my local system and viewed their file type.

![](IMAGES/25.png)

*oneforall.jpg* seemed to have some contents so I viewed its exif data.

![](IMAGES/26.png)

The file had an extension of **jpg** but the file type shown was **png**. So I loaded the file in an online hex editor and viewed the magic headers.

![](IMAGES/27.png)

![](IMAGES/28.png)

![](IMAGES/29.png)

The image had the magic header bytes of **png** type.

![](IMAGES/30.png)

I switched the file headers and downloaded the new image file.

![](IMAGES/31.png)

Everything seemed fine now.

![](IMAGES/32.png)

Finally, I tried extracting data from the image. I used the base64 decoded password that I had found in the passphrase.txt file as the password.

![](IMAGES/33.png)

I had found the credentials of *deku* so I logged in using **ssh**.

![](IMAGES/34.png)

I captured the user flag from *deku*'s home flag.

![](IMAGES/35.png)

# PRIVILEGE ESCALATION

I looked at my **sudo** privileges and found I was allowed to execute a bash script. I read the bash script and found it allowed us to execute commands.

![](IMAGES/36.png)

In Bash, the `eval` command is used to evaluate and execute a string as a shell command.

![](IMAGES/37.png)

I executed the script and added a new rule in the **sudoers** file allowing my current user to execute all commands as **sudo** without a password.

![](IMAGES/38.png)

I verified the changes by viewing my **sudo** privileges.

![](IMAGES/39.png)

I then executed **bash** as **sudo** and got shell as root. Finally I captured the root flag from */root* directory.

![](IMAGES/40.png)

Happy hacking !

---
