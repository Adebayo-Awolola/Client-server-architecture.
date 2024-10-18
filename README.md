# Client-server-architecture.
##Create and configure two Linux-based virtual servers (EC2 instances in AWS).
Server A name - `mysql server`
Server B name - `mysql client`

![image](https://github.com/user-attachments/assets/fbdc5c3e-959c-4329-9472-08fdc6efa1de)
On mysql server Linux Server install MySQL Server software.
## Sudo apt Update && upgrade to update your operating system and install the updates.
## sudo apt install mysql-server
Start the service, to start you may be prompted to restart your server.
## sudo systemctl start mysql.service
These commands will install and start MySQL, but will not prompt you to set a password or make any other configuration changes. this leaves your installation of MySQL insecure.
###You can skip this for the purpose of the test and move to the next step.
Configuring MySQL
For fresh installations of MySQL, you’ll want to run the DBMS’s included security script. This script changes some of the less secure default options for things like remote root logins and sample users.
 As of July 2022, an error will occur when you run the mysql_secure_installation script without some further configuration. The reason is that this script will attempt to set a password for the installation’s root MySQL account but, by default on Ubuntu installations, this account is not configured to connect using a password.
Before July 2022, this script would silently fail after attempting to set the root account password and continue with the rest of the prompts. However, as of this writing the script will return an error.
This will lead the script into a recursive loop which you can only get out of by closing your terminal window.
Because the mysql_secure_installation script performs several other actions that are useful for keeping your MySQL installation secure, it’s still recommended that you run it before you begin using MySQL to manage your data. To avoid entering this recursive loop, though, you’ll need to first adjust how your root MySQL user authenticates.
First, open up the MySQL prompt:
## sudo mysql

## ![image](https://github.com/user-attachments/assets/cdac6afb-bdb1-4ac5-a1e9-6a65ed2834e8)

Then run the following ALTER USER command to change the root user’s authentication method to one that uses a password. The following example changes the authentication method to mysql_native_password:
## ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
After making this change, exit the MySQL prompt:
##Mysql exit
Following that, you can run the mysql_secure_installation script without issue.

Once the security script completes, you can then reopen MySQL and change the root user’s authentication method back to the default.
 To authenticate as the root MySQL user using a password, run this command:
##  mysql -u root -p
Then go back to using the default authentication method using this command:
## ALTER USER 'root'@'localhost' IDENTIFIED WITH auth_socket;
Run the security script with sudo:
## sudo mysql_secure_installation
![image](https://github.com/user-attachments/assets/5531492a-ef57-4636-af61-9375d598a914)

This will take you through a series of prompts where you can make some changes to your MySQL installation’s security options. The first prompt will ask whether you’d like to set up the Validate Password Plugin, which can be used to test the password strength of new MySQL users before deeming them valid.
If you elect to set up the Validate Password Plugin, any MySQL user you create that authenticates with a password will be required to have a password that satisfies the policy you select. The strongest policy level — which you can select by entering 2 — will require passwords to be at least eight characters long and include a mix of uppercase, lowercase, numeric, and special characters:
From there, you can press Y and then ENTER to accept the defaults for all the subsequent questions. This will remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes you have made.
Once the script completes, your MySQL installation will be secured. You can now move on to creating a dedicated database user with the MySQL client.
 Creating a Dedicated MySQL User and Granting Privileges
Upon installation, MySQL creates a root user account which you can use to manage your database. This user has full privileges over the MySQL server, meaning it has complete control over every database, table, user, and so on. Because of this, it’s best to avoid using this account outside of administrative functions. This step outlines how to use the root MySQL user to create a new user account and grant it privileges.
## mysql -u root -p or sudo mysql if you skipped step 3.

![image](https://github.com/user-attachments/assets/d481ab1f-453a-48f6-b91c-c8ace7b7a874)

Once you have access to the MySQL prompt, you can create a new user with a CREATE USER statement. These follow this general syntax:
## CREATE USER 'sammy'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
After CREATE USER, you specify a username. This is immediately followed by an @ sign and then the hostname from which this user will connect. If you only plan to access this user locally from your Ubuntu server, you can specify localhost. Wrapping both the username and host in single quotes isn’t always necessary, but doing so can help to prevent errors.
Or use. ## CREATE USER 'sammy01'@'%' IDENTIFIED WITH mysql_native_password BY 'password'; this grant access from any host.
![image](https://github.com/user-attachments/assets/33ca4fa8-0860-483b-bb74-77edfe32eaba)

### On mysql client Linux Server install MySQL Client software.
## Sudo apt Update && upgrade to update your operating system and install the updates.
  Before you start, you should confirm that you don't have MySQL client installed. If you have installed MySQL server before, likely you will have MySQL client installed as well. If you see something like below, then MySQL client is already installed and you can stop reading now.
$ mysql --version
You will see the error below if MYSQL is not installed.
![image](https://github.com/user-attachments/assets/96f880c7-856d-4bcd-8e21-089060cceaa0)

Install the MySQL client by running the following command:
## sudo apt-get install mysql-client -y
![image](https://github.com/user-attachments/assets/4019524f-3a66-4e76-b4e5-afb3207314a1)

After the installation is complete, you can verify that the MySQL client is installed by running the following command:
## mysql --version

![image](https://github.com/user-attachments/assets/2e1210aa-a4c5-4971-9a04-903d50a076b3)
By default, both of your EC2 virtual servers are located in the same local virtual network, so they can communicate using local IP addresses. Use mysql server's local IP address to connect from MySQL client. MySQL server uses TCP port 3306 by default, so you will have to open it by creating a new entry in 'Inbound rules' in 'mysql server' Security Groups. For extra security, do not allow all IP addresses to reach your 'mysql server' - allow access only to the specific local IP address of your 'mysql client'.

![image](https://github.com/user-attachments/assets/e109d09e-1f03-45c6-84b7-73824164cee7)

You might need to configure MySQL server to allow connections from remote hosts.
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf 
Replace '127.0.0.1' to '0.0.0.0' like this: restart your service after this configuration change.
![image](https://github.com/user-attachments/assets/3f416439-e09e-4f25-8a62-04e3230b3029)

From mysql client Linux Server connects remotely to MySQL server Database Engine without using SSH. You must use the mysql utility to perform this action.
## mysql -u sammy01 -p -h your_hostname or private IP, for example, mysql -u sammy -p -h 172.31.38.6
Check that you have successfully connected to a remote MySQL server and can perform SQL queries:
![image](https://github.com/user-attachments/assets/789ef278-c48f-4147-872b-10d5f6fa7548)
Show databases;
If you see an output similar to the below image, then you have successfully completed this project you have deployed a fully functional MySQL Client-Server setup. Well Done! You are getting there gradually. You can further play around with this setup and practice creating/dropping databases & tables and inserting/selecting records to and from them.


![image](https://github.com/user-attachments/assets/9cd4d7ef-abde-44eb-8af0-08e5297bd9df)










