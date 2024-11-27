原文地址：https://www.percona.com/blog/mysql-101-10-great-mysql-dba-interview-questions-and-answers/



# MySQL 101: 10 Great MySQL DBA Interview Questions (and Answers)



DBAs are important cogs in the availability, performance, and security of your data. Getting a great DBA is one of the most important things any company can do to ensure their database operations continue working without a problem. So how do you find great DBAs?

In this post I’m going to show you some of the questions I like to ask during my interviews with DBA candidates.

### *Performance is crucial, so does this candidate know how to tune and optimize?*

1. **Back to the beginning: what are the most common optimizations you would recommend for performance?**

   This is about conceptual knowledge and is often an open-ended question. Are they looking at throwing hardware and increasing resources? This is the easiest solution, but normally not the correct one. Often performance is limited if the database design is poor. Will they talk about this? I want people to talk through the best options and decide which variables or fixes make the most sense. The easy answer I have seen with customers is tweaking a single variable like “innodb_dedicated_server”, which of course helps as is a shortcut for several configurations but if you don’t understand it properly it can cause some undesired harm (like a lot of unneeded redo log files). I am looking for people to understand the difference between quick and long-lasting issues. I’m expecting candidates to think in basic and important optimizations like good Primary Key design, normalization of data, thinking in proper data types, and so on.

 

2. **What are the main parameters you would tune to make a better IO utilization in IO-bound systems? Name 3, why?**

   Disk IO is the slowest part of any database, and any DBA absolutely must understand this concept. The easy answer is always to add more memory, but it is not just about the buffer pool. Does the candidate understand the other variables and database internals that impact IO? For instance, do they understand the redo logs, flushing, io_capacity, etc and can they walk through what they do? The combination of these variables will reduce IO dependency while extending SSD life.

 

3. **You get the call that the database is slow. What do you do? How would you identify a slow query? What are the best strategies to optimize a query?**

   Query optimization is an activity every DBA must know. Even if the database is seemingly fast and performant 99% of the time, one rogue or slow query can bring down an entire application. Here we are looking for the process of not only finding the slow query but also tuning and fixing it. We need to understand what are the causes for having slow queries, like indexes, not good SQL writing skills, etc. Luckily we have tools to identify the bad guys and help with the optimization process. What tools the candidate decides to use are a little less critical than the process they go through. We are looking for things like their ability to identify missing indexes, bad primary keys, problems with very large tables, schema design, etc

   

   4. **Let’s suppose you need to increase 10x the traffic against a MySQL server. What would your scalability strategy be? Is the same scaling for reads as scaling for writes?**
      
      Here there are many strategies for scalability. You are looking for not only the concrete technologies or processes but the why behind them. Key things to discuss and dig into: Adding replicas to offload traffic, setting a load balancer in place, increasing the available hardware, sharding, etc. All of these are valid strategies. The real question is does the candidate understand them and when they should and should not be used?

    

   ### *Every DBA must build and ensure their database systems are available whenever they are needed. So understanding database availability strategies is critical. It is a must to ask questions to gauge their understanding:*

   5. **How would you explain asynchronous replication lag? Why can it be a problem? How would you fix it?**
      
      Asynchronous replication is still a very important topic and while performance is getting better with every release and change being added to MySQL, I am still helping a lot of customers to fix issues with replication setup and lag. Many companies use replica’s for reporting, backups, and high availability, because of this we want our replicas to be up to date as much as possible. The good news is this also gives the candidate another opportunity to think outside the box because asynchronous replication lag can be dramatically impacted by database design issues like no Primary Keys, long-running queries, select for updates, etc.

    

   6. **Can they explain the difference between asynchronous and synchronous replication? and When would they pick one over the other?** Of course, there are several different types of asynchronous replication (MySQL GTID, MariaDB GTID, Tungsten Replicator) and synchronous replication (Percona XtraDB Cluster, MySQL Group Replication) you can use in MySQL, so gauging their general knowledge of this can be helpful in assessing their skills. While there’s not a 100% correct answer here is important to understand what are the decision drivers to pick one solution over the other, like performance, consistency, availability, etc.

    

   7. **What is the lowest number of nodes Galera-based solutions require? What are the pros and cons of adding more nodes?** There are two dominant clustering solutions in the MySQL space. Galera-based replication like Percona XtraDB Cluster and Group Replication Based Clustering like the InnoDB Cluster.  The key to this question is to know that Galera is designed for consistency and availability, not write scalability. Is quite important to understand these differences because it is common to be trapped by the idea of “more writers more writes” as if adding more writer nodes may increase the write capacity of the cluster.

    

   ### *Of course, you need to ensure that not only does the candidate know how to troubleshoot performance and set up high availability they need to understand some of the fundamental database operations and technologies out there. Asking at least one question on security, backups, and hosting ( cloud or not ) is a good way to ensure you cover the basics.* 

    

   8. **Please describe your recommended backup policy. What tools you’d use and how?**
      
      Is important to understand how important backups are. I am looking for the person to walk me through their experience and tell me what they feel is a good and safe backup strategy. As they talk I will ask questions to see if they understand the limitations or benefits of the setup they have been running. I am looking for them to have some mastery of the different backup options (physical, logical) and what can be recommended for a backup policy (daily, weekly, monthly, full, incremental, etc). Of course, I am very interested in how people ensure their backups are “Good”. Not everyone thinks restore tests should be considered part of the backup policy.

    

   9. **What are the basic security strategies you would follow when deploying MySQL?** There is again no one size fits all answer, but there is no doubt a DBA has to secure a company’s data. Some things to listen for: Strong passwords, root access only from localhost, removing users without passwords, removing SUPER priv to non-admin users, limiting users access to their own DB/table, encrypted data, etc.

    

   10. **Please explain the main differences between RDS MySQL and Aurora. When would you recommend one solution over the other?** There is no doubt that the cloud has changed the way we run databases. Does the candidate have a fundamental understanding of the differences between AWS’s top two MySQL options?  They are different, do they know that?  Understanding the benefits and drawbacks is crucial. While RDS presents an easy way to set up asynchronous replication that works well out of the box, it requires some operational activities to ensure the environment is healthy, i.e. data consistency checks, handling failover in case of primary failure, etc. On the other hand, Aurora solves the operational part by ensuring consistency and availability at a higher cost and with some extra write overhead so it may not scale well in high traffic environments.

   These are just some of the questions I think are good questions for people to ask when trying to find outstanding database professionals. Do you have others? I would love to hear from you in the comments section..
