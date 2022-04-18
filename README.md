# MySQL 

## Commands

Basic Commands

```bash
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

```bash
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
```bash
SELECT @@GLOBAL.SQL_MODE;
SELECT @@GLOBAL.SQL_MODE, @@SESSION.SQL_MODE;
SET sql_mode = 'STRICT_ALL_TABLES';
SELECT @@SESSION.SQL_MODE;
SET sql_mode = 'NO_ENGINE_SUBSTITUTION';
```
