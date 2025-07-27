To access the machine, click on the link given below:
- https://tryhackme.com/room/glitch

# SCANNING

I performed an **nmap** aggressive scan to identify open ports and the services running on the target.

![](IMAGES/1.png)

# FOOTHOLD

I visited the website running on the target and found nothing interesting at first.

![](IMAGES/2.png)

It's source code revealed an interesting endpoint.

![](IMAGES/3.png)

I used **Burp**'s **Repeater** to then send a `GET` request to the endpoint and received a base64 encoded token.

![](IMAGES/4.png)

I used the **Decoder** tab to then decode this token and added this as the cookie value on the web page.

![](IMAGES/5.png)

Refreshing the site now revealed the actual web content.

![](IMAGES/6.png)

However, again there was nothing special.

![](IMAGES/7.png)

The source code of this page used a **javascript** file which could contain new endpoints.

![](IMAGES/8.png)

I viewed the file and found another endpoint.

![](IMAGES/9.png)

Then I used **Repeater** to send a request to this endpoint and got another list of items.

![](IMAGES/10.png)

I then switched the HTTP method to **POST** and received something unusual, maybe we could send some value through an argument...

![](IMAGES/11.png)

I used **ffuf** and found the argument that could be used to send the value.

![](IMAGES/12.png)

Next I sent a value with the argument and received an interesting message.

![](IMAGES/13.png)

When I switched the number value to an alphabet, the application threw an error. The error was thrown by the **eval** function of **node.js**.

![](IMAGES/14.png)

A simple **google** search revealed a way to exploit this and execute our commands.

![](IMAGES/15.png)

![](IMAGES/16.png)

I then used **revshells** to generate a reverse shell payload.

![](IMAGES/17.png)

Finally, I started a **netcat** listener and exploited the vulnerability to get a reverse shell.

![](IMAGES/18.png)

After gaining shell, I stabilized it.

![](IMAGES/19.png)

Finally, I captured the user flag from *user*'s home directory.

![](IMAGES/20.png)

# PRIVILEGE ESCALATION : 1

I checked for binaries with **SUID** bits. I noticed the **pkexec** binary and thought of giving the **PwnKit** exploit a try.

![](IMAGES/21.png)

I downloaded the exploit code on the target system and provided it executable permission.

![](IMAGES/22.png)

Upon executing it, I received root access.

![](IMAGES/23.png)

I then captured the root flag from *root*'s home directory.

![](IMAGES/24.png)

# PRIVILEGE ESCALATION : 2

The exploitation of **pkexec** was most likely an unintended way of gaining root access. So I again listed the binaries with **SUID** bits and this time, used the `doas` binary for privilege escalation.

![](IMAGES/25.png)

Before using the `doas` binary, I decided to go through the existing information in my directory. I found a folder for **firefox**. This folder could contain user credentials.

![](IMAGES/26.png)

When I listed the contents of the directory, I found the credential files:
- **key4.db**
- **logins.json**

![](IMAGES/27.png)

I transferred both the files using **netcat**.

![](IMAGES/28.png)

![](IMAGES/29.png)

![](IMAGES/30.png)

![](IMAGES/31.png)

![](IMAGES/32.png)

After transferring both the files to my local system, I used the **firepwd** tool to decrypt and reveal any passwords stored in them.
- https://github.com/lclevy/firepwd

![](IMAGES/33.png)

![](IMAGES/34.png)

![](IMAGES/35.png)

![](IMAGES/36.png)

The files revealed *v0id*'s password. So, I used it to switch to the *v0id* user.

![](IMAGES/37.png)

After switching to *v0id*, I ran the `doas` binary to spawn a **bash** shell as *root*.

![](IMAGES/38.png)

![](IMAGES/39.png)

---
