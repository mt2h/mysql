Naming columns and tables

```sql
SELECT id AS survey_id, weight AS weight_kg, sugar AS sugar_intake_score, health AS health_core  FROM survey;
SELECT s.id AS survey_id, s.weight AS weight_kg, s.sugar AS sugar_intake_score, s.health AS health_core  FROM survey AS s;
```

Foreign Keys

```sql
CREATE DATABASE people;
USE people;

CREATE TABLE address (id INT PRIMARY KEY auto_increment, street VARCHAR(50));
INSERT INTO address (street) VALUES ('Apple Lane'), ('Broad Street'), ('Church Lane');

CREATE TABLE person(id INT PRIMARY KEY auto_increment, name VARCHAR(50), address_id INT, FOREIGN KEY (address_id) REFERENCES address(id));

INSERT INTO person(name, address_id) VALUES ('Anna', 1);
INSERT INTO person(name, address_id) VALUES ('Bob', 2);
INSERT INTO person(name, address_id) VALUES ('Clare', 3);
INSERT INTO person(name, address_id) VALUES ('Arnold', 1);

DESCRIBE person;
```