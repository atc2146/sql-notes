# SQL Notes

* SQL syntax is different, but ANSI SQL is standard
* Not case sensitive
* Use a semi-colon at the end of each statement

## Schemas

### Star Schema
* Not normalized
* Good for simple relationships

### Snowflake Schema
* Normalized, no redundant information
* A fact table contains business facts, and foreign keys which refer to candidate keys in the dimension tables.  Dimension tables contain descriptive fields that are typically textual fields.

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

## Selecting

A select statement that selects the columns "col1" and "col2"
from the table "table_name".

```sql
SELECT col1, col2
FROM table_name;
```

The statement below will only return distinct values from the "col1".   columns.
```sql
SELECT DISTINCT col1 
FROM table_name;
```
If multiple columns are used, the query uses a combination of all values. `NULL` counts as one distinct value only.

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
* is used to filter records
* operators that can be used in WHERE clause: `<>`, `BETWEEN`, `LIKE`
* can also do `WHERE NOT` <condition>

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
* `LIKE`, `ALL`, `ANY`
* `>`, `>=`, `<`, `<=`, `=`

Note: the equality operator is one *single* equal sign.


### LIKE operator
* `%`: The percent sign represents zero, one, or multiple characters
* `_`: The underscore represents a single character

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

### IN operator
```sql
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1, value2, ...);
```

When checking for `NULL` values, you must use the `IS` keyword, not `=`.

### NULL values
* check using `IS NULL` or `IS NOT NULL`
* `COALESCE()` takes the first non-null value
* ANSI_NULLS operate differently, it is three-valued logic
* Generally, aggregate functions like `AVG()` dissmisses rows with `NULL` values

```sql
SELECT col1
FROM table_name
WHERE col1 IS NULL;
```

## Ordering

Put the `ORDER BY` statement at the end of your SQL query.
* Two options: `ASC`, `DESC`
  * Sorted by ascending order by default
* Can order by multiple columns

```sql
SELECT column1, column2, ...
FROM table_name
ORDER BY column1, column2, ... [ASC|DESC];
```

## Creating Tables

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

## Inserting Values

```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

## Updating Values

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

## Aggregate Functions

### MIN() and MAX() functions
* `MIN()` returns the lowest value of the selected column
* Should use aliases

```sql
SELECT MAX(Price) AS LargestPrice
FROM Products;
```

### COUNT(), SUM(), and AVG() functions

```sql
SELECT COUNT(column_name)
FROM table_name
WHERE condition;
```


## JOINS

SQl joins include:
* `INNER JOIN`:
* `LEFT (OUTER) JOIN`:


```sql
SELECT column_name(s)
FROM table1
INNER JOIN table2
ON table1.column_name = table2.column_name;
```

** UNION
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
- UNION ALL does not remove any rows
- UNION removes duplicates

** GROUP BY
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
ORDER BY column_name(s);

** HAVING
- The HAVING clause was added to SQL because the WHERE keyword could not be used with aggregate functions.
- When GROUP BY and WHERE is in the same query, WHERE clause is used to filter records from a result.  The filtering
occurs before any groupings are made.  The HAVING clause is used to filter values from a group. 

SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
HAVING aggregate_condition
ORDER BY column_name(s);

** CASE
The CASE statement goes through conditions and returns a value when the first condition is met (like an IF-THEN-ELSE statement).
So, once a condition is true, it will stop reading and return the result. If no conditions are true, it returns the value in the ELSE clause.
If there is no ELSE part and no conditions are true, it returns NULL.

CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    WHEN conditionN THEN resultN
    ELSE result
END;

CASE WHEN THEN ELSE END
CAN ALSO
SUM(CASE WHEN condition THEN 1 ELSE 0 END)

** OTHER
concat is ||
WHERE aggregates before HAVING


** COMMON TABLE EXPRESSIONS (WITH CLAUSES)
- Why? an alternative to creating a view
- Increased readability and simplification
- Named columns must match selected columns in CTE definition

WITH CTE_expression_name(named_col_1, ...)
AS
    (CTE_definition)
SQL_statement using above CTE;

-can write multiple CTEs in one query
WITH   PersonCTE (BusinessEntityID, FirstName, LastName)
AS     (SELECT Person.BusinessEntityID,
               FirstName,
               LastName
        FROM   Person.Person
        WHERE  LastName LIKE 'C%'),
PhoneCTE (BusinessEntityID, PhoneNumber)
AS     (SELECT BusinessEntityID,
               PhoneNumber
        FROM   Person.PersonPhone)
SELECT FirstName,
       LastName,
       PhoneNumber
FROM   PersonCTE
INNER JOIN
PhoneCTE
ON PersonCTE.BusinessEntityID = PhoneCTE.BusinessEntityID;


- Can be used for recursive statements
WITH RECURSIVE factorial(n, fact) AS ( 
          SELECT 0, 1 
          UNION ALL  
          SELECT n + 1, fact * (n+1)  
          FROM factorial 
          WHERE n < 20 ) 
SELECT * from factorial;
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
GROUP BY 1

** DERIVED TABLES
- A subquery nested within a FROM clause, this result must be named
SELECT col1, col2
FROM (
	SELECT col, col2
	FROM table1
	) named_tabled
WHERE named_tabled.col1 > 0;

- JOINS with derived tables
SELECT * FROM
(SELECT * from table1) t1 INNER JOIN
(SELECT * from table2) t2 ON
t1.id = t2.id


** WINDOW FUNCTIONS
- Act after the result of the query
- Window functions operate on a set of rows and return a single value for each row from the underlying query.
- PARTITION BY col, and ORDER BY col are optional; i.e if no partiton, taken over whole table
- The ORDER BY clause defines the logical order of the rows within each partition of the result set.
- Function can be AVG(), SUM(), ROW_NUMBER(), etc.
- For ranking functions, make sure to get the correct ordering.
- Note: The ROW_NUMBER() function does not reset if it sees a different partition value that switches back (i.e XXYX)

FUNCTION() OVER([PARTITION BY col1,...] [ORDER BY col1, ...[NULLS{FIRST|LAST}]][FRAME CLAUSE]) AS calc_col

- The FRAME CLAUSE is by default [ROWS|RANGE] BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
- RANGE will include those rows in the window frame which have the same ORDER BY values as the current row.
- The FRAME CLAUSE can be either {RANGE|ROWS} frame_start or {RANGE|ROWS} BETWEEN frame_start and frame_end
where frame_start is UNBOUNDED PRECEDING or CURRENT ROW and frame_end is CURRENT ROW or UNBOUNDED FOLLOWING
- Example: AVERAGE(price) OVER(ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) as avg_3D_price


- LAG and LEAD gets the previous or following value of a particular column.
- The syntax is LAG(col, offset, [default_if_no_value]) OVER()
- DENSE_RANK()  does not skip any ranks if there is a tie between the ranks of the preceding records.
- RANK() may skip ranks.



## References

https://www.red-gate.com/simple-talk/sql/learn-sql-server/window-functions-in-sql-server-part-2-the-frame/#:~:text=RANGE%20versus%20ROW&text=The%20ROWS%20clause%20limits%20the,value%20in%20the%20current%20row.
https://www.essentialsql.com/introduction-common-table-expressions-ctes/
https://data36.com/sql-interview-questions-tech-screening-data-analysts/
https://stevestedman.com/2013/04/rows-and-range-preceding-and-following/