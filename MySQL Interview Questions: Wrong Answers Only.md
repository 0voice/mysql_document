原文链接：https://www.percona.com/blog/mysql-interview-questions-wrong-answers-only/



# MySQL Interview Questions: Wrong Answers Only

December 6, 2023

[Kedar Vaijanapurkar](https://www.percona.com/blog/author/kedar-vaijanapurkar)

During an interview or while having general discussions, I have found some funny responses that can be easily classified as “Wrong Answers,” but at times, they’re thought-provoking or involve a deep meaning within. This blog is regarding some of the usual MySQL database conversations and responses, which can appear “wrong” or “funny,” but there’s actually more to them. I will share a selection of such seemingly “wrong” or whimsical responses and take a closer look at the valuable lessons and perspectives they offer.

Let the “MySQL Interview” begin.

> ## Q: How will you improve a slow query?
>
> A: Let’s not execute it at all. A query avoided is a query improved.

While this is a fact, we should carefully consider whether a query is necessary before executing it. Avoiding unnecessary queries and fetching only the required data can significantly optimize the query’s performance.

An approach to improve a query which cannot be avoided will be:

- Monitor slow query log and use pt-query-digest to generate a summary report for slow queries.
- Use an explain statement in MySQL to understand the query execution plan, offering insights into table access order, index usage, and potential performance bottlenecks.

#### Additional read

- Mike’s blog on [How to Find and Tune a Slow SQL Query](https://www.percona.com/blog/mysql-101-how-to-find-and-tune-a-slow-sql-query/)

![image](https://github.com/user-attachments/assets/83fdd05b-fd8e-411c-9bb7-31df7a607187)


- > ## Q: What is your disaster recovery (DR) strategy?
  >
  > A: We have a replica under our primary database.

  Hmm, a replica seems like a straightforward response, but it is not a comprehensive disaster recovery strategy. In reality, relying solely on a replica under the primary server is not sufficient for a robust disaster recovery plan.

  In a disaster recovery (DR) strategy, it is essential to consider multiple aspects, naming a few

  - Data backup
  - High availability
  - Failover mechanisms
  - Offsite storage

  While having a replica is beneficial for load balancing and read scaling, it does not cover all disaster scenarios.

  #### Additional read

  - [Baron’s quick note on why you cannot rely on a replica for DR](https://www.percona.com/blog/why-you-cant-rely-on-a-replica-for-disaster-recovery/)

   

  > Q: What about delayed replica?
  >
  > 
  > A: Well, it is our delayed disaster recovery.

  “What about delayed replica?” you may ask. Well, it is a delayed disaster-in-waiting. 🙂 

  A lot depends on how strong your monitoring strategy is and how fast you can react to the DR call.

  The delayed replica surely complements regular real-time replicas by providing an additional layer of DR protection as compared to the active primary. But when disaster strikes and, importantly, is detected within the configured replica-delay, it provides a bit of an easy recovery option. That said, if the delayed replica is hosted on the same infrastructure/data center, it is vulnerable to the same disaster affecting the primary.

  It should surely help provide a good backup plan to guard against human error, logical error, data corruption, etc.

  #### Additional read

  - Walter’s ultimate guide of [MySQL Backup and Recovery Best Practices](https://www.percona.com/blog/mysql-backup-and-recovery-best-practices/).

   

  > ## Q: What is one of your favourite (and common) security worst practices?
  >
  > A: Usage of .my.cnf file

  The .my.cnf file is typically used to store login credentials for MySQL, allowing users to connect to the database without providing credentials explicitly. We all know that saving plaintext passwords in this file is a significant security risk, as it could lead to unauthorized access if the file system is compromised. The same risk is present while using the password on the command prompt.

  #### Additional read

  - [Use MySQL Without a Password (and Still be Secure)](https://www.percona.com/blog/use-mysql-without-a-password/)
  - [How to Secure MySQL – Percona Community MySQL Live Stream ](https://www.youtube.com/watch?v=Bei-AhP52vc)

   

  > ## Q: What will you do to alter a table sized 10T?
  >
  > A: Nothing. I will not.

  Well, the natural response would be to suggest looking for ONLINE ALTER options using tools like pt-online-schema-change or gh-ost. While those answers seem correct, would you really be able to alter a 10T table? Think about the time and resources required for such an activity. Clearly, 10T is just a number to represent a gigantic table size to give a perspective.

  The counter question would be, “Why do you have such a large table in the database?”. Since the size is “terrantic” (terabyte-sized), further growth is highly likely; there should either be an archiving strategy or some change in application logic to have a manageable table size.

  Large tables in your production will cost your query performance, cause inefficient reading and writing, slow backup/restores, and introduce challenges in application changes and database upgrades. It is important to understand and monitor the table growth in your system and work on possible table archiving strategies.

  The [Percona Monitoring and Management](https://www.percona.com/software/database-tools/percona-monitoring-and-management) dashboard does list the large tables by size, by rows, and even tables that are getting to table-full situations. 

  Finally, one trivia question, I request that you respond in the comments.

  **MySQL has a single database object, which is actually double. You can’t see either of them, yet you can query! What is that?**

  #### Additional read

  - Learn about [Percona’s online schema change tool](https://docs.percona.com/percona-toolkit/pt-online-schema-change.html).

  ### Conclusion

  Before concluding, I invite you to share your own playful takes on MySQL-related questions. As we wrap up, let’s emphasize the importance of going beyond the obvious when tackling questions. Sometimes, the right answer requires a deeper dive, and that’s where the true understanding lies. Until next time, happy MySQL-ing!
