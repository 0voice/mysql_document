原文地址：https://www.mysqltutorial.org/mysql-stored-procedure/



# MySQL Stored Procedures

In this section, you will learn about MySQL stored procedures and how to define stored procedures for your database.

## Section 1. Basic MySQL Stored procedures

- [Introduction to MySQL stored Procedures](https://www.mysqltutorial.org/mysql-stored-procedure/introduction-to-sql-stored-procedures/)– introduce you to MySQL stored procedures, their advantages, and disadvantages.
- [Changing the default delimiter](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-delimiter/) – learn how to change the default delimiter in MySQL.
- [Creating new stored procedures](https://www.mysqltutorial.org/mysql-stored-procedure/getting-started-with-mysql-stored-procedures/) – show you how to create and use the `CREATE PROCEDURE` statement to create a new stored procedure in the database.
- [Removing stored procedures](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-drop-procedure/) – show you how to use the `DROP PROCEDURE` statement to drop an existing stored procedure.
- [Variables](https://www.mysqltutorial.org/mysql-stored-procedure/variables-in-stored-procedures/) – guide on you how to use variables to hold immediate results inside stored procedures.
- [Parameters](https://www.mysqltutorial.org/mysql-stored-procedure/stored-procedures-parameters-in-mysql/) – introduce you to various types of parameters used in stored procedures including `IN`, `OUT`, and `INOUT` parameter.
- [Altering stored procedure](https://www.mysqltutorial.org/mysql-stored-procedure/alter-stored-procedure/) – show you step by step how to alter a stored procedure using a sequence of `DROP PROCEDURE` and `CREATE PROCEDURE` statements in MySQL Workbench.
- [Listing stored procedures](https://www.mysqltutorial.org/mysql-stored-procedure/listing-stored-procedures-in-a-mysql-database/) – provide you with some useful commands to list stored procedures from databases.

## Section 2. Conditional Statements

- [IF statement](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-if-statement/) – show you how to use the `IF THEN` statement in stored procedures.
- [CASE statement](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-case-statement/) – introduce you to the `CASE` statements including simple `CASE` and searched `CASE` statements.

## Section 3. Loops

- [LOOP](https://www.mysqltutorial.org/mysql-stored-procedure/loop-in-stored-procedures/) – learn how to execute a list of statements repeatedly based on a condition.
- [WHILE Loop](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-while-loop/) – show you how to execute a loop as long as a condition is true.
- [REPEAT Loop](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-repeat-loop/) – show you how to execute a loop until a search condition is true.
- [LEAVE statement](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-leave/) – guide you on how to exit a loop immediately.

## Section 4. Error Handling

- [Show Warnings](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-show-warnings/) – Learn how to display errors and warnings of the latest query execution.
- [Show Errors](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-show-errors/) – Learn how to display errors of the latest query execution.
- [DECLARE … HANDLER](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-declare-handler/) – Show you how to declare exit or continue handler to handle errors within stored procedures.
- [DECLARE … CONDITION](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-declare-condition/) – Guide you on how to associate a name with a condition specified by a MySQL error code or an SQL state value.
- [SIGNAL](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-signal/) – Learn how to raise an exception (error or warning) using the `SIGNAL` statement.
- [RESIGNAL](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-resignal/) – show you how to re-raise an exception using the `RESIGNAL` statement.

## Section 5. Cursors

- [Cursors](https://www.mysqltutorial.org/mysql-stored-procedure/sql-cursor-in-stored-procedures/) – learn how to use cursors to process row by row in a result set.
- [Prepared Statements](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-prepared-statement/) – guide you on how to use prepared statements to make your queries more secure and faster to execute.

## Section 6. Stored Functions

- [Creating a stored function](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-stored-function/) – show you how to create stored functions in the database.
- [Removing a stored function](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-drop-function/) – use the `DROP FUNCTION` statement to remove a stored function.
- [Listing stored functions](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-show-functions/) – learn how to list all stored functions in the database by using the `SHOW FUNCTION STATUS` statement or querying from the data dictionary.

## Section 7. Stored Program Security

- [Stored object access control](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-stored-object-access-control/) – learn how to control the security of the stored objects.

## Section 8. Transactions

- [Transactions](https://www.mysqltutorial.org/mysql-stored-procedure/mysql-transactions/) – guide you on how to use transactions in stored procedures to ensure data integrity.
