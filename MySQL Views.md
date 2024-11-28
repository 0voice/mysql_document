原文地址：https://www.mysqltutorial.org/mysql-views/



# MySQL Views

**Summary**: in this tutorial, you will learn about MySQL views and how to manipulate views effectively.

## Introduction to MySQL Views

Let’s see the following tables `customers` and `payments` from the [sample database](https://www.mysqltutorial.org/getting-started-with-mysql/mysql-sample-database/).

![image](https://github.com/user-attachments/assets/1a739552-bfa9-4b02-a671-96786238eb86)

This [query](https://www.mysqltutorial.org/mysql-basics/mysql-select-from/) returns data from both tables `customers` and `payments` using the [inner join](https://www.mysqltutorial.org/mysql-basics/mysql-inner-join/):

![image](https://github.com/user-attachments/assets/e6bc3df2-de85-4954-bfb5-b2488f789c9b)

Here is the output:

![image](https://github.com/user-attachments/assets/077c2565-16a3-4a2c-b484-ff21e282b702)

Next time, if you want to get the same information including customer name, check number, payment date, and amount, you need to issue the same query again.

One way to do this is to save the query in a file, either .txt or .sql file so that later you can open and execute it from MySQL Workbench or any other MySQL client tools.

A better way to do this is to save the query in the database server and assign a name to it. This named query is called a **database view,** or simply, **view**.

By definition, a view is a named query stored in the database catalog.

To create a new view you use the `CREATE VIEW` statement. This statement creates a view `customerPayments` based on the above query above:

![image](https://github.com/user-attachments/assets/30fdca68-1c80-4a65-9a1f-7d339512469c)

After you execute the `CREATE VIEW` statement, MySQL creates the view and stores it in the database.

Now, you can reference the view as a table in SQL statements. For example, you can query data from the `customerPayments` view using the `SELECT` statement:

![image](https://github.com/user-attachments/assets/a8ad17e3-4640-4fdc-ba92-9748d1eee145)

As you can see, the syntax is much simpler.

Note that a view does not physically store the data. When you issue the `SELECT` statement against the view, MySQL executes the underlying query specified in the view’s definition and returns the result set. For this reason, sometimes, a view is referred to as a virtual table.

MySQL allows you to create a view based on a `SELECT` statement that retrieves data from one or more tables. This picture illustrates a view based on columns of multiple tables:

![image](https://github.com/user-attachments/assets/4e028eb6-31c4-4b41-a4ec-24d0a06e0106)

In addition, MySQL even allows you to create a view that does not refer to any table. But you will rarely find this kind of view in practice.

For example, you can create a view called daysofweek that return 7 days a week by executing the following query:

![image](https://github.com/user-attachments/assets/de8d29fd-c48b-412e-89c1-596305cc47eb)

You can query data from the daysofweek view as follows:

![image](https://github.com/user-attachments/assets/d3547d87-ac6f-45dc-959b-85f244da458c)

This picture shows the output:

![image](https://github.com/user-attachments/assets/d6d42fa8-8a7a-4133-bc27-4040be5f9f24)

## Advantages of MySQL Views

MySQL views bring the following advantages.

### 1) Simplify complex query

Views help simplify complex queries. If you have any frequently used complex query, you can create a view based on it so that you can reference the view by using a simple `SELECT` statement instead of typing the query all over again.

### 2) Make the business logic consistent

Suppose you have to repeatedly write the same formula in every query. Or you have a query that has complex business logic. To make this logic consistent across queries, you can use a view to store the calculation and hide the complexity.

### 3) Add extra security layers

A table may expose a lot of data including sensitive data such as personal and banking information.

By using views and privileges, you can limit which data users can access by exposing only the necessary data to them.

For example, the table `employees` may contain SSN and address information, which should be accessible by the HR department only.

To expose general information such as first name, last name, and gender to the General Administration (GA) department, you can create a view based on these columns and grant the users of the GA department the view, not the entire table `employees` .

### 4) Enable backward compatibility

In legacy systems, views can enable backward compatibility.

Suppose, you want to normalize a big table into many smaller ones. And you don’t want to impact the current applications that reference the table.

In this case, you can create a view whose name is the same as the table based on the new tables so that all applications can reference the view as if it were a table.

Note that a view and table cannot have the same name so you need to [drop the table](https://www.mysqltutorial.org/mysql-drop-table) first before creating a view whose name is the same as the deleted table.

## Managing views in MySQL

- [Create views](https://www.mysqltutorial.org/mysql-views/mysql-create-view/) – show you how to use the `CREATE VIEW` statement to create a new view in the database.
- [Understand view processing algorithms](https://www.mysqltutorial.org/mysql-views/mysql-view-processing-algorithms/) – learn how MySQL processes a view.
- [Create updatable views](https://www.mysqltutorial.org/mysql-views/mysql-updatable-views/) – learn how to create updatable views.
- Create views with a `WITH CHECK OPTION` – ensure the consistency of views using the `WITH CHECK OPTION` clause.
- `LOCAL & CASCADED` and `WITH CHECK OPTION` – specify the scope of the check with `LOCAL` and `CASCADED` options.
- [Show views](https://www.mysqltutorial.org/mysql-views/mysql-show-view/) – provide ways to find views in a database.
- [Show create view](https://www.mysqltutorial.org/mysql-views/mysql-show-create-view/) – learn how to display the statement that creates a view.
- [Rename views](https://www.mysqltutorial.org/mysql-views/mysql-rename-view/) – change the name of a view to another.
- [Drop views](https://www.mysqltutorial.org/mysql-views/mysql-drop-view/) – guide you on how to remove one or more existing views.
