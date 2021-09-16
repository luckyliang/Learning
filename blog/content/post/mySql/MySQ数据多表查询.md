---
layout:     post
title:      "MySQL(三) - 数据多表查询"
subtitle:   ""
description: "这里主要参考官方文档，记录简单用法"
excerpt: ""
date:       2018-10-24 23:32:00
author:     "Cheng"
image: "https://img.zhaohuabing.com/in-post/2018-04-11-service-mesh-vs-api-gateway/background.jpg"
published: true
tags:
    - MySQL
URL: "/2018/10/23/MySqlLeaning03/"

---

 创建数据库Sql语句，数据就自己随填写了

```mysql
DROP DATABASE IF EXISTS test;
CREATE DATABASE test;
USE test;

DROP TABLE  IF EXISTS customer;
DROP TABLE  IF EXISTS company;
#公司表
CREATE TABLE company (
	id INT AUTO_INCREMENT PRIMARY KEY,
	name VARCHAR(20) NOT NULL UNIQUE,
	address VARCHAR(50)
);
#客户表
CREATE TABLE customer (
	id INT AUTO_INCREMENT PRIMARY KEY,
	name VARCHAR(20) NOT NULL,
	age INT,
	company_id INT,
	FOREIGN KEY (company_id) REFERENCES company(id) 
);

```

## 使用WHERE查询

这样相当于查询两个表的**交集**，必须满足`cr.company_id = cy.id`;的数据

`# l ∩ r`

```mysql
SELECT 
cr.name cr_name,
cr.company_id cr_company_id,
cy.name cy_name,
cy.id cy_id
FROM
customer cr, 
company cy
WHERE
cr.company_id = cy.id;
```

## 内连接

`INNER JOIN、CROSS JOIN、JOIN` 在 `MySQL`中，他们是等价的；但是在标准SQL中不是等价的

使用 `JOIN ... ON ...` 连接查询  ,其实就是查询满足ON后面条件的数据（查**交集**）

`# l ∩ r`

![innerjoin](/img/sqlJoins/innerjoin.png)

```mysql
SELECT 
	cr.name cr_name,
	cy.name cy_name
FROM
	customer cr JOIN company cy 
	ON cr.company_id = cy.id
```



## 外连接

### 左外连接`LEFT JOIN` 

`# l ∪ (l ∩ r)` 

`l: Left r:Right  ∪ :并集  ∩:交集`

以左边表a为主，查询a表和b表交集的数据和a独有的数据

![leftJoin01](/img/sqlJoins/leftJoin01.png)

这里就是查询所有雇员的信息，有公司和没公司的雇员都会查询出来

```mysql
SELECT
	* 
FROM
	customer a
	LEFT JOIN company b ON a.company_id = b.id;
```



`# l - (l ∩ r)`

查询a表独有

![leftJoin02](/img/sqlJoins/leftJoin02.png)



```mysql
SELECT
	* 
FROM
	customer a
RIGHT  JOIN company b 
ON a.company_id = b.id 
WHERE a.company_id IS NULL;
#或者 WHERE b.id IS NULL
```



### 右外连接`RIGHT JOIN`

以右边表b为主，查询a表和b表交集的数据和b独有的数据

`# r ∪ (l ∩ r)`

![rightJoin01](/img/sqlJoins/rightJoin01.png)

```mysql
SELECT
	* 
FROM
	customer a
	RIGHT JOIN company b ON a.company_id = b.id;
```

查询b表独有

`# r - (l ∩ r)`

![rightJoin02](/img/sqlJoins/rightJoin02.png)



## 并集

`# l ∪ r` 

`Mysql`中使用`UNION`连接两个并集， 没有如图中的`FULL OUTER JOIN` 写法

![fullJoin](/img/sqlJoins/fullJoin.png)

```mysql
(
SELECT * FROM 
	customer l
LEFT JOIN 
	company r
ON
	l.company_id = r.id
)
UNION
(
SELECT * FROM 
	customer l
RIGHT JOIN 
	company r
ON
	l.company_id = r.id
);

```

`# (l ∪ r) - (l ∩ r)`

![fullJoin02](/img/sqlJoins/fullJoin02.png)

```mysql
# (l ∪ r) - (l ∩ r)
(
SELECT * FROM 
	customer l
LEFT JOIN 
	company r
ON
	l.company_id = r.id
WHERE
	r.id IS NULL
)
UNION
(
SELECT * FROM 
	customer l
RIGHT JOIN 
	company r
ON
	l.company_id = r.id
WHERE
	l.company_id IS NULL
);

```

## 其他类型

![towInnerJoins](/img/sqlJoins/towInnerJoins.png)

![twoFullOuterJoins](/img/sqlJoins/twoFullOuterJoins.png)

![innerJoinAndLeftOuterJoin](/img/sqlJoins/innerJoinAndLeftOuterJoin.png)

![twoLeftOuterJoins](/img/sqlJoins/twoLeftOuterJoins.png)