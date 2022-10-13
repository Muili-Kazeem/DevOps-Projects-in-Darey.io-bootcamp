# CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS)

## Preparing prerequisites
  * Set and spin up 2 new EC2 instances of t2.micro family with Ubuntu Server 20.04 LTS each.One for the server and the other for client
  * Connected through SSH from my windows terminal into the remote AWS VM instance using the downloaded pem key
  
## Running Installation, DBMS Configuration and Connection
  * Updated both VM instances with `sudo apt update`
  * On the Server: I installed mysql-server using `sudo apt install mysql-server`
    On the Client: I installed mysql-client using `sudo apt install mysql-client`
  * On the server:
          * Started and activated the server with `sudo systemctl enable mysql`
          * Set up secure installation with user password with `sudo mysql_secure_installation`
          * Accessed the mysql server with `sudo mysql -p` then input the password setup above
          * In the MySql Server, I created a user with a password, created a databse and granted the user all access to the database created with the codes below
                  `CREATE USER 'ubuntu_user'@'%' IDENTIFIED WITH mysql_native_password BY 'ubuntu';`
                  `CREATE DATABASE ubuntu_db;`
                  `GRANT ALL ON ubuntu_db.* TO 'ubuntu_user'@'%' WITH GRANT OPTION;`
                  `FLUSH PRIVILEGES;
          * After which I exited the server
          * Then I configured the server to allow connections from remote hosts with `sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf` changing the bind address to 0.0.0.0
          
  * On the AWS Console: I opened the TCP port 3306 by creating a new entry in 'Inbound rules' in 'mysql server' security groups. For extra security, I allowed access only to the specific local IP address of mysql client
  * On the MySql client instance: I connected to the MySQL server with `sudo mysql -u ubuntu_user -h [mysql server IP address] -p` after which I entered the password and accessed the server
  * On the server, I ran the SQL command `SHOW DATABASES;`, after which I saw the list of databases created earlier om the MySql server
