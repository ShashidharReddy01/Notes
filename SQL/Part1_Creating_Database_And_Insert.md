# SQL Notes – Part 1 (Databases, Tables, Inserts)

---

# 1. Viewing Databases

Show all existing databases:

```sql
SHOW DATABASES;
````

Example Output:

| Database           |
| ------------------ |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| soap_store         |

---

# 2. Creating Databases

General syntax:

```sql
CREATE DATABASE <database_name>;
```

Example:

```sql
CREATE DATABASE soap_store;
```

---

# 3. Using a Database

To select a database for use:

```sql
USE <database_name>;
```

Example:

```sql
USE soap_store;
```

---

# 4. Dropping a Database

Delete an entire database:

```sql
DROP DATABASE <database_name>;
```

Example:

```sql
DROP DATABASE soap_store;
```

---

# 5. Creating Tables

General syntax:

```sql
CREATE TABLE table_name (
    column_name datatype,
    column_name datatype
);
```

Example:

```sql
CREATE TABLE cats (
    name VARCHAR(50),
    age INT
);
```

Another example:

```sql
CREATE TABLE dogs (
    name VARCHAR(50),
    breed VARCHAR(50),
    age INT
);
```

---

# 6. Viewing Tables

Show all tables in the current database:

```sql
SHOW TABLES;
```

Example Output:

| Tables_in_soap_store |
| -------------------- |
| cats                 |
| dogs                 |

---

# 7. Viewing Table Structure

Show column information:

```sql
SHOW COLUMNS FROM cats;
```

or

```sql
DESC cats;
```

Example Output:

| Field | Type        | Null | Key | Default | Extra |
| ----- | ----------- | ---- | --- | ------- | ----- |
| name  | varchar(50) | YES  |     | NULL    |       |
| age   | int         | YES  |     | NULL    |       |

---

# 8. Dropping Tables

General syntax:

```sql
DROP TABLE <table_name>;
```

Example:

```sql
DROP TABLE cats;
```

Example workflow:

```sql
CREATE TABLE pastries (
    name VARCHAR(50),
    quantity INT
);

SHOW TABLES;

DESC pastries;

DROP TABLE pastries;
```

---

# 9. Inserting Data

## Single Insert

```sql
INSERT INTO cats (name, age)
VALUES ('Blue Steele', 5);
```

Another example:

```sql
INSERT INTO cats (name, age)
VALUES ('Jenkins', 7);
```

---

# 10. Insert with Different Column Order

Column order can be changed if values match the order specified.

```sql
INSERT INTO cats (age, name)
VALUES (2, 'Beth');
```

---

# 11. Multiple Row Insert

Insert several rows in one statement:

```sql
INSERT INTO cats (name, age)
VALUES
('Meatball', 5),
('Turkey', 1),
('Potato Face', 15);
```

View inserted data:

```sql
SELECT * FROM cats;
```

Example Output:

| name        | age |
| ----------- | --- |
| Blue Steele | 5   |
| Jenkins     | 7   |
| Beth        | 2   |
| Meatball    | 5   |
| Turkey      | 1   |
| Potato Face | 15  |

---

# 12. Insert Challenge Example

Creating a table:

```sql
CREATE TABLE people (
    first_name VARCHAR(20),
    last_name VARCHAR(20),
    age INT
);
```

Insert data:

```sql
INSERT INTO people(first_name, last_name, age)
VALUES ('Tina', 'Belcher', 13);

INSERT INTO people(age, last_name, first_name)
VALUES (42, 'Belcher', 'Bob');

INSERT INTO people(first_name, last_name, age)
VALUES
('Linda', 'Belcher', 45),
('Phillip', 'Frond', 38),
('Calvin', 'Fischoeder', 70);
```

View table:

```sql
SELECT * FROM people;
```

Example Output:

| first_name | last_name  | age |
| ---------- | ---------- | --- |
| Tina       | Belcher    | 13  |
| Bob        | Belcher    | 42  |
| Linda      | Belcher    | 45  |
| Phillip    | Frond      | 38  |
| Calvin     | Fischoeder | 70  |

Delete table:

```sql
DROP TABLE people;
```

---

# 13. NOT NULL Constraint

Ensures that a column **cannot contain NULL values**.

```sql
CREATE TABLE cats2 (
    name VARCHAR(100) NOT NULL,
    age INT NOT NULL
);
```

Trying to insert NULL will cause an error.

---

# 14. DEFAULT Values

Defines a default value if none is provided during insert.

```sql
CREATE TABLE cats3 (
    name VARCHAR(20) DEFAULT 'no name provided',
    age INT DEFAULT 99
);
```

View structure:

```sql
DESC cats3;
```

Insert without name:

```sql
INSERT INTO cats3(age)
VALUES (13);
```

Insert using all defaults:

```sql
INSERT INTO cats3()
VALUES();
```

Check results:

```sql
SELECT * FROM cats3;
```

Example Output:

| name             | age |
| ---------------- | --- |
| no name provided | 13  |
| no name provided | 99  |

---

# 15. Combining NOT NULL and DEFAULT

```sql
CREATE TABLE cats4 (
    name VARCHAR(20) NOT NULL DEFAULT 'unnamed',
    age INT NOT NULL DEFAULT 99
);
```

---

# 16. SQL Comments

## Single-line comment

```sql
-- This is a comment
SELECT * FROM cats;
```

## Multi-line comment

```sql
/*
This is a
multi-line comment
*/
```

---

# 17. Primary Keys

A **Primary Key uniquely identifies each row in a table**.

### Method 1

```sql
CREATE TABLE unique_cats (
    cat_id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    age INT NOT NULL
);
```

### Method 2

```sql
CREATE TABLE unique_cats2 (
    cat_id INT,
    name VARCHAR(100) NOT NULL,
    age INT NOT NULL,
    PRIMARY KEY (cat_id)
);
```

---

# 18. AUTO_INCREMENT

Automatically increases the value for each new row.

```sql
CREATE TABLE unique_cats3 (
    cat_id INT AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    age INT NOT NULL,
    PRIMARY KEY (cat_id)
);
```

Example Insert:

```sql
INSERT INTO unique_cats3(name, age)
VALUES ('Misty', 3);
```

Example Output:

| cat_id | name  | age |
|-|-|-|
| 1      | Misty | 3   |

---

# 19. Real Example: Employees Table

### Version 1

```sql
CREATE TABLE employees (
    id INT AUTO_INCREMENT,
    first_name VARCHAR(255) NOT NULL,
    last_name VARCHAR(255) NOT NULL,
    middle_name VARCHAR(255),
    age INT NOT NULL,
    current_status VARCHAR(255) NOT NULL DEFAULT 'employed',
    PRIMARY KEY(id)
);
```

### Version 2 (Inline Primary Key)

```sql
CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(255) NOT NULL,
    last_name VARCHAR(255) NOT NULL,
    middle_name VARCHAR(255),
    age INT NOT NULL,
    current_status VARCHAR(255) NOT NULL DEFAULT 'employed'
);
```

Insert example:

```sql
INSERT INTO employees(first_name, last_name, age)
VALUES ('Dora', 'Smith', 58);
```

Example Output:

| id | first_name | last_name | middle_name | age | current_status |
|----|------------|-----------|-------------|-----|----------------|
| 1  | Dora       | Smith     | NULL        | 58  | employed       |

---

