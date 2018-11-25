## **Ubuntu setup guide**
### Update the installed packages
```sh
$ sudo apt-get update
```
#### To install apache2 server

```sh
$ sudo apt-get install apache2
```
#### To install MySql-Server
```sh
$ sudo apt-get install mysql-server
```
#### To install phpMyAdmin (MySql-Client)
```sh
$ sudo apt-get install phpmyadmin
```
Reference: https://www.ostechnix.com/install-phpmyadmin-with-lamp-stack-on-ubuntu-16-04/

#### To reconfigure phpMyAdmin
```sh
$ sudo dpkg-reconfigure phpmyadmin
```

#### Allow MySQL Remote Access
```sh
$ sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
Under the [mysqld] Locate the Line,
```sh
bind-address            = 127.0.0.1
```
And change it to,
```sh
bind-address            = 0.0.0.0
```
Restart mysql
```sh
$ sudo service mysql restart
or
$ systemctl restart mysql.service
```
Now Ubuntu Server will allow remote access to the MySQL Server, But still you need to configure MySQL users to allow access from any host.
```sh
$ mysql> CREATE USER 'username'@'%' IDENTIFIED BY 'password';
```
Or Allow from Specific IP Address,
```sh
CREATE USER 'username'@'192.168.1.100' IDENTIFIED BY 'password';
```
Refrence: https://www.configserverfirewall.com/ubuntu-linux/enable-mysql-remote-access-ubuntu/

#### To allow permission to user
To allow full permission to the user
```sh
GRANT ALL PRIVILEGES ON * . * TO 'username'
```
To Grant Different User Permissions
Here is a short list of other common possible permissions that users can enjoy.

ALL PRIVILEGES- as we saw previously, this would allow a MySQL user full access to a designated database (or if no database is selected, global access across the system)

CREATE- allows them to create new tables or databases

DROP- allows them to them to delete tables or databases

DELETE- allows them to delete rows from tables

INSERT- allows them to insert rows into tables

SELECT- allows them to use the SELECT command to read through databases

UPDATE- allow them to update table rows

GRANT OPTION- allows them to grant or remove other users' privileges

To provide a specific user with a permission, you can use this framework:

```sh
mysql> GRANT type_of_permission ON database_name.table_name TO 'username';
or
mysql> GRANT type_of_permission ON database_name.table_name TO 'username';
```

Reference: https://www.digitalocean.com/community/tutorials/how-to-create-a-new-user-and-grant-permissions-in-mysql