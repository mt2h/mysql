# MySQL 

## Commands

Basic Commands

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

Storage Engine and Configuration

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

SQL Mode
```sql
SELECT @@GLOBAL.SQL_MODE;
SELECT @@GLOBAL.SQL_MODE, @@SESSION.SQL_MODE;
SET sql_mode = 'STRICT_ALL_TABLES';
SELECT @@SESSION.SQL_MODE;
SET sql_mode = 'NO_ENGINE_SUBSTITUTION';
```

More Commands

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

Importing and Exporting data 

```bash
mysqldump -uadmin -hlocalhost -p********** tutorial1
mysqldump -uadmin -hlocalhost -p********** tutorial1 > temp.sql
```

```sql
CREATE DATABASE temp;
```

```bash
mysql -uadmin -hlocalhost -p********** temp < temp.sql
```