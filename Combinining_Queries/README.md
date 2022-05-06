Union and Union all

```sql
USE people;
--comine two queries whitout duplicate records
SELECT * FROM person WHERE id >= 2 UNION SELECT * FROM person WHERE id < 2;

SELECT * FROM person p LEFT JOIN address a ON a.id = p.address_id
UNION
SELECT * FROM person p RIGHT JOIN address a ON a.id = p.address_id;

--UNION ALL for whith duplicates
SELECT * FROM person p LEFT JOIN address a ON a.id = p.address_id
UNION ALL
SELECT * FROM person p RIGHT JOIN address a ON a.id = p.address_id;

SELECT * FROM person p LEFT JOIN address a ON a.id = p.address_id WHERE p.address_id IS NULL
UNION ALL
SELECT * FROM person p RIGHT JOIN address a ON a.id = p.address_id;
```

Subqueries with "in"

```sql
SELECT * FROM person WHERE id IN (2, 3, 5);
SELECT * FROM person WHERE id IN (SELECT id FROM person WHERE id > 2);
```

Inline Views

```sql
USE health;

DROP TABLE IF EXISTS `concentration`;

CREATE TABLE `concentration` (
  `id` TINYINT(4) NOT NULL AUTO_INCREMENT,
  `question` VARCHAR(120) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=latin1;

INSERT INTO `concentration` VALUES (1,'Very poor; I am easily distracted or have trouble thinking clearly.'),(2,'Not very good, but I can concentrate when really necessary.'),(3,'My ability to concentrate is around average.'),(4,'I can concentrate rather well.'),(5,'I have  very good concentration and focus.');

DROP TABLE IF EXISTS `drink`;

CREATE TABLE `drink` (
  `id` tinyint(4) NOT NULL AUTO_INCREMENT,
  `question` varchar(120) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=latin1;

INSERT INTO `drink` VALUES (1,'I don\'t drink alcohol'),(2,'Occasionally'),(3,'I drink every day, but moderately'),(4,'I\'m a heavy drinker');

DROP TABLE IF EXISTS `exercise`;

CREATE TABLE `exercise` (
  `id` tinyint(4) NOT NULL AUTO_INCREMENT,
  `question` varchar(120) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=latin1;

INSERT INTO `exercise` VALUES (1,'I avoid exercise'),(2,'Sometimes / Occasionally'),(3,'I exercise at least weekly'),(4,'I exercise daily');

DROP TABLE IF EXISTS `fat`;

CREATE TABLE `fat` (
  `id` tinyint(4) NOT NULL AUTO_INCREMENT,
  `question` varchar(120) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=latin1;

INSERT INTO `fat` VALUES (1,'I follow a low-fat diet.'),(2,'I try to avoid fat, but my diet isnt really low fat.'),(3,'I think I eat an average amount of fat.'),(4,'I probably eat more fat than average.'),(5,'I eat a high-fat diet and consume a much greater proportion of my calories from fat than the average person.');

DROP TABLE IF EXISTS `health`;

CREATE TABLE `health` (
  `id` tinyint(4) NOT NULL AUTO_INCREMENT,
  `question` varchar(120) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=latin1;

INSERT INTO `health` VALUES (1,'I have suffered, or am suffering from, a serious illness during the past year'),(2,'My health has been poor, but I havent been seriously ill.'),(3,'My health has been OK.'),(4,'My health has generally been good.'),(5,'My health has been excellent.');

DROP TABLE IF EXISTS `smoke`;

CREATE TABLE `smoke` (
  `id` tinyint(4) NOT NULL AUTO_INCREMENT,
  `question` varchar(120) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=latin1;

INSERT INTO `smoke` VALUES (1,'I dont smoke'),(2,'Occasionally'),(3,'I smoke daily, but moderately'),(4,'I smoke heavily');

DROP TABLE IF EXISTS `sugar`;

CREATE TABLE `sugar` (
  `id` tinyint(4) NOT NULL AUTO_INCREMENT,
  `question` varchar(120) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=latin1;

INSERT INTO `sugar` VALUES (1,'Very rarely or never'),(2,'Occasionally'),(3,'Dont know / uncertain'),(4,'Often/frequently'),(5,'I am addicted to sweet things / Very often');

DROP TABLE IF EXISTS `survey`;

CREATE TABLE `survey` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `gender` enum('male','female') NOT NULL,
  `country` varchar(30) NOT NULL,
  `weight` decimal(3,0) NOT NULL,
  `height` decimal(3,0) NOT NULL,
  `age` decimal(3,0) NOT NULL,
  `fat` tinyint(4) NOT NULL,
  `sugar` tinyint(4) NOT NULL,
  `smoke` tinyint(4) NOT NULL,
  `drink` tinyint(4) NOT NULL,
  `exercise` tinyint(4) NOT NULL,
  `health` tinyint(4) NOT NULL,
  `concentration` tinyint(4) NOT NULL,
  PRIMARY KEY (`id`),
  KEY `fat` (`fat`),
  KEY `sugar` (`sugar`),
  KEY `smoke` (`smoke`),
  KEY `drink` (`drink`),
  KEY `exercise` (`exercise`),
  KEY `health` (`health`),
  KEY `concentration` (`concentration`),
  CONSTRAINT `survey_ibfk_1` FOREIGN KEY (`fat`) REFERENCES `fat` (`id`),
  CONSTRAINT `survey_ibfk_2` FOREIGN KEY (`sugar`) REFERENCES `sugar` (`id`),
  CONSTRAINT `survey_ibfk_3` FOREIGN KEY (`smoke`) REFERENCES `smoke` (`id`),
  CONSTRAINT `survey_ibfk_4` FOREIGN KEY (`drink`) REFERENCES `drink` (`id`),
  CONSTRAINT `survey_ibfk_5` FOREIGN KEY (`exercise`) REFERENCES `exercise` (`id`),
  CONSTRAINT `survey_ibfk_6` FOREIGN KEY (`health`) REFERENCES `health` (`id`),
  CONSTRAINT `survey_ibfk_7` FOREIGN KEY (`concentration`) REFERENCES `concentration` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=150 DEFAULT CHARSET=latin1;

SELECT AVG(respondents) 
FROM (SELECT country, COUNT(*) AS respondents FROM survey GROUP BY country) AS totals;

SELECT * FROM 
(SELECT country, COUNT(*) AS respondents FROM survey GROUP BY country) AS a 
WHERE country LIKE "A%";
```