原文地址：https://www.red-gate.com/simple-talk/blogs/devising-the-perfect-index-for-your-use-case-mysql-edition/



# Devising the Perfect Index for Your Use Case – MySQL Edition

## Introduction

If you’ve been following SimpleTalk for a while, you will be aware that I’ve recently blogged about MySQL indexes. I’ve already told you about [the nuances of indexes in MySQL](https://www.red-gate.com/simple-talk/databases/mysql/the-nuances-of-mysql-indexes/), [vanilla B-Tree indexes](https://www.red-gate.com/simple-talk/databases/mysql/mysql-index-overviews-b-tree-indexes/), [composite B-Tree indexes](https://www.red-gate.com/simple-talk/databases/mysql/mysql-index-overviews-composite-b-tree-indexes/), and other kinds of indexes too.

This blog will walk you through how to choose the perfect index for your specific use case. It will also advise you on the things you should consider to devise the perfect index design in MySQL.

## About Indexes in MySQL

One thing you should take away from those blogs is that there are two general types of indexes in MySQL that are often split into multiple different sub-types. We have B-tree indexes, and R-tree, or spatial, indexes.

Most indexes will be related to the B-tree index type, and those that aren’t will be considered R-tree indexes. Covering, composite, clustered, descending, unique, hash, full-text, and primary key indexes will be B-tree indexes, and spatial indexes will be R-tree indexes.

A B-tree index is a balanced tree index. A balanced tree index is a data structure that sorts data and facilitates searching for data that’s stored in a sorted order. It thus enables your database to find rows quicker.

An R-tree index is an index that’s used to find data falling within a general range, and then perform operations on that data. R-tree indexes are used to facilitate spatial searches – that is, searches through geographic data sets.

## Indexing for Your Use Case

If you consider using indexes, you’re most likely considering them to facilitate an end goal for your specific use case. That end goal is most likely related to one or more of the following points:

1. You have quite a lot of data in your tables (think tens of millions of rows or more.)
2. Your application necessitates search operations through bigger data sets.
3. Your use case necessitates the removal of duplicate rows where necessary.

Indexes help these use cases because they can eliminate rows from consideration when a WHERE clause is in use.

Most of your use cases will necessitate vanilla B-tree indexes unless your use case deals with geographical data. B-tree indexes can be applied when creating a table. To do that, add an INDEX clause once you define all of the necessary columns:

```
CREATE TABLE `demo_table` (`
``col` VARCHAR(115) NOT NULL,`
``col2` INT(5) NOT NULL,`
`**INDEX `idx_name`(`col`)**`
`);
```

Or when modifying data using the `CREATE INDEX` or `ALTER` clauses:

```
CREATE INDEX `btree_idx` ON `demo_table`(`column`);
```

![image](https://github.com/user-attachments/assets/4e03474f-8992-48a5-824f-11165401fe3d)

Adding an index is easy, but choosing the specific index and designing it as necessary is a little harder.

## What Index to Choose for Your Use Case? Tips & Tricks

To choose an index for your use case, consider the following:

- Choose a **vanilla B-tree index*** *if you need to use an index to eliminate rows from consideration.* *It may help you when your use case doesn’t necessitate any other kind of index type* (see index types below.)
- Choose a **covering B-tree index** *if your use case enables and/or necessitates you to read data from the index* instead of reading it from the disk.
- Choose a **multicolumn or composite index** *if your* *`WHERE`* *clause involves multiple columns, your query includes* *`OR`* *clauses, or you want to make the query optimizer able to directly access the table if the column is not in the index.*
- Choose a **clustered index** when your table must contain id columns. If you don’t choose one, MySQL will contain a clustered index in the form of a primary key, anyway – refer to my blog about primary keys to learn more.
- Choose a **descending index** *when your use case necessitates all rows to be stored in a descending manner* (think using an `ORDER BY` clause to sort the results of a query in descending order, etc.)
- Use a **unique index** *when you need to get rid of duplicate rows inside of a table.*
- Use a **hash index** *when your use case necessitates blazing fast searches using the MEMORY storage engine.*
- *Use a **full-text index** when your use case necessitates searches using “fuzzy matching.”* That may include wildcards, or searches in Boolean or query expansion modes. Full-text indexes allow you to include Boolean or other kind of sophisticated logic in your search queries. Their search modes are worth exploring too – for more information, refer to my blog post on full-text indexes.**
- *Use a **spatial index** when your use case necessitates searching through geographic data*. More information [here](https://dev.mysql.com/doc/refman/8.4/en/creating-spatial-indexes.html).

### Rules Involving Indexes

Rules involving indexes are simple and they’re not rocket science, however, one must be aware of the fact that each type of indexing comes with its unique propositions and problems. Most of those problems are [already covered in my series on indexes in MySQL](https://www.red-gate.com/simple-talk/author/lukas-vileikis/), so I won’t get into them here, so if you’re curious, give these blogs a read!

**A “vanilla” B-tree index is a B-tree index that is not a covering, composite, clustered, descending, unique, hash, full-text, or primary key index.*

***Wildcards are not exclusive to full-text searches and they can also be used when no indexes are in use or when other types of indexes are being used*. *Keep in mind that you cannot use them using the “\*” symbol – we have to choose the percentage sign (“%”) instead.*

## How to Devise the Perfect Index Design? Examples, Tips & Tricks

Once you’ve chosen the type of index you are going to employ to help your use case, it’s time to devise the perfect index design.

The way your “perfect” index design will look like will depend on a couple of factors, the main ones being:

1. **The type of the index you chose** – the type of index is directly related to your use case.
2. **Your use case and the types of queries you run** – in this case, your use case has a lot to do with the structure of your SELECT queries.

These two things are closely linked – choose the necessary index based on your query structure, and your query structure will be dictated by your use case.

### Examples

Here are a couple of examples:

| **ID** | **Query or Use Case**                                        | **Index to Choose**                   | **Definition of the Index**                                  |
| ------ | ------------------------------------------------------------ | ------------------------------------- | ------------------------------------------------------------ |
| 1      | `SELECT * FROM `demo` WHERE `col` = ‘Value’;`                | Vanilla B-tree index                  | `CREATE INDEX `idx` ON `demo`(`col`);`                       |
| 2      | `SELECT * FROM `demo` WHERE `col` = ‘Value’ AND `c2` = ‘Value 2’;` | Vanilla B-tree index (covering index) | `CREATE INDEX `idx` ON `demo`(`col`,`c2`);` Alternatively, index #1. |
| 3      | `SELECT * FROM `demo` WHERE MATCH(col) AGAINST(“Value”);`    | Full-text index                       | `CREATE FULLTEXT INDEX `ftx` ON `demo`(`col`);`              |
| 4      | `SELECT data FROM `demo` WHERE id = 1;`                      | Clustered index                       | `ALTER TABLE `demo` ADD PRIMARY KEY(`id`);`                  |
| 5      | A lot of rows, adding a B-tree doesn’t make sense, but data has to be indexed for *some* efficiency. | Partial index                         | `CREATE INDEX `partial_idx` ON `demo`(`col`(10));`           |
| 6      | `SELECT id,username FROM `demo` WHERE regdate LIKE ‘2016%’ **ORDER BY id DESC**;` | Descending index                      | `CREATE INDEX `desc_idx` ON `demo`(`regdate`, `id` DESC); or CREATE INDEX `desc_idx` ON `demo`(`regdate` DESC);` |

For many “vanilla” use cases you may choose a vanilla B-tree index as shown in some of the examples below. Some use cases may necessitate you using a covering index (*consider a covering index when you search through a lot of data*.)

Some use cases may necessitate a partial index to benefit from a B-tree index and save disk space at the same time. For a partial index to be effective, define the index on “active” (i.e. used by the user) amount of characters in the column. To choose a proper character count for the partial index, consider your use case and define the partial index on the average number of characters that are searched through. For example, if your use case involves searching for email addresses, defining a partial index on the first 20 characters should help.



## Summary

Choosing a proper index and designing it properly is vital for your use case. [My indexing series here on Simple-Talk](https://www.red-gate.com/simple-talk/author/lukas-vileikis/) should give you a good enough insight into the index types available to choose from in MySQL, while this blog should give you enough examples to make adequate choices quickly.

