---
{"dg-publish":true,"dg-path":"Cheat sheets/MySQL cheat sheet.md","permalink":"/cheat-sheets/my-sql-cheat-sheet/"}
---


> [!tip] Tips
> - The order of keywords matters!
> - Table/column names that are also keywords (ex. `trigger`) must be quoted using `

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

- Also see [[Development/Cheat sheets/MySQL cheat sheet#Count number of distinct values\|#Count number of distinct values]] and [[Development/Cheat sheets/MySQL cheat sheet#GROUP BY\|#GROUP BY]]

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

- INNER JOIN is the default type

![[sqlJoins_7.webp]]

## WHERE

```mysql
SELECT *
FROM pets
WHERE
    species = 'cat'
    OR species = 'dog'
    AND age >= 1
```

### `null` checking

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

```mysql
WHERE name IN ('Alice', 'Bob')
```

### BETWEEN (ranges)

- Both sides are inclusive

```mysql
WHERE id BETWEEN 100 AND 200
```

### Dates

```mysql
WHERE DATE(created_at) = '2022-12-25'
```

- If the date column has an index. you can avoid casting to improve performance:

```mysql
WHERE
    created_at >= '2022-12-25'
AND
    created_at < '2022-12-26'
```

#### INTERVAL

- Select dates in the last 24 hours:

```mysql
WHERE created_date >= NOW() - INTERVAL 1 DAY
```

### EXISTS and NOT EXISTS

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

- WHERE cannot be used with [[Development/Cheat sheets/MySQL cheat sheet#Aggregate functions\|aggregate functions]], use HAVING instead

### List all values that match more than one row

```mysql
SELECT first_name, COUNT(*)
FROM users
GROUP BY first_name
HAVING COUNT(*) > 1
```

## GROUP BY

- Often used in queries with [[Development/Cheat sheets/MySQL cheat sheet#Aggregate functions\|aggregate functions]] to analyze each group of rows
- Count the number of rows matching each distinct value of a column (see [[Development/Cheat sheets/MySQL cheat sheet#COUNT\|#COUNT]])

```mysql
SELECT owner_id, COUNT(*)
FROM pets
GROUP BY owner_id
```

- You can also select other fields (or even the whole table, ex. `pets.*`), and if combined with [[Development/Cheat sheets/MySQL cheat sheet#ORDER BY\|#ORDER BY]] use this to, for example, get the oldest pet for each owner

```mysql
SELECT owner_id, name, age
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

## WITH / AS

- Lets you store temporary results that you can refer to later
- MySQL does not let you combine [[Development/Cheat sheets/MySQL cheat sheet#UPDATE / SET\|UPDATE]] and [[Development/Cheat sheets/MySQL cheat sheet#LIMIT\|#LIMIT]], this can be used to get around that

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
        ELSE 'LP'
    END
```

## INSERT

```mysql
INSERT INTO pets (name, species, age, owner_id)
VALUES ('Rover', 'dog', 3, 1)
```

## UPDATE

> [!attention]
> Don't forget the WHERE clause - otherwise you'll update every row in the table!

```mysql
UPDATE
    pets p
SET
    p.sound = 'meow'
WHERE
    p.species = 'cat'
```

# Functions

## Aggregate functions

- `COUNT(), MAX(), MIN(), SUM(), AVG()`

## COUNT

### Count number of distinct values

```mysql
SELECT COUNT(DISTINCT first_name) ...
```

## LENGTH (string length)

```mysql
SELECT *
FROM pets
WHERE length(first_name) <= 5
```

## JSON

### JSON_EXTRACT

- Select based on values within JSON fields

```mysql
SELECT JSON_EXTRACT(column_name, '$.version') ...
```

- To handle [[Development/Cheat sheets/MySQL cheat sheet#Dates\|dates]] or datetimes:

```mysql
DATE(JSON_UNQUOTE(JSON_EXTRACT(p.metadata, '$.created_date')))
CAST(JSON_UNQUOTE(JSON_EXTRACT(p.metadata, '$.created_date')) as datetime)
```

### See also

- [[Development/Clipped/How to Use JSON Data Fields in MySQL Databases\|How to Use JSON Data Fields in MySQL Databases]]

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
