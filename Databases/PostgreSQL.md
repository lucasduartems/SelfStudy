# Table of contents

- [PostgreSQL installation on Linux](#postgresql-installation-on-linux)
  * [Accessing PostgreSQL for the first time](#accessing-postgresql-for-the-first-time)
  * [Setting a password](#setting-a-password)
  * [Importing a sample database](#importing-a-sample-database)
- [Structured Query Language (SQL)](#structured-query-language--sql-)
  * [SELECT](#select)
    + [FROM](#from)
      - [Specific columns](#specific-columns)
      - [All columns](#all-columns)
      - [Concatenation](#concatenation)
      - [Expressions](#expressions)
      - [Column alias](#column-alias)
      - [Expression alias](#expression-alias)
    + [ORDER BY](#order-by)
      - [ASC, DESC](#asc--desc)
      - [NULLS FIRST, NULLS LAST](#nulls-first--nulls-last)
    + [DISTINCT](#distinct)
      - [DISTINCT single column](#distinct-single-column)
      - [DISTINCT multiple columns](#distinct-multiple-columns)
      - [DISTINCT ON](#distinct-on)
    + [WHERE](#where)
      - [Simple filter clause](#simple-filter-clause)
      - [AND, OR](#and--or)
      - [IN](#in)
      - [LIKE](#like)
        * [PostgreSQL ILIKE](#postgresql-ilike)
        * [PostgreSQL operators](#postgresql-operators)
        * [Wildcards](#wildcards)
      - [BETWEEN](#between)
      - [Clause-building operators](#clause-building-operators)
    + [LIMIT](#limit)
      - [OFFSET](#offset)
      - [Top/bottom rows](#top-bottom-rows)
    + [FETCH](#fetch)
    + [JOIN](#join)
      - [INNER JOIN](#inner-join)
        * [Table alias](#table-alias)
        * [USING](#using)
        * [INNER JOIN with 3 tables](#inner-join-with-3-tables)
      - [LEFT OUTER JOIN](#left-outer-join)
      - [LEFT OUTER JOIN - only left table rows](#left-outer-join---only-left-table-rows)
      - [RIGHT OUTER JOIN](#right-outer-join)
      - [RIGHT OUTER JOIN - only right table rows](#right-outer-join---only-right-table-rows)
      - [FULL OUTER JOIN](#full-outer-join)
      - [FULL OUTER JOIN - only rows unique to both tables](#full-outer-join---only-rows-unique-to-both-tables)
      - [Self join](#self-join)
      - [Summary](#summary)
  * [PostgreSQL CLI commands](#postgresql-cli-commands)
- [References](#references)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>

# PostgreSQL installation on Linux

```bash
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get install postgresql
```

## Accessing PostgreSQL for the first time

```
sudo apt-get install postgresql
sudo -i -u postgres
psql
```

## Setting a password

```
postgres=# \password 
Enter new password:
Enter it again: 
```

## Importing a sample database

The steps below can be followed to import an example database called `dvdvrental`:

```
curl -O https://sp.postgresqltutorial.com/wp-content/uploads/2019/05/dvdrental.zip
unzip dvdrental.zip
```

```
psql
postgres=# create database dvdrental;
postgres=# \q
```

```bash
pg_restore --dbname=dvdrental --verbose dvdrental.tar
```

The imported `dvd rental` represents the business process of a DVD rental store and has the following tables:

![Tables](images/Tables.png)

A summary and the data types can be found below:

![TablesBrief](images/TablesBrief.png)

![TableDataTypes](images/TableDataTypes.png)

# Structured Query Language (SQL)

## SELECT

### FROM

<br>

<tt>
    <b>SELECT</b> columns<br>
    <b>FROM</b> &nbsp;&nbsp;table
</tt>

<br>
<br>

Order of evaluation:

```
╭──────────╮         ╭──────────╮
│   FROM   │  ────>  │  SELECT  │
╰──────────╯         ╰──────────╯
```

#### Specific columns

```sql
SELECT first_name FROM customer;
```
| first_name |
|------------|
| Jared      |
| Mary       |
| Patricia   |
| Linda      |
| Barbara    |
| . . .      |

<br>

```sql
SELECT first_name, last_name FROM customer;
```
| first_name | last_name |
|------------|-----------|
| Jared      | Ely       |
| Mary       | Smith     |
| Patricia   | Johnson   |
| Linda      | Williams  |
| Barbara    | Jones     |
|  . . .     | . . .     |

<br>

#### All columns

```sql
SELECT * FROM customer;
```
| customer_id | store_id | first_name | last_name |                email                | . . . |
|-------------|----------|------------|-----------|-------------------------------------|-------|
|         524 |        1 | Jared      | Ely       | jared.ely@sakilacustomer.org        | . . . |
|           1 |        1 | Mary       | Smith     | mary.smith@sakilacustomer.org       | . . . |
|           2 |        1 | Patricia   | Johnson   | patricia.johnson@sakilacustomer.org | . . . |
|           3 |        1 | Linda      | Williams  | linda.williams@sakilacustomer.org   | . . . |
|           4 |        2 | Barbara    | Jones     | barbara.jones@sakilacustomer.org    | . . . |
|. . .|. . .|. . .|. . .|. . .|. . .|

The use of `*` in the `SELECT` statement is discouraged due to the possible impacts on performance, as well as to avoid fetching unnecessary data.

*It is a good practice to name the columns.*

<br>

#### Concatenation

```sql
SELECT first_name || ', ' || last_name, email
FROM customer;
```
|     ?column?      |                email                |
|-------------------|-------------------------------------|
| Ely, Jared        | jared.ely@sakilacustomer.org        |
| Smith, Mary       | mary.smith@sakilacustomer.org       |
| Johnson, Patricia | patricia.johnson@sakilacustomer.org |
| Williams, Linda   | linda.williams@sakilacustomer.org   |
| Jones, Barbara    | barbara.jones@sakilacustomer.org    |
|. . .|. . .|

<br>

#### Expressions

```sql
SELECT 2 * 3;
```
| ?column? |
|----------|
|    6     |

<br>

#### Column alias

```sql
SELECT first_name, last_name AS surname
FROM customer;
```
| first_name |  surname  |
|------------|-----------|
| Jared      | Ely       |
| Mary       | Smith     |
| Patricia   | Johnson   |
| Linda      | Williams  |
| Barbara    | Jones     |
|  . . .     | . . .     |

<br>

#### Expression alias

```sql
SELECT first_name || ' ' || last_name AS full_name
FROM customer;
```

|    full_name     |
|------------------|
| Jared Ely        |
| Mary Smith       |
| Patricia Johnson |
| Linda Williams   |
| Barbara Jones    |
| . . . |

<br>

```sql
SELECT first_name || ' ' || last_name AS "Full name"
FROM customer;
```

|    Full name     |
|------------------|
| Jared Ely        |
| Mary Smith       |
| Patricia Johnson |
| Linda Williams   |
| Barbara Jones    |
| . . . |

<br>


### ORDER BY

<br>

<tt>
    <b>SELECT</b> &nbsp;&nbsp;columns<br>
    <b>FROM</b> &nbsp;&nbsp;&nbsp;&nbsp;table<br>
    <b>ORDER BY</b> sort_expression
</tt>

<br>
<br>

Order of evaluation:

```
╭──────────╮         ╭──────────╮         ╭──────────╮
│   FROM   │  ────>  │  SELECT  │  ────>  │ ORDER BY │
╰──────────╯         ╰──────────╯         ╰──────────╯
```

<br>

```sql
SELECT first_name, last_name AS full_name
FROM customer
ORDER BY first_name ASC;
```
| first_name | last_name |
|------------|-----------|
| Aaron      | Selby     |
| Adam       | Gooch     |
| Adrian     | Clary     |
| Agnes      | Bishop    |
| Alan       | Kahn      |
|. . .|. . .|

<br>

#### ASC, DESC

<br>

```sql
SELECT first_name, last_name AS full_name
FROM customer
ORDER BY first_name ASC, last_name DESC;
```
| first_name  | last_name    |
|-------------|-----------   |
| . . .       | . . .        |
| Kay         | Caldwell     |
| Keith       | Rico         |
| ***Kelly*** | ***Torres*** |
| ***Kelly*** | ***Knott***  |
| Ken         | Prewitt      |
| . . .       | . . .        |


<br>

```sql
SELECT first_name, LENGTH(first_name) AS len
FROM customer
ORDER BY len;
```
| first_name |  len   |
|------------|--------|
| Jo         |      2 |
| Sam        |      3 |
| Roy        |      3 |
| Eva        |      3 |
| Don        |      3 |
| . . .      | . . .  |

<br>

#### NULLS FIRST, NULLS LAST

<br>

```sql
SELECT number
FROM some_table
ORDER BY number NULLS FIRST;
```
| number |
|--------|
| [null] |
|   1    |
|   2    |
|   3    |

<br>

### DISTINCT

<br>

<tt>
    <b>SELECT DISTINCT</b> column<br>
    <b>FROM</b> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;table<br>
</tt>

<br>
<br>

The following examples take the example `colors` table below into consideration:


| id |            background            |            foreground            |
|----|----------------------------------|----------------------------------|
|1   | <font color="red">red</font>     | <font color="red">red</font>     |
|2   | <font color="red">red</font>     | <font color="red">red</font>     |
|3   | <font color="red">red</font>     | <font color="gray">[null]</font> |
|4   | <font color="gray">[null]</font> | <font color="red">red</font>     |
|5   | <font color="red">red</font>     | <font color="green">green</font> |
|6   | <font color="red">red</font>     | <font color="blue">blue</font>   |
|7   | <font color="green">green</font> | <font color="red">red</font>     |
|8   | <font color="green">green</font> | <font color="blue">blue</font>   |
|9   | <font color="green">green</font> | <font color="green">green</font> |
|10  | <font color="blue">blue</font>   | <font color="red">red</font>     |
|11  | <font color="blue">blue</font>   | <font color="green">green</font> |
|12  | <font color="blue">blue</font>   | <font color="blue">blue</font>   |

<br>

#### DISTINCT single column

```sql
SELECT DISTINCT background
FROM colors
ORDER BY background;
```
| background |
|------------|
| <font color="blue">blue</font>   |
| <font color="red">red</font>     |
| <font color="green">green</font> |
| <font color="gray">[null]</font> |


<br>

#### DISTINCT multiple columns

Select the distinct `background, foreground` combinations:

```sql
SELECT DISTINCT background, foreground
FROM colors
ORDER BY background, foreground;
```
|            background            |            foreground            |
|----------------------------------|----------------------------------|
| <font color="blue">blue</font>   | <font color="blue">blue</font>   |
| <font color="blue">blue</font>   | <font color="green">green</font> |
| <font color="blue">blue</font>   | <font color="red">red</font>     |
| <font color="green">green</font> | <font color="blue">blue</font>   |
| <font color="green">green</font> | <font color="green">green</font> |
| <font color="green">green</font> | <font color="red">red</font>     |
| <font color="red">red</font>     | <font color="blue">blue</font>   |
| <font color="red">red</font>     | <font color="green">green</font> |
| <font color="red">red</font>     | <font color="red">red</font>     |
| <font color="red">red</font>     | <font color="gray">[null]</font> |
| <font color="gray">[null]</font> | <font color="red">red</font>     |

<br>

#### DISTINCT ON

```sql
SELECT DISTINCT ON (background) background, foreground
FROM colors;
```
|            background            |            foreground            |
|----------------------------------|----------------------------------|
| <font color="blue">blue</font>   | <font color="red">red</font>     |
| <font color="green">green</font> | <font color="red">red</font>     |
| <font color="red">red</font>     | <font color="red">red</font>     |
| <font color="gray">[null]</font> | <font color="red">red</font>     |

<br>

```sql
SELECT DISTINCT ON (background) background, foreground
FROM colors
ORDER BY background, foreground;
```
|            background            |            foreground            |
|----------------------------------|----------------------------------|
| <font color="blue">blue</font>   | <font color="blue">blue</font>   |
| <font color="green">green</font> | <font color="blue">blue</font>   |
| <font color="red">red</font>     | <font color="blue">blue</font>   |
| <font color="gray">[null]</font> | <font color="red">red</font>     |

<br>

In the example above the rows are put into groups unique by `background`, each group was sorted by `background, foreground`, and then the *first row* of each group was kept:

<table>
    <tr>
        <td>
            <table>
                <tr><th>background</th><th>foreground</th></tr>
                <tr><td><font color="red">red</font></td><td><font color="red">red</font></td></tr>
                <tr><td><font color="red">red</font></td><td><font color="gray">[null]</font></td></tr>
                <tr><td><font color="red">red</font></td><td><font color="green">green</font></td></tr>
                <tr><td><font color="red">red</font></td><td><font color="blue">blue</font></td></tr>
            </table>
        </td>
        <td>&nbsp;</td>
        <td>
            <table>
                <tr><th>background</th><th>foreground</th></tr>
                <tr><td><font color="red">red</font></td><td><font color="blue">blue</font></td></tr>
                <tr><td><font color="red">red</font></td><td><font color="green">green</font></td></tr>
                <tr><td><font color="red">red</font></td><td><font color="red">red</font></td></tr>
                <tr><td><font color="red">red</font></td><td><font color="gray">[null]</font></td></tr>
            </table>
        </td>
        <td>&nbsp;</td>
        <td>
            <table>
                <tr><th>background</th><th>foreground</th></tr>
                <tr><td><font color="red">red</font></td><td><font color="blue">blue</font></td></tr>
            </table>
        </td>
    </tr>
    <tr>
        <td>
            <table>
                <tr><th>background</th><th>foreground</th></tr>
                <tr><td><font color="green">green</font></td><td><font color="red">red</font></td></tr>
                <tr><td><font color="green">green</font></td><td><font color="blue">blue</font></td></tr>
                <tr><td><font color="green">green</font></td><td><font color="green">green</font></td></tr>
            </table>
        </td>
        <td>
            <img src="images/arrow.png">
        </td>
        <td>
            <table>
                <tr><th>background</th><th>foreground</th></tr>
                <tr><td><font color="green">green</font></td><td><font color="blue">blue</font></td></tr>
                <tr><td><font color="green">green</font></td><td><font color="green">green</font></td></tr>
                <tr><td><font color="green">green</font></td><td><font color="red">red</font></td></tr>
            </table>
        </td>
        <td>
            <img src="images/arrow.png">
        </td>
        <td>
            <table>
                <tr><th>background</th><th>foreground</th></tr>
                <tr><td><font color="green">green</font></td><td><font color="blue">blue</font></td></tr>
            </table>
        </td>
    </tr>
    <tr>
        <td>
            <table>
                <tr><th>background</th><th>foreground</th></tr>
                <tr><td><font color="blue">blue</font></td><td><font color="red">red</font></td></tr>
                <tr><td><font color="blue">blue</font></td><td><font color="green">green</font></td></tr>
                <tr><td><font color="blue">blue</font></td><td><font color="blue">blue</font></td></tr>
            </table>
        </td>
        <td>&nbsp;</td>
        <td>
            <table>
                <tr><th>background</th><th>foreground</th></tr>
                <tr><td><font color="blue">blue</font></td><td><font color="blue">blue</font></td></tr>
                <tr><td><font color="blue">blue</font></td><td><font color="green">green</font></td></tr>
                <tr><td><font color="blue">blue</font></td><td><font color="red">red</font></td></tr>
            </table>
        </td>
        <td>&nbsp;</td>
        <td>
            <table>
                <tr><th>background</th><th>foreground</th></tr>
                <tr><td><font color="blue">blue</font></td><td><font color="blue">blue</font></td></tr>
            </table>
        </td>
    </tr>
    <tr>
        <td>
            <table>
                <tr><th>background</th><th>foreground</th></tr>
                <tr><td><font color="gray">[null]</font></td><td><font color="red">red</font></td></tr>
            </table>
        </td>
        <td>&nbsp;</td>
        <td>
            <table>
                <tr><th>background</th><th>foreground</th></tr>
                <tr><td><font color="gray">[null]</font></td><td><font color="red">red</font></td></tr>
            </table>
        </td>
        <td>&nbsp;</td>
        <td>
            <table>
                <tr><th>background</th><th>foreground</th></tr>
                <tr><td><font color="gray">[null]</font></td><td><font color="red">red</font></td></tr>
            </table>
        </td>
    </tr>
</table>

<br>

It is a good practice to use `ORDER BY` with `DISTINCT ON (expression)` to make this order of results predictable.

<br>

```sql
SELECT DISTINCT ON (foreground) background, foreground
FROM colors;
```
|            background            |            foreground            |
|----------------------------------|----------------------------------|
| <font color="blue">blue</font>   | <font color="blue">blue</font>   |
| <font color="green">green</font> | <font color="green">green</font> |
| <font color="green">green</font> | <font color="red">red</font>     |
| <font color="red">red</font>     | <font color="gray">[null]</font> |

<br>

### WHERE

<tt>
    <b>SELECT</b> &nbsp;&nbsp;columns<br>
    <b>FROM</b> &nbsp;&nbsp;&nbsp;&nbsp;table<br>
    <b>WHERE</b> &nbsp;&nbsp;&nbsp;condition<br>
    <b>ORDER BY</b> sort_expression
</tt>

<br>
<br>

Order of evaluation:

```
╭──────────╮         ╭──────────╮         ╭──────────╮         ╭──────────╮
│   FROM   │  ────>  │  WHERE   │  ────>  │  SELECT  │  ────>  │ ORDER BY │
╰──────────╯         ╰──────────╯         ╰──────────╯         ╰──────────╯
```

The following examples refer to the `dvd rental` database once again:

#### Simple filter clause

```sql
SELECT last_name, first_name 
FROM customer
WHERE first_name = 'Jamie';
```
| last_name | first_name  |
|-----------|-------------|
| Rice      | Jamie       |
| Waugh     | Jamie       |

<br>

#### AND, OR

```sql
SELECT last_name, first_name 
FROM customer
WHERE first_name = 'Jamie' AND last_name = 'Rice';
```
| last_name | first_name  |
|-----------|-------------|
| Rice      | Jamie       |

<br>

```sql
SELECT first_name, last_name
FROM customer
WHERE last_name = 'Rodriguez' OR first_name = 'Adam';
```
| first_name  | last_name |
|-------------|-----------|
| Laura       | Rodriguez |
| Adam        | Gooch     |

<br>

#### IN

```sql
SELECT first_name, last_name
FROM customer
WHERE first_name IN ('Ann', 'Anne', 'Annie');
```
| first_name  | last_name |
|-------------|-----------|
| Ann         | Evans     |
| Anne        | Powell    |
| Annie       | Russell   |

<br>

PostgreSQL executes the `IN` operator *faster* than the equivalent query with `OR` operators.

```sql
SELECT first_name, last_name
FROM customer
WHERE first_name NOT IN ('Ann', 'Anne', 'Annie');
```
| first_name  | last_name |
|-------------|-----------|
| Jared       | Ely       |
| Mary        | Smith     |
| Patricia    | Johnson   |
| Linda       | Williams  |
| Barbara     | Jones     |
| . . .       | . . .     |

<br>

```sql
SELECT customer_id, first_name, last_name 
FROM customer
WHERE customer_id IN (
	SELECT customer_id
	FROM rental
	WHERE CAST (return_date AS DATE) = '2005-05-27'
)
ORDER BY customer_id;
```
| customer_id | first_name | last_name  |
|-------------|------------|------------|
|          37 | Pamela     | Baker      |
|          47 | Frances    | Parker     |
|          48 | Ann        | Evans      |
|          65 | Rose       | Howard     |
|          73 | Beverly    | Brooks     |
| . . .       | . . .      | . . .      |

<br>

#### LIKE

```sql
SELECT first_name, last_name
FROM customer
WHERE first_name LIKE 'Ann%';
```
| first_name  | last_name |
|-------------|-----------|
| Anna        | Hill      |
| Ann         | Evans     |
| Anne        | Powell    |
| Annie       | Russell   |
| Annette     | Olson     |

<br>

##### PostgreSQL ILIKE

```sql
SELECT first_name, last_name
FROM customer
WHERE first_name ILIKE 'aNN%';
```
| first_name  | last_name |
|-------------|-----------|
| Anna        | Hill      |
| Ann         | Evans     |
| Anne        | Powell    |
| Annie       | Russell   |
| Annette     | Olson     |

<br>

##### PostgreSQL operators

```sql
SELECT first_name, last_name
FROM customer
WHERE first_name ~~ 'Ann%';
```
| first_name  | last_name |
|-------------|-----------|
| Anna        | Hill      |
| Ann         | Evans     |
| Anne        | Powell    |
| Annie       | Russell   |
| Annette     | Olson     |

<br>

| Operator   | Equivalent      |
|------------|-----------------|
| **`~~`**   | **`LIKE`**      |
| **`~~*`**  | **`ILIKE`**     |
| **`!~~`**  | **`NOT LIKE`**  |
| **`!~~*`** | **`NOT ILIKE`** |

<br>

```sql
SELECT first_name, last_name
FROM customer
WHERE first_name LIKE 'Ann%' AND first_name != 'Anne';
```
| first_name  | last_name |
|-------------|-----------|
| Anna        | Hill      |
| Ann         | Evans     |
| Annie       | Russell   |
| Annette     | Olson     |

<br>

In the example above, `Ann%` is equivalent to the *`Ann`* followed by *`zero or more chars`*.

##### Wildcards

| Expression                 | Value   |
|----------------------------|---------|
| `'abcd'` **LIKE** `'abcd'` | `true`  |
| `'abcd'` **LIKE** `'ab%'`  | `true`  |
| `'abcd'` **LIKE** `'_bc_'` | `true`  |
| `'abcd'` **LIKE** `'_b%'`  | `true`  |
| `'abcd'` **LIKE** `'a_'`   | `false` |

<br>

#### BETWEEN

```sql
SELECT first_name, LENGTH(first_name) AS name_length
FROM customer
WHERE first_name LIKE 'A%' AND LENGTH(first_name) BETWEEN 3 AND 5
ORDER BY name_length;
```
| first_name  | name_length |
|-------------|-------------|
| Amy         |           3 |
| Ann         |           3 |
| Ana         |           3 |
| Andy        |           4 |
| Anna        |           4 |
| . . .       | . . .       |

<br>

```sql
SELECT customer_id, payment_id, amount, payment_date 
FROM payment
WHERE payment_date NOT BETWEEN '2007-02-07' AND '2007-02-15'
```
| customer_id | payment_id | amount |    payment_date     |
|-------------|------------|--------|---------------------|
|         341 |      17503 |   7.99 | 2007-02-15 22:25:46 |
|         341 |      17504 |   1.99 | 2007-02-16 17:23:14 |
|         341 |      17505 |   7.99 | 2007-02-16 22:41:45 |
|         341 |      17506 |   2.99 | 2007-02-19 19:39:56 |
|         341 |      17507 |   7.99 | 2007-02-20 17:31:48 |
| . . .       | . . .      | . . .  | . . .               |

<br>

#### Clause-building operators

| Operator             | Description                        |
|----------------------|------------------------------------|
| **`=`**              | Equal                              |
| **`>`**              | Greater than                       |
| **`<`**              | Less than                          |
| **`>=`**             | Greater than or equal              |
| **`<=`**             | Less than or equal                 |
| **`<>`** or **`!=`** | Not equal                          |
| **`AND`**            | Logical `AND`                      |
| **`OR`**             | Logical `OR`                       |
| **`IN`**             | `true` if value is in a list       |
| **`BETWEEN`**        | `true` if value is between a range |
| **`LIKE`**           | `true` if value matches a pattern  |
| **`IS NULL`**        | `true` if value is `NULL`          |
| **`NOT`**            | Logical `NOT`                      |

<br>

### LIMIT

<tt>
    <b>SELECT</b> &nbsp;&nbsp;columns<br>
    <b>FROM</b> &nbsp;&nbsp;&nbsp;&nbsp;table<br>
    <b>ORDER BY</b> sort_expression<br>
    <b>LIMIT</b> &nbsp;&nbsp;&nbsp;row_count
</tt>

<br>
<br>

```sql
SELECT film_id, title, release_year 
FROM film
ORDER BY film_id 
LIMIT 5;
```
| film_id |      title       | release_year  |
|---------|------------------|---------------|
|       1 | Academy Dinosaur |          2006 |
|       2 | Ace Goldfinger   |          2006 |
|       3 | Adaptation Holes |          2006 |
|       4 | Affair Prejudice |          2006 |
|       5 | African Egg      |          2006 |

<br>

We should always use `LIMIT` together with `ORDER BY`, as *tables store rows in an unspecified order*.

<br>

#### OFFSET

```sql
SELECT film_id, title, release_year 
FROM film
ORDER BY film_id 
LIMIT 5 OFFSET 3;
```
| film_id |      title       | release_year  |
|---------|------------------|---------------|
|       4 | Affair Prejudice |          2006 |
|       5 | African Egg      |          2006 |
|       6 | Agent Truman     |          2006 |
|       7 | Airplane Sierra  |          2006 |
|       8 | Airport Pollock  |          2006 |

<br>

#### Top/bottom rows

```sql
SELECT film_id, title, rental_rate 
FROM film
ORDER BY rental_rate DESC
LIMIT 10;
```
| film_id |        title        | rental_rate |
|---------|---------------------|-------------|
|      13 | Ali Forever         |        4.99 |
|      20 | Amelie Hellfighters |        4.99 |
|       7 | Airplane Sierra     |        4.99 |
|      10 | Aladdin Calendar    |        4.99 |
|       2 | Ace Goldfinger      |        4.99 |
|       8 | Airport Pollock     |        4.99 |
|      98 | Bright Encounters   |        4.99 |
|     133 | Chamber Italian     |        4.99 |
|     384 | Grosse Wonderful    |        4.99 |
|      21 | American Circus     |        4.99 |

<br>

### FETCH

Altough [LIMIT](#limit) is used by PostgreSQL, MySQL, H2, etc., it is not a SQL standard.

The `FETCH` clase is equivalent and conforms to SQL standards:

<tt>
    <b>SELECT</b> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;columns<br>
    <b>FROM</b> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;table<br>
    <b>ORDER BY</b> &nbsp;&nbsp;&nbsp;sort_expression<br>
    <b>OFFSET</b> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;row_count <b>ROWS</b><br>
    <b>FETCH FIRST</b> row_count <b>ROWS ONLY</b>;
</tt>

<br>
<br>

```sql
SELECT film_id, title 
FROM film
ORDER BY title
OFFSET 3 ROWS
FETCH FIRST 5 ROWS ONLY;
```
| film_id |      title       |
|---------|------------------|
|       4 | Affair Prejudice |
|       5 | African Egg      |
|       6 | Agent Truman     |
|       7 | Airplane Sierra  |
|       8 | Airport Pollock  |

<br>

Again, `FETCH` should always be used together with `ORDER BY`, as *rows are stored in an unpredictable order*.

<br>

### JOIN

Joins are used to *combine tables* based on *common columns*.

The common columns are usually the *primary key* of the *first* table and a *foreign key* in the *second* table.

The join examples below are based on the following `color_a` and `color_b` tables:

<div>
    <table>
        <tr>
            <td>
                <h3 align="center">color_a</h3>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="red">red</font></td></tr>
                    <tr><td>2</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>3</td><td><font color="gold">yellow</font></td></tr>
                    <tr><td>4</td><td><font color="limegreen">green</font></td></tr>
                </table>
            </td>
            <td>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
            </td>
            <td>
                <h3 align="center">color_b</h3>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>2</td><td><font color="red">red</font></td></tr>
                    <tr><td>3</td><td><font color="dodgerblue">blue</font></td></tr>
                    <tr><td>4</td><td><font color="darkmagenta">purple</font></td></tr>
                </table>
            </td>
        </tr>
    </table>
</div>

<br>

#### INNER JOIN

<div>
    <table>
        <tr>
            <td>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="red">red</font></td></tr>
                    <tr><td>2</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>3</td><td><font color="gold">yellow</font></td></tr>
                    <tr><td>4</td><td><font color="limegreen">green</font></td></tr>
                </table>
            </td>
            <td>
                &nbsp;
            </td>
            <td>
                <img src="images/inner_join.png">
            </td>
            <td>
                &nbsp;
            </td>
            <td>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>2</td><td><font color="red">red</font></td></tr>
                    <tr><td>3</td><td><font color="dodgerblue">blue</font></td></tr>
                    <tr><td>4</td><td><font color="darkmagenta">purple</font></td></tr>
                </table>
            </td>
        </tr>
    </table>
</div>


<br>

```sql
SELECT *
FROM color_a INNER JOIN color_b
ON color_a.color = color_b.color;
```

| id   | color                                  | id   | color                                  |
|------|----------------------------------------|------|----------------------------------------|
| 1    | <font color="red">red</font>           | 2    | <font color="red">red</font>           |
| 2    | <font color="darkorange">orange</font> | 1    | <font color="darkorange">orange</font> |

<br>
<br>

```sql
SELECT *
FROM color_b INNER JOIN color_a
ON color_a.color = color_b.color;
```

| id   | color                                  | id   | color                                  |
|------|----------------------------------------|------|----------------------------------------|
| 1    | <font color="darkorange">orange</font> | 2    | <font color="darkorange">orange</font> |
| 2    | <font color="red">red</font>           | 1    | <font color="red">red</font>           |

<br>

##### Table alias

```sql
SELECT A.color
FROM color_a A INNER JOIN color_b B
ON A.color = B.color
ORDER BY A.color;
```
| color                                  |
|----------------------------------------|
| <font color="red">red</font>           |
| <font color="darkorange">orange</font> |

<br>

##### USING

```sql
SELECT color
FROM color_a INNER JOIN color_b
USING(color);
```
| color                                  |
|----------------------------------------|
| <font color="red">red</font>           |
| <font color="darkorange">orange</font> |

<br>

##### INNER JOIN with 3 tables

The `dvd rental` brings the following table relationships:

```
┌───────────────┐                                  ┌───────────────┐
│     staff     │                                  │   customer    │
├───────────────┤        ┌────────────────┐        ├───────────────┤
│ * staff_id    │        │    payment     │        │ * customer_id │
│   first_name  │        ├────────────────┤        │   store_id    │
│   last_name   │        │ * payment_id   │        │   first_name  │
│   address_id  │        │   customer_id  │        │   last_name   │
│   email       ├───────<│   staff_id     │>───────┤   email       │
│   store_id    │        │   rental_id    │        │   address_id  │
│   active      │        │   amount       │        │   activebool  │
│   username    │        │   payment_date │        │   create_date │
│   password    │        └────────────────┘        │   last_update │
│   last_update │                                  │   active      │
│   picture     │                                  └───────────────┘
└───────────────┘                                                   
```

<br>

 - Each `customer` makes zero or more `payment`s
 - Each `payment` is made by only one `customer`
 
 <br>

 - Each `staff` member handles zero or more `payment`s
 - Each `payment` is handled by one `staff` member

<br>

If we want to list all `payment`s, with the corresponding `customer` and `staff` member, we can *join 3 tables*:

```sql
SELECT
	payment.payment_id,
	customer.first_name || ' ' || customer.last_name AS customer_name,
	staff.first_name    || ' ' || staff.last_name    AS staff_member
FROM
	customer
	INNER JOIN payment ON customer.customer_id = payment.customer_id
	INNER JOIN staff   ON payment.staff_id     = staff.staff_id;
```
| payment_id | customer_name  | staff_member |
|------------|----------------|--------------|
|      17503 | Peter Menard   | Jon Stephens |
|      17504 | Peter Menard   | Mike Hillyer |
|      17505 | Peter Menard   | Mike Hillyer |
|      17506 | Peter Menard   | Jon Stephens |
|      17507 | Peter Menard   | Jon Stephens |
|      17508 | Peter Menard   | Mike Hillyer |
|      17509 | Harold Martino | Jon Stephens |
|      17510 | Harold Martino | Mike Hillyer |
|      17511 | Harold Martino | Mike Hillyer |
|      17512 | Douglas Graf   | Jon Stephens |
|      17513 | Douglas Graf   | Mike Hillyer |
| . . .      | . . .          | . . .        |

<br>

#### LEFT OUTER JOIN

<div>
    <table>
        <tr>
            <td>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="red">red</font></td></tr>
                    <tr><td>2</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>3</td><td><font color="gold">yellow</font></td></tr>
                    <tr><td>4</td><td><font color="limegreen">green</font></td></tr>
                </table>
            </td>
            <td>
                &nbsp;
            </td>
            <td>
                <img src="images/left_join.png">
            </td>
            <td>
                &nbsp;
            </td>
            <td>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>2</td><td><font color="red">red</font></td></tr>
                    <tr><td>3</td><td><font color="dodgerblue">blue</font></td></tr>
                    <tr><td>4</td><td><font color="darkmagenta">purple</font></td></tr>
                </table>
            </td>
        </tr>
    </table>
</div>


<br>

```sql
SELECT *
FROM color_a LEFT JOIN color_b
ON color_a.color = color_b.color;
```

| id   | color                                   | id                               | color                                  |
|------|-----------------------------------------|----------------------------------|----------------------------------------|
| 1    | <font color="red">red</font>            | 2                                | <font color="red">red</font>           |
| 2    | <font color="darkorange">orange</font>  | 1                                | <font color="darkorange">orange</font> |
| 3    | <font color="gold">yellow</font>        | <font color="gray">[null]</font> | <font color="gray">[null]</font>       |
| 4    | <font color="limegreen">green</font>    | <font color="gray">[null]</font> | <font color="gray">[null]</font>       |

<br>

#### LEFT OUTER JOIN - only left table rows

<div>
    <table>
        <tr>
            <td>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="red">red</font></td></tr>
                    <tr><td>2</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>3</td><td><font color="gold">yellow</font></td></tr>
                    <tr><td>4</td><td><font color="limegreen">green</font></td></tr>
                </table>
            </td>
            <td>
                &nbsp;
            </td>
            <td>
                <img src="images/left_join_left_only.png">
            </td>
            <td>
                &nbsp;
            </td>
            <td>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>2</td><td><font color="red">red</font></td></tr>
                    <tr><td>3</td><td><font color="dodgerblue">blue</font></td></tr>
                    <tr><td>4</td><td><font color="darkmagenta">purple</font></td></tr>
                </table>
            </td>
        </tr>
    </table>
</div>


<br>

```sql
SELECT *
FROM color_a LEFT JOIN color_b
ON color_a.color = color_b.color
WHERE color_b.id IS NULL;
```

| id   | color                                   | id                               | color                            |
|------|-----------------------------------------|----------------------------------|----------------------------------|
| 3    | <font color="gold">yellow</font>        | <font color="gray">[null]</font> | <font color="gray">[null]</font> |
| 4    | <font color="limegreen">green</font>    | <font color="gray">[null]</font> | <font color="gray">[null]</font> |

<br>

#### RIGHT OUTER JOIN

<div>
    <table>
        <tr>
            <td>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="red">red</font></td></tr>
                    <tr><td>2</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>3</td><td><font color="gold">yellow</font></td></tr>
                    <tr><td>4</td><td><font color="limegreen">green</font></td></tr>
                </table>
            </td>
            <td>
                &nbsp;
            </td>
            <td>
                <img src="images/right_join.png">
            </td>
            <td>
                &nbsp;
            </td>
            <td>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>2</td><td><font color="red">red</font></td></tr>
                    <tr><td>3</td><td><font color="dodgerblue">blue</font></td></tr>
                    <tr><td>4</td><td><font color="darkmagenta">purple</font></td></tr>
                </table>
            </td>
        </tr>
    </table>
</div>


<br>

```sql
SELECT *
FROM color_a RIGHT JOIN color_b
ON color_a.color = color_b.color;
```

| id                               | color                                  | id   | color                                      |
|----------------------------------|----------------------------------------|------|--------------------------------------------|
| 2                                | <font color="darkorange">orange</font> | 1    | <font color="darkorange">orange</font>     |
| 1                                | <font color="red">red</font>           | 2    | <font color="red">red</font>               |
| <font color="gray">[null]</font> | <font color="gray">[null]</font>       | 3    | <font color="dodgerblue">blue</font>       |
| <font color="gray">[null]</font> | <font color="gray">[null]</font>       | 4    | <font color="darkmagenta">purple</font>    |

<br>

#### RIGHT OUTER JOIN - only right table rows

<div>
    <table>
        <tr>
            <td>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="red">red</font></td></tr>
                    <tr><td>2</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>3</td><td><font color="gold">yellow</font></td></tr>
                    <tr><td>4</td><td><font color="limegreen">green</font></td></tr>
                </table>
            </td>
            <td>
                &nbsp;
            </td>
            <td>
                <img src="images/right_join_right_only.png">
            </td>
            <td>
                &nbsp;
            </td>
            <td>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>2</td><td><font color="red">red</font></td></tr>
                    <tr><td>3</td><td><font color="dodgerblue">blue</font></td></tr>
                    <tr><td>4</td><td><font color="darkmagenta">purple</font></td></tr>
                </table>
            </td>
        </tr>
    </table>
</div>


<br>

```sql
SELECT *
FROM color_a RIGHT JOIN color_b
ON color_a.color = color_b.color
WHERE color_a.id IS NULL;
```

| id                               | color                                  | id   | color                                      |
|----------------------------------|----------------------------------------|------|--------------------------------------------|
| <font color="gray">[null]</font> | <font color="gray">[null]</font>       | 3    | <font color="dodgerblue">blue</font>       |
| <font color="gray">[null]</font> | <font color="gray">[null]</font>       | 4    | <font color="darkmagenta">purple</font>    |

<br>

#### FULL OUTER JOIN

<div>
    <table>
        <tr>
            <td>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="red">red</font></td></tr>
                    <tr><td>2</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>3</td><td><font color="gold">yellow</font></td></tr>
                    <tr><td>4</td><td><font color="limegreen">green</font></td></tr>
                </table>
            </td>
            <td>
                &nbsp;
            </td>
            <td>
                <img src="images/full_join.png">
            </td>
            <td>
                &nbsp;
            </td>
            <td>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>2</td><td><font color="red">red</font></td></tr>
                    <tr><td>3</td><td><font color="dodgerblue">blue</font></td></tr>
                    <tr><td>4</td><td><font color="darkmagenta">purple</font></td></tr>
                </table>
            </td>
        </tr>
    </table>
</div>


<br>

```sql
SELECT *
FROM color_a FULL JOIN color_b
ON color_a.color = color_b.color;
```

| id                               | color                                   | id                               | color                                      |
|----------------------------------|-----------------------------------------|----------------------------------|--------------------------------------------|
| 1                                | <font color="red">red</font>            | 2                                | <font color="red">red</font>               |
| 2                                | <font color="darkorange">orange</font>  | 1                                | <font color="darkorange">orange</font>     |
| 3                                | <font color="gold">yellow</font>        | <font color="gray">[null]</font> | <font color="gray">[null]</font>           |
| 4                                | <font color="limegreen">green</font>    | <font color="gray">[null]</font> | <font color="gray">[null]</font>           |
| <font color="gray">[null]</font> | <font color="gray">[null]</font>        | 3                                | <font color="dodgerblue">blue</font>       |
| <font color="gray">[null]</font> | <font color="gray">[null]</font>        | 4                                | <font color="darkmagenta">purple</font>    |

<br>

#### FULL OUTER JOIN - only rows unique to both tables

<div>
    <table>
        <tr>
            <td>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="red">red</font></td></tr>
                    <tr><td>2</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>3</td><td><font color="gold">yellow</font></td></tr>
                    <tr><td>4</td><td><font color="limegreen">green</font></td></tr>
                </table>
            </td>
            <td>
                &nbsp;
            </td>
            <td>
                <img src="images/full_join_unique.png">
            </td>
            <td>
                &nbsp;
            </td>
            <td>
                <table>
                    <tr><th>id</th><th>color</th></tr>
                    <tr><td>1</td><td><font color="darkorange">orange</font></td></tr>
                    <tr><td>2</td><td><font color="red">red</font></td></tr>
                    <tr><td>3</td><td><font color="dodgerblue">blue</font></td></tr>
                    <tr><td>4</td><td><font color="darkmagenta">purple</font></td></tr>
                </table>
            </td>
        </tr>
    </table>
</div>


<br>

```sql
SELECT *
FROM color_a FULL JOIN color_b
ON color_a.color = color_b.color
WHERE color_a.id IS NULL OR color_b.id IS NULL;
```

| id                               | color                                   | id                               | color                                      |
|----------------------------------|-----------------------------------------|----------------------------------|--------------------------------------------|
| 3                                | <font color="gold">yellow</font>        | <font color="gray">[null]</font> | <font color="gray">[null]</font>           |
| 4                                | <font color="limegreen">green</font>    | <font color="gray">[null]</font> | <font color="gray">[null]</font>           |
| <font color="gray">[null]</font> | <font color="gray">[null]</font>        | 3                                | <font color="dodgerblue">blue</font>       |
| <font color="gray">[null]</font> | <font color="gray">[null]</font>        | 4                                | <font color="darkmagenta">purple</font>    |

<br>

#### Self join

```sql
SELECT a1.color AS "Color 1", a2.color AS "Color 2" 
FROM color_a a1 INNER JOIN color_a a2
ON a1.color = a2.color;
```

| Color 1                                 | Color 2                                 |
|-----------------------------------------|-----------------------------------------|
| <font color="red">red</font>            | <font color="red">red</font>            |
| <font color="darkorange">orange</font>  | <font color="darkorange">orange</font>  |
| <font color="gold">yellow</font>        | <font color="gold">yellow</font>        |
| <font color="limegreen">green</font>    | <font color="limegreen">green</font>    |

<br>

#### Summary

<table>
    <tr>
        <td style="padding: 20px;">
            <img src="images/inner_join.png">
        </td>
        <td>
            <tt>
                <b><font color="dodgerblue">SELECT</font></b> * <br>
                <b><font color="dodgerblue">FROM</font></b> a <b><font color="dodgerblue">INNER JOIN</font></b> b <br>
                <b><font color="dodgerblue">ON</font></b> a.key = b.key;
            </tt>
        </td>
    </tr>
    <tr>
        <td style="padding: 20px;">
            <img src="images/left_join.png">
        </td>
        <td>
            <tt>
                <b><font color="dodgerblue">SELECT</font></b> * <br>
                <b><font color="dodgerblue">FROM</font></b> a <b><font color="dodgerblue">LEFT JOIN</font></b> b <br>
                <b><font color="dodgerblue">ON</font></b> a.key = b.key;
            </tt>
        </td>
    </tr>
    <tr>
        <td style="padding: 20px;">
            <img src="images/left_join_left_only.png">
        </td>
        <td>
            <tt>
                <b><font color="dodgerblue">SELECT</font></b> * <br>
                <b><font color="dodgerblue">FROM</font></b> a <b><font color="dodgerblue">LEFT JOIN</font></b> b <br>
                <b><font color="dodgerblue">ON</font></b> a.key = b.key<br>
                <b><font color="dodgerblue">WHERE</font></b> b.key <b><font color="dodgerblue">IS NULL</font></b>;
            </tt>
        </td>
    </tr>
    <tr>
        <td style="padding: 20px;">
            <img src="images/right_join.png">
        </td>
        <td>
            <tt>
                <b><font color="dodgerblue">SELECT</font></b> * <br>
                <b><font color="dodgerblue">FROM</font></b> a <b><font color="dodgerblue">RIGHT JOIN</font></b> b <br>
                <b><font color="dodgerblue">ON</font></b> a.key = b.key;
            </tt>
        </td>
    </tr>
    <tr>
        <td style="padding: 20px;">
            <img src="images/right_join_right_only.png">
        </td>
        <td>
            <tt>
                <b><font color="dodgerblue">SELECT</font></b> * <br>
                <b><font color="dodgerblue">FROM</font></b> a <b><font color="dodgerblue">RIGHT JOIN</font></b> b <br>
                <b><font color="dodgerblue">ON</font></b> a.key = b.key<br>
                <b><font color="dodgerblue">WHERE</font></b> a.key <b><font color="dodgerblue">IS NULL</font></b>;
            </tt>
        </td>
    </tr>
    <tr>
        <td style="padding: 20px;">
            <img src="images/full_join.png">
        </td>
        <td>
            <tt>
                <b><font color="dodgerblue">SELECT</font></b> * <br>
                <b><font color="dodgerblue">FROM</font></b> a <b><font color="dodgerblue">FULL JOIN</font></b> b <br>
                <b><font color="dodgerblue">ON</font></b> a.key = b.key;
            </tt>
        </td>
    </tr>
    <tr>
        <td style="padding: 20px;">
            <img src="images/full_join_unique.png">
        </td>
        <td>
            <tt>
                <b><font color="dodgerblue">SELECT</font></b> * <br>
                <b><font color="dodgerblue">FROM</font></b> a <b><font color="dodgerblue">FULL JOIN</font></b> b <br>
                <b><font color="dodgerblue">ON</font></b> a.key = b.key<br>
                <b><font color="dodgerblue">WHERE</font></b> a.key <b><font color="dodgerblue">IS NULL</font></b>
                <b><font color="dodgerblue">OR</font></b> b.key <b><font color="dodgerblue">IS NULL</font></b>;
            </tt>
        </td>
    </tr>
</table>

```sql
```
|     |     |
|-----|-----|
|     |     |

<br>

```sql
```
|     |     |
|-----|-----|
|     |     |

<br>

```sql
```
|     |     |
|-----|-----|
|     |     |

<br>

```sql
```
|     |     |
|-----|-----|
|     |     |

<br>

```sql
```
|     |     |
|-----|-----|
|     |     |

<br>

```sql
```
|     |     |
|-----|-----|
|     |     |

<br>

```sql
```
|     |     |
|-----|-----|
|     |     |

<br>

```sql
```
|     |     |
|-----|-----|
|     |     |

<br>

```sql
```
|     |     |
|-----|-----|
|     |     |

<br>

```sql
```
|     |     |
|-----|-----|
|     |     |

<br>


## PostgreSQL CLI commands

| Command | Action |
| ---- | ---- |
| `\c <database>` | Connect to a database |
| `\password`     | Set user password     |
| `\q`      | Quit |

# References

 - [PostgreSQL Tutorial](https://www.postgresqltutorial.com/)
 - [Geeky Tidbits: SELECT DISTINCT ON in PostgreSQL](https://www.geekytidbits.com/postgres-distinct-on/)