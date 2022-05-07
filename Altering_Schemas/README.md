Adding Columns

```sql
USE people;
DESC person;

ALTER TABLE person DROP COLUMN name;
ALTER TABLE person ADD COLUMN name VARCHAR(50) NOT NULL AFTER id;
--ALTER TABLE person ADD COLUMN name VARCHAR(50) NOT NULL FIRST;

DESC person;
```

Adding Foreign Keys

```sql
USE temp;

CREATE TABLE book (id INT PRIMARY KEY AUTO_INCREMENT, name VARCHAR(50), library INT);
CREATE TABLE library(id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(50));

INSERT INTO library (name) VALUES ('Cardiff Church Road'), ('Nottingham Orchard Way');

ALTER TABLE book ADD CONSTRAINT fk_book_library FOREIGN KEY (library) REFERENCES library(id);

INSERT INTO book (name, library) VALUES ('The 39 Steps', 1);

ALTER TABLE book DROP FOREIGN KEY fk_book_library;
```

Adding Indexes

```sql
--primary key has an index by default
ALTER TABLE book ADD INDEX idx_library(library);

--ALTER TABLE book DROP FOREIGN KEY fk_book_library;
ALTER TABLE book DROP INDEX idx_library;
```

Indexes on Multiple Columns

```sql
ALTER TABLE book ADD INDEX idx_library(library, name);
```