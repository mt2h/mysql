## Exclusive Table Locks

Conection 1

```sql
USE online_shop;
LOCK TABLES categories WRITE;
--LOCK TABLES categories, customers WRITE;

--Table 'sales' was not locked with LOCK TABLES, this conextion no response other tables
SELECT * FROM sales;

--this conection can select data also insert too
SELECT * FROM categories;
```

Conection 2

```sql
--don't response because conextion 1 blocked
SELECT * FROM categories;
```

Conection 1

```sql
UNLOCK TABLES;

--now this conection can quering other tables
SELECT * FROM sales;
```

Conection 2

```sql
--now this conection can quering categories
SELECT * FROM categories;
```

## Shared Table Locks

Conection 1

```sql
LOCK TABLES categories READ;

--can't quering and write on customers table
SELECT * FROM customers;
INSERT INTO customers (name) VALUES ('Sweets');
```

Conection 2

```sql
--can quering on customers table
SELECT * FROM customers;

--cann't write on customers table because was block by conection 1
INSERT INTO customers (name) VALUES ('Sweets');

LOCK TABLES categories READ;

UNLOCK TABLES;
```

Conection 1

```sql
UNLOCK TABLES;
```

## Using Variables

```sql
SET @user = "John"; 
SELECT @user;

SET @some_value = 99;
SELECT @some_value;

USE online_shop;
SET @min_value = 8.99;
SELECT * FROM sales WHERE transaction_value > @min_value;
```

## Settings Variables with Selects

```sql
USE online_shop;

CREATE TABLE sales_history
(
  recorded DATETIME,
  total INT NOT NULL
);

SELECT @total := SUM(transaction_value), @min_value := MIN(transaction_value) FROM sales;

SELECT @total;
SELECT @min_value;
```

Select-Update Example

```sql
SELECT @total := sum(transaction_value) FROM sales;
insert into sales_history (recorded, total) VALUES (NOW(), @total);
SELECT * FROM sales_history;

EXPLAIN INSERT INTO sales_history (recorded, total) VALUES (NOW(), (SELECT sum(transaction_value) FROM sales));
```

Fixing Select-Updates with Tables Locks

```sql
USE online_shop;

LOCK TABLES sales READ, sales_history WRITE;

SELECT @total := sum(transaction_value) FROM sales;
INSERT INTO sales_history (recorded, total) VALUES (NOW(), @total);

UNLOCK TABLES;

INSERT INTO sales (customer_id, product_id, sold_at, transaction_value) VALUES (1, 1, NOW(), 88.77);

SELECT * FROM sales_history;
```

## ACID InnoDB

https://dev.mysql.com/doc/refman/5.6/en/mysql-acid.html

## Intro about Transaccions

A simple Transaction

```sql
-- commit
USE tutorial1;
SET autocommit=0;
--these datas will be insert after commit command
--only view datas in current conections, no in news conection until these datas will be insert with commit
INSERT INTO book (name) VALUES ('The Journey');
INSERT INTO book (name) VALUES ('The Mountain');
COMMIT;

--rollback
INSERT INTO book (name) VALUES ('The Universe');
DELETE FROM book WHERE id = 3;
UPDATE book SET name = 'The Mountain 2' WHERE name = 'The Mountain';
ROLLBACK;

SELECT * FROM book;
```

Start Transaction

```sql
-- CONNECTION 1
--by default
SET autocommit=1; 

START TRANSACTION;
SELECT * FROM book;
UPDATE book SET name = 'The Valley 2' WHERE id = 1;

-- CONNECTION 2
USE tutorial1;
START TRANSACTION;
SELECT * FROM book;

-- CONNECTION 1
COMMIT;

-- CONNECTION 2
SELECT * FROM book;
```

InnoDB Row Locking and Isolation

```sql
SELECT @@session.tx_isolation;
-- Table Locking
-- Row level locking indexes
-- Read locks (shared) write locks (exclusive)
```

ACID Isolation Levels

```sql
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;
--the lock is in rows with indexes


--SERIALIZABLE is the highest level of the isolation, the other conections can't make something in rows into transactions

--REPEATABLE READ is by default, into transaction here always we watch the same, whihout other changes in other conections, it's can be produce phantom rows 

--READ COMMITTED is more low level of the isolation, if other conection make something and the changes are visible into transaction into the other conection with transaction, level with committes

--READ UNCOMMITTED is as any transaction, is the lowest level of the isolation 
```