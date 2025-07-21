
This is a walkthrough for Evil-gpt v1 and Evil-gpt v2 ctf challenge on TryHackMe.

![](IMAGES/x.jpg)

# EVIL-GPT V1

You can access the machine by clicking on the link given below:
- https://tryhackme.com/room/hfb1evilgpt

I connected to the AI bot using **netcat**.

![](IMAGES/1.png)

The bot asked for a command, so I asked it to list the contents in my current working directory. I came up with the appropriate command and asked me if I wanted to execute it. I said yest and was able to view the contents.

![](IMAGES/2.png)

The bot allowed me to execute system commands, so I asked it to list files in the root directory in hopes of finding the flag.

![](IMAGES/3.png)

Since the root did not contain the flag, the next possible location would be the `/root` directory. Since access to that directory is restricted, I verified my current user.

![](IMAGES/4.png)

Since the bot was running as root, I could view the contents inside the `/root` directory where I found the flag.

![](IMAGES/5.png)

![](IMAGES/6.png)

# EVILGPT V2

> https://tryhackme.com/room/hfb1evilgptv2

This time, I was provided with a web based chatbot.

![](IMAGES/7.png)

When I asked the bot to reveal the flag, it said that revealing the flag violated its rules. So, I asked it to list down its rules where I discovered the flag.

![](IMAGES/8.png)

That's it from my side, until next time :)

---
