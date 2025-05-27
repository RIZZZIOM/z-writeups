To access the machine, click on the link given below:
- https://tryhackme.com/room/dreaming

# RECONNAISSANCE

I performed an **nmap** aggressive scan to find open ports and the services running on them.

![](IMAGES/1.png)

# CAPTURING THE FLAGS

The target only had **ssh** and **http** running, so I accessed the web server through my browser.

![](IMAGES/2.png)

The server had a default Apache landing page. So I fuzzed for hidden directories using **ffuf**.

![](IMAGES/3.png)

The endpoint had a directory listing.

![](IMAGES/4.png)

I used **searchsploit** to look for exploits related to the CMS and found an interesting exploit that could be used if I had some credentials.

![](IMAGES/5.png)

I clicked on *admin* and was prompted to log in.

![](IMAGES/6.png)

I tried using common passwords and logged in using *'password'*.

![](IMAGES/7.png)

![](IMAGES/8.png)

![](IMAGES/9.png)

After logging in, I downloaded the exploit on my local system and viewed it to understand its usage.

![](IMAGES/10.png)

The exploit required the target IP, port, password and path to the CMS.

![](IMAGES/11.png)

Hence I ran the exploit by giving it the required parameters.

![](IMAGES/12.png)

I accessed the uploaded shell through my browser.

![](IMAGES/13.png)

I verified if the target had **netcat** and got a reverse shell.

![](IMAGES/14.png)

![](IMAGES/15.png)

![](IMAGES/16.png)

![](IMAGES/17.png)

After getting a reverse shell, I viewed the number of users present in the system.

![](IMAGES/18.png)

While exploring the file system, I found 2 python files that contained user credentials. The password of *death* was not visible but I found the password of another user called *lucien*.

![](IMAGES/19.png)

![](IMAGES/20.png)

I logged in as *lucien*.

![](IMAGES/21.png)

![](IMAGES/22.png)

![](IMAGES/23.png)

I then captured *lucien*'s flag.

![](IMAGES/24.png)

Listing **sudo** privileges revealed I was allowed to run a python script as the user *death*.

![](IMAGES/25.png)

I ran the script to see what it does.

![](IMAGES/26.png)

I had also found a script with the same name in the `/opt` directory. Examining the script revealed that there was a database named library that had a table that contained 2 columns. Both were printed on our terminal.

![](IMAGES/27.png)

*Lucien*'s bash history also had the **mysql** password.

![](IMAGES/28.png)

So I accessed the **mysql** server and viewed the table that was being used by the *getDreams.py* script.

![](IMAGES/29.png)

![](IMAGES/30.png)

![](IMAGES/31.png)

I wanted to substitute the value of the *dream* column with a command's execution. So I looked for ways I could do it on google.

![](IMAGES/32.png)

After finding a way to substitute values, I used a revshells script to get a reverse shell as the use *death*.

![](IMAGES/33.png)

![](IMAGES/34.png)

![](IMAGES/35.png)

![](IMAGES/36.png)

After getting a shell as *death*, I captured *death*'s flag.

![](IMAGES/37.png)

I then viewed the python script and found *death*'s password.

![](IMAGES/38.png)

![](IMAGES/39.png)

I now only needed to find the flag of morpheus. So I viewed that user's files and found a pythons script.

![](IMAGES/40.png)

Since it was a python script that imported libraries, I looked for writable files inside the `/usr/` directory hoping to find something interesting and found I had write permissions on **shutil.py**.

![](IMAGES/41.png)

I copied a python reverse shell payload and replaced the contents of **shutil.py** with it. I then started a **netcat** listener.

![](IMAGES/42.png)

![](IMAGES/43.png)

After some time, I received a reverse shell.

![](IMAGES/44.png)

Finally, I captured *morpheus*'s flag

![](IMAGES/45.png)

That's it from my side!
Until next time :)

---
