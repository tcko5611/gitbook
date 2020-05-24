# MySql

## Install mysql into ubuntu

```text
 sudo apt-get install mysql-server
 sudo apt-get install mysql-client
 sudo apt-get install libmysqlclient-dev
 sudo apt-get install net-tools
 sudo netstat -tap | grep mysql
```

The unbuntu mysql config file is in _/etc/mysql/debian.cnf_, and you need to check the file to see the root user name and password:

```text
[client]
host     = localhost
user     = debian-sys-maint
password = aVkAJBXnVul9ldSM
socket   = /var/run/mysqld/mysqld.sock
[mysql_upgrade]
host     = localhost
user     = debian-sys-maint
password = aVkAJBXnVul9ldSM
socket   = /var/run/mysqld/mysqld.sock
```

## Create new user and set the prilevige

type _mysql -u debian-sys-maint -p_ and enter the root password. In mysql like you want to add a new user tcko@localhost with password xxxx, you can do as follow:

```text
mysql> CREATE USER 'tcko@localhost' IDENTIFIED BY 'XXX';
mysql> GRANT ALL PRIVILEGES ON *.* TO tcko@localhost;
mysql> FLUSH PRIVILEGES;
```

## Create and show DATABASE

```text
mysql> CREATE DATABASE 'moneydb'
mysql> show databases; /* all databases */
mysql> use moneydb; 
mysql> select database(); /* show current database */
```

## Create and show table

```text
mysql> CREATE TABLE mainItem (id INT NOT NULL AUTO_INCREMENT, name 
  NCHAR(10) NOT NULL, PRIMARY KEY(id));

mysql> INSERT INTO mainItem (name) VALUES('食'); 

mysql> SELECT id, name FROM mainItem;

mysql> create table subItem (id int not null auto_increment, name nchar(10), main_id int references mainItem(id), primary key(id));

mysql> show  tables;
mysql> ALTER TABLE subItem MODIFY main_id int NOT NULL
mysql> describe subItem;
```

