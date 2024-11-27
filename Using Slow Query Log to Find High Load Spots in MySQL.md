原文地址：https://www.percona.com/blog/identifying-high-load-mysql-slow-query-log-pt-query-digest/



# Using Slow Query Log to Find High Load Spots in MySQL

*This post was originally published in October 2018 and was updated in March 2023.*

[**pt-query-digest**](https://www.percona.com/doc/percona-toolkit/LATEST/pt-query-digest.html) is one of the most commonly used tools for query auditing in MySQL*.* By default, pt-query-digest reports the top ten queries consuming the most amount of time inside MySQL. A query that takes more time than the set threshold for completion is considered slow, but it’s not always true that tuning such queries makes them faster. Sometimes, when resources on the server are busy, it will impact every other operation on the server, and so will impact queries too. In such cases, you will see the proportion of slow queries going up. That can also include queries that work fine in general.

This article explains a small trick to identify such spots using pt-query-digest and the slow query log. pt-query-digest is a component of [Percona Toolkit](https://www.percona.com/software/database-tools/percona-toolkit), open source software that is free to download and use.

## Overview of the MySQL Slow Query Log

The MySQL slow query log is a feature of MySQL that logs information about queries that take longer than a specified threshold to execute. It is a useful tool for identifying performance issues in MySQL databases.

When enabled, the slow query log records information about each slow query, including query text, execution time, and query timestamp. This information can be used to analyze and optimize database performance.

Once enabled, you can analyze the log file using various tools, such as the MySQL command-line tool, [Percona Toolkit, ](https://www.percona.com/software/database-tools/percona-toolkit)or [Percona Monitoring and Management](https://www.percona.com/software/database-tools/percona-monitoring-and-management) (PMM).

## How to enable the slow query log in MySQL

1. Open your MySQL configuration file.
2. Find the [mysqld] section in the configuration file.
3. Add the following lines to the [mysqld] section:

```
slow_query_log = 1slow_query_log_file = /path/to/your/log/file.loglong_query_time = 5
```

 

Here, “slow_query_log” enables the slow query log, “slow_query_log_file” specifies the location and name of the file, and “long_query_time” specifies the number of seconds (example, you can enter your own number) that a query must take to be considered “slow” and logged.

1. Save the configuration file and restart the MySQL server.

After enabling the slow query log, MySQL will log all queries that take longer than the “long_query_time” value you entered.

To verify that the slow query log is working correctly, run a slow query to test if the slow query log is working. If the query takes longer than the specified time (in this example, five seconds), check the slow query log file to verify that it contains the slow query.

### Slow query log parameters

To enable the MySQL slow query log, you need to modify the MySQL server configuration file (usually `my.cnf` or `my.ini`) and set the following parameters:

1. `slow_query_log`: This specifies whether the slow query log is enabled or disabled. The default value is `OFF`. To enable the slow query log, set it to `ON`.
2. `slow_query_log_file`: This parameter specifies the name and location of the file where slow queries will be logged. The default value is `hostname-slow.log` and it is stored in the data directory of the MySQL server.
3. `long_query_time`: This specifies the amount of time a query must take to be considered “slow” and logged in the slow query log. The default value is `10` seconds. You can adjust this value.
4. `log_queries_not_using_indexes`: This parameter specifies whether to log queries that are not using indexes. If you want to log queries that are not using indexes, set it to `ON` as the default value is `OFF`.
5. `log_slow_admin_statements`: This parameter specifies whether to log administrative statements that take a long time to execute. The default value is `OFF`. If you want to log slow administrative statements, set it to `ON`.

### Slow query log contents

The slow query log provides information that helps identify queries that are taking too long and can be optimized. The types of slow query log statements include the following:

1. **Query timestamp**: Date and time the query was executed.
2. **Query execution time**: The time taken to execute the query.
3. **Query ID**: A unique identifier assigned to each query to match queries with corresponding log entries.
4. **Query user**: The user name of the person who executed the query.
5. **Query database**: The database name on which the query was executed.
6. **Query text**: The SQL text of the executed query.
7. **Query result**: How many rows were returned by the query.
8. **Query error message**: If the query fails to execute, this is the error message associated with it.

## How to read the MySQL slow query log

By analyzing the MySQL slow query log, you can identify queries that are taking too long to execute and causing performance issues. In order to read the slow query log, follow these steps:

 

1. Locate and open the slow query log file, usually located in the MySQL data directory.
2. Each log entry coincides with a query that exceeded the threshold originally set in the MySQL configuration. The entry contains such information as the query timestamp, query execution time, and the user who executed the query. This information can then be used to identify the specific queries causing performance issues.
3. After identifying the slow queries, you can optimize them by adding indexes, rewriting queries, or tuning the MySQL configuration. Using tools such as “explain” can help in analyzing the execution plan of the queries to identify areas that require optimization.

## Some sample data

Let’s have a look at sample data in Percona Server for MySQL 5.7. The slow query log is configured to capture queries longer than ten seconds with no limit on the logging rate, which is generally considered for throttling the IO that comes while writing slow queries to the log file.

![image](https://github.com/user-attachments/assets/94d33a48-bc40-4dc7-8ffd-73d3187f355c)

When I run **pt-query-digest**, I see in the summary report that **80%** of the queries have come from just three query patterns.

```
# Profile
# Rank Query ID                      Response time    Calls R/Call   V/M  
# ==== ============================= ================ ===== ======== =====
#    1 0x7B92A64478A4499516F46891... 13446.3083 56.1%   102 131.8266  3.83 SELECT performance_schema.events_statements_history
#    2 0x752E6264A9E73B741D3DC04F...  4185.0857 17.5%    30 139.5029  0.00 SELECT table1
#    3 0xAFB5110D2C576F3700EE3F7B...  1688.7549  7.0%    13 129.9042  8.20 SELECT table2
#    4 0x6CE1C4E763245AF56911E983...  1401.7309  5.8%    12 116.8109 13.45 SELECT table4
#    5 0x85325FDF75CD6F1C91DFBB85...   989.5446  4.1%    15  65.9696 55.42 SELECT tbl1 tbl2 tbl3 tbl4
#    6 0xB30E9CB844F2F14648B182D0...   420.2127  1.8%     4 105.0532 12.91 SELECT tbl5
#    7 0x7F7C6EE1D23493B5D6234382...   382.1407  1.6%    12  31.8451 70.36 INSERT UPDATE tbl6
#    8 0xBC1EE70ABAE1D17CD8F177D7...   320.5010  1.3%     6  53.4168 67.01 REPLACE tbl7
#   10 0xA2A385D3A76D492144DD219B...   183.9891  0.8%    18  10.2216  0.00 UPDATE tbl8
#      MISC 0xMISC                     948.6902  4.0%    14  67.7636   0.0 <10 ITEMS>
```

Query #1 is generated by the qan-agent from PMM and runs approximately once a minute. These results will be handed over to PMM Server. Similarly, queries **#2** & **#3** are pretty simple. I mean, they scan just one row and will return either zero or one row. They also use indexing, which makes me think that this is not because of something just within MySQL. I wanted to know if I could find any common aspect of all these occurrences.

Let’s take a closer look at the queries recorded in the slow query log.

![image](https://github.com/user-attachments/assets/c02ad032-e3e9-44af-b2b0-c09b40e23d65)

Now you can see two things.

- All of them have the same Unix timestamp.
- They all spent over 70% of their execution time waiting for some lock.

## Analyzing MySQL Slow Query Log from pt-query-digest

Now I want to check if I can group the count of queries based on their time of execution. If there are multiple queries at a given time captured into the slow query log, time will be printed for the first query but not all. Fortunately, in this case, I can rely on the Unix timestamp to compute the counts. The timestamp gets captured for every query. Luckily, without a long struggle, a combination of grep and awk utilities displayed what I wanted to display.

![image](https://github.com/user-attachments/assets/bd1084cd-8edb-4211-be70-66a182d10015)

You can use the command below to check the regular date time format of a given timestamp. So, Oct 3, 05:32 is when there was something wrong on the server:

![image](https://github.com/user-attachments/assets/71396d2f-ca77-4cdc-9bf6-5abfd5f7c961)

Query tuning can be carried out alongside this, but identifying such spots helps avoid spending time on query tuning where badly written queries are not the problem. Having said that, from this point, further troubleshooting may take different sub-paths such as checking log files at that particular time, looking at CPU reports, reviewing past [pt-stalk](https://www.percona.com/doc/percona-toolkit/LATEST/pt-stalk.html) reports if set up to run in the background, and dmesg, etc. This approach is useful for identifying at what time (or time range) MySQL was more stressed just using a slow query log when no robust monitoring tools, like [Percona Monitoring and Management (PMM)](https://www.percona.com/software/database-tools/percona-monitoring-and-management), are deployed.

## Using PMM to monitor queries

If you have PMM, you can review Query Analytics to see the topmost slow queries, along with details like execution counts, load, etc. Below is a sample screen copy for your reference:

![image](https://github.com/user-attachments/assets/a97fcb0c-56e3-4648-81ef-90a9245ff216)

**NOTE**: If you use [Percona Server for MySQL](https://www.percona.com/software/mysql-database/percona-server), a slow query log can report time in microseconds. It also supports extended logging of other statistics about query execution. These provide extra power to see the insights of query processing. You can see more information about these options [here.](https://docs.percona.com/percona-server/8.0/slow-extended.html)

For more tips and tools on MySQL slow query log, visit our other blogs [here](https://www.percona.com/blog/mysql-slow-query-log-tools-and-tips/) and [here](https://www.percona.com/blog/percona-server-for-mysql-highlights-extended-slow-query-logging/).

## FAQs

### What is the default location of MySQL Slow Query Log?

In versions before MySQL 5.7.5, generally, the default location of the slow query log is the MySQL data directory, and the file name is “hostname-slow.log.”

For versions later than MySQL 5.7.5, the default location of the slow query log is also the MySQL data directory, and the file name is “slow.log.”

The data directory location can vary depending on the operating system, but it is typically “/var/lib/mysql” on Linux systems.

### How can I exclude certain queries from the slow query log?

To exclude certain queries from the slow query log, you can use the MySQL configuration option `slow_query_log` and specify a value of `0` to completely disable the slow query log. To meet specific criteria, you can also enable the log only for those queries, using the `slow_query_log` and `long_query_time` options.

To prevent slow queries that do not use indexes from being logged, you can use the `log_queries_not_using_indexes` option. Additionally, the `min_examined_row_limit` option excludes queries that examine fewer rows than a specified threshold.

### Can MySQL Slow Query Log affect database performance?

Enabling the MySQL slow query log can have a small impact on database performance, as it involves additional I/O operations to write the slow query log to disk. However, the benefits often outweigh any costs.

MySQL logs those queries that take longer than 10 seconds to execute by default. This limit can be adjusted using the `long_query_time` variable. However, if you set it too low, it can cause an excessive number of queries to be logged, which can increase disk I/O and affect overall performance.

### Can I use third-party tools to analyze MySQL Slow Query Log?

Yes, you can. A few examples include the following:

- [Percona Toolkit](https://www.percona.com/software/database-tools/percona-toolkit): This is a collection of command-line tools that can help you manage and analyze MySQL databases. The toolkit includes a tool called “pt-query-digest” that can analyze the Slow Query Log and produce a report that shows which queries are the slowest.
- [MySQL Workbench](https://www.mysql.com/products/workbench/): It includes a performance dashboard displaying information about slow queries, including the query text, execution time, and the number of times it was executed.

Others are available, and their use will depend on your specific needs.

### How do I optimize MySQL performance for specific queries?

- To optimize query performance, it is crucial to **use indexes**. Ensure that the columns utilized in the WHERE, JOIN, and ORDER BY clauses of your query are suitably indexed.
- **Analyze the query** and simplify it. Consider using subqueries or JOINs to replace multiple SELECT statements.
- Configure the **built-in caching mechanisms**, such as query cache and InnoDB buffer pool.
- Ensure that the server has sufficient memory, CPU, and disk space to manage the workload. Modify the **MySQL configuration parameters** to optimize the utilization of resources.
- Utilize **profiling tools** (*as mentioned above*) to monitor query performance and detect bottlenecks.
- MySQL’s built-in query optimizer analyzes and optimizes your queries to determine the most efficient means for executing queries. Make it active to achieve the best possible performance.

**Percona Distribution for MySQL is the most complete, stable, scalable, and secure open source MySQL solution available, delivering enterprise-grade database environments for your most critical business applications… and it’s free to use!**



