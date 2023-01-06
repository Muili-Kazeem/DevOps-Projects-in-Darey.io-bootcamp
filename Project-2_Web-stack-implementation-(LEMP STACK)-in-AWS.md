# WEB STACK IMPLEMENTATION (LEMP STACK) IN AWS


## Preparing prerequisites
  * Set and spin up a new EC2 instance of t2.micro family with Ubuntu Server 20.04 LTS
  * Connected through SSH from my windows terminal into the remote AWS VM instance using the downloaded pem key

## STEP 1: Installing the Nginx Server
  * Updated the VM instance package manager using `sudo apt update`
  * Installed Nginx using `sudo apt install nginx`
  * Verified that Nginx was successfully installed and running as a service on the Ubuntu VM instance running `sudo systemctl status nginx`
  * To receive any traffic on the Web Server, I exposed TCP port 80 by editing the inbound rules of the Web Server to accept traffic from any IP address after which I accessed the Web Server locally and on my web browser using my AWS VM instance Public IP address
![Screenshot (154)](https://user-images.githubusercontent.com/69347415/193548242-5ae787ad-75b9-4f77-bc4d-7c3f01e02ad3.png)
![Screenshot (155)](https://user-images.githubusercontent.com/69347415/193548317-2e99017e-0265-4f2f-b736-31c1e1a54f13.png)
![Screenshot (156)](https://user-images.githubusercontent.com/69347415/193548639-66380485-ce31-45ac-9365-ba77b130adfb.png)
![Screenshot (157)](https://user-images.githubusercontent.com/69347415/193548669-8b5e56f0-2bb7-4b20-98c3-e147aa55e687.png)
![Screenshot (158)](https://user-images.githubusercontent.com/69347415/193548721-0b01243c-5ca0-4d89-8e92-1e1a7ed0e349.png)

## STEP 2: Installing MySQL
  * Acquired and installed MySql package `sudo apt install mysql-server`
  * Logged into MySQL console using `sudo mysql`
  * Set password for the root user using mysql_native_password as default authentication method `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';` after which I exited the MySQL console
  * Ran a security script that came pre-installed with MySQL `sudo mysql_secure_installation` but I did not enable the Validate Password Plugin after which I confirmed password for the MySQL root user
  * Tested and logged in to the MySQL console using `sudo MySQL -p`
  * Exited the MySQL console after
![Screenshot (159)](https://user-images.githubusercontent.com/69347415/193549507-8d4f3722-9181-4a54-b33e-5c9c8b195189.png)
![Screenshot (160)](https://user-images.githubusercontent.com/69347415/193549614-90976b45-a49b-432f-b514-8d0f10cc7113.png)
![Screenshot (161)](https://user-images.githubusercontent.com/69347415/193549850-d5401c39-864c-48aa-baf2-497114e02c5e.png)
![Screenshot (162)](https://user-images.githubusercontent.com/69347415/193549968-70abccca-c0f1-4717-8652-d9ce06f27fd3.png)

## STEP 3: Installing PHP
  * Installed Php alongside with additional packages such as php-fpm and php-mysql using the command `sudo apt install php-fpm php-mysql`
  * Check and confirmed for the version of PHP using `php -v`
![Screenshot (163)](https://user-images.githubusercontent.com/69347415/193551303-6c87184e-327f-4fba-a444-08e38f41c0d4.png)

## STEP 4: Configuring Nginx to use PHP processor
  * Created the root web directory for my domain `sudo mkdir /var/www/projectLEMP`
  * Assigned ownership of the directory with the $USER environment variable, which referenced the current system user `sudo chown -R $USER:$USER /var/www/projectLEMP`
  * Opened a new config file in Nginx’s sites-available directory using nano editor with the command `sudo nano /etc/nginx/sites-available/projectLEMP`
  * The bare-bone config code is shown below
  ```
  #/etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```
  * Activated the configuration by linking to the config file from Nginx’s sites-enabled directory `sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`
  * Tested the configuration for syntax errors using the command `sudo nginx -t`
  * Also disabled default Nginx host that is currently configured to listen on port 80 by running `sudo unlink /etc/nginx/sites-enabled/default`
  * Reloaded Nginx to apply the changes with the command `sudo systemctl reload nginx`
  * Created an index.html file in web root directory so that I can test that the new server block works, with the command `sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`
  * Opened the website's URL using the VM instance Public IP address
![Screenshot (168)](https://user-images.githubusercontent.com/69347415/193561095-f5747f84-395c-4d64-afd7-335d22a3754f.png)
![Screenshot (167)](https://user-images.githubusercontent.com/69347415/193561958-319bbd7a-185c-40b0-a0b7-ed661fb5ac27.png)

## STEP 5: Testing PHP with Nginx
  * Created and opened an info.php file as a test file in the document root directory using vim editor `sudo nano /var/www/projectLEMP/info.php`
  * The PHP code in the test file is seen below
  ```
  <?php
  phpinfo();
  ```
  * Removed the test file using the command `sudo rm /var/www/your_domain/info.php`

## STEP 6: Retrieving data from MySQL Database with PHP
  * Connected to the MySQL console `sudo mysql`
  * Created a new database called 'example_database' with `CREATE DATABASE 'example_database';`
  * Created a new user named example_user, using mysql_native_password as default authentication method and the user’s password as 'password' `CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';`
  * Granted user permission over the example_database database with the command `GRANT ALL ON example_database.* TO 'example_user'@'%';`
  * Then I exited the MySQL console
  * Attempted logging in to the MySQL console again, but this time, using the custom user credentials: `mysql -u example_user -p`
  * After logging in, I accessed the databases using the SQL command `SHOW DATABASES;`
  * Created a test table named todo_list from the MySQL console with the SQL command below
```
CREATE TABLE example_database.todo_list (
     item_id INT AUTO_INCREMENT,
     content VARCHAR(255),
     PRIMARY KEY(item_id)
);
```
  * Populated the database a number of times with different data using SQL `INSERT INTO` command
  * Then select the data in the database with the SQL command `SELECT * FROM example_database.todo_list;`
  * Then I exited the MySQL console
  * Thereafter I created a todo-list.php PHP script that will connect to MySQL and ran query commands on the database
  * Populated the script with the code below using the vim editor
```
<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```
  * Then I access the page in my web browser by visiting the public IP address configured for my website followed by /todo-list.php
![Screenshot (170)](https://user-images.githubusercontent.com/69347415/193597093-d53020f2-2454-4891-b1cc-9341ac0cfc9c.png)
![Screenshot (171)](https://user-images.githubusercontent.com/69347415/193597481-445d7b84-eec7-49f7-a492-e4298ffd81ee.png)
![Screenshot (172)](https://user-images.githubusercontent.com/69347415/193597923-da744aca-f443-45a8-9937-a4ceba4e7d44.png)
![Screenshot (174)](https://user-images.githubusercontent.com/69347415/193597975-91ae7032-43d5-4d21-b4b4-5069f5298909.png)
![Screenshot (175)](https://user-images.githubusercontent.com/69347415/193598025-8d3aa05f-0486-4fd8-926f-39acdce66c7f.png)
