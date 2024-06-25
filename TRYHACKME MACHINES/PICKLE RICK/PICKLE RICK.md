![[IMAGES/x.png]]
# GETTING STARTED

> [!NOTE]
> To access the room, click on the link given below
> https://tryhackme.com/r/room/picklerick

To work on the machine through our locally installed Linux, I downloaded the **openvpn** configuration file from [here](https://tryhackme.com/r/access).

Finally, I ran the *openvpn* configuration file.

```bash

openvpn file.ovpn
```

# RECON

I performed an **nmap** aggressive scan to identify open ports and services running on the target.

![[IMAGES/1.png]]

# INGREDIENT 1

I visited the web server using a browser.

![[IMAGES/2.png]]

Upon inspecting the source code, I found a username.

![[IMAGES/3.png]]

I performed a **ffuf** scan to find other files on the web server.

![[IMAGES/4.png]]

I accessed the *login.php* and got a login panel.

![[IMAGES/5.png]]

The *robots.txt* file also contained some text.

![[IMAGES/6.png]]

I tried logging in using the username I had found on the home page along with this text and gained access to a command panel.

![[IMAGES/7.png]]

Using this panel, I executed **whoami** and received a response from the server.

![[IMAGES/8.png]]

However, the other pages were inaccessible.

![[IMAGES/9.png]]

I viewed the source code of the command panel but found nothing interesting.

![[IMAGES/10.png]]

I then viewed the contents inside my folder by executing **ls**.

![[IMAGES/11.png]]

When I tried to read the ingredient, I encountered an error.

![[IMAGES/12.png]]

So I tried other ways to read it. Since it was present in the directory my page was located, I accessed it through the URL. Alternatively, even the command **less Sup3rS3cretPickl3Ingred.txt** worked.

![[IMAGES/13.png]]

Hence, I obtained the first ingredient.

# INGREDIENT 2

I looked at the *clue.txt* file for hints.

![[IMAGES/14.png]]

I executed **grep -R " "** to view all the codes in the current directory.

![[IMAGES/15.png]]

Upon viewing the source, I discovered the commands that weren't allowed to be used.

![[IMAGES/16.png]]

Since **sudo** wasn't restricted, I viewed my *sudo privileges* using **sudo -l**.

![[IMAGES/17.png]]

Hence, I was allowed to execute **sudo** without a password.

I executed **ls ../../../**.

![[IMAGES/18.png]]

I then looked inside the home directory using **ls ../../../home**.

![[IMAGES/19.png]]

Then I looked inside *rick*.

![[IMAGES/20.png]]

Finally, I read the second ingredient using **less '../../../home/rick/second ingredient'**.

![[IMAGES/21.png]]

# INGREDIENT 3

For the final ingredient, I looked inside the *root* directory using **sudo ls ../../../root**.

![[IMAGES/22.png]]

I then read this ingredient using **sudo less '../../../root/3rd.txt'**.

![[IMAGES/23.png]]

# CLOSURE

Here's a summary of how I compromised the machine:
1. I collected login credentials through reconnaissance and used them to access the application.
2. Using the command panel, I retrieved the first ingredient from my current directory.
3. Similarly, I retrieved the second ingredient from the */home/rick* directory.
4. Finally, I obtained the final ingredient from the */root* directory using the **sudo** command.

![[IMAGES/24.png]]

That's it from my side, until next time :)

---