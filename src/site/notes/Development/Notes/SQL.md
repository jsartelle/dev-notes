---
{"dg-publish":true,"dg-path":"Notes/Notes/SQL.md","permalink":"/notes/notes/sql/","tags":["tech/databases"]}
---


> [!note]
> Unless otherwise specified, these notes are written with MySQL in mind.

> [!important]
> The order of keywords matters! The keywords below are listed in the order they should be used.

# General

- Comments start with `--`
- `TRUE` and `FALSE` are the same as `1` and `0` respectively
- Single and double quotes are the same except for escaping (same as JavaScript, Python, etc)
- Table/column names that are also keywords (ex. `trigger`) must be quoted using \`

# Variables

- must start with `@`
- can only hold simple data types: integer, decimal, float, string, NULL

```mysql
SET @start_date = '2023-01-01'; -- semicolon is important

SELECT *
FROM users
WHERE created_at > @start_date
```

# Dates & Times

> [!important]
> Make sure to put dates in quotes! Without quotes, `2022-12-25` will be interpreted as "the number 2,022 minus 12 minus 25"

- Use `NOW()` to get the current timestamp
- To cast to a timestamp:
    - `TIMESTAMP()`: date and time
    - `DATE()`: date without time
    - `TIME()`: time only

```mysql
# created_at is a timestamp, which looks like '2022-12-01 14:27:08'
WHERE DATE(created_at) <= '2022-12-25'
# or
WHERE TIME(created_at) > '12:00:00'
```

- If the column has an index, you can avoid casting to improve performance:

```mysql
WHERE
    created_at >= '2022-12-25'
AND
    created_at < '2022-12-26'
```

## INTERVAL

- Select dates in the last 24 hours
    - interval names are singular, ex. `DAY`, `WEEK`, `MONTH`

```mysql
WHERE created_date >= NOW() - INTERVAL 1 DAY
```

# Keywords

## SELECT / AS / FROM

```mysql
SELECT
    id,
    name,
    owner_name AS owner
FROM pets
```

### SELECT DISTINCT

```mysql
SELECT DISTINCT owner_name
FROM pets
```

- Select all distinct **combinations** of `name` and `owner_name`

```mysql
SELECT DISTINCT name, owner_name
FROM pets
```

- Also see [[Development/Notes/SQL#Count number of distinct values\|#Count number of distinct values]] and [[Development/Notes/SQL#GROUP BY\|#GROUP BY]]

## CASE

- Like switch statements (no fallthrough)

```mysql
SELECT
    a.name,
    CASE
        WHEN a.rating <= 2 THEN 'Bad'
        WHEN a.rating = 3 THEN 'Average'
        WHEN a.rating >= 4 THEN 'Good'
    END AS quality
FROM
    albums a
```

```mysql
UPDATE
    albums a
SET
    a.album_type = CASE
        WHEN a.track_count = 1 THEN 'Single'
        WHEN a.track_count <= 5 THEN 'EP'
        # fallback to existing value, otherwise it will be set to NULL
        ELSE a.album_type
    END
```

## INSERT

```mysql
INSERT INTO pets (name, species, age, owner_id)
VALUES ('Rover', 'dog', 3, 1)
```

- to insert multiple rows, add multiple sets of values

```mysql
INSERT INTO pets (name, species, age, owner_id)
VALUES ('Rover', 'dog', 3, 1), ('Spot', 'dog', 1, 1)
```

### INSERT IGNORE

- ignores errors and moves on to the next `INSERT`

```mysql
INSERT INTO pets (id, name) VALUES (1, 'Rover')

INSERT INTO pets (id, name) VALUES (1, 'Spot') # errors

INSERT IGNORE INTO pets (id, name) VALUES (1, 'Spot') # doesn't error, does nothing
```

### REPLACE (upsert)

- inserts the row if it doesn't exist, deletes it and inserts a replacement if it does

```mysql
REPLACE INTO pets (id, name) VALUES (1, 'Rover') # inserts (if the database is empty)

REPLACE INTO pets (id, name) VALUES (1, 'Spot')
```

### Conditional inserts

- only inserts the row if this owner does not already have a dog
    - note the use of `SELECT` instead of `VALUES`

```mysql
INSERT INTO pets (name, species, age, owner_id)
SELECT 'Rover', 'dog', 3, 1
WHERE NOT EXISTS (SELECT * FROM pets WHERE species = 'dog' AND owner_id = 1)
```

## UPDATE / SET

> [!warning]
> Don't forget the WHERE clause - otherwise you'll update every row in the table!

```mysql
UPDATE
    pets p
SET
    p.sound = 'meow',
    p.fluffy = 1
WHERE
    p.species = 'cat'
```

## JOIN

```mysql
SELECT
	u.id,
	u.name,
	p.name AS petName,
	p.species
FROM
	database.users u
	JOIN database.pets p ON p.owner_id = u.id
```

- INNER JOIN: returns only rows where the value matches in both tables
    - INNER JOIN is the default type
- CROSS JOIN: returns all rows from both tables
- LEFT JOIN: returns all rows from left table, and matching rows from right table
- RIGHT JOIN: returns all rows from right table, and matching rows from left table

![sqlJoins_7.webp](/img/user/Development/%E2%80%A2%20Attachments/sqlJoins_7.webp)

### USING

- `USING (user_id)` is a shortcut for `ON A.user_id = B.user_id`
    - with `USING`, the join column only appears once in the result, so you don't need to qualify it with `TABLE_NAME.column_name`

```mysql
SELECT users.id, pets.id, address
FROM users
JOIN pets USING (address)
```

## WHERE

```mysql
SELECT *
FROM pets
WHERE
    species = 'cat'
    OR species = 'dog'
    AND age >= 1
```

### Boolean and `null` checking

- Use `IS` and `IS NOT` when comparing against `TRUE`, `FALSE`, or `NULL`

```mysql
SELECT *
FROM pets
WHERE
    first_name IS NOT NULL
    AND last_name IS NULL
```

### Check if a match exists

- This will return an empty record if there is no match, or `1` if there is a match. This avoids pulling the whole record if you just want to know if it exists.

```mysql
SELECT 1
FROM pets
WHERE p.species = 'cat'
LIMIT 1
```

### LIKE (pattern matching)

```mysql
WHERE name LIKE '%alice%'
```

- Case sensitive:

```mysql
WHERE name LIKE BINARY '%Alice%'
```

### REGEXP_LIKE (regular expressions)

- Backslashes must be escaped

```mysql
WHERE REGEXP_LIKE(name, '\\w+_\\d{4}_\\d{2}_\\d{2}')
```

### IN (list of values)

> [!warning]
> The order of the returned rows doesn't necessarily match the order of the given values!

```mysql
WHERE name IN ('Alice', 'Bob')
```

### MEMBER_OF (in JSON array)

```mysql
WHERE id MEMBER OF ('[1, 2, 3]')
```

- since JSON object keys are always strings, if you used [[Development/Notes/SQL#JSON_KEYS\|#JSON_KEYS]] to get the array, you may need to [[Development/Notes/SQL#CAST\|cast]] the value being checked to a string

```mysql
-- if `user.relationships` was a JSON field with something like '{ 123: 'sibling' }'
WHERE CONVERT(user.id, CHAR) MEMBER OF (JSON_KEYS(user.relationships))
```

### BETWEEN (ranges)

- Both sides are inclusive
- Also `NOT BETWEEN`

```mysql
WHERE id BETWEEN 100 AND 200
```

### EXISTS and NOT EXISTS

- Lets you filter based on the results of another query
- If using multiple in a row, only use the `WHERE` keyword once

```mysql
WHERE EXISTS ( ... )
AND EXISTS ( ... )
AND NOT EXISTS ( ... )
```

#### Select rows that share a value with other rows

- This will select pets that share an owner with at least one other pet

```mysql
SELECT a.*
FROM pets a
WHERE EXISTS (
    SELECT 1
    FROM pets b
    WHERE a.owner_id = b.owner_id
    AND a.id <> b.id
)
```

#### Select rows that don't meet a condition in another table

- Select all users who do not own a cat

```mysql
SELECT *
FROM users u
WHERE NOT EXISTS (
    SELECT 1
    FROM pets p
    WHERE u.id = p.owner_id
    AND p.species = 'cat'
)
```

## HAVING

- WHERE cannot be used with [[Development/Notes/SQL#Aggregate functions (COUNT, MAX, MIN, SUM, AVG)\|aggregate functions]], use HAVING instead

### List all values that match more than one row

```mysql
SELECT first_name, COUNT(*)
FROM users
GROUP BY first_name
HAVING COUNT(*) > 1
```

## GROUP BY

- Often used in queries with [[Development/Notes/SQL#Aggregate functions (COUNT, MAX, MIN, SUM, AVG)\|aggregate functions]] to analyze each group of rows
- Count the number of rows matching each distinct value of a column

```mysql
SELECT owner_id, COUNT(*)
FROM pets
GROUP BY owner_id
```

### GROUP BY and ordering

- By default, which row is returned from `GROUP_BY` is not deterministic, so the following will not work as expected (returning the oldest pet for each owner)

```mysql
SELECT owner_id, name, age
FROM pets
GROUP BY owner_id
ORDER BY age DESC
```

- To fix this, use an [[Development/Notes/SQL#Aggregate functions (COUNT, MAX, MIN, SUM, AVG)\|aggregate function]]

```mysql
SELECT owner_id, name, MAX(age)
FROM pets
GROUP BY owner_id
ORDER BY age DESC
```

## ORDER BY

```mysql
SELECT id, name
FROM pets
ORDER BY id DESC -- or ASC
```

## LIMIT / OFFSET

```mysql
SELECT id
FROM pets
LIMIT 5
OFFSET 10
```

# WITH / AS (subqueries)

- Let you store temporary results (*Common Table Expressions* or *CTE*s) that you can refer to later
- MySQL does not let you combine [[Development/Notes/SQL#UPDATE / SET\|UPDATE]] and [[Development/Notes/SQL#LIMIT / OFFSET\|#LIMIT / OFFSET]], this can be used to get around that

```mysql
WITH ids AS (
    SELECT
        a.id
    FROM
        albums a
    WHERE
        a.stars = 5
    LIMIT 10
)

UPDATE
    albums a
SET
    a.recommended = 1
WHERE
    a.id IN (SELECT * FROM ids)
```

- To use multiple AS blocks:

```mysql
WITH foo AS (
    ...
), bar AS (
    ...
)
```

- To select from multiple CTEs:

```mysql
SELECT
    foo.id AS foo, bar.id AS bar
FROM
    foo
    JOIN bar USING (id)
```

# Functions

> [!WARNING]
> Do not put a space between the function name and parentheses!

## Aggregate functions (COUNT, MAX, MIN, SUM, AVG)

- `COUNT(), MAX(), MIN(), SUM(), AVG()`

### Select based on an aggregate

- Select the youngest dog
    - the `GROUP BY` is important!

```mysql
SELECT
    MIN(birth_date) as birth_date,
    id
FROM
    pets
WHERE
    species = "dog"
GROUP BY
    species
```

### Count number of distinct values

```mysql
SELECT COUNT(DISTINCT first_name) ...
```

## CAST

| Format       | Notes/Example                                                                 |
| ------------ | --------------------------------------------------------------------------------- |
| DATE     | YYYY-MM-DD                                                                    |
| DATETIME | YYYY-MM-DD HH:MM:SS                                                           |
| DECIMAL  | takes two parameters (M, D) for max integer digits & number of decimal digits |
| TIME     | HH:MM:SS                                                                      |
| CHAR     | fixed length string                                                           |
| NCHAR    | string with the national character set                                        |
| SIGNED   | signed 64-bit int                                                             |
| UNSIGNED | unsigned 64-bit int                                                           |
| BINARY   | binary string                                                                 |

## LENGTH (string length)

```mysql
SELECT *
FROM pets
WHERE length(first_name) <= 5
```

## JSON

### JSON_OBJECT

- Create a JSON object from key-value pairs

```mysql
SET u.metadata = JSON_OBJECT('is_enrolled', TRUE, 'is_mobile', FALSE)
```

### JSON_KEYS

- Get a JSON array from the keys of a JSON object
    - see [[Development/Notes/SQL#MEMBER_OF (in JSON array)\|MEMBER_OF]] to use it in a WHERE or JOIN

```mysql
SELECT JSON_KEYS(user.metadata)
```

### JSON_CONTAINS_PATH

- Find out if a value exists at a path
    - can provide many paths - second argument is "one" or "all", to check whether any or all of the paths have a value

```mysql
WHERE JSON_CONTAINS_PATH(p.metadata, 'one', '$.processed_date')

WHERE JSON_CONTAINS_PATH(p.metadata, 'all', '$.created_date', '$.processed_date')
```

### JSON_EXTRACT

- Select based on values within JSON fields
    - make sure to use backticks or nothing around the column name, not single or double quotes
- Returns NULL if the path is NULL

```mysql
SELECT JSON_EXTRACT(column_name, '$.version') ...
```

- You can also use the `->` operator

```mysql
SELECT column_name->"$.version"
```

- To handle [[Development/Notes/SQL#Dates & Times\|dates]] or datetimes:

```mysql
DATE(JSON_UNQUOTE(JSON_EXTRACT(p.metadata, '$.created_date')))
CAST(JSON_UNQUOTE(JSON_EXTRACT(p.metadata, '$.created_date')) as datetime)
```

### JSON_SET, JSON_INSERT, JSON_REPLACE

- `JSON_SET`: Insert or update JSON data
- `JSON_REPLACE`: same but only replaces *existing* values
- `JSON_INSERT`: same but only inserts *new* values
- doesn't work if the object at the given path is NULL - use a CASE to check for this

```mysql
UPDATE
	users u
SET
	u.metadata = CASE WHEN u.metadata IS NULL
	THEN JSON_OBJECT('is_enrolled', TRUE)
	ELSE JSON_SET(u.metadata, '$.is_enrolled', TRUE)
	END
WHERE
	u.id = 12345
```

### JSON_REMOVE

- Remove the specified JSON key

```mysql
UPDATE
    users u
SET
    u.metadata = JSON_REMOVE(u.metadata, '$.is_enrolled')
    END
WHERE
    u.id = 12345
```

### See also

- [[Development/Clipped/How to Use JSON Data Fields in MySQL Databases\|How to Use JSON Data Fields in MySQL Databases]]

# Creating tables

```mysql
CREATE TABLE pets (
    id int NOT NULL AUTO_INCREMENT,
    owner_id int NOT NULL,
    name varchar(255),
    FOREIGN KEY (owner_id) REFERENCES Users(id),
    PRIMARY KEY (id)
);
```

# Transactions

- Running scripts in a transaction lets you test & roll back changes

```mysql
START TRANSACTION;

-- destructive code goes here - don't forget a semicolon at the end
;

-- add a SELECT here to verify that the changes look good
;

ROLLBACK;
-- or to commit the changes use COMMIT;
```

# Configuration

## DESCRIBE

- Prints a table's schema

```mysql
DESCRIBE database.users
```

## Auto increment

```sql
ALTER TABLE 'users' AUTO_INCREMENT = 1;
```

## SQL mode

### Get

```mysql
SELECT @@GLOBAL.sql_mode;
```

### Set

```mysql
SET GLOBAL sql_mode = 'NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';
```

## Time zone

### Get

```mysql
SELECT @@global.time_zone;
```

- `SYSTEM` means the timezone is set in the `my.cnf` file. To get the time zone offset:

```mysql
SELECT TIMEDIFF(NOW(), UTC_TIMESTAMP);
```

### Set

```mysql
SET @@global.time_zone = '+00:00';
```

# Renaming columns

Renaming existing columns can cause deployed code to break. One approach to safely renaming columns:

1. update the code to handle both the old and new names and deploy
2. rename the column
3. update the code to remove the old name

Another approach:

1. add a new column with the new name and copy the data over
2. update the code to use the new column and deploy
3. drop the old column

# PostgreSQL differences from MySQL

- not as performant for reads
- more performant for writes, large datasets, large numbers of concurrent sessions
- variables don't need to start with `@`, aren't set with the SET keyword, and can be any data type

```postgresql
user_id integer;
quantity numeric(5) DEFAULT 32;
url varchar := 'http://mysite.com';
transaction_time CONSTANT timestamp with time zone := now();
myrow tablename%ROWTYPE;
myfield tablename.columnname%TYPE;
arow RECORD;
```

- `||` is the concatenation operator

```postgresql
'AB' || 'CD' -> 'ABCD'

'ABC' || 123 -> 'ABC123'
```

## Arrays

- write array values as literals using `'{}'` or `ARRAY[]`

```postgresql
CREATE TABLE sal_emp (
    name            text,
    pay_by_quarter  integer[],
    schedule        text[][]
);

INSERT INTO sal_emp
    VALUES ('Bill',
    '{10000, 10000, 10000, 10000}',
    '{{"meeting", "lunch"}, {"training", "presentation"}}');
-- or
INSERT INTO sal_emp
    VALUES ('Bill',
    ARRAY[10000, 10000, 10000, 10000],
    ARRAY[['meeting', 'lunch'], ['training', 'presentation']]);

SELECT pay_by_quarter[3] FROM sal_emp;

-- if using slices, use them for all dimensions
SELECT schedule[1:2][1:1] FROM sal_emp WHERE name = 'Bill';

UPDATE sal_emp SET pay_by_quarter[1:2] = '{27000,27000}'
    WHERE name = 'Carol';
```

- `||` can concatenate arrays, or add elements to an array

```postgresql
ARRAY[1, 2] || ARRAY[3, 4] -> {1, 2, 3, 4}

ARRAY[1, 2, 3] || 4 -> {1, 2, 3, 4}

0 || ARRAY[1, 2, 3] -> {0, 1, 2, 3}
```

- to check if an array contains an element, use `ANY ()`

```postgresql
WHERE 10000 = ANY (pay_by_quarter)
```
