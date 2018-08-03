# 成绩分组取最大值问题



#### 描述

一张班级成绩表 （科目，分数，同学），现需要查出各科目 最高分的同学；

#### 数据库表结构

```text
create table grade2(
  name char(8) not null,
  subject char(8) not null,
  grade int not null
);
```

#### 数据库中数据

| name | subject | grade |
| :--- | :--- | :--- |
| 1 | lang | 90 |
| 2 | lang | 87 |
| 3 | lang | 56 |
| 4 | lang | 234 |
| 5 | lang | 23 |
| 5 | english | 43 |
| 4 | english | 32 |
| 3 | english | 21 |
| 2 | english | 45 |
| 1 | english | 64 |
| 1 | math | 23 |
| 2 | math | 12 |
| 3 | math | 4 |
| 4 | math | 43 |
| 5 | math | 43 |

#### 解

**错误示范**

```text
SELECT name,max(grade),subject FROM grade2 GROUP BY subject;
```

| name | max\(grade\) | subject |
| :--- | :--- | :--- |
| 5 | 64 | english |
| 1 | 234 | lang |
| 1 | 43 | math |

如上所示，该sql语句虽然能查出最大的分数，但是其他列的数据对应关系是错的。而且预期希望查出的math最大分数应该有两个.

**解答1**

将grade2 表分组后作为新的表，用表联结去查询。

```text
SELECT a.name,a.subject,a.grade FROM grade2 AS a 
RIGHT OUTER JOIN 
(SELECT max(grade) AS grade , subject FROM grade2 GROUP BY subject) AS b
ON a.grade = b.grade AND a.subject = b.subject;
```

| name | subject | grade |
| :--- | :--- | :--- |
| 4 | lang | 234 |
| 1 | english | 64 |
| 4 | math | 43 |
| 5 | math | 43 |

**解答2**

使用**not exists**

```text
SELECT * FROM grade2 a
WHERE 
  (
    NOT EXISTS(SELECT 1 FROM grade2 c where c.subject = 'math' AND c.grade > a.grade) AND a.subject = 'math')
  )
OR
  (
    NOT EXISTS(SELECT 1 FROM grade2 c WHERE c.subject = 'lang' AND c.grade > a.grade) AND a.subject = 'lang'
  )
OR
  (
    NOT EXISTS(SELECT 1 FROM grade2 c WHERE c.subject = 'english' AND c.grade > a.grade) AND a.subject = 'english'
  )
```

| name | subject | grade |
| :--- | :--- | :--- |
| 4 | lang | 234 |
| 1 | english | 64 |
| 4 | math | 43 |
| 5 | math | 43 |
|  |  |  |

上面那个语句还可以写为

```text
#SQL3
SELECT * FROM grade2 a WHERE
NOT EXISTS
(
    SELECT 1 FROM grade2 c where c.subject = a.subject AND c.grade > a.grade
);
```

#### 效率 {#效率}

为了测试以上三条语句的效率，我向数据库中插入了3万条数据。

按照顺序，三条语句的执行时间分别为

* SQL1,用时0.07sec
* SQL2,用时1.04sec
* SQL3,用时0.44sec

可以看出,exists函数的效率是十分低下的。

![](../../.gitbook/assets/sql1%20%281%29.png)

![](../../.gitbook/assets/sql2%20%281%29.png)

![](../../.gitbook/assets/sql3%20%281%29.png)

