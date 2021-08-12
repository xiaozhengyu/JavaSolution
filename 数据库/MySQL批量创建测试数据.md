# MySQL批量创建测试数据

---

### 表结构

```mysql
CREATE TABLE `test`  (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `value` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `remark` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;
```

### 存储过程

```mysql
CREATE DEFINER=`root`@`localhost` PROCEDURE `insert_test`(IN max_num INT(10))
BEGIN
    DECLARE i INT DEFAULT 0;
    SET autocommit = 0;
    
    REPEAT       
        SET i = i + 1;
	    INSERT INTO test(`value`) VALUES (RAND());				
     UNTIL i = max_num END REPEAT;

	COMMIT;
END
```

