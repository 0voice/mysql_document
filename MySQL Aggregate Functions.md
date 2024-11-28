原文地址：https://www.mysqltutorial.org/mysql-aggregate-functions/



# MySQL Aggregate Functions

**Summary**: in this tutorial, you will learn about MySQL aggregate functions including `AVG`, `COUNT`, `SUM`, `MAX` and `MIN.`

## Introduction to MySQL aggregate functions

An aggregate function performs a calculation on multiple values and returns a single value.

For example, you can use the `AVG()` aggregate function that takes multiple numbers and returns the average value of the numbers.

The following illustrates the syntax of an aggregate function:

![image](https://github.com/user-attachments/assets/c66d31f3-ea73-44fc-84dc-7c472eeb7bee)

In this syntax:

- First, specify the name of the aggregate function e.g., `AVG()`. See the list of aggregate functions in the following section.
- Second, use `DISTINCT` if you want to calculate based on distinct values or `ALL` in case you want to calculate all values including duplicates. The default is `ALL`.
- Third, specify an expression that can be a column or an expression that involves column and arithmetic operators.

The aggregate functions are often used with the `GROUP BY` clause to calculate an aggregate value for each group e.g., the average value by the group or the sum of values in each group.

The following picture illustrates the `SUM()` aggregate function is used in conjunction with a `GROUP BY` clause:

![image](https://github.com/user-attachments/assets/5e967602-cb0d-451d-b113-ab6f6f48619e)

MySQL supports the following aggregate functions:

| Aggregate function                                           | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [AVG()](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-avg/) | Return the summation of all non-NULL values in a set.        |
| [BIT_AND()](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-bit_and/) | Perform a bitwise AND of values in a column of a table.      |
| [BIT_OR()](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-bit_or/) | Perform a bitwise OR of values in a column of a table.       |
| [BIT_XOR()](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-bit_xor/) | Perform a bitwise XOR of values in a column of a table.      |
| [COUNT()](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-count/) | Return the number of rows in a group, including rows with NULL values. |
| [COUNT(DISTINCT)](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-count-distinct/) | Count the number of unique values of a column in a table.    |
| [COUNT(IF)](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-count-if/) | Count the number of values that meet a specified condition.  |
| [GROUP_CONCAT()](https://www.mysqltutorial.org/mysql-group_concat/) | Return a concatenated string.                                |
| [JSON_ARRAYAGG()](https://www.mysqltutorial.org/mysql-json/mysql-json_arrayagg/) | Return result set as a single JSON array.                    |
| [JSON_OBJECTAGG()](https://www.mysqltutorial.org/mysql-json/mysql-json_objectagg/) | Return result set as a single JSON object.                   |
| [MAX()](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-max-function/) | Return the highest value (maximum) in a set of non-NULL values. |
| [MIN()](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-min/) | Return the lowest value (minimum) in a set of non-NULL values. |
| [STDEV()](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-standard-deviation/) | Return the summation of all non-NULL values in a set.        |
| [STDDEV_POP()](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-standard-deviation/) | Return the population standard deviation.                    |
| [STDDEV_SAMP()](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-standard-deviation/) | Return the sample standard deviation.                        |
| [SUM()](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-sum/) | Return the summation of all non-NULL values a set.           |
| [SUM(IF)](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-sum-if/) | Perform conditional summation using the SUM and IF functions. |
| [VAR_POP()](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-variance/) | Return the summation of all non-NULL values in a set.        |
| [VAR_SAMP()](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-var_samp/) | Return the sample variance of values in a column of a table. |
| [VARIANCE()](https://www.mysqltutorial.org/mysql-aggregate-functions/mysql-variance/) | Return the population standard variance of all non-NULL values in a set. |



## MySQL aggregate function examples

We will use the `products` and `orderdetails` tables from the [sample database](https://www.mysqltutorial.org/getting-started-with-mysql/mysql-sample-database/) for demonstration:

![image](https://github.com/user-attachments/assets/f4e2c839-0315-42f1-9c41-db6fc05e582b)

### The AVG() function examples

The `AVG()` function calculates the average value of a set of values. It ignores NULL in the calculation.

```
AVG(expression)Code language: SQL (Structured Query Language) (sql)
```

For example, you can use the `AVG` function to calculate the average buy price of all products in the `products` table by using the following query:

```
SELECT 
    AVG(buyPrice) average_buy_price
FROM 
    products;Code language: SQL (Structured Query Language) (sql)
```

[Try It Out](https://www.mysqltutorial.org/tryit/query/mysql-aggregate-functions/#1)

![MySQL Aggregate Function - AVG example](https://www.mysqltutorial.org/wp-content/uploads/2019/09/MySQL-Aggregate-Function-AVG-example.png)

The following example uses the `AVG()` function to calculate the average buy price for each product line:

```
SELECT 
    productLine, 
    AVG(buyPrice)
FROM
    products
GROUP BY productLine
ORDER BY productLine;
Code language: SQL (Structured Query Language) (sql)
```

[Try It Out](https://www.mysqltutorial.org/tryit/query/mysql-aggregate-functions/#1)

![MySQL Aggregate Function - AVG with GROUP BY example](https://www.mysqltutorial.org/wp-content/uploads/2019/09/MySQL-Aggregate-Function-AVG-with-GROUP-BY-example.png)

### The COUNT() function examples

The `COUNT()` function returns the number of the values in a set.

For example, you can use the `COUNT()` function to get the number of products in the `products` table as shown in the following query:

```
SELECT 
    COUNT(*) AS total
FROM 
    products;Code language: PHP (php)
```

[Try It Out](https://www.mysqltutorial.org/tryit/query/mysql-aggregate-functions/#4)

![MySQL Aggregate Function - COUNT example](https://www.mysqltutorial.org/wp-content/uploads/2019/09/MySQL-Aggregate-Function-COUNT-example.png)

The following statement uses the `COUNT()` function with the `GROUP BY` clause to get the number of products for each product line:

```
SELECT 
    productLine, 
    COUNT(*)
FROM
    products
GROUP BY productLine
ORDER BY productLine;
Code language: SQL (Structured Query Language) (sql)
```

[Try It Out](https://www.mysqltutorial.org/tryit/query/mysql-aggregate-functions/#1)

![MySQL Aggregate Function - COUNT with GROUP BY example](https://www.mysqltutorial.org/wp-content/uploads/2019/09/MySQL-Aggregate-Function-COUNT-with-GROUP-BY-example.png)

### The SUM() function examples

The `SUM()` function returns the sum of values in a set. The `SUM()` function ignores `NULL`. If no matching row is found, the `SUM()` function returns NULL.

To get the total order value of each product, you can use the `SUM()` function in conjunction with the `GROUP BY` clause as follows:

```
SELECT 
    productCode, 
    SUM(priceEach * quantityOrdered) total
FROM
    orderDetails
GROUP BY productCode
ORDER BY total DESC;Code language: SQL (Structured Query Language) (sql)
```

[Try It Out](https://www.mysqltutorial.org/tryit/query/mysql-aggregate-functions/#6)

![MySQL Aggregate Function - SUM example](https://www.mysqltutorial.org/wp-content/uploads/2019/09/MySQL-Aggregate-Function-SUM-example.png)

To see the result in more detail, you can [join](https://www.mysqltutorial.org/mysql-basics/mysql-join/) the `orderdetails` table to the `products` table as shown in the following query:

```
SELECT 
    productCode,
    productName,
    SUM(priceEach * quantityOrdered) total
FROM
    orderDetails
        INNER JOIN
    products USING (productCode)
GROUP BY productCode
ORDER BY total;Code language: SQL (Structured Query Language) (sql)
```

[Try It Out](https://www.mysqltutorial.org/tryit/query/mysql-aggregate-functions/#1)

![MySQL Aggregate Function - SUM with JOIN example](https://www.mysqltutorial.org/wp-content/uploads/2019/09/MySQL-Aggregate-Function-SUM-with-JOIN-example.png)

### The MAX() function examples

The `MAX()` function returns the maximum value in a set.

```
MAX(expression)Code language: SQL (Structured Query Language) (sql)
```

For example, you can use the `MAX()` function to get the highest buy price from the `products` table as shown in the following query:

```
SELECT 
     MAX(buyPrice) highest_price
FROM 
     products;Code language: SQL (Structured Query Language) (sql)
```

[Try It Out](https://www.mysqltutorial.org/tryit/query/mysql-aggregate-functions/#8)

![MySQL Aggregate Function - MAX example](https://www.mysqltutorial.org/wp-content/uploads/2019/09/MySQL-Aggregate-Function-MAX-example.png)

The following statement uses the `MAX()` function with the `GROUP BY` clause to get the highest price per product line:

```
SELECT 
    productLine, MAX(buyPrice)
FROM
    products
GROUP BY productLine
ORDER BY MAX(buyPrice) DESC;Code language: SQL (Structured Query Language) (sql)
```

[Try It Out](https://www.mysqltutorial.org/tryit/query/mysql-aggregate-functions/#1)

![MySQL Aggregate Function - MAX with GROUP BY example](https://www.mysqltutorial.org/wp-content/uploads/2019/09/MySQL-Aggregate-Function-MAX-with-GROUP-BY-example.png)

### The MIN() function examples

The `MIN()` function returns the minimum value in a set of values.

```
MIN(expression)Code language: SQL (Structured Query Language) (sql)
```

For example, the following query uses the `MIN()` function to find the lowest price from the `products` table:

```
SELECT 
    MIN(buyPrice) lowest_price
FROM 
    products;Code language: SQL (Structured Query Language) (sql)
```

[Try It Out](https://www.mysqltutorial.org/tryit/query/mysql-aggregate-functions/#11)

![MySQL Aggregate Function - MIN example](https://www.mysqltutorial.org/wp-content/uploads/2019/09/MySQL-Aggregate-Function-MIN-example.png)

The following example uses the `MIN()` function with the `GROUP BY` clause to get the lowest price per product line:

```
SELECT 
    productLine, 
    MIN(buyPrice)
FROM
    products
GROUP BY productLine
ORDER BY MIN(buyPrice);Code language: SQL (Structured Query Language) (sql)
```

[Try It Out](https://www.mysqltutorial.org/tryit/query/mysql-aggregate-functions/#1)

![MySQL Aggregate Function - MIN with GROUP BY example](https://www.mysqltutorial.org/wp-content/uploads/2019/09/MySQL-Aggregate-Function-MIN-with-GROUP-BY-example.png)

### The GROUP_CONCAT() function example

The `GROUP_CONCAT()` concatenates a set of strings and returns the concatenated string. See the following `employees` and `customers` tables:

![img](https://www.mysqltutorial.org/wp-content/uploads/2019/09/customers-employees.png)

The following statement uses the `GROUP_CONCAT()` function to return the sales staff and list of customers that each sales staff is in charge of:

```
SELECT 
    firstName,
    lastName,
    GROUP_CONCAT(
	DISTINCT customername
        ORDER BY customerName) customers
FROM
    employees
INNER JOIN customers 
	ON customers.salesRepEmployeeNumber = employeeNumber
GROUP BY employeeNumber
ORDER BY firstName , lastname;Code language: SQL (Structured Query Language) (sql)
```

[Try It Out](https://www.mysqltutorial.org/tryit/query/mysql-aggregate-functions/#1)

![MySQL Aggregate Function - GROUP_CONCAT example](https://www.mysqltutorial.org/wp-content/uploads/2019/09/MySQL-Aggregate-Function-GROUP_CONCAT-example.png)

In this tutorial, you have learned how to use the most commonly used MySQL aggregate functions.
