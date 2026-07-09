# PostgreSQL Notes - Database Creation & Table Management

> These notes cover the fundamentals of PostgreSQL database creation, table management, constraints, and basic CRUD operations.

---

# Table of Contents

- [Creating a Database](#creating-a-database)
- [Creating Tables](#creating-tables)
- [PostgreSQL Data Types](#postgresql-data-types)
- [Constraints](#constraints)
- [Viewing Data](#viewing-data)
- [ALTER TABLE](#alter-table)
- [INSERT INTO](#insert-into)
- [NULL Values](#null-values)
- [UPDATE](#update)
- [DELETE](#delete)
- [DROP TABLE](#drop-table)
- [Relationships](#relationships)
- [Best Practices](#best-practices)
- [Command Summary](#command-summary)

---

# Creating a Database

A database is the highest-level container in PostgreSQL.

## Syntax

```sql
CREATE DATABASE database_name;
```

### Example

```sql
CREATE DATABASE owners_pets;
```

### Full Syntax

```sql
CREATE DATABASE owners_pets
WITH
OWNER = postgres
ENCODING = 'UTF8'
LC_COLLATE = 'English_United States.1252'
LC_CTYPE = 'English_United States.1252'
TABLESPACE = pg_default
CONNECTION LIMIT = -1;
```

## Common Options

| Option | Description |
|---------|-------------|
| `OWNER` | Database owner |
| `ENCODING` | Character encoding (`UTF8` recommended) |
| `LC_COLLATE` | Sorting rules |
| `LC_CTYPE` | Character classification |
| `TABLESPACE` | Storage location |
| `CONNECTION LIMIT` | Maximum simultaneous connections |

---

# Creating Tables

Tables store data in rows and columns.

## Syntax

```sql
CREATE TABLE table_name (
    column_name data_type constraints
);
```

### Example

```sql
CREATE TABLE owners (
    id SERIAL PRIMARY KEY,
    first_name VARCHAR(30),
    last_name VARCHAR(30),
    city VARCHAR(30),
    state CHAR(2)
);
```

---

# PostgreSQL Data Types

## SERIAL

Automatically generates sequential integers.

```sql
id SERIAL
```

Example:

```
1
2
3
4
```

✅ Commonly used for primary keys.

---

## VARCHAR(n)

Variable-length string.

```sql
first_name VARCHAR(30)
```

Stores up to *n* characters.

---

## CHAR(n)

Fixed-length string.

```sql
state CHAR(2)
```

Example:

```
CA
TX
NY
```

Perfect for:

- Country codes
- State abbreviations
- Gender codes

---

## INT

Stores whole numbers.

```sql
age INT
```

Example

```
18
35
42
```

---

# Constraints

Constraints enforce rules on your data.

---

## PRIMARY KEY

```sql
id SERIAL PRIMARY KEY
```

✔ Must be unique

✔ Cannot be NULL

✔ Identifies every row

---

## FOREIGN KEY

```sql
owner_id INT REFERENCES owners(id)
```

Creates a relationship between tables.

```
Owners
-------
id

        ▲
        │
        │
owner_id

Pets
-------
id
species
```

Ensures every pet belongs to an existing owner.

---

## UNIQUE

```sql
email VARCHAR(50) UNIQUE
```

Duplicate values are **not allowed**.

Example:

| Email | Valid |
|--------|-------|
| john@gmail.com | ✅ |
| emma@gmail.com | ✅ |
| john@gmail.com | ❌ |

---

## NOT NULL

```sql
age INT NOT NULL
```

The column must always contain a value.

---

# Viewing Data

Retrieve every row from a table.

```sql
SELECT * FROM owners;
```

`*` means:

> Select every column.

---

# ALTER TABLE

Modify an existing table without recreating it.

---

## Add a Column

```sql
ALTER TABLE owners
ADD COLUMN email VARCHAR(50) UNIQUE;
```

Before

| id | first_name |
|----|------------|

After

| id | first_name | email |
|----|------------|-------|

---

## Add Multiple Columns

```sql
ALTER TABLE examples
ADD COLUMN nationality VARCHAR(30),
ADD COLUMN age INT NOT NULL;
```

---

## Change a Data Type

```sql
ALTER TABLE owners
ALTER COLUMN last_name TYPE VARCHAR(50);
```

Useful when existing storage is too small.

---

# INSERT INTO

Insert new records into a table.

## Single Row

```sql
INSERT INTO examples
(first_name,last_name,email,nationality,age)
VALUES
('David','Mitchell','dmitch@gmail.com','GBR',43);
```

---

## Multiple Rows

```sql
INSERT INTO examples (...)
VALUES
(...),
(...),
(...);
```

### Advantages

- Faster
- Cleaner
- Better performance

---

# NULL Values

`NULL` means:

> Unknown or missing data.

Example

```sql
(NULL,'Maxwell',NULL,'CA')
```

**NULL is NOT:**

- `0`
- `''`
- `FALSE`

---

# UPDATE

Modify existing records.

## Syntax

```sql
UPDATE table_name
SET column = value
WHERE condition;
```

Example

```sql
UPDATE pets
SET age = 3
WHERE full_name = 'Fluffy';
```

---

> [!WARNING]
> Omitting the `WHERE` clause updates **every row**.

```sql
UPDATE pets
SET age = 3;
```

---

# DELETE

Remove records from a table.

```sql
DELETE FROM owners
WHERE last_name = 'Maxwell';
```

---

## Delete Every Row

```sql
DELETE FROM examples;
```

The table remains.

Only the data is removed.

---

# DROP TABLE

Delete the table itself.

```sql
DROP TABLE practice;
```

---

## DELETE vs DROP

| DELETE | DROP TABLE |
|---------|------------|
| Removes rows | Removes the table |
| Structure remains | Structure is deleted |
| Can use WHERE | No WHERE clause |

---

# Relationships

This exercise demonstrates a **One-to-Many** relationship.

```
Owners
────────────────────────
id (PK)
first_name
last_name

        │
        │ 1
        │
        ▼

Pets
────────────────────────
id (PK)
species
owner_id (FK)
```

One owner can own multiple pets.

Each pet belongs to exactly one owner.

---

# Best Practices

- Use `SERIAL` (or `GENERATED AS IDENTITY` in PostgreSQL 10+) for primary keys.
- Always define a `PRIMARY KEY`.
- Use `FOREIGN KEY` constraints to maintain referential integrity.
- Store emails and usernames with `UNIQUE`.
- Always use a `WHERE` clause with `UPDATE` and `DELETE`, unless updating/deleting every row intentionally.
- Insert multiple rows in one `INSERT` statement whenever possible.
- Choose appropriate data types (`CHAR` for fixed-length values, `VARCHAR` for variable-length text).

---

# Command Summary

| Command | Description |
|----------|-------------|
| `CREATE DATABASE` | Create a database |
| `CREATE TABLE` | Create a table |
| `SELECT` | Retrieve data |
| `INSERT INTO` | Insert new rows |
| `UPDATE` | Modify existing rows |
| `DELETE` | Delete rows |
| `ALTER TABLE` | Modify table structure |
| `DROP TABLE` | Delete a table |

---

# Key Takeaways

- Databases contain tables.
- Tables contain rows and columns.
- Constraints maintain data integrity.
- Foreign keys create relationships.
- `ALTER TABLE` modifies existing tables.
- `DELETE` removes data.
- `DROP TABLE` removes the entire table.
- Always use `WHERE` with `UPDATE` and `DELETE` unless every row should be affected.
