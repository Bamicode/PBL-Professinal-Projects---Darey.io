# CLIENT-SERVER ARCHITECTURE WITH MYSQL

Client-Server refers to an architecture in which two or more computers are connected together over a network to send and receive requests between one another.

In their communication, each machine has its own role: the machine sending requests is usually referred as "Client" and the machine responding (serving) is called "Server".

A simple diagram of Web Client-Server architecture is presented below:

![Client server architecture](https://user-images.githubusercontent.com/96090546/166747944-0c43e129-4a67-43f5-910f-aa2cd7b97cb7.PNG)

A quick example and see Client-Server communicatation in action.

Open Windows terminal and run curl command:

curl -Iv www.propitixhomes.com

![clt-serv](https://user-images.githubusercontent.com/96090546/166749274-cafa8219-a3a4-47ef-8d56-fb21aeb6a798.PNG)

# IMPLEMENT A CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS).

## TASK – To implement a Client Server Architecture using MySQL Database Management System (DBMS).

1. Create and configure two Linux-based virtual servers (EC2 instances in AWS).

Server A name - DB server

Server B name - DB client

![Instances](https://user-images.githubusercontent.com/96090546/166819203-a5fe3ac0-7927-4dd0-9200-5ef112f6ca0c.PNG)

2. On mysql server Linux Server install MySQL Server software

3. On mysql client Linux Server install MySQL Client software.

4. By default, both of your EC2 virtual servers are located in the same local virtual network, so they can communicate to each other using local IP addresses. Use mysql server's local IP address to connect from mysql client. MySQL server uses TCP port 3306 by default, so you will have to open it by creating a new entry in ‘Inbound rules’ in ‘mysql server’ Security Groups. For extra security, do not allow all IP addresses to reach your ‘mysql server’ – allow access only to the specific local IP address of your ‘mysql client’

![Inbound rules](https://user-images.githubusercontent.com/96090546/166819466-6e197be8-b0c0-41b9-8b0e-4c30b4c1e83c.PNG)


5. Configuring MySQL server to allow connections from remote hosts

Using the following commands on the Mysql server EC2 instance terminal:

Create remote user password:
CREATE USER 'remote_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';

Create a database:
CREATE DATABASE test_db;

Grant priviledges:
GRANT ALL ON test_db.* TO 'remote_user'@'%' WITH GRANT OPTION;

Flush Priviledges:
FLUSH PRIVILEGES;

Now restart mysql server using
sudo systemctl restart mysql

Now on the Mysql Client client EC2 instance

sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
Replace ‘127.0.0.1’ to ‘0.0.0.0’ like this:

![Replacing IP](https://user-images.githubusercontent.com/96090546/166819964-af684d19-8e63-4f22-ba17-ed5e0b51e20b.PNG)


6. From mysql client Linux Server connect remotely to mysql server Database Engine using the mysql utility to perform this action,

Run the following command:

sudo mysql -u remote_user -h <Private-IP-address> -p
  - where Private-ip-address is from my DB server instance.
  
![Client-server connection using mysql](https://user-images.githubusercontent.com/96090546/166820487-0607c794-5331-41cc-8752-7b6530ebcae1.PNG)
  


