原文链接：https://www.percona.com/blog/mysql-general-tablespaces-a-powerful-storage-option-for-your-data/

# MySQL General Tablespaces: A Powerful Storage Option for Your Data

January 4, 2024

[Arunjith Aravindan](https://www.percona.com/blog/author/arunjith-aravindan)

Managing storage and performance efficiently in your MySQL database is crucial, and general tablespaces offer flexibility in achieving this. This blog discusses general tablespaces and explores their functionalities, benefits, and practical usage, along with illustrative examples.

## What are MySQL general tablespaces?

In contrast to the single system tablespace that holds system tables by default, general tablespaces are user-defined storage containers for multiple InnoDB tables. They provide flexibility in data organization and performance optimization compared to the default setup.

### Key features

- **Multi-table storage**: Unlike file-per-table tablespaces, which store each table in a separate file, general tablespaces can house numerous tables, enhancing storage efficiency.
- **Flexible location**: Data files can reside within the MySQL data directory or an independent location, enabling finer control over storage management and performance tuning.
- **Support for all table formats**: General tablespaces accommodate all InnoDB table formats, including Redundant, Compact, Dynamic, and Compressed Row formats, offering flexibility for specific needs.
- **Memory optimization**: Shared tablespace metadata reduces memory consumption compared to multiple file-per-table tablespaces.

### Benefits of using general tablespaces

- **Improved performance**: Strategically placing data files on faster disks or spreading tables across multiple disks can significantly enhance performance.
- **RAID and DRBD integration**: Data files can be placed on RAID or DRBD volumes for enhanced data redundancy and disaster recovery.
- **Encryption support**: MySQL supports encryption for General Tablespaces, enhancing the security of your data.
- **Convenient table management**: General tablespaces allow you to group multiple tables together, making it easier to manage and organize your database objects.

### Creating and managing general tablespaces

You can create general tablespaces using the CREATE TABLESPACE statement, specifying data file locations and engine options. 

Creating a general tablespace involves a few simple steps. The CREATE TABLESPACE statement below creates a new tablespace named my_general_tablespace with a specified data file ‘general_tablespace.ibd’. Additionally, it enables encryption for the tablespace with the option ENCRYPTION=’Y’ and sets the file block size with the FILE_BLOCK_SIZE = 16384 option.

 Let’s create a general tablespace named my_general_tablespace:

```
 mysql> CREATE TABLESPACE my_general_tablespace
    -> ADD DATAFILE 'general_tablespace.ibd'
    -> ENCRYPTION='Y'
    -> FILE_BLOCK_SIZE = 16384;
ERROR 3185 (HY000): Can't find master key from keyring, please check in the server log if a keyring is loaded and initialized successfully.
mysql>


mysql> pager grep -i keyring_file;
PAGER set to 'grep -i keyring_file'

mysql> SHOW PLUGINS;
50 rows in set (0.00 sec)

mysql> INSTALL PLUGIN keyring_file SONAME 'keyring_file.so';
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW PLUGINS;
| keyring_file                     | ACTIVE   | KEYRING            | keyring_file.so | GPL     |
50 rows in set (0.00 sec)

mysql> CREATE TABLESPACE my_general_tablespace
    -> ADD DATAFILE 'general_tablespace.ibd'
    -> ENCRYPTION='Y'
    -> FILE_BLOCK_SIZE = 16384;
Query OK, 0 rows affected (0.01 sec)

mysql>
```

Now, let us see how to create a general tablespace outside the data directory.

```
root@mysql8:/var/lib# mkdir mysql_user_defined
root@mysql8:/var/lib# chown -R mysql.mysql mysql_user_defined
root@mysql8:/var/lib#

mysql> CREATE TABLESPACE user_defined_general_tablespace
    -> ADD DATAFILE '/var/lib/var/lib/mysql_user_defined/user_defined_general_tablespace.ibd'
    -> Engine=InnoDB;
ERROR 3121 (HY000): The DATAFILE location must be in a known directory.
```

Error 3121 (HY000): The DATAFILE location must be in a known directory. Indicates that MySQL cannot create the tablespace in the specified directory because it’s not configured as a valid location for data files.

To address this error, follow these steps: Check the configured directories using SHOW VARIABLES LIKE ‘innodb_directories’; If /var/lib/mysql_user_defined is not listed, proceed to add the directory.

```
mysql> SHOW VARIABLES LIKE 'innodb_directories';
+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| innodb_directories |       |
+--------------------+-------+
1 row in set (0.00 sec)

root@mysql8:/etc/mysql/mysql.conf.d# grep -i innodb_directories mysqld.cnf
innodb_directories=/var/lib/mysql_user_defined
root@mysql8:/etc/mysql/mysql.conf.d# service mysql restart
root@mysql8:/etc/mysql/mysql.conf.d

mysql> CREATE TABLESPACE user_defined_general_tablespace
    -> ADD DATAFILE '/var/lib/mysql_user_defined/user_defined_general_tablespace.ibd'
    -> Engine=InnoDB;
Query OK, 0 rows affected (0.02 sec)
```

## Assigning tables to general tablespaces

Once a MySQL general tablespace is created, you can assign tables to it during the table creation process or by altering existing tables. Here’s an example of creating a table within the my_general_tablespace:

```
mysql> CREATE TABLE my_table (
    ->     id INT PRIMARY KEY,
    ->     name VARCHAR(50)
    -> ) TABLESPACE = my_general_tablespace;
ERROR 3825 (HY000): Request to create 'unencrypted' table while using an 'encrypted' tablespace.
mysql>

mysql> CREATE TABLE my_table (
    ->     id INT PRIMARY KEY,
    ->     name VARCHAR(50)
    -> ) TABLESPACE = my_general_tablespace
    ->   ENCRYPTION='Y';
Query OK, 0 rows affected (0.02 sec)
```

The user_defined_general_tablespace we created is unencrypted, allowing us to create unencrypted tables within it.

```
mysql> CREATE TABLE my_unencrypted_table(
    -> id INT PRIMARY KEY,
    -> name VARCHAR(50)
    -> ) TABLESPACE = user_defined_general_tablespace;
Query OK, 0 rows affected (0.01 sec)
```

##  Migrating tables to general tablespaces

If you have existing tables and want to move them to a general tablespace, you can use the ALTER TABLE statement. For instance:

```
mysql> show create table authorsG
*************************** 1. row ***************************
       Table: authors
Create Table: CREATE TABLE `authors` (
  `id` int DEFAULT NULL,
  `first_name` varchar(50) DEFAULT NULL,
  `last_name` varchar(50) DEFAULT NULL,
  `age` int DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

mysql> ALTER TABLE authors
    -> TABLESPACE = my_general_tablespace;
ERROR 3825 (HY000): Request to create 'unencrypted' table while using an 'encrypted' tablespace.

mysql> ALTER TABLE authors ENCRYPTION='Y';
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE authors
    -> TABLESPACE = my_general_tablespace;
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0
mysql>
```

To transfer a table from the general tablespace to a file-per-table tablespace, specify “innodb_file_per_table” as the target tablespace name.

```
mysql> ALTER TABLE authors
    -> TABLESPACE = innodb_file_per_table ENCRYPTION = 'Y';
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

## Monitoring

The query retrieves information about specified MySQL tablespaces, including tablespace name, file name, storage engine, status, and available free data space.

```
mysql> SELECT TABLESPACE_NAME, FILE_NAME, ENGINE, STATUS, DATA_FREE FROM INFORMATION_SCHEMA.FILES WHERE TABLESPACE_NAME IN ('my_general_tablespace',
'user_defined_general_tablespace')G
*************************** 1. row ***************************
TABLESPACE_NAME: my_general_tablespace
      FILE_NAME: ./general_tablespace.ibd
         ENGINE: InnoDB
         STATUS: NORMAL
      DATA_FREE: 0
*************************** 2. row ***************************
TABLESPACE_NAME: user_defined_general_tablespace
      FILE_NAME: /var/lib/mysql_user_defined/user_defined_general_tablespace.ibd
         ENGINE: InnoDB
         STATUS: NORMAL
      DATA_FREE: 0
2 rows in set (0.00 sec)
```

The following query helps to find information about InnoDB tables that belong to the specified tablespace.

```
mysql> SELECT NAME, SPACE_TYPE, TABLESPACE_NAME from INFORMATION_SCHEMA.INNODB_TABLES JOIN INFORMATION_SCHEMA.FILES ON FILE_ID=SPACE WHERE TABLESPACE_NAME='my_general_tablespace'G
*************************** 1. row ***************************
           NAME: mytestdb/my_table
     SPACE_TYPE: General
TABLESPACE_NAME: my_general_tablespace
*************************** 2. row ***************************
           NAME: mytestdb/books
     SPACE_TYPE: General
TABLESPACE_NAME: my_general_tablespace
2 rows in set (0.01 sec)
```

To retrieve TABLESPACE information for a particular InnoDB table, utilize the following query.

```
mysql> SELECT NAME, SPACE_TYPE, TABLESPACE_NAME from INFORMATION_SCHEMA.INNODB_TABLES JOIN INFORMATION_SCHEMA.FILES ON FILE_ID=SPACE WHERE NAME='mytestdb/my_table'G
*************************** 1. row ***************************
           NAME: mytestdb/my_table
     SPACE_TYPE: General
TABLESPACE_NAME: my_general_tablespace
1 row in set (0.00 sec)
```
**Examples of practical usage:**

- Separating frequently accessed and rarely used tables: Place frequently accessed tables in general tablespaces residing on SSDs for superior performance while keeping rarely used tables in HDD-based general tablespaces to optimize storage costs.
- Balancing I/O load: Distribute tables across multiple general tablespaces located on different disks to avoid I/O bottlenecks and improve query execution speed.
- Dedicated storage for critical data: Create a separate general tablespace with RAID or DRBD configuration for critical tables, ensuring maximum redundancy and protection against hardware failures.

**Conclusion**

MySQL general tablespaces offer a powerful and flexible storage solution for optimizing data organization and performance, and understanding their functionalities and effectively deploying them can significantly improve your database management efforts. In order to maximize their benefits, remember to carefully consider your specific needs and workload characteristics before implementing general tablespaces.

**Percona Distribution for MySQL is the most complete, stable, scalable, and secure open source MySQL solution available, delivering enterprise-grade database environments for your most critical business applications… and it’s free to use!**
