To access the machine, click on the link given below:
- https://tryhackme.com/room/jurassicpark

# SCANNING

I performed an **nmap** aggressive scan to find open ports, service info and script scan results on the target.

![](IMAGES/1.png)

The target had only 2 ports open:
- Port 80 : http
- Port 22 : ssh

# FOOTHOLD

I accessed the web application through my browser.

![](IMAGES/2.png)

I clicked on the *online shop* and was given an option to select a package.

![](IMAGES/3.png)

I clicked on a package and noticed that the package details was being fetched using the **`id`** parameter. I tried adding a **`'`** to see how the application behaved to it.

![](IMAGES/4.png)

It instantly triggered an error.

![](IMAGES/5.png)

Scrolling to the bottom of the page revealed a challenge.

![](IMAGES/6.png)

I then fuzzed for files in the web application and found *robots.txt* that could contain path to interesting endpoints.

![](IMAGES/7.png)

However, there was nothing.

![](IMAGES/8.png)

The only page left to inspect now was the *item.php* where an error was thrown upon sending a **`'`** in the **`id`** parameter. I viewed the source code and found that the target was running **MySQL** database.

![](IMAGES/9.png)

Since, I was dared to use **sqlmap**, I tried using it. However, it was taking a long time and wasn't giving the expected results.

![](IMAGES/10.png)

Hence, I switched to manual testing. I captured the request made for the package on **Burp Suite** and sent it to **Repeater**.

![](IMAGES/11.png)

Since **`'`** was causing an error, I added a true statement directly after the **id** value. 

The payload that I used:
```
+OR+1=1
```

There was a visible change in the response of the web app.

![](IMAGES/12.png)

When I sent a false statement, no results were found.

```
+AND+1=2
```

![](IMAGES/13.png)

I then had to find the number of columns. I used **`ORDER BY`** query for the same.

```
+ORDER+BY+1
```

![](IMAGES/14.png)

When I tried sorting the result based on column number 6, I received an error. Hence, I could conclude that the table being used had 5 columns.

```
+ORDER+BY+6
```

![](IMAGES/15.png)

I then used **`UNION`** to find columns that were returned to us on the web page.

```
UNION+SELECT+1,2,3,4,5
```

![](IMAGES/16.png)

My usual methods did not work and was being blocked so I searched online to see if there was any another way.

![](IMAGES/17.png)

I used the below query to find the name of the current database.

```
+UNION+SELECT+1,DATABASE(),3,4,5
```

![](IMAGES/18.png)

I then used the below query to find the version of the server

```
+UNION+SELECT+1,version(),3,4,5
```

![](IMAGES/19.png)

I then queried the table name from the current database.

```
UNION+SELECT+1,table_name+3,4,5+FROM+information_schema.tables+WHERE+table_schema=database()
```

![](IMAGES/20.png)

Then I queried the column name for the *users* table.

```
UNION+SELECT+1,column_name,3,4,5+FROM+information_schema.columns+WHERE+table_name="users"+AND+table_schema=database()
```

![](IMAGES/21.png)

I then extracted password from the table.

```
UNION+SELECT+1,password,3,password,5+FROM+users
```

![](IMAGES/22.png)

I found a password. The question on **Tryhackme** already provided a username called **Dennis**. So I checked if it was a valid **SSH** credential.

![](IMAGES/23.png)

I then sent the package request to **intruder** and added the payload marker at the `id` parameter.

![](IMAGES/24.png)

I set the payload sets to Number and gave a range from 0 to 50.

![](IMAGES/25.png)

When I ran the attack, I found out another valid **id** value that wasn't visible on the package selection page.

![](IMAGES/26.png)

This **id** revealed the same user that was specified on **TryHackMe**'s task question.

![](IMAGES/27.png)

At this point, I was a little lost, so I tried viewing all the columns that were present in the current table.

```
+UNION+SELECT+1,GROUP_CONCAT(column_name)+3,4,5+FROM+information_schema.columns+WHERE+table_name="users"+AND+table_schema=database()
```

![](IMAGES/28.png)

I retried the username and password against **ssh** and changed the letter case of the username...

![](IMAGES/29.png)

I used the username and password to log into the target using **ssh**.

![](IMAGES/30.png)

Finally, I captured first flag.

![](IMAGES/31.png)

# PRIVILEGE ESCALATION

I then looked at me **sudo** privileges and found that I was allowed to run **scp** as root.

![](IMAGES/32.png)

My directory also contained a *bash_history* file which would contain a log of commands entered on the terminal.

![](IMAGES/33.png)

Viewing the *bash_history* file revealed the third flag. I also got a hint of the location of the fifth flag.

![](IMAGES/34.png)

I copied the *shadow* file to see if it had the root password hash.

![](IMAGES/35.png)

![](IMAGES/36.png)

I then visited **GTFOBins** and found a way to exploit the **sudo** privileges on **scp**.

![](IMAGES/37.png)

I followed the method shown on the website and spawned a shell as root.

![](IMAGES/38.png)

I captured the fifth flag from */root* directory.

![](IMAGES/39.png)

Since, I had root access, I used the **find** command to search for the second flag.

![](IMAGES/40.png)

![](IMAGES/41.png)

I also found the location of the second flag inside *ubuntu* user's bash history file.

![](IMAGES/42.png)

![](IMAGES/43.png)

![](IMAGES/44.png)

I then captured flag two.

![](IMAGES/45.png)

> PS: There is no flag 4.

That's it from my side!
Until next time:)

---
