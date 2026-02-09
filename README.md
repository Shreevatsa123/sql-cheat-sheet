# Comprehensive SQL Cheatsheet: Beginner to Advanced

## Table of Contents

- [I. Basic Queries & Clauses](#i-basic-queries--clauses)
- [II. Filtering Operators (Used with `WHERE` and `HAVING`)](#ii-filtering-operators-used-with-where-and-having)
- [III. Sorting & Limiting Results](#iii-sorting--limiting-results)
- [IV. Aggregate Functions (Usually used with `GROUP BY`)](#iv-aggregate-functions-usually-used-with-group-by)
- [V. Grouping Data](#v-grouping-data)
- [VI. Joins (Combining Data from Multiple Tables)](#vi-joins-combining-data-from-multiple-tables)
- [VII. Set Operations (Combining Result Sets)](#vii-set-operations-combining-result-sets)
- [VIII. Subqueries (Nested Queries)](#viii-subqueries-nested-queries)
- [IX. Common Table Expressions (CTEs)](#ix-common-table-expressions-ctes)
- [X. Window Functions](#x-window-functions)
- [XI. String Functions (Syntax/availability varies)](#xi-string-functions-syntaxavailability-varies)
- [XII. Numeric Functions](#xii-numeric-functions)
- [XIII. Date & Time Functions (Highly dialect-specific)](#xiii-date--time-functions-highly-dialect-specific)
- [XIV. Conditional Logic](#xiv-conditional-logic)
- [XV. Data Definition Language (DDL) - Defines/Modifies Database Structure](#xv-data-definition-language-ddl--definesmodifies-database-structure)
- [XVI. Constraints (Enforce Data Integrity)](#xvi-constraints-enforce-data-integrity)
- [XVII. Data Manipulation Language (DML) - Manipulates Data](#xvii-data-manipulation-language-dml--manipulates-data)
- [XVIII. Transaction Control Language (TCL) - Manages Transactions](#xviii-transaction-control-language-tcl--manages-transactions)
- [XIX. Views](#xix-views)
- [XX. Indexes](#xx-indexes)
- [XXI. Stored Procedures & Functions (Syntax varies greatly)](#xxi-stored-procedures--functions-syntax-varies-greatly)
- [XXII. Data Control Language (DCL) - Manages Permissions](#xxii-data-control-language-dcl--manages-permissions)
- [XXIII. Advanced & Miscellaneous](#xxiii-advanced--miscellaneous)
- [XXIV. Logical Query Processing Order (How a `SELECT` Actually Runs)](#xxiv-logical-query-processing-order-how-a-select-actually-runs)
- [XXV. Common Schema Types (OLTP vs. OLAP / Warehouse Schemas)](#xxv-common-schema-types-oltp-vs-olap--warehouse-schemas)
  - [1. Normalized OLTP Schema (3NF-ish)](#1-normalized-oltp-schema-3nf-ish)
  - [2. Star Schema (Data Warehouse)](#2-star-schema-data-warehouse)
  - [3. Snowflake Schema](#3-snowflake-schema)
  - [4. Constellation / Galaxy Schema](#4-constellation--galaxy-schema)
- [XXVI. Slowly Changing Dimensions (SCD) – Types & Quick Examples](#xxvi-slowly-changing-dimensions-scd--types--quick-examples)
  - [SCD Type 0 – Retain Original (No Changes)](#scd-type-0--retain-original-no-changes)
  - [SCD Type 1 – Overwrite (No History)](#scd-type-1--overwrite-no-history)
  - [SCD Type 2 – Add Row (Full History)](#scd-type-2--add-row-full-history)
  - [SCD Type 3 – Limited History (Current + Previous)](#scd-type-3--limited-history-current--previous)
  - [SCD Type 4 – History Table (Separate)](#scd-type-4--history-table-separate)
  - [SCD Type 6 – Hybrid (1 + 2 + 3)](#scd-type-6--hybrid-1--2--3)
- [XXIV. Normalization (Database Design)](#xxiv-normalization-database-design)
  - [A. Anomalies (What Normalization Tries to Avoid)](#a-anomalies-what-normalization-tries-to-avoid)
  - [B. First Normal Form (1NF)](#b-first-normal-form-1nf)
  - [C. Second Normal Form (2NF)](#c-second-normal-form-2nf)
  - [D. Third Normal Form (3NF)](#d-third-normal-form-3nf)
  - [E. Boyce–Codd Normal Form (BCNF)](#e-boycecodd-normal-form-bcnf)
  - [F. Normalize vs. Denormalize](#f-normalize-vs-denormalize)
- [XXVII. ACID Properties (Transaction Management)](#xxvii-acid-properties-transaction-management)
  - [A. Atomicity (All or Nothing)](#a-atomicity-all-or-nothing)
  - [B. Consistency (Valid State to Valid State)](#b-consistency-valid-state-to-valid-state)
  - [C. Isolation (Concurrent Transactions Don't Interfere)](#c-isolation-concurrent-transactions-dont-interfere)
  - [D. Durability (Committed Changes Persist)](#d-durability-committed-changes-persist)
  - [E. Savepoints (Partial Rollback)](#e-savepoints-partial-rollback)
  - [F. Deadlock Handling](#f-deadlock-handling)


**General Notes:**

* **Case Sensitivity:** SQL keywords (e.g., `SELECT`, `FROM`) are generally case-insensitive. Table and column names might be case-sensitive depending on the specific database system and configuration.
* **Semicolons (;):** Standard SQL statement terminator. Often required, especially when executing multiple statements.
* **Comments:**
    * Single-line: `-- This is a comment`
    * Multi-line: `/* This is a multi-line comment */`
* **Dialects:** Syntax can vary slightly between database systems (e.g., PostgreSQL, MySQL, SQL Server, Oracle, SQLite). This guide aims for general concepts and common syntax, noting variations where significant.

---

## I. Basic Queries & Clauses

* `SELECT <column1, column2, ... | *>`
    * Specifies the columns to retrieve data from. `*` selects all columns.
* `FROM <table_name>`
    * Specifies the table(s) to query.
* `WHERE <condition>`
    * Filters rows based on specified conditions. Executed *before* grouping.
    
    * WHERE 1=1

        - Always true condition in `WHERE` clause.
        - Used for **dynamic query building** to append multiple conditions easily.
        - Helps in **debugging/toggling** conditions safely.
        - Ensures **template/ORM consistency** when generating queries.
        - Rarely used to create always-true join conditions (cross join effect).

* `AS <alias>`
    * Assigns a temporary, more readable name to a column or table for the duration of the query.
    * `SELECT c.name AS customer_name FROM customers AS c;`
* `DISTINCT <column1, ...>`
    * Returns only unique combinations of values for the specified columns.
    * `SELECT DISTINCT country FROM customers;`

---

## II. Filtering Operators (Used with `WHERE` and `HAVING`)

* `=`, `<>`, `!=`, `>`, `<`, `>=`, `<=`
    * Standard comparison operators: Equal, Not equal, Greater than, Less than, Greater than or equal to, Less than or equal to.
* `BETWEEN <value1> AND <value2>`
    * Checks if a value falls within a specified range (inclusive).
* `LIKE <pattern>`
    * Searches for a pattern in string data. Case sensitivity depends on collation settings.
    * `%`: Wildcard for zero or more characters (e.g., `'S%'` finds strings starting with S).
    * `_`: Wildcard for exactly one character (e.g., `'T_m'` finds Tim, Tom, Tam).
    * `[]` with REGEXP: Matches any single character within the brackets (e.g., WHERE name REGEXP `'^[TC]om'` finds Tom and Com)..
* `IN (<value1, value2, ...>)`
    * Checks if a value matches any value in a provided list.
* `IS NULL` / `IS NOT NULL`
    * Checks if a column's value is NULL or not NULL. Cannot use `= NULL`.
* `AND`, `OR`, `NOT`
    * Logical operators to combine multiple conditions. `AND` requires all conditions to be true, `OR` requires at least one, `NOT` negates a condition.

---

## III. Sorting & Limiting Results

* `ORDER BY <column1> [ASC|DESC], <column2> [ASC|DESC], ...`
    * Sorts the result set based on one or more columns. `ASC` (Ascending) is default, `DESC` is Descending.
* `LIMIT <number>` (MySQL/PostgreSQL/SQLite)
    * Restricts the number of rows returned.
* `OFFSET <number>` (MySQL/PostgreSQL/SQLite)
    * Skips a specified number of rows before starting to return rows (often used with `LIMIT` for pagination).
    * `LIMIT 10 OFFSET 20` (Returns rows 21-30).
      
```sql 
-- Get top 2
SELECT * FROM Scores ORDER BY score DESC LIMIT 2;

-- Get rows 21-30 (Pagination)
SELECT * FROM Scores ORDER BY score DESC LIMIT 10 OFFSET 20;
```

---

## IV. Aggregate Functions (Usually used with `GROUP BY`)

* `COUNT(<column | *>)`
    * Counts rows. `COUNT(*)` counts all rows; `COUNT(column)` counts non-NULL values in that column; `COUNT(DISTINCT column)` counts unique non-NULL values.
* `SUM(<numeric_column>)`
    * Calculates the sum of values in a numeric column. Ignores NULLs.
* `AVG(<numeric_column>)`
    * Calculates the average of values in a numeric column. Ignores NULLs.
* `MIN(<column>)`
    * Finds the minimum value in a column.
* `MAX(<column>)`
    * Finds the maximum value in a column.
* `GROUP_CONCAT(<column> [SEPARATOR 'sep'])` (MySQL) 
    * Concatenates string values from different rows within a group.
 
#### SQL: Concatenate Strings Across Rows

Use this to combine values from multiple rows into a single string per group.

##### Example Table: `Fruits`
| category | name   |
|----------|--------|
| Citrus   | Orange |
| Citrus   | Lemon  |
| Berry    | Strawberry |
| Berry    | Blueberry |

##### MySQL 
```sql
SELECT category, GROUP_CONCAT(name ORDER BY name SEPARATOR ', ') AS all_fruits
FROM Fruits
GROUP BY category;
````

##### Notes

* Concatenates all row values **within each group**.
* `ORDER BY` inside `LISTAGG` controls the order of concatenated strings.
* Separator is optional; default is usually a comma.

---

## V. Grouping Data

* `GROUP BY <column1, column2, ...>`
    * Groups rows with the same values in specified columns into a single summary row. Columns in `SELECT` must be either aggregated or included in `GROUP BY`.
* `HAVING <condition>`
    * Filters groups *after* aggregation (based on aggregate results). Similar to `WHERE` but operates on grouped data.

---

## VI. Joins (Combining Data from Multiple Tables)

* `INNER JOIN <table2> ON <join_condition>`
    * Returns only rows where the join condition is met in *both* tables.
* `LEFT JOIN <table2> ON <join_condition>` (or `LEFT OUTER JOIN`)
    * Returns *all* rows from the left table (table1), and matching rows from the right table (table2). If no match, columns from the right table have NULL values.
* `RIGHT JOIN <table2> ON <join_condition>` (or `RIGHT OUTER JOIN`)
    * Returns *all* rows from the right table (table2), and matching rows from the left table (table1). If no match, columns from the left table have NULL values.
* `FULL OUTER JOIN <table2> ON <join_condition>`
    * Returns all rows when there is a match in *either* the left or right table. Combines `LEFT` and `RIGHT` join results. (Not supported directly in MySQL - emulate with `UNION`).
* `CROSS JOIN <table2>`
    * Returns the Cartesian product: every row from the first table combined with every row from the second table. No `ON` clause needed (or use `ON 1=1`).
* `SELF JOIN`
    * Joining a table to itself. Requires using table aliases to distinguish the "copies". Used for comparing rows within the same table (e.g., finding employees and their managers).
    * `SELECT e.name AS Employee, m.name AS Manager FROM employees e LEFT JOIN employees m ON e.manager_id = m.employee_id;`
* `NATURAL JOIN <table2>`
    * Joins tables based on *all* columns with the same name in both tables. Often discouraged due to potential ambiguity if column names change.
* `JOIN <table2> USING (<column1, column2, ...>)`
    * A shorthand for `ON` when the join columns have the same name in both tables.
    * `SELECT * FROM orders JOIN customers USING (customer_id);`

---

## VII. Set Operations (Combining Result Sets)

* `UNION`
    * Combines results of two or more `SELECT` statements. Returns *distinct* rows. Columns must match in number, order, and have compatible data types.
* `UNION ALL`
    * Combines results like `UNION`, but includes *all* rows (duplicates are kept). Generally faster.
* `INTERSECT`
    * Returns only rows that appear in the results of *both* `SELECT` statements. Returns distinct rows. (Not supported directly in MySQL - emulate with `JOIN` or `IN`).
* `EXCEPT` (Standard SQL/PostgreSQL/SQL Server) / `MINUS` (Oracle)
    * Returns distinct rows from the first `SELECT` statement that are *not* found in the second `SELECT` statement. (Not supported directly in MySQL - emulate with `LEFT JOIN...IS NULL` or `NOT IN`).

---

## VIII. Subqueries (Nested Queries)

* **Scalar Subquery:** Returns a single value. Can be used where a single value expression is expected.
    * `SELECT name FROM products WHERE price > (SELECT AVG(price) FROM products);`
* **Multi-row Subquery:** Returns a column of multiple rows. Often used with `IN`, `ANY`, `ALL`, `EXISTS`.
    * `SELECT * FROM customers WHERE customer_id IN (SELECT customer_id FROM orders WHERE order_date > '2025-01-01');`
* **Correlated Subquery:** References columns from the outer query. Evaluated once per row of the outer query. Can impact performance.
    * `SELECT p1.name FROM products p1 WHERE p1.price = (SELECT MAX(p2.price) FROM products p2 WHERE p2.category_id = p1.category_id);`
* **Subquery in `FROM` (Derived Table):** The result set of the subquery acts as a temporary table. Must be aliased.
    * `SELECT MAX(total_sales) FROM (SELECT SUM(amount) AS total_sales FROM orders GROUP BY customer_id) AS sales_summary;`
* `EXISTS (<subquery>)`
    * Checks if the subquery returns *any* rows. Returns TRUE or FALSE. Often more efficient than `IN` for checking existence.
* `ANY` / `SOME` (Synonyms)
    * Compares a value to *each* value returned by a multi-row subquery. Returns TRUE if the comparison is TRUE for *at least one* value. (e.g., `= ANY(...)` is like `IN(...)`).
    * `WHERE price > ANY (SELECT price FROM competitor_products);`
* `ALL`
    * Compares a value to *each* value returned by a multi-row subquery. Returns TRUE if the comparison is TRUE for *all* values.
    * `WHERE price > ALL (SELECT price FROM competitor_products);`

---

## IX. Common Table Expressions (CTEs)

* `WITH <cte_name> [(<column_aliases>)] AS ( <subquery_definition> ) SELECT ... FROM <cte_name>;`
    * Defines a temporary, named result set referenced within a single statement (SELECT, INSERT, UPDATE, DELETE, MERGE). Improves readability and organization of complex queries. Can define multiple CTEs separated by commas.
* **Recursive CTE:** A CTE that references itself. Used for hierarchical data (org charts, graphs, bill of materials). Requires a base member (anchor) and a recursive member, combined with `UNION ALL`.
    * `WITH RECURSIVE EmployeeHierarchy AS ( SELECT employee_id, name, manager_id, 1 AS level FROM employees WHERE manager_id IS NULL UNION ALL SELECT e.employee_id, e.name, e.manager_id, eh.level + 1 FROM employees e JOIN EmployeeHierarchy eh ON e.manager_id = eh.employee_id ) SELECT * FROM EmployeeHierarchy;` (Syntax like `RECURSIVE` may vary).

---

## X. Window Functions

* **Syntax:** `<function>() OVER ( [PARTITION BY <...>] [ORDER BY <...>] [ROWS/RANGE frame_clause] )`
    * Perform calculations across a set of table rows (a "window") related to the current row. Unlike aggregate functions with `GROUP BY`, they do not collapse rows.
* `PARTITION BY <col1, ...>`: Divides rows into partitions (groups). Function applied independently to each partition. Optional.
* `ORDER BY <col1, ...>`: Orders rows within each partition. Required or meaningful for ranking, offset, and some aggregate window functions. Optional for others.
* `ROWS/RANGE frame_clause`: Defines the window frame (subset of rows within the partition) relative to the current row (e.g., `ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING`, `RANGE UNBOUNDED PRECEDING`).
* **Ranking Functions:**
    * `ROW_NUMBER()`: Assigns a unique sequential integer to each row within its partition based on `ORDER BY`.
    * `RANK()`: Assigns rank based on `ORDER BY`. Ties receive the same rank, subsequent ranks are skipped (e.g., 1, 2, 2, 4).
    * `DENSE_RANK()`: Assigns rank like `RANK()`, but without gaps for ties (e.g., 1, 2, 2, 3).
    * `NTILE(n)`: Divides rows within a partition into `n` ranked groups (buckets).
* **Offset (Value) Functions:**
    * `LAG(<column>, [offset=1], [default=NULL])`: Accesses data from a *previous* row within the partition based on `ORDER BY`.
    * `LEAD(<column>, [offset=1], [default=NULL])`: Accesses data from a *subsequent* row within the partition based on `ORDER BY`.
    * `FIRST_VALUE(<column>)`: Returns the value from the *first* row in the window frame.
    * `LAST_VALUE(<column>)`: Returns the value from the *last* row in the window frame. (Often needs careful frame definition like `ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING`).
* **Aggregate Functions as Window Functions:** `SUM()`, `AVG()`, `COUNT()`, `MIN()`, `MAX()` used with `OVER()`.
    * `SELECT order_id, amount, SUM(amount) OVER (PARTITION BY customer_id ORDER BY order_date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total FROM orders;`

---

## XI. String Functions (Syntax/availability varies)

* `CONCAT(str1, str2, ...)` / `str1 + str2` / `str1 || str2`: Concatenates strings.
* `LENGTH(str)` / `LEN(str)` / `DATALENGTH(str)`: Returns the length (bytes or characters depending on function/DB).
* `SUBSTRING(str, start_pos, length)` / `SUBSTR(...)`: Extracts a substring.
* `LEFT(str, num_chars)` / `RIGHT(str, num_chars)`: Extracts characters from the left/right.
* `UPPER(str)` / `UCASE(str)` / `LOWER(str)` / `LCASE(str)`: Converts case.
* `REPLACE(str, substring_to_replace, replacement_substring)`: Replaces occurrences of a substring.
* `TRIM([ [LEADING|TRAILING|BOTH] [chars_to_trim] FROM ] str)`: Removes leading/trailing/both spaces or specified characters.
* `LTRIM(str, [chars])` / `RTRIM(str, [chars])`: Removes leading/trailing spaces or specified characters.
* `POSITION(substring IN string)` / `CHARINDEX(substring, string, [start])` / `INSTR(string, substring, [start])`: Finds the starting position of a substring.
* `LPAD(str, length, pad_str)` / `RPAD(str, length, pad_str)`: Pads a string to a certain length with another string (left/right).
* `FORMAT(value, format_code)`: Formats numbers, dates, etc., as strings (Syntax varies greatly).
* `STUFF(str, start, length, insert_str)` (SQL Server): Deletes part of a string and inserts another part.
* `REVERSE(str)`: Reverses a string.
* `SOUNDEX(str)` / `DIFFERENCE(str1, str2)`: Phonetic matching functions.

---

## XII. Numeric Functions

* `ABS(num)`: Absolute value.
* `ROUND(num, [decimals])`: Rounds to specified decimal places (or integer if decimals=0).
* `CEILING(num)` / `CEIL(num)`: Smallest integer >= number.
* `FLOOR(num)`: Largest integer <= number.
* `POWER(base, exponent)` / `POW(...)`: Raises base to the power of exponent.
* `SQRT(num)`: Square root.
* `MOD(dividend, divisor)` / `dividend % divisor`: Modulo (remainder of division).
* `SIGN(num)`: Returns -1 (negative), 0 (zero), or 1 (positive).
* `EXP(num)`: Exponential function (e^num).
* `LOG(num)` / `LN(num)` / `LOG10(num)`: Logarithms (natural or base 10).
* `PI()`: Returns the value of Pi.
* `RAND()` / `RANDOM()`: Returns a random floating-point value (range varies).

---

## XIII. Date & Time Functions (Highly dialect-specific)

* **Current Date/Time:** `CURRENT_TIMESTAMP`, `NOW()`, `GETDATE()`, `SYSDATE`, etc.
* **Current Date Only:** `CURRENT_DATE`, `GETUTCDATE()` (SQL Server for UTC), `CURDATE()` (MySQL).
* **Current Time Only:** `CURRENT_TIME`.
* **Extract Parts:** `EXTRACT(part FROM datetime)`, `DATEPART(part, datetime)`, `YEAR()`, `MONTH()`, `DAY()`, `HOUR()`, `MINUTE()`, `SECOND()`.
    * `part`: YEAR, MONTH, DAY, HOUR, MINUTE, SECOND, QUARTER, WEEK, DAYOFWEEK, DOY, etc.
* **Add/Subtract Intervals:** `DATEADD(part, num, datetime)` (SQL Server), `datetime + INTERVAL 'num part'` (PostgreSQL/MySQL), `datetime + num` (Oracle days).
* **Calculate Difference:** `DATEDIFF(part, start_datetime, end_datetime)` (SQL Server/MySQL), `end_datetime - start_datetime` (PostgreSQL/Oracle - returns interval/days).
* **Format:** `FORMAT(datetime, format_str)`, `TO_CHAR(datetime, format_str)`, `DATE_FORMAT(datetime, format_str)`, `STRFTIME(format_str, datetime)`.
* **Construct Date/Time:** `MAKEDATE()`, `MAKETIME()` (MySQL), `DATEFROMPARTS()`, `TIMEFROMPARTS()`, `DATETIMEFROMPARTS()` (SQL Server).

---

## XIV. Conditional Logic

* `CASE WHEN condition1 THEN result1 [WHEN condition2 THEN result2 ...] [ELSE else_result] END`
    * Standard SQL conditional expression. Evaluates conditions sequentially.
* `CASE <expression> WHEN value1 THEN result1 [WHEN value2 THEN result2 ...] [ELSE else_result] END`
    * Simplified CASE expression comparing an expression against multiple values.
* `COALESCE(value1, value2, ...)`
    * Returns the first non-NULL expression in the list. Useful for providing default values.
* `NULLIF(expression1, expression2)`
    * Returns NULL if the two expressions are equal, otherwise returns the first expression. Useful for avoiding division-by-zero errors (`/ NULLIF(divisor, 0)`).
* `IF(condition, value_if_true, value_if_false)` (MySQL) / `IIF(condition, value_if_true, value_if_false)` (SQL Server >= 2012 / Access)
    * Simpler function for binary conditional logic.
* `DECODE(expression, search1, result1, [search2, result2, ...], [default])` (Oracle)
    * Oracle-specific function similar to the simplified CASE expression.

---

## XV. Data Definition Language (DDL) - Defines/Modifies Database Structure

* `CREATE DATABASE <db_name>`
* `ALTER DATABASE <db_name> <options>` (e.g., modify properties)
* `DROP DATABASE <db_name>` (Use with extreme caution!)
* `CREATE TABLE <table_name> ( <col1> <type> [constraints], <col2> <type> [constraints], ... );`
* `ALTER TABLE <table_name> ADD <column_definition | table_constraint>`
* `ALTER TABLE <table_name> DROP [COLUMN] <column_name | CONSTRAINT <constraint_name>`
* `ALTER TABLE <table_name> ALTER COLUMN <column_name> <new_type | SET DEFAULT | DROP DEFAULT | ADD/DROP NOT NULL>` (Syntax varies: `MODIFY COLUMN` in MySQL/Oracle)
* `ALTER TABLE <table_name> RENAME COLUMN <old_name> TO <new_name>` (Not universal, e.g., `sp_rename` in SQL Server)
* `ALTER TABLE <table_name> RENAME TO <new_table_name>` (Syntax varies: `RENAME TABLE` in MySQL)
* `DROP TABLE <table_name>` (Deletes table structure and data)
* `TRUNCATE TABLE <table_name>` (Deletes all data quickly, usually cannot be rolled back, may not fire triggers)
* `CREATE SCHEMA <schema_name>` / `DROP SCHEMA <schema_name>` (Namespace for tables/views etc.)

---

## XVI. Constraints (Enforce Data Integrity)

* `NOT NULL`: Column cannot store NULL values.
* `UNIQUE [(col1, ...)]`: Ensures values in a column or set of columns are unique across rows (allows NULLs, usually one per unique constraint).
* `PRIMARY KEY [(col1, ...)]`: Uniquely identifies each row. Implicitly `NOT NULL` and `UNIQUE`. One per table.
* `FOREIGN KEY [(col)] REFERENCES <parent_table>[(parent_col)] [ON DELETE action] [ON UPDATE action]`
    * Links rows to a primary/unique key in another table. `action`: `CASCADE`, `SET NULL`, `SET DEFAULT`, `RESTRICT`, `NO ACTION`.
* `CHECK (<condition>)`: Ensures values meet a specific logical condition.
* `DEFAULT <value>`: Provides a default value if none is specified during `INSERT`.

---

## XVII. Data Manipulation Language (DML) - Manipulates Data

* `INSERT INTO <table_name> [(col1, ...)] VALUES (val1, ...), (val2, ...);` (Inserts one or more rows)
* `INSERT INTO <table_name> [(col1, ...)] SELECT <col_a, ...> FROM <source_table> WHERE ...;` (Inserts results of a query)
* `UPDATE <table_name> SET <col1 = val1, col2 = val2, ...> WHERE <condition>;` (Modifies existing rows. **Use `WHERE` carefully!**)
* `DELETE FROM <table_name> WHERE <condition>;` (Removes existing rows. **Use `WHERE` carefully!**)
* `MERGE INTO <target> USING <source> ON <join_condition> WHEN MATCHED THEN UPDATE SET ... [DELETE] WHEN NOT MATCHED [BY TARGET] THEN INSERT ... WHEN NOT MATCHED BY SOURCE THEN UPDATE/DELETE ...;` (Standard SQL - performs INSERT/UPDATE/DELETE in one operation. Syntax/support varies. Often used for UPSERT).
    * MySQL: `INSERT ... ON DUPLICATE KEY UPDATE ...`
    * PostgreSQL: `INSERT ... ON CONFLICT ... DO UPDATE/NOTHING`
    * SQLite: `INSERT ... ON CONFLICT ... DO UPDATE/NOTHING`

---

## XVIII. Transaction Control Language (TCL) - Manages Transactions

* `BEGIN TRANSACTION` / `START TRANSACTION`: Marks the start of a transaction.
* `COMMIT [TRANSACTION | WORK]`: Makes all changes within the transaction permanent.
* `ROLLBACK [TRANSACTION | WORK]`: Undoes all changes made since the transaction began (or since the last `SAVEPOINT`).
* `SAVEPOINT <savepoint_name>`: Sets a point within a transaction to which you can later roll back.
* `ROLLBACK TO SAVEPOINT <savepoint_name>`: Rolls back changes made after the specified savepoint, but not the entire transaction.
* `SET TRANSACTION ISOLATION LEVEL <level>`: Sets the isolation level (e.g., `READ UNCOMMITTED`, `READ COMMITTED`, `REPEATABLE READ`, `SERIALIZABLE`) controlling how transactions interact.

---

## XIX. Views

* `CREATE [OR REPLACE] VIEW <view_name> [(col_aliases)] AS <SELECT_statement>;`
    * Creates a virtual table based on a stored query. Simplifies access, provides abstraction.
* `CREATE MATERIALIZED VIEW <view_name> AS <SELECT_statement>;` (PostgreSQL/Oracle etc.)
    * Stores the view's result physically. Needs refreshing (`REFRESH MATERIALIZED VIEW`). Faster reads, uses storage.
* `DROP VIEW <view_name>;` / `DROP MATERIALIZED VIEW <view_name>;`

---

## XX. Indexes

* `CREATE [UNIQUE] INDEX <index_name> ON <table_name> (col1 [ASC|DESC], ...);`
    * Creates an index to speed up data retrieval. `UNIQUE` enforces uniqueness.
* `CREATE INDEX <index_name> ON <table_name> (...) INCLUDE (col_a, col_b);` (SQL Server/PostgreSQL)
    * Includes non-key columns at the leaf level for covering queries.
* `CREATE FULLTEXT INDEX ON <table_name> (col1, ...);` (MySQL/SQL Server)
    * Special index for full-text searching.
* `DROP INDEX <index_name> [ON <table_name>];` (Syntax varies)

---

## XXI. Stored Procedures & Functions (Syntax varies greatly)

* `CREATE [OR REPLACE] PROCEDURE <proc_name> ( [param_mode param_name type, ...] ) LANGUAGE <lang> AS $$ BEGIN ... END; $$;` (PostgreSQL style)
    * Creates a stored procedure (block of code stored in DB). `param_mode`: IN, OUT, INOUT.
* `CREATE [OR REPLACE] FUNCTION <func_name> ( [params...] ) RETURNS <return_type> LANGUAGE <lang> AS $$ BEGIN ... RETURN value; END; $$;` (PostgreSQL style)
    * Creates a user-defined function (UDF). Can return scalar or table types.
* `EXEC <proc_name> (args);` / `CALL <proc_name> (args);` / `SELECT <func_name>(args);`
    * Executes a procedure or function.
* `DROP PROCEDURE <proc_name>;` / `DROP FUNCTION <func_name>;`

---

## XXII. Data Control Language (DCL) - Manages Permissions

* `GRANT <privilege1, privilege2, ... | ALL PRIVILEGES> ON <object_type> <object_name> TO <user | role | PUBLIC>;`
    * Gives permissions (e.g., `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `EXECUTE`) on objects (TABLE, VIEW, PROCEDURE, etc.).
* `REVOKE <privilege1, ... | ALL PRIVILEGES> ON <object_type> <object_name> FROM <user | role | PUBLIC>;`
    * Removes permissions.
* `CREATE USER <user_name> [WITH PASSWORD 'password'];` / `DROP USER <user_name>;` (Syntax varies)
* `CREATE ROLE <role_name>;` / `DROP ROLE <role_name>;`
* `GRANT <role_name> TO <user_name>;` / `REVOKE <role_name> FROM <user_name>;`

---

## XXIII. Advanced & Miscellaneous

* `PIVOT` / `UNPIVOT`: Transforms data from rows to columns (PIVOT) or columns to rows (UNPIVOT). Syntax is highly database-specific (native in SQL Server/Oracle, often emulated with conditional aggregation elsewhere).
* `Sequences`: Database objects for generating unique numeric identifiers.
    * `CREATE SEQUENCE <seq_name> [START WITH ...] [INCREMENT BY ...] ...;`
    * `NEXT VALUE FOR <seq_name>` (Standard/SQL Server) / `nextval('<seq_name>')` (PostgreSQL/Oracle)
    * `DROP SEQUENCE <seq_name>;`
* `EXPLAIN [ANALYZE] <query>;` / `EXPLAIN PLAN FOR <query>;` / `SET SHOWPLAN_TEXT ON;`
    * Shows the query execution plan chosen by the optimizer. Crucial for performance tuning. `ANALYZE` actually runs the query.
* **JSON/XML Functions:** Functions to query, create, and manipulate JSON/XML data stored in columns (e.g., `JSON_VALUE`, `JSON_QUERY`, `JSON_TABLE`, `XMLQUERY`, `XMLTABLE`). Highly dialect-specific.
* **Full-Text Search Functions:** `CONTAINS`, `FREETEXT` (SQL Server); `MATCH() AGAINST()` (MySQL); `@@`, `to_tsvector`, `to_tsquery` (PostgreSQL). Used with full-text indexes.
* `TABLESAMPLE [BERNOULLI | SYSTEM] (<percentage>) [REPEATABLE (<seed>)]` (Standard SQL/PostgreSQL/SQL Server)
    * Retrieves a random sample of rows from a table.
* **Optimizer Hints:** Database-specific directives embedded in queries (often in comments) to influence the query plan (e.g., `/*+ INDEX(table index_name) */` in Oracle, `OPTION (HASH JOIN)` in SQL Server). Use with caution.
* `TRIGGER`: A procedure automatically executed in response to certain DML events (INSERT, UPDATE, DELETE) on a table.
    * `CREATE TRIGGER <trigger_name> [BEFORE|AFTER|INSTEAD OF] [INSERT|UPDATE|DELETE] ON <table_name> [FOR EACH ROW|STATEMENT] EXECUTE FUNCTION <proc_name>();` (Syntax varies greatly).

This cheatsheet provides a broad overview of SQL. Remember that practical mastery comes from applying these concepts and understanding the nuances of your specific database system.

## XXIV. Logical Query Processing Order (How a `SELECT` Actually Runs)

SQL is **written** in one order but **logically executed** in another. This explains why some things (like aliases) are not available in `WHERE`, but are in `ORDER BY`.

**Logical (conceptual) execution order:**

1. `FROM`  
2. `ON` (join condition)  
3. `JOIN` (combine rows from tables)  
4. `WHERE` (row-level filtering)  
5. `GROUP BY` (create groups)  
6. `HAVING` (group-level filtering)  
7. `SELECT` (compute expressions, aliases)  
8. `DISTINCT`  
9. `ORDER BY`  
10. `LIMIT` / `OFFSET` / `FETCH` / `TOP`  

**Key implications:**

* `WHERE` cannot use aggregate functions (`SUM`, `COUNT`, etc.); use `HAVING` for that.
* `SELECT` aliases are **not** available in `WHERE`, but **are** available in `ORDER BY` and `HAVING` (most DBs).
* `WHERE` filters rows **before** grouping; `HAVING` filters **after** grouping.

```sql
SELECT
    d.department,
    COUNT(*) AS emp_count,
    AVG(e.salary) AS avg_salary
FROM employees e
JOIN departments d ON e.department_id = d.department_id
WHERE e.hire_date >= '2022-01-01'      -- row filter (before GROUP BY)
GROUP BY d.department
HAVING COUNT(*) >= 5                   -- group filter (after GROUP BY)
ORDER BY avg_salary DESC;              -- can use alias here
```

***

## XXV. Common Schema Types (OLTP vs. OLAP / Warehouse Schemas)

### 1. Normalized OLTP Schema (3NF-ish)

Optimized for **transactions** (many small reads/writes). Reduces redundancy.

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name        VARCHAR(100) NOT NULL,
    email       VARCHAR(200) UNIQUE,
    created_at  TIMESTAMP NOT NULL
);

CREATE TABLE orders (
    order_id     INT PRIMARY KEY,
    customer_id  INT NOT NULL REFERENCES customers(customer_id),
    order_date   DATE NOT NULL,
    total_amount DECIMAL(10,2) NOT NULL
);

CREATE TABLE order_items (
    order_item_id INT PRIMARY KEY,
    order_id      INT NOT NULL REFERENCES orders(order_id),
    product_id    INT NOT NULL,
    quantity      INT NOT NULL,
    unit_price    DECIMAL(10,2) NOT NULL
);
```

### 2. Star Schema (Data Warehouse)

Optimized for **analytics** (large scans, aggregations). Central **fact** table, surrounded by **dimension** tables.

```sql
-- Fact table (measures)
CREATE TABLE fact_sales (
    sales_key    INT PRIMARY KEY,
    date_key     INT NOT NULL,
    product_key  INT NOT NULL,
    customer_key INT NOT NULL,
    quantity     INT NOT NULL,
    revenue      DECIMAL(12,2) NOT NULL
    -- Foreign keys → dimensions (often enforced logically)
);

-- Dimension tables (descriptive attributes)
CREATE TABLE dim_date (
    date_key  INT PRIMARY KEY,
    full_date DATE,
    year      INT,
    month     INT,
    day       INT,
    weekday   VARCHAR(10)
);

CREATE TABLE dim_product (
    product_key   INT PRIMARY KEY,
    product_id    VARCHAR(50),
    product_name  VARCHAR(100),
    category      VARCHAR(50),
    brand         VARCHAR(50)
);
```

Star schema: **denormalized** dimensions (more repeated text, fewer joins, faster BI queries).

### 3. Snowflake Schema

Like star schema, but **dimensions are normalized** into multiple tables (more joins, less redundancy).

```sql
CREATE TABLE dim_product (
    product_key  INT PRIMARY KEY,
    product_id   VARCHAR(50),
    product_name VARCHAR(100),
    category_key INT
);

CREATE TABLE dim_category (
    category_key   INT PRIMARY KEY,
    category_name  VARCHAR(50),
    department_key INT
);

CREATE TABLE dim_department (
    department_key INT PRIMARY KEY,
    department_name VARCHAR(50)
);
```

### 4. Constellation / Galaxy Schema

Multiple fact tables sharing dimensions (e.g., `fact_sales`, `fact_inventory` both use `dim_date`, `dim_product`).

```sql
CREATE TABLE fact_inventory (
    inventory_key INT PRIMARY KEY,
    date_key      INT NOT NULL,
    product_key   INT NOT NULL,
    warehouse_key INT NOT NULL,
    qty_on_hand   INT NOT NULL
);
-- Shares dim_date, dim_product, etc. with fact_sales
```

***

## XXVI. Slowly Changing Dimensions (SCD) – Types & Quick Examples

Used in data warehousing to track **dimension attribute changes over time**  
(e.g., customer moves city, product category changes, etc.).

Assume a generic dimension structure:

```sql
CREATE TABLE dim_customer (
    customer_sk    INT PRIMARY KEY,      -- surrogate key
    customer_id    INT,                  -- business/natural key
    name           VARCHAR(100),
    city           VARCHAR(100),
    effective_from DATE,
    effective_to   DATE,
    is_current     BOOLEAN,
    version        INT
);
```

### SCD Type 0 – Retain Original (No Changes)

* Changes are **ignored** for chosen attributes.
* Used for “fixed” attributes (e.g., date of birth).

_No special SQL: you simply do not update these columns._

***

### SCD Type 1 – Overwrite (No History)

* Always keep only the **latest** value; past values are **lost**.
* Simple, but no time-travel analysis.

```sql
-- Overwrite current row in-place
UPDATE dim_customer d
SET
    name = s.name,
    city = s.city
FROM staging_customer s
WHERE d.customer_id = s.customer_id;
```

***

### SCD Type 2 – Add Row (Full History)

* **New row per change**; each row has a validity period.
* Supports “as of date” queries.

**Step 1: expire current row (if anything changed).**

```sql
UPDATE dim_customer d
SET
    effective_to = CURRENT_DATE - INTERVAL '1 day',
    is_current   = FALSE
FROM staging_customer s
WHERE d.customer_id = s.customer_id
  AND d.is_current = TRUE
  AND (d.name <> s.name OR d.city <> s.city);
```

**Step 2: insert new current row.**

```sql
INSERT INTO dim_customer (
    customer_id,
    name,
    city,
    effective_from,
    effective_to,
    is_current,
    version
)
SELECT
    s.customer_id,
    s.name,
    s.city,
    CURRENT_DATE             AS effective_from,
    DATE '9999-12-31'        AS effective_to,
    TRUE                     AS is_current,
    COALESCE(d.version, 0) + 1 AS version
FROM staging_customer s
LEFT JOIN dim_customer d
  ON d.customer_id = s.customer_id
 AND d.is_current = TRUE
WHERE (d.customer_sk IS NULL)           -- new customer
   OR (d.name <> s.name OR d.city <> s.city);  -- changed customer
```

**Point-in-time query example:**

```sql
SELECT *
FROM dim_customer
WHERE customer_id = 123
  AND DATE '2024-06-30' BETWEEN effective_from AND effective_to;
```

***

### SCD Type 3 – Limited History (Current + Previous)

* Store only **current** and **one previous** value in columns.

```sql
ALTER TABLE dim_customer
ADD COLUMN previous_city VARCHAR(100),
ADD COLUMN city_change_date DATE;

-- On city change:
UPDATE dim_customer d
SET
    previous_city    = d.city,
    city             = s.city,
    city_change_date = CURRENT_DATE
FROM staging_customer s
WHERE d.customer_id = s.customer_id
  AND d.city <> s.city;
```

***

### SCD Type 4 – History Table (Separate)

* Current table stays simple; separate **history** table keeps all old versions.

```sql
CREATE TABLE dim_customer_current (
    customer_id INT PRIMARY KEY,
    name        VARCHAR(100),
    city        VARCHAR(100)
);

CREATE TABLE dim_customer_history (
    history_sk   INT PRIMARY KEY,
    customer_id  INT,
    name         VARCHAR(100),
    city         VARCHAR(100),
    effective_from DATE,
    effective_to   DATE
);

-- On change: archive old row → history, then update current
INSERT INTO dim_customer_history (
    customer_id, name, city, effective_from, effective_to
)
SELECT
    c.customer_id,
    c.name,
    c.city,
    c.effective_from,
    CURRENT_DATE - INTERVAL '1 day'
FROM dim_customer_current c
JOIN staging_customer s
  ON c.customer_id = s.customer_id
WHERE c.city <> s.city;

UPDATE dim_customer_current c
SET
    name  = s.name,
    city  = s.city,
    effective_from = CURRENT_DATE
FROM staging_customer s
WHERE c.customer_id = s.customer_id;
```

***

### SCD Type 6 – Hybrid (1 + 2 + 3)

* Combines:
  * Type 1: current values kept on row.
  * Type 2: multiple rows with validity dates.
  * Type 3: separate “previous” columns.

Example structure:

```sql
CREATE TABLE dim_customer_type6 (
    customer_sk       INT PRIMARY KEY,
    customer_id       INT,
    current_city      VARCHAR(100),   -- Type 1: always latest
    historical_city   VARCHAR(100),   -- Type 2: city at that time
    previous_city     VARCHAR(100),   -- Type 3: prior value
    effective_from    DATE,
    effective_to      DATE,
    is_current        BOOLEAN
);
```

Pattern: on change, **expire** current row, **insert** new row (Type 2), and use `current_city` / `previous_city` to keep both latest and prior value on each row.

## XXIV. Normalization (Database Design)

**Normalization** is the process of structuring relational tables to:

- Reduce **data redundancy** (avoid unnecessary duplication)  
- Avoid anomalies on `INSERT`, `UPDATE`, `DELETE`  
- Improve **data integrity** and consistency  

In practice, this usually means splitting data into multiple related tables so that each table represents **one concept**, and non-key columns depend properly on the key.

### A. Anomalies (What Normalization Tries to Avoid)

- **Update anomaly:** Same fact stored in many rows; updating one place but not others causes inconsistency.  
- **Insert anomaly:** Cannot insert a fact because other unrelated data is missing.  
- **Delete anomaly:** Deleting one fact accidentally removes other important data.  

***

### B. First Normal Form (1NF)

A table is in **1NF** if:

- Each column holds **atomic** (indivisible) values (no arrays, lists, repeated groups).  
- Each row is unique.  
- There are no repeating column groups like `phone1`, `phone2`, etc.  

**Bad (not 1NF):**

```text
order_id | customer_name | product_ids
--------------------------------------
1        | Alice         | 10, 12, 15
```

**Good (1NF):**

```text
orders
-------
order_id | customer_name
1        | Alice

order_items
-----------
order_id | product_id
1        | 10
1        | 12
1        | 15
```

***

### C. Second Normal Form (2NF)

Applies when the primary key is **composite** (multiple columns).

A table is in **2NF** if:

- It is already in **1NF**.  
- Every non-key column depends on the **entire** primary key, not just part of it.  

**Bad (not 2NF):**

```text
-- PK = (order_id, product_id)
order_id | product_id | product_name | quantity
----------------------------------------------
1        | 10         | Keyboard     | 2
1        | 11         | Mouse        | 1
```

Here, `product_name` depends only on `product_id`, not on the full key `(order_id, product_id)`.

**Good (2NF):**

```text
products
---------
product_id | product_name
10         | Keyboard
11         | Mouse

order_items
-----------
order_id | product_id | quantity
1        | 10         | 2
1        | 11         | 1
```

***

### D. Third Normal Form (3NF)

A table is in **3NF** if:

- It is already in **2NF**.  
- There are **no transitive dependencies**:
  - No non-key column depends on another non-key column.  

**Bad (not 3NF):**

```text
customers
---------
customer_id | zip_code | city    | state
----------------------------------------
1           | 98101    | Seattle | WA
2           | 98052    | Redmond | WA
```

If `zip_code → (city, state)`, then `city` and `state` depend on `zip_code` (a non-key), not directly on `customer_id`.

**Good (3NF):**

```text
customers
---------
customer_id | zip_code
1           | 98101
2           | 98052

zip_codes
---------
zip_code | city    | state
98101    | Seattle | WA
98052    | Redmond | WA
```

Now every non-key column in each table depends only on that table’s key.

***

### E. Boyce–Codd Normal Form (BCNF)

BCNF is a slightly stricter version of 3NF:

- For every functional dependency `X → Y` in a table, `X` must be a **superkey**.

In practice:

- If your schema is in **3NF** and you do not have unusual dependencies (e.g., overlapping candidate keys), you are often close to BCNF.  
- BCNF may require further decomposition into more tables to remove subtle dependency issues.  

***

### F. Normalize vs. Denormalize

- **Normalize (1NF → 2NF → 3NF/BCNF)** for OLTP:
  - Many small, concurrent writes.  
  - Want to avoid anomalies and keep data consistent.  

- **Denormalize (selectively)** for OLAP / analytics:
  - Fewer writes, many complex reads.  
  - Star/snowflake schemas intentionally duplicate some descriptive data for simpler, faster reporting queries.  

You're right! I included ACID properties in my initial long code block but it didn't make it into your final markdown. Here's the **ACID section** to add after the Normalization section:

***

## XXVII. ACID Properties (Transaction Management)

**ACID** stands for **Atomicity, Consistency, Isolation, Durability** — the four key properties that guarantee reliable database transactions.

### A. Atomicity (All or Nothing)

A transaction is treated as a **single unit of work**:

- Either **all** operations in the transaction succeed and are committed, or  
- **None** of them are applied (transaction is rolled back).

No partial completion is allowed.

**Example:**

```sql
START TRANSACTION;

    UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;
    UPDATE accounts SET balance = balance + 500 WHERE account_id = 2;
    
    -- If either fails, ROLLBACK undoes both
    -- If both succeed, COMMIT makes both permanent

COMMIT;  -- or ROLLBACK;
```

***

### B. Consistency (Valid State to Valid State)

A transaction moves the database from one **valid state** to another:

- All defined rules (constraints, triggers, cascades) must be satisfied before and after the transaction.
- If a transaction would violate constraints, it is rolled back.

**Example:**

```sql
CREATE TABLE accounts (
    account_id INT PRIMARY KEY,
    balance DECIMAL(10,2) NOT NULL CHECK (balance >= 0)  -- Constraint
);

START TRANSACTION;
    UPDATE accounts SET balance = -100 WHERE account_id = 1;  -- FAILS (violates CHECK)
ROLLBACK;
```

***

### C. Isolation (Concurrent Transactions Don't Interfere)

Transactions execute **independently** of each other:

- Concurrent transactions should not see each other's uncommitted changes (depending on isolation level).
- Controlled by **isolation levels** that balance consistency vs. performance.

#### Isolation Levels (from least to most strict):

| Level | Dirty Reads | Non-Repeatable Reads | Phantom Reads |
|-------|-------------|---------------------|---------------|
| **READ UNCOMMITTED** | ✓ Possible | ✓ Possible | ✓ Possible |
| **READ COMMITTED** | ✗ Prevented | ✓ Possible | ✓ Possible |
| **REPEATABLE READ** | ✗ Prevented | ✗ Prevented | ✓ Possible |
| **SERIALIZABLE** | ✗ Prevented | ✗ Prevented | ✗ Prevented |

**Setting isolation level:**

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
-- or REPEATABLE READ, SERIALIZABLE, READ UNCOMMITTED

START TRANSACTION;
    -- your queries
COMMIT;
```

**Definitions:**

- **Dirty read:** Reading uncommitted changes from another transaction.
- **Non-repeatable read:** Same query returns different results within a transaction because another transaction modified the data.
- **Phantom read:** Same query returns different rows within a transaction because another transaction inserted/deleted rows.

***

### D. Durability (Committed Changes Persist)

Once a transaction is **committed**, changes are **permanent**:

- Data survives system crashes, power failures, or other errors.
- Implemented via **write-ahead logging (WAL)** and periodic checkpoints.

**Example:**

```sql
START TRANSACTION;
    INSERT INTO orders VALUES (1001, 'Customer A', CURRENT_DATE);
COMMIT;  -- After COMMIT succeeds, this data will survive even if server crashes immediately
```

***

### E. Savepoints (Partial Rollback)

You can set **savepoints** within a transaction to roll back to a specific point without undoing the entire transaction:

```sql
START TRANSACTION;

    INSERT INTO orders VALUES (1001, 'Customer A', CURRENT_DATE);
    
    SAVEPOINT before_items;
    
    INSERT INTO order_items VALUES (1001, 'Product X', 2);
    INSERT INTO order_items VALUES (1001, 'Product Y', 1);
    
    -- Oops, Product Y is out of stock
    ROLLBACK TO SAVEPOINT before_items;  -- Undo only order_items, keep order
    
    INSERT INTO order_items VALUES (1001, 'Product Z', 1);

COMMIT;
```

***

### F. Deadlock Handling

A **deadlock** occurs when two or more transactions wait for each other to release locks:

**Transaction 1:**

```sql
START TRANSACTION;
    UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
    -- waiting for lock on account 2...
    UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;
COMMIT;
```

**Transaction 2 (runs simultaneously):**

```sql
START TRANSACTION;
    UPDATE accounts SET balance = balance - 50 WHERE account_id = 2;
    -- waiting for lock on account 1... (DEADLOCK!)
    UPDATE accounts SET balance = balance + 50 WHERE account_id = 1;
COMMIT;
```

**Resolution:**

- Database detects the deadlock and **kills one transaction** (victim).
- Application should **catch the error and retry** the failed transaction.

**Prevention:**

- Always acquire locks in the **same order** across all transactions.
- Keep transactions **short** and **fast**.

***
