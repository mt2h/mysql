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
SELECT @@transaction_ISOLATION;
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

Serializable and Row vs Table Locking

```sql
-- conection 1
USE people;

UPDATE person SET name = 'Vic' WHERE id = 1;
UPDATE person SET name = 'Bob' WHERE id = 2;
UPDATE person SET name = 'John' WHERE id = 3;

DELETE FROM person_product WHERE person_id = 5;
DELETE FROM person WHERE id = 5;

SHOW INDEX IN person;

SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE;

START TRANSACTION;

SET sql_safe_updates = 0; --It's useful for precaution to preventdeleting a bunch of rows at once.

SELECT * FROM person WHERE id = 1;

--COMMIT;

-- conection 2
USE people;
SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE;

START TRANSACTION;

SET sql_safe_updates = 0; --It's useful for precaution to preventdeleting a bunch of rows at once.

SELECT * FROM person;

UPDATE person SET name = 'Linda' WHERE id = 1; --don't work because is locked by connection 1

--COMMIT;
```

_Note:_
* _The other conexction can execute select, update, insert comand when the other conection makes a commit_
* _Isolation level serializable locks the rows according condition in the where_

```sql
-- conection 1
START TRANSACTION;
SELECT * FROM person WHERE id = 1;

-- conection 2
START TRANSACTION;
UPDATE person SET name = 'Roger' WHERE id = 2;

COMMIT;
```

_Note:_
_In this case is posible that conection 2 makes a update because conection 1 only have locks row with id equal to 1_

```sql
-- conection 1
START TRANSACTION;
SELECT * FROM person;
SELECT * FROM person WHERE name = 'Roger';

-- conection 2
START TRANSACTION;
UPDATE person SET name = 'Roger' WHERE id = 3;

-- conection 1
UPDATE person SET name = 'Clare' WHERE id = 3; --it's not possible
```

_Note:_
_After commit conection 2, conection 1 can makes update
_After commit conection 1, conection 2 sees update makes by conection 1

Rolling Back to Savepoints

```sql
USE tutorial1;

START TRANSACTION;

SELECT * FROM book;
INSERT INTO book (name) VALUES ('The horror');
SELECT * FROM book;
DELETE FROM book WHERE id = 22;
SELECT * FROM book;
SAVEPOINT test1;

UPDATE book SET name = 'The Forest' WHERE id = 1;
ROLLBACK to test1; --ignore steps after savepoint
SELECT * FROM book;


ROLLBACK; --ignore all
SELECT * FROM book;
```

```sql
--Conection 1
USE tutorial1;
START TRANSACTION;
SELECT * FROM book;
SAVEPOINT test1;

UPDATE book SET name = 'The Amazing Universe' WHERE id = 1;
SELECT * FROM book;

ROLLBACK to test1; --ignore steps after savepoint
SELECT * FROM book;

--Conection 2
USE tutorial1;
START TRANSACTION;
UPDATE book SET name = 'The Chaotic Universe' WHERE id = 1;
SELECT * FROM book;

--Conection 1
ROLLBACK;
```

_Note:_
_Savepoint doesn't release the transaction, for this the transaction must do commit or rollback 


The Account Transfer Problem

```sql
CREATE DATABASE test;
USE test;

CREATE TABLE accounts(
  id INT PRIMARY KEY AUTO_INCREMENT,
  balance NUMERIC(10.2) DEFAULT 0
  );

INSERT INTO accounts (balance) VALUES (1000.00);
INSERT INTO accounts (balance) VALUES (1000.00);

SELECT * FROM accounts;

SET @transfer = 200.00;

START TRANSACTION;

UPDATE accounts SET balance = balance - @transfer WHERE id = 1;
UPDATE accounts SET balance = balance + @transfer WHERE id = 2;

COMMIT;

SELECT * FROM accounts;
```

Select for Update

```sql
--connection 1
USE test;

UPDATE accounts SET balance = 800.00;

SELECT * FROM accounts;

START TRANSACTION;

SET @withdraw = 500;
SET @account = 1;

--SELECT balance FROM accounts WHERE id=@account;
SELECT balance FROM accounts WHERE id=@account for UPDATE;

-- Check that the balance is bigger than the withdrawal amount

UPDATE accounts SET balance = balance - @withdraw WHERE id=@account;

COMMIT;

SELECT * FROM accounts;

--connection 2
USE test;

SELECT * FROM accounts;

START TRANSACTION;

SET @withdraw = 500;
SET @account = 1;

--SELECT balance FROM accounts WHERE id=@account;
SELECT balance FROM accounts WHERE id=@account for UPDATE;

UPDATE accounts SET balance = balance - @withdraw WHERE id=@account;

COMMIT;

SELECT * FROM accounts;
```

Lock in Share Mode

```sql
USE tutorial1;

CREATE TABLE library(
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(50)
  );

ALTER TABLE book ADD COLUMN library_id INT AFTER name;
ALTER TABLE book ADD CONSTRAINT fk_book_library FOREIGN KEY (library_id) REFERENCES library(id);

INSERT INTO library (name) VALUES ('York'), ('Nottingham'), ('Manchester');

DELETE FROM book;

INSERT INTO book (name, library_id) VALUES ('Professor Smith Experiment', 1), ('Java for Absolute Beginners', 1), ('How to Became Talier', 1), ('Training Dogs for Beginners', 2), ('I AM Not a Robot', 3);
 
--conection 1
START TRANSACTION;

SELECT id FROM library WHERE name="Nottingham" LOCK IN SHARE MODE;

INSERT INTO book (name, library_id) VALUES ("Painting for Beginners", 2);

commit;

--conection 2
USE tutorial1;

DELETE FROM library WHERE id = 2;
```