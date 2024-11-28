原文地址：https://www.mysqltutorial.org/mysql-regular-expressions/



# MySQL Regular Expressions

**Summary**: in this tutorial, you will learn about MySQL regular expressions and how to use the regular expression functions and operators.

## Introduction to MySQL regular expressions

Regular expressions are special strings that describe search patterns. It is a powerful tool that gives you a concise and flexible way to identify text strings, such as characters and words, based on patterns.

For example, you can use regular expressions to search for emails, IP addresses, phone numbers, social security numbers, or anything with a specific pattern.

Regular expressions have their own syntax that a regular expression processor can interpret. Regular expressions are widely used across various platforms, from programming languages to databases, including MySQL.

The advantage of using regular expression is that you are not limited to searching for a string based on a fixed pattern with the percent sign (%) and underscore (_) in the `LIKE` operator. The regular expressions offer a wide range of meta-characters to create flexible patterns.

The disadvantage of using regular expressions is that they can be challenging to understand and maintain, especially with such complicated patterns. Therefore, it is advisable to provide a comment in the SQL statement to explain the purpose of the regular expression. Additionally, in some cases, complex patterns can lead to decreased data retrieval speed.

MySQL uses International Components for Unicode (ICU) to support regular expression. This ensures that Unicode and compatibility with multibyte characters are fully supported.

Note that prior to MySQL 8.0.4, MySQL implemented regular expressions using Henry Spencer’s method, which worked with bytes and was not multibyte safe.

## MySQL regular expression functions & operators

The following table illustrates the regular expression functions and operators in MySQL:

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [REGEXP](https://www.mysqltutorial.org/mysql-regular-expressions/mysql-regexp/) | Return 1 if a string matches a regular expression or 0 otherwise. |
| [REGEXP_INSTR()](https://www.mysqltutorial.org/mysql-regular-expressions/mysql-regexp_instr/) | Return the starting index of a substring that matches a regular expression. |
| [REGEXP_LIKE()](https://www.mysqltutorial.org/mysql-regular-expressions/mysql-regexp_like/) | Test if a string matches a regular expression.               |
| [REGEXP_REPLACE()](https://www.mysqltutorial.org/mysql-regular-expressions/mysql-regexp_replace/) | Replace substrings that match a regular expression.          |
| [REGEXP_SUBSTR()](https://www.mysqltutorial.org/mysql-regular-expressions/mysql-regexp_substr/) | Extract a substring that matches a regular expression.       |
| [RLIKE](https://www.mysqltutorial.org/mysql-regular-expressions/mysql-regexp/) | Determine if a string matches a regular expression.          |

## Regular expression syntax

In this section, we’ll cover only the basic regular expression syntax.

### 1) Literals

To form a regular expression in MySQL, you use a regular string. For example:

```
'MySQL'Code language: JavaScript (javascript)
```

This is a simple expression that matches the string `MySQL`. If the input string is `'MySQL is awesome'`, it’ll have one match.

### 2) Character classes

Character classes are sets of characters, for example, digits (0-9), and alphabets (a-z). Regular expressions use character classes to match sets of characters.

Here are some common character classes:

| Character class | Description                                                  |
| :-------------- | :----------------------------------------------------------- |
| \d              | matches a single digit from 0 to 9.                          |
| \w              | matches a word character set that includes alphabet, digit, and underscore. |
| \s              | matches a whitespace including a space, a tab a new line, a carriage return, and a vertical tab. |

Each character class has an inverse character class. For example, \D matches a single character except a digit. Similarly, \W matches a single character that is not a word character and \S matches a single character except whitespace.

### 3) Anchors

The anchors match the character’s position. The anchors include `^` which matches the beginning of a string and `$` which matches the end of a string.

For example, `^\d` match a digit at the beginning of a string. It’ll match the number 1 in the string ‘1 dolphin’.

### 4) Quantifiers

The quantifiers define how many times a character or a character should occur within a pattern. The following table displays the quantifiers in regular expressions:

| Quantifier | Description                                                  |
| :--------- | :----------------------------------------------------------- |
| *          | Matches zero or more occurrences of the preceding character or class. |
| +          | Matches one or more occurrences of the preceding character or class. |
| ?          | Matches zero or one occurrence of the preceding character or class. |
| {n}        | Matches exactly n occurrences of the preceding character or class. |
| {n,}       | Matches n or more occurrences of the preceding character or class. |
| {n,m}      | Matches between n and m occurrences of the preceding character or class. |

## MySQL regular expression examples

Let’s take some examples of using MySQL regular expression functions and operators.

### 1) Checking if a string matches a regular expression

To determine if a string matches a regular expression, you use the `REGEXP` operator:

```
expression REGEXP patternCode language: SQL (Structured Query Language) (sql)
```

The `REGEXP` returns 1 if the expression matches the regular expression specified by the pattern, 0 otherwise.

If the expression or pattern is `NULL`, the `REGEXP` operator returns 0.

For example, the following query uses the `REGEPX` operator to check if the string MySQL 8 matches the regular expression specified by the pattern `"\\d+"`:

```
SELECT 'MySQL 8' REGEXP '\\d+';Code language: SQL (Structured Query Language) (sql)
```

Output:

```
+-------------------------+
| 'MySQL 8' REGEXP '\\d+' |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set (0.00 sec)Code language: SQL (Structured Query Language) (sql)
```

The pattern “\\d+” matches one or more digits in a string. Therefore, the `REGEXP` returns 1 in this example.

If you remove the number 8, it’ll return 0 because the string `MySQL` has no digits:

```
SELECT 'MySQL' REGEXP '\\d+';Code language: SQL (Structured Query Language) (sql)
```

Output:

```
+-----------------------+
| 'MySQL' REGEXP '\\d+' |
+-----------------------+
|                     0 |
+-----------------------+
1 row in set (0.00 sec)Code language: SQL (Structured Query Language) (sql)
```

The `RLIKE` works the same as the `REGPEX` operator:

```
SELECT 'MySQL 8' RLIKE '\\d+';Code language: SQL (Structured Query Language) (sql)
```

Output:

```
+------------------------+
| 'MySQL 8' RLIKE '\\d+' |
+------------------------+
|                      1 |
+------------------------+
1 row in set (0.00 sec)Code language: SQL (Structured Query Language) (sql)
```

Note that `REGEXP` and `RLIKE` are synonyms for `REGEXP_LIKE()`.

### 2) Getting the index of the substring that matches a regular expression

To get the position of the substring that matches a regular expression, you use the `REGEXP_INSTR()` function.

Here’s the basic syntax of the `REGEXP_INSTR()` function:

```
REGEXP_INSTR(expression, patttern)Code language: SQL (Structured Query Language) (sql)
```

Note that `REGEXP_INSTR()` function has more parameters that we’ll cover in the [REGEXP_INSTR tutorial](https://www.mysqltutorial.org/mysql-regular-expressions/mysql-regexp_instr/).

The following example uses the `REGEXP_INSTR()` function to find the position of the first digit in the string `'MySQL 8.0 Release'` :

```
SELECT 
   REGEXP_INSTR('MySQL 8.0 Release', '\\d+') position;Code language: SQL (Structured Query Language) (sql)
```

Output:

```
+----------+
| position |
+----------+
|        7 |
+----------+
1 row in set (0.00 sec)Code language: SQL (Structured Query Language) (sql)
```

It returns the index 7, which is the position of the number 8 in the input string.

### 3) Extracting the matches

To return the substring that matches a regular expression, you use the `REGEXP_SUBSTR()` function:

```
REGEXP_SUBSTR(expression, patttern)Code language: SQL (Structured Query Language) (sql)
```

For example, the following returns the first number in the string `'MySQL 8.0 Release'`:

```
SELECT 
  REGEXP_SUBSTR('MySQL 8.0 Release', '\\d+') version;Code language: SQL (Structured Query Language) (sql)
```

Output:

```
+---------+
| version |
+---------+
| 8       |
+---------+
1 row in set (0.00 sec)Code language: SQL (Structured Query Language) (sql)
```

### 4) Replacing the matches

To replace the match with a new pattern, you use the `REGEXP_REPLACE()` function:

```
REGEXP_REPLACE(
  expression, 
  pattern, 
  replacement
)Code language: SQL (Structured Query Language) (sql)
```

For example, the following uses the `REGEXP_REPLACE()` function to replace the match by the number 9:

```
SELECT 
  REGEXP_REPLACE('MySQL 8 Release', '\\d+', 9);Code language: SQL (Structured Query Language) (sql)
```

Output:

```
+----------------------------------------------+
| REGEXP_REPLACE('MySQL 8 Release', '\\d+', 9) |
+----------------------------------------------+
| MySQL 9 Release                              |
+----------------------------------------------+
1 row in set (0.00 sec)
```
