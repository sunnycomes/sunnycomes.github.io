---
layout: post
title: "Basic Practical Usage of JDBC API"
date: 2013-12-31 14:54:53 +0800
comments: true
tags: MySQL
---

##Backup Tool##

###Back up From the Command Line (using mysqldump)

If you have shell or telnet access to your web server, you can backup your MySQL data by using the mysqldump command. This command connects to the MySQL server and creates an SQL dump file. The dump file contains the SQL statements necessary to re-create the database. Here is the proper syntax:

 * `mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]`  
This command will backup the 'Tutorials' database into a file called tut_backup.sql which will contain all the SQL statements needed to re-create the database.

* `mysqldump -u root -p Tutorials php_tutorials asp_tutorials > tut_backup.sql`  
With mysqldump command you can specify certain tables of your database you want to backup. For example, to back up only php_tutorials and asp_tutorials tables from the 'Tutorials' database accomplish the command below. Each table name has to be separated by space.

* `mysqldump -u root -p --databases Tutorials Articles Comments > content_backup.sql`  
Sometimes it is necessary to back up more that one database at once. In this case you can use the --database option followed by the list of databases you would like to backup. Each database name has to be separated by space.

* `mysqldump -u root -p --all-databases > alldb_backup.sql`  
If you want to back up all the databases in the server at one time you should use the --all-databases option. It tells MySQL to dump all the databases it has in storage.

The mysqldump command has also some other useful options:

1. `--add-drop-table`: Tells MySQL to add a `DROP TABLE` statement before each `CREATE TABLE` in the dump.

1. `--no-data`: Dumps only the database structure, not the contents.

1. `--add-locks`: Adds the `LOCK TABLES` and `UNLOCK TABLES` statements you can see in the dump file.

The mysqldump command has advantages and disadvantages. The advantages of using mysqldump are that it is simple to use and it takes care of table locking issues for you. The disadvantage is that the command locks tables. If the size of your tables is very big mysqldump can lock out users for a long period of time.

###Back up your MySQL Database with Compress

* `mysqldump -u [uname] -p[pass] [dbname] | gzip -9 > [backupfile.sql.gz]`  
If your mysql database is very big, you might want to compress the output of mysqldump. Just use the mysql backup command below and pipe the output to gzip, then you will get the output as gzip file.

* `gunzip [backupfile.sql.gz]`  
If you want to extract the .gz file, use the command above:

* `Restoring your MySQL Database`  
Above we backup the Tutorials database into tut_backup.sql file. To re-create the Tutorials database you should follow two steps:
	1. Create an appropriately named database on the target machine
	1. Load the file using the mysql command:
	1. `$ mysql -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]`

Have a look how you can restore your tut_backup.sql file to the Tutorials database.

* To restore compressed backup files you can do the following:  
`gunzip < [backupfile.sql.gz] | mysql -u [uname] -p[pass] [dbname]`

* If you need to restore a database that already exists, you'll need to use mysqlimport command. The syntax for mysqlimport is as follows:  
`mysqlimport -u [uname] -p[pass] [dbname] [backupfile.sql]`

##Operations over Web with PHPMyAdmin##

* It is assumed that you have phpMyAdmin installed since a lot of web service providers use it. To backup your MySQL database using PHPMyAdmin just follow a couple of steps:
	1. Open phpMyAdmin.
	1. Select your database by clicking the database name in the list on the left of the screen.
	1. Click the Export link. This should bring up a new screen that says View dump of database (or something similar).
	1. In the Export area, click the Select All link to choose all of the tables in your database.
	1. In the SQL options area, click the right options.
	1. Click on the Save as file option and the corresponding compression option and then click the `Go` button. A dialog box should appear prompting you to save the file locally.

* Restoring your database is easy as well as backing it up. Make the following:
	1. Open phpMyAdmin.
	1. Create an appropriately named database and select it by clicking the database name in the list on the left of the screen. If you would like to rewrite the backup over an 	1. existing database then click on the database name, select all the check boxes next to the table names and select Drop to delete all existing tables in the database.
	1. Click the SQL link. This should bring up a new screen where you can either type in SQL commands, or upload your SQL file.
	1. Use the browse button to find the database file.
	1. Click Go button. This will upload the backup, execute the SQL commands and re-create your database.


##References:
* [How to Back Up and Restore a MySQL Database](http://webcheatsheet.com/sql/mysql_backup_restore.php)