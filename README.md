# An Introduction to SQL
*A compact introduction to using SQL.*

Structured Query Language (SQL) is a programming language designed to manage data stored in relational databases. SQL operates through simple, declarative statements. This keeps data accurate and secure, and it helps maintain the integrity of databases, regardless of size.

The SQL language is simple to use and learn since its statements read almost like an English sentence. As such, no prior programming experience is required to learn SQL.

To use SQL, we need to choose a relational database management system (RDBMS), of which there are many. Some RDBMS's use a variant of SQL, such as T-SQL, SQL/PSM, and PL/SQL, which are used by Microsoft SQL Server, MySQL, and Oracle DB, respectively. Knowing the standard SQL language is a good place to start before looking at variants of it.

## Table of Contents

- [Definitions](#definitions)
- [Manipulation](#manipulation)
  * [Table Creation](#table-creation)
  * [Table Alteration](#table-alteration)
- [Queries](#queries)
  * [Commands](#commands)
  * [Operators](#operators)
  * [IF-THEN Logic](#if-then-logic)
  * [Column References](#column-references)
- [Functions](#functions)
  * [Aggregate Functions](#aggregate-functions)
  * [Grouping](#grouping)
  * [Other Functions](#other-functions)
- [Multiple Tables](#multiple-tables)
  * [Temporary Tables](#temporary-tables)
  * [Joining](#joining)

## Definitions

A *relational database* is a database which organises data into one or more tables. It is conventional for the *columns* of tables to be data values of a particular type, and for the *rows* of tables to be the records in a table.

Data stored in a relational database will be of a certain *data type*. Different RDBMS's may have different names and details for data types, but for ones which use the standard SQL language, some common ones are:

- `INTEGER`: a positive or negative whole number.
- `TEXT`: a text string.
- `DATE`: the date formatted as YYYY-MM-DD.
- `REAL`: a decimal value.

An SQL *statement* is text which is recognised as a valid command. Statements always end in a `;`. Statements consist of *clauses* or *commands* which are special pieces of text that perform specific tasks in SQL. By convention, clauses/commands are written in capital letters.

Unlike other programming languages, in SQL the number of lines used does not matter, so statements can be broken up over multiple lines for readability.

It is also conventional to wrap strings in single quotes `' '`, as opposed to double quotes `" "`, but either will work.

Line and block comments start with `/*` and end with `*/`.

## Manipulation

Often we can use tools other than SQL to manipulate relational databases, but knowing how to do so with SQL opens up the possibility for automation.

### Table Creation

The `CREATE TABLE` command is used to create completely new tables.

```SQL
CREATE TABLE table_name (
  column1 datatype1,
  column2 datatype2,
  column3 datatype3
);
```

We may also add column constraints to our tables, i.e. rules which apply to the values of individual columns. This can be used to tell the database to reject inserted data that does not adhere to a certain restriction, which can help keep stored data reliable and consistent.

```SQL
CREATE TABLE student (
  id INTEGER PRIMARY KEY,
  name TEXT UNIQUE,
  grade INTEGER NOT NULL,
  age INTEGER DEFAULT 10
);
```

Commonly used constraints are:

- `NOT NULL`: ensures that a column cannot have a `NULL` value.
- `UNIQUE`: ensures that each value in a column is different. A table may have multiple `UNIQUE` columns.
- `PRIMARY KEY`: a combination of both `NOT NULL` and `UNIQUE`. This is used to uniquely identify each row in a table. A table may only have one `PRIMARY KEY` column.
- `DEFAULT`: assigns a default value for a column when no value is specified.

### Table Alteration

The `INSERT INTO` command is used add new rows to a table. Either of the following statements can be used - the first inserts columns in order, and the second inserts columns by name:

```SQL
INSERT INTO table_name
VALUES (value1, value2, value3);
```

```SQL
INSERT INTO table_name (column1, column2, column3)
VALUES (value1, value2, value3);
```

We can also insert multiple rows without using multiple `VALUES` commands.

```SQL
INSERT INTO table_name
VALUES
  (value1, value2, value3),
  (value4, value5, value6);
```

```SQL
INSERT INTO table_name (column1, column2, column3)
VALUES
  (value1, value2, value3),
  (value4, value5, value6);
```

The `DELETE` command is used to remove rows from a table.

```SQL
DELETE FROM table_name
WHERE some_column = some_value;
```

The `ALTER TABLE` command is used to modify existing columns of a table. For example, it may be used with the `ADD` command to add a new column to the table. See [here](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html) for a full list of command options.

```SQL
ALTER TABLE table_name
ADD column_name datatype;
```

The `UPDATE` command is used to edit rows in a table. It is usually followed by a `SET` command, to indicate which columns should be updated to what values, and a `WHERE` (seen in further detail later) command, to specify what criteria the values in the columns should satisfy before being set to another value.

```SQL
UPDATE table_name
SET column1 = value1, column2 = value2
WHERE some_column = some_value;
```

## Queries

Querying is the most popular reason to use SQL. In this section we will look at some of the most commonly used commands, and operators used in combination with them.

### Commands

The `SELECT` command is used to return specified columns from a table. It can be used with the `*` wildcard character to return all columns in a table.

```SQL
SELECT *
FROM table_name;
```

```SQL
SELECT column1, column2
FROM table_name;
```

The `AS` command is used to alias (i.e. rename) columns or tables. The returned result set will reflect this alias.

```SQL
/* Returns the column_name column in the resulting table as new_name. */
SELECT column_name AS new_name
FROM table_name;
```

```SQL
/* Returns the resulting table as new_name. */
SELECT column_name
FROM table_name AS new_name;
```

The `DISTINCT` command is used to return unique values of specified columns. For example, if the values of a specified column were Alice, Bob, Carol, Bob, and Eve, then the returned values would be Alice, Bob, Carol and Eve.

```SQL
SELECT DISTINCT column_name
FROM table_name;
```

The `LIMIT` command is used to restrict the result set to a specified number of rows.

```SQL
/* Limits the result set to the first 10 rows. */
SELECT *
FROM table_name
LIMIT 10;
```

The `ORDER BY` command is used to sort the result set of a query by one or more columns. The `ASC` and `DESC` keywords can be used to specify how to order the result set by ascending or descending order, respectively. If no keyword is used, then ascending is the default.

```SQL
SELECT *
FROM table_name
ORDER BY
  some_column1 ASC,
  some_column2 DESC;
```

The `WHERE` command is used to filter rows that match a certain condition. We can also use `IS NULL` or `IS NOT NULL` in combination with this command.

```SQL
SELECT *
FROM table_name
WHERE condition;
```

### Operators

Operators are often in combination with the `WHERE` command.

The `AND` and `OR` operators allows multiple conditions to be combined. Brackets can be used to create more complex logic. As usual, if the `AND` operator is used, then the returned set will match both joined conditions; and if the `OR` operator is used, then the returned set will match either joined condition.

```SQL
SELECT column1, column2
FROM table_name
WHERE (column1 = 4 OR column5 < 8) AND column9 >= 2;
```

The `LIKE` operator is used in a `WHERE` command to match a specified pattern. The `%` or `_` wildcard characters can be used to create a pattern to match. The `%` character is used to match zero or more unspecified characters, and the `_` character is used to match any single unspecified character.

```SQL
/* Returns names starting with "Ad". */
SELECT name
FROM customer_list
WHERE name LIKE 'Ad%';
```

```SQL
/* Returns names ending with "m". */
SELECT name
FROM customer_list
WHERE name LIKE '%m';
```

```SQL
/* Returns names which have "C", "r", "l" as their first, third and fifth characters, respectively. */
SELECT name
FROM customer_list
WHERE name LIKE 'C_r_l';
```

The `BETWEEN` operator is used to filter a range of values. The values can be text, numbers, or date data. Filtering will always be inclusive.

```SQL
/* Returns the years 1980, 1981, 1982, 1983, 1984, 1985, 1986, 1987, 1988, 1989, 1990. */
SELECT *
FROM book_data
WHERE year BETWEEN 1980 AND 1990;
```

### IF-THEN Logic

In SQL the `CASE` command is used to handle IF-THEN logic, i.e. a way to create different outputs based on certain conditions. This command is usually used in combination with the `SELECT` command to produce a new column, based on conditions. In the following statement, when `condition1` is met, the corresponding row in the `new_column` column will contain `result1`, and so on. As usual, the `ELSE` command deals with all other conditions.

```SQL
SELECT column1, column2,
  CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ELSE result3
  END AS new_column
FROM table_name;
```

### Column References

Instead of using explicit column names, we can instead reference selected columns by the number in which they appear in the `SELECT` statement. This is often used with the `ORDER BY` and `GROUP BY` (seen in more detail later) commands.

```SQL
/* Groups books by the year published, ordered by price. */
SELECT day, week, month, year, book, price, author
FROM book_data
GROUP BY 4
ORDER BY 6;
```

## Functions

SQL has a number of useful functions which can be used to produce more helpful results or filtering. In this section we list some of the most commonly used ones.

### Aggregate Functions

Aggregate functions are useful for returning aggregate data based on the function's argument, as a way of simplifying data to produce more insightful results. They are commonly used with the `SELECT` command, but can also be used elsewhere such as in `CASE` statements. As such, all of the statements in this section will have aggregate functions used in combination with the `SELECT` command. It is also common to use the `WHERE` command in combination with aggregate functions.

The `MAX()` aggregate function takes the name of a column as an argument, and returns the largest value in that column.

```SQL
SELECT MAX(column_name)
FROM table_name;
```

Similarly, the `MIN()` aggregate function takes the name of a column as an argument, and returns the smallest value in that column.

```SQL
SELECT MIN(column_name)
FROM table_name;
```

The `SUM()` aggregate function takes the name of a column as an argument, and returns the sum of all values in that column.

```SQL
SELECT SUM(column_name)
FROM table_name;
```

The `AVG()` aggregate function takes the name of a column as an argument, and returns the average of all values in that column.

```SQL
SELECT AVG(column_name)
FROM table_name;
```


One of the most useful aggregate functions is the `COUNT()` function. This function returns the total number of rows which match the specified criteria.

```SQL
/* Produces a count of all books published after the year 2000. */
SELECT COUNT(*)
FROM book_data
WHERE year > 2000;
```

Note that that the `COUNT()` function can also use column names as arguments, but will *not* count `NULL` values in that column.

### Grouping

It is common to want to group results by columns in combination with aggregate function usage. In particular, if we want to select more columns for our result set, then we must use grouping. The `GROUP BY` command will group rows in a result set by identical values in one or more columns. The `GROUP BY` command can come after `FROM` or `WHERE`, but must come before `ORDER BY` or `LIMIT`.

```SQL
/* Produces a count of all books published for each year. */
SELECT year, COUNT(*)
FROM book_data
GROUP BY year;
```

Furthermore, we can further filter our grouped result set. since `GROUP BY` cannot be followed by `WHERE`, we must instead use the `HAVING` command, often used with aggregate functions.

```SQL
/* Restricts the previous example to years which published a significant number of books. */
SELECT year, COUNT(*)
FROM book_data
GROUP BY year
HAVING COUNT(*) > 100;
```

### Other Functions

The `ROUND()` function will round a number to a specified number of places. It takes two arguments: a number, and a number of decimal places. It can also be combined with other aggregate functions.

```SQL
/* Returns the average price of books for each year, rounded to two decimal places. */
SELECT year, ROUND(AVG(price), 2)
FROM book_data
GROUP BY year;
```

The `LEN()` function is used to return the length of a text field.

```SQL
/* Returns the average length of book names for each year. */
SELECT year, AVG(LEN(name))
FROM book_data
GROUP BY year;
```

## Multiple Tables

Sometimes it is useful to join multiple tables in a relational database before working on them as a single table, or to create multiple tables for temporary use within a statement.

### Temporary Tables

The `WITH` command can be used to store the result of a query in a temporary table using an alias. Multiple temporary tables can be created with a single instance of the command. These temporary tables can then be worked on or even joined (discussed next).

```SQL
WITH temporary_table1 AS (
  SELECT column1, column2
  FROM table_name1
),
temporary_table2 AS (
  SELECT column3
  FROM table_name2
);
```

### Joining

We now look at some common ways of working with multiple tables via joins, but it should be noted that many more ways of joining tables exist.

The `INNER JOIN` command performs an inner join. This join returns a result set based on column values common to both tables, where conditions are specified using the `ON` command. Note that we can also use the `JOIN` command instead, since an inner join is the default join. The general statement is as follows:

```SQL
SELECT *
FROM table1
JOIN table2
  ON table1.some_column1 = table2.some_column2;
```

For a more specific example, suppose we have a table of books, with author IDs, and a table of authors, also with IDs attached. Then we can create a useful result set matching books with their authors by using the following statement:

```SQL
SELECT *
FROM books
JOIN authors
  ON books.author_id = authors.id;
```

The `LEFT JOIN` command performs a left outer join. This join returns a result set based on column values common to the left table. If there is no match for a column value from the left table with a corresponding right table value, then the right table value will be set to `NULL` in the result set. Similarly, the `RIGHT JOIN` command performs a right outer join.

```SQL
SELECT *
FROM table1
LEFT JOIN table2
  ON table1.some_column1 = table2.some_column2;
```

The `UNION` command performs a full join. This join returns a result set by combining results from multiple `SELECT` statements, and filters duplicates. Note that both `SELECT` statements must select the same number of columns, and have the same respective data types.

```SQL
SELECT some_column1
FROM table1
UNION
SELECT some_column2
FROM table2;
```

The `CROSS JOIN` command performs a cross join. This join returns a result set by combining each row from one table with each row from another table.

```SQL
/* Returns all possible combinations of shirts and ties. */
SELECT shirts.shirt_colour, pants.pants_colour
FROM shirts
CROSS JOIN pants;
```
