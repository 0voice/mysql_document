原文地址：https://www.mysqltutorial.org/mysql-basics/



# MySQL Basics

This MySQL basics section teaches you how to use SQL statements to manage data in MySQL. It’ll provide you with everything you need to know to work with MySQL effectively.

## Section 1. Querying data

- [SELECT FROM ](https://www.mysqltutorial.org/mysql-basics/mysql-select-from/)– show you how to use a simple `SELECT FROM` statement to query the data from a single table.
- [SELECT](https://www.mysqltutorial.org/mysql-basics/mysql-select/) – learn how to use the SELECT statement without referencing a table.

## Section 2. Sorting data

- [ORDER BY ](https://www.mysqltutorial.org/mysql-basics/mysql-order-by/)– show you how to sort the result set using the ORDER BY clause. You will also learn how to use custom sort order with the FIELD function.

## Section 3. Filtering data

- [WHERE ](https://www.mysqltutorial.org/mysql-basics/mysql-where/)– learn how to use the WHERE clause to filter rows based on specified conditions.
- [SELECT DISTINCT](https://www.mysqltutorial.org/mysql-basics/mysql-distinct/) – show you how to use the DISTINCT operator in the SELECT statement to eliminate duplicate rows in a result set.
- [AND](https://www.mysqltutorial.org/mysql-basics/mysql-and/) – introduce you to the AND operator to combine Boolean expressions to form a complex condition for filtering data.
- [OR](https://www.mysqltutorial.org/mysql-basics/mysql-or/)– introduce you to the OR operator and show you how to combine the OR operator with the AND operator to filter data.
- [IN ](https://www.mysqltutorial.org/mysql-basics/mysql-in/)– show you how to use the IN operator in the WHERE clause to determine if a value matches any value in a set.
- [NOT IN](https://www.mysqltutorial.org/mysql-basics/mysql-not-in/) – negate the IN operator using the NOT operator to check if a value doesn’t match any value in a set.
- [BETWEEN](https://www.mysqltutorial.org/mysql-basics/mysql-between/) – show you how to query data based on a range using the BETWEEN operator.
- [LIKE  ](https://www.mysqltutorial.org/mysql-basics/mysql-like/)– query database on pattern matching using wildcards such as `%` and `_`.
- [LIMIT](https://www.mysqltutorial.org/mysql-basics/mysql-limit/) – use LIMIT to limit the number of rows returned by the SELECT statement
- [IS NULL](https://www.mysqltutorial.org/mysql-basics/mysql-is-null/) – test whether a value is NULL or not by using the IS NULL operator.

## Section 4. Joining tables

- [Table & Column Aliases ](https://www.mysqltutorial.org/mysql-basics/mysql-alias/)– introduce you to table and column aliases.
- [Joins](https://www.mysqltutorial.org/mysql-basics/mysql-join/) – give you an overview of joins supported in MySQL including inner join, left join, and right join.
- [INNER JOIN ](https://www.mysqltutorial.org/mysql-basics/mysql-inner-join/)– query rows from a table that has matching rows in another table.
- [LEFT JOIN ](https://www.mysqltutorial.org/mysql-basics/mysql-left-join/)– return all rows from the left table and matching rows from the right table or null if no matching rows are found in the right table.
- [RIGHT JOIN](https://www.mysqltutorial.org/mysql-basics/mysql-right-join/) – return all rows from the right table and matching rows from the left table or null if no matching rows are found in the left table.
- [Self-join ](https://www.mysqltutorial.org/mysql-basics/mysql-self-join/)– join a table to itself using a table alias and connect rows within the same table using inner join and left join.
- [CROSS JOIN](https://www.mysqltutorial.org/mysql-basics/mysql-cross-join/) – make a Cartesian product of rows from multiple tables.

## Section 5. Grouping data

- [GROUP BY](https://www.mysqltutorial.org/mysql-basics/mysql-group-by/) – show you how to group rows into groups based on columns or expressions.
- [HAVING ](https://www.mysqltutorial.org/mysql-basics/mysql-having/)– filter the groups by a specific condition.
- [HAVING COUNT](https://www.mysqltutorial.org/mysql-basics/mysql-having-count/) – show you how to use the HAVING clause with the COUNT function to filter groups by the number of items.
- [ROLLUP](https://www.mysqltutorial.org/mysql-basics/mysql-rollup/) – generate multiple grouping sets considering a hierarchy between columns specified in the GROUP BY clause.

##  Section 6. Subqueries

- [Subquery ](https://www.mysqltutorial.org/mysql-basics/mysql-subquery/)– show you how to nest a query (inner query) within another query (outer query) and use the result of the inner query for the outer query.
- [Derived table](https://www.mysqltutorial.org/mysql-basics/mysql-derived-table/) – introduce you to the derived table concept and show you how to use it to simplify complex queries.
- [EXISTS](https://www.mysqltutorial.org/mysql-basicshttps://www.mysqltutorial.org/mysql-basics/mysql-exists/) – test for the existence of rows.

## Section 7. Set operators

- [UNION ](https://www.mysqltutorial.org/mysql-basics/mysql-union/)– combine two or more result sets of multiple queries into a single result set.
- [EXCEPT](https://www.mysqltutorial.org/mysql-basics/mysql-except/) – show you how to use the EXCEPT operator to find the set difference between two sets of data.
- [INTERSECT](https://www.mysqltutorial.org/mysql-basics/mysql-intersect/) – show you how to use the INTERSECT operator to find common rows of two or more queries.

## Section 8. Managing databases

This section shows you how to manage MySQL databases.

- [Selecting a database](https://www.mysqltutorial.org/mysql-basics/selecting-a-mysql-database-using-use-statement/) – show you how to use the USE statement to set the current database.
- [CREATE DATABASE](https://www.mysqltutorial.org/mysql-basics/mysql-create-database/) – show you step by step how to create a new database in MySQL Server.
- [DROP DATABASE](https://www.mysqltutorial.org/mysql-basics/mysql-drop-database/) – walk you through the steps of deleting a database from the database server.

## Section 9. Working with tables

This section shows you how to manage the most important database objects in MySQL, including databases and tables.

- [MySQL storage engines](https://www.mysqltutorial.org/mysql-administration/mysql-storage-engines/)– it is essential to understand the features of each storage engine so that you can use them effectively to maximize the performance of your databases.
- [CREATE TABLE ](https://www.mysqltutorial.org/mysql-basics/mysql-create-table/)– show you how to create new tables in a database using the CREATE TABLE statement.
- [MySQL data types ](https://www.mysqltutorial.org/mysql-basics/mysql-data-types/)– show you various data types in MySQL so that you can apply them effectively in designing database tables.
- [AUTO_INCREMENT ](https://www.mysqltutorial.org/mysql-basics/mysql-auto_increment/)– show you how to use an AUTO_INCREMENT column to generate unique numbers automatically for the primary key.
- [ALTER TABLE ](https://www.mysqltutorial.org/mysql-basics/mysql-alter-table/)– learn how to change the structure of a table using the ALTER TABLE statement.
- [Renaming tables](https://www.mysqltutorial.org/mysql-basics/mysql-rename-table/) –  show you how to rename a table using the RENAME TABLE statement.
- [Removing a column from a table](https://www.mysqltutorial.org/mysql-basics/mysql-drop-column/) – show you how to use the ALTER TABLE DROP COLUMN statement to remove one or more columns from a table.
- [Adding a new column to a table](https://www.mysqltutorial.org/mysql-basics/mysql-add-column/) – show you how to add one or more columns to an existing table using the ALTER TABLE ADD COLUMN statement.
- [DROP TABLE](https://www.mysqltutorial.org/mysql-basics/mysql-drop-table/) – show you how to remove existing tables using the DROP TABLE statement.
- [Temporary tables ](https://www.mysqltutorial.org/mysql-basics/mysql-temporary-table/)– discuss MySQL temporary tables and show you how to manage temporary tables effectively.
- [TRUNCATE TABLE ](https://www.mysqltutorial.org/mysql-basics/mysql-truncate-table/)– show you how to delete all data from a table quickly and more efficiently using the TRUNCATE TABLE statement.
- [Generated columns](https://www.mysqltutorial.org/mysql-basics/mysql-generated-columns/) – guide you on how to use the generated columns to store data computed from an expression or other columns.

## Section 10. MySQL constraints

- [Primary key](https://www.mysqltutorial.org/mysql-basics/mysql-primary-key/) – guide you on how to use the primary key constraint to create the primary key for a table.
- [Foreign key](https://www.mysqltutorial.org/mysql-basics/mysql-foreign-key/) – introduce you to the foreign key and show you step by step how to create and drop foreign keys.
- [Disable foreign key checks](https://www.mysqltutorial.org/mysql-basics/mysql-disable-foreign-key-checks/) – learn how to disable foreign key checks.
- [NOT NULL](https://www.mysqltutorial.org/mysql-basics/mysql-not-null-constraint/)– introduce you to the NOT NULL constraint and show you how to declare a NOT NULL column or add a NOT NULL constraint to an existing column.
- [UNIQUE constraint](https://www.mysqltutorial.org/mysql-basics/mysql-unique-constraint/) – show you how to use the UNIQUE constraint to enforce the uniqueness of values in a column or a group of columns in a table.
- [CHECK constraint](https://www.mysqltutorial.org/mysql-basics/mysql-check-constraint/) – learn how to create CHECK constraints to ensure data integrity.
- [DEFAULT](https://www.mysqltutorial.org/mysql-basics/mysql-default/) – show you how to set a default value for a column using the DEFAULT constraint.

## Section 11. MySQL data types

- [BIT](https://www.mysqltutorial.org/mysql-basics/mysql-bit/) – introduce you to BIT datatype and how to store bit values in MySQL.
- [INT](https://www.mysqltutorial.org/mysql-basics/mysql-int/) – show you how to use integer data type.
- [BOOLEAN](https://www.mysqltutorial.org/mysql-basics/mysql-boolean/) – explain to you how MySQL handles Boolean values by using TINYINT(1) internally.
- [DECIMAL](https://www.mysqltutorial.org/mysql-basics/mysql-decimal/) – show you how to use DECIMAL datatype to store exact values in decimal format.
- [CHAR](https://www.mysqltutorial.org/mysql-basics/mysql-char-data-type/) – a guide to CHAR data type for storing the fixed-length string.
- [VARCHAR](https://www.mysqltutorial.org/mysql-basics/mysql-varchar/) – give you the essential guide to VARCHAR datatype.
- [TEXT](https://www.mysqltutorial.org/mysql-basics/mysql-text/) – show you how to store text data using TEXT datatype.
- [DATETIME](https://www.mysqltutorial.org/mysql-basics/mysql-datetime/) – introduce you to the DATETIME datatype and some useful functions to manipulate DATETIME values.
- [TIMESTAMP ](https://www.mysqltutorial.org/mysql-basics/understanding-mysql-timestamp/)– introduce you to TIMESTAMP and its features called automatic initialization and automatic update, which allow you to define auto-initialized and auto-updated columns for a table.
- [DATE](https://www.mysqltutorial.org/mysql-basics/mysql-date/) – introduce you to the DATE datatype and show you some date functions to handle the date data effectively.
- [TIME ](https://www.mysqltutorial.org/mysql-basics/mysql-time/)– walk you through the features of TIME datatype and show you how to use some useful temporal functions to handle time data.
- [BINARY](https://www.mysqltutorial.org/mysql-basics/mysql-binary/) – show you how to store fixed-length byte data.
- [VARBINARY](https://www.mysqltutorial.org/mysql-basics/mysql-varbinary/) – learn how to store variable-length byte data.
- [BLOB](https://www.mysqltutorial.org/mysql-basics/mysql-blob/) – guide you on how to use the BLOB data type to store large binary data such as images, videos, and so on.
- [ENUM](https://www.mysqltutorial.org/mysql-basics/mysql-enum/) – learn how to use ENUM datatype correctly to store enumeration values.
- [JSON](https://www.mysqltutorial.org/mysql-json/mysql-json-data-type/) – show you how to use JSON data type to store JSON documents.
- [MySQL UUID Smackdown: UUID vs. INT for Primary Key](https://www.mysqltutorial.org/mysql-basics/mysql-uuid/) – introduce you to MySQL UUID, shows you how to use it as the primary key (PK) for a table, and discusses the pros and cons of using it as the PK.

## Section 12. Modifying data in MySQL

In this section, you will learn how to insert, update, and delete data from tables using various MySQL statements.

- [INSERT ](https://www.mysqltutorial.org/mysql-basics/mysql-insert/)– use various forms of the INSERT statement to insert data into a table.
- [INSERT Multiple Rows](https://www.mysqltutorial.org/mysql-basics/mysql-insert-multiple-rows/) – insert multiple rows into a table.
- [INSERT INTO SELECT](https://www.mysqltutorial.org/mysql-basics/mysql-insert-into-select/) – insert data into a table from the result set of a query.
- [INSERT IGNORE](https://www.mysqltutorial.org/mysql-basics/mysql-insert-ingore/) – explain the INSERT IGNORE statement that inserts rows into a table and ignores rows that cause errors.
- [Insert datetime values](https://www.mysqltutorial.org/mysql-basics/mysql-insert-datetime/) – show you how to insert datetime values into a DATETIME column.
- [Insert date values](https://www.mysqltutorial.org/mysql-basics/mysql-insert-date/) – learn how to insert date values into a DATE column.
- [UPDATE ](https://www.mysqltutorial.org/mysql-basics/mysql-update/)– learn how to use the UPDATE statement to update data in database tables.
- [UPDATE JOIN ](https://www.mysqltutorial.org/mysql-basics/mysql-update-join/)– show you how to perform cross-table updates using the UPDATE JOIN statement with INNER JOIN and LEFT JOIN.
- [DELETE ](https://www.mysqltutorial.org/mysql-basics/mysql-delete/)– show you how to use the DELETE statement to delete rows from one or more tables.
- [ON DELETE CASCADE ](https://www.mysqltutorial.org/mysql-basics/mysql-on-delete-cascade/)– learn how to use ON DELETE CASCADE referential action for a foreign key to delete data from a child table automatically when you delete data from a parent table.
- [DELETE JOIN ](https://www.mysqltutorial.org/mysql-basics/mysql-delete-join/)– show you how to delete data from multiple tables.
- [REPLACE ](https://www.mysqltutorial.org/mysql-basics/mysql-replace/)– learn how to insert or update data depending on whether data exists in the table or not.

## Section 13. Common Table Expressions

- [Common Table Expression](https://www.mysqltutorial.org/mysql-basics/mysql-cte/) – explain to you the common table expression concept and show you how to use CTE for querying data from tables.
- [Recursive CTE](https://www.mysqltutorial.org/mysql-basics/mysql-recursive-cte/) – use the recursive CTE to traverse the hierarchical data.

## Section 14. MySQL Locking

- [Table locking](https://www.mysqltutorial.org/mysql-basics/mysql-table-locking/) – learn how to use MySQL locking for cooperating table access between sessions.

## Section 15. MySQL globalization

- [Character Set ](https://www.mysqltutorial.org/mysql-basics/mysql-character-set/)– discuss character set and show you step by step how to perform various operations on character sets.
- [Collation ](https://www.mysqltutorial.org/mysql-basics/mysql-collation/)– discuss collation and show you how to set character sets and collations for the MySQL server, database, tables, and columns.

## Section 16. User-defined variables

- [Import CSV File Into MySQL Table](https://www.mysqltutorial.org/mysql-basics/import-csv-file-mysql-table/) – show you how to use LOAD DATA INFILE statement to import CSV files into a MySQL table.
- [Export MySQL Table to CSV](https://www.mysqltutorial.org/mysql-basics/mysql-export-table-to-csv/) –  learn various techniques of how to export MySQL table to a CSV file format.

## Section 17. MySQL import & export CSV

- [User-defined Variables](https://www.mysqltutorial.org/mysql-basics/mysql-variables/) – learn how to use MySQL user-defined variables in SQL statements.
- [SELECT INTO Variable](https://www.mysqltutorial.org/mysql-basics/mysql-select-into-variable/) – show you how to use the MySQL SELECT INTO variable to store query results in one or more variables.

## Section 18. Advanced techniques

- [Natural sorting](https://www.mysqltutorial.org/mysql-basics/mysql-natural-sorting/) – walk you through various natural sorting techniques in MySQL using the ORDER BY clause.
- [Get Row Count in MySQL](https://www.mysqltutorial.org/mysql-basics/mysql-row-count/) – show you various ways to get MySQL row count in the MySQL database.
- [Compare Two Tables](https://www.mysqltutorial.org/mysql-basics/mysql-compare-two-tables/) – show you how to compare two tables to find the unmatched records in MySQL
- [Find Duplicate Values](https://www.mysqltutorial.org/mysql-basics/mysql-find-duplicate-values/) – show you step by step how to find duplicate values in one or more columns in MySQL.
- [Delete Duplicate Rows](https://www.mysqltutorial.org/mysql-basics/mysql-delete-duplicate-rows/) – learn how to delete duplicate rows in MySQL by using the DELETE JOIN statement or an immediate table.
- [Copy Tables](https://www.mysqltutorial.org/mysql-basics/mysql-copy-table/) – copy tables within the same database or from one database to another using CREATE TABLE LIKE and SELECT statements in MySQL.
- [Compare Successive Rows within the same table](https://www.mysqltutorial.org/mysql-basics/mysql-compare-rows-within-the-same-table/) – how to compare successive rows within the same table.
- [Select Random Records](https://www.mysqltutorial.org/mysql-basics/mysql-select-random/) – Show you various techniques to select random records from a database table.
- [Select The nth Highest Record](https://www.mysqltutorial.org/mysql-basics/select-the-nth-highest-record-in-a-database-table/) – learn how to the nth highest record in a database table using various techniques.
- [Reset Auto Increment Values](https://www.mysqltutorial.org/mysql-basics/mysql-reset-auto-increment-value/) – reset auto-increment values of AUTO_INCREMENT columns.
- [MySQL Comments](https://www.mysqltutorial.org/mysql-basics/mysql-comment/) – show you how to use MySQL comments to document an SQL statement or a block of code.
- [MySQL NULL](https://www.mysqltutorial.org/mysql-basics/mysql-null/) – learn how to work with MySQL NULL values. In addition, you’ll learn some useful functions to deal with the NULL values effectively.
- [Map NULL to Meaningful Values](https://www.mysqltutorial.org/mysql-basics/avoid-displaying-null-values-by-mapping-to-other-values/) – map NULL to other values for a better data representation.
- [Managing Hierarchical Data in MySQL Using the Adjacency List Model](https://www.mysqltutorial.org/mysql-basics/mysql-adjacency-list-tree/) – learn how to use the adjacency list model for managing hierarchical data in MySQL.
- [MySQL Interval](https://www.mysqltutorial.org/mysql-basics/mysql-interval/) – show you how to use the MySQL interval for date and time arithmetic with many practical examples.
- [MySQL commands](https://www.mysqltutorial.org/mysql-basics/mysql-commands/) – learn how to use the most commonly used mysql commands for mysql client.
