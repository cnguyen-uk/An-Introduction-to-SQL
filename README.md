# An Introduction to SQL
*A compact introduction to using SQL.*

Structured Query Language (SQL) is a programming language designed to manage data stored in relational databases. SQL operates through simple, declarative statements. This keeps data accurate and secure, and it helps maintain the integrity of databases, regardless of size.

The SQL language is simple to use and learn since its statements read almost like an English sentence. As such, no prior programming experience is required to learn SQL.

To use SQL, we need to choose a relational database management system (RDBMS), of which there are many. Some RDBMS's use a variant of SQL, such as T-SQL, SQL/PSM, and PL/SQL, which are used by Microsoft SQL Server, MySQL, and Oracle DB, respectively. Knowing the standard SQL language is a good place to start before looking at variants of it.

## Table of Contents

- [Code Standards](#code-standards)
  * [Indentation](#indentation)
  * [Comments](#comments)
    + [Inline Comments](#inline-comments)
    + [Block Comments](#block-comments)
  * [Quotes](#quotes)
  * [Names](#names)
- [Definitions](#definitions)
- [Manipulation](#manipulation)
  * [Table Creation](#table-creation)
  * [Table Alteration](#table-alteration)
- [Queries](#queries)
  * [Keywords](#keywords)
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

## Code Standards

All written code should follow a style guide to ensure that standards are kept consistent across any codebase and make code easier to read. Badly written code is difficult to scale, optimise, and debug. Such is the importance of high code standards that this guide will discuss it as a separate section before any code is seen.

We follow the standards in [S. Holywell's SQL Style Guide](https://www.sqlstyle.guide/). In particular, this section will act as a reference for language-specific best practices for indentation, comments, quotes, and names, since these can vary across different programming languages.

### Indentation

The number of lines used and how they are indented does not functionally affect the output, but should be used to improve the readability. Foundational best practices will be discussed here.

Spaces should be used to create indentation. In particular, they should be used to align code so that all keywords which start significant clauses are right aligned. This forms a whitespace river which makes the code easier to read, since clauses will have keywords clearly separated from their implementation detail, which should be left-aligned. Additionally, line breaks should be intelligently used to separate code into related sections.

```SQL
SELECT people_name,
       people_age, people_weight, people_height
       people_favourite_music, people_favourite_food
  FROM people
 WHERE people_name = 'Alice'
    OR people_name = 'Bob'
 GROUP BY people_name;
```

Joins should be indented to the other side of the whitespace river.

```SQL
SELECT *
  FROM people
       JOIN country
       ON people.country_id = country.id;
```

Within a `CREATE` clause four spaces should be used to indent column definitions, and each aspect of the code should be aligned to create sensible whitespace rivers.

```SQL
CREATE TABLE people (
    id      INTEGER PRIMARY KEY,
    name    TEXT    NOT NULL,
    age     INTEGER NOT NULL,
    country TEXT    DEFAULT United Kingdom
);
```

### Comments

Comments are wrapped with `/* */`. Inline comments have the option to begin with `--`.

#### Inline Comments

Inline comments should be used sparingly and not state the obvious. They should be separated by at least two spaces from the code and use commanding language rather than descriptive. For example, the following is preferred:

```SQL
SELECT people_name,
       people_age, people_weight -- Group characterstics together
```

As a comparison, the following should be avoided:

```SQL
SELECT people_name,
       people_age, people_weight -- Groups characterstics together
```

#### Block Comments

Block comments should be written in complete sentences, be indented with the code which it is commenting, and come *before* the code which it is commenting. Placing the comment after is merciless on a confused reader. Paragraphs are separated by a single line, and multi-sentence comments should have two spaces after a sentence-ending period (except after the final sentence).

```SQL
/* This statement queries for information on people.  It does not filter results.

People can be interesting.
*/
SELECT *
  FROM people
       JOIN country
       ON people.country_id = country.id;
```

### Quotes

The convention is to wrap strings in single quotes `' '`, rather than double quotes `" "`, but functionally either will work.

### Names

Keywords should be written in uppercase. Names should be written in *snake_case*, but uppercase characters may be used if necessary, such as for proper nouns.

## Definitions

A *relational database* is a database which organises data into one or more tables. It is conventional for the *columns* of tables to be data values of a particular type, and for the *rows* of tables to be the records in a table.

Data stored in a relational database will be of a certain *data type*. Different RDBMS's may have different names and details for data types, but for ones which use the standard SQL language, some common ones are:

- `INTEGER`: a positive or negative whole number.
- `TEXT`: a text string.
- `DATE`: the date formatted as YYYY-MM-DD.
- `REAL`: a decimal value.

An SQL *statement* is text which is recognised as a valid command. Statements always end in a `;`. Statements consist of *keywords*, which are special pieces of text that begin specific tasks in SQL; *clauses* or *commands* contain the implementation details of what the specific task is.

## Manipulation

Often we can use tools other than SQL to manipulate relational databases, but knowing how to do so with SQL opens up the possibility for automation.

### Table Creation

The `CREATE TABLE` keyword is used to create completely new tables.

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
    id    INTEGER PRIMARY KEY,
    name  TEXT    UNIQUE,
    grade INTEGER NOT NULL,
    age   INTEGER DEFAULT 10
);
```

Commonly used constraints are:

- `NOT NULL`: ensures that a column cannot have a `NULL` value.
- `UNIQUE`: ensures that each value in a column is different. A table may have multiple `UNIQUE` columns.
- `PRIMARY KEY`: a combination of both `NOT NULL` and `UNIQUE`. This is used to uniquely identify each row in a table. A table may only have one `PRIMARY KEY` column.
- `DEFAULT`: assigns a default value for a column when no value is specified.

### Table Alteration

The `INSERT INTO` keyword is used add new rows to a table. Either of the following statements can be used - the first inserts columns in order, and the second inserts columns by name:

```SQL
INSERT INTO table_name
VALUES (value1, value2, value3);
```

```SQL
INSERT INTO table_name (column1, column2, column3)
VALUES (value1, value2, value3);
```

We can also insert multiple rows without using multiple `VALUES` keywords.

```SQL
INSERT INTO table_name
VALUES (value1, value2, value3),
       (value4, value5, value6);
```

```SQL
INSERT INTO table_name (column1, column2, column3)
VALUES (value1, value2, value3),
       (value4, value5, value6);
```

The `DELETE` keyword is used to remove rows from a table.

```SQL
DELETE FROM table_name
 WHERE some_column = some_value;
```

The `ALTER TABLE` keyword is used to modify existing columns of a table. For example, it may be used with the `ADD` keyword to add a new column to the table. See [here](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html) for a full list of keyword options.

```SQL
ALTER TABLE table_name
  ADD column_name datatype;
```

The `UPDATE` keyword is used to edit rows in a table. It is usually followed by a `SET` keyword, to indicate which columns should be updated to what values, and a `WHERE` (seen in further detail later) keyword, to specify what criteria the values in the columns should satisfy before being set to another value.

```SQL
UPDATE table_name
   SET column1 = value1, column2 = value2
 WHERE some_column = some_value;
```

## Queries

Querying is the most popular reason to use SQL. In this section we will look at some of the most commonly used keywords, and operators used in combination with them. For a full list of keywords, see [here](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/reserved-keywords-transact-sql).

### Keywords

The `SELECT` keyword is used to return specified columns from a table. It can be used with the `*` wildcard character to return all columns in a table.

```SQL
SELECT *
  FROM table_name;
```

```SQL
SELECT column1, column2
  FROM table_name;
```

The `AS` keyword is used to alias (i.e. rename) columns or tables. The returned result set will reflect this alias.

```SQL
/* This statement returns the column_name column as new_name. */
SELECT column_name AS new_name
  FROM table_name;
```

```SQL
/* This statement returns the resulting table as new_name. */
SELECT column_name
  FROM table_name AS new_name;
```

The `DISTINCT` keyword is used to return unique values of specified columns. For example, if the values of a specified column were Alice, Bob, Carol, Bob, and Eve, then the returned values would be Alice, Bob, Carol and Eve.

```SQL
SELECT DISTINCT column_name
  FROM table_name;
```

The `LIMIT` keyword is used to restrict the result set to a specified number of rows.

```SQL
/* This statement limits the result set to the first 10 rows. */
SELECT *
  FROM table_name
 LIMIT 10;
```

The `ORDER BY` keyword is used to sort the result set of a query by one or more columns. The `ASC` and `DESC` keywords can be used to specify how to order the result set by ascending or descending order, respectively. If no keyword is used, then ascending is the default.

```SQL
SELECT *
  FROM table_name
 ORDER BY some_column1 ASC, some_column2 DESC;
```

The `WHERE` keyword is used to filter rows that match a certain condition. We can also use `IS NULL` or `IS NOT NULL` in combination with this keyword.

```SQL
SELECT *
  FROM table_name
 WHERE condition;
```

### Operators

Operators are often in combination with the `WHERE` keyword.

The `AND` and `OR` operators allows multiple conditions to be combined. Brackets can be used to create more complex logic. As usual, if the `AND` operator is used, then the returned set will match both joined conditions; and if the `OR` operator is used, then the returned set will match either joined condition.

```SQL
SELECT column1, column2
  FROM table_name
 WHERE (column1 = 4 OR column5 < 8)
       AND column9 >= 2;
```

The `LIKE` operator is used in a `WHERE` keyword to match a specified pattern. The `%` or `_` wildcard characters can be used to create a pattern to match. The `%` character is used to match zero or more unspecified characters, and the `_` character is used to match any single unspecified character.

```SQL
/* This statement returns names starting with "Ad". */
SELECT name
  FROM customer_list
 WHERE name LIKE 'Ad%';
```

```SQL
/* This statement returns names ending with "m". */
SELECT name
  FROM customer_list
 WHERE name LIKE '%m';
```

```SQL
/* This statement returns names which have "C", "r", "l" as their
first, third and fifth characters, respectively. */
SELECT name
  FROM customer_list
 WHERE name LIKE 'C_r_l';
```

The `BETWEEN` operator is used to filter a range of values. The values can be text, numbers, or date data. Filtering will always be inclusive.

```SQL
/* This statement filters to the years 1980, 1981, 1982 and 1983. */
SELECT *
  FROM book_data
 WHERE year BETWEEN 1980 AND 1983;
```

### IF-THEN Logic

In SQL the `CASE` keyword is used to handle IF-THEN logic, i.e. a way to create different outputs based on certain conditions. This keyword is usually used in combination with the `SELECT` keyword to produce a new column, based on conditions. In the following statement, when `condition1` is met, the corresponding row in the `new_column` column will contain `result1`, and so on. As usual, the `ELSE` keyword deals with all other conditions.

```SQL
SELECT column1, column2
    CASE
        WHEN condition1 THEN result1
        WHEN condition2 THEN result2
        ELSE result3
    END AS new_column
  FROM table_name;
```

### Column References

Instead of using explicit column names, we can instead reference selected columns by the number in which they appear in the `SELECT` statement. This is often used with the `ORDER BY` and `GROUP BY` (seen in more detail later) keywords.

```SQL
/* This statement groups books by the year published, and ordered
by price. */
SELECT day, week, month, year,
       book, price, author
  FROM book_data
 GROUP BY 4
 ORDER BY 6;
```

## Functions

SQL has a number of useful functions which can be used to produce more helpful results or filtering. In this section we list some of the most commonly used ones.

### Aggregate Functions

Aggregate functions are useful for returning aggregate data based on the function's argument, as a way of simplifying data to produce more insightful results. They are commonly used with the `SELECT` keyword, but can also be used elsewhere such as in `CASE` statements. As such, all of the statements in this section will have aggregate functions used in combination with the `SELECT` keyword. It is also common to use the `WHERE` keyword in combination with aggregate functions.

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
/* This statement returns a count of all books published after the
year 2000. */
SELECT COUNT(*)
  FROM book_data
 WHERE year > 2000;
```

Note that that the `COUNT()` function can also use column names as arguments, but will *not* count `NULL` values in that column.

### Grouping

It is common to want to group results by columns in combination with aggregate function usage. In particular, if we want to select more columns for our result set, then we must use grouping. The `GROUP BY` keyword will group rows in a result set by identical values in one or more columns. The `GROUP BY` keyword can come after `FROM` or `WHERE`, but must come before `ORDER BY` or `LIMIT`.

```SQL
/* This statement returns a count of all books published, for
each year. */
SELECT year, COUNT(*)
  FROM book_data
 GROUP BY year;
```

Furthermore, we can further filter our grouped result set. since `GROUP BY` cannot be followed by `WHERE`, we must instead use the `HAVING` keyword, often used with aggregate functions.

```SQL
/* This statement restricts the previous example to years which
published a significant number of books. */
SELECT year, COUNT(*)
  FROM book_data
 GROUP BY year
HAVING COUNT(*) > 100;
```

### Other Functions

The `ROUND()` function will round a number to a specified number of places. It takes two arguments: a number, and a number of decimal places. It can also be combined with other aggregate functions.

```SQL
/* This statement returns the average price of books for each year,
rounded to two decimal places. */
SELECT year, ROUND(AVG(price), 2)
  FROM book_data
 GROUP BY year;
```

The `LEN()` function is used to return the length of a text field.

```SQL
/* This statement returns the average length of book names for
each year. */
SELECT year, AVG(LEN(name))
  FROM book_data
 GROUP BY year;
```

## Multiple Tables

Sometimes it is useful to join multiple tables in a relational database before working on them as a single table, or to create multiple tables for temporary use within a statement.

### Temporary Tables

The `WITH` keyword can be used to store the result of a query in a temporary table using an alias. Multiple temporary tables can be created with a single instance of the keyword. These temporary tables can then be worked on or even joined (discussed next).

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

The `INNER JOIN` keyword performs an inner join. This join returns a result set based on column values common to both tables, where conditions are specified using the `ON` keyword. Note that we can also use the `JOIN` keyword instead, since an inner join is the default join. The general statement is as follows:

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

The `LEFT JOIN` keyword performs a left outer join. This join returns a result set based on column values common to the left table. If there is no match for a column value from the left table with a corresponding right table value, then the right table value will be set to `NULL` in the result set. Similarly, the `RIGHT JOIN` keyword performs a right outer join.

```SQL
SELECT *
  FROM table1
       LEFT JOIN table2
       ON table1.some_column1 = table2.some_column2;
```

The `UNION` keyword performs a full join. This join returns a result set by combining results from multiple `SELECT` statements, and filters duplicates. Note that both `SELECT` statements must select the same number of columns, and have the same respective data types.

```SQL
SELECT some_column1
  FROM table1
 UNION
SELECT some_column2
  FROM table2;
```

The `CROSS JOIN` keyword performs a cross join. This join returns a result set by combining each row from one table with each row from another table.

```SQL
/* This statement returns all possible combinations of shirts
and ties. */
SELECT shirts.shirt_colour, ties.tie_colour
  FROM shirts
       CROSS JOIN ties;
```
