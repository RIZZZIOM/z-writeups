![[IMAGES/x.png]]
# GETTING STARTED

This machine contains 2 flags and our goal is to capture them both.

> [!NOTE] 
> This writeup documents the steps that successfully led to pwnage of the machine. It does not include the dead-end steps encountered during the process (which were numerous). This is just my take on pwning the machine and you are welcome to choose a different path.

| MACHINE    | IP           |
| ---------- | ------------ |
| kali       | 10.10.14.62  |
| twomillion | 10.10.11.221 |

# RECONNAISSANCE

I mapped the IP to the domain *2million.htb*.

```bash

vim /etc/hosts
```

![[IMAGES/1.png]]

I performed an **nmap** aggressive scan to find open ports and running services.

![[IMAGES/2.png]]

# INITIAL ACCESS

I accessed the page in the browser.

![[IMAGES/3.png]]

I read through the page source and found a reference to the */invite* page.

![[IMAGES/4.png]]

So I visited the page.

![[IMAGES/5.png]]

I used the developer tab and refreshed the page to inspect which files were being loaded. Here, I found a *JavaScript* file that had something to do with the invite code.

![[IMAGES/6.png]]

Hence, I double-clicked on the **inviteapi.min.js** file and found obfuscated JavaScript code. I used **chatgpt** to Deobfuscate it.

```javascript

function verifyInviteCode(code) {
    var formData = {"code": code};
    $.ajax({
        type: "POST",
        dataType: "json",
        data: formData,
        url: '/api/v1/invite',
        success: function(response) {
            console.log(response);
        },
        error: function(response) {
            console.log(response);
        }
    });
}

function makeInviteCode() {
    $.ajax({
        type: "POST",
        dataType: "json",
        url: '/api/v1/invite/generate/how/to',
        success: function(response) {
            console.log(response);
        },
        error: function(response) {
            console.log(response);
        }
    });
}
```

I used **curl** to fetch an invite code from the URL */api/v1/invite/generate/how/to*.

![[IMAGES/7.png]]

This code was URL encoded, so I decoded it using the **base64** command.

![[IMAGES/8.png]]

Finally, I entered this code on the */invite* page.

![[IMAGES/9.png]]
\
I registered using some fake credentials.

![[IMAGES/10.png]]

I looked around the website, and when I viewed the source code of the */access* page, I found an endpoint where a request was made.

![[IMAGES/11.png]]

To analyze the *API* path, I turned on **Burp Suite** and made a request to `http://2million.htb/api`.

![[IMAGES/12.png]]

It returned the version of the API. I then looked inside */api/v1*.

![[IMAGES/13.png]]

I saw a couple of paths that had *admin* in them. So I tried each one of them.

Upon sending a request to */api/v1/admin/vpn/generate* and */api/v1/admin/settings/update*, I got a different response code of **405**.

![[IMAGES/14.png]]

![[IMAGES/15.png]]

This indicated that the **GET** request wasn't allowed for this request. So I tried other requests on both the endpoints. I got a different response in */api/v1/admin/vpn/generate* when I tried the **POST** request.

![[IMAGES/16.png]]

Finally, when I tried accessing the */api/v1/admin/settings/update* using the **PUT** method, I got a status code of **200**.

![[IMAGES/17.png]]

I received a response saying "invalid content-type." So I tried adding the *content-type* as **application/json**.

![[IMAGES/18.png]]

Now I enter an email and resend the data.

![[IMAGES/19.png]]

I then added *is_admin* in the request.

![[IMAGES/20.png]]

Hence, I gained admin access as the *test* user.

![[IMAGES/21.png]]

I now tried to generate a VPN as admin.

![[IMAGES/22.png]]

I added the *username* parameter.

![[IMAGES/23.png]]

This returned a status code of *200*. Since the *username* value was being sent as JSON, I checked for command injection vulnerability by adding a command along with the username.

![[IMAGES/24.png]]

By injecting `;pwd;`, I found out the directory I was in.

![[IMAGES/25.png]]

I viewed the *database.php* and found that the credentials were fetched through environment variables.

![[IMAGES/26.png]]

Hence, I tried to view all the files in the */var/www/html* directory.

![[IMAGES/27.png]]

I found the **.env** file.

I read the file to view its contents.

![[IMAGES/28.png]]

I used the username and password to access the machine through **ssh**.

![[IMAGES/29.png]]

I found the first flag in the */home/admin* directory.

![[IMAGES/30.png]]

# PRIVILEGE ESCALATION

I viewed my mail in the */var/mail* directory.

![[IMAGES/31.png]]

I then found kernel info from **uname** and looked for the CVE mentioned in the mail.

![[IMAGES/32.png]]

![[IMAGES/33.png]]

Hence, I looked for ways to exploit this and found this **C** code: [https://github.com/xkaneiki/CVE-2023-0386](https://github.com/xkaneiki/CVE-2023-0386).

I checked if **gcc** and **wget** were present.

![[IMAGES/34.png]]

I then downloaded the script as a zip file on my system and transferred it to the target.

![[IMAGES/35.png]]

I unzipped the file and compiled the exploit.

![[IMAGES/36.png]]

![[IMAGES/37.png]]

![[IMAGES/38.png]]

![[IMAGES/39.png]]

Now I moved into the *root* directory and captured the last flag.

![[IMAGES/40.png]]

# CLOSURE

This was the first time I worked with an API, so this machine helped me learn several concepts. Here's a summary of how I captured both the flags:
1. I found the */invite* panel through the source code of the main website and used it to log in as a user.
2. After logging in, I navigated to the */access* tab. Using Burp Suite, I made requests at `/api/v1/admin/settings/update` to give my account *admin* access.
3. I then visited `/api/v1/admin/settings/update` and found the credentials to log in using **ssh**.
4. In the home directory of *admin*, I found the user flag.
5. I discovered a hint on how to escalate my privileges through an email in */var/mail*.
6. I used a kernel exploit to escalate my privileges.
7. Finally, I captured the final flag in the *root* directory.

![[IMAGES/41.png]]

------------------------------------------------------------------------------------