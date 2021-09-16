---
layout:     post
title:      "MySQL(一) - 基本使用"
subtitle:   ""
description: "这里主要参考官方文档，记录简单用法"
excerpt: ""
date:       2018-10-23 23:32:00
author:     "Cheng"
image: "https://img.zhaohuabing.com/in-post/2018-04-11-service-mesh-vs-api-gateway/background.jpg"
published: true
tags:
    - MySQL
URL: "/2018/10/23/MySqlLeaning01/"

---

### 

## MySQL安装和连接

安装就不介绍了，网上多的很，这里主要参考[官方文档](https://dev.mysql.com/doc/refman/8.0/en/)，记录简单用法

### **连接mysql**

```mysql
mysql -h host -u user -p
Enter password: ********
```

host 连接的服务，我本地调式就是localhost，user：用户名，安装的时候默认用的是root, 然后输入密连接

在同一台机器上使用可以省略host直接使用下面命令连接

```mysql
mysql -u user -p
Enter password: ********
```

### **断开连接**

```mysql
mysql> QUIT
Bye
```

在mac上使用 `control + D`快捷键也可退出

**换行和返回上一步**

sql语句使用`；`分号结束，如果不写；直接回车只是换行

```mysql
mysql> SELECT
    -> USER()
```

返回上一步`\c`

```mysql
mysql> SELECT
    -> USER()
    -> \c //直接返回上一行
mysql>
```

### 创建数据库

**显示所有数据库`SHOW DATABASES;`**

```mysql
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| classicmodels      |
| information_schema |
| menagerie          |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
6 rows in set (0.00 sec)

mysql> 
```

**使用某个数据库 `USE classicmodels**

```mysql
mysql> use classicmodels
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> 
```

**创建和选择一个数据库**

```mysql
mysql> CREATE DATABASE menagerie;
```

**使用刚刚创建的这个数据库**

```mysql
mysql> USE menagerie
Database changed
```

## SQL语句

### SQL语句主要可以分为4大类

- DDL（`Data Definition Lauguage`）
  - 数据定义语言
  - 创建（`CREATE`）、修改（`ALTER`）、删除（`DROP`）**数据库\表**
- DQL （`Data Query Language`）
  - 数据查询语句
  - 查询记录（`SELECT`）
- DML （`Data Manipilation Language` ）
  - 数据库操纵语言
  - 增加（`INSERT`）、删除（`DELETE`）、修改（`UPDATE`） **记录**
- DCL （`Data Control Language`）
  - 数据库控制语言
  - 控制访问权限（`GRANT、REVOKE`）

每一条语句是分号（`;`）结束

不区分大小写，建议：关键字使用的大写，其他使用小写，单词之间用下划线连接，比如`my_firstname`

单行注释

- `--` 注释内容（后面要预留至少一个空格）
-  `#` 注释内容

多行注释

- `/*注释内容*/`

### DDL语句 - 数据库

#### 创建

`CREATE DATABASE` **数据库名**   	#创建数据库（使用默认的字符编码）

`CREATE DATABASE` **数据库名** CHARACTER SET **字符编码**  	#创建数据库（使用指定的字符编码）

`CREATE DATABASE IF NOT EXISTS` **数据库名** 	#如果这个数据库不存在，才创建

`CREATE DATABASE IF NOT EXISTS`  **数据库名** `CHARACTER SET` **字符编码**

#### 查询

`SHOW DATABASE` 	查询所有的数据库

`SHOW CREATE DATABASE` **数据库名**	#查询数据库的创建语句

`USE` **数据库名** 	#使用数据库

`SELECT DATABASE()` 	#查询正在使用的数据库

#### 修改

`ALTER DATABASE` **数据库名** `CHARACTER SET` **字符编码**	#修改数据库的字符编码

#### 删除

`DROP DATABAS` **数据库名** 

`DROP DATABASE IF EXISTS` **数据库名**  #如果这个数据库存在，才删除

### DDL 语句 - 表

##### 创建（基本语法）

```mysql
CREATE TABLE 表名 {
	列名1 数据类型1，
	...
	列名n 数据类型n
}
```

#### 查询

`SHOW TABLES`	#查询当前数据库的所有表

`DESC` **表名**		#查看表结构

#### 删除

`DROP TABLE` **表名**

`DROP TABLE IF EXISTS` **表名**	

#### 修改

`ALTER TABLE` **表名** `RENAME TO` **新表名**

`ALTER TABLE` **表名**  `CHARACTER SET` **字符集**

`ALTER TABLE` **表名** `ADD` **列名**	#增加新的一列

`ALTER TABLE` **表名** `MODIFY` **列名**	**新数据类型**	#修改某一列的数据类型

`ALTER TABLE` **表名** `CHANGE` **列名** **新列名** **新数据类型**  #修改某一列的列名、数据类型

`ALTER TABLE` **表名** `DROP` **列名**  #删除某一列

### DML语句

#### 增加

`INSERT INTO` **表名** （列名1，列名2, ..., 列名n） `VALUES` (值1，值2，..., 值n)

非数字类型的值，一般需要用引号括住（单引号或双引号，建议使用单引号）

`INSERT INTO` **表名** `VALUES` （值1，值2，...，值n）	#从左至右按顺序给所有列添加值

#### 修改

`UPDATE` **表名** `SET` 列名1 = 值1， 列名2 = 值2，...，列名n = 值n   `WHERE` **条件**

如果没有添加条件，将会修改表中说有记录

#### 删除

`DELETE FROM` **表名**  `WHERE` **条件**

如果没有添加条件将会删除表中所有记录

#### TRUNCATE

如果要删除表的所有数据（保留表结构），有2种常见做法

`DELETE FROM` **表名** 	#逐行删每一条记录

`TRUNCATE` **表名**	#先删除后重新创建表（效率高）

为了实现高性能，他绕过了删除数据的`DML`方法，因此他不能被回滚，不会导致`ON DELETE`触发器触发，并且不能对`InnoDB`具有父子外键关系的表执行

### DQL语句

#### SELECT 语句

```mysql
SELECT DISTINCT	列名1，列名2，... 列名n
FROM 表名
WHERE...
GROUP BY ...
HAVING ...
ORDER BY ...
LIMIT	...
```

`SELECT * FROM customer`	#查询表中所有的记录

`SELECT DISTINCT * FROM customer`	#查询表中的所有记录（去除了重复的记录）

#### 聚合函数

`SELECT COUNT(*) FROM customer`	#查询表中记录总数

`SELECT COUNT（phone）FROM customer` 	#查询表中phone的总数 （不包括NULL）

`SELECT COUNT (DISTINCT phone) FROM customer`	#不包括NULL， 去除了重复的记

`SELECT SUM (salary) FROM customer` #计算所有的salary总和

`SELECT MIN (age) FROM customer`	#查询最小的age

`SELECT MAX (age) FROM customer` 	#查询最大的age 

`SELECT AVG (salary) FROM  customer`	#计算所有的salary的平均值

#### 常见的`WHERE`子句

##### **比较运算**

`WHERE age > 18`

`WHERE age <= 30`

`WHERE age = 20`

`WHERE name = '张三'`

`WHERE age != 25`

`WHERE age <> 25` #不等于25

##### **NULL值判断（不能用=、!= 、<>）**

`WHERE phone IS NULL`	#phone的值为NULL

`WHERE phone IS NOT NULL` 	#phone的值不为NULL

##### **逻辑运算**

`WHERE age > 18 AND age <= 30`

`WHERE age > 18 && age <= 30`

`WHERE age BETWEEN 20 and 30` #age大于等于20且小于等于30

`WHERE age = 18 or age = 20 or age = 22`

`WHERE age IN (18, 20, 22)`	#age等于18或20或22

`WHERE NOT (age < 18)`	#age大于等于18

`WHERE ! (age < 18)`	#age 大于等于18

##### **模式匹配**

`WHERE name LIKE '_码_'`	#name是3个字符并且中间是‘码’字

`WHERE name LIKE '___'`	#name是3个字符

`WHERE name LIKE '李%'` #name以’李‘字开头

`WHERE name LIKE '_码%'`	#name的第二个字符是’码'字

`WHERE name LIKE '%码%'`#name中包含‘码’字

## 常用数据类型

### 数字类型

|           类型            |  字节  |                     有符号取值范围                     |         无符号取值范围         |    用途    |
| :-----------------------: | :----: | :----------------------------------------------------: | :----------------------------: | :--------: |
|   TINYINT\BOOL\BOOLEAN    |   1    |                       -128 ~ 127                       |            0 ~ 255             |  小整数值  |
|         SMALLINT          |   2    |                    -32 768 ~ 32 767                    |           0 ~ 65 535           |  大整数值  |
|         MEDIUMINT         |   3    |                 -8 388 608 ~ 8 388 607                 |         0 ~ 16 777 215         |  大整数值  |
|       INT或INTEGER        |   4    |             -2 147 483 648 ~ 2 147 483 647             |        0 ~ 4294 967 295        |  大整数值  |
|          BIGINT           |   8    | -9 233 372 036 854 775 808 ~ 9 223 372 036 854 775 807 | 0 ~ 18 446 744 073 709 551 615 | 极大整数值 |
|           FLOAT           |   4    |                   储存的小数是近似值                   |                                |            |
|          DOUBLE           |   8    |                   储存的小数是近似值                   |                                |            |
| DECIMAL\DEC\NUMERIC\FIXED | 看情况 |                 存储的小数可以更加精确                 |                                |            |

### 字符串类型

|  类型   |                             描述                             |
| :-----: | :----------------------------------------------------------: |
|  CHAR   |   长度可以指定为0 ~ 255，查询数据时，会省略后面的空白字符    |
| VARCHAR | 长度可以指定为0~65535（较常使用）查询数据时，不会省略后面的空白字符 |
|  BLOB   |          用于储存二进制数据（照片、文件、大文本等）          |
|  TEXT   |                         用于储大文本                         |

### 日期和时间类型

|   类型    | 字节（MySQL 5.6.4之前） | 字节（MySQL 5.6.4开始） |            显示格式             |
| :-------: | :---------------------: | :---------------------: | :-----------------------------: |
|   YEAR    |            1            |            1            |              YYYY               |
|   DATE    |            3            |            3            |           YYYY-MM-DD            |
|   TIME    |            3            |     3 + 小数秒存储      |     HH:MM:SS [ .fraction ]      |
| DATETIME  |            8            |     5 + 小数秒储存      | YYYY-MM-DD HH:MM:SS [.fraction] |
| TIMESTAMP |            4            |     4 + 小数秒储存      | YYYY-MM-DD HH:MM:SS [.fraction] |

**注意**：从`MySQL 5.6.4`开始，允许`TIME/DATETIME/TIMESTAMP`有小数部分。需要0~3字节储存

`DATETIME` 支持范围：1000-01-01 00:00:00.000000 到 9999-12-31 23:59:59.999999

`TIMESTAMP`支持范围：1970-01-01 00:00:01.000000 到 2038-01-19 03:14:07.999999

### DATETIME\TIMESTAMP 的自动设置

- `DEFAULT CURRENT_TIMESTAMP`

  当**插入**记录时，如果没有指定时间值，就设置时间为当前的系统时间

- `ON UPDATE CURRENT_TIMESTAMP`

    当**修改**记录时，如果没有指定时间值，就设置时间为当前系统时间

### 表的复制

- 创建一张拥有相同表结构的空表（只复制表结构，不复记录）

  `CREATE TABLE new_table LIKE old_table`

- 创建一张拥有相同表结构，相同记录的表（复制表结构、复制记录）

  `CREATE TABLE new_table AS (SELECT * FROM old_table)`

## 列的常用属性

- `NOT NULL`: 不能设置为NULL值

- `COMMENT`： 注释

- `DEFALUT`： 默认值（`BLOB、TEXT、GEOMETRY、JSON`类型不能有默认值）

- `AUTO_INCREMENT`: 自动增长
  - 适用于`INT、FLOAT、DOUBLE` 类型
  - 在插入记录时，如果不指定此列的值或设置为NULL，会在此前的基础上自动增长1
  - 不能有默认值（不能使用`DEFAULT`）
  - 在一个表格中，最多只能有一列被设置为`AUTO_INCREMENT`
  - 这一列必须被索引（`UNIQUE、PRIMARY KEY、FOREIGN KEY`等）

### UNIQUE索引

一旦某一列被设置了 `UNIQUE`索引，该列的所有值必须是唯一的，允许存在多个NULL值

**两种常见的写法**

列名 数据类型 `UNIQUE `

`UNIQUE` (列名)

```mysql
CREATE TABLE student(
	id INT UNIQUE
	name VARCHAR(20)
	UNIQUE (name)
);
```

### 主键

主键的作用：可以保证在一张表中的每一条记录都是唯一的

如果将某一列设置为主键，那么这一列相当于加上了` NOT NULL UNIQUE`

建议每一张表都有主键

主键最好跟业务无关，常设置为 `INT AUTO_INCREMENT`

**两种常见写法**

列名 数据类型 `PRIMARY KEY`

```mysql
CREATE TABLE company(
	id INT AUTO_INCREMENT PRIMARY KEY,
	name VARCHAR(20) NOT NULL UNIQUE
);
```

`PRIMARY KEY `(列名)

```mysql
CREATE TABLE company(
	id INT AUTO_INCREMENT,
	name VARCHAR(20) NOT NULL UNIQUE,
	PRIMARY KEY (id)
);
```

### 外键

一般用外键来引用其他表的主键

常见写法

`FOREIGN KEY (列名) REFERENCE 表名（列名）`

常见写法

```mysql
CREATE TABLE company(
	id INT AUTO_INCREMENT PRIMARY KEY,
	name VARCHAR(20) NOT NULL UNIQUE
);

CREATE TABLE customer(
	id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(20) NOT NULL,
  company_id INT NOT NULL,
  FOREIGN KEY (company_id) REFERENCES company(id)
);
```

### 级联

定义外键时，可以设置级联

`ON DELETE CASCADE`

当删除**被引用**的记录时，引用了此记录的其他所有记录都会被自动删除

`ON UPDATE CASCADE`

当修改**被引用**的记录时，引用了此记录的其他所有记录都会被自动更新

```mysql
CREATE TABLE company (
	id INT AUTO_INCREMENT PRIMARY KEY,
	name VARCHAR(20) NOT NULL UNIQUE,
	address VARCHAR(50)
);

CREATE TABLE customer (
	id INT AUTO_INCREMENT PRIMARY KEY,
	name VARCHAR(20) NOT NULL,
	age INT,
	company_id INT,
	FOREIGN KEY (company_id) REFERENCES company(id) ON DELETE CASCAD
  #FOREIGN KEY (company_id) REFERENCES company(id) ON UPDATE CASCADE 更新
  #FOREIGN KEY (company_id) REFERENCES company(id) ON DELETE CASCAD ON UPDATE CASCADE 删除或		更新
);
```

当删除或者更新company时，引用了该company的customer记录也会被删除或者更新

```mysql
DELETE FROM company WHERE id = 1; #customer中company_id为1的记录也会被删除
UPDATE company SET id = 3 WHERE id = 2; #更新
```



### 多表查询

**内连接**

`INNER JOIN、CROSS JOIN、JOIN`

在 `MySQL`中，他们是等价的；但是在标准SQL中，他们并不是等价的

**外连接**

`LEFT JOIN、RIGHT JOIN`

**并集**

`UNION`

MySQL并不支持标准SQL中的 `FULL JOIN` ， 可以用UNION来替代实现

**多表查询**

`ON 和 WHERE`后面都可以跟着条件，他们的区别是：

- `ON`：配合 `JOIN` 语句使用，用以指定如何连接表的条件
- `WHERE`：限制哪些记录出现在结果集中

`INNER JOIN` 和逗号在没有连接条件的情况下，语意上是等价的

- 都在指定的表之间产生笛卡尔乘积
- 也就是说，第一个表中的每一行都连接到第二个表中的第一行

### 排序、分页

##### 排序

`ORDER BY` 字段 [ `ASC | DESC`]

##### 分页

`LIMIT { [offset,] row_count | row_count OFFSET offset }`

- `offset `是记录的偏量（最小值0），从那一条记录开始选择

- `row_count` 是希望选择的记录数量

  `LIMIT 10, 20 ` 或者 `LIMIT 20 OFFSET 10`	表示从第10条记录开始，选择20条记录

### 子查询

当一个查询是另一个查询的条件是，称为**子查询**

`SELECT * FROM customer WHERE company_id = (SELECT id FROM company WHERE name = '腾讯')`

