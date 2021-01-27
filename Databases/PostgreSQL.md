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
    + [GROUP BY](#group-by)
      - [Aggregate functions](#aggregate-functions)
      - [HAVING](#having)
    + [Combining result sets](#combining-result-sets)
      - [UNION](#union)
      - [INTERSECT](#intersect)
      - [EXCEPT](#except)
      - [GROUPING SETS](#grouping-sets)
      - [GROUPING](#grouping)
      - [CUBE](#cube)
      - [ROLLUP](#rollup)
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
      - [CROSS JOIN](#cross-join)
      - [NATURAL JOIN](#natural-join)
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

```sql
SELECT columns
FROM   table
```
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

```sql
SELECT   columns
FROM     table
ORDER BY sort_expression
```

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

```sql
SELECT DISTINCT column
FROM            table
```

<br>

The following examples take the example `colors` table below into consideration:

![](images/table1.png)

<br>

#### DISTINCT single column

```sql
SELECT DISTINCT background
FROM colors
ORDER BY background;
```
![](images/table2.png)

<br>

#### DISTINCT multiple columns

Select the distinct `background, foreground` combinations:

```sql
SELECT DISTINCT background, foreground
FROM colors
ORDER BY background, foreground;
```
![](images/table3.png)

<br>

#### DISTINCT ON

```sql
SELECT DISTINCT ON (background) background, foreground
FROM colors;
```
![](images/table4.png)

<br>

```sql
SELECT DISTINCT ON (background) background, foreground
FROM colors
ORDER BY background, foreground;
```
![](images/table5.png)

<br>

In the example above the rows are put into groups unique by `background`, each group was sorted by `background, foreground`, and then the *first row* of each group was kept:

![](images/table6.png)

<br>

It is a good practice to use `ORDER BY` with `DISTINCT ON (expression)` to make this order of results predictable.

<br>

```sql
SELECT DISTINCT ON (foreground) background, foreground
FROM colors;
```
![](images/table7.png)

<br>

### WHERE

```sql
SELECT   columns
FROM     table
WHERE    condition
ORDER BY sort_expression
```

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

```sql
SELECT   columns
FROM     table
ORDER BY sort_expression
LIMIT    row_count
```

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

```sql
SELECT      columns
FROM        table
ORDER BY    sort_expression
OFFSET      row_count ROWS
FETCH FIRST row_count ROWS ONLY;
```

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

### GROUP BY

```sql
SELECT   columns
FROM     table
GROUP BY columns
```

<br>

Order of evaluation:

```
  ┌──────────┐
  │   FROM   │
  └──────────┘
       ↓
  ┌──────────┐
  │  WHERE   │
  └──────────┘
       ↓
  ╔══════════╗
  ║ GROUP BY ║
  ╚══════════╝
       ↓
  ┌──────────┐
  │  HAVING  │
  └──────────┘
       ↓
  ┌──────────┐
  │  SELECT  │
  └──────────┘
       ↓
  ┌──────────┐
  │ DISTINCT │
  └──────────┘
       ↓
  ┌──────────┐
  │ ORDER BY │
  └──────────┘
       ↓
  ┌──────────┐
  │  LIMIT   │
  └──────────┘
```

<br>

```sql
SELECT customer_id 
FROM payment
GROUP BY customer_id
```
| customer_id |
|-------------|
|         184 |
|          87 |
|         477 |
|         273 |
|         550 |
|. . .        |

<br>

The qeury above works like the `DISINCT` clause, as it removes any duplicate `customer_id`s.

#### Aggregate functions

```sql
SELECT customer_id, SUM(amount) 
FROM payment
GROUP BY customer_id;
```
| customer_id |  sum   |
|-------------|--------|
|         184 |  80.80 |
|          87 | 137.72 |
|         477 | 106.79 |
|         273 | 130.72 |
|         550 | 151.69 |
|. . .        |. . .   |

<br>

```sql
SELECT customer_id, SUM(amount) 
FROM payment
GROUP BY customer_id
ORDER BY SUM(amount) DESC;
```
| customer_id |  sum   |
|-------------|--------|
|         148 | 211.55 |
|         526 | 208.58 |
|         178 | 194.61 |
|         137 | 191.62 |
|         144 | 189.60 |
|. . .        |. . .   |


<br>

```sql
SELECT
	first_name || ' ' || last_name AS full_name,
	SUM(amount) AS total_spent

FROM payment INNER JOIN customer
USING (customer_id)

GROUP BY full_name

ORDER BY total_spent DESC;
```
|   full_name    | total_spent |
|----------------|-------------|
| Eleanor Hunt   |      211.55 |
| Karl Seal      |      208.58 |
| Marion Snyder  |      194.61 |
| Rhonda Kennedy |      191.62 |
| Clara Shaw     |      189.60 |
|. . .           |. . .        |

<br>

For info on *joins* see the [JOIN](#join) section.

<br>

```sql
SELECT staff_id, COUNT(payment_id) 
FROM payment
GROUP BY staff_id 
```
| staff_id | count |
|----------|-------|
|        1 |  7292 |
|        2 |  7304 |

<br>

```sql
SELECT customer_id, staff_id, SUM(amount) AS total_spent 
FROM payment
GROUP BY staff_id, customer_id
ORDER BY customer_id;
```
| customer_id | staff_id | total_spent |
|-------------|----------|-------------|
|           1 |        2 |       53.85 |
|           1 |        1 |       60.85 |
|           2 |        2 |       67.88 |
|           2 |        1 |       55.86 |
|           3 |        1 |       59.88 |
|           3 |        2 |       70.88 |
|           4 |        2 |       31.90 |
|           4 |        1 |       49.88 |
| . . .       | . . .    | . . .       |

<br>

In the query above, `total_spent` is calculated for every <u>*`(customer_id, staff_id)`</u>* pair.

<br>

```sql
SELECT DATE(payment_date), sum(amount) 
FROM payment
GROUP BY DATE(payment_date)
ORDER BY DATE(payment_date);
```
|    date    |   sum   |
|------------|---------|
| 2007-02-14 |  116.73 |
| 2007-02-15 | 1188.92 |
| 2007-02-16 | 1154.18 |
| 2007-02-17 | 1188.17 |
| 2007-02-18 | 1275.98 |
| . . .      | . . .   |

#### HAVING

```sql
SELECT   columns
FROM     table
GROUP BY columns
HAVING   conditions
```

<br>

Order of evaluation:

```
  ┌──────────┐
  │   FROM   │
  └──────────┘
       ↓
  ┌──────────┐
  │  WHERE   │
  └──────────┘
       ↓
  ┌──────────┐
  │ GROUP BY │
  └──────────┘
       ↓
  ╔══════════╗
  ║  HAVING  ║
  ╚══════════╝
       ↓
  ┌──────────┐
  │  SELECT  │
  └──────────┘
       ↓
  ┌──────────┐
  │ DISTINCT │
  └──────────┘
       ↓
  ┌──────────┐
  │ ORDER BY │
  └──────────┘
       ↓
  ┌──────────┐
  │  LIMIT   │
  └──────────┘
```

<br>

While [`WHERE`](#where) allows us to filter *<u>rows</u>*, `HAVING` allows us to filter *<u>groups of rows</u>*:

```sql
SELECT customer_id, SUM(amount)
FROM payment
GROUP BY customer_id
HAVING SUM(amount) >= 200;
```
| customer_id |  sum   |
|-------------|--------|
|         526 | 208.58 |
|         148 | 211.55 |

<br>

The query above calculates the total amount each customer has spent and ignores the amounts smaller than $200.

<br>

```sql
SELECT store_id, count(customer_id) 
FROM customer
GROUP BY store_id
HAVING count(customer_id) > 300; 
```

<br>

The query above selects the store with more than 300 customers.

### Combining result sets

#### UNION

Taking the two tables below as an example:

```
               top_rated_films                            most_popular_films
┌──────────────────────────┬────────────────┐    ┌────────────────────┬──────────────┐
│          title           │  release_year  │    │       title        │ release_year │
├──────────────────────────┼────────────────┤    ├────────────────────┼──────────────┤
│ The Shawshank Redemption │           1994 │    │ An American Pickle │         2020 │
│ The Godfather            │           1972 │    │ The Godfather      │         1972 │
│ "12 Angry Men"           │           1957 │    │ Greyhound          │         2020 │
└──────────────────────────┴────────────────┘    └────────────────────┴──────────────┘
```

<br>

We can combine the results of both tables, if the *<u>number and order</u>* of the columns are the same and the *<u>data types</u>* are compatible:

![](images/full_join.png)
```sql
SELECT *
FROM top_rated_films

UNION

SELECT *
FROM most_popular_films;
```
|          title           | release_year |
|--------------------------|--------------|
| An American Pickle       |         2020 |
| Greyhound                |         2020 |
| The Shawshank Redemption |         1994 |
| The Godfather            |         1972 |
| 12 Angry Men             |         1957 |

<br>

To keep duplicates:

```sql
SELECT *
FROM top_rated_films

UNION ALL

SELECT *
FROM most_popular_films;
```
|          title           | release_year |
|--------------------------|--------------|
| The Shawshank Redemption |         1994 |
| The Godfather            |         1972 |
| 12 Angry Men             |         1957 |
| An American Pickle       |         2020 |
| The Godfather            |         1972 |
| Greyhound                |         2020 |

<br>

```sql
SELECT *
FROM top_rated_films

UNION ALL

SELECT *
FROM most_popular_films

ORDER BY title;
```
|          title           | release_year |
|--------------------------|--------------|
| 12 Angry Men             |         1957 |
| An American Pickle       |         2020 |
| Greyhound                |         2020 |
| The Godfather            |         1972 |
| The Godfather            |         1972 |
| The Shawshank Redemption |         1994 |

<br>

#### INTERSECT

Taking the `top_rated_films` and `most_popular_films` tables as an example once again:

```
               top_rated_films                            most_popular_films
┌──────────────────────────┬────────────────┐    ┌────────────────────┬──────────────┐
│          title           │  release_year  │    │       title        │ release_year │
├──────────────────────────┼────────────────┤    ├────────────────────┼──────────────┤
│ The Shawshank Redemption │           1994 │    │ An American Pickle │         2020 │
│ The Godfather            │           1972 │    │ The Godfather      │         1972 │
│ "12 Angry Men"           │           1957 │    │ Greyhound          │         2020 │
└──────────────────────────┴────────────────┘    └────────────────────┴──────────────┘
```

<br>

We can once combine the results of [`SELECT`](#select) statements (if the *<u>number and order</u>* of the columns are the same and the *<u>data types</u>* are compatible) and *keep only the rows available in both* result sets:

![](images/inner_join.png)
```sql
SELECT *
FROM top_rated_films

INTERSECT

SELECT *
FROM most_popular_films;
```
|          title           | release_year |
|--------------------------|--------------|
| The Godfather            |         1972 |

#### EXCEPT

Just as the previous [`UNION`](#union) and [`INTERSECT`](#intersect) clauses, we can also use `EXCEPT` to combine [`SELECT`](#select) statements if the *<u>number and order</u>* of the columns are the same and the *<u>data types</u>* are compatible.

`EXCEPT` keeps only distinct rows that are present in the first query and not present in the second one.

Using the same film tables once again:

```
               top_rated_films                            most_popular_films
┌──────────────────────────┬────────────────┐    ┌────────────────────┬──────────────┐
│          title           │  release_year  │    │       title        │ release_year │
├──────────────────────────┼────────────────┤    ├────────────────────┼──────────────┤
│ The Shawshank Redemption │           1994 │    │ An American Pickle │         2020 │
│ The Godfather            │           1972 │    │ The Godfather      │         1972 │
│ "12 Angry Men"           │           1957 │    │ Greyhound          │         2020 │
└──────────────────────────┴────────────────┘    └────────────────────┴──────────────┘
```

<br>

We can combine them using `EXCEPT`:

![](images/left_join_left_only.png)
```sql
SELECT *
FROM top_rated_films

EXCEPT

SELECT *
FROM most_popular_films

ORDER BY title;
```
|          title           | release_year |
|--------------------------|--------------|
| 12 Angry Men             |         1957 |
| The Shawshank Redemption |         1994 |

<br>

#### GROUPING SETS

For this section we will take the following `sales` table as ane example:

| brand | segment | quantity |
|-------|---------|----------|
| ABC   | Premium |      100 |
| ABC   | Basic   |      200 |
| XYZ   | Premium |      100 |
| XYZ   | Basic   |      300 |


<br>

The queries shown below correspond to:

 - Products sold by `brand, segment` combinations;
 - Products sold by `brand`;
 - Products sold by `segment`;
 - Number of products sold;

```sql
-- Products sold by (brand, segment) combinations

SELECT brand, segment, SUM(quantity)
FROM sales
GROUP BY brand, segment;
```
| brand | segment | sum |
|-------|---------|-----|
| XYZ   | Basic   | 300 |
| ABC   | Premium | 100 |
| ABC   | Basic   | 200 |
| XYZ   | Premium | 100 |

<br>

```sql
-- Products sold by brand

SELECT brand, SUM(quantity)
FROM sales
GROUP BY brand;
```
| brand | sum |
|-------|-----|
| ABC   | 300 |
| XYZ   | 400 |


<br>

```sql
-- Products sold by segment

SELECT segment, SUM(quantity)
FROM sales
GROUP BY segment;
```
| segment | sum |
|---------|-----|
| Basic   | 500 |
| Premium | 200 |

<br>

```sql
-- Number of products sold
SELECT SUM(quantity)
FROM sales;
```
| sum |
|-----|
| 700 |

<br>

We could combine the four result sets above by using `UNION ALL` clauses:

```sql
SELECT brand, segment, SUM(quantity)
FROM sales
GROUP BY brand, segment

UNION ALL

SELECT brand, NULL, SUM(quantity)
FROM sales
GROUP BY brand

UNION ALL

SELECT NULL, segment, SUM(quantity)
FROM sales
GROUP BY segment

UNION ALL 

SELECT NULL, NULL, SUM(quantity)
FROM sales
```
| brand  | segment | sum |
|--------|---------|-----|
| XYZ    | Basic   | 300 |
| ABC    | Premium | 100 |
| ABC    | Basic   | 200 |
| XYZ    | Premium | 100 |
| ABC    | [null]  | 300 |
| XYZ    | [null]  | 400 |
| [null] | Basic   | 500 |
| [null] | Premium | 200 |
| [null] | [null]  | 700 |

<br>

The query shown above is quite big. It could be rewritten with `GROUPING SETS`, which is more readable and has an optimized execution performance:

```sql
SELECT brand, segment, SUM(quantity)
FROM sales
GROUP BY GROUPING SETS ((brand, segment), (brand), (segment), ());
```
| brand  | segment | sum |
|--------|---------|-----|
| [null] | [null]  | 700 |
| XYZ    | Basic   | 300 |
| ABC    | Premium | 100 |
| ABC    | Basic   | 200 |
| XYZ    | Premium | 100 |
| ABC    | [null]  | 300 |
| XYZ    | [null]  | 400 |
| [null] | Basic   | 500 |
| [null] | Premium | 200 |

#### GROUPING

Taking the `sales` table as an example once again:

| brand | segment | quantity |
|-------|---------|----------|
| ABC   | Premium |      100 |
| ABC   | Basic   |      200 |
| XYZ   | Premium |      100 |
| XYZ   | Basic   |      300 |

<br>

```sql
SELECT
	GROUPING(brand) AS grp_brand,
	GROUPING(segment) AS grp_segment,
	brand,
	segment,
	SUM(quantity)
FROM sales
GROUP BY GROUPING SETS ((brand), (segment), ())
ORDER BY brand, segment;
```
| grp_brand | grp_segment | brand  | segment | sum |
|-----------|-------------|--------|---------|-----|
|         0 |           1 | ABC    | [null]  | 300 |
|         0 |           1 | XYZ    | [null]  | 400 |
|         1 |           0 | [null] | Basic   | 500 |
|         1 |           0 | [null] | Premium | 200 |
|         1 |           1 | [null] | [null]  | 700 |

<br>

We can see from the example above that `GROUPING` is a function that returns:

 - `0` if the argument is a member of the current grouping set;
 - `1` otherwise;

<br>

We could add a `HAVING` clause to the query to find the *subtotal* of each brand:

```sql
SELECT
	GROUPING(brand) AS grp_brand,
	GROUPING(segment) AS grp_segment,
	brand,
	segment,
	SUM(quantity)
FROM sales
GROUP BY GROUPING SETS ((brand), (segment), ())
HAVING GROUPING(brand) = 0
ORDER BY brand, segment;
```
| grp_brand | grp_segment | brand  | segment | sum |
|-----------|-------------|--------|---------|-----|
|         0 |           1 | ABC    | [null]  | 300 |
|         0 |           1 | XYZ    | [null]  | 400 |

#### CUBE

`CUBE` generates *all possible [GROUPING SETS](#grouping-sets)* for the specified columns.

The following statements are equivalent:

```sql
CUBE(a, b, c)

GROUPING SETS ((a, b, c), (a, b), (a, c), (b, c), (a), (b), (c), ())
```

<br>

The examples in this section will use the `sales` table below as example once again:

| brand | segment | quantity |
|-------|---------|----------|
| ABC   | Premium |      100 |
| ABC   | Basic   |      200 |
| XYZ   | Premium |      100 |
| XYZ   | Basic   |      300 |

<br>

```sql
SELECT brand, segment, SUM (quantity)
FROM sales
GROUP BY CUBE (brand, segment)
ORDER BY brand, segment;
```
| brand  | segment | sum |
|--------|---------|-----|
| ABC    | Basic   | 200 |
| ABC    | Premium | 100 |
| ABC    | [null]  | 300 |
| XYZ    | Basic   | 300 |
| XYZ    | Premium | 100 |
| XYZ    | [null]  | 400 |
| [null] | Basic   | 500 |
| [null] | Premium | 200 |
| [null] | [null]  | 700 |

We can identify the groups generate by `CUBE` in the results above: `(brand, segment)`, `(brand)`, `(segment)`, `()`.

<br>

```sql
SELECT brand, segment, SUM (quantity)
FROM sales
GROUP BY brand, CUBE (segment)
ORDER BY brand, segment;
```
| brand  | segment | sum |
|--------|---------|-----|
| ABC    | Basic   | 200 |
| ABC    | Premium | 100 |
| ABC    | [null]  | 300 |
| XYZ    | Basic   | 300 |
| XYZ    | Premium | 100 |
| XYZ    | [null]  | 400 |

The query above shows a *partial* `CUBE`.

For each `brand` (`ABC`, `XYZ`) we cann see all the possible values of `segment` (`Basic`, `Premium`, `null`).

#### ROLLUP

`ROLLUP` is similar to [`CUBE`](#cube), as it also generates [`GROUPING SETS`](#grouping-sets).

However, `ROLLUP` does not generate all possible [`GROUPING SETS`](#grouping-sets), but *<u>assumes a hierarchy</u>* among the specified columns:

```sql
CUBE(a, b, c)  →  (a, b, c)                 ROLLUP(a, b, c)  →  (a, b, c)
                  (a, b)                                        (a, b)
                  (a, c)                                        (a)
                  (b, c)                                        ()
                  (a)
                  (b)
                  (c)
                  ()
```

<br>

This way, `ROLLUP` can be used to *generate subtotals and grand totals* for reports.

It can also be used to *calculate aggregations by year, month and date*.

<br>

Using the `sales` table once again:

| brand | segment | quantity |
|-------|---------|----------|
| ABC   | Premium |      100 |
| ABC   | Basic   |      200 |
| XYZ   | Premium |      100 |
| XYZ   | Basic   |      300 |

<br>

```sql
SELECT brand, segment, SUM (quantity)
FROM sales
GROUP BY ROLLUP (brand, segment)
ORDER BY brand, segment;
```
| brand  | segment | sum |
|--------|---------|-----|
| ABC    | Basic   | 200 |
| ABC    | Premium | 100 |
| ABC    | [null]  | 300 |
| XYZ    | Basic   | 300 |
| XYZ    | Premium | 100 |
| XYZ    | [null]  | 400 |
| [null] | [null]  | 700 |

In the example above the hirearchy is `brand > segment`.

The 3<sup>rd</sup> row brings the total sales of brand `ABC`.

The 6<sup>th</sup> row brings the total sales of brand `XYZ`.

The 9<sup>th</sup> row brings the *grand total* for all brands.

<br>

Comparing with [`CUBE`](#cube):

```sql
SELECT                                      SELECT
    brand,                                      brand,
    segment,                                    segment,
    SUM (quantity)                              SUM (quantity)

FROM sales                                  FROM sales

GROUP BY ROLLUP (brand, segment)            GROUP BY CUBE (brand, segment)

ORDER BY brand, segment;                    ORDER BY brand, segment;


┌────────┬─────────┬─────┐                  ┌────────┬─────────┬─────┐
│ brand  │ segment │ sum │                  │ brand  │ segment │ sum │
├────────┼─────────┼─────┤                  ├────────┼─────────┼─────┤
│ ABC    │ Basic   │ 200 │                  │ ABC    │ Basic   │ 200 │
│ ABC    │ Premium │ 100 │                  │ ABC    │ Premium │ 100 │
│ ABC    │ [null]  │ 300 │                  │ ABC    │ [null]  │ 300 │
│ XYZ    │ Basic   │ 300 │                  │ XYZ    │ Basic   │ 300 │
│ XYZ    │ Premium │ 100 │                  │ XYZ    │ Premium │ 100 │
│ XYZ    │ [null]  │ 400 │                  │ XYZ    │ [null]  │ 400 │
│ [null] │ [null]  │ 700 │                  │ [null] │ Basic   │ 500 │
└────────┴─────────┴─────┘                  │ [null] │ Premium │ 200 │
                                            │ [null] │ [null]  │ 700 │
                                            └────────┴─────────┴─────┘
```

<br>

Taking the `rental` table from the `dvd rental` database as an example:

```
┌────────────────┐
│     rental     │
├────────────────┤
│ * rental_id    │
│   rental_rate  │
│   inventory_id │
│   customer_id  │
│   return_date  │
│   staff_id     │
│   last_update  │
└────────────────┘
```

<br>

```sql
SELECT 
	EXTRACT (YEAR  FROM rental_date) AS y,
	EXTRACT (MONTH FROM rental_date) AS m,
	EXTRACT (DAY   FROM rental_date) AS d,
	COUNT (rental_id)

FROM rental

GROUP BY ROLLUP (
	EXTRACT (YEAR  FROM rental_date),
	EXTRACT (MONTH FROM rental_date),
	EXTRACT (DAY   FROM rental_date)
)

ORDER BY (
	EXTRACT (YEAR  FROM rental_date),
	EXTRACT (MONTH FROM rental_date),
	EXTRACT (DAY   FROM rental_date)
);
```
|   y   |     m |     d  | count |
|-------|-------|--------|-------|
| 2005  |     5 |    24  |     8 |
| 2005  |     5 |    25  |   137 |
| 2005  |     5 |    26  |   174 |
| 2005  |     5 |    27  |   166 |
| 2005  |     5 |    28  |   196 |
| 2005  |     5 |    29  |   154 |
| 2005  |     5 |    30  |   158 |
| 2005  |     5 |    31  |   163 |
| 2005  |     5 | [null] |  1156 |
| 2005  |     6 |    14  |    16 |
| 2005  |     6 |    15  |   348 |
| . . . | . . . | . . .  | . . . |

### JOIN

Joins are used to *combine tables* based on *common columns*.

The common columns are usually the *primary key* of the *first* table and a *foreign key* in the *second* table.

The join examples below are based on the following `color_a` and `color_b` tables:

![](images/table8.png)

<br>

#### INNER JOIN

<div>
    <img src="images/table9.png">                                 &nbsp;&nbsp;&nbsp;&nbsp;
    <img src="images/inner_join.png" style="margin-bottom: 3em">  &nbsp;&nbsp;&nbsp;&nbsp;
    <img src="images/table10.png">
</div>

<br>

```sql
SELECT *
FROM color_a INNER JOIN color_b
ON color_a.color = color_b.color;
```

![](images/table11.png)

<br>
<br>

```sql
SELECT *
FROM color_b INNER JOIN color_a
ON color_a.color = color_b.color;
```
![](images/table12.png)

<br>

##### Table alias

```sql
SELECT A.color
FROM color_a A INNER JOIN color_b B
ON A.color = B.color
ORDER BY A.color;
```
![](images/table13.png)

<br>

##### USING

```sql
SELECT color
FROM color_a INNER JOIN color_b
USING(color);
```
![](images/table13.png)

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
    <img src="images/table9.png">                                &nbsp;&nbsp;&nbsp;&nbsp;
    <img src="images/left_join.png" style="margin-bottom: 3em">  &nbsp;&nbsp;&nbsp;&nbsp;
    <img src="images/table10.png">
</div>

<br>

```sql
SELECT *
FROM color_a LEFT JOIN color_b
ON color_a.color = color_b.color;
```
![](images/table14.png)

<br>

#### LEFT OUTER JOIN - only left table rows

<div>
    <img src="images/table9.png">                                          &nbsp;&nbsp;&nbsp;&nbsp;
    <img src="images/left_join_left_only.png" style="margin-bottom: 3em">  &nbsp;&nbsp;&nbsp;&nbsp;
    <img src="images/table10.png">
</div>

<br>

```sql
SELECT *
FROM color_a LEFT JOIN color_b
ON color_a.color = color_b.color
WHERE color_b.id IS NULL;
```
![](images/table15.png)

<br>

#### RIGHT OUTER JOIN

<div>
    <img src="images/table9.png">                                 &nbsp;&nbsp;&nbsp;&nbsp;
    <img src="images/right_join.png" style="margin-bottom: 3em">  &nbsp;&nbsp;&nbsp;&nbsp;
    <img src="images/table10.png">
</div>

<br>

```sql
SELECT *
FROM color_a RIGHT JOIN color_b
ON color_a.color = color_b.color;
```
![](images/table16.png)

<br>

#### RIGHT OUTER JOIN - only right table rows

<div>
    <img src="images/table9.png">                                            &nbsp;&nbsp;&nbsp;&nbsp;
    <img src="images/right_join_right_only.png" style="margin-bottom: 3em">  &nbsp;&nbsp;&nbsp;&nbsp;
    <img src="images/table10.png">
</div>

<br>

```sql
SELECT *
FROM color_a RIGHT JOIN color_b
ON color_a.color = color_b.color
WHERE color_a.id IS NULL;
```
![](images/table17.png)

<br>

#### FULL OUTER JOIN

<div>
    <img src="images/table9.png">                                &nbsp;&nbsp;&nbsp;&nbsp;
    <img src="images/full_join.png" style="margin-bottom: 3em">  &nbsp;&nbsp;&nbsp;&nbsp;
    <img src="images/table10.png">
</div>

<br>

```sql
SELECT *
FROM color_a FULL JOIN color_b
ON color_a.color = color_b.color;
```
![](images/table18.png)

<br>

#### FULL OUTER JOIN - only rows unique to both tables

<div>
    <img src="images/table9.png">                                       &nbsp;&nbsp;&nbsp;&nbsp;
    <img src="images/full_join_unique.png" style="margin-bottom: 3em">  &nbsp;&nbsp;&nbsp;&nbsp;
    <img src="images/table10.png">
</div>

<br>

```sql
SELECT *
FROM color_a FULL JOIN color_b
ON color_a.color = color_b.color
WHERE color_a.id IS NULL OR color_b.id IS NULL;
```
![](images/table19.png)

<br>

The following example tables show employees and departments. Each department can have *zero or more employees*, and each employee belongs to *zero or one* department:

| department_id | department_name |
|---------------|-----------------|
|             1 | Sales           |
|             2 | Marketing       |
|             3 | HR              |
|             4 | IT              |
|             5 | Production      |

| employee_id |  employee_name  | department_id |
|-------------|-----------------|---------------|
|           1 | Bette Nicholson | 1             |
|           2 | Christian Gable | 1             |
|           3 | Joe Swank       | 2             |
|           4 | Fred Costner    | 3             |
|           5 | Sandra Kilmer   | 4             |
|           6 | Julia Mcqueen   | *[null]*      |

<br clear="all">

To have an overview of the `employee` &harr; `department` relationships we can use the following query:

```sql
SELECT employee_name, department_name
FROM employees e FULL JOIN departments d
ON d.department_id = e.department_id;
```
|  employee_name  | department_name |
|-----------------|-----------------|
| Bette Nicholson | Sales           |
| Christian Gable | Sales           |
| Joe Swank       | Marketing       |
| Fred Costner    | HR              |
| Sandra Kilmer   | IT              |
| Julia Mcqueen   | *[null]*        |
| *[null]*        | Production      |

<br>

To find the department that doen not have any employees we can use the following query:

```sql
SELECT employee_name, department_name
FROM employees e FULL JOIN departments d
ON d.department_id = e.department_id
WHERE employee_name IS NULL;
```
|  employee_name  | department_name |
|-----------------|-----------------|
| *[null]*        | Production      |

<br>

And to find the employee that does not belong to any department:

```sql
SELECT employee_name, department_name
FROM employees e FULL JOIN departments d
ON d.department_id = e.department_id
WHERE department_name IS NULL;
```
|  employee_name  | department_name |
|-----------------|-----------------|
| Julia Mcqueen   | *[null]*        |

#### Self join

```sql
SELECT a1.color AS "Color 1", a2.color AS "Color 2" 
FROM color_a a1 INNER JOIN color_a a2
ON a1.color = a2.color;
```
![](images/table20.png)

<br>

Self joins can be used to <u>*query hierarchical data*</u>, for example.

Supposing the following employee hierarchy in an organization:

```
                              ┌───────┐
                              │ Windy │
                              │ Hays  │
                              └───┬───┘
                  ┌───────────────┴─────────────────┐
              ┌───┴────┐                     ┌──────┴──────┐
              │ Hassan │                     │     Ava     │
              │ Conner │                     │ Christensen │
              └───┬────┘                     └──────┬──────┘
    ┌─────────────┼────────────┐             ┌──────┴──────┐
┌───┴────┐    ┌───┴────┐    ┌──┴───┐     ┌───┴────┐    ┌───┴────┐
│ Salley │    │ Kelsie │    │ Tory │     │  Sau   │    │  Anna  │
│ Lester │    │  Hays  │    │ Goff │     │ Norman │    │ Reeves │
└────────┘    └────────┘    └──────┘     └────────┘    └────────┘
```

We could have the following `employee` table:

| employee_id | first_name |  last_name  | manager_id |
|-------------|------------|-------------|------------|
|           1 | Windy      | Hays        |     [null] |
|           2 | Ava        | Christensen |          1 |
|           3 | Hassan     | Conner      |          1 |
|           4 | Anna       | Reeves      |          2 |
|           5 | Sau        | Norman      |          2 |
|           6 | Kelsie     | Hays        |          3 |
|           7 | Tory       | Goff        |          3 |
|           8 | Salley     | Lester      |          3 |

In this table, the `manager_id` column references the `employee_id` column.

It brings the id of the manager the employee reports too.

Windy Hays is the top manager and reports to nobody.

<br>

The following *self join* query shows who reports to whom:

```sql
SELECT   e.first_name || ' ' || e.last_name AS employee,
	     m.first_name || ' ' || m.last_name AS manager

FROM     employee e INNER JOIN employee m
ON       e.manager_id = m.employee_id

ORDER BY manager;
```

|    employee     |     manager     |
|-----------------|-----------------|
| Sau Norman      | Ava Christensen |
| Anna Reeves     | Ava Christensen |
| Salley Lester   | Hassan Conner   |
| Kelsie Hays     | Hassan Conner   |
| Tory Goff       | Hassan Conner   |
| Ava Christensen | Windy Hays      |
| Hassan Conner   | Windy Hays      |

<br>

To show the top manager, we can use `LEFT JOIN` instead of `INNER JOIN`:

```sql
SELECT   e.first_name || ' ' || e.last_name AS employee,
	     m.first_name || ' ' || m.last_name AS manager

FROM     employee e LEFT JOIN employee m
ON       e.manager_id = m.employee_id

ORDER BY manager;
```
|    employee     |     manager     |
|-----------------|-----------------|
| Sau Norman      | Ava Christensen |
| Anna Reeves     | Ava Christensen |
| Salley Lester   | Hassan Conner   |
| Kelsie Hays     | Hassan Conner   |
| Tory Goff       | Hassan Conner   |
| Ava Christensen | Windy Hays      |
| Hassan Conner   | Windy Hays      |
| Windy Hays      | [null]          |

<br>

Self joins can be also used to <u>*compare rows with the same table*</u>.

Taking the `film` table from the `dvd rental` database as an example:

```
┌──────────────────────┐
│         film         │
├──────────────────────┤
│ * film_id            │
│   title              │
│   description        │
│   release_year       │
│   language_id        │
│   rental_duration    │
│   rental_rate        │
│   lenght             │
│   replacement_cost   │
│   rating             │
│   last_update        │
│   special_features   │
│   fulltext           │
└──────────────────────┘
```

We can use the following query to find pairs of films that have the same length:

```sql
SELECT f1.title, f2.title, f1.length
FROM film f1 INNER JOIN film f2
ON f1.film_id != f2.film_id AND f1.length = f2.length;
```
|      title       |         title          | legnth |
|------------------|------------------------|--------|
| Chamber Italian  | Resurrection Silverado |    117 |
| Chamber Italian  | Magic Mallrats         |    117 |
| Chamber Italian  | Graffiti Love          |    117 |
| Chamber Italian  | Affair Prejudice       |    117 |
| Grosse Wonderful | Hurricane Affair       |     49 |
| Grosse Wonderful | Hook Chariots          |     49 |
| Grosse Wonderful | Heavenly Gun           |     49 |
| Grosse Wonderful | Doors President        |     49 |
| Airport Pollock  | Sense Greek            |     54 |
| Airport Pollock  | October Submarine      |     54 |
| . . .            | . . .                  | . . .  |


#### CROSS JOIN

Taking the `T1` and `T2` tables below as an example:

```  
   T1           T2
┌───────┐    ┌───────┐
│ label │    │ score │
├───────┤    ├───────┤
│ A     │    │     1 │
│ B     │    │     2 │
└───────┘    │     3 │
             └───────┘
```

We can use the `CROSS JOIN` to produce the *cartesian product* of two or more tables, as shown in the illustration below:

![](images/cartesian.png)


The SQL query is as simple as:

```sql
SELECT *
FROM T1 CROSS JOIN T2;
```
| label | score |
|-------|-------|
| A     |     1 |
| B     |     1 |
| A     |     2 |
| B     |     2 |
| A     |     3 |
| B     |     3 |

<br>

The following queries are equivalent:

```sql
SELECT *
FROM T1 CROSS JOIN T2;
```
```sql
SELECT *
FROM T1, T2;
```
```sql
SELECT *
FROM T1 INNER JOIN T2
ON true;
```
<br>

#### NATURAL JOIN

Let's take the following `categories` and `products` tables below as an example:

```
          categories                                  products
┌─────────────┬───────────────┐    ┌────────────┬─────────────────┬─────────────┐
│ category_id │ category_name │    │ product_id │  product_name   │ category_id │
├─────────────┼───────────────┤    ├────────────┼─────────────────┼─────────────┤
│           1 │ Smart Phone   │    │          1 │ iPhone          │           1 │
│           2 │ Laptop        │    │          2 │ Samsung Galaxy  │           1 │
│           3 │ Tablet        │    │          3 │ HP Elite        │           2 │
└─────────────┴───────────────┘    │          4 │ Lenovo Thinkpad │           2 │
                                   │          5 │ iPad            │           3 │
                                   │          6 │ Kindle Fire     │           3 │
                                   └────────────┴─────────────────┴─────────────┘
```

`category_id` if the primary key in `categories` and a foreign key in `products`. This way, every product belongs to one category.

We can then join the tables *without specifying the join clause* with `NATURAL JOIN`.

`NATURAL JOIN` uses the *implicit join clause based on the common column* (`category_id`):

```sql
SELECT *
FROM products NATURAL JOIN categories;
```
| category_id | product_id |  product_name   | category_name |
|-------------|------------|-----------------|---------------|
|           1 |          1 | iPhone          | Smart Phone   |
|           1 |          2 | Samsung Galaxy  | Smart Phone   |
|           2 |          3 | HP Elite        | Laptop        |
|           2 |          4 | Lenovo Thinkpad | Laptop        |
|           3 |          5 | iPad            | Tablet        |
|           3 |          6 | Kindle Fire     | Tablet        |

<br>

The query below is equivalent to this one with `INNER JOIN`:

```sql
SELECT *
FROM products INNER JOIN categories
USING (category_id);
```

<br>

If we want `NATURAL JOIN` to perform joins other than the `INNER JOIN`, we use the optional `[INNER|LEFT|JOIN]` specifications as shown below:

```sql
SELECT *
FROM products NATURAL [INNER|LEFT|RIGHT] JOIN categories;
```

<br>

*`NATURAL JOIN` should be avoided whnever possible*, as it <u>*may produce unexpected results*</u> (e.g. if tables have more than one common column).

#### Summary

|                                              |                                                                                                                                                                    |
|----------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| &emsp; ![](images/inner_join.png)            | <tt>**SELECT** *</tt> <br> <tt>**FROM** a **INNER JOIN** b</tt> <br> <tt>**ON** a.key = b.key;</tt>                                                                |
| &emsp; ![](images/left_join.png)             | <tt>**SELECT** *</tt> <br> <tt>**FROM** a **LEFT JOIN** b</tt> <br> <tt>**ON** a.key = b.key;</tt>                                                                 |
| &emsp; ![](images/left_join_left_only.png)   | <tt>**SELECT** *</tt> <br> <tt>**FROM** a **LEFT JOIN** b</tt> <br> <tt>**ON** a.key = b.key</tt> <br> <tt>**WHERE** b.key **IS NULL**;</tt>                       |
| &emsp; ![](images/right_join.png)            | <tt>**SELECT** *</tt> <br> <tt>**FROM** a **RIGHT JOIN** b</tt> <br> <tt>**ON** a.key = b.key;</tt>                                                                |
| &emsp; ![](images/right_join_right_only.png) | <tt>**SELECT** *</tt> <br> <tt>**FROM** a **RIGHT JOIN** b</tt> <br> <tt>**ON** a.key = b.key</tt> <br> <tt>**WHERE** a.key **IS NULL**;</tt>                      |
| &emsp; ![](images/full_join.png)             | <tt>**SELECT** *</tt> <br> <tt>**FROM** a **FULL JOIN** b</tt> <br> <tt>**ON** a.key = b.key;</tt>                                                                 |
| &emsp; ![](images/full_join_unique.png)      | <tt>**SELECT** *</tt> <br> <tt>**FROM** a **FULL JOIN** b</tt> <br> <tt>**ON** a.key = b.key</tt>  <br> <tt>**WHERE** a.key **IS NULL OR** b.key **IS NULL**;</tt> |
| ![](images/cartesian_mini.png)               | <tt>**SELECT** *</tt> <br> <tt>**FROM** a **CROSS JOIN** b;</tt>                                                                                                   |

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