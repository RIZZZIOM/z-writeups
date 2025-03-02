# DVWA SETUP ON WINDOWS

| RESOURCE | LINK                                        |
| -------- | ------------------------------------------- |
| DVWA     | https://github.com/digininja/DVWA           |
| XAMPP    | https://www.apachefriends.org/download.html |

1. Visit the **XAMPP** page and download the installer for your system.

![[IMAGES/1.png]]

2. Run the installer and make sure you mark the **Apache** and **MySQL** service for installation.

![[IMAGES/2.png]]

3. The **xampp control panel** will pop up.

![[IMAGES/3.png]]

4. **Apache Server Configuration**
- Click on *Config* --> *Apache (httpd.conf)*

![[IMAGES/4.png]]

- Change the listening port to **8000**

![[IMAGES/5.png]]

- Save and close it. Click on *config* --> *Apache (httpd-ssl.conf)*

![[IMAGES/6.png]]

- Change the listening port to **4433**

![[IMAGES/7.png]]

- Save and close it. Click on *config* --> *PHP (php.ini)*

![[IMAGES/8.png]]

- Ensure the following values are turned on

![[IMAGES/9.png]]

- In the same file, search for **;extension=gd** and remove the **;** from the start.

![[IMAGES/10.png]]

- Finally save and close the file.

5. Download the **dvwa** zip file from the Github link and extract it. Rename the folder to **dvwa**.
6. Open the folder and go inside the **config** folder.

![[IMAGES/11.png]]

7. Rename the file to **config.inc.php**.
8. Open this file and set the following values.

![[IMAGES/12.png]]

9. Save and close the file.
10. Finally copy the main folder and paste it in **C:\xampp\htdocs** ( inside the xampp\htdocs) folder. This directory contains files that need to be up on the website.

![[IMAGES/13.png]]

11. Run the **Apache** and **MySQL** service on the **xampp** control panel and go to your browser and visit **`http://127.0.0.1/dvwa`**

![[IMAGES/14.png]]

![[IMAGES/15.png]]

12. Click on **Create / Reset Database** and log in using the credentials **admin** | **password**.

![[IMAGES/16.png]]

*DVWA* is up and running. Happy Hacking :)

------------------------------------------------------------------------------------

# DVWA SETUP ON LINUX

1. Move to `/var/www/html` and download *DVWA* from github

```bash

cd /var/www/html/
git clone https://github.com/digininja/DVWA
```

2. For convenience, rename the folder from *DVWA* to *dvwa* for ease of use.

```bash

mv DVWA dvwa
```

3. Set read, write and execute permissions to the *dvwa* directory.

```bash

chmod -R 777 dvwa/
```

4. Set up username and password to access the database:
i. go to the *config* folder and change the filename from *config.inc.php.dist* to *config.inc.php* 

```bash

cd dvwa/config
cp config.inc.php.dist config.inc.php
```

ii. edit the php file and set the *db_user='username_of_your_choice'* and *db_password='password_of_your_choice'*

```bash

vim config.inc.php
```

5. Install *MySQL* on your system.

```bash

apt install default-mysql-server
```

6. Configure the *MySQL* database
i. Start the *MySQL* server and check if it is running

```bash

service mysql start
systemctl status mysql
```

ii. Log into the *MySQL* server using super user

```bash

mysql -u root -p
```

iii. Create a new database and user with the username and password configured in the *dvwa* configuration file.

```mysql

create database dvwa;
create user 'your_username'@'127.0.0.1' identified by 'your_password'
```
be sure to replace the *your_username* and *your_password* with the username and password set in the config.inc.php file.

iv. Grant this user permission over the *dvwa* database

```mysql

grant all privileges on dvwa.* to 'your_username'@'127.0.0.1' identified by 'your_password'
```

7. Edit the *php.ini* file in *apache2* and make *Allow_url_include=On* and *Allow_url_fopen=On*. Also change `;extension=gd` to `extension=gd`.

```bash

vim /etc/php/8.2/apache2/php.ini
```

8. Restart the *apache* and *MySQL* services

```bash

service apache2 restart
service mysql restart
```

9. Visit **`http://localhost/dvwa`** on your browser and click on *create/reset database* 

![[IMAGES/15.png]]

10. Login using the username and password set by you in the *config.inc.ini* file.

![[IMAGES/16.png]]

------------------------------------------------------------------------------------
