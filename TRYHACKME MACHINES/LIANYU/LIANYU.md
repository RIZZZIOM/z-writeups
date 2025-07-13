- https://tryhackme.com/room/lianyu

# SCANNING

I scanned the target using **nmap** to find its open ports, services etc.

![](IMAGES/1.png)

# FOOTHOLD

Since the target was running a web application, I fuzzed it for hidden directories and found an interesting endpoint.

![](IMAGES/2.png)

I visited the endpoint and felt that the information on the page was incomplete.

![](IMAGES/3.png)

So, I viewed the source code and found a potential username/password.

![](IMAGES/4.png)

I then fuzzed for hidden directories inside the newly discovered endpoint and found another endpoint.

![](IMAGES/5.png)

Upon visiting the page, I viewed the source code and found an interesting comment left by the developer.

![](IMAGES/6.png)

The comment said we could avail our `.ticket`... Maybe, there could be a file or directory on this endpoint with the `.ticket` extension.

![](IMAGES/7.png)

So, I fuzzed for `.ticket` endpoints inside the hidden directory.

![](IMAGES/8.png)

I accessed the newly discovered endpoint and found an encoded piece of string.

![](IMAGES/9.png)

I tried various methods to decode the string and successfully decoded it when I used the *From Base58* decoder from **cyberchef**.

![](IMAGES/10.png)

This looked like a password so I checked if this could be used with the username that I had found earlier on the other services running on the target i.e **ssh** or **ftp**. The credentials worked with **ftp*.

![](IMAGES/11.png)

I connected to the ftp server and listed the available files.

![](IMAGES/12.png)

It contained some images so I downloaded them onto my local system.

![](IMAGES/13.png)

The `leave_me_alone.png` file seemed to have some problem.

![](IMAGES/14.png)

The exif data confirmed that there was a file format error.

![](IMAGES/15.png)

I uploaded the image in a hex editor and found that the magic header numbers were incorrect.

![](IMAGES/16.png)

![](IMAGES/17.png)

I fixed the headers and downloaded the image.

![](IMAGES/18.png)

However, the image contained nothing interesting.

![](IMAGES/19.png)

I then tried extracting data from `aa.jpg` and found out it contained a zip file. In uncompressed the file and found 2 files.

![](IMAGES/20.png)

One file contained a password while the other had some kind of a note.

![](IMAGES/21.png)

At this point, I tried tried fuzzing spraying this password with the potential usernames that I had found so far, however, nothing worked. I went back to the **ftp** server and listed the hidden files as well to find an interesting hidden file called *`.other_user`*.

![](IMAGES/22.png)

I downloaded the file on my local system.

![](IMAGES/23.png)

The file contained a bunch of potential usernames.

![](IMAGES/24.png)

I tried the password with these usernames aswell and found a valid ssh credential.

![](IMAGES/25.png)

I connected to the machine using the discovered credentials through **ssh**.

![](IMAGES/26.png)

Finally, I captured the user flag from my home directory.

![](IMAGES/27.png)

# PRIVILEGE ESCALATION

I then listed my **sudo** privileges and found that I was allowed to run the **pkexec** binary as root without password.

![](IMAGES/28.png)

I referred to  **gtfobins** and found a way to exploit this to get root access.

![](IMAGES/29.png)

I executed the commands and gained root access on the target.

![](IMAGES/30.png)

I then captured the root flag from the root user's home directory.

![](IMAGES/31.png)

That's it from my side!
Until next time :)

---
