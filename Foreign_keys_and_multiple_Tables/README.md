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

Joins

```sql
--join and cartesian products
SELECT p.id AS person_id, p.name, p.address_id AS person_address_id, a.id AS address_id, a.street FROM person p, address a
WHERE p.address_id=a.id;

--inner join
SELECT p.id AS person_id, p.name, p.address_id AS person_address_id, a.id AS address_id, a.street FROM person p 
INNER JOIN address a ON a.id=p.address_id;

INSERT INTO address (id, street) VALUES (99, 'Nowhere');
INSERT INTO person(name, address_id) VALUES ('No-one', NULL);

-- inner keyword is optional (has no effect)
SELECT name, street FROM person p INNER JOIN address a ON p.address_id = a.id;
--SELECT name, street FROM person p JOIN address a ON p.address_id = a.id

-- outer keyword is optional (has no effect)
SELECT name, street FROM person p LEFT OUTER JOIN address a ON p.address_id = a.id;
--SELECT name, street FROM person p LEFT JOIN address a ON p.address_id = a.id; 

-- outer keyword is optional (has no effect)
SELECT name, street FROM person p RIGHT OUTER JOIN address a ON p.address_id = a.id;
--SELECT name, street FROM person p RIGHT JOIN address a ON p.address_id = a.id;

--Multiple Join
SELECT su.id, su.country, su.age, sm.question AS smoking, e.question AS exercise FROM survey su JOIN smoke sm ON su.smoke=sm.id JOIN exercise e ON e.id=su.id;
```

```sql
DROP TABLE IF EXISTS `person`;
DROP TABLE IF EXISTS `address`;
DROP TABLE IF EXISTS `region`;

CREATE TABLE `region` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(50) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=latin1;

CREATE TABLE `address` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `street` varchar(50) DEFAULT NULL,
  `region_id` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `region_id` (`region_id`),
  CONSTRAINT `address_ibfk_1` FOREIGN KEY (`region_id`) REFERENCES `region` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=latin1;

CREATE TABLE `person` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(50) DEFAULT NULL,
  `address_id` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `address_id` (`address_id`),
  CONSTRAINT `person_ibfk_1` FOREIGN KEY (`address_id`) REFERENCES `address` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=latin1;

INSERT INTO `region` VALUES (1,'Derbyshire'),(2,'Staffodshire'),(3,'Nottinghamshire'),(4,'Yorkshire');
INSERT INTO `address` VALUES (1,'Apple Lane',1),(2,'Broad Street',2),(3,'Church Lane',4);
INSERT INTO `person` VALUES (1,'Anna',1),(2,'Bob',2),(3,'Clare',3),(5,'Arnold',1);
```