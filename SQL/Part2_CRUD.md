# SQL Notes – Part 2 (CRUD: Create, Read, Update, Delete)

**CRUD** maps to SQL as: **C**reate (INSERT), **R**ead (SELECT), **U**pdate (UPDATE), **D**elete (DELETE). Table definition uses **CREATE**; removing structure uses **DROP**.

---

# 1. Setup: List Tables, Drop, Create

Show tables in the current database:

```sql
SHOW TABLES;
```

Drop a table (removes structure and data):

```sql
DROP TABLE cats;
```

Create a table with an auto-increment primary key:

```sql
CREATE TABLE cats (
    cat_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    breed VARCHAR(100),
    age INT
);
```

---

# 2. Insert Multiple Rows

```sql
INSERT INTO cats (name, breed, age)
VALUES
    ('Ringo', 'Tabby', 4),
    ('Cindy', 'Maine Coon', 10),
    ('Dumbledore', 'Maine Coon', 11),
    ('Egg', 'Persian', 4),
    ('Misty', 'Tabby', 13),
    ('George Michael', 'Ragdoll', 9),
    ('Jackson', 'Sphynx', 7);
```

---

# 3. SELECT: All Columns and Specific Columns

All columns:

```sql
SELECT * FROM cats;
```

Chosen columns only:

```sql
SELECT name, breed FROM cats;
```

---

# 4. WHERE: Filtering Rows

By numeric equality:

```sql
SELECT * FROM cats WHERE age = 4;
```

By string equality (match the case/collation your server uses; often case-insensitive for non-binary strings in MySQL):

```sql
SELECT * FROM cats WHERE breed = 'Tabby';
```

Single column, all rows:

```sql
SELECT cat_id FROM cats;
```

Compare two columns in the same row:

```sql
SELECT * FROM cats WHERE age = cat_id;
```

---

# 5. Column Aliases (AS)

Rename columns in the result set (does not change the table):

```sql
SELECT
    cat_id AS id,
    name AS cat_name
FROM cats;
```

---

# 6. UPDATE: Change Existing Rows

Change one column for rows that match a condition:

```sql
UPDATE cats
SET name = 'Bingo'
WHERE name = 'Ringo';
```

Another single-column update:

```sql
UPDATE cats
SET breed = 'British Shorthair'
WHERE name = 'Bingo';
```

Verify:

```sql
SELECT * FROM cats;
```

Update **all** rows that match (here, every Maine Coon):

```sql
UPDATE cats
SET age = 12
WHERE breed = 'Maine Coon';

SELECT * FROM cats;
```

**Note:** `WHERE` limits which rows change. Omitting `WHERE` updates **every** row in the table.

---

# 7. DELETE: Remove Rows

Delete rows matching a condition:

```sql
DELETE FROM cats WHERE age = 4;

SELECT * FROM cats;
```

Delete where a condition compares columns:

```sql
DELETE FROM cats WHERE age = cat_id;

SELECT * FROM cats;
```

Delete **all** rows (table structure remains; often slower than `TRUNCATE` on large tables, behavior differs by engine):

```sql
DELETE FROM cats;
```

---

# 8. Practice: `shirts` Table

Select database (create it first if it does not exist):

```sql
USE shirts_db;
```

Create table:

```sql
CREATE TABLE shirts (
    shirt_id INT AUTO_INCREMENT PRIMARY KEY,
    article VARCHAR(50),
    color VARCHAR(50),
    shirt_size VARCHAR(5),
    last_worn INT
);
```

Describe **table** structure (table name, not database name):

```sql
DESC shirts;
```

---

# 9. Practice: Inserts

Bulk insert:

```sql
INSERT INTO shirts (article, color, shirt_size, last_worn)
VALUES
    ('t-shirt', 'white', 'S', 10),
    ('t-shirt', 'green', 'S', 200),
    ('polo shirt', 'black', 'M', 10),
    ('tank top', 'blue', 'S', 50),
    ('t-shirt', 'pink', 'S', 0),
    ('polo shirt', 'red', 'M', 5),
    ('tank top', 'white', 'S', 200),
    ('tank top', 'blue', 'M', 15);
```

Single extra row:

```sql
INSERT INTO shirts (article, color, shirt_size, last_worn)
VALUES ('polo shirt', 'purple', 'M', 50);
```

---

# 10. Practice: SELECT and WHERE

```sql
SELECT * FROM shirts;

SELECT article, color FROM shirts;

SELECT * FROM shirts WHERE shirt_size = 'M';

SELECT * FROM shirts WHERE article = 'polo shirt';
```

---

# 11. Practice: UPDATE

```sql
UPDATE shirts
SET shirt_size = 'L'
WHERE article = 'polo shirt';

UPDATE shirts
SET last_worn = 0
WHERE last_worn = 15;
```

Update **multiple columns** in one statement:

```sql
UPDATE shirts
SET
    color = 'off white',
    shirt_size = 'XS'
WHERE
    color = 'white';
```

---

# 12. Practice: DELETE and Cleanup

```sql
DELETE FROM shirts WHERE last_worn = 200;

DELETE FROM shirts;

DROP TABLE shirts;
```

---

# 13. Quick Reference

| Goal              | Statement | Typical use                          |
| ----------------- | --------- | ------------------------------------ |
| Add rows          | `INSERT`  | One or many `VALUES` rows            |
| Read rows         | `SELECT`  | `WHERE` filters; `*` or column list  |
| Change rows       | `UPDATE`  | `SET` columns; **always** use `WHERE` when you mean specific rows |
| Remove rows       | `DELETE`  | `WHERE` for subset; no `WHERE` = all rows |
| Remove table      | `DROP TABLE` | Deletes definition + data         |

---
