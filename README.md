# Comprehensive SQL Cheatsheet: Beginner to Advanced

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
    * `[]` (SQL Server/Access): Matches any single character within the brackets (e.g., `'[TC]om'` finds Tom and Com).
    * `^` or `!` (SQL Server/Access): Matches any character *not* within the brackets (e.g., `'[^A-F]%'` finds strings not starting with A-F).
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
* `FETCH FIRST <number> ROWS ONLY` (Standard SQL/Oracle/DB2)
    * Standard SQL way to limit rows.
* `TOP <number> [PERCENT] [WITH TIES]` (SQL Server/Access)
    * Limits rows returned (used in `SELECT`). `WITH TIES` includes rows that match the sort value of the last row.

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
* `GROUP_CONCAT(<column> [SEPARATOR 'sep'])` (MySQL) / `STRING_AGG(<column>, 'sep')` (PostgreSQL/SQL Server) / `LISTAGG(<column>, 'sep') WITHIN GROUP (ORDER BY ...)` (Oracle)
    * Concatenates string values from different rows within a group.

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
