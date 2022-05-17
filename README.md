# MySQL 

## Basic Commands

```sql
SHOW databases;
CREATE DATABASE tutorial1;
USE tutorial1;
CREATE TABLE users (username TEXT);
SHOW TABLES;
DESCRIBE users;
INSERT INTO users (username) VALUES ("Bob");
SELECT * FROM users;
DROP TABLE users;
CREATE TABLE users (id INT, username TEXT);
INSERT INTO users (id, username) VALUES (1, "Bob");
DROP TABLE users;
CREATE TABLE users (id INT NOT NULL, username TEXT NOT NULL);
```

## Storage Engine and Configuration

```sql
SHOW ENGINES;
SHOW TABLE STATUS;
CREATE TABLE test (id INT) engine=MYISAM;
DROP TABLE test;
SET DEFAULT_STORAGE_ENGINE=MYISAM;
SHOW ENGINES;
CREATE TABLE test (id INT);
SET DEFAULT_STORAGE_ENGINE=InnoDB;
#/etc/mysql/my.cnf
```

## SQL Mode

```sql
SELECT @@GLOBAL.SQL_MODE;
SELECT @@GLOBAL.SQL_MODE, @@SESSION.SQL_MODE;
SET sql_mode = 'STRICT_ALL_TABLES';
SELECT @@SESSION.SQL_MODE;
SET sql_mode = 'NO_ENGINE_SUBSTITUTION';
```

## More Commands

```sql
DELETE FROM users;
SET sql_safe_updates = 1;
SELECT @@SESSION.SQL_SAFE_UPDATES;
SET sql_safe_updates = 0;
DELETE FROM users;

DROP TABLE users;
CREATE TABLE users (id INT PRIMARY KEY, name TEXT, email TEXT);
DESC users;
INSERT INTO users (id, name, email) VALUES (0, "Bob", "bob@test.com");
INSERT INTO users (id, name, email) VALUES (1, "Vicky", "vicky@test.com");
DROP TABLE users;
CREATE TABLE users (id INT PRIMARY KEY AUTO_INCREMENT, name TEXT);
DESC users;
INSERT INTO users (name) VALUES ("Bob");
INSERT INTO users (name) VALUES ("Vicky");
INSERT INTO users (id, name) VALUES (3, "Vicky");
SELECT * FROM users;

SELECT * FROM users WHERE id = 3;
SELECT name FROM users WHERE name = "Bob";
DELETE FROM users WHERE id = 3;
DELETE FROM users WHERE name = "Vicky";
```

## Importing and Exporting data 

```bash
mysqldump -uadmin -hlocalhost -p********** tutorial1
mysqldump -uadmin -hlocalhost -p********** tutorial1 > temp.sql

mysqldump --host localhost --user admin --password --lock-tables tutorial1 users > /tmp/users.sql
```

## Export CSV

```bash
mysql localhost -u admin -p tutorial1 -p << EOQ | sed -e 's/        /,/g' | tee users.csv
select * 
from users
EOQ
```

```sql
CREATE DATABASE temp;
```

```bash
mysql -uadmin -hlocalhost -p********** temp < temp.sql
```

## Operators

```sql
USE tutorial1;
DROP TABLE users;
CREATE TABLE users (id INT PRIMARY KEY AUTO_INCREMENT, name TEXT, age INT);
INSERT INTO users (name, age) VALUES ("Bob", 46);
SELECT COUNT(*) users;
SELECT * FROM users WHERE age < 30;
SELECT * FROM users WHERE age > 30;
SELECT * FROM users WHERE age <= 30;
SELECT * FROM users WHERE name != "Bob";
SELECT * FROM users WHERE name IS NULL;
SELECT * FROM users WHERE name IS NOT NULL;
SELECT * FROM users WHERE name LIKE "%o%";
SELECT * FROM users WHERE name NOT LIKE "%o%";
SELECT * FROM users WHERE age > 30 AND name LIKE '%o%' AND name IS NOT NULL;
SELECT * FROM users WHERE (age < 20 OR age > 30) AND name = 'Bob';
SELECT COUNT(*) FROM users WHERE NOT ((age < 20) OR (age >= 39));
SELECT * FROM users WHERE age < 46 XOR age < 46; --returns TRUE if one or the other but not both is TRUE
```

## Update, Order and Limit

```sql
UPDATE users SET name = "Jim" WHERE name = "Bob" AND id = 6;
SELECT * FROM users WHERE id < 20 ORDER BY name ASC;
SELECT * FROM users WHERE id < 20 ORDER BY id DESC;
SELECT * FROM users WHERE id < 20 ORDER BY age, name, id;
SELECT * FROM users ORDER BY age LIMIT 5;
SELECT * FROM users ORDER BY age LIMIT 10, 5; --offset from the firt value
SELECT * FROM users ORDER BY age LIMIT 0, 1;
```

## MySQL Data Types

```sql
CREATE TABLE test(zip_code CHAR(7), name VARCHAR(60));
CREATE TABLE test(width FLOAT);
CREATE TABLE test(value DECIMAL(4, 2));
-- FLOAT without precisition, DOUBLE is the double presicition than FLOAT and DECIMAL is about finantial number for exactly presicition
CREATE TABLE test(bitfield BIT(4));
INSERT INTO test (bitfield) VALUES (b'1111');
SELECT BIN(bitfield) FROM test;
CREATE TABLE products (product VARCHAR(100), available BOOLEAN DEFAULT false);
CREATE TABLE images (name VARCHAR(50), data BLOB);
SELECT now(), year(now()), time(now()), date(now());
CREATE TABLE moments (id INT PRIMARY KEY AUTO_INCREMENT, theyear YEAR, thedate DATE, thetime TIME);
CREATE TABLE products (name VARCHAR(60), sold_at TIMESTAMP DEFAULT now(), recived DATETIME);
CREATE TABLE foods (name VARCHAR(50), temperature ENUM('cold', 'hot') DEFAULT 'cold', flavour ENUM('sweet', 'savoury') DEFAULT 'sweet');
```

## Agregate Functions and Grouping

see examples [Agregate Functions and Grouping](Agregate_Functions_and_Grouping/README.md)

## Foreign Keys and multiple Tables

see examples [Foreign Keys and multiple Tables](Foreign_keys_and_multiple_Tables/README.md)

## Combinining Queries

see examples [Combinining Queries](Combinining_Queries/README.md)

## Combinining Queries

see examples [Altering Schemas](Altering_Schemas/README.md)

## Users and Privileges

```sql
CREATE USER `john`@`localhost` IDENTIFIED BY 'C!:B-=h>6+O74A#d';
-- all tables and dastabases privileges
GRANT ALL PRIVILEGES ON *.* TO `john`@`localhost`;
FLUSH PRIVILEGES;
```

How to Reset Password Admin: https://www.cloudsigma.com/how-to-reset-mariadb-or-mysql-root-password/

```sql
--view users
SELECT * FROM mysql.user;
--delete user
DROP USER `john`@`localhost`;
REVOKE ALL PRIVILEGES, GRANT OPTION FROM `john`@`localhost`;
FLUSH PRIVILEGES;
--specific permissions
CREATE USER `john`@`localhost` IDENTIFIED BY 'WW}362=q?&t/,~';
GRANT SELECT ON online_shop.* TO `john`@`localhost`;
SHOW GRANTS FOR 'john'@'localhost';
SELECT USER FROM mysql.user;
```

```sql
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, CREATE VIEW, EVENT, TRIGGER, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, EXECUTE ON `online_shop`.* TO 'john'@'%';
```

## Views

Create a simple View

```sql
USE tutorial1;
CREATE TABLE book(id INT PRIMARY KEY AUTO_INCREMENT, name VARCHAR(50) NOT NULL, notes VARCHAR (100));
INSERT INTO bookview (id, name) VALUES (1, "The Thirty-Nine Steps");

CREATE VIEW bookview AS SELECT id, name FROM book;
SHOW FULL TABLES;
SELECT * FROM bookview;

INSERT INTO bookview (id, name) VALUES (2, "War and Peace");
DROP VIEW bookview;
```

View Algorithms

```sql
USE online_shop;

-- this algorithm is preferred as it allows your view to utilize table indexes, and doesn't introduce a delay in creating temporary table
CREATE ALGORITHM = MERGE VIEW customer_sales1 AS SELECT c.id AS customer_id, sold_at FROM sales s JOIN customers c ON c.id = s.customer_id;

--create on a temporal table
CREATE ALGORITHM = temptable VIEW customer_sales2 AS SELECT c.id  AS customer_id, sold_at FROM sales s JOIN customers c ON c.id = s.customer_id;

--default
CREATE ALGORITHM = undefined view customer_sales3 AS SELECT c.id AS customer_id, sold_at FROM sales s JOIN customers c ON c.id = s.customer_id;
SHOW FULL TABLES;
```

With check option

```sql
USE tutorial1;
CREATE TABLE book(id INT PRIMARY KEY AUTO_INCREMENT, name VARCHAR(50) NOT NULL);
INSERT INTO book (id, name) VALUES (1, "The Thirty-Nine Steps");
CREATE VIEW bookview AS SELECT id, name FROM book WHERE id < 10;
SELECT * FROM bookview;
--can insert data greather than 10, but can't see in this view because for the condition
DROP VIEW bookview;
CREATE VIEW bookview AS SELECT id, name FROM book WHERE id < 10 WITH CHECK OPTION;
INSERT INTO bookview (id, name) VALUES (20, "War and Peace");
```