原文地址：https://www.percona.com/blog/a-guide-to-better-understanding-mysql-charset-levels/

# A Guide to Better Understanding MySQL Charset Levels
We usually receive and see some questions regarding the charset levels in MySQL, especially after the deprecation of utf8mb3 and the new default uf8mb4. If you understand how the charset works on MySQL but have some questions regarding this change, please check out Migrating to utf8mb4: Things to Consider by Sveta Smirnova.

Some of the questions that usually come up when talking about charsets are:

Will changing my server charset affect my existing databases and tables?

Will changing my database charset affect my existing tables?

Will changing my table charset affect the data in my existing columns?

Following those questions, we can see there are several charset levels on MySQL (server, database, table, and column), and sometimes, we are afraid of touching any of them and end up messing up our data.

To get rid of these worries, let’s understand a little bit more about each level:

## Server character set
The variable character_set_server defines the server’s charset. Its value will be used as the default character set for any CREATE DATABASE commands. For example:

```
MySQL > SELECT @@character_set_server;
+------------------------+
| @@character_set_server |
+------------------------+
| utf8mb4                |
+------------------------+
1 row in set (0.00 sec)

MySQL > CREATE DATABASE TEST;
Query OK, 1 row affected (0.01 sec)

MySQL > SHOW CREATE DATABASE TESTG
*************************** 1. row ***************************
Database: test
Create Database: CREATE DATABASE `test` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ /*!80016 DEFAULT ENCRYPTION='N' */
1 row in set (0.00 sec)
```

## Database charset
As mentioned in the previous section, a database’s character set is inherited from the value of character_set_server at the time of the database creation (unless explicitly set). Still, there is a variable named character_set_database. This one is different from the rest. You are not supposed to configure this variable, as its value is automatically inherited from your default database. For example, when you connect to a database with latin1 character set with use <database>, your session value of character_set_database is automatically set to latin1. If no default database is set, the session uses the global value of character_set_server. This variable is deprecated, and modifying it generates a warning:

```
Level: Warning
Code: 1681
Message: Updating 'character_set_database' is deprecated. It will be made read-only in a future release.
1 row in set (0.00 sec)
```

This variable (character_set_database) only affects LOAD DATA commands that don’t include an explicit charset. On the other hand, the database’s charset will affect all the tables and stored procedures created without an explicit charset. Using the database created above as an example, if we create a table without specifying a charset, it is created with utf8mb4 since it’s the database’s character set:

Creating both tables:

MySQL > SHOW CREATE DATABASE TESTG
*************************** 1. row ***************************
Database: test
Create Database: CREATE DATABASE `test` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ /*!80016 DEFAULT ENCRYPTION='N' */
1 row in set (0.00 sec)

```
MySQL > CREATE TABLE t1( i int not null);
Query OK, 0 rows affected (0.06 sec)

MySQL > > CREATE TABLE t2 ( i int not null) DEFAULT CHARSET=utf8mb3;
Query OK, 0 rows affected, 1 warning (0.11 sec)
```

Table created without explicit charset inherits the database’s character set:

```
MySQL > SHOW CREATE TABLE t1G
*************************** 1. row ***************************
Table: t1
Create Table: CREATE TABLE `t1` (
`i` int NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)
```

The table created with explicit charset ignores the database’s character set:

```
MySQL > SHOW CREATE TABLE t2G
*************************** 1. row ***************************
Table: t2
Create Table: CREATE TABLE `t2` (
`i` int NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb3
1 row in set (0.00 sec)
```

## Table charset
As we verified, the table’s charset is inherited from the database if there’s no explicit charset or collation on the create table command. The table charset is used as a default character set for that table’s columns if a charset is not explicitly set. For example:

Creating a table with a default charset column and setting latin1 as the charset for the other column:

```
MySQL > SHOW CREATE DATABASE TESTG
*************************** 1. row ***************************
Database: TEST
Create Database: CREATE DATABASE `TEST` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ /*!80016 DEFAULT ENCRYPTION='N' */
MySQL > CREATE TABLE t1 ( col1 CHAR(10) CHARACTER SET latin1, col2 CHAR(10));
Query OK, 0 rows affected (0.13 sec)
```

Show create table from this table:

```
MySQL > SHOW CREATE TABLE t1G
*************************** 1. row ***************************
Table: t1
Create Table: CREATE TABLE `t1` (
`col1` char(10) CHARACTER SET latin1 COLLATE latin1_swedish_ci DEFAULT NULL,
`col2` char(10) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)
```

The col2 inherits the table’s charset, while col1 uses latin1.

```
MySQL > SELECT TABLE_SCHEMA,TABLE_NAME,COLUMN_NAME,DATA_TYPE,CHARACTER_SET_NAME,COLLATION_NAME FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_SCHEMA='test' AND TABLE_NAME='t1';
+--------------+------------+-------------+-----------+--------------------+--------------------+
| TABLE_SCHEMA | TABLE_NAME | COLUMN_NAME | DATA_TYPE | CHARACTER_SET_NAME | COLLATION_NAME     |
+--------------+------------+-------------+-----------+--------------------+--------------------+
| test         | t1         | col1        | char      | latin1             | latin1_swedish_ci  |
| test         | t1         | col2        | char      | utf8mb4            | utf8mb4_0900_ai_ci |
+--------------+------------+-------------+-----------+--------------------+--------------------+
```

## Column charset
As we just demonstrated, a column’s character set is inherited from the table’s default character set. The column’s charset ultimately affects the character set of the data stored in that column.

### Keeping it simple
Here is a handy summary of what we learned so far:

The variable character_set_server affects database creation.

A database’s character set affects tables created inside that database.

The variable character_set_database takes the value of the default database (or character_set_server, if not connected to any database) and affects LOAD DATA.

The table’s character set affects the columns created or added inside that table.

The column’s character set controls the data’s character set.

The previous items describe the default, which can be overruled by explicitly informing the character set and collation when creating the database/table/column.

So, we have: character_set_server -> database -> table -> column -> data.

### What can I modify?
Now that we have gained some knowledge about the character set levels in MySQL, we can address the questions from the beginning of the blog post about modifying charsets. Modifying the value of character_set_server, a database’s default character set, or a table’s default character set will not change the default charset for the already created objects nor convert any data. Still, after you modify a database’s default character set, for example, any new tables will be created using the new default value. Here is an example:

```
mysql > SHOW CREATE DATABASE testG
*************************** 1. row ***************************
Database: test
Create Database: CREATE DATABASE `test` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ /*!80016 DEFAULT ENCRYPTION='N' */
1 row in set (0.00 sec)
```

Changing the default value to latin1 on the database:


mysql > ALTER DATABASE test DEFAULT CHARACTER SET latin1;
Query OK, 1 row affected (0.03 sec)

```
mysql > SHOW CREATE DATABASE testG
*************************** 1. row ***************************
Database: test
Create Database: CREATE DATABASE `test` /*!40100 DEFAULT CHARACTER SET latin1 */ /*!80016 DEFAULT ENCRYPTION='N' */
1 row in set (0.00 sec)
```

After changing the database default, all new tables will use the new default if they do not have an explicit charset value:

```
mysql > CREATE TABLE t2 ( id int not null auto_increment primary key, name varchar(30));
Query OK, 0 rows affected (0.06 sec)
```

However, as we can see below, the tables previously created keep their original charset:

```
mysql > SHOW CREATE TABLE t1G
*************************** 1. row ***************************
Table: t1
Create Table: CREATE TABLE `t1` (
`id` int NOT NULL AUTO_INCREMENT,
`name` varchar(30) DEFAULT NULL,
PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)
mysql > SHOW CREATE TABLE t2G
*************************** 1. row ***************************
Table: t2
Create Table: CREATE TABLE `t2` (
`id` int NOT NULL AUTO_INCREMENT,
`name` varchar(30) DEFAULT NULL,
PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1
1 row in set (0.00 sec)
```

What about modifying the column’s character set?

The column’s character set is the one that ultimately affects the data. When you modify a column and change its character set, you force MySQL to convert the data. For example:

```
ALTER TABLE t MODIFY col1 CHAR(50) CHARACTER SET utf8mb4;
```

You can also convert the character set for all columns of a table in a single command with:

```
ALTER TABLE tbl_name CONVERT TO CHARACTER SET charset_name;
```

NOTE: There are dangers associated with converting data, which are not in the scope of this blog post.

Keep in mind these data conversions are different from modifying a table’s default character set, which only affects the default character set for any columns that are added after the change (as in the example below):

```
ALTER TABLE tbl_name DEFAULT CHARACTER SET charset_name;
```


## Client charset
The previous charsets are related to how the data is stored inside the database. There are other variables that control how information is sent and retrieved from the database. The four variables are: ‘character_set_client’, ‘character_set_connection’, ‘collation_connection’, and ‘character_set_results’. The first three are related to the character set of the commands sent by the client connected to the database, while ‘character_set_results’ configures the character set MySQL will use to send query results to the client.

The variable ‘character_set_client’ controls the character set of the commands sent by the client connected to the database. The ‘character_set_connection’ and ‘collation_connection’ control the character set and collation used for string comparisons. Queries with “where column = ‘mytext’”, the string ‘mytext’ will be interpreted by MySQL using the connection’s ‘character_set_connection’ and ‘collation_connection’. For example:

We have a table with latin1 data:

```
mysql > show create table latin_table G
*************************** 1. row ***************************
Table: latin_table
Create Table: CREATE TABLE `latin_table` (
`col1` varchar(10) CHARACTER SET latin1 COLLATE latin1_swedish_ci DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1
1 row in set (0.00 sec)
```

While the connection is using utf8mb4 character set:

```
mysql > select @@character_set_connection;
+----------------------------+
| @@character_set_connection |
+----------------------------+
| utf8mb4                    |
+----------------------------+
1 row in set (0.00 sec)
mysql > select @@collation_connection;
+------------------------+
| @@collation_connection |
+------------------------+
| utf8mb4_0900_ai_ci     |
+------------------------+
1 row in set (0.00 sec)
```

A query like the one below will generate an error because the string ‘test’ is in utf8mb4, and the data from the column col1 is stored as latin1:

```
mysql > select concat_ws(';',IF(col1 is not null, 'test', null), col1) from latin_table;
ERROR 1270 (HY000): Illegal mix of collations (utf8mb4_0900_ai_ci,COERCIBLE), (utf8mb4_0900_ai_ci,COERCIBLE), (latin1_swedish_ci,IMPLICIT) for operation 'concat_ws'
```

Although MySQL is able to perform some collation conversions, it is not able to do it inside a concat_ws with an IF involved.

If we change our character_set_connection to latin1 (the collation automatically changes to a latin1 collation), we are able to run the same query without causing conversion issues:

```
mysql > set session character_set_connection=latin1;
Query OK, 0 rows affected (0.00 sec)

mysql > select @@collation_connection;
+------------------------+
| @@collation_connection |
+------------------------+
| latin1_swedish_ci |
+------------------------+
1 row in set (0.00 sec)

mysql > select concat_ws(';',IF(col1 is not null, 'test', null), col1) from latin_table;
+---------------------------------------------------------+
| concat_ws(';',IF(col1 is not null, 'test', null), col1) |
+---------------------------------------------------------+
| test;test |
+---------------------------------------------------------+
1 row in set (0.01 sec)
```

These connection-specific parameters can all be set globally, but the global values are only used if your application client doesn’t have them set (or set them by default).

We hope this helps demystify MySQL character sets and helps you understand which values you need to modify to reach your desired outcome!

**Percona Distribution for MySQL is a complete, stable, scalable, and secure, open source MySQL solution, delivering enterprise-grade database environments for your most critical business applications. Deploy anywhere and implement easily with one-to-one compatibility with MySQL Community Edition.**
