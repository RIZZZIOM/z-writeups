Link to machine : https://tryhackme.com/room/anthem

# RECONNAISSANCE

I performed an **nmap** aggressive scan to find open ports and the services running on them. It also ran default scripts on the services found.

![](IMAGES/1.png)

# FOOTHOLD

The **nmap** scan revealed an **http** server running on the target. So I accessed it from my browser.

![](IMAGES/2.png)

I used **ffuf** to bruteforce hidden files on the web app.

![](IMAGES/3.png)

The *robots.txt* file usually contains sensitive endpoints. So I accessed it and found a string. I saved the string as it could be used in the future.

![](IMAGES/4.png)

I continued analyzing the application and found an email address.

![](IMAGES/5.png)

![](IMAGES/6.png)

When I tried accessing the page of a specific author, I received an error. So I accessed the *authors* page and discovered the first flag.

![](IMAGES/7.png)

![](IMAGES/8.png)

The reviewed the source code of my home page and found another flag.

![](IMAGES/9.png)

I visited the rest of the endpoints that I had discovered from robots.txt and **ffuf** scan.

![](IMAGES/10.png)

I found a login panel at the *umbraco* endpoint.

![](IMAGES/11.png)

I was able to log in using the email id I found on the web page and using the string from *robots.txt* as password.

![](IMAGES/12.png)

Hence, I got access to a CMS admin panel.

![](IMAGES/13.png)

I viewed the source code and got another flag.

![](IMAGES/14.png)

I viewed the source code of other pages from here and found another flag.

![](IMAGES/15.png)

I checked if the credentials I used on the web app could be used to get **rdp** access using **nxc**.

![](IMAGES/16.png)

Since I had the valid credentials, I got **rdp** access on the target.

![](IMAGES/17.png)

![](IMAGES/18.png)

I captured the user flag from the user's desktop.

![](IMAGES/19.png)

# PRIVILEGE ESCALATION

I looked at the folders and files present (including hidden files) and found a backup folder inside the C drive.

![](IMAGES/20.png)

This folder contained a file called *restore.txt* that I could not access.

![](IMAGES/21.png)

![](IMAGES/22.png)

Hence, I modified the file's properties to allow the machine account to read the file.

![](IMAGES/23.png)

![](IMAGES/24.png)

![](IMAGES/25.png)

Finally I read the file and found a string. This string could be another password so I tried using it to log in as administrator.

![](IMAGES/26.png)

![](IMAGES/27.png)

After logging in as administrator, I captured the final flag from desktop.

![](IMAGES/28.png)

![](IMAGES/29.png)

That's it from my side !
Happy hacking :)

---
