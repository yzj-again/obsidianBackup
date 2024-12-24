---
date: 
tags:
  - MYSQL
---
# 1．MySQL概述

在这一章节，我们主要介绍两个部分，数据库相关概念及MySQL数据库的介绍、下载、安装、启动及连接。
## 1.1 数据库相关概念

在这一部分，我们先来讲解三个概念：数据库、数据库管理系统、SQL。
![](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/3b823457aeb4bf8d7c116d11f2c0791c880ca5a59ff4a661bc084161661b759c.jpg)
![Pasted image 20241204173547](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Pasted%20image%2020241204173547.png)

而目前主流的关系型数据库管理系统的市场占有率排名如下：

![Pasted image 20241204173640](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Pasted%20image%2020241204173640.png)
​Oracle：大型的收费数据库，Oracle公司产品，价格昂贵。

MySQL：开源免费的中小型数据库，后来Sun公司收购了MySQL，而Oracle又收购了Sun公司。目前Orac1e推出了收费版本的MySQL，也提供了免费的社区版本。

SQL Server： Microsoft 公司推出的收费的中型数据库，C＃、net等语言常用。

PostgreSQL：开源免费的中小型数据库。

DB2：IBM公司的大型收费数据库产品。

SQLLite：嵌入式的微型数据库。Android内置的数据库采用的就是该数据库。

MariaDB：开源免费的中小型数据库。是MysQL数据库的另外一个分支、另外一个衍生产品，与MySQL数据库有很好的兼容性。

而不论我们使用的是上面的哪一个关系型数据库，最终在操作时，都是使用SQL语言来进行统一操作，因为我们前面讲到SQL语言，是操作关系型数据库的==统一标准==。所以即使我们现在学习的是MySQL，假如我们以后到了公司，使用的是别的关系型数据库，如：oracle、DB2、SQLServer，也完全不用担心，因为操作的方式都是一致的。

## 1.2 MYSQL数据库

### 1.2.1 数据模型
#### 1）关系型数据库（RDBMS）

概念：建立在关系模型基础上，由多张相互连接的二维表组成的数据库。

而所谓二维表，指的是由行和列组成的表，如下图（就类似于 Excel 表格数据，有表头、有列、有行，还可以通过一列关联另外一个表格中的某一列数据）。我们之前提到的MySQL、Oracle、DB2、SQLServer这些都是属于关系型数据库，里面都是基于二维表存储数据的。简单说，==基于二维表存储数据的数据库==就成为关系型数据库，不是基于二维表存储数据的数据库，就是非关系型数据库。

特点：

+ 使用表存储数据，格式统一，便于维护。
+ 使用SQL语言操作，标准统一，使用方便。

![Pasted image 20241204175309](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Pasted%20image%2020241204175309.png)
#### 2）数据模型

MySQL是关系型数据库，是基于二维表进行数据存储的，具体的结构图下：

![Pasted image 20241204175423](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Pasted%20image%2020241204175423.png)

我们可以通过MySQL客户端连接数据库管理系统DBMS，然后通过DBMS操作数据库。

可以使用SQL语句，通过数据库管理系统操作数据库，以及操作数据库中的表结构及数据。

==一个数据库服务器中可以创建多个数据库，一个数据库中也可以包含多张表，而一张表中又可以包含多行记录。==

# 2. SQL

全称 `Structured Query Language`，==结构化查询语言==。操作关系型数据库的编程语言，定义了一套操作关系型数据库统一标准。
## 2.1 SQL通用语法

在学习具体的SQL语句之前，先来了解一下SQL语言的同于语法。

1. SQL语句可以单行或多行书写，以分号结尾。
2. SQL语句可以使用空格／缩进来增强语句的可读性。
3. MySQL数据库的SQL语句不区分大小写，关键字建议使用大写。
4. 注释：
	单行注释：`--` 注释内容或 `#` 注释内容
	多行注释：`/* 注释内容 */`

## 2.2 SQL分类

SQL语句，根据其功能，主要分为四类：DDL、DML、DQL、DCL。

| 分类  | 全称                         | 说明                          |
| --- | -------------------------- | --------------------------- |
| DDL | Data Definition Language   | 数据定义语言，用来定义数据库对象（数据库，表，字段）  |
| DML | Data Manipulation Language | 数据操作语言，用来对数据库表中的数据进行==增删改== |
| DQL | Data Query Language        | 数据查询语言，用来==查询==数据库中表的记录     |
| DCL | Data Control Language      | 数据控制语言，用来创建数据库用户、控制数据库的访问权限 |

## 2.3 DDL

`Data Definition Language`，数据定义语言，用来定义数据库对象（数据库，表，字段）。

### 2.3.1 数据库操作

#### 1）查询所有数据库

```
show databases;
```

查询当前数据库：

```
select database();
```

#### 3）创建数据库

```
CREATE DATABASE [IF NOT EXISTS] 数据库名 [DEFAULT CHARSET 字符集] [COLLATE 排序规则];
```

在同一个数据库服务器中，不能创建两个名称相同的数据库，否则将会报错。

可以通过 `IF NOT EXISTS` 参数来解决这个问题，数据库不存在，则创建该数据库，如果存在，则不创建。

#### 4）删除数据库

```
DROP DATEBASE [IF EXISTS] 数据库名;
```

如果删除一个不存在的数据库，将会报错。此时，可以加上参数 `IF EXISTS`，如果数据库存在，再执行删除，否则不执行删除。

#### 5）切换数据库

```
use 数据库名;
```

我们要操作某一个数据库下的表时，就需要通过该指令，切换到对应的数据库下，否则是不能操作的。比如，切换到itcast数据库，执行如下SQL：

![[Pasted image 20241204182337.png]]

### 2.3.2 表操作

#### 2.3.2.1 表操作-查询创建

1. 查询当前数据库所有表

必须先进入数据库，才能查询当前数据库下的表：`SHOW TABLES;`

比如，我们可以切换到sys这个系统数据库，并查看系统数据库中的所有表结构。

```sql
use sys;

show tables;

mysql>

mysq1> use sys;

Database cnanged

mysql>show tables;
```

2. 查看指定表结构：`DECS 表名;`

通过这条指令，我们可以查看到指定表的字段，字段的类型、是否可以为NULL，是否存在默认值等信息。

```sql
mysql> DESC tb_user;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | int(11)     | YES  |     | NULL    |       |
| name   | varchar(50) | YES  |     | NULL    |       |
| age    | int(11)     | YES  |     | NULL    |       |
| gender | varchar(1)  | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
4 rows in set (0.01 sec)

```

3. 查询指定表的建表语句

通过这条指令，主要是用来查看建表语句的，而有部分参数我们在创建表的时候，并未指定也会查询到，因为这部分是数据库的默认值，如：存储引擎、字符集等。
```sql
SHOW CREATE TABLE 表名;

mysql> SHOW CREATE TABLE tb_user;
+---------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table   | Create Table                                                                                                                                                                                                                                                                                    |
+---------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| tb_user | CREATE TABLE `tb_user` (
  `id` int(11) DEFAULT NULL COMMENT 'bianhao',
  `name` varchar(50) DEFAULT NULL COMMENT 'xingming',
  `age` int(11) DEFAULT NULL COMMENT 'nianling',
  `gender` varchar(1) DEFAULT NULL COMMENT 'xingbie'
) ENGINE=InnoDB DEFAULT CHARSET=latin1 COMMENT='yonghubiao' |
+---------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

```
4. 创建表结构

```sql
CREATE TABLE 表名(
    字段1 字段1类型[COMMENT 字段1注释],
	字段2 字段2类型[COMMENT 字段2注释],
	字段3 字段3类型[COMMENT 字段3注释],
	...
	字段n 字段n类型[COMMENT 字段n注释]
)[COMMENT 表注释];
```

> 注意：`[⋯]` 内为可选参数，最后一个字段后面没有逗号

比如，我们创建一张表`tb_user`，对应的结构如下，那么建表语句为：
```sql
CREATE TABLE tb_user (
    id int comment '编号',
    name varchar(50) comment '姓名',
    age int comment '年龄',
    gender varchar(1) comment '性别'
) comment '用户表';
```
#### 2.3.2.2 表操作-数据类型

在上述的建表语句中，我们在指定字段的数据类型时，用到了int，varchar，那么在MySQL中除了以上的数据类型，还有哪些常见的数据类型呢？接下来，我们就来详细介绍一下MySQL的数据类型。

MySQL中的数据类型有很多，主要分为三类：数值类型、字符串类型、日期时间类型。

##### 2.3.2.2.1 数值类型

| 类型          | 大小     | 有符号（SIGNED）范围                                      | 无符号（UNSIGNED）范围                                     | 描述         |
| ----------- | ------ | -------------------------------------------------- | --------------------------------------------------- | ---------- |
| TINYINT     | 1byte  | (-128,127)                                         | (0,255)                                             | 小整数值       |
| SMALLINT    | 2bytes | (-32768,32767)                                     | (0,65535)                                           | 大整数值       |
| MEDIUMINT   | 3bytes | (-8388608,8388607)                                 | (0,16777215)                                        | 大整数值       |
| INT/INTEGER | 4bytes | (-2147483648,2147483647)                           | (0,4294967295)                                      | 大整数值       |
| BIGINT      | 8bytes | (-2^63,2^63-1)                                     | (0,2^64-1)                                          | 极大整数值      |
| FLOAT       | 4bytes | (-3.402823466 E+38,3.402823466351 E+38)            | 0和（1.175494351 E-38,3.402823466 E+38)               | 单精度浮点数值    |
| DOUBLE      | 8bytes | (-1.7976931348623157E+308,1.7976931348623157E+308) | 0和(2.2250738585072014E-308,1.7976931348623157E+308) | 双精度浮点数值    |
| DECIMAL     |        | 依赖于M（精度）和D（标度）的值                                   | 依赖于M（精度）和D（标度）的值                                    | 小数值（精确定点数） |

如：
+ 年龄字段--不会出现负数，而且人的年龄不会太大，用 `age tinyint unsigned`
+ 分数--总分100分，最多出现一位小数 `score double(4,1)`
##### 2.3.2.2.2 字符串类型

| 类型         | 大小                    | 描述              |
| ---------- | --------------------- | --------------- |
| CHAR       | 0-255 bytes           | 定长字符串（需要指定长度）   |
| VARCHAR    | 0-65 535 bytes        | 变长字符串（需要指定长度）   |
| TINYBLOB   | 0-255 bytes           | 不超过255个字符的二进制数据 |
| TINYTEXT   | 0-255 bytes           | 短文本字符串          |
| BLOB       | 0-65 535 bytes        | 二进制形式的长文本数据     |
| TEXT       | 0-65 535 bytes        | 长文本数据           |
| MEDIUMBLOB | 0-16 777 215 bytes    | 二进制形式的中等长度文本数据  |
| MEDIUMTEXT | 0-16 777 215 bytes    | 中等长度文本数据        |
| LONGBLOB   | 0-4 294 967 295 bytes | 二进制形式的极大文本数据    |
| LONGTEXT   | 0-4 294 967 295 bytes | 极大文本数据          |

char 与 varchar 都可以描述字符串，char 是定长字符串，指定长度多长，就占用多少个字符，和字段值的长度无关。而 varchar 是变长字符串，指定的长度为最大占用长度。相对来说，char 的性能会更高些。

如：

+ 用户名 username 长度不定，最长不会超过50，用 `username varchar(50)`
+ 性别 gender 存储值，不是男，就是女，用 `gender char(1)`
+ 手机号phone 固定长度为11，用 `phone char(11)`
##### 2.3.2.2.3 日期时间类型

| 类型        | 大小  | 范围                                      | 格式                 | 描述           |
| --------- | --- | --------------------------------------- | ------------------ | ------------ |
| DATE      | 3   | 1000-01-01至 9999-12-31                  | YYYY-MM-DD         | 日期值          |
| TIME      | 3   | -838：59：59 至 838：59：59                  | HH:MM:SS           | 时间值或持续时间     |
| YEAR      | 1   | 1901至2155                               | YYYY               | 年份值          |
| DATETIME  | 8   | 1000-01-01 00：00：00至9999-12-31 23:59:59 | YYYY-MM-DDHH:MM:SS | 混合日期和时间值     |
| TIMESTAMP | 4   | 1970-01-01 00:00:01至2038-01-19 03:14:07 | YYYY-MM-DDHH:MM:SS | 混合日期和时间值，时间戳 |
如：
+ 生日字段 birthday，用 `birthday date`
+ 创建时间 createtime，用 `createtime datetime` 

#### 2.3.2.3 表操作-案例
```
设计一张员工信息表，要求如下：
1．编号（纯数字）
2．员工工号（字符串类型，长度不超过10位）
3．员工姓名（字符串类型，长度不超过10位）
4．性别（男／女，存储一个汉字）
5．年龄（正常人年龄，不可能存储负数）
6．身份证号（二代身份证号均为18位，身份证中有x这样的字符）
7．入职时间（取值年月日即可）
```

对应的建表语句如下：
```sql
mysql> CREATE TABLE emp(
    -> id int comment '编号',
    -> workno varchar(10) comment '工号',
    -> name varchar(10) comment '姓名',
    -> gender char(1) comment '性别',
    -> age tinyint unsigned comment '年龄',
    -> idcard char(18) comment '身份证号',
    -> entrydate date comment '入职时间'
    -> ) comment '员工表';
Query OK, 0 rows affected (0.01 sec)
mysql> DESC emp;
+-----------+---------------------+------+-----+---------+-------+
| Field     | Type                | Null | Key | Default | Extra |
+-----------+---------------------+------+-----+---------+-------+
| id        | int(11)             | YES  |     | NULL    |       |
| workno    | varchar(10)         | YES  |     | NULL    |       |
| name      | varchar(10)         | YES  |     | NULL    |       |
| gender    | char(1)             | YES  |     | NULL    |       |
| age       | tinyint(3) unsigned | YES  |     | NULL    |       |
| idcard    | char(18)            | YES  |     | NULL    |       |
| entrydate | date                | YES  |     | NULL    |       |
+-----------+---------------------+------+-----+---------+-------+
7 rows in set (0.00 sec)
```

表结构创建好了，里面的 name 字段是 varchar 类型，最大长度为10，也就意味着如果超过10将会报错，如果我们想修改这个字段的类型或修改字段的长度该如何操作呢？接下来再来讲解DDL语句中，如何操作表字段。


#### 2.3.2.4 表操作-修改

+ 1）添加字段

```sql
ALTER TABLE 表名 ADD 字段名 类型 (长度)[COMMENT 注释][约束];
```

案例：

为emp表增加一个新的字段“昵称”为nickname，类型为varchar（20）

```sql
mysql> ALTER TABLE emp ADD nickname varchar(20) COMMENT '昵称';
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC emp;
+-----------+---------------------+------+-----+---------+-------+
| Field     | Type                | Null | Key | Default | Extra |
+-----------+---------------------+------+-----+---------+-------+
| id        | int(11)             | YES  |     | NULL    |       |
| workno    | varchar(10)         | YES  |     | NULL    |       |
| name      | varchar(10)         | YES  |     | NULL    |       |
| gender    | char(1)             | YES  |     | NULL    |       |
| age       | tinyint(3) unsigned | YES  |     | NULL    |       |
| idcard    | char(18)            | YES  |     | NULL    |       |
| entrydate | date                | YES  |     | NULL    |       |
| nickname  | varchar(20)         | YES  |     | NULL    |       |
+-----------+---------------------+------+-----+---------+-------+
8 rows in set (0.00 sec)

```

+ 2）修改数据类型

```sql
ALTER TABLE 表名 MODIFY 字段名 新数据类型 (长度);
```

+ 3）修改字段名和字段类型

```sql
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型 (长度)[COMMENT 注释][约束];
```

案例：

将 emp 表的 nickname 字段修改为 username，类型为 varchar(30)

```
mysql> ALTER TABLE emp CHANGE nickname username varchar(30) COMMENT '昵称';
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC emp;
+-----------+---------------------+------+-----+---------+-------+
| Field     | Type                | Null | Key | Default | Extra |
+-----------+---------------------+------+-----+---------+-------+
| id        | int(11)             | YES  |     | NULL    |       |
| workno    | varchar(10)         | YES  |     | NULL    |       |
| name      | varchar(10)         | YES  |     | NULL    |       |
| gender    | char(1)             | YES  |     | NULL    |       |
| age       | tinyint(3) unsigned | YES  |     | NULL    |       |
| idcard    | char(18)            | YES  |     | NULL    |       |
| entrydate | date                | YES  |     | NULL    |       |
| username  | varchar(30)         | YES  |     | NULL    |       |
+-----------+---------------------+------+-----+---------+-------+
8 rows in set (0.00 sec)

```

+ 4）删除字段

```
ALTER TABLE 表名 DROP 字段名;
```

案例：

将 emp 表的字段 username 删除

```
ALTER TABLE emp DROP username;
```

+ 5）修改表名

```
ALTER TABLE 表名 RENAME TO 新表名;
```

案例 :

将 emp 表的表名修改为 employee

```
mysql> ALTER TABLE emp RENAME TO employee;
Query OK, 0 rows affected (0.01 sec)

mysql> DESC emp;
ERROR 1146 (42S02): Table 'itcast.emp' doesn't exist

```

#### 2.3.2.5 表操作-删除

+ 1). 删除表

```
DROP TABLE [IF EXISTS] 表名;
```

可选项 `IF EXISTS` 代表，只有表名存在时才会删除该表，表名不存在，则不执行删除操作（如果不加该参数项，删除一张不存在的表，执行将会报错）。

案例 :

如果tb_user表存在，则删除tb_user表 

+ 2). 删除指定表, 并重新创建表

```
TRUNCATE TABLE 表名;
```

> 注意: 在删除表的时候，表中的全部数据也都会被删除。

## 2.4 DML

DML 英文全称是 `Data Manipulation Language` (数据操作语言)，用来对数据库中表的数据记录进行增、删、改操作。

添加数据（INSERT）

修改数据（UPDATE）

删除数据（DELETE）

### 2.4.1 添加数据

+ 1）给指定字段添加数据

```
INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...);
```

+ 2）给全部字段添加数据

```
INSERT INTO 表名 VALUES (值 1, 值 2, ...);
```

+ 3）批量添加数据

```
INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), (值 1, 值2, ...);

INSERT INTO 表名 VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...) ;
```

> 注意事项
> 
> - 插入数据时，指定的字段顺序需要与值的顺序是一一对应的。
> - 字符串和日期型数据应该包含在引号中。
> - 插入的数据大小，应该在字段的规定范围内

> 在插入时报错：
> mysql> INSERT INTO employee (id, workno, name, gender, age, idcard, entrydate) VALUES(1, '1', 'Itcast001', '男', 18, '123456789012345678', '2000-01-02');
> ERROR 1366 (HY000): Incorrect string value: '\xE7\x94\xB7' for column 'gender' at row 1
> **解决方式1**：
> **在创建数据表时就需要注意将中文字段属性设置为utf8编码形式**
> ```
> CREATE TABLE emp(
     id int comment '编号',
    workno varchar(10) comment '工号',
    name varchar(10) comment '姓名',
    gender char(1) ==character set utf8 collate utf8_unicode_ci== comment '性别',
    age tinyint unsigned comment '年龄',
    idcard char(18) comment '身份证号',
    entrydate date comment '入职时间'
    ) comment '员工表';
> ```
> **解决方式2**：
> **创建时不需要指定，之后进行修改**
> 1. 检查数据表所有字段的状态
> SHOW FULL COLUMNS FROM employee;
> 2. 发现gender字段的Collation项非utf8，修改它
> `ALTER TABLE employee change gender gender char(1) character set utf8 collate utf8_unicode_ci not null;`

案例：给employee表所有的字段添加数据

```
INSERT INTO employee (id, workno, name, gender, age, idcard, entrydate) VALUES(1, '1', 'Itcast001', '男', 18, '123456789012345678', '2000-01-02');

INSERT INTO employee VALUES(2, '2', 'Itcast002', '男', 19, '123456789012345679', '2001-01-02');

INSERT INTO employee VALUES (3, '3', 'Itcast003', '男', 20, '123456789012345677', '2001-01-02'),(4, '4', 'Itcast004', '男', 22, '123456789012345676', '2001-01-02');
```

### 2.4.2 修改数据

修改数据的具体语法为:
```
UPDATE 表名 SET 字段名1 = 值1, 字段名2 = 值2, .... [WHERE 条件] ;
```

> 注意事项：
> 修改语句的条件可以有，也可以没有，如果没有条件，则会修改整张表的所有数据。

案例：
```
-- 修改id为1的数据，将name修改为itheima--
UPDATE employee SET name = 'itcast1' WHERE id = 1;

-- 修改id为2的数据, 将name修改为小昭, gender修改为女--
UPDATE employee SET name = '小zhao', gender = '女' WHERE id = 2; 

-- 将所有的员工入职日期修改为2008-01-01--
UPDATE employee SET entrydate = '2008-10-11'; 
```

### 2.4.3 删除数据

删除数据的具体语法为：

```
DELETE FROM 表名 [WHERE 条件] ;
```

> 注意事项：
> + DELETE 语句的条件可以有，也可以没有，如果没有条件，则会删除整张表的所有数据。
> + DELETE 语句不能删除某一个字段的值(可以使用UPDATE，将该字段值置为NULL即可)。
> + 当进行删除全部数据操作时，datagrip会提示我们，询问是否确认删除，我们直接点击Execute即可。

案例：

```
-- 删除gender为女的员工--

DELETE FROM employee WHERE gender = '女'; 

-- 删除所有员工--

DELETE FROM employee; 
```

## 2.5 DQL
DQL 英文全称是 `Data Query Language`(数据查询语言)，数据查询语言，用来查询数据库中表的记录。

查询关键字: ==SELECT==

在一个正常的业务系统中，查询操作的频次是要远高于增删改的，当我们去访问企业官网、电商网站，在这些网站中我们所看到的数据，实际都是需要从数据库中查询并展示的。而且在查询的过程中，可能还会涉及到条件、排序、分页等操作。

### 2.5.1 基本语法
DQL 查询语句，语法结构如下：
```
SELECT
	字段列表 
FROM
	表名列表 
WHERE
	条件列表 
GROUP  BY
	分组字段列表 
HAVING
	分组后条件列表
ORDER BY
	排序字段列表
LIMIT
	分页参数
```

我们在讲解这部分内容的时候，会将上面的完整语法进行拆分，分为以下几个部分：

基本查询（不带任何条件）

条件查询（WHERE）、聚合函数（count、max、min、avg、sum）、分组查询（group by）、排序查询（order by）、分页查询（limit）

### 2.5.2 基础查询

+ 1）查询多个字段

```
SELECT 字段1, 字段2, 字段3, ... FROM 表名;
SELECT * FROM 表名;
```

> 注意：* 号代表查询所有字段，在实际开发中尽量少用（不直观、影响效率）

+ 2）字段设置别名

```
SELECT 字段1 [ AS 别名1 ] , 字段2 [ AS 别名2 ] ... FROM 表名;
SELECT 字段1 [ 别名1 ] , 字段2 [ 别名2 ] ... FROM 表名;
```

+ 3）去除重复记录

```
SELECT DISTINCT 字段列表 FROM 表名;
```

案例：

```
-- 1.查询指定字段 name, workno, age 返回
SELECT name, workno, age FROM emp;
-- 2.查询所有字段返回
SELECT id, workno, name, gender, age, idcard, workaddress, entrydate FROM emp;
SELECT * FROM emp;
-- 3.查询所有员工的工作地址，起别名
SELECT workaddress AS '工作地址' FROM emp;
-- AS可以省略
SELECT workaddress '工作地址' FROM emp;
-- 4.查询公司员工的上班地址（不要重复）
SELECT DISTINCT workaddress FROM emp;
```

### 2.5.3 条件查询

+ 1）语法

```
SELECT 字段列表 FROM 表名 WHERE 条件列表;
```

+ 2）条件

![Pasted image 20241204221729](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Pasted%20image%2020241204221729.png)

常用的逻辑运算符如下：
![Pasted image 20241204221822](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Pasted%20image%2020241204221822.png)

案例：

```
-- 条件查询
-- 1.查询年龄等于 88 的员工
SELECT * FROM emp WHERE age = 88;
-- 2.查询年龄小于 20 的员工信息
SELECT * FROM emp WHERE age < 20;
-- 3.查询年龄小于等于 20 的员工信息
SELECT * FROM emp WHERE age <= 20;
-- 4.查询没有身份证号的员工信息
SELECT * FROM emp WHERE idcard IS null;
-- 5.查询有身份证号的员工信息
SELECT * FROM emp WHERE idcard IS NOT null;
-- 6.查询年龄不等于 88 的员工信息
SELECT * FROM emp WHERE age != 88;
SELECT * FROM emp WHERE age <> 88;
-- 7.查询年龄在 15 岁(包含) 到 20 岁(包含)之间的员工信息
SELECT * FROM emp WHERE age >=15  && age<=20;
SELECT * FROM emp WHERE age >=15  AND age<=20;
SELECT * FROM emp WHERE age BETWEEN 15 AND 20;
-- 最大最小值顺序不能写反
SELECT * FROM emp WHERE age BETWEEN 20 AND 15;
-- 8.查询性别为 女 且年龄小于 25 岁的员工信息
SELECT * FROM emp WHERE gender = '女' AND age<=25;
-- 9.查询年龄等于18 或 20 或 40 的员工信息
SELECT * FROM emp WHERE age = 18 OR age = 20 OR age = 40;
SELECT * FROM emp WHERE age IN(18, 20, 40);
-- 10.查询姓名为两个字的员工信息 _ %
SELECT * FROM emp WHERE name LIKE '__';
-- 11.查询身份证号最后一位是X的员工信息
SELECT * FROM emp WHERE idcard LIKE '%X';
```

### 2.5.4 聚合函数
+ 1）介绍

将一列数据作为一个整体，进行纵向计算 。

+ 2）常见的聚合函数

![Pasted image 20241204223529](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Pasted%20image%2020241204223529.png)

+ 3）语法

```
SELECT 聚合函数(字段列表) FROM 表名;
```

> 注意：
> 1. NULL值是不参与所有聚合函数运算的。
> 2. 对于count聚合函数，统计符合条件的总记录数，还可以通过 count(数字/字符串)的形式进行统计查询

案例：

```sql
-- 聚合函数
-- 1.统计该企业员工数量
SELECT COUNT(*) FROM emp;
SELECT COUNT(id) FROM emp;
-- NULL值是不参与所有聚合函数运算的
SELECT COUNT(idcard) FROM emp;
SELECT COUNT(1) FROM emp;
-- 2.统计该企业员工的平均年龄
SELECT AVG(age) FROM emp;
-- 3.统计该企业员工的最大年龄
SELECT MAX(age) FROM emp;
-- 4.统计该企业员工的最小年龄
SELECT MIN(age) FROM emp;
-- 5.统计西安地区员工的年龄之和
SELECT SUM(age) FROM emp WHERE workaddress = '西安'; 
```

### 2.5.5 分组查询

+ 1）语法

```sql
SELECT 字段列表 FROM 表名 [WHERE 条件] GROUP BY 分组字段名 [HAVING 分组后过滤条件 ];
```

+ 2）where 与 having 区别

==执行时机不同==：where是分组之前进行过滤，不满足where条件，不参与分组；而having是分组之后对结果进行过滤。

==判断条件不同==：where不能对聚合函数进行判断，而having可以。

注意事项:

• 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义。

• 执行顺序 : where > 聚合函数 > having

支持多字段分组，具体语法为：`group by columnA, columnB`

在实际执行时，SQL查询的大致流程如下：

1. FROM子句确定基本的数据来源表。
2. WHERE子句对从FROM子句中获取的数据进行行级别的过滤。
3. GROUP BY子句对数据进行分组。
4. HAVING子句基于每个组应用过滤条件，此时是可以使用SELECT列表中定义的别名的，因为在这个阶段，SQL引擎已经知道了每个组的聚合值。
5. SELECT子句选择并计算每个组的聚合值或其他列，这里的Sum(item_price * quantity) AS total_price就是这样的一个计算。
6. ORDER BY子句对结果集进行排序。

因此，在上述SQL语句中，total_price别名在HAVING子句中是合法的，因为SQL引擎会在应用HAVING过滤条件之前计算出这个总价格。

> Another MySQL extension to standard SQL permits references in the `HAVING` clause to aliased expressions in the select list. For example, the following query returns `name` values that occur only once in table `orders`:
> 标准 SQL 的另一个 MySQL 扩展允许在 HAVING 子句中引用选择列表中的别名表达式。 例如，下面的查询会返回只在表订单中出现一次的名称值：
> 
> ```sql
> SELECT name, COUNT(name) FROM orders GROUP BY name HAVING COUNT(name) = 1;
> ```
> 
> The MySQL extension permits the use of an alias in the `HAVING` clause for the aggregated column:
> MySQL 扩展允许在聚合列的 HAVING 子句中使用别名：
> 
> ```sql
> SELECT name, COUNT(name) AS c FROM orders GROUP BY name HAVING c = 1;
> ```
> 
> Standard SQL permits only column expressions in `GROUP BY` clauses, so a statement such as this is invalid because `FLOOR(value/100)` is a noncolumn expression:
> 标准 SQL 只允许在 `GROUP BY` 子句中使用列表达式，因此这样的语句是无效的，因为 `FLOOR(value/100)` 是一个非列表达式：
> 
> ```sql
> SELECT id, FLOOR(value/100) FROM _tbl_name_ GROUP BY id, FLOOR(value/100)
> ```
> MySQL extends standard SQL to permit noncolumn expressions in `GROUP BY` clauses and considers the preceding statement valid.
> MySQL 扩展了标准 SQL，允许在 `GROUP BY` 子句中使用非列表达式，并认为前面的语句有效。
> 
> Standard SQL also does not permit aliases in `GROUP BY` clauses. MySQL extends standard SQL to permit aliases, so another way to write the query is as follows:  
> 标准 SQL 也不允许在 `GROUP BY` 子句中使用别名。 MySQL 扩展了标准 SQL，允许使用别名，因此查询的另一种写法如下：
> ```sql
> SELECT id, FLOOR(value/100) AS val FROM _tbl_name_ GROUP BY id, val;
> ```
> The alias `val` is considered a column expression in the `GROUP BY` clause.  
> 在 `GROUP BY` 子句中，别名 val 被视为列表达式。

> A MySQL extension to standard SQL permits references in the `HAVING` clause to aliased expressions in the select list. The `HAVING` clause can refer to aliases regardless of whether [`ONLY_FULL_GROUP_BY`](https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html#sqlmode_only_full_group_by) is enabled.
> 标准 SQL 的 MySQL 扩展允许在 `HAVING` 子句添加到选择（select）列表中的别名表达式。`HAVING` 子句可以引用别名，无论 `ONLY_FULL_GROUP_BY` 是否启用

总结：
按照标准SQL规范来讲，一条SQL语句的执行顺序并不是简单的从左到右，而是遵循一个特定的逻辑顺序，一条SQL语句的执行顺序是这样的：  
- **FROM**：确定数据来源的表（或视图），并生成一个数据集。
- **ON**：在连接表时，使用 `ON` 指定连接条件
- **JOIN**：如果有多个表，执行连接操作，生成一个新的数据集。
- **WHERE**：应用过滤条件，排除不符合条件的记录。
- **GROUP BY**：将数据按指定列分组。
- **HAVING**：对分组后的数据应用过滤条件。
- **SELECT**：选择需要返回的列。
- **DISTINCT**：去除重复的记录（如果有的话）。
- **ORDER BY**：对结果集进行排序。
- **LIMIT** / **OFFSET**：限制返回的记录数或跳过前几条记录。
![Pasted image 20241205115908](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Pasted%20image%2020241205115908.png)
根据这个标准having会在select之前执行，所以在having中使用select定义的别名会报错。  
但是现在很多数据库都对此做了扩展，例如MySQL、SQLLite等都支持having使用聚合之后的别名

扩展：
#### 列表达式（List Expression）

列表表达式通常是指一个包含多个值的集合，可以用于进行比较、过滤或其他操作。例如，在 `IN` 子句中可以看到列表表达式的用法：
```sql
SELECT * FROM employees
WHERE department_id IN (1, 2, 3);
```
在这个例子中，`(1, 2, 3)` 就是一个列表表达式，它表示要筛选出 `department_id` 为 1、2 或 3 的员工。

#### 非列表达式（Non-List Expression）

非列表达式则是指那些不以多个值的集合形式出现的表达式。它们通常是单个值、单个列名、函数调用或者计算表达式。例如：
```sql
SELECT * FROM employees
WHERE salary > 50000;
```
在这个例子中，`salary > 50000` 是一个非列表达式，因为它只涉及一个列（`salary`）和一个单一的值（`50000`），并且没有列表形式的多个值。

案例：

```sql
-- 分组查询
-- 1.根据性别分组，统计男性员工和女性员工的数量
SELECT gender, COUNT(*) FROM emp GROUP BY gender;
-- 2.根据性别分组，统计男性员工和女性员工的平均年龄
SELECT gender, AVG(age) FROM emp GROUP BY gender;
-- 3.查询年龄小于45的员工，并根据工作地址分组，获取员工数量大于等于3的工作地址
SELECT workaddress, COUNT(*) FROM emp WHERE age < 45 GROUP BY workaddress HAVING COUNT(*) >= 3;
SELECT workaddress, COUNT(*) AS address_count FROM emp WHERE age < 45 GROUP BY workaddress HAVING address_count >= 3;
```

### 2.5.6 排序查询

排序在日常开发中是非常常见的一个操作，有升序排序，也有降序排序。

+ 1）语法

```sql
SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1, 字段2 排序方式2;
```

+ 2）排序方式

	+ ASC : 升序(默认值)
	+ DESC: 降序


> 注意事项：
> + 如果是升序，可以不指定排序方式ASC；
> + 如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序；
