原文地址：https://www.red-gate.com/simple-talk/databases/mysql/mysql-vs-postgresql-which-open-source-database-is-right-for-you/



# MySQL vs PostgreSQL: Which Open-Source Database is right for you?



When I joined a growing startup company as a backend developer, we were at crossroads choosing between MySQL and PostgreSQL for our backend. Our team was divided: some favored MySQL for its speed and simplicity, while others leaned towards PostgreSQL for its advanced features and robustness.

We initially chose MySQL due to its widespread use and our team’s familiarity with it. Setting it up was straightforward, and it integrated well with our existing infrastructure. MySQL’s performance was impressive, especially for read-heavy operations, which was crucial for our product catalog and user authentication systems.

The ease of replication and the abundance of documentation made it easy to scale our database, ensuring high availability during peak traffic. However, as our business grew, we encountered limitations. We needed complex queries for analytics and reporting, but MySQL’s lack of support for certain SQL standards and its limited JSON functionalities made these tasks challenging.

As developers, we often had to write intricate code to achieve what could be done with simpler SQL in other databases. After a year, we decided to try out PostgreSQL. We wanted more advanced data processing capabilities and support for complex queries. Setting up PostgreSQL required a bit more effort, but the extensive documentation and community support eased the process.

One of the first benefits we noticed was PostgreSQL’s support for advanced SQL features. PostgreSQL’s JSONB support allowed us to store and query semi-structured data efficiently. We also used its full-text search capabilities to implement a solution that scanned millions of records in real time for the marketing team.

![image](https://github.com/user-attachments/assets/9651b368-42c3-43dd-96da-1af62c2dc9ee)

## A Closer Look At The Two Heavy Weights: MySQL vs PostgreSQL

When it comes to relational database management systems (RDBMS), it’s no news that MySQL and PostgreSQL both stand out as two popular choices. They offer powerful features and capabilities, even though they differ greatly in several things such as data type support, schemas, indexing mechanisms, and query optimizations, among others.

MySQL and PostgreSQL are both open-source database systems, this means that they are both available to the public to use and build applications for free. They also both follow the standard Structured Query Language (SQL) procedure for writing queries and provide built-in backup and replication mechanisms.

The purpose of this article is to provide comprehensive documentation on the features and limitations of MySQL and PostgreSQL, that may help you make informed decisions when selecting the right RDBMS.

We will be starting off the first part of this series with an introduction to SQL language, the histories of both MySQL and PostgreSQL, similarities between both databases and briefly highlighting high-level differences that may affect users.

In subsequent parts of this series, we will get into the nitty-gritty details discussing its features and going into details on their high-level differences. Let’s get started!

## SQL Language in MySQL and PostgreSQL

The Structured Query Language, popularly known as SQL, and pronounced as Es-Que-El or Se-Que-El, is the widely accepted standard language used for creating and making changes to relational databases. MySQL and PostgreSQL are both relational databases, hence, they both follow the SQL standard for writing queries. The key standards followed when issuing commands in SQL are:

- **Data Definition Language (DDL)**: This SQL standard allows users to make changes to the structure of their databases. This includes making changes to tables, indexes, views, and constraints.
- **Data Manipulation Language (DML)**: This SQL standard allows users to make changes to the data contained in the database by inserting, updating, and deleting the contents of the data.
- **Data Control Language (DCL)**: Data Control Language statements in SQL help users manage access to the database by using control statements. These statements grant or revoke user permission and privileges to the database.
- **Transaction Control Statements**: SQL supports transaction management. This helps users manage the consistency and integrity of data through transaction management commands like ROLLBACK, which allows users to roll back to a previous state, and COMMIT, which allows users to commit changes to their data.
- **Data Query Language**: SQL supports the use of SELECT statements which allows users to retrieve data from one or more tables. The Data Query Language statement is mostly used with DDL statements.

## A bit of history

In this section I will take a look at a bit of history of both of the RDBMS types to help establish a bit of background as I take you through the differences in them. While there are a lot of similarities, there are quite a few differences, and the history of how they got started.

### MySQL

MySQL, typically pronounced as My-Es-Que-L was founded by Swedes David Axmark, Allan Larsson, and Finnish Michael “Monty” Widenius. The term “My” was coined from Finnish Michael “Monty” Widenius’s daughter’s name, My. The remaining part- SQL stands for Structured Query Language. It was created in Sweden in 1995.

MySQL was first created from the msQL database system based on the Indexed Sequential Access Method, created by IBM. They later discovered that msQL was not fast and flexible enough to meet the requirements at hand, therefore, this led to the creation of a new SQL interface.

In 2008, Sun MicroSystems acquired MySQL AB, the company that developed MySQL. This acquisition raised concerns about the future of MySQL, as Sun was later acquired by Oracle Corporation. Many feared that Oracle’s control over MySQL might hinder its open-source nature, but Oracle made efforts to ensure the continuous commitment to MySQL’s open-source development.

Over the years, MySQL has been able to evolve and has remained intact in being the popular choice for web developers and businesses looking for a reliable database solution.

### PostgreSQL

PostgreSQL pronounced as Post-Gress-Que-El sometimes referred to as just Postgres (and then pronounced Post-Gress or Post-Grey by some people), has a history that dates as far back as the 1980s. It started as a project at the University of California, Berkley which Professor Michael Stonebraker led. It was initially known as Ingres, which is a successor to the Ingres database developed in the 1970s.

Since its inception in 1986, Postgres has gone through several releases and evolutions, from using the POSTQUEL query language and being known as Postgres95, to adopting the SQL query language and changing its name to PostgreSQL. The first formal release of PostgreSQL was introduced in 1996 and the name PostgreSQL was chosen to reflect its support for SQL.

In the year 2000, PostgreSQL became an open-source project that allowed developers worldwide to contribute to its development. It has since then released new features and extensions that support the object-oriented paradigms.

## Similarities between MySQL and PostgreSQL

MySQL and PostgreSQL are not so different when it comes to a few commonalities. Aside from the fact that both MySQL and PostgreSQL follow the standard SQL principles, these popular database choices share several similarities, such as:

- **Relational Database Model**: MySQL and PostgreSQL both adhere to the relational database model standard. They both support using predefined schemas to organize data into tables and support the relationship between these tables.
- **Open-Source**: MySQL and PostgreSQL are both open-source database management systems, meaning their source code is freely available and can be modified.
- **Compliance with the ACID properties**: Both MySQL and PostgreSQL are ACID compliant, that is, they both support Atomicity, Consistency, Isolation, and Durability, ensuring that transactions are reliable and consistent throughout data manipulation.
- **SQL Languages**: MySQL and PostgreSQL both use the SQL standard. This makes it easy for users who are familiar with the SQL language to follow its widely adopted syntax for defining and modifying relational databases and easily transition between either databases.
- **Cross-Platform Compatibility:** MySQL and PostgreSQL are both designed to support various operating systems such as Windows, Linux, and others. This flexibility makes it easy for users to deploy in different environments.
- **Data Types**: Both MySQL and PostgreSQL databases support a wide range of data types such as numeric, text, date/time, and so on. However, some data types are available in PostgreSQL and aren’t in MySQL, and vice versa. This will be discussed later in the series.
- **Replication**: MySQL and PostgreSQL both support replication techniques. This allows data to be duplicated across several servers to avoid loss of data and improve performance. Check out our existing tutorial on using replication techniques in [MySQ](https://www.red-gate.com/simple-talk/blogs/beginners-guide-to-mysql-replication-part-1/)L.
- **Client-Server Architecture**: MySQL and PostgreSQL both use a client-server architecture, allowing clients to connect to the database server over a network. In MySQL, the MySQL server (mysqld) handles all database operations, while clients like MySQL Workbench, the MySQL command-line client, and various programming language libraries (e.g., MySQL Connector/Python) interact with the server. In PostgreSQL, the PostgreSQL server (postgres) manages the database, while clients like pgAdmin, the psql command-line tool, and various programming language libraries (e.g., psycopg2 for Python) communicate with the server.
- **Backup and recovery**: MySQL and PostgreSQL both provide tools and features for backing up and restoring databases, ensuring data safety and disaster recovery. Backup refers to the process of creating a copy of data stored in a database to prevent data loss in case of hardware failures, software bugs, human errors, or other unforeseen incidents while Disaster Recovery (DR) is the process of restoring data and database operations after a catastrophic event such as natural disasters, cyber-attacks, or significant hardware failures. MySQL uses backup tools like Percona XtraBackup, MySQL Enterprise Backup, etc, while PostgreSQL uses pg_basebackup, and Barman, among others.
- **Extensibility**: MySQL and PostgreSQL can both be extended with plugins and extensions to add new functionalities and custom features. An example is the audit plugin used in MySQL, which is used to enable detailed logging of database activities. PostgreSQL also provides the pg_cron plugin which allows scheduled jobs to run directly in the database.

## High-Level Differences in MySQL and PostgreSQL

While MySQL and PostgreSQL both follow the SQL standard and share common similarities, there are notable high-level differences between both databases that users may need to consider before choosing the right database for a particular project. Here are some of the distinctions between MySQL and PostgreSQL.

Some of these terms may not be familiar to you, but in future entries in this series, I will show you how these features are users and implemented, or how you might work around the limitations of the lack of features:

| **Feature**                  | **MySQL**                                                    | **PostgreSQL**                                               |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Data types**               | MySQL supports data types such as, INT, DOUBLE, JSON, CHAR, DATE, TIME, and many more. However, it does not have support for some data types such as XML, instead it stores XML data as a text or blob. It also does not have a dedicated BOOLEAN data type, instead uses the TINYINT to represent Boolean values. | PostgreSQL supports the typical data types as most other RDBMS, including complex types such as intervals, arrays, XML, JSON, and JSONB, amongst others. |
| **ACID Compliance**          | ACID-compliant when using the InnoDB storage engine, ensuring transactions are processed reliably. | Fully ACID-compliant, ensuring robust transaction management across the board. |
| **Syntax variation**         | One example is that MySQL is case insensitive.A bit of syntax that seems unimportant until you need it is that MySQL does not natively support the FULL OUTER JOIN statement. Instead, users need to use a combination of LEFT JOIN and RIGHT JOIN with a UNION to achieve similar functionality.MySQL does not support partial (also known as filtered) indexes (indexes on a portion of the rows in a table). This can be a limitation for optimizing performance on large datasets. | PostgreSQL is case sensitive with comparisons.PostgreSQL supports the FULL OUTER JOIN statement.It also supports partial indexing. |
| **Database Engine Support**  | Supports multiple storage engines such as InnoDB, MyISAM, and others, allowing flexibility in choosing the engine based on the use case. | Uses a single storage engine, which is highly reliable and consistent in performance. |
| **Locking**                  | MySQL’s InnoDB storage engine uses Multi-Version Concurrency Control (MVCC) to handle locking. It supports various types of locks, including shared locks for reads, and exclusive locks for writes. | PostgreSQL also uses Multi-Version Concurrency Control (MVCC) to manage locking but without heavy reliance on additional locking mechanisms. |
| **Inheritance**              | Does not support table inheritance as a feature in the language. | Supports table inheritance, allowing child tables to inherit columns from parent tables. Useful object-oriented database design. |
| **Indexing**                 | Supports B-trees, Full-text indexing, and Spatial indexing (with plugins). | Supports a wide variety of indexing methods including B-trees, Hash, GIN (Generalized Inverted Index), GiST (Generalized Search Tree), and more |
| **Geospatial Data**          | Limited geospatial support. It can be used through plugins like Spatial. | Advanced geospatial support with the PostGIS extension, making PostgreSQL a powerful database for geographic information systems (GIS). |
| **Operating System Support** | Compatible with Windows, macOS, Linux, and Unix, offering broad platform support. | Also compatible with Windows, macOS, Linux, and Unix.PostgreSQL’s deeper integration with Unix-like systems (such as Linux) is often noted, with many features and optimizations specific to these platforms. |
| **Full-text search**         | MySQL provides a native full-text search.                    | PostgreSQL’s supports full-text-searches, but the approach differs in implementation. It involves the use of specialized data types. |
| **Handling of VIEWS**        | You cannot update a view that uses the GROUP BY, DISTINCT, UNION, HAVING clauses, or Aggregate functions. You also cannot update a view that contains subqueries in its select statement | PostgreSQL tends to have more flexible and sophisticated options for updating views compared to MySQL. |

## Conclusion

We’ve taken a closer look at two heavyweights in the field- MySQL and PostgreSQL. We’ve delved into their histories and also explored both databases briefly.

In the upcoming articles of this series, we will go into a detailed and comprehensive comparison between MySQL and PostgreSQL, dissect their high-level differences, strengths and weaknesses, and suitability for various use cases, to help you make an informed decision on which database might be the right fit for you. Hope to see you in the next part of this series!
