# MySQL overview and application framework
1 Introduction to MySQL

MySQL is a relational database management system, which is developed by MySQL AB of Sweden and belongs to Oracle. MySQL is one of the most popular relational database management systems. In terms of Web applications, MySQL is one of the best RDBMS (Relational Database Management System) applications.

MySQL is a relational database management system. Relational databases store data in different tables instead of putting all data in a large warehouse, which increases speed and flexibility.

The SQL language used by MySQL is the most commonly used standardized language for accessing databases. MySQL software adopts the dual authorization policy, which is divided into community version and commercial version. Because of its small size, fast speed, low total cost of ownership, especially the open source, MySQL is generally selected as the website database for the development of small, medium and large websites.

2 MySQL logical architecture

MySQL's logical architecture can be roughly divided into three layers: the client layer, the server layer, and the storage engine layer.

(1) Layer 1: client (server layer)

It mainly deals with connection processing, authorization authentication, security assurance, etc.

(2) Layer 2: Server layer

It covers most of MySQL's core service functions, including query parsing, analysis, optimization, caching and all built-in functions (such as date, time, math and encryption functions). Stored procedures, triggers, views and other cross storage engine functions are also implemented in this layer.

Server layer basic components:

Connector: We use the database. The first step is to connect to the database. The connector is responsible for establishing a connection with the client, obtaining permissions, maintaining and managing the connection query cache: when executing the query statement, it will first query the cache to verify whether the SQL has been executed. If there is a SQL cache, it will be directly returned to the client. If there is no hit, the subsequent operations will be executed; (MySQL 8.0 is deleted)

Analyzer: If the cache is not hit, the SQL statement will go through the analyzer, which is mainly divided into two steps: lexical analysis and syntax analysis. First, see what the SQL statement needs to do, and then check whether the SQL statement syntax is correct;

Optimizer: the optimizer optimizes queries, including rewriting queries, determining the read/write order of tables, selecting appropriate indexes, and generating execution plans. The main operations of the optimizer include: deciding which index to use when there are multiple indexes in a table; When a statement has multiple table joins, determine the join order of each table;

Executor: Before execution, the user will be checked for permission. If there is no permission, an error message will be returned. If there is permission, the engine interface will be called according to the execution plan, and the result will be returned.

(3) Layer 3: storage engine layer

It is mainly responsible for data storage and extraction. The server layer interacts with the storage engine layer through APIs. The server communicates with the storage engine through APIs. These interfaces mask the differences between different storage engines, making the differences transparent to the upper layer query process. In addition to the InnoDB defined by the foreign key, the storage engine will not parse SQL, and different storage engines will not communicate with each other, but simply respond to the request of the upper layer server.

3 MySQL statement execution process

(1) The execution process of the query statement is as follows:

Permission verification, query cache, analyzer, optimizer, permission verification, executor, engine. First, check the permission. If there is no permission, an error is returned; If the cache is enabled, it will check whether the cache has the corresponding result of the sql (the cache storage form is key vlaue, where key is the executed sql and value is the corresponding value). If the cache is enabled and there is a mapping of the sql, the result will be returned directly; Lexical analysis and grammatical analysis. Extract the table name and query criteria, and check whether the syntax is incorrect; The optimizer generates execution plans, selects indexes, and selects the optimal execution scheme; Then come to the executor, open the table to call the storage engine interface, judge whether the query conditions are met line by line, put them in the result set, and finally return them to the client; If an index is used, the filtered rows will also be filtered according to the index.

(2) Update statement execution process

The execution process of the update statement is as follows: analyzer, permission verification, executor, engine, redo log (prepare status), binlog, and redo log (commit status). The cache will be used if there is a cache. Get the query results, update the contents, and then call the engine interface to write the updated data. The Innodb engine saves the data in memory and records the redo log. At this time, the redo log enters the prepare state. After receiving the notification, the actuator records the binlog, calls the engine interface, and submits the redo log to the commit status. Update completed.

4 Differences between MySQL and SQL Server

(1) Open source

MySQL is an open source relational database management system (RDBMS); SQL Server is not open source, but commercial.

(2) Procedure

MySQL is mainly programmed in C and C++programming languages. SQL Server is mainly programmed in C++, but there are some parts in C language.

(3) Use platform

SQL Server only supports Linux and Windows platforms, mainly for Net application or Windows project. In contrast, MySQL supports many platforms, mainly for PHP projects or applications.

(4) Grammar

MySQL syntax is relatively complex; SQL Server syntax is simpler and easier to use.

(5) Execute Query

In MySQL, once the query is executed, you cannot cancel the query halfway. In SQL Server, queries can be canceled halfway after execution.

(6) Storage Engine

In MySQL, there are multiple storage engines that allow developers to use engines for tables more flexibly based on performance. InnoDB is a popular storage engine. SQL Server can only use one or only one storage engine.

(7) Backup

When using MySQL, developers must back up data by extracting all data into SQL statements. Due to the execution of multiple SQL statements, data recovery is time-consuming. SQL Server does not block the database when backing up data, which enables users to back up and restore large amounts of data without spending additional time and effort.

(8) Security

Both enterprise database systems are designed as binary collections. MySQL enables developers to operate database files through binary files at runtime. It even allows other processes to access and manipulate database files at runtime. However, SQL Server does not allow any process to access or manipulate its database files or binaries. It requires users to execute specific functions or operation files by running instances. Therefore, hackers cannot directly access or manipulate data. Design rules make SQL Server more secure than MySQL.

(9) Supported programming languages

MySQL and SQL Server both support multiple programming languages. They all support PHP, C++, Python, Visual Basic, etc. But MySQL also supports Perl, Scheme, Haskel, Eiffel and other programming languages. MySQL is more popular because it supports many programming languages.

(10) Filtering

MySQL allows users to filter out tables, rows and users in various ways, but it requires users to filter out tables, rows or users according to individual databases. When filtering data, developers must run multiple queries to filter database tables separately. SQL Server uses row based filtering, and row based filtering options filter the data on the database by database. The filtered data is stored in a separate distribution database.

5 Index function

An index is a special file (the index on the InnoDB data table is a component of the table space). It contains reference pointers to all records in the data table. Indexes are not universal. Indexes can speed up data retrieval operations, but slow down data modification operations. The index must be refreshed every time the data record is modified. In order to remedy this defect to some extent, many SQL commands have a DELAY_ KEY_ WRITE items. This option temporarily prevents MySQL from refreshing the index after inserting a new record and modifying an existing record. The index will be refreshed after all records are inserted/modified. When many new records need to be inserted into a data table, DELAY_ KEY_ The WRITE option will be very useful. In addition, the index also takes up considerable space on the hard disk. Therefore, you should only index the data columns that are most frequently queried and sorted. Note that if a data column contains a lot of duplicate content, indexing it will not have much practical effect.

(1) Index of InnoDB data table

Compared with InnoDB data tables, indexes are much more important to InnoDB data tables. On the InnoDB data table, the index will not only play a role in searching data records, but also be the basis of the data row level locking mechanism. "Data row level locking" means that the individual records being processed are locked during the execution of transaction operations to prevent other users from accessing them. This locking will affect (but not limited to) the SELECT, LOCKINSHAREMODE, SELECT, FORUPDATE commands, and INSERT, UPDATE, and DELETE commands. For efficiency reasons, data row level locking of InnoDB data tables actually occurs on their indexes, not on the data tables themselves. Obviously, the data row level locking mechanism can only work when the relevant data table has a suitable index for locking.

(2) Restrictions

If there is an inequality sign (WHERE colon!=) in the query condition of the WHERE clause, MySQL cannot use the index. Similarly, if a function (WHERE DAY (column)=) is used in the query condition of the WHERE clause, MySQL cannot use the index. In JOIN operations (when data needs to be extracted from multiple data tables), MySQL can only use indexes when the data types of primary keys and foreign keys are the same.

If the comparison operators LIKE and REGEXP are used in the query conditions of the WHERE clause, MySQL can use the index only if the first character of the search template is not a wildcard. For example, if the query condition is LIKE 'abc%', MySQL will use the index; If the query condition is LIKE '% abc', MySQL will not use the index.

In the ORDER BY operation, MySQL uses indexes only when the sort condition is not a query condition expression. (Even so, in queries involving multiple data tables, even if indexes are available, those indexes have little effect in speeding up ORDER BY.). If a data column contains many duplicate values, even if it is indexed, it will not work well. For example, if a data column contains only "0/1" or "Y/N" equivalents, it is unnecessary to create an index for it.

6 Index Category

(1) General Index

The task of a normal index (an index defined by the keyword KEY or INDEX) is to speed up data access. Therefore, indexes should be created only for the data columns in the query condition (WHERE column=) or sort condition (ORDER BY column) that most often occur. Whenever possible, you should select the most tidy and compact data column (such as an integer type data column) to create an index.

(2) Index

A normal index allows the indexed data column to contain duplicate values. For example, because a person may have the same name, the same name may appear twice or more times in the same Employee Profile data table. If it can be determined that a data column will only contain different values, the keyword UNIQUE should be used to define it as an index when creating an index for this data column. The advantages of this approach are: First, it simplifies MySQL's management of the index, which makes the index more efficient; Second, when a new record is inserted into the data table, MySQL will automatically check whether the value of this field of the new record has appeared in this field of a record; If yes, MySQL will refuse to insert that new record. In other words, indexes can ensure the uniqueness of data records. In fact, on many occasions, people create indexes not to improve access speed, but to avoid duplication of data.

(3) Primary index

It has been repeatedly emphasized that an index must be created for the primary key field, which is called the "primary index". The difference between primary indexes is that the former is defined with PRIMARY instead of UNIQUE.

(4) Foreign Key Index

If you define a foreign key constraint for a foreign key field, MySQL will define an internal index to help you manage and use the foreign key constraint in the most efficient way.

(5) Composite index

Indexes can cover multiple data columns, such as INDEX (columnA, columnB) indexes. The feature of this index is that MySQL can selectively use one such index. If the query operation only needs to use an index on the columnA data column, you can use the composite index INDEX (columnA, columnB). However, this usage only applies to data column combinations that rank first in a composite index. For example, INDEX (A, B, C) can be used as an index of A or (A, B), but not B, C or (B, C).
