原文地址：https://www.percona.com/blog/mysql-key-performance-indicators



# MySQL Performance Monitoring and Key Performance Indicators (KPI) With PMM

April 2, 2024

[Kedar Vaijanapurkar](https://www.percona.com/blog/author/kedar-vaijanapurkar)

*This blog was originally published in June of 2023 and updated in April of 2024.*

As a MySQL database administrator, keeping a close eye on the performance of your MySQL server is crucial to ensure optimal database operations. A monitoring tool like [Percona Monitoring and Management](https://www.percona.com/software/database-tools/percona-monitoring-and-management) (PMM) is a popular choice among open source options for effectively monitoring MySQL performance.

However, simply deploying a monitoring tool is not enough; you need to know which Key Performance Indicators (KPIs) to monitor to gain insights into your MySQL server’s health and performance.

In this blog, we will explore various MySQL KPIs and performance metrics that are basic and essential to track using monitoring tools like PMM. We will also discuss related configuration variables to consider that can impact these KPIs, helping you gain a comprehensive understanding of your MySQL server’s performance and efficiency.

Let’s dive in and learn how (and what) to effectively monitor MySQL performance, along with examples from PMM, by understanding the critical KPIs to watch for.

## Database uptime and availability

Monitoring database uptime and availability is crucial as it directly impacts the availability of critical data and the performance of applications or websites that rely on the MySQL database.

PMM monitors the MySQL uptime:

![image](https://github.com/user-attachments/assets/9a75bddb-9b3e-4bb5-980a-aa9008d75aa0)

![image](https://github.com/user-attachments/assets/1de39c48-5bb1-4048-b4d5-220e24fec4a2)

Indicates the amount of time (seconds) the MySQL server has been running since the last restart.

## Query performance

Query performance is a key performance indicator (KPI) in MySQL, as it measures the efficiency and speed of query execution. This includes metrics such as query execution time, the number of queries executed per second, and the utilization of query cache and adaptive hash index.

Too many slow queries, inefficient queries, or long-running queries can indicate potential performance issues that may negatively impact the database’s performance and why monitoring query performance is crucial.

On PMM, we have the following panels showing the gist of query execution and summarizing the pattern. This is not an exhaustive list but an example of what we can watch for.

> Related Learning: [MySQL Query Performance Troubleshooting](https://www.percona.com/blog/mysql-query-performance-troubleshooting-resource-based-approach/)

### Number of slow queries recorded

![image](https://github.com/user-attachments/assets/8adfbe45-c1b1-4017-ab00-97ed873f04bf)

Select types, sorts, locks, and total questions against a database

![image](https://github.com/user-attachments/assets/90980011-0675-4c61-a4a4-79c72bf87e84)

Command counters and handlers used by queries give an overall traffic summary

![image](https://github.com/user-attachments/assets/03972247-5cf2-4cf8-8667-aa7f252b7520)

Along with this, PMM also comes with **Query Analytics** giving much detailed information about queries getting executed.

### Query Analytics

![image](https://github.com/user-attachments/assets/a4973d28-81ec-4f0d-9208-c802e6bc99aa)

You should watch out for status variables for these, for example:

![image](https://github.com/user-attachments/assets/c590a39d-4ffd-45c6-82f6-b274a04c11bf)

Some of the configuration variables to note for:

- **slow_query_log**: Enables / Disables slow query log.
- **long_query_time**: Defines the threshold for query execution time, and queries taking longer than this threshold are considered slow and logged for further analysis
- **innodb_buffer_pool_size**: Sets the size of the InnoDB buffer pool.
- **query cache**: Disable (query_cache_size: 0, query_cache_type:OFF)
- **innodb_adaptive_hash_index**: Check adaptive hash index usage to determine its efficiency.

> Related Blog: [MySQL Performance Tuning 101](https://www.percona.com/blog/mysql-101-parameters-to-tune-for-mysql-performance/)

## Indexing efficiency

Monitoring [indexing efficiency in MySQL](https://www.percona.com/blog/mysql-indexing-101-a-challenging-single-table-query/) involves analyzing query performance, using EXPLAIN statements, utilizing performance monitoring tools, reviewing error logs, performing regular index maintenance, and benchmarking/testing. This KPI is also directly related to Query Performance and helps improve it.

There are multiple tables MySQL internal system manages that come in handy, identifying the inefficient indexes, to name a few: sys.schema_unused_indexes, and information_schema.index_statistics.

The pt-duplicate-key-checker is another [Percona Toolkit](https://www.percona.com/software/database-tools/percona-toolkit) utility to eliminate duplicate indexes and improve indexing efficiency.

## Connection usage

Connection usage is a critical key performance indicator (KPI) in MySQL, as it involves tracking the number of concurrent connections to the MySQL server and ensuring that it does not exceed the allowed limit. Improper configuration of connection settings can have catastrophic effects on the production database, resulting in it becoming inaccessible during connection spikes or even [experiencing out-of-memory (OoM)](https://www.percona.com/blog/what-to-do-when-mysql-runs-out-of-memory-troubleshooting-guide/) kills of the MySQL daemon.

By monitoring and managing connection usage, you can proactively identify and address potential issues such as connection spikes, resource exhaustion, or improper configuration that may impact the availability and performance of the MySQL database.

### PMM captures the MySQL connection matrix

![image](https://github.com/user-attachments/assets/3c34fc81-823e-49e7-b30a-cd2ae9da7391)

It is important to provide appropriate max_connections and also monitor max_used_connections, max_used_connections_time to review the history of max usage to estimate the traffic.

Implementing appropriate connection pooling or choosing appropriate connection settings can help optimize resource utilization and reduce downtime or performance degradation. Hint Hint, ProxySQL helps.

## CPU and memory usage

Monitor CPU and memory utilization of the MySQL server to ensure efficient resource utilization. It is advisable to have a dedicated production MySQL Server that can independently claim the system resources as needed. That said, it should also be monitored for usage, which will exhibit the traffic pressuring them.

### PMM dashboard – CPU utilization and memory details

![image](https://github.com/user-attachments/assets/448a643f-ebff-4184-b69a-00e7ac1f0f3c)

![image](https://github.com/user-attachments/assets/00b13eed-20d2-4a93-a75d-a136163d39f8)

The most effective memory configuration variable, innodb_buffer_pool_size, sets the size of the InnoDB buffer pool. 

Another related variable, innodb_buffer_pool_instances, determines the number of buffer pool instances for the InnoDB storage engine, which can improve the performance of multi-core systems by reducing contention on the buffer pool latch.

That said, CPU or memory usage is not only limited to these two variable configurations, and further analysis is required to track what’s causing the usage spikes. The point we’re making here is they are critical for MySQL Performance.

## Disk space usage

Monitor the disk space usage of MySQL data files, log files, and temporary files.

You may review the fragmented tables, binary-logs, and duplicate or redundant indexes to reclaim the space. As a best practice, It is advisable to have different mounts for MySQL data and log files with specific system configurations.

### PMM – Disk Details, which includes disk usage as well as disk performance charts

![image](https://github.com/user-attachments/assets/c0f191bd-bec9-413f-9032-101bc5b19080)

## Replication lag

Monitoring [replication lag](https://www.percona.com/blog/fighting-mysql-replication-lag/) is important as it can affect the consistency and reliability of data across multiple database instances in a replication setup. 

Replication lag can occur due to various factors such as network latency, system resource limitations, complex transactions, or heavy write loads on the primary/master database. If replication lag is too high, it can result in stale or outdated data on the replica/slave databases, leading to data inconsistencies and potential application issues.

### PMM – MySQL Replication Summary dashboard

![image](https://github.com/user-attachments/assets/9b18a7ac-7227-4411-9e8d-97c7164fab3f)

Related configuration or status variables to consider:

- **Seconds_Behind_Master**: In SHOW REPLICA STATUS command, this value indicates the replication lag in seconds.

One of the possible improvements in lag would be utilizing Parallel Replication.

## Backup and recovery metrics

Backup and recovery metrics are key performance indicators (KPIs) for MySQL that provide insights into the reliability, efficiency, and effectiveness of backup and recovery processes. They include backup success rate, backup duration, recovery time objective (RTO), recovery point objective (RPO), and backup storage utilization. Monitoring these metrics helps ensure data protection, minimize downtime, and ensure business continuity.

You should not only monitor the backup mount for disk space and backup log but also regularly test the restores and log to match RPO and RTO objectives.

## Error rates

The MySQL error log contains information about errors, warnings, and other issues that occur within the MySQL database. By monitoring the error log, you can quickly identify and resolve any problems that may arise, such as incorrect queries, missing or corrupt data, or database server configuration issues.

- **error_log**: Specifies the location of the MySQL error log.

## Leveraging MySQL Performance Monitoring Tools

In this blog, we discussed the basics of MySQL’s key performance indicators (KPIs) using PMM. By monitoring these KPIs, such as database uptime and availability, query performance, indexing efficiency, connection usage, CPU and memory usage, disk space usage, replication lag, backup and recovery metrics, and error rates, you can gain valuable insights into your MySQL server’s health and performance.

Note that the specific configuration variables and their optimal values may vary depending on the MySQL version, system hardware, workload, and other factors. It’s important to thoroughly understand the MySQL configuration variables and their impacts before making any changes and to carefully monitor the effects of configuration changes to ensure they improve the desired KPIs.

**Percona Monitoring and Management is a best-of-breed open source database monitoring solution. It helps you reduce complexity, optimize performance, and improve the security of your business-critical database environments, no matter where they are located or deployed. \**Get started with Percona Monitoring and Management, your MySQL performance monitoring tool.\****

## FAQs

### What are the key performance indicators (KPIs) to monitor in MySQL performance?

The key KPIs for MySQL performance include query response time, throughput (queries per second), server load, connection usage, and buffer pool efficiency.

### What are the essential MySQL performance metrics to track for optimal database performance?

Essential MySQL performance metrics to track are slow query logs, InnoDB metrics (like row operations and buffer pool usage), thread activity, memory usage, and disk I/O activity.

### How does Percona Monitoring and Management help in analyzing MySQL performance metrics?

Percona Monitoring and Management (PMM) provides comprehensive tools for real-time monitoring and historical analysis of MySQL performance. It offers dashboards for metrics such as query performance, server load, and resource utilization, enabling quick identification of performance bottlenecks and optimization opportunities.

### What are the benefits of using Percona Monitoring and Management over other MySQL performance monitoring tools?

Percona Monitoring and Management (PMM) is an open source solution that offers deep insights into database behavior, flexible data retention policies, and extensive support for MySQL and other database systems. PMM facilitates easy integration with existing infrastructures, providing a holistic view of your database operations.







