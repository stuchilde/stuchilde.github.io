---
title: SQL面试常见问题解析(上)
date: 2018-03-20 12:15:45
tags: 
     - mysql
     - 面试
---

### 1.用一条sql语句查出所有的课程得分大于80分的同学姓名。

| name | subject | score |
| :--: | :-----: | :---: |
| 张三 | 语文 | 81 |
| 张三 |数学|75|
| 李四 | 语文 | 76 |
|李四|数学|90|
|王五|语文|81|
|王五|数学|100|
|王五|英语|90|

**这道题目有两种解法：**

*从正面来看，可以以NAME分组然后每组的最低得分如果都大于80分，那么该同学就满足要求*
```Mysql
SELECT NAME FROM students GROUP BY NAME HAVING MIN(score) > 80;
```

*从反面来看，我们可以先选出所有得分低于80分的同学，然后选出所有不属于前面结果集的同学即可*


```Mysql
SELECT DISTINCT(NAME) FROM students where name not in (SELECT NAME FROM students where score < 80);
```

<!-- more -->

### 2.删除除了自动编号不同, 其他都相同的学生冗余信息

| auto_no | no   | name | subject_no |subject | score |
| :-----: | :--: | :--: | :--: | :--: | :--: |
| 1 | 2018001 | 张三 |0001|数学|69|
|2|2018002|李四|0002|语文|72|
|3|2018001|张三|0001|数学|69|

*这道题目就是删除不在分组(no, name, subject_no, subject, score)的行*

```Mysql
DELETE FROM `school` where auto_no not in (SELECT MAX(auto_no) FROM `school` GROUP BY no, name, subject_no, subject, score);

DELETE FROM `school` where auto_no not in (SELECT MAX(t.auto_no) FROM (SELECT auto_no, no, name, subject_no, subject, score FROM `school` ) as t GROUP BY no, name, subject_no, subject, score);
```



###  3.一个叫team的表，里面只有一个字段name，一共有4条记录，分别是a,b,c,d对应四个球队，现在四个球队进行比赛，用一条sql语句显示所有的可能比赛组合。


表结构如下:

| Name |
| :--: |
|  a   |
|  b   |
|  c   |
|  d   |

执行下面代码就可以得到结果：

```mysql
SELECT a.name, b.name FROM `team` AS a, `team` AS b where a.name != b.name;
```

| name | name |
| ---- | ---- |
| b    | a    |
| c    | a    |
| d    | a    |
| a    | b    |
| c    | b    |
| d    | b    |
| a    | c    |
| b    | c    |
| d    | c    |
| a    | d    |
| b    | d    |
| c    | d    |




### 4.请用SQL 语句实现：从JcyAudit 数据表中查询出所有月份的发生额都比101 科目相应月份的发生额高的科目。请注意：JcyAudit 中有很多科目，都有1 －12 月份的发生额。

  AccID ：科目代码，Occmonth ：发生额月份，DebitOccur ：发生额。
  数据库名：JcyAudit ，数据集：Select * from TestDB


**解决sql语句复杂查询最好是要分步进行**

1. 先查询出所有的AccID = 101的行

   ```Mysql
   SELECT * FROM JcyAudit WHERE AccID = 101;
   ```

   ​

2. 然后查询出所有相同月份但是发生额大于101科目的行

   ```Mysql
   SELECT a.Occmonth, a.AccID FROM JcyAudit AS a, (SELECT * FROM JcyAudit WHERE AccID = 101) AS b WHERE a.Occmonth = b.Occmonth AND a.DebitOccur > b.DebitOccur;
   ```

3.  最后统计上面的查询结果

   ```mysql
   SELECT c.AccID FROM (
       SELECT a.Occmonth, a.AccID FROM JcyAudit AS a, (SELECT * FROM JcyAudit WHERE AccID = 101) AS b WHERE a.Occmonth = b.Occmonth AND a.DebitOccur > b.DebitOccur
   ) AS c GROUP BY c.AccID HAVING count(c.AccID) = 12;
   ```

   ​


### 5.面试题：怎么把这样一个表

|year |  month| amount|
|:--:|:--:|:--:|
|1991  | 1  |   1.1|
|1991  | 2  |   1.2|
|1991  | 3  |   1.3|
|1991  | 4  |   1.4|
|1992  |  1 |   2.1|
|1992  |  2 |   2.2|
|1992  |  3 |   2.3|
|1992  |  4 |   2.4|
**查成这样一个结果**

| year | m1  | m2 |  m3 |  m4 |
|:--:|:--:|:--:|:--:|:--:|
|1991 |1.1  | 1.2 | 1.3 | 1.4|
|1992| 2.1 | 2.2 | 2.3 | 2.4 |

```Mysql
SELECT `year`, 
(SELECT amount FROM total AS t WHERE t.year = total.year AND month = 1) AS m1,
(SELECT amount FROM total AS t WHERE t.year = total.year AND month = 2) AS m2,
(SELECT amount FROM total AS t WHERE t.year = total.year AND month = 3) AS m3,
(SELECT amount FROM total AS t WHERE t.year = total.year AND month = 4) AS m4
FROM total GROUP BY year;
```

