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
      - [CROSS JOIN](#cross-join)
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

<div>
    <div style="float: left">
        <table>
            <tr><th>department_id</th><th>department_name</th></tr>
            <tr><td>1</td><td>Sales</td></tr>
            <tr><td>2</td><td>Marketing</td></tr>
            <tr><td>3</td><td>HR</td></tr>
            <tr><td>4</td><td>IT</td></tr>
            <tr><td>5</td><td>Production</td></tr>
        </table>
    </div>
    <div style="float: left; width: 3em">
        &nbsp;
    </div>
    <div style="float: left">
        <table>
            <tr><th>employee_id</th><th>employee_name</th><th>department_id</th></tr>
            <tr><td>1</td><td>Bette Nicholson</td><td>1</td></tr>
            <tr><td>2</td><td>Christian Gable</td><td>1</td></tr>
            <tr><td>3</td><td>Joe Swank</td><td>2</td></tr>
            <tr><td>4</td><td>Fred Costner</td><td>3</td></tr>
            <tr><td>5</td><td>Sandra Kilmer</td><td>4</td></tr>
            <tr><td>6</td><td>Julia Mcqueen</td><td>[null]</td></tr>
        </table>
    </div>
</div>

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
    ┌─────────────┼────────────┐            ┌───────┴─────┐
┌───┴────┐    ┌───┴────┐    ┌──┴───┐    ┌───┴────┐    ┌───┴────┐
│ Salley │    │ Kelsie │    │ Tory │    │  Sau   │    │  Anna  │
│ Lester │    │  Hays  │    │ Goff │    │ Norman │    │ Reeves │
└────────┘    └────────┘    └──────┘    └────────┘    └────────┘
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

<div>
    <div style="float: left">
        <h4 align="center">T1</h4>
        <table>
            <tr><th>label</th></tr>
            <tr><td>A</td></tr>
            <tr><td>B</td></tr>
        </table>
    </div>
    <div style="float: left; width: 3em">
        &nbsp;
    </div>
    <div style="float: left">
        <h4 align="center">T2</h4>
        <table>
            <tr><th>score</th></tr>
            <tr><td>1</td></tr>
            <tr><td>2</td></tr>
            <tr><td>3</td></tr>
        </table>
    </div>
</div>

<br clear="all">

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