# SQL Cheat Sheet
- **SELECT** - used to retrieve data from a database
```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

- **INSERT INTO** - used to insert new rows into a table
```sql
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);
```

- **UPDATE** - used to modify existing rows in a table
```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

- **DELETE FROM** - used to delete existing rows from a table
```sql
DELETE FROM table_name
WHERE condition;

```
- **CREATE TABLE** - used to create a new table
```sql
CREATE TABLE table_name (
    column1 datatype constraint,
    column2 datatype constraint,
    ...
);
```

- **ALTER TABLE** - used to modify an existing table
```sql
ALTER TABLE table_name
ADD column datatype constraint;

ALTER TABLE table_name
DROP COLUMN column;
```

- **TRUNCATE TABLE** - used to delete all data from a table
```sql
TRUNCATE TABLE table_name;
```

- **JOIN** - used to combine rows from multiple tables
```sql
SELECT column1, column2, ...
FROM table1
JOIN table2
ON table1.column = table2.column;
```

- **INNER JOIN** - returns rows that have a match in both tables. An inner join is the default type of join if no specific join type is specified.
```sql
SELECT column1, column2, ...
FROM table1
INNER JOIN table2
ON table1.column = table2.column;
```

- **LEFT JOIN** - returns all rows from the left table, and the matching rows from the right table. Unmatched rows from the right table are returned as NULL.
```sql
SELECT column1, column2, ...
FROM table1
LEFT JOIN table2
ON table1.column = table2.column;
```

- **RIGHT JOIN** - returns all rows from the right table, and the matching rows from the left table. Unmatched rows from the left table are returned as NULL.
```sql
SELECT column1, column2, ...
FROM table1
RIGHT JOIN table2
ON table1.column = table2.column;
```

- **FULL JOIN** - returns all rows from both tables, and returns NULL for any unmatched rows.
```sql
SELECT column1, column2, ...
FROM table1
FULL JOIN table2
ON table1.column = table2.column;
```

- **CROSSJOIN** - returns the Cartesian product of both tables. This means that every row in the first table is combined with every row in the second table.
```sql
SELECT column1, column2, ...
FROM table1
CROSS JOIN table2;
```

- **INTERSECTION** - returns only the rows that are common to both tables. This can be achieved using the INTERSECT operator.
```sql
SELECT column1, column2, ...
FROM table1
INTERSECT
SELECT column1, column2, ...
FROM table2;
```

- **EXCEPT** - returns the rows that are in the first table, but not in the second table. This can be achieved using the EXCEPT operator.
```sql
SELECT column1, column2, ...
FROM table1
EXCEPT
SELECT column1, column2, ...
FROM table2;
```

- **UNION** - used to combine the result sets of two or more SELECT statements
```sql
SELECT column1, column2, ...
FROM table1
UNION
SELECT column1, column2, ...
FROM table2;
```

- **GROUP BY** - used to group together rows that have the same values in one or more columns
```sql
SELECT column1, SUM(column2)
FROM table_name
GROUP BY column1;
```

- **HAVING** - used to filter the results of a GROUP BY clause
```sql
SELECT column1, SUM(column2)
FROM table_name
GROUP BY column1
HAVING SUM(column2) > value;
```

- **LIMIT** - used to limit the number of rows returned in a SELECT statement
```sql
SELECT column1, column2, ...
FROM table_name
LIMIT number;
```

- **ORDER BY** - used to sort the result set of a SELECT statement
```sql
SELECT column1, column2, ...
FROM table_name
ORDER BY column1 ASC/DESC;
```

- **BETWEEN** - used to select values within a specific range
```sql
SELECT column1, column2, ...
FROM table_name
WHERE column1 BETWEEN value1 AND value2;
```

- **LIKE** - used to search for a specific pattern in a column
```sql
SELECT column1, column2, ...
FROM table_name
WHERE column1 LIKE pattern;
```

- **IN** - used to specify a list of values to compare against
```sql
SELECT column1, column2, ...
FROM table_name
WHERE column1 IN (value1, value2, ...);
```

- **IS NULL** - used to test for NULL values
```sql
SELECT column1, column2, ...
FROM table_name
WHERE column1 IS NULL;
```

- **EXISTS** - used to test for the existence of rows in a subquery
```sql
SELECT column1, column2, ...
FROM table_name
WHERE EXISTS (SELECT * FROM table_name WHERE condition);
```


- **COUNT** - used to return the number of rows in a SELECT statement
```sql
SELECT COUNT(*)
FROM table_name;
```


- **SUM** - used to return the sum of the values in a specific column
```sql
SELECT SUM(column)
FROM table_name;
```

- **AVG** - used to return the average value of a specific column
```sql
SELECT AVG(column)
FROM table_name;
```

- **MIN** - used to return the minimum value in a specific column
```sql
SELECT MIN(column)
FROM table_name;
```

- **MAX** - used to return the maximum value in a specific column
```sql
SELECT MAX(column)
FROM table_name;
```

- **DISTINCT** - used to return unique values in the output

```sql
SELECT DISTINCT column1, column2, ...
FROM table_name;
```

- **AS** - used to specify an alias for a column or table
```sql
SELECT column1 AS 'alias'
FROM table_name;

SELECT t1.column1, t2.column2
FROM table1 AS t1
JOIN table2 AS t2
ON t1.column = t2.column;
```


- **CASE** - used to create conditional statements within a SELECT statement
```sql
SELECT column1,
    (CASE
        WHEN column2 > value THEN 'A'
        WHEN column2 = value THEN 'B'
        ELSE 'C'
    END) AS 'alias'
FROM table_name;
```

- **COALESCE** - used to return the first non-NULL value in a list of values
```sql
SELECT COALESCE(column1, column2, ...)
FROM table_name;
```

- **NULLIF** - used to return NULL if two expressions are equal
```sql
SELECT NULLIF(expression1, expression2)
FROM table_name;
```

- **IFNULL** - used to return a specified value if an expression is NULL
```sql
SELECT IFNULL(expression, value)
FROM table_name;

```
- **DATE_ADD** - used to add a specified time interval to a date
```sql
SELECT DATE_ADD(date, INTERVAL value unit)
FROM table_name;
```

- **DATE_SUB** - used to subtract a specified time interval from a date
```sql
SELECT DATE_SUB(date, INTERVAL value unit)
FROM table_name;
```

- **DATE_FORMAT** - used to format a date in a specific way
```sql
SELECT DATE_FORMAT(date, 'format')
FROM table_name;
```

- **CONCAT** - used to concatenate two or more strings
```sql
SELECT CONCAT(string1, string2, ...)
FROM table_name;
```
