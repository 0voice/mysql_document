原文地址：https://www.mysqltutorial.org/mysql-administration/



# MySQL Administration

This MySQL administration tutorial series offers everything you need to know to manage your MySQL database server effectively.

## What you will learn

- Understand MySQL architecture.
- Carry basic MySQL administrative tasks.
- Choose appropriate MySQL store engines.
- Configure the InnoDB storage engine.
- Perform backup & recovery.
- Create user accounts & grant permissions.
- Maintain and optimize the databases.

## Section 1. Exploring MySQL Server

- [MySQL Architecture](https://www.mysqltutorial.org/mysql-administration/mysql-architecture/) – Learn about MySQL architecture and understand its key components.
- [mysqld](https://www.mysqltutorial.org/mysql-administration/mysqld/) – Explain the mysqld, which is known as MySQL server.
- [Start MySQL Server](https://www.mysqltutorial.org/mysql-administration/start-mysql/) – Show you how to start the MySQL Server on Windows and Linux.
- [Stop MySQL Server](https://www.mysqltutorial.org/mysql-administration/stop-mysql/) – Describe the steps of stopping MySQL Server on Windows and Linux.
- [Restart MySQL Server](https://www.mysqltutorial.org/mysql-administration/restart-mysql/) – Guide you on how to restart MySQL Server on Windows and Linux.
- [MySQL configuration file](https://www.mysqltutorial.org/mysql-administration/mysql-configuration-file/) – Explore the MySQL configuration file, discover its location, and understand its structure.
- [MySQL Data Directory](https://www.mysqltutorial.org/mysql-administration/mysql-data-directory/) – Learn about the data directory that stores databases, tables, log files, and other essential data for MySQL Server
- [Customizing MySQL prompt](https://www.mysqltutorial.org/mysql-administration/mysql-prompt/) – Show you how to customize the MySQL prompt to make it more obvious.

## Section 2. System Variables

- [Global variables](https://www.mysqltutorial.org/mysql-administration/mysql-global-variables/) – Show you how to use global variables to change the behaviors of the MySQL server.
- [Session variables](https://www.mysqltutorial.org/mysql-administration/mysql-session-variables/) – Guide you on how to use the session variables to change the settings specific to a database session.
- [Display MySQL version](https://www.mysqltutorial.org/mysql-administration/show-mysql-version/) – Learn how to display the version of the MySQL server.

## Section 3. MySQL Storage Engines

- [Storage Engines](https://www.mysqltutorial.org/mysql-administration/mysql-storage-engines/) – Briefly explain the storage engines and their features.
- [InnoDB](https://www.mysqltutorial.org/mysql-administration/mysql-innodb-storage-engine/) – Learn about the InnoDB storage engine, which is the default storage engine of MySQL.
- [MyISAM](https://www.mysqltutorial.org/mysql-administration/mysql-myisam/) – Show you how to use the MyISAM storage engine for speed and simplicity.
- [MERGE](https://www.mysqltutorial.org/mysql-administration/mysql-merge-storage-engine/) – Learn how to merge multiple MyISAM tables into a MERGE table and manage them as one table.
- [ARCHIVE](https://www.mysqltutorial.org/mysql-administration/mysql-archive-storage-engine/) – Learn how to use the ARCHIVE storage engine to create tables for archiving data with minimal space.
- [BLACKHOLE](https://www.mysqltutorial.org/mysql-administration/mysql-blackhole-storage-engine/) – Use the BLACKHOLE storage engine not to store table data locally.
- [MEMORY](https://www.mysqltutorial.org/mysql-administration/mysql-memory-storage-engine/) – Explain the MEMORY storage engine and how to store table data entirely in the memory.
- [CSV](https://www.mysqltutorial.org/mysql-administration/mysql-csv-storage-engine/) – Learn how to use the CSV storage engine to store table data in CSV files.

## Section 4. InnoDB Storage Engine Configuration

This section introduces the InnoDB architecture and shows you how to configure key InnoDB variables to optimize the MySQL server.

- [InnoDB Architecture](https://www.mysqltutorial.org/mysql-administration/mysql-innodb-architecture/) – introduce the InnoDB architecture and its in-memory and on-disk structures.
- [innodb_buffer_pool_size](https://www.mysqltutorial.org/mysql-administration/innodb_buffer_pool_size/) – configure the buffer pool size to improve MySQL performance.
- [innodb_buffer_pool_instances](https://www.mysqltutorial.org/mysql-administration/innodb_buffer_pool_instances/) – configure multiple buffer pool instances to improve concurrency.
- [innodb_buffer_pool_chunk_size](https://www.mysqltutorial.org/mysql-administration/innodb_buffer_pool_chunk_size/) – configure the chunk size of the buffer pool.
- [innodb_flush_method](https://www.mysqltutorial.org/mysql-administration/innodb_flush_method/) – configure the InnoDB flush method for flushing data from memory to disk.
- [innodb_dedicated_server](https://www.mysqltutorial.org/mysql-administration/innodb_dedicated_server/) – automatically configure InnoDB key variables when MySQL runs on a dedicated server.

## Section 5. MySQL Server Logs

- [Binary Logs](https://www.mysqltutorial.org/mysql-administration/mysql-binary-logs/) – introduce the binary logs concepts and how to manage binary logs effectively.
- [Disable Binary Logs](https://www.mysqltutorial.org/mysql-administration/mysql-disable-binary-logs/) – learn step-by-step how to disable binary logs.
- [Slow Query Logs](https://www.mysqltutorial.org/mysql-administration/mysql-slow-query-logs/) – guide you on how to enable the slow query logs, configure various parameters, and examine the slow queries.

## Section 6. Authentication & Authorizations

- [Create Users](https://www.mysqltutorial.org/mysql-administration/mysql-create-user/) – Learn how to create a new user in MySQL Server.
- [Grant Privileges](https://www.mysqltutorial.org/mysql-administration/mysql-grant/) – Grant privileges to a user account.
- [Revoke Privileges](https://www.mysqltutorial.org/mysql-administration/mysql-revoke/) – Revoke privileges from a user account.
- [Manage Roles](https://www.mysqltutorial.org/mysql-administration/mysql-roles/) – Manage roles in the database.
- [Show Granted Privileges](https://www.mysqltutorial.org/mysql-administration/mysql-show-grants/) – Display privileges associated with a user account or role.
- [Drop users](https://www.mysqltutorial.org/mysql-administration/mysql-drop-user/) – Show how to delete a user account.
- [Change password](https://www.mysqltutorial.org/mysql-administration/change-mysql-user-password/) – Describe ways to change passwords for users.
- [Show Users](https://www.mysqltutorial.org/mysql-administration/mysql-show-users/) – Learn how to show user accounts in MySQL Server.
- [Rename users](https://www.mysqltutorial.org/mysql-administration/mysql-rename-user/) – Rename a user to another.
- [Lock user accounts](https://www.mysqltutorial.org/mysql-administration/mysql-lock-account/) – How to lock user accounts.
- [Unlock](https://www.mysqltutorial.org/mysql-adminsitration/mysql-account-unlock/)[ user accounts](https://www.mysqltutorial.org/mysql-administration/mysql-account-unlock/) – How to unlock user accounts.
- [mysql_config_editor](https://www.mysqltutorial.org/mysql-administration/mysql_config_editor/) – Show you how to use the mysql_config_editor utility to manage passwords securely.

## Section 7. Show commands

- [Show Databases](https://www.mysqltutorial.org/mysql-administration/mysql-show-databases/) – Display all databases in the MySQL Server.
- [Show Tables](https://www.mysqltutorial.org/mysql-administration/mysql-show-tables/) – List all tables in a given database.
- [Show Columns](https://www.mysqltutorial.org/mysql-administration/mysql-show-columns/) – List all columns in a table.
- [Show Processlist](https://www.mysqltutorial.org/mysql-administration/mysql-show-processlist/) – Show the current processes in the MySQL Server.

## Section 8. MySQL Backup & Restore

- [MySQL Backup](https://www.mysqltutorial.org/mysql-administration/mysql-backup/) – Learn about backup types in MySQL, including physical and logical backups.
- [Back up and restore a database](https://www.mysqltutorial.org/mysql-administration/mysql-backup-a-database/) – show you how to back up and restore a single database.
- [Back up and restore all databases](https://www.mysqltutorial.org/mysql-administration/mysql-backup-all-databases/) – learn how to back up and restore all databases on a MySQL server.
- [Back up one or more tables](https://www.mysqltutorial.org/mysql-administration/mysql-dump-one-table/) – guide you on how to back up one or more tables from a database.
- [Restore from a dump file](https://www.mysqltutorial.org/mysql-administration/restore-mysql-dump/) – learn how to restore from a dump file.
- [Perform a Point-in-time Recovery](https://www.mysqltutorial.org/mysql-administration/mysql-point-in-time-recovery/) – show you how to perform a point-in-time recovery that allows you to restore a database to a specified time.

## Section 9. Table maintenance

- [Check tables](https://www.mysqltutorial.org/mysql-administration/mysql-check-table/) – Guide you on how to check one or more tables or views for errors.
- [Analyze tables](https://www.mysqltutorial.org/mysql-administration/mysql-analyze-table/) – Show you how to update the table statistics to help the query optimizer generate the optimal query execution plans.
- [Repair tables](https://www.mysqltutorial.org/mysql-administration/mysql-repair-table/) – Learn how to repair possibly corrupted tables including MyISAM, ARCHIVE, and CSV tales.
- [Optimize tables](https://www.mysqltutorial.org/mysql-administration/mysql-optimize-table/) – Show you how to optimize tables to reclaim wasted storage space & improve table access.
- [mysqlcheck](https://www.mysqltutorial.org/mysql-administration/mysqlcheck/) – Learn how to use the mysqlcheck command-line utility to check, repair, analyze, and optimize MySQL database tables.

## Section 10. Using mysqladmin tool

In this section, you will learn how to use the mysqladmin client tool to perform database administrative tasks.

- [mysqladmin](https://www.mysqltutorial.org/mysql-administration/mysqladmin/) – Show you how to efficiently carry out database administrative tasks using the mysqladmin command-line utility.
- [Create a new database](https://www.mysqltutorial.org/mysql-administration/mysqladmin-create-database/) – show you how to use the mysqladmin to create a new database.
- [Drop a database](https://www.mysqltutorial.org/mysql-administration/mysqladmin-drop-database/) – Guide you on how to use the mysqladmin command to drop an existing database.
- [Change user’s password](https://www.mysqltutorial.org/mysql-administration/mysqladmin-change-user-password/) – Learn how to change the user’s password.

## Section 11. Other MySQL Administration Tasks

- [Execute SQL files](https://www.mysqltutorial.org/mysql-administration/execute-sql-file-in-mysql/) – Guide you on how to use the SOURCE command to execute SQL statements in a file.
- [Load Timezones Data](https://www.mysqltutorial.org/mysql-administration/load-time-zone-tables/) – Learn how to load time zone data into the time zone tables in MySQL server on Windows, macOS, and Linux.
- [Kill a process in MySQL](https://www.mysqltutorial.org/mysql-administration/kill-process-in-mysql/) – Learn how to kill a process in MySQL using the KILL statement.
- [MySQL port](https://www.mysqltutorial.org/mysql-administration/mysql-port/) – show you how to find the port that MySQL is using.
