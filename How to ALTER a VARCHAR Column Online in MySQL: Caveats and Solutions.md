原文链接：https://www.percona.com/blog/how-to-alter-a-varchar-column-online-in-mysql-caveats-and-solutions/



# How to ALTER a VARCHAR Column Online in MySQL: Caveats and Solutions



In the world of database management, ALTER TABLE operations are a crucial part of modifying database structures. MySQL, a popular database management system, offers online operations since version 5.6, providing a convenient way to perform these alterations without locking the table. However, there are caveats. In this blog, we’ll explore the process of altering VARCHAR columns online, delving into insights gained while expanding the size of such columns.

To kick start our journey, let’s consider a table definition that requires the expansion of a VARCHAR column named “_varchar” to accommodate more data. Here’s the original table definition:

![image](https://github.com/user-attachments/assets/ad4e7997-5c31-4258-8e84-667ad0d4347b)

We execute the initial ALTER TABLE command:

```
mysql> ALTER TABLE test.varchar_alter CHANGE COLUMN _varchar _varchar VARCHAR(85) NOT NULL DEFAULT '', ALGORITHM=INPLACE, LOCK=NONE;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SHOW CREATE TABLE `varchar_alter`G
*************************** 1. row ***************************
       Table: varchar_alter
Create Table: CREATE TABLE `varchar_alter` (
  `id` int NOT NULL AUTO_INCREMENT,
  `_varchar` varchar(85) NOT NULL DEFAULT '',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb3
1 row in set (0.00 sec)
```

The alteration appears successful, and we see the table definition accordingly modified. 

Now, let us try a subsequent alteration — attempting to expand to 200. Surprisingly, when we attempt to raise the VARCHAR column length to 200, we encounter an error:

```
mysql> ALTER TABLE test.varchar_alter CHANGE COLUMN _varchar _varchar VARCHAR(200) NOT NULL DEFAULT '', ALGORITHM=INPLACE, LOCK=NONE;
ERROR 1846 (0A000): ALGORITHM=INPLACE is not supported. Reason: Cannot change column type INPLACE. Try ALGORITHM=COPY.
```

The DDL is denied, and MySQL suggests using the COPY algorithm instead. The command was changed to go with algorithm copy and shared lock as follows, and that successfully performed ALTER the VARCHAR column:

```
mysql> ALTER TABLE test.varchar_alter CHANGE COLUMN _varchar _varchar VARCHAR(200) NOT NULL DEFAULT '', ALGORITHM=COPY, LOCK=SHARED;
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SHOW CREATE TABLE `varchar_alter`G
*************************** 1. row ***************************
       Table: varchar_alter
Create Table: CREATE TABLE `varchar_alter` (
  `id` int NOT NULL AUTO_INCREMENT,
  `_varchar` varchar(200) NOT NULL DEFAULT '',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb3
1 row in set (0.00 sec)
```

## Understanding the limits of in-place ALTER

Why did MySQL deny the online alter (ALGORITHM=INPLACE) for modifying the VARCHAR column length to 200 though initially, it allowed for raising this to 85?

We get the answer from the documentation itself, and it has to do with how VARCHAR stores the data, actually prefix *and* data. In MySQL, the VARCHAR values are stored as a one or two-byte length prefix plus data. The prefix length depends on how large the data is. For the data length up to 255 bytes, only one byte of prefix is used, but for values more than 255 bytes, the prefix length required is two bytes. Thus in-place ALTER TABLE only supports increasing VARCHAR column size from 0 to 255 bytes or from 256 bytes to a greater size. It doesn’t allow INPLACE alter when the ALTER requires extending the prefix length.

To see this in action, you can query the information_schema’s COLUMNS table. Initially, when the column length was ALTER-ed to 85, note the details below.

```
mysql> select * from information_schema.COLUMNS where TABLE_SCHEMA = 'test' and TABLE_NAME in ('varchar_alter') and COLUMN_NAME = '_varchar'G
*************************** 1. row ***************************
           TABLE_CATALOG: def
            TABLE_SCHEMA: test
              TABLE_NAME: varchar_alter
             COLUMN_NAME: _varchar
        ORDINAL_POSITION: 2
          COLUMN_DEFAULT:
             IS_NULLABLE: NO
               DATA_TYPE: varchar
<strong>CHARACTER_MAXIMUM_LENGTH: 85
  CHARACTER_OCTET_LENGTH: 255
</strong>       NUMERIC_PRECISION: NULL
           NUMERIC_SCALE: NULL
      DATETIME_PRECISION: NULL
      CHARACTER_SET_NAME: utf8mb3
          COLLATION_NAME: utf8mb3_general_ci
             COLUMN_TYPE: varchar(85)
              COLUMN_KEY:
                   EXTRA:
              PRIVILEGES: select,insert,update,references
          COLUMN_COMMENT:
   GENERATION_EXPRESSION:
                  SRS_ID: NULL
1 row in set (0.00 sec)
```

Here, CHARACTER_MAXIMUM_LENGTH specifies the length of characters stored in the column, while CHARACTER_OCTET_LENGTH specifies the length in bytes. Note that for this column, the byte length is already 255, and increasing it further would require MySQL to extend the VARCHAR prefix size to two bytes. After changing the VARCHAR column size to 200, the following is the storage requirement.

```
mysql> select * from information_schema.COLUMNS where TABLE_SCHEMA = 'test' and TABLE_NAME in ('varchar_alter') and COLUMN_NAME = '_varchar'G
*************************** 1. row ***************************
           TABLE_CATALOG: def
            TABLE_SCHEMA: test
              TABLE_NAME: varchar_alter
             COLUMN_NAME: _varchar
        ORDINAL_POSITION: 2
          COLUMN_DEFAULT:
             IS_NULLABLE: NO
               DATA_TYPE: varchar
<strong>CHARACTER_MAXIMUM_LENGTH: 200
  CHARACTER_OCTET_LENGTH: 600
</strong>       NUMERIC_PRECISION: NULL
           NUMERIC_SCALE: NULL
      DATETIME_PRECISION: NULL
      CHARACTER_SET_NAME: utf8mb3
          COLLATION_NAME: utf8mb3_general_ci
             COLUMN_TYPE: varchar(200)
              COLUMN_KEY:
                   EXTRA:
              PRIVILEGES: select,insert,update,references
          COLUMN_COMMENT:
   GENERATION_EXPRESSION:
                  SRS_ID: NULL
1 row in set (0.00 sec)
```

### How to ONLINE alter VARCHAR columns in such cases

When facing the limitations of online ALTER TABLE for VARCHAR columns in MySQL, an excellent alternative to consider is using [Percona Toolkit](https://www.percona.com/software/database-tools/percona-toolkit)‘s pt-online-schema-change. It is well known and industry-standard tool, part of the Percona Toolkit, to perform ONLINE changes with minimal downtime.

### ALTER TABLE to change CHARACTER SET of a VARCHAR column

Also, remember that this also implies changing the character set of the column. Of course, a character set defines the storage requirements, and changing CHARACTER SET also causes an increase in bytes required. Thus you will also need to consider this while making the change. Check the following sample, where you can see that to store the VARCHAR length of 85 bytes, length has increased to 340 instead of 255 previously.

```
mysql> ALTER TABLE test.varchar_alter CHANGE COLUMN _varchar _varchar VARCHAR(84) charset UTF8MB4 NOT NULL DEFAULT '', ALGORITHM=INPLACE, LOCK=NONE;
ERROR 1846 (0A000): ALGORITHM=INPLACE is not supported. Reason: Cannot change column type INPLACE. Try ALGORITHM=COPY.
mysql> ALTER TABLE test.varchar_alter CHANGE COLUMN _varchar _varchar VARCHAR(85) charset UTF8MB4 NOT NULL DEFAULT '', ALGORITHM=COPY, LOCK=SHARED;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> select * from information_schema.COLUMNS where TABLE_SCHEMA = 'test' and TABLE_NAME in ('varchar_alter') and COLUMN_NAME = '_varchar'G
*************************** 1. row ***************************
           TABLE_CATALOG: def
            TABLE_SCHEMA: test
              TABLE_NAME: varchar_alter
             COLUMN_NAME: _varchar
        ORDINAL_POSITION: 2
          COLUMN_DEFAULT:
             IS_NULLABLE: NO
               DATA_TYPE: varchar
CHARACTER_MAXIMUM_LENGTH: 85
  CHARACTER_OCTET_LENGTH: 340
       NUMERIC_PRECISION: NULL
           NUMERIC_SCALE: NULL
      DATETIME_PRECISION: NULL
      CHARACTER_SET_NAME: utf8mb4
          COLLATION_NAME: utf8mb4_0900_ai_ci
             COLUMN_TYPE: varchar(85)
              COLUMN_KEY:
                   EXTRA:
              PRIVILEGES: select,insert,update,references
          COLUMN_COMMENT:
   GENERATION_EXPRESSION:
                  SRS_ID: NULL
1 row in set (0.00 sec)
```

### Conclusion

Online ALTER TABLE operations in MySQL bring flexibility to database management, but they have specific limitations, especially when dealing with VARCHAR columns. Understanding the underlying storage mechanics is vital for successful alterations. The COPY ALGORITHM can help manage VARCHAR expansions beyond the 255-byte limit, but pt-online-schema-change can come in handy to perform the operation with minimal hiccups.

**Percona Distribution for MySQL is the most complete, stable, scalable, and secure open source MySQL solution available, delivering enterprise-grade database environments for your most critical business applications… and it’s free to use!**
