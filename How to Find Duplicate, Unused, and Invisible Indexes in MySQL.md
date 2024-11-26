原文地址：https://www.percona.com/blog/duplicate-redundant-and-invisible-indexes/



# How to Find Duplicate, Unused, and Invisible Indexes in MySQL

April 2, 2024

[Arunjith Aravindan](https://www.percona.com/blog/author/arunjith-aravindan)

*This blog was originally published in January 2023 and was updated in April 2024.*

MySQL index is a data structure used to optimize the performance of database queries at the expense of additional writes and storage space to keep the index data structure up to date. It is used to quickly locate data without having to search every row in a table. Indexes can be created using one or more columns of a table, and each index is given a name. Indexes are especially useful for queries that filter results based on columns with a high number of distinct values.

Indexes are useful for our queries, but duplicate, redundant, and unused indexes reduce performance by confusing the optimizer with query plans, requiring the storage engine to maintain, calculate, and update more index statistics, as well as requiring more disk space.

Since MySQL 8.0, indexes can be marked as invisible. In this blog, I will detail how to detect duplicate and unused indexes, as well as the new feature of invisible indexes and how it can help with index management.



## How to find the duplicate MySQL indexes

[pt-duplicate-key-checker](https://docs.percona.com/percona-toolkit/pt-duplicate-key-checker.html) is a command-line tool from [Percona Toolkit](https://percona.com/software/database-tools/percona-toolkit) that scans a MySQL database and identifies tables that have duplicate indexes or primary keys. It can help identify potential problems with the schema and provide guidance on how to fix them.

For each duplicate key, the tool prints a DROP INDEX statement by default, allowing you to copy-paste the statement into MySQL to get rid of the duplicate key.



### For example:

# ![image-20241126152235896](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20241126152235896.png)



## How to find the unused MySQL indexes

Unused indexes are indexes that are created in a database but are not being used in any queries. These indexes take up space in the database and can slow down query performance if they are not maintained. Unused indexes should be identified and removed to improve the performance of the database.

We can use the sys.schema [unused indexes view](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-unused-indexes.html) to see which indexes have not been used since the last time the MySQL server was restarted.



### For example:

![image-20241126152306291](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20241126152306291.png)

Since the index idx_first_name was used to retrieve the records, it is not listed.

![image-20241126152327747](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20241126152327747.png)



## Use invisible indexes before deleting MySQL indexes

In MySQL 8.0, there is a feature that allows you to have an invisible index. This means that an index is created on a table, but the optimizer does not use it by default. By using this feature, you can test the impacts of removing an index without actually dropping it. If desired, the index can be made visible again, therefore avoiding the time-consuming process of re-adding the index to a larger table.

The SET_VAR(optimizer_switch = ‘use_invisible_indexes=on’) allows the invisible index to be used for specific application activities or modules during a single query while preventing it from being used across the entire application.

Let’s look at a simple example to see how it works.

![image-20241126152348215](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20241126152348215.png)

The indexes are set to visible by default, and we may use the ALTER TABLE table name ALTER INDEX index name to make it INVISIBLE/VISIBLE.

To find the index details, we can use “SHOW INDEXES FROM table;” or query the INFORMATION SCHEMA.STATISTICS.

![image-20241126152409209](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20241126152409209.png)

The query cannot utilize the index on the email column since it is invisible.

![image-20241126152427439](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20241126152427439.png)

The SET VAR optimizer hint can be used to temporarily update the value of an optimizer switch and enable invisible indexes for a single query only, as shown below:

![image-20241126152449251](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20241126152449251.png)

Please refer to the documentation for more information on [invisible indexes](https://dev.mysql.com/doc/refman/8.0/en/invisible-indexes.html).



## The Benefits of Removing Duplicate and Unused Indexes in MySQL

Removal of duplicate and unnecessary indexes is recommended to prevent performance degradation in high concurrent workloads with less-than-ideal data distribution on a table. Unwanted keys take up unnecessary disc space, which can cause overhead to DML and read queries. To test the effects of dropping an index without fully removing it, it can be made invisible first.

However, there are additional performance advantages to consider, one being enhanced query performance. When a MySQL database has fewer indexes, the query optimizer has a simpler decision-making process. With fewer indexes to evaluate, the optimizer can more quickly determine the most efficient way to execute a query. This streamlined decision-making process reduces the CPU and memory resources required during query execution, leading to faster response times and lower system load.

Another significant benefit is reducing storage space. Indexes, while useful for speeding up data retrieval, occupy physical disk space. When indexes are no longer used or have duplicates that serve the same purpose, they unnecessarily consume space that could otherwise be utilized for storing more valuable data or improving the I/O efficiency of the database system. By cleaning up these redundant indexes, you can potentially reduce the size of the database, leading to cost savings in terms of storage and also enhancing the performance of backup operations.

Improved index maintenance is an often overlooked yet crucial advantage. Maintenance tasks, such as index rebuilding or reorganizing, become simpler and quicker when there are fewer indexes to manage. This not only reduces the maintenance window but also minimizes the performance impact during these operations. Regular maintenance is smoother and less disruptive, which is especially important for 24/7 operational databases where downtime is costly. Simplifying the index structure can also aid in more predictable performance and easier troubleshooting and tuning of the database.

Our MySQL Performance Tuning guide covers the critical aspects of MySQL performance optimization. It will help you ensure your databases run smoother, faster, and more reliably. Get it today:



## FAQs

### Why are unused indexes in MySQL bad?

Unused indexes in MySQL can negatively impact overall database performance because they consume system resources, expensive disk space, and extra processing during data manipulation operations like inserts, updates, and deletes.

### Why are duplicate indexes in MySQL bad?

Duplicate indexes in MySQL result in wasted disk space and additional overhead during write operations, as each index must be updated. This does not provide any benefit and complicates the query optimization process, potentially leading to suboptimal execution plans.

### How do you make an index invisible?

To make an index invisible in MySQL, you can alter the index’s visibility property. This is done using the SQL command ALTER TABLE table_name ALTER INDEX index_name INVISIBLE;. Making an index invisible allows you to test the impact of its removal without actually dropping it.

### What are the advantages of making an index invisible?

In MySQL, you can assess the impact of deleting an index without completely deleting it by making it invisible. This functionality aids in performance testing by enabling the database to disregard the index during query optimization without compromising the integrity of the data or the physical structure. This is especially helpful during performance tuning to ensure that deleting an index doesn’t adversely impact query performance.
