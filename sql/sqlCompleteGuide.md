# 🧠 SQL Complete Guide

## 🏗️ Create Database & Table
```sql
CREATE DATABASE library;

USE library;

CREATE TABLE book (
                      id INT PRIMARY KEY AUTO_INCREMENT,
                      book_name VARCHAR(100),
                      author VARCHAR(100),
                      published_year INT,
                      price DECIMAL(10,2),
                      category VARCHAR(50),
                      book_date DATE
);
```
---
## 📥 Insert Data
```sql
INSERT INTO book (book_name, author, published_year, price, category, book_date)
VALUES ('Clean Code', 'Robert Martin', 2008, 50.00, 'Programming', '2008-08-01');
```
---
## 📚 Select Data
```sql
SELECT * FROM book;
```
### 🎯 Select Specific Columns
```sql
SELECT book_name, author FROM book;
```
---
## 🔤 ORDER BY — Sorting Results
```sql
SELECT * FROM book
ORDER BY book_name ASC;  -- ASC or DESC
```
---
### 🧩 ORDER BY Multiple Columns
- Default is ASC if not written.
- The order matters — SQL evaluates columns from left to right.

#### Example 1 — Two columns (both ascending)
```sql
-- ➡️ Sorts first by category,then alphabetically by book_name within each category.
SELECT * 
FROM book
ORDER BY category ASC, book_name ASC;
```

#### Example 2 — Mixed order
```sql
-- ➡️ Sorts categories A–Z,and within each category, the most expensive books appear first.
SELECT * 
FROM book
ORDER BY category ASC, price DESC;
```

#### Example 3 — Three columns
```sql
-- ➡️ First by country,then city,and finally by last_name in reverse order.
SELECT * 
FROM customers
ORDER BY country ASC, city ASC, last_name DESC;
```

---
## ➕ Arithmetic Expressions
You can use `+`, `-`, `*`, `/`, `%`.
```sql
SELECT first_name, points, (points + 10) * 5 AS bonus_points
FROM customers;
```
---
## 🏷️ Alias
Rename columns or tables using `AS`.
```sql
SELECT book_name AS "Book Title", author AS "Writer"
FROM book;
```
---
## 🔁 DISTINCT
Removes duplicate values.
```sql
SELECT DISTINCT author FROM book;
```
---
## 🎯 WHERE — Filtering Data
Operators:
| Operator | Description |
|-----------|-------------|
| `=` | Equal |
| `!=`, `<>` | Not equal |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal |
| `<=` | Less than or equal |
| `AND` | All conditions must be true |
| `OR` | At least one condition true |
| `NOT` | Negates condition |
Example:
```sql
SELECT * FROM book
WHERE price > 40 AND category = 'Programming';
```
---
## 🧩 Parentheses for clarity
```sql
SELECT * FROM book
WHERE (author = 'Ali' OR author = 'Sara') AND price > 30;
```
---
## 🔢 IN / NOT IN
```sql
SELECT * FROM book
WHERE author IN ('Ali', 'Sara', 'John');
```
---
## 📅 BETWEEN
```sql
SELECT * FROM book
WHERE published_year BETWEEN 2000 AND 2015;
```
---
## 🔍 LIKE — Pattern Matching
| Pattern | Meaning |
|----------|----------|
| `'m%'` | starts with “m” |
| `'%m'` | ends with “m” |
| `'%m%'` | contains “m” |
| `'m___d'` | starts with “m”, ends with “d”, 3 letters between |
```sql
SELECT * FROM customers
WHERE name LIKE 'm%';
```
---
## 🔎 REGEXP — Regular Expressions
| Symbol | Meaning |
|---------|----------|
| `^` | Beginning of string |
| `$` | End of string |
| `|` | OR condition |
| `[a-f]` | Range a to f |
| `[0-9]` | Any digit |
| `[A-Za-z]` | Any letter |
```sql
SELECT * FROM customers
WHERE name REGEXP '^moh|ali$';
```
---
## ⚙️ NULL Handling
| Operator | Description |
|-----------|-------------|
| `IS NULL` | Value is null |
| `IS NOT NULL` | Value is not null |
```sql
SELECT * FROM book
WHERE category IS NULL;
```
---
## 🔢 Aggregate Functions
| Function | Description |
|-----------|-------------|
| `COUNT()` | Counts rows |
| `SUM()` | Adds up values |
| `AVG()` | Average value |
| `MIN()` | Smallest value |
| `MAX()` | Largest value |
```sql
SELECT COUNT(*) AS total_books FROM book;
SELECT AVG(price) AS avg_price FROM book;
```
---
## 🧮 GROUP BY
Used with aggregates.
```sql
SELECT category, COUNT(*) AS total_books
FROM book
GROUP BY category;
```
---
## 🚧 HAVING
Used to filter grouped results (like WHERE but after aggregation).
```sql
SELECT category, COUNT(*) AS total_books
FROM book
GROUP BY category
HAVING COUNT(*) > 3;
```
---
## 🔗 JOINs
Used to combine data from multiple tables.
### INNER JOIN
Returns matching rows.
```sql
SELECT orders.id, customers.name
FROM orders
         INNER JOIN customers ON orders.customer_id = customers.id;
```
### LEFT JOIN
Returns all rows from left table, and matches from right table.
```sql
SELECT customers.name, orders.id
FROM customers
         LEFT JOIN orders ON customers.id = orders.customer_id;
```
### RIGHT JOIN
Opposite of LEFT JOIN.
```sql
SELECT orders.id, customers.name
FROM orders
         RIGHT JOIN customers ON orders.customer_id = customers.id;
```
### FULL OUTER JOIN *(if supported)*
```sql
SELECT *
FROM customers
         FULL OUTER JOIN orders ON customers.id = orders.customer_id;
```
---
## 🧩 Subqueries
Query inside another query.
```sql
SELECT * FROM book
WHERE price > (SELECT AVG(price) FROM book);
```
---
## 🔐 Constraints
| Constraint | Description |
|-------------|-------------|
| `PRIMARY KEY` | Unique & not null |
| `FOREIGN KEY` | References another table |
| `UNIQUE` | No duplicates |
| `NOT NULL` | Cannot be null |
| `DEFAULT` | Default value if none given |
| `CHECK` | Must satisfy condition |
Example:
```sql
CREATE TABLE customers (
                           id INT PRIMARY KEY AUTO_INCREMENT,
                           name VARCHAR(100) NOT NULL,
                           email VARCHAR(100) UNIQUE,
                           age INT CHECK(age >= 18)
);
```
---
## 🧱 ALTER TABLE
```sql
ALTER TABLE book ADD COLUMN publisher VARCHAR(100);
ALTER TABLE book DROP COLUMN publisher;
ALTER TABLE book MODIFY COLUMN price DECIMAL(12,2);
```
---
## 🗑️ DELETE / DROP
```sql
DELETE FROM book WHERE id = 1;     -- Delete row
TRUNCATE TABLE book;               -- Delete all rows
DROP TABLE book;                   -- Delete table
DROP DATABASE library;             -- Delete database
```
---
## 🧩 UPDATE
```sql
UPDATE book
SET price = 60.00
WHERE book_name = 'Clean Code';
```
---
## 🚀 LIMIT & OFFSET
```sql
SELECT * FROM book
LIMIT 5 OFFSET 10;  -- Skip 10, show next 5
```
---
## ⚡ CASE Statement
```sql
SELECT book_name,
       CASE
           WHEN price < 30 THEN 'Cheap'
           WHEN price BETWEEN 30 AND 60 THEN 'Medium'
           ELSE 'Expensive'
           END AS price_category
FROM book;
```
---
## 🔒 Transactions
```sql
START TRANSACTION;
UPDATE book SET price = price - 10 WHERE id = 1;
ROLLBACK;  -- Undo changes
COMMIT;    -- Save changes
```
---
## 📈 Views
A view is a saved query.
```sql
CREATE VIEW expensive_books AS
SELECT * FROM book WHERE price > 50;
SELECT * FROM expensive_books;
```
---
## 🧩 Index
Used to improve query performance.
```sql
CREATE INDEX idx_book_name ON book(book_name);
DROP INDEX idx_book_name ON book;
```
---
## 📘 Example Summary Query
```sql
SELECT category, COUNT(*) AS total_books, AVG(price) AS avg_price
FROM book
WHERE published_year > 2000
GROUP BY category
HAVING AVG(price) > 40
ORDER BY avg_price DESC;
```
---
### 🚀 LIMIT & OFFSET — Control Number of Rows
- `LIMIT` restricts how many rows are returned.
- `OFFSET` skips a number of rows before starting to return results.
- `LIMIT` always comes after `ORDER BY` (if present).
- In some databases like SQL Server, you use: `SELECT TOP 5 * FROM book;`
#### 🧩 Example 1 — Limit number of rows
```sql
-- ➡️ Returns only the first 5 rows.
SELECT * FROM book LIMIT 5;
```
#### 🧭 Example 2 — Limit with offset
```sql
-- ➡️ Skips the first 10 rows and returns the next 5.
SELECT * FROM book LIMIT 5 OFFSET 10;
```


1:24