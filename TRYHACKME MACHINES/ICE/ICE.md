To access the machine, click on the link given below:
https://tryhackme.com/room/ice

# SCANNING

I performed an **nmap** aggressive scan on the target to identify open ports and the services running on them.

![](IMAGES/1.png)

![](IMAGES/2.png)

![](IMAGES/3.png)

# FOOTHOLD

The **nmap** scan revealed an interesting service running on port 8000 so I accessed it.

![](IMAGES/4.png)

The room had the following link for reference:
https://www.cvedetails.com/cve/CVE-2004-1561/

![](IMAGES/5.png)

The **Icecast** service running on port 8000 seemed to be vulnerable to code execution. **Metasploit** contained an exploit that could be used for this. So I started the **metasploit** framework and selected the appropriate exploit.

![](IMAGES/6.png)

I configured the required options and ran the exploit to get a reverse meterpreter shell.

![](IMAGES/7.png)

![](IMAGES/8.png)

# PRIVILEGE ESCALATION

I used the `local_exploit_suggester` post module to look for privilege escalation vectors.

![](IMAGES/9.png)

![](IMAGES/10.png)

I then used a privilege escalation module and ran it to escalate my privilege.

![](IMAGES/11.png)

I was still running as Dark user but had admin privileges.

![](IMAGES/12.png)

![](IMAGES/13.png)

I then listed running processes in the target and found **spoolsv** to be running as NT Authority.

![](IMAGES/14.png)

I migrated to the process and got NT Authority access.

![](IMAGES/15.png)

I then loaded **mimikatz** using the **kiwi** extension of **metasploit**.

![](IMAGES/16.png)

I also dumped the Administrator hash using the **hashdump** command in meterpreter.

![](IMAGES/17.png)

---
