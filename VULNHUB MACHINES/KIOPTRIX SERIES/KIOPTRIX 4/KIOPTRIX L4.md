# GETTING STARTED

To download Kioptrix level 4, click [here](https://www.vulnhub.com/entry/kioptrix-level-13-4,25/)

**DISCLAIMER**
> This writeup documents the steps that successfully led to pwnage of the machine. It does not include the dead-end steps encountered during the process (which were numerous). I recommend attempting to solve the lab independently. If you find yourself stuck on a phase for more than a day, you may refer to the writeups for guidance. Please note that this is just one approach to capturing all the flags, and there are alternative methods to solve the machine.

# RECONNAISSANCE

I conducted a network scan to identify the target.

```bash

┌──(root㉿kali)-[~/ctf/kioptrix-4]
└─# nmap -sn 192.168.1.0/24                              
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-11 13:38 EDT
Nmap scan report for RTK_GW (192.168.1.1)
Host is up (0.0073s latency).
MAC Address: F8:C4:F3:D0:63:13 (Shanghai Infinity Wireless Technologies)
Nmap scan report for 192.168.1.17
Host is up (0.00052s latency).
MAC Address: 00:0C:29:39:EC:5E (VMware)
Nmap scan report for kali (192.168.1.12)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 5.54 seconds
```

Now that I know the target is *192.168.1.17*, I use an **nmap** aggressive scan to discover the open ports and services running on it.

![[IMAGES/1.png]]

# INITIAL ACCESS

I access port 80 and reach a login panel.

![[IMAGES/2.png]]

In the background, I also use **ffuf** to fuzz the web directories for more information.

![[IMAGES/3.png]]

The *database.sql* file looks interesting, so I access it to gather more information.

![[IMAGES/4.png]]

I found a table name, username, and a potential password in the file. I then try these credentials on the login page.

![[IMAGES/5.png]]

It fails, so I try another way to bypass the authentication. Adding a **`'`** in the *password* field results in an error, confirming the presence of an SQL injection vulnerability.

![[IMAGES/6.png]]

Therefore, I use the following payload and log into the system with the username *john*.

```mysql

1234'or''=''#
```

![[IMAGES/7.png]]

I successfully obtained this user's credentials.

The **nmap** scan also revealed an SMB service running, so I use **enum4linux** to gather information about it.

```bash

enum4linux 192.168.1.17
```

![[IMAGES/8.png]]

I discovered a few more users, so I try logging in with their credentials.

| user   | password              |
| ------ | --------------------- |
| john   | MyNameIsJohn          |
| robert | ADGAdsafdfwt4gadfga== |

Now that I have these credentials, I use **ssh** to establish a connection with the target.

![[IMAGES/9.png]]

The shell I get when I log in is *rbash*. So I search online for rbash escapes using the **echo** command.

I then use the following payload to break out of the rbash.

```bash

echo os.system('/bin/bash')
```

![[IMAGES/10.png]]

With this, I have gained initial access to the system.

# PRIVILEGE ESCALATION

I downloaded the [**linux smart enumeration**](https://github.com/diego-treitos/linux-smart-enumeration) script from GitHub and created a file named **lse.sh** on the target machine with the script's code. I also gave it executable permission using `chmod +x lse.sh`.

![[IMAGES/11.png]]

The script revealed that I could connect to MySQL as root without a password. Therefore, I looked for services running as root.

![[IMAGES/12.png]]

I use `grep -v "]"` to exclude internal system services when searching for services running as root, simplifying the results. This confirms that MySQL is not running with a service user as it normally should but as root. So I log into the database:

![[IMAGES/13.png]]

I looked into the *members* database and found the credentials of Robert and John.

![[IMAGES/14.png]]

Since I am running as root, I can use built-in functions like **load_file** to read system files.

```mysql

select load_file('/etc/passwd');
```

![[IMAGES/15.png]]

Inside the *mysql* database, I found a table with functions that could be interesting.

![[IMAGES/16.png]]

The **sys_exec** function seemed interesting, so I tried it.

![[IMAGES/17.png]]

![[IMAGES/18.png]]

So, it can be used to execute commands.

I execute the following command to add an SUID bit to the **bash** shell.

![[IMAGES/19.png]]

![[IMAGES/20.png]]

Now that I am root, I can capture the flag located in the */root* directory.

![[IMAGES/21.png]]

# CLOSURE

I gained access to Kioptrix 4 by following these steps:
- First, I explored the web page and discovered a file named *database.sql*.
- Inside this file, I found a user named **john**.
- Using **enum4linux**, I uncovered another user named **robert**.
- By exploiting a SQL injection vulnerability in the password field on the login page, I bypassed authentication and obtained passwords for both users.
- Although I initially accessed the target via **ssh** using these credentials, I ended up in a restricted shell.
- I escaped this restricted shell and ran a script called **lse**.
- Through this script, I discovered that the MySQL service was accessible with the **root** user and a blank password.
- Upon logging into the SQL server, I found a user-defined function that allowed me to execute system commands.
- Because the MySQL service was running as **root**, I had the privilege to execute any command.
- To further elevate privileges, I added a special permission (SUID bit) to the **bash** shell.
- Subsequently, I reconnected, escaped the restricted shell, and executed `bash -p` to gain root access.

Given the freedom to execute commands, there are numerous other methods to achieve root access, such as:
- Modifying the **sudoers** file.
- Adjusting the **passwd** file to change the user ID (**uid**) of **john** to 0.
- Adding your SSH keys to **authorized_keys** for additional access.

![[IMAGES/22.png]]

That's it from my side, Happy Hacking :)

------------------------------------------------------------------------------------
