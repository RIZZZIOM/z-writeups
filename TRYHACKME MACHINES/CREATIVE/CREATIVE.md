
To access the machine, click on the link given below:
https://tryhackme.com/room/creative

# SCANNING

I performed an **nmap** aggressive scan on the target to identify open ports and the services running on them.

![](IMAGES/1.png)

# FOOTHOLD

I was able to discover a web server running so I added the IP in my `/etc/hosts` file and accessed the web server through my browser.

![](IMAGES/2.png)

I tried looking for files, directories but found nothing. I then enumerated subdomains and found 1.

![](IMAGES/3.png)

After adding the subdomain in the **`hosts`** file, I accessed it and found an interesting feature. It allowed us to send request to a website to see if it is active.

![](IMAGES/4.png)

I started an **http** server on my local system and tried accessing it through the application.

![](IMAGES/5.png)

![](IMAGES/6.png)

![](IMAGES/7.png)

Since I was able to make the server send requests, I tried using it to execute php, js, python payloads but none of them seemed to work. The application simply displayed the contents of the files. Out of curiosity, I tried accessing the localhost and found the rendered html of the domain.

I then fired up Burp and tried analyzing it in an efficient manner.

![](IMAGES/8.png)

So, if the page existed, I would get the rendered contents of the page else, I would receive an error. I exploited this behavior to scan internal ports using intruder.

![](IMAGES/9.png)

![](IMAGES/10.png)


![](IMAGES/11.png)

I found port 1337 to be hosting the root directory.

![](IMAGES/12.png)

I was then able to access the user flag from *saad*'s home directory.

![](IMAGES/13.png)

![](IMAGES/14.png)

I also found `saad`'s **ssh** keys and copied the private key onto my local system.

![](IMAGES/15.png)

I tried logging in using the private key but was prompted for a passphrase. So, I converted the key to **john** crackable format and cracked it using **john** the ripper.

![](IMAGES/16.png)

![](IMAGES/17.png)

Finally, I used the passphrase to log in as *saad*.

![](IMAGES/18.png)

# PRIVILEGE ESCALATION

I examined the contents of *saad*'s home directory and found the **.bash_history** file which could hold command history. I viewed the file and found out *saad*'s password.

![](IMAGES/19.png)

I then looked for my **sudo** privileges and found the following

![](IMAGES/20.png)

The **ping** permission in itself wasn't anything special. However, the environment variable `env_keep+=LD_PRELOAD` was something that could be exploited. I could make the program load a library of my choice before running the `ping` command as sudo.

I referred to the below article for reference.

![](IMAGES/21.png)

I created a C code to spawn a **bash** shell and converted it into a library.

![](IMAGES/22.png)

Finally, I used the environment variable to load my binary before executing the **ping** command as **sudo** and spawned a **bash** shell as **root**.

![](IMAGES/23.png)

I then captured the root flag from **`/root`**

![](IMAGES/24.png)

---

