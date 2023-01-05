# CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS)

## Preparing prerequisites
  * Set and spin up 2 new EC2 instances of t2.micro family with Ubuntu Server 20.04 LTS each.One for the server and the other for client
  * Connected through SSH from my windows terminal into the remote AWS VM instance using the downloaded pem key
  
## Running Installation, DBMS Configuration and Connection
  * Updated both VM instances with `sudo apt update`
     * Server:
     ![Screenshot (244)](https://user-images.githubusercontent.com/69347415/195480097-d1b8b706-7aca-4fe6-ad19-14e8a68846aa.png)
     * Client:
     ![Screenshot (241)](https://user-images.githubusercontent.com/69347415/195480173-59a726f2-57b8-4298-8c3c-bd7360df6336.png)

  * On the Server: I installed mysql-server using `sudo apt install mysql-server`
    ![Screenshot (245)](https://user-images.githubusercontent.com/69347415/195480468-c7ad2131-0a5b-45a5-9def-1178c0c81260.png)
    On the Client: I installed mysql-client using `sudo apt install mysql-client`
    ![Screenshot (242)](https://user-images.githubusercontent.com/69347415/195480251-81d351c2-68f5-42ae-bcf3-298ae4c19ba7.png)

  * On the server:
    * Started and activated the server with `sudo systemctl enable mysql`
    * Set up secure installation with user password with `sudo mysql_secure_installation`
      ![Screenshot (247)](https://user-images.githubusercontent.com/69347415/195480610-c83bc9e9-7e43-4117-806f-047ab12c84d7.png)
    * Accessed the mysql server with `sudo mysql -p` then input the password setup above
    * In the MySql Server, I created a user with a password, created a databse and granted the user all access to the database created with the codes below
     * `CREATE USER 'ubuntu_user'@'%' IDENTIFIED WITH mysql_native_password BY 'ubuntu';`
     * `CREATE DATABASE ubuntu_db;`
     * `GRANT ALL ON ubuntu_db.* TO 'ubuntu_user'@'%' WITH GRANT OPTION;`
     * `FLUSH PRIVILEGES;`
    * After which I exited the server
    * Then I configured the server to allow connections from remote hosts with `sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf` changing the bind address to 0.0.0.0
      ![Screenshot (251)](https://user-images.githubusercontent.com/69347415/195480722-14cb20f0-93ad-4d54-a874-5c118be7f03f.png)
      
          
  * On the AWS Console: I opened the TCP port 3306 by creating a new entry in 'Inbound rules' in 'mysql server' security groups. For extra security, I allowed access only to the specific local IP address of mysql client
  * On the MySql client instance: I connected to the MySQL server with `sudo mysql -u ubuntu_user -h [mysql server IP address] -p` after which I entered the password and accessed the server
  * On the server, I ran the SQL command `SHOW DATABASES;`, after which I saw the list of databases created earlier om the MySql server
    ![Screenshot (243)](https://user-images.githubusercontent.com/69347415/195480840-15088768-d328-429e-89f9-a5551b3fd39e.png)

