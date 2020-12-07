# INSERT


``` sql
CREATE TABLE `test`.`t1` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `c1` varchar(45) COLLATE utf8mb4_bin NOT NULL,
  `c2` varchar(45) COLLATE utf8mb4_bin DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `idx_c1` (`c1`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin COMMENT='表1';

```
