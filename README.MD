# SQL Notes

Notes and references on SQL. Taken from various places on the internet.

## Table of Contents

* [Overview](#overview)
* [Schemas](#schemas)
  * [Star Schema](#star-schema)
  * [Snowflake Schema](#snowflake-schema)
* [Terminology](#terminology)
  * [Data Definiton Language (DDL)](#data-definiton-language-ddl)
  * [Data Manipulation Language (DML)](#data-manipulation-language-dml)
* [Indexes](#indexes)
* [Selecting](#selecting)
* [Filtering Data](#filtering-data)
* [Operators](#operators)
  * [LIKE Operator](#like-operator)
  * [IN operator](#in-operator)
  * [ANY and ALL](#any-and-all)
* [NULL values](#null-values)
* [Ordering](#ordering)
* [Data Definiton Language (DDL) Commands](#data-definiton-language-ddl-commands)
  * [Creating Tables](#creating-tables)
  * [Inserting Values](#inserting-values)
  * [Updating Values](#updating-values)
* [Aggregate Functions](#aggregate-functions)
  * [MIN() and MAX() functions](#min-and-max-functions)
  * [COUNT(), SUM(), and AVG() functions](#count-sum-and-avg-functions)
* [JOINS](#joins)
  * [ON WHERE vs ON AND in JOINS](#on-where-vs-on-and-in-joins)
* [Other Functions](#other-functions)
  * [Union](#union)
  * [Intersect](#intersect)
  * [Except](#except)
  * [Group Concat](#group-concat)
* [Grouping](#grouping)
  * [Having](#having)
* [Case Statements](#case-statements)
* [Common Table Expresions (CTEs) / WITH Clauses](#common-table-expresions-ctes--with-clauses)
* [Derived Tables](#derived-tables)
* [Window Functions](#window-functions)
  * [Frame Clauses](#frame-clauses)
  * [Lag and Lead](#lag-and-lead)
  * [Ranking Functions](#ranking-functions)
* [Built-In Functions](#built-in-functions)
* [Date Functions](#date-functions)
* [Other](#other)
* [References](#references)

## Overview

* SQL syntax is different, but ANSI SQL is standard.
* **Not** case sensitive.
* Semi-optional: Use a semi-colon at the end of each statement.

## Schemas

### Star Schema
* Not normalized.
* Good for simple relationships.

### Snowflake Schema
* Normalized, no redundant information.
* **Fact tables** contains business facts, and have foreign keys which refer to candidate keys in the dimension tables.
* **Dimension tables** contain descriptive fields that are typically textual fields.

## Terminology

### Data Definiton Language (DDL)
DDL is used for creating and modifying database objects such as tables, indexes, and users.  

Includes statements like:
```SQL
CREATE, ALTER, DROP, TRUNCATE, COMMENT, RENAME
```

#### Data Manipulation Language (DML)
DML is used for adding (inserting), deleting, and modifying (updating) data in a database.

Includes statements like:
```SQL
SELECT, INSERT, UPDATE, DELETE, MERGE, CALL, EXPLAIN PLAN, LOCK TABLE
```

## Indexes
Indexes are used to find rows with specific column values quickly.  Without an index, MySQL must begin with the first row and then read through the entire table to find the relevant rows.  Usually implemented with [b-trees](https://en.wikipedia.org/wiki/B-tree).

## Selecting

A select statement that selects the columns "col1" and "col2"
from the table "table_name".

```sql
SELECT col1, col2
FROM table_name;
```

The statement below will only return distinct values from the "col1" columns.  Alternatively, one could use a `GROUP BY` clause (recommend to use `DISTINCT`).

```sql
SELECT DISTINCT col1 
FROM table_name;
```
If multiple columns are used, the query uses a combination of all values. `NULL` counts as one distinct value only.

```sql
SELECT DISTINCT col1, col2, col3
FROM table_name;
```

You can also get a count of the distinct values:

```sql
SELECT COUNT(DISTINCT col1) 
FROM table_name;
```

In most databases, you can do:
```sql
SELECT TOP [number|percent] column_name(s)
FROM table_name
WHERE condition;
```

Example:
```sql
SELECT col1, col2
FROM table_name
WHERE (col2 + col1) = 4
LIMIT 3;
```


## Filtering Data

The `WHERE` clause:
* Is used to filter records.
* Operators that can be used in `WHERE` clause: `<>`, `BETWEEN`, `LIKE`.
* Can also do `WHERE NOT` <condition>.

```sql
SELECT col1
FROM table_name
WHERE col1 <> 'some_value';

SELECT col1_date
FROM table_name
WHERE col1_date BETWEEN '2020-12-01' AND '2020-12-03';

SELECT col2
FROM table_name
WHERE col2 NOT IN (1, 3, 4);
```

## Operators

Operators include:
* `AND`, `OR`, `NOT`
* `LIKE`, `ALL`, `ANY`, `ILIKE`
* `>`, `>=`, `<`, `<=`, `=`

Note: the equality operator is one *single* equal sign.


### LIKE operator
* `%`: The percent sign represents zero, one, or multiple characters.
* `_`: The underscore represents a single character.

```sql
-- First name must start with A
SELECT first_name
FROM employees
WHERE first_name LIKE 'A%'

-- First name must start with A and be 2 or more characters in length
SELECT first_name
FROM employees
WHERE first_name LIKE 'A_%'
```

The `ILIKE` operator is case insenstive.

### IN operator
```sql
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1, value2, ...);
```

### ANY and ALL


## NULL values
When checking for `NULL` values, you must use the `IS` keyword, not `=`.

* Check using `IS NULL` or `IS NOT NULL`.
* `COALESCE()` takes the first non-null value.  Similar functions in other databases include `NVL`.
* ANSI_NULLS operate [differently](https://stackoverflow.com/questions/9766717/in-sql-server-what-does-set-ansi-nulls-on-mean#:~:text=If%20ANSI_NULLS%20is%20set%20to,compared%20to%20NULL%20is%20TRUE%20.), it is three-valued logic.
* Generally, aggregate functions like `AVG()` dissmisses rows with `NULL` values.

```sql
SELECT col1
FROM table_name
WHERE col1 IS NULL;
```

## Ordering

Put the `ORDER BY` statement at the end of your SQL query.
* Two options: `ASC`, `DESC`
  * Sorted by ascending order by default.
* Can order by multiple columns.

```sql
SELECT column1, column2, ...
FROM table_name
ORDER BY column1, column2, ... [ASC|DESC];
```

## Data Definiton Language (DDL) Commands

### Creating Tables

```sql
CREATE TABLE table_name (
	col1_name TYPE [CONSTRAINTS] [KEY],
	...
);
```

Example:
```sql
CREATE TABLE employees
(
  employee_id int NOT NULL PRIMARY KEY,
  first_name varchar(50) NOT NULL,
  last_name varchar(50) NOT NULL,
  manager_id int NULL
);
```

### Inserting Values

```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

### Updating Values

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

## Aggregate Functions

### MIN() and MAX() functions
* `MIN()` returns the lowest value of the selected column.
* Should use aliases for clarity.

```sql
SELECT MAX(Price) AS LargestPrice
FROM Products;
```

Note: The `LEAST()` and `GREATEST()` [function](https://www.w3schools.com/sql/func_mysql_least.asp) may be of interest.

### COUNT(), SUM(), and AVG() functions

```sql
SELECT col1, COUNT(col2) AS count_col2
FROM table_name
WHERE condition
GROUP BY col1
```

A `COUNT(*)` will count all `NULL` and `NOT NULL` values.

## JOINS

SQL joins include:
* `INNER JOIN`: Selects records that have matching join values in both tables.
* `LEFT (OUTER) JOIN`: Returns all records from the left table (table1), and the matched records from the right table (table2). The result is `NULL` from the right side, if there is no match.
* `RIGHT (OUTER) JOIN`: Same logic as `LEFT JOIN`.
* `FULL (OUTER) JOIN`: Returns all records when there is a match in left (table1) or right (table2) table records.
* `CROSS JOIN`: Does a cartesian product. The total number of rows will be (number of rows in table1)*(number of rows in table2).



```sql
-- INNER JOIN
SELECT DISTINCT c.name
FROM customers c
INNER JOIN orders o
ON c.id = o.cust_id;

-- LEFT JOIN
SELECT 
  c.name
  ,o.order_id
  ,o.order_date
FROM customers c
LEFT JOIN orders o
ON c.id = o.cust_id;

-- CROSS JOIN
SELECT * 
FROM table1 
CROSS JOIN table2;
```


Left Exclusive Join: If you don’t want any of the right table’s values and you just want the left table’s values.

```sql
-- LEFT EXCLUSIVE JOIN
SELECT column_name(s)
FROM table1
LEFT JOIN table2
ON table1.key = table2.key
WHERE table2.key IS NULL;
```

### ON WHERE vs ON AND in JOINS
```SQL
SELECT *
FROM Orders
LEFT JOIN OrderLines 
       ON OrderLines.OrderID = Orders.ID
    WHERE Orders.ID = 12345


SELECT *
FROM Orders
LEFT JOIN OrderLines
       ON OrderLines.OrderID = Orders.ID 
      AND Orders.ID = 12345
```
The first will return an order and its lines, if any, for order number `12345`. The second will return all orders, but only order `12345` will have any lines associated with it.

With an `INNER JOIN`, the clauses are effectively equivalent.

Summary:
* `WHERE`: Filtered **after** joining.
* `AND` Filtered **before** joining.

## Other Functions

### Union

* `UNION ALL`: Does not remove any rows.
* `UNION`: Removes duplicate rows.
* The number of columns from the queries must match.  The datatype of each column must also match.

```sql
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```


### Intersect
`INTERSECT` is used to combine two `SELECT` statements, but returns rows only from the first `SELECT` statement that are identical to a row in the second `SELECT` statement.

### Except
`EXCEPT` combines two `SELECT` statements and returns rows from the first `SELECT` statement that are not returned by the second `SELECT` statement.

In set theory, this is equivalent to A - B = A ∩ B<sup>c</sup>, where A is the first table and B is the second table.

### Group Concat
`GROUP_CONCAT` / `LISTAGG` function is an aggregate function that concatenates strings from a group into a single string with various options.

```sql
GROUP_CONCAT(
    DISTINCT expression
    ORDER BY expression
    SEPARATOR sep
)
```

## Grouping

The `GROUP BY` keyword is used to group values in a column and is usually used with aggregate functions such as `SUM()`, `MAX()`, `COUNT()`.

```sql
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
ORDER BY column_name(s);
```

### Having
* The `HAVING` clause was added to SQL because the `WHERE` keyword could not be used with aggregate functions.
* When `GROUP BY` and `WHERE` is in the same query, `WHERE` clause is used to filter records from a result.  The filtering
occurs **before** any groupings are made.  The `HAVING` clause is used to filter values from a group. 

```sql
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
HAVING aggregate_condition
ORDER BY column_name(s);
```

Example:

```sql
SELECT titles.pub_id, AVG(titles.price) AS avg_price
FROM titles 
INNER JOIN publishers  
  ON titles.pub_id = publishers.pub_id  
WHERE publishers.state = 'CA'  
GROUP BY titles.pub_id  
HAVING AVG(titles.price) > 10  
```

## Case Statements
The CASE statement goes through conditions and returns a value when the first condition is met (like an IF-THEN-ELSE statement).
So, once a condition is true, it will stop reading and return the result. If no conditions are true, it returns the value in the `ELSE` clause.
If there is no `ELSE` part and no conditions are true, it returns `NULL`.

```sql
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ...
    WHEN conditionN THEN resultN
    ELSE result
END;
```

```sql
CASE 
  WHEN condition THEN return_val ELSE some_other_val
END
```

or sum the values
```sql
SUM(CASE WHEN condition THEN 1 ELSE 0 END) AS col_name
```

## Common Table Expresions (CTEs) / WITH Clauses
* Why? An alternative to creating a view.
* Increased readability and simplification.
* Named columns must match selected columns in CTE definition.

```sql
WITH CTE_expression_name([named_col_1, ...]) AS (
  CTE_definition
)
SQL_statement using above CTE;
```

You can write multiple CTEs in one query
```sql
WITH send_timespent AS (
    SELECT age_breakdown.age_bucket, SUM(activities.time_spent) AS send_timespent 
    FROM age_breakdown
  LEFT JOIN activities on age_breakdown.user_id = activities.user_id
    WHERE activities.type = 'send'
    GROUP BY 1
),
open_timespent AS (
    SELECT age_breakdown.age_bucket, SUM(activities.time_spent) AS open_timespent 
    FROM age_breakdown
  LEFT JOIN activities on age_breakdown.user_id = activities.user_id
    WHERE activities.type = 'open'
    GROUP BY 1
),
SELECT a.age_bucket, 
    s.send_timespent / (s.send_timespent + o.open_timespent) AS pct_send,
    o.open_timespent / (s.send_timespent + o.open_timespent) AS pct_open,
FROM age_breakdown a 
LEFT JOIN send_timespent s ON a.age_bucket = s.age_bucket
LEFT JOIN open_timespent o ON a.age_bucket = o.age_bucket
GROUP BY 1;
```

Can be used for recursive statements
```sql
WITH RECURSIVE factorial(n, fact) AS ( 
  SELECT 0, 1 
  UNION ALL  
  SELECT n + 1, fact * (n+1)  
  FROM factorial 
  WHERE n < 20
) 
SELECT * from factorial;

--Output
+------+---------------------+
| n    | fact                |
+------+---------------------+
|    0 |                   1 |
|    1 |                   1 |
|    2 |                   2 |
|    3 |                   6 |
|    4 |                  24 |
|    5 |                 120 |
|    6 |                 720 |
|    7 |                5040 |
|    8 |               40320 |
|    9 |              362880 |
|   10 |             3628800 |
|   11 |            39916800 |
|   12 |           479001600 |
|   13 |          6227020800 |
|   14 |         87178291200 |
|   15 |       1307674368000 |
|   16 |      20922789888000 |
|   17 |     355687428096000 |
|   18 |    6402373705728000 |
|   19 |  121645100408832000 |
|   20 | 2432902008176640000 |
+------+---------------------+
```


## Derived Tables
* A subquery nested within a FROM clause, this result **must** be named.
  * Usually writing a CTE is cleaner.

```sql
SELECT 
  named_table.col1
  ,named_table.col2
FROM 
  (
  SELECT col1, col2
  FROM table1
  ) named_tabled
WHERE named_tabled.col1 > 0;
```

`JOINS` with derived tables

```sql
SELECT * FROM
(SELECT * from table1) t1 INNER JOIN
(SELECT * from table2) t2 ON
t1.id = t2.id
```

## Window Functions

* Act **after** the result of the query.
* Window functions operate on a set of rows and return a single value for *each* row from the underlying query.
* `PARTITION BY` col, and `ORDER BY` col are optional; i.e if no partiton, taken over whole table
  * `ORDER BY` is not optional for `ROW_NUMBER()`
* The `ORDER BY` clause defines the logical order of the rows within each partition of the result set.
* Function can be `AVG()`, `SUM()`, `ROW_NUMBER()`, etc.
* For ranking functions, make sure to get the correct ordering.
* Note: The `ROW_NUMBER()` function does not reset if it sees a different partition value that switches back (i.e XXYX)

```sql
FUNCTION() OVER([PARTITION BY col1,...] 
                [ORDER BY col1, ...[NULLS {FIRST|LAST}]
                [FRAME CLAUSE]) AS calc_col
```

Example:

```sql
--- 3 Day Moving Average
SELECT
  Ticker
  ,Date
  ,Price
  ,AVG(Price) OVER(ORDER BY Date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_average
FROM stock_prices;
```

### Frame Clauses

The FRAME CLAUSE is by default:
```sql
[ROWS|RANGE] BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
```
`RANGE` will include those rows in the window frame which have the same `ORDER BY` values as the current row.

The FRAME CLAUSE can be either:
* {RANGE|ROWS} *frame_start*
* {RANGE|ROWS} BETWEEN *frame_start* and *frame_end*

Where *frame_start* is:
* `UNBOUNDED PRECEDING` or `CURRENT ROW`  

And *frame_end* is:  
* `CURRENT ROW` or `UNBOUNDED FOLLOWING`

Example:
```sql
SELECT
AVG(price) OVER(ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) as avg_3D_price
...
```

### Lag and Lead

* `LAG` and `LEAD` gets the previous or following value of a particular column.  

The syntax is:
```sql
SELECT
LAG(col, offset, [default_if_no_value]) OVER()
...
```

Example:
```sql
SELECT
LAG(sale_value, 1) OVER(ORDER BY date_updated) AS previous_sale_price
FROM sales
```

### Ranking Functions

* `ROW_NUMBER()` attributes a unique value to each row.
* `RANK()` attributes the same row number to the same value, leaving "holes".
* `DENSE_RANK()` attributes the same row number to the same value, leaving no "holes".

Here is a good [explanation](https://stackoverflow.com/questions/11183572/whats-the-difference-between-rank-and-dense-rank-functions-in-oracle) of the differences.


```text
CREATE TABLE t AS
SELECT 'a' v FROM dual UNION ALL
SELECT 'a'   FROM dual UNION ALL
SELECT 'a'   FROM dual UNION ALL
SELECT 'b'   FROM dual UNION ALL
SELECT 'c'   FROM dual UNION ALL
SELECT 'c'   FROM dual UNION ALL
SELECT 'd'   FROM dual UNION ALL
SELECT 'e'   FROM dual;

SELECT
  v,
  ROW_NUMBER() OVER (ORDER BY v) row_number,
  RANK()       OVER (ORDER BY v) rank,
  DENSE_RANK() OVER (ORDER BY v) dense_rank
FROM t
ORDER BY v;

+---+------------+------+------------+
| V | ROW_NUMBER | RANK | DENSE_RANK |
+---+------------+------+------------+
| a |          1 |    1 |          1 |
| a |          2 |    1 |          1 |
| a |          3 |    1 |          1 |
| b |          4 |    4 |          2 |
| c |          5 |    5 |          3 |
| c |          6 |    5 |          3 |
| d |          7 |    7 |          4 |
| e |          8 |    8 |          5 |
+---+------------+------+------------+
```

## Built-In Functions

* concat is `||`

### Date Functions
`DATEDIFF(date1, date2)` calculate the number of days between (date1 - date2)

```sql
SELECT DATEDIFF("2017-06-25", "2017-06-15");
-- 10
```

## Other
* Division:  SQL automically does integer division if both the numerator and denominator are integers.  Explicit cast to floats is needed.  You can also force the numerator or denominator into a float by multiplying by `1.0` .
* Gotchas: For boolean expressions in where be very careful when using a combination of `OR` and `AND` statements. For example, `(expr1 OR expr2) AND expr3` is different than `expr1 OR expr2 AND expr3` . 


## References
* [Microsoft: Use HAVING and WHERE Clauses in the Same Query (Visual Database Tools)](https://docs.microsoft.com/en-us/sql/ssms/visual-db-tools/use-having-and-where-clauses-in-the-same-query-visual-database-tools?view=sql-server-ver15)
* [Red Gate Window Functions](https://www.red-gate.com/simple-talk/sql/learn-sql-server/window-functions-in-sql-server-part-2-the-frame/#:~:text=RANGE%20versus%20ROW&text=The%20ROWS%20clause%20limits%20the,value%20in%20the%20current%20row)
* [EssentialSQL: CTEs](https://www.essentialsql.com/introduction-common-table-expressions-ctes/)
* [Data36: Interview Questions](https://data36.com/sql-interview-questions-tech-screening-data-analysts/)
* [Stevestedman: Rows and Range](https://stevestedman.com/2013/04/rows-and-range-preceding-and-following/)
* [Toptal: Intro to Window Functions](https://www.toptal.com/sql/intro-to-sql-windows-functions)
* [Gdcoder: Practice DS SQL Interview Questions](https://gdcoder.com/how-to-nail-a-data-scientist-sql-interview-includes-sql-code/)
* [How MySQL Uses Indexes](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html)
* [StackOverFlow: On vs Where in Joins](https://stackoverflow.com/questions/354070/sql-join-where-clause-vs-on-clause)
* [Nick Singh: 30 SQL Interview Practice Questions](https://www.nicksingh.com/posts/30-sql-and-database-design-questions-from-real-data-science-interviews)
* [Github rbhatia46 DS Interview Resources](https://github.com/rbhatia46/Data-Science-Interview-Resources)
* [Medium: Churn rate with SQL](https://medium.com/@gozdeturan/churn-rate-with-sql-eef787ca4b2b)
* [Medium: SQL Questions (the histogram) for the aspiring Data Scientist](https://medium.com/analytics-and-data/sql-interview-questions-for-aspiring-data-scientist-the-histogram-76a46de9b0a3)
*[MySQL GGROUP_CONCAT](https://www.mysqltutorial.org/mysql-group_concat/)