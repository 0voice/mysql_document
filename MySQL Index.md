原文地址：https://www.mysqltutorial.org/mysql-index/



# MySQL Index

MySQL uses indexes to rapidly locate rows with specific column values. Without an index, MySQL must scan the entire table to find the relevant rows. The larger the table, the slower the search becomes.

## Section 1. Creating and Managing MySQL indexes

This section explains what an index is and shows you how to create, modify, and drop an index.

- [Creating indexes](https://www.mysqltutorial.org/mysql-index/mysql-create-index/) – introduce the index concept and show you how to create an index for one or more columns of a table.
- [Removing indexes](https://www.mysqltutorial.org/mysql-index/mysql-drop-index/) – show you how to remove an existing index of a table.
- [Listing table indexes](https://www.mysqltutorial.org/mysql-index/mysql-show-indexes/) – provide you with a statement to list all indexes or specific indexes of a table.

## Section 2. MySQL Index Types

This section discusses various types of indexes and helps you to choose the right indexing strategy for your applications.

- [Unique indexes](https://www.mysqltutorial.org/mysql-index/mysql-unique-index/) – use unique indexes to ensure distinct values stored in a column.
- [Prefix indexes](https://www.mysqltutorial.org/mysql-index/mysql-prefix-index/) – show you how to use the prefix index to create an index for a character string column.
- [Invisible indexes](https://www.mysqltutorial.org/mysql-index/mysql-invisible-index/) – cover the index visibility and show you how to make an index visible or invisible.
- [Descending indexes](https://www.mysqltutorial.org/mysql-index/mysql-descending-index/) – show you how to use descending indexes to increase query performance.
- [Composite indexes](https://www.mysqltutorial.org/mysql-index/mysql-composite-index/) – illustrate the application of composite indexes and show you when to use them to speed up your queries.
- [Clustered indexes](https://www.mysqltutorial.org/mysql-index/mysql-clustered-index/) – explain the clustered indexes in InnoDB tables.
- [Index cardinality](https://www.mysqltutorial.org/mysql-index/mysql-index-cardinality/) – explain the index cardinality and show you how to view it using the show indexes command.
- [Functional index](https://www.mysqltutorial.org/mysql-index/mysql-functional-index/) – learn how to create an index based on the result of an expression or function.

## Section 3. MySQL Index Hints

This section introduces the index hints that you can use when the query optimizer doesn’t choose the most efficient index for your specific query, or when you want to test with different index options to improve query performance.

- [USE INDEX hint](https://www.mysqltutorial.org/mysql-index/mysql-use-index/) – show you how to use the USE INDEX hint to instruct the query optimizer to use the only list of specified indexes to find rows in a table.
- [FORCE INDEX hint](https://www.mysqltutorial.org/mysql-index/mysql-force-index/) – show you how to use the FORCE INDEX hint to force the query optimizer to use specified indexes to select data from a table.
