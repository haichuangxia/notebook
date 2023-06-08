

常见的查询：

## 投影查询

投影查询：查询并返回指定列，过滤不需要使用的列

SELECT id, score, name FROM students;

条件查询：查询并返回所有的列

SELECT * FROM students WHERE score >= 80;



where：用于条件查询

select *：返回所有列

select id：投影

分组聚合：

对查询结构先进行分组，然后再对各组进行聚合。（有优先级）

SELECT COUNT(*) num FROM students GROUP BY class_id;



## 连接查询

连接查询是另一种类型的多表查询。连接查询对多个表进行JOIN运算，简单地说，就是先确定一个主表作为结果集，然后，把其他表的行有选择性地“连接”在主表结果集上。



- INNER JOIN:返回两个表中都有的行。（交集）

- RIGHT OUTER JOIN：返回右表中有的行，把左表的字段拼过去
- LEFT OUTER JOIN：返回左表中有的行，把右表的字段拼过去
- FULL OUTER JOIN：返回左表或右表中有的行，把另一个表的拼过去（并集）