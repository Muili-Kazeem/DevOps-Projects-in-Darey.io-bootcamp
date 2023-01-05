# WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS


## Preparing Prerequisites
  * Created an AWS account in order to set up an EC2 VM instance with Ubuntu Server OS
  * Set and spin up a new EC2 instance of t2.micro family with Ubuntu Server 20.04 LTS
  * Connected through SSH from my windows terminal into the remote AWS VM instance using the pem key downloaded upon spinning up my VM instance
  ![Screenshot (143)](https://user-images.githubusercontent.com/69347415/193482539-1bec2cfa-488f-4281-8ab9-4da4fd8a15e8.png)


## STEP 1 — Installing Apache and updating the Firewall
  * Updated packages in package manager `sudo apt update`
  * Installed Apache2 `sudo apt install apache2`
  * Verified that Apache2 is running as a Service in the OS, using `sudo systemctl status apache2`
  * To receive any traffic on the Web Server, I exposed TCP port 80 by editing the inbound rules of the Web Server to accept traffic from any IP address
  * Then I was able to access the Web Server locally and through my web browser using my AWS VM instance Public IP address
![Screenshot (144)](https://user-images.githubusercontent.com/69347415/193483011-dc93c463-5c56-4561-a2e6-10f0179177f5.png)
![Screenshot (145)](https://user-images.githubusercontent.com/69347415/193483015-ba8c7a59-8275-4028-9a2a-06298d2c5837.png)
![Screenshot (146)](https://user-images.githubusercontent.com/69347415/193483047-862adefa-ed01-4bac-89a9-69ed7d0322f9.png)


## STEP 2 - Installing MySQL
  * Acquired and installed MySql package `sudo apt install mysql-server`
  * Logged into MySQL console using `sudo mysql`
  * Set password for the root user using mysql_native_password as default authentication method `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';` after which I exited the MySQL console
  * Ran a security script that came pre-installed with MySQL `sudo mysql_secure_installation`
  * but I did not enable the Validate Password Plugin after which I confirmed password for the MySQL root user
  * Tested and logged in to the MySQL console using `sudo MySQL -p`
  * Exited the MySQL concole after
 ![Screenshot (147)](https://user-images.githubusercontent.com/69347415/193484609-44e0c71b-e44f-4e0e-a17f-9691e193e871.png)
![Screenshot (148)](https://user-images.githubusercontent.com/69347415/193484145-ef561ade-cda8-44d8-8ae0-7980a22279a5.png)


## Installing PHP
  * Installed Php alongside with additional packages such as php-mysql and libapache2-mod-php using the command `sudo apt install php libapache2-mod-php php-mysql`\
  * Check and confirmed for the version of PHP using `php -v`
![Screenshot (149)](https://user-images.githubusercontent.com/69347415/193484345-7de790eb-22e1-41ab-98d0-2168ade33627.png)
![Screenshot (150)](https://user-images.githubusercontent.com/69347415/193484759-76cf2909-523c-46e2-a8af-b47b8f8725c8.png)

      
## Creating a Virtual Host for the Website Using Apache
  * created a directory for the website on the server using `sudo mkdir /var/www/projectlamp`. ProjectLamp is the name of the website by the way
  * Assigned directory ownership tp current system user `sudo chown -R $USER:$USER /var/www/projectlamp`
  * Created and opened a new config file in Apache’s sites-available directory using vim as my editor `sudo vim /etc/apache2/sites-available/projectlamp.conf`
  * Set the editor to insert mode and included my config code as seen below
  ```
  <VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>
```
  * Saved the config file and exited the vim editor
  * Used a2ensite command to enable the new virtual host with the command `sudo a2ensite projectlamp`
  * Also disabled Apache’s default website by using a2dissite command `sudo a2dissite 000-default`
  * Ran `sudo apache2ctl configtest` to ensure the config file doesn't contain syntx error. Well, at first it did, so I went back to the editor to correct the errs.
  * TO effect the changes, I reload Apache using `sudo systemctl reload apache2`
  * Created an index.html file in that location so that I can test that the virtual host works as expected `sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html`
![Screenshot (151)](https://user-images.githubusercontent.com/69347415/193485138-5d8529f7-d5fc-4496-994e-71fa85e96e55.png)


## Enable PHP on the website
  * With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file.
  * To change this behaviour, I used vim to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive `sudo vim /etc/apache2/mods-enabled/dir.conf`
  * The config code I used is seen below
```
<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
  * I reloaded Apache to effect the changes `sudo systemctl reload apache2`
  * Created an index.php test script inside the web root directory to confirm that Apache is able to handle and process requests for PHP files `vim /var/www/projectlamp/index.php`
  * The code inside the script is as seen below 
```
<?php
phpinfo();
```
  * Then I refreshed my server page on the browser
  * After examining the changes, I removed the file I created to prevent disclosing sensitive server information `sudo rm /var/www/projectlamp/index.php`
![Screenshot (153)](https://user-images.githubusercontent.com/69347415/193486009-a2c0a99a-d38e-4e59-b5a6-0031ab64ae83.png)
![Screenshot (152)](https://user-images.githubusercontent.com/69347415/193485274-35dffca7-92fe-4027-a5a8-546777228438.png)

