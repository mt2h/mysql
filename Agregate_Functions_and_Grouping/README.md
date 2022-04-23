Arithmetic

```sql
DROP TABLE IF EXISTS `users`;

CREATE TABLE `users` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` text,
  `age` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=23 DEFAULT CHARSET=latin1;

LOCK TABLES `users` WRITE;

INSERT INTO `users` VALUES (1,'Bob',46),(2,'Bob',47),(3,'Vicky',25),(4,'Raj',21),(5,'Pete',60),(6,'John',41),(7,'Mark',30),(8,'Sue',53),(9,'Don',25),(10,'John',20),(11,'Syed',33),(12,'Christina',28),(13,'Mario',19),(14,'Justin',23),(15,'Clare',49),(16,'Sarah',73),(17,'Pete',28),(18,'Steve',32),(19,'Zoe',36),(20,'Cori',17),(21,NULL,99),(22,NULL,20);

UNLOCK TABLES;

SELECT DISTINCT name FROM users ORDER BY name;
SELECT COUNT(DISTINCT name) FROM users;
SELECT AVG(age) FROM users;
SELECT AVG(DISTINCT age) FROM users;
SELECT MAX(age) FROM users;
SELECT MAX(age)*COUNT(*) FROM users;
SELECT SUM(age) FROM users;
```

The Health Survey Dataset

```sql
DROP TABLE IF EXISTS `concentration`;

CREATE TABLE `concentration` (
  `id` tinyint(4) NOT NULL AUTO_INCREMENT,
  `question` varchar(120) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=latin1;

LOCK TABLES `concentration` WRITE;

INSERT INTO `concentration` VALUES (1,'Very poor; I am easily distracted or have trouble thinking clearly.'),(2,'Not very good, but I can concentrate when really necessary.'),(3,'My ability to concentrate is around average.'),(4,'I can concentrate rather well.'),(5,'I have very good concentration and focus.');

UNLOCK TABLES;

DROP TABLE IF EXISTS `drink`;

CREATE TABLE `drink` (
  `id` tinyint(4) NOT NULL AUTO_INCREMENT,
  `question` varchar(120) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=latin1;

LOCK TABLES `drink` WRITE;

INSERT INTO `drink` VALUES (1,'I don\'t drink alcohol'),(2,'Occasionally'),(3,'I drink every day, but moderately'),(4,'I\'m a heavy drinker');

UNLOCK TABLES;

DROP TABLE IF EXISTS `exercise`;

CREATE TABLE `exercise` (
  `id` tinyint(4) NOT NULL AUTO_INCREMENT,
  `question` varchar(120) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=latin1;

LOCK TABLES `exercise` WRITE;

INSERT INTO `exercise` VALUES (1,'I avoid exercise'),(2,'Sometimes / Occasionally'),(3,'I exercise at least weekly'),(4,'I exercise daily');

UNLOCK TABLES;

DROP TABLE IF EXISTS `fat`;

CREATE TABLE `fat` (
  `id` tinyint(4) NOT NULL AUTO_INCREMENT,
  `question` varchar(120) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=latin1;

LOCK TABLES `fat` WRITE;

INSERT INTO `fat` VALUES (1,'I follow a low-fat diet.'),(2,'I try to avoid fat, but my diet isnt really low fat.'),(3,'I think I eat an average amount of fat.'),(4,'I probably eat more fat than average.'),(5,'I eat a high-fat diet and consume a much greater proportion of my calories from fat than the average person.');

DROP TABLE IF EXISTS `health`;

CREATE TABLE `health` (
  `id` tinyint(4) NOT NULL AUTO_INCREMENT,
  `question` varchar(120) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=latin1;

LOCK TABLES `health` WRITE;

INSERT INTO `health` VALUES (1,'I have suffered, or am suffering from, a serious illness during the past year'),(2,'My health has been poor, but I havent been seriously ill.'),(3,'My health has been OK.'),(4,'My health has generally been good.'),(5,'My health has been excellent.');

UNLOCK TABLES;
```