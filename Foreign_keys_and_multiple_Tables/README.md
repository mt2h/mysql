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

--multiple Join
SELECT su.id, su.country, su.age, sm.question AS smoking, e.question AS exercise FROM survey su JOIN smoke sm ON su.smoke=sm.id JOIN exercise e ON e.id=su.id;
```

```sql
USE people;

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

Queryng chains of tables

```sql
USE people;

SELECT * 
FROM person p 
JOIN address a ON p.address_id = a.id
JOIN region r ON a.region_id = r.id;

--many to many
CREATE TABLE product (id INT PRIMARY KEY auto_increment, name VARCHAR(50));

INSERT INTO product (name) VALUES ('Electric cat groomer'), ('Automatic dog chaser'), ('Egg warmer');

CREATE TABLE person_product (person_id INT NOT NULL, product_id INT NOT NULL,  FOREIGN KEY (person_id) REFERENCES person(id),  FOREIGN KEY (product_id) REFERENCES product(id));

DESCRIBE person_product;

INSERT INTO person_product (person_id, product_id) VALUES (1, 3), (5, 2), (1, 3), (1, 3), (2, 1), (3, 2);

SELECT p.name, pp.product_id, pr.name 
FROM person p 
JOIN person_product pp ON pp.person_id = p.id  
JOIN product pr ON pr.id = pp.product_id;

--join tables to themselves
CREATE DATABASE cinema;

CREATE TABLE seats (id INT PRIMARY KEY auto_increment, free BOOLEAN);

INSERT INTO seats (free) VALUES (true), (false), (true), (true), (false), (true), (false), (true), (true), (false), (true);

SELECT s1.id AS 'Seat One ID' 
FROM seats s1 
JOIN seats s2 ON s1.id+1 = s2.id 
WHERE s1.free = TRUE AND s2.free = TRUE;

--restrict
CREATE DATABASE online_shop;

USE online_shop;

CREATE TABLE kingdom (
  id INT(11) NOT NULL AUTO_INCREMENT,
  name VARCHAR(50) NOT NULL,
  PRIMARY KEY (id)
);

CREATE TABLE organism (
  id INT(11) NOT NULL AUTO_INCREMENT,
  name VARCHAR(50) NOT NULL,
  kingdom_id INT(11) DEFAULT NULL,
  PRIMARY KEY (id),
  KEY kingdom_id (kingdom_id),
  CONSTRAINT organism_ibfk_1 FOREIGN KEY (kingdom_id) REFERENCES kingdom (id) ON DELETE RESTRICT ON UPDATE RESTRICT
);

CREATE TABLE individual (
  id INT(11) NOT NULL AUTO_INCREMENT,
  name VARCHAR(50) NOT NULL,
  organism_id INT(11) NOT NULL,
  PRIMARY KEY (id),
  KEY organism_id (organism_id),
  CONSTRAINT individual_ibfk_1 FOREIGN KEY (organism_id) REFERENCES organism (id) 
ON DELETE CASCADE
ON UPDATE CASCADE
);

INSERT INTO kingdom VALUES (1,'plant'),(2,'animal'),(3,'fungi');
INSERT INTO organism VALUES (1,'dog',2),(2,'mushroom',3);
INSERT INTO individual VALUES (3,'Fido',1),(4,'Freddy',2);

--can't update beacusae this contrainst is RESTRICT
UPDATE kingdom SET id = 4 WHERE id = 2;

-- cascade foreing keys
DROP TABLE organism;
DROP TABLE individual;

CREATE TABLE organism (
  id INT(11) NOT NULL AUTO_INCREMENT,
  name VARCHAR(50) NOT NULL,
  kingdom_id INT(11) DEFAULT NULL,
  PRIMARY KEY (id),
  KEY kingdom_id (kingdom_id),
  CONSTRAINT organism_ibfk_1 FOREIGN KEY (kingdom_id) REFERENCES kingdom (id) ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE individual (
  id INT(11) NOT NULL AUTO_INCREMENT,
  name VARCHAR(50) NOT NULL,
  organism_id INT(11) NOT NULL,
  PRIMARY KEY (id),
  KEY organism_id (organism_id),
  CONSTRAINT individual_ibfk_1 FOREIGN KEY (organism_id) REFERENCES organism (id) 
ON DELETE CASCADE
ON UPDATE CASCADE
);

INSERT INTO organism VALUES (1,'dog',2),(2,'mushroom',3);
INSERT INTO individual VALUES (3,'Fido',1),(4,'Freddy',2);

SELECT * 
FROM individual i 
JOIN organism o ON o.id = i.organism_id 
JOIN kingdom k ON k.id = o.kingdom_id;

--delete all the relationship about the other tables
DELETE FROM kingdom WHERE id = 2;
```

Exercises

```sql
-- Online shop

-- Use foreign keys
-- Keep it fairly simple (unless you really want to get stuck into it)

-- List these things:
-- product
-- product categories
-- customers
-- record all sales including time of sale
-- Don't use enum for any potentially long lists, like products or categories

-- Write a join query that lists all the sales and all associated information
-- Create some join queries that retrieve other information; whatever you can think up (e.g a list of products by number of sales, top selling product first)
DROP DATABASE online_shop;
CREATE DATABASE online_shop;
USE online_shop;

CREATE TABLE categories 
(
  id INT PRIMARY KEY AUTO_INCREMENT, 
  name VARCHAR(50) NOT NULL
);

INSERT INTO categories (name) VALUES ('Books'), ('Films'), ('Music');
	
CREATE TABLE products 
(
  id INT PRIMARY KEY AUTO_INCREMENT, 
	name VARCHAR(100) NOT NULL, 
  price DECIMAL(7, 2) NOT NULL, 
  category_id INT NOT NULL, 
	quantity_available INT DEFAULT 0,
	FOREIGN KEY (category_id) REFERENCES categories(id)
);
	
INSERT INTO products (name, price, category_id, quantity_available) VALUES
('The 39 Steps', 2.99, 1, 200),
('The Exorcist', 6.99, 1, 100),
('The Man Who Mistook His Wife for a Hat', 7.99, 1, 200),
('Withnail and I', 8.99, 2, 400),
('Before Sunrise', 8.99, 2, 300),
('Groundhog Day', 7.99, 2, 200),
('Easy Rider', 6.99, 2, 300),
('David Bowie Greatest Hits', 9.99, 3, 200),
('Depeche Mode Greatest Hits', 9.99, 3, 200);

CREATE TABLE customers 
(
  id INT PRIMARY KEY AUTO_INCREMENT, 
  name VARCHAR(100) NOT NULL,
  email VARCHAR(80) NOT NULL
);

INSERT INTO  customers (name, email) VALUES
('Chris Walken', 'chris@caveofprogramming.com'),
('Mike Mikkelson', 'mike@quantumlifetime.com'),
('Rog Blake', 'blake@fascinatingexperiments.com');

CREATE TABLE sales 
(
  id INT PRIMARY KEY AUTO_INCREMENT, 
  customer_id INT NOT NULL,
  product_id INT NOT NULL,
  sold_at DATETIME,
  transaction_value DECIMAL(7, 2) DEFAULT 0,
  FOREIGN KEY (customer_id) REFERENCES customers(id),
  FOREIGN KEY (product_id) REFERENCES products(id)
);

INSERT INTO sales (customer_id, product_id, sold_at, transaction_value)
VALUES
(1, 4, '2015-03-13 12:24:43', 8.99),
(2, 2, '2015-03-12 11:04:12', 6.99),
(1, 7, '2015-03-12 11:14:12', 6.99),
(3, 2, '2015-03-11 11:05:12', 6.99),
(3, 8, '2015-03-12 11:07:12', 9.99),
(1, 2, '2015-03-11 11:14:12', 6.99),
(2, 4, '2015-03-12 11:04:12', 8.99),
(1, 1, '2015-03-14 11:34:12', 2.99);

SELECT * 
FROM customers c 
JOIN sales s ON s.customer_id = c.id 
JOIN products p ON p.id = s.product_id 
JOIN categories cat ON cat.id = p.category_id;

SELECT COUNT(*) AS `Total Sales`, p.name AS Product, c.name AS Category
FROM sales s
JOIN products p ON p.id = s.product_id 
JOIN categories c ON c.id = p.category_id
GROUP BY product_id ORDER BY COUNT(*) DESC;
```