---
title: 数据库(基于mysql8.0)
date: 2021-09-10 02:18:24.0
updated: 2021-11-09 08:16:37.252
url: /?p=21
categories: 
- 服务器
tags: 
- mysql8.0
---

## 一、数据库的介绍

### 1. 数据库的概念

> 数据库局势存放数据的仓库
>
> 数据库(DataBase) 是长期存储在计算机内部结构的、大量的、共享的数据集合

+ 长期存储: 持久存储
+ 有结构:
  + 类型: 数据库不仅可以存储数据,而且存储的数据还是有类型的
  + 关系: 存储数据与数据之间的关系
+ 大量: 大多数数据库都是文件系统的,也就是时候存储在数据库中的数据上就是存储在磁盘的文件中
+ 共享: 多个应用程序可以通过数据库实现数据共享

### 2. 关系型数据库和非关系型数据库

+ 关系型数据库

  > 关系型数据库,采用了关系模型来组织数据的存储,以行和列的形式来存储数据,并记录数据与数据之间的关系. 

+ 非关系型数据库

  > 非关系型数据库, 采用键值对的模型来存储数据,只完成数据的记录,不会记录数据与数据之间的关系.
  >
  > 在非关系型数据库总基于特定的存储结构来解决一些大数据应用的难题.
  >
  > NoSQL(not only SQL)数据库来指代非关系型数据库

### 3. 常见的数据库产品

**关系型数据库产品**

+ `MySQL`
  + MariaDB
  + Percona Server
+ PostgreSQL
+ `Oracle`
+ SQL Server
+ Access
+ Sybase
+ 达梦数据库(国产)

**非关系型数据库产品**

+ 面向检索的列式存储 Column-Oriented
  + HaBase(Hadoop子系统)
  + BigTable(Google)
+ 面向高并发的缓存存储 key-value
  + `Redis`
  + MemcacheDB
+ 面向海量数据访问的文档存储 Document-Oriented
  + `MongoDB`
  + CouchDB



## 二、MySQL数据库环境准备

**MySQL 8.x新特性**

+ 性能: 官方8.x比5.7速度快2倍
+ 支持NoSQL存储: 5.7开始提供支持,8.x对其做了改进
+ 窗口函数
+ 索引: 隐藏索引、降序索引
+ 可用性、可靠性

## 三、MySQL逻辑结构

> MySQL可以存储数据,但是存储在mysql中的数据需要按照特定的结构进行存储

## 四、SQL语法

### 1. SQL概述

> SQL(Structured Query Language) 结构化查询语言

#### 1.1 SQL分类

> 根据SQL指令完成的数据库操作的不同,可以讲SQL指令分为四类:

+ **DDL** (Data Definition Language) 数据定义语言
  + 用于完成对数据库对象(数据库、数据表、视图、索引等)的创建、删除、修改
+ **DML** (Data Manipulation Language) 数据操作语言
  + 用于完成对数据表中的数据增删改
+ **DQL** (Data Query Language) 数据查询语言
  + 用于将对数据表中的数据进行查询

+ **DCL** (Data Control Language) 数据定义语言
  + 用于完成事务管理等一些假哦能控制操作

### 2. SQL基本语法

+ SQL指令不区分大小写
+ SQL语句之间用`空格`分割
+ 每条SQL语句后面都要以`分号`结尾
+ SQL语句之间可以不限制换行

### 3. DDL 数据定义语言

#### 3.1 DDL-数据库操作

> 数据库的 查询、修改、删除

**创建数据库**

```sql
create database <dbName>;
-- 当指定数据库名不存在的时候创建数据库
create database if not exists dbName;
-- 指定字符集 
create database dbName character set utf8;
```

**查询数据库**

```sql
show databases;
-- 显示指定数据库的创建语句
show create database dbName;
```

**修改数据库**

```sql
-- 修改数据库字符集
alter database dbName character set utf8; --utf8、utf8mb4、gbk
```

**删除数据库**

```sql
drop database dbName;
-- 如果数据库存在,删除数据库
drop database if exists dbName;
```

#### **使用/切换数据库**

```sql
use dbName;
```

 

#### 3.2 DML-数据表操作

> 数据表的增删改

**创建数据表**

```sql
create table students (
  stu_id char(8) not null unique,
  stu_name varchar(20) not null,
  stu_gender char(2) not null,
  stu_age int not null,
  stu_tel char(11) not null unique,
  stu_qq char(11) unique
);
-- 定长字符串 char
-- varchar 可变长度字符串
-- not null 不可为空
-- unique 不可重复
```

**查询数据表**

```sql
show tables;
-- 查看表创建详情
desc tbName;
```

**删除数据表**

```sql
drop tbName;
drop if exists tbName
```

**修改数据表**

```sql
	-- 修改表名	alter table students rename to stus;	-- 数据表也是有字符集的,默认与数据库相同, 修改数据集	alter table tbName character set utf8mb4;	-- 添加字段	alter table stus add stu_remark char(200);	-- 修改字段名和类型	alter table tbName change <oldColumName> <newColumnName> [type];	alter table stud change stu_remark stu_desc char(300);	-- 删除字段	alter table tbName drop <columnName>;
```

### 4. MySQL数据类型

#### 4.1 数值类型

| 类型        | 大小         | 范围(有符号)   | 说明                               |
| :---------- | :----------- | :------------- | :--------------------------------- |
| tinyint     | 1byte        | -128~127       | 特小型整数(年龄)                   |
| smallint    | 2byte        | -32768~32767   |                                    |
| mediumint   | 3byte        | -2^31^~2^31^-1 |                                    |
| Int/integer | 4byte        |                |                                    |
| bigint      | 8byte        |                |                                    |
| float       | 4byte        |                |                                    |
| double      | 8byte        |                |                                    |
| decimal     | 第一个参数+2 |                | decimal(10,2) 一共十位,小数点后2位 |

#### 4.2 字符类型

| 类型       | 长度        | 说明             |
| :--------- | :---------- | :--------------- |
| char       | 0~255       | 定长字符串       |
| varchar    | 0~65535     | 不定长字符串     |
| tinyblob   | 0~255       |                  |
| blob       | 0~65535     | 存储二进制字符串 |
| mediumblob | 0~1677215   |                  |
| Long blob  | 0~429467295 |                  |
| tinytext   | 0~255       | 文本数据字符串   |
| text       | 0~65535     |                  |
| mediumtext | 0~1677215   |                  |
| longtext   | 0~429467295 |                  |

#### 4.3 日起类型

| 类型       | 格式                | 说明                        |
| :--------- | :------------------ | :-------------------------- |
| date       | 2021-10-12          | 日期,只存储年月日           |
| time       | 20:57:21            | 时间,只存储时间             |
| year       | 2021                | 年,只存储年份               |
| `datetime` | 2021-10-12 20:57:21 | 日期+时间                   |
| timestamp  |                     | 从1970年1月1日0点0分0秒开始 |

### 5. 字段约束

#### 5.1 约束的介绍

> 在创建数据表的时候,对指定字段进行字段限制

为什么要给表中的字段添加约束呢?

+ 保证数据的有效性
+ 保证数据的完整性
+ 保证数据的正确性

字段常见的约束有哪些?

+ 非空约束(not null)

+ 唯一约束(unique)

+ 主键约束(primary key)

  ```sql
  -- 删除主键约束alter table books drop primary key;-- 在原表没有主键的情况下,将某一个字段设置为主键alter table books modify book_id int primary key;-- 主键的自动增长(创建表时)create table books (  book_id int primary key auto_increment,  book_name varchar(32) not null,  book_remark varchar(100)                   )
  ```

#### 5.2 联合主键

> 联合主键——将数据表中的多个字段组合在一块儿设置为另一张表的主键

Students																	

| student_num | student_name | student_gender | Student_age |
| :---------- | :----------- | :------------- | ----------- |
| 101         | zhangsan     | male           | 21          |
| 102         | lisi         | famale         | 20          |

Courses

| Course_id | Course_name | Course_credit |
| :-------- | ----------- | ------------- |
| 1         | Java        | 4             |
| 2         | C++         | 3             |

Grades 

| ~~Grades_id~~ | Student_num | Course_id | Score |
| :------------ | :---------- | :-------- | :---- |
| ~~1~~         | 101         | 1         | 59    |
| ~~2~~         | 101         | 2         | 60    |
| ~~3~~         | 102         | 1         | 78    |
| ~~4~~         | 102         | 2         | 67    |

**定义联合主键**

```sql
create table grades(	student_num char(8),  course_id int,  score int,  primary key(student_num,course_id));
```

注意: 在实际开发中,联合主键用的很少,当一张表中没有明确的字段来表示唯一字段的的时候,可以另外定义一个id字段

#### 5.3 外键约束

略

### 6. DML 数据管理语言

> 数据表中数据的增删改

#### 6.1 插入数据

```sql
insert into tbName(columnName,...) values(	...);
```

#### 6.2 删除数据

```sql
delete from tbName where id=1;delete from tbName;
```

#### 6.3 修改数据

```sql
-- update one columnupdate tbName set student_name="wanger" where student_num="20210105"; -- update one more column use seprator ","update tbName set student_namse="wanger", student_gender="famale" where student_num="20210105";
```

### 7. DQL 数据查询语言

> 从数据库中提取满足限定条件的记录
>
> + 单表查询
> + 多表查询

#### 7.1 查询基础语法

```sql
select * from students;select * from students where student_num="20210105";select student_num,student_name from students where student_num="20210105";
```

#### 7.2 where子句条件

```sql
# = 等于# != 不等于# > 大于# >= 大于等于# < 小于# <= 小于等于# 多条件# and# or
```

#### 7.3 like模糊查询

+ 在like关键字后的reg表达式中
  + %表示任意多个字符
  + _表示任意一个字符(可以重复使用,表示几个字符)

#### 7.4 对查询结果的处理

**设置查询的列**

> 声明显示查询结果的指定列

```sql
select columnName1,... from tbName where ...;
```



**计算列**

> 对从数据表中查询的记录的列进行一定的运算之后显示出来

```sql
select 2021-student_age from students;
```



**字段别名**

> 给查询结果起一个语意性更强的名字

```sql
select 2021-student_age as 出生年份 from students
```



**去重**

```sql
select distinct student_age from students;
```



**排序**

> 将查询到的结果按指定顺序排序,升序排列(默认)和降序排列
>
> + asc  升序排列
> + desc  降序排列

```sql
select * from students where student_age>15 order by student_age asc|desc;
```



**多字段排序**

```sql
select * from students where student_age>15 order by student_gender asc,student_age desc;
```



**聚合函数**

+ count()
+ max()
+ min()
+ avg()
+ sum()



**日期函数和字符串函数**

+ 日期函数

  + now()
  + sysdate()

+ 字符串函数

  + concat()

    ```sql
    select concat(student_name, "-", "student_age") from students;
    ```

  + upper()

  + lower()



**分组查询**

> group by 分组就是将数据表中的结果根据指定类进行分组

```sql
select * from student group by student_gender;select 分组字段/聚合函数 from tbName [where 条件] group by 分组字段 [having 条件];
```

注意: 如果我们这里使用*来表示结果字段的话,那么结果只会返回分组后的一个数据,这样无意义

先根据where条件从数据库中筛选数据,然后对筛选后的数据进行分组,最后针对分组后的数据执行having条件筛选



**分页查询**

> limit 0,3 从索引0开始获取3条数据
>
> 总记录数 count
>
> 每页显示 pageSize
>
> 总页数 pageCount = count%pageSize==0 ? count/pageSize : count/pageSize+1

```sql
select * from student limit 0,3;select * form tbName [where ...] limit (pageNum-1)*pageSize,perSize;
```



## 七、数据表的关联关系

### 1. 关联关系介绍

+ 一对一关系
+ 一对多关系
+ 多对一关系
+ 多对多关系

### 2. 一对一关系

> 人 - 身份证
>
> 学生 - 学籍
>
> 用户 - 用户详情

### 3. 一对多与多对一关系

> 班级 - 学生	一对多 一个班级包含多个学生
>
> 学生 - 班级	多对一 多个学生可以属于一个班级

### 4. 多对多关系

> 学生 - 课程	一个学生可以选择多门课,一个课程可以被多个学生选择

### 5. 外键约束

> 外键约束——将一个字段添加外键约束与另一张表的主键相关联

```sql
# 先创建和其他表没有关系的表create table classes(c_id int primary key auto_increment,                     c_name varchar(40) not null unique,                     c_remark varchar(200)                    )                    # 然后创建需要添加外键的表create table students(stu_id int primary key auto_increment,                      stu_name varchar(4) not null,                      stu_gender varchar(2),                      stu_age tinyint,                      cid int,                      # 声明外键                      constraint FK_STUDENTS_CLASSES foreign key(cid) references classes(c_id)                     )                     # 添加数据 同样先给没有外键的表添加数据insert into classes(c_name,c_remark) values	("java2104",""),	("java2105",""),	("java2106",""),	("python2106","");insert into students (stu_name,stu_gender,stu_age,cid) values  	("zhangsan","male",21,2),   ("lisi","male",22,1),   ("wanger","famale",21,1),   ("erlengzi","male",22,3),   ("ershazi","male",23,4);    # 删除外键关系  alter table students drop foreign key FK_STUDENTS_CLASSES;  # 重新添加外键约束  alter table students add constraint FK_STUDENTS_CLASSES foreign key(cid) references classes(c_id);
```

### 6. 级联操作

```sql
create table students(stu_id int primary key auto_increment,                      stu_name varchar(4) not null,                      stu_gender varchar(2),                      stu_age tinyint,                      cid int,                      # 声明外键                      # constraint FK_STUDENTS_CLASSES foreign key(cid) references classes(c_id)                      # 这个时候我们不能使用这种方式来创建外键了,需要在后面添加上 on update cascade和on delete cascade                      constraint FK_students_classes foreign key(cid) references classes(c_id) on update cascade on delete cascade;                     )
```

### 7. 连接查询

> 在MySQL中可以使用join实现多表的联合操作
>
> + inner join 内连接
> + left join 左连接
> + right join 右连接

#### 1. 内连接(inner join)

```sql
select ... from tbName1 inner join tbName2;select * from students inner join classes;+--------+----------+------------+---------+------+------+------------+----------+| stu_id | stu_name | stu_gender | stu_age | cid  | c_id | c_name     | c_remark |+--------+----------+------------+---------+------+------+------------+----------+|      1 | zhangsan | male       |      21 |    2 |    4 | python2104 | ...      ||      1 | zhangsan | male       |      21 |    2 |    3 | java2103   | ...      ||      1 | zhangsan | male       |      21 |    2 |    2 | java2102   | ...      ||      1 | zhangsan | male       |      21 |    2 |    1 | java2101   | ...      ||      2 | lisi     | male       |      22 |    1 |    4 | python2104 | ...      ||      2 | lisi     | male       |      22 |    1 |    3 | java2103   | ...      ||      2 | lisi     | male       |      22 |    1 |    2 | java2102   | ...      ||      2 | lisi     | male       |      22 |    1 |    1 | java2101   | ...      ||      3 | wanger   | famale     |      21 |    1 |    4 | python2104 | ...      ||      3 | wanger   | famale     |      21 |    1 |    3 | java2103   | ...      ||      3 | wanger   | famale     |      21 |    1 |    2 | java2102   | ...      ||      3 | wanger   | famale     |      21 |    1 |    1 | java2101   | ...      ||      4 | erlengzi | male       |      22 |    3 |    4 | python2104 | ...      ||      4 | erlengzi | male       |      22 |    3 |    3 | java2103   | ...      ||      4 | erlengzi | male       |      22 |    3 |    2 | java2102   | ...      ||      4 | erlengzi | male       |      22 |    3 |    1 | java2101   | ...      ||      5 | ershazi  | male       |      23 |    4 |    4 | python2104 | ...      ||      5 | ershazi  | male       |      23 |    4 |    3 | java2103   | ...      ||      5 | ershazi  | male       |      23 |    4 |    2 | java2102   | ...      ||      5 | ershazi  | male       |      23 |    4 |    1 | java2101   | ...      |+--------+----------+------------+---------+------+------+------------+----------+# 使用笛卡尔积进行匹配,一共4*5=20条数据, 这是无意义的,所以我们需要再匹配结果后面添加一个条件# 使用 where 设置过滤条件,先生成笛卡尔积再从笛卡尔积中进行过滤数据(效率很低)select * from students inner join classes where students.cid = classes.c_id;+--------+----------+------------+---------+------+------+------------+----------+| stu_id | stu_name | stu_gender | stu_age | cid  | c_id | c_name     | c_remark |+--------+----------+------------+---------+------+------+------------+----------+|      2 | lisi     | male       |      22 |    1 |    1 | java2101   | ...      ||      3 | wanger   | famale     |      21 |    1 |    1 | java2101   | ...      ||      1 | zhangsan | male       |      21 |    2 |    2 | java2102   | ...      ||      4 | erlengzi | male       |      22 |    3 |    3 | java2103   | ...      ||      5 | ershazi  | male       |      23 |    4 |    4 | python2104 | ...      |+--------+----------+------------+---------+------+------+------------+----------+# 使用 on 设置查询条件: 先判断连接条件是否城里,如果成立两张表的数据进行组合生成一条结果记录,之后还可以继续跟随 where 条件进行过滤select * from students inner join classes on students.cid = classes.c_id where stu_gender="famale";+--------+----------+------------+---------+------+------+----------+----------+| stu_id | stu_name | stu_gender | stu_age | cid  | c_id | c_name   | c_remark |+--------+----------+------------+---------+------+------+----------+----------+|      3 | wanger   | famale     |      21 |    1 |    1 | java2101 | ...      |+--------+----------+------------+---------+------+------+----------+----------+
```

#### 2. 左连接(left join)

> 依托于左表进行匹配,右表匹配不到的数据显示 null

#### 3. 右连接(right join)

> 依托于右表进行匹配,左表匹配不到的数据显示 null

#### 4. 数据表别名

> 如果在连接表中有相同的字段,我们可以使用 tbName.field 来区分

使用示例:

```sql
select s.*,c.* from students s inner join classes c on s.cid = c.c_id;
```

#### 5. 子查询/嵌套查询

> 子查询 - 先进行一次查询,第一次查询的结果作为第二次查询的条件

```sql
# 需求: 查询班级名称为"java2102"班级中的学生信息select c_id from classes where c_name="java2102";select * from students where cid in (select c_id from classes where c_name="java2102");+--------+----------+------------+---------+------+| stu_id | stu_name | stu_gender | stu_age | cid  |+--------+----------+------------+---------+------+|      1 | zhangsan | male       |      21 |    2 |+--------+----------+------------+---------+------+# 需求二: 查询所有 java 班级中的学生信息select * from students where cid in (select c_id from classes where c_name like "%java%");+--------+----------+------------+---------+------+| stu_id | stu_name | stu_gender | stu_age | cid  |+--------+----------+------------+---------+------+|      2 | lisi     | male       |      22 |    1 ||      3 | wanger   | famale     |      21 |    1 ||      1 | zhangsan | male       |      21 |    2 ||      4 | erlengzi | male       |      22 |    3 |+--------+----------+------------+---------+------+
```



## 八、存储过程

> 如果我们需要多次重复多次执行相同 sql,sql 执行都需要通过连接传递给 MySQL,并且需要经过编译和执行的步骤
>
> 如果我们需要执行多个 sql 指令,并且第二个指令需要使用第一个 sql 指令的执行结果作为参数;
>
> 这时候我们就需要使用存储过程来进行操作

### 1. 创建存储过程

```sql
create procedure proc_test1(in a int,in b int,out c int)begin	set c = a+b;end;
```

### 2. 调用存储过程

```sql
set @value = 0;call procedure proc_test1(3,2,@value);# 显示变量值select @value fromo dual;
```

### 3. 存储过程的修改和删除以及查看

+ **修改存储过程**

  > 使用  ALTER  语句可以修改存储过程或函数的特性，语法格式如下：
  >
  > `ALTER {PROCEDURE|FUNCTION  }sp_name[characteristic...]  ` 
  >
  > 其中，那  `sp_name` 参数表示存储过程或函数的名称；`characteristic` 参数指定存储函数的特性，可能的取值有:
  >
  > + CONTAINS SQL  表示子程序包含  SQL 语句，但不包含读或写数据的语句。
  > + NO  SQL  表示了程序中不包含 SQL 语句。
  > + READS  SQL DATA  表示子程序中包含读数据的语句。
  > + MODIFIES  SQL DATA  表示子程序中包含写数据的语句。
  > + SQL  SECURITY  {  DEFINER  |   INVOKER  } 指明谁有权限来执行。
  > + DEFINER  表示只有定义者自己才能够执行。
  > + INVOKER 表示调用者可以执行。
  > + COMMENT  'string'  表示注释信息。
  >
  > 【注】修改存储过程使用  ALTER PROCEDURE 语句，修改存储函数使用 ALTER FUNCTION 语句，这两个语句的结构是一样的，语句中的所有参教也是一样的，而且，它们与创建存储过程或函数的语句中的参数基本也是一样的

+ 删除存储过程

  >  删除存储过程和函数可以使用  DROP 语句，其语法结构如下:
  >
  >  `DROP {PROCEDURE|FUNCTION} [IF EXISTS] Sp_name`

+ 查看存储过程

  > 方法一(**MySQL8.0不适用**):
  >
  > `SELCT "NAME" FROM MySQL.proc where db="dbName" and type='PROCEDURE';`
  >
  > 方法二:
  >
  > `show procedure status;`

### 4. 存储过程中变量的使用

> + 局部变量
> + 用户变量

```sql
# 暂时修改结束符delimiter //create procedure proc_test2(in a int,out r int)begin  declare x int default 0;  declare y int default 1;  set x = a*a;  set y = a/2;  set r = x+y;end//delimiter ;set @result = 0;call procedure proc_test2(2,@result);select @result from dual;
```

**当存储函数中出现查询语句需要输出的时候,不能使用`set`而是需要使用`into`**

```sql
delimiter //create procedure proc_test4(out c int)   begin   select count(stu_id) into c from students;   end//delimiter ;set @result=0;call proc_test4(@result);select @result from dual;+---------+| @result |+---------+|       5 |+---------+
```

### 5. 存储过程的参数

> + in
> + out
> + inout

### 6. 存储过程的流程控制

#### 6.1 分支语句

+ **if-then-else**

  ```sql
  delimiter //create procedure proc_test5(in a int)begin	if a = 1 then		insert into classes(c_name,remark) values("java2109","test");	# else			# sql statements	end if;end//delimiter;
  ```

+ **case**

  ```sql
  delimiter //create procedure proc_test6(in a int)begin	case a	when 1 then		# sql statements	when 2 then		# sql statements	else		# sql statements	end case;end//delimiter;
  ```

#### 6.2 循环语句

+ **while**

  ```sql
  delimiter //create procedure proc_test7(in a int)begin	declare i int;	set i = 1;	while 1 < a do		# sql statements		set i = i + 1;	end while;end//delimiter;
  ```

+ **repeat**

  ```sql
  delimiter //create procedure proc_test7(in a int)begin	set @i = 0;	repeat		# sql statements		set i = i + 1;	until i >= a end repeat;end//delimiter;
  ```

+ **loop**

  ```sql
  delimiter //create procedure proc_test6(in a int)begin	set @i=1;	myloop:loop		# sql statements		set @i = @i+1;		if @i = a then			leave myloop;		end if;	end loop;end//delimiter;
  ```

### 7. 存储过程的游标

``` sql
# 创建一个存储过程,返回查询到的一条图书信息delimiter //create procedure proc_test1(in id int,out result varchar(100))begin	declare bname varchar(20);	declare bauthor varchar(20);	declare bprice decimal(10,2);	select book_name,book_author,book_price into bname,bauthor,bprice from books where book_id=id; # 一行数据	set result = concat("~",bname,bauthor,bprice);end//delimiter;set @r = "";call proc_test1(2,@r);select @r from dual;######################delimiter //create procedure proc_test1(out result varchar(100))begin	declare bname varchar(20);	declare bauthor varchar(20);	declare bprice decimal(10,2);	# select book_name,book_author,book_price from books; # 数据集	# 使用游标可以遍历数据集中的每一条数据		declare i int;	declare str varchar(50);	# 游标的声明	declare mycursor cursor for select book_name,book_author,book_price from books;	# 打开游标	open mycursor;	# 使用游标需要结合循环语句	set i = 0;	while i < num do		# 使用游标:提取游标当前指向的记录,并自动指向下一条		fetch mycursor into bname,bauthor,bprice		set i++;		# set result = concat_ws("~",bname,bauthor,bprice); 等同于		select concat_ws("~",bname,bauthor,bprice) into str;		set result = concat_ws(",",result,str);  end while;    # 关闭游标  close mycursor;	end//delimiter;
```



## 十、触发器

### 1.介绍

>触发器,就是一种特殊的存储过程.触发器和存储过程一样是一个能够完成特定功能,存储在服务器上的 SQL 片段,但是触发器无需调用,当数据表中的数据执行 DML 操作的时候,自动触发,无需手动操作
>
>只有在 update\delete\update 操作才能触发触发器

### 2. 触发器的使用

```sql
# 日志记录表: 记录对学生那个信息的操作(记录在什么时间对什么学生进行了什么操作)create table stulog(	id int primary key auto_increment,  time timestamp,  log_text varchar(200));# 当向 students 表中添加数据的时候,同事需要在stulogs 表中添加一条操作日志insert into students(stu_name,stu_gender,stu_age) values("evan","male",22);insert into stulogs(time,log_text) values(now(),"添加1004学生信息");# 当向 students 表中删除数据的时候,同事需要在stulogs 表中添加一条操作日志
```

+ 创建触发器

  ```sql
  # 语法create trigger tri_name<before|after>	# 定义触发器时机<insert|delete|update>	# 定义 DML 类型on <table_name>for each row	# 声明为行级触发器# sql statements
  ```

  ```sql
  # 示例create trigger tri_test1after insert on students# sql statementsfor each rowinsert into stulogs(time,log_text) values(now(),concat("添加",NEW.stu_id),"学生信息");
  ```

+ 查看触发器

  ```sql
  show triggers;
  ```

+ 删除触发器

  ```sql
  drop trigger tri_test1;
  ```

+ new和 old

  > 触发器用于监听对数据表中的 insert\delete\update 操作,在触发器中通常操作一些 DML的关联操作,我们可以使用`new`和`old`关键字在触发器中获取触发这个触发器的 DML操作的数据
  >
  > + `new`: 在触发器中用于获取 insert 操作添加的数据以及 update 操作更新后的数据
  > + `old`: 在触发器中用于获取 delete 操作删除前的数据以及 update 操作谢改前的数据



## 十一、视图

> 虚拟表
>
> + 优点:
>   + 安全性: 如果我们将数据表授权给用户操作,那么用户可以 CURD 数据表中所有数据,加入我们想要对数据表中部分数据进行保护,可以将公开的数据生成视图,授权给用户访问视图;用户通过查询数据表中公开的数据,从而达到讲数据表中的部分数据对用户隐藏
>   + 简单性: 如果我们需要查询的数据来自于多张表,可以使用多表连接查询来实现;我们通过视图将这些数据表的结果对用户开放,用户则可以通过查询视图获取多表数据,操作更便捷

### 1. 创建视图

```sql
create view view_name as select_statements# 将学生表中性别为男的学生生成一个视图create view view_male_students as select * from students where stu_gender="male";select * from view_male_students;
```

### 2. 视图数据的特性

> 视图是虚拟表,查询视图的数据是来源于数据表的. 当对视图进行操作的时候, 对元数据表具有一定影响
>
> + 查询操作: 如果在数据表中添加了心得数据,而且这个数据满足创建视图时查询语句的条件,通过查询视图也可以查询处新增的数据;当删除原表中满足查询条件的数据时,也会从视图中删除
> + 新增数据: 如果在视图中添加数据,数据也会被添加到原数据表
> + 删除数据: 视图中删除数据,原表中也会删除
> + 修改操作: 视图中谢改数据,原表也会被修改
>
> **视图的使用建议**: 对复杂查询简单化,并且不会对数据进行修改的情况

### 3. 查询视图结构

```sql
desc view_test;
```

### 4. 修改视图

```sql
# 方式一create or replace view view_test1 as select_statements;# 方式二alter view view_test1 as select_statements;
```

### 5. 删除视图

删除视图不会影响原来数据表

```sql
drop view view_test1;
```



十二、索引

> 数据库是用来存储数据的,在互联网应用中数据库中存储的数据可能会很大(大数据),数据库中数据的查询速率也会随着数据量的增长二逐渐变慢,导致响应用户请求的速度也会变慢

### 1. 索引的介绍

### 2. 索引的分类

MySQL 中的索引, 根据创建列的不同,可以分为:

+ 主键索引: 在数据表的主键字段创建的索引,这个字段必须被primary key 修饰
+ 唯一索引: 在数据表中的唯一列创建的索引(unique),此列的所有值都只能出现一次,可以为 NULL
+ 普通索引: 在普通字段上创建的索引,没有唯一性的限制
+ 组合索引: 两个及以上的字段组合起来的索引

> 说明:
>
> 1. 在创建数据表的时候,将字段声明为主键,会自动在主键字段创建主键索引
> 2. 在创建数据表的时候,讲字段声明为唯一键,会自动在唯一字段上添加唯一索引

### 3. 创建索引

```sql
# 创建唯一索引create unique index index_test1 on tbName(tid);# 创建普通索引create index index_test2 on tbName(name);# 创建组合索引create index index_test3 on tbName(tid,name);# 创建全文索引	MySQL5.6版本新增的索引,可以通过这个索引记性全文检索操作,因为全文索引不支持中文,因此全文索引不被开发者关注,在应用开发中通常是通过搜索引擎(数据库中间件)实现全文搜索create fulltext index index_test4 on tbName(tid);
```

### 4. 索引的使用

> 索引创建完之后无需调用,当根据创建索引的列进行数据查询的时候,会自动使用索引
>
> 组合索引需要根据创建索引的所有字段进行查询才会触发

+ 在命令行中可以查看查询语句的查询规划

  `explain select * from tb_testindex where tid=25000\G;`

### 5. 查看数据表的索引

```sql
# 方案一show create table tbName\G;# 方案二show indexes from tbName;# 方案三show keys from tbName;
```

### 6. 删除索引

```sql
drop index index_test on tbName;
```



## 十三、事务

### 1. 数据库事务的特性

> ACID 特性, 高频面试题

+ 原子性(atomicty): 一个事务中的多个 DML 操作,要么同时完成,要么执行失败
+ 一致性(Consistency): 事务执行之前和事务执行之后,数据库中的数据是一致的,完成性和一致性不能被破坏
+ 隔离性(Isolate): 数据库允许多个事务同时执行,多个事务之间不能相互影响
+ 持久性(Durability): 事务完成之后对数据的操作是永久的

### 2. MySQL事务管理

#### 2.1 自动提交和手动提交

+ 在 MySQL 中,默认 DML 指令的操作是自动提交的,但我们执行完指令之后,会自动将执行的结果同步到数据库中

#### 2.2 事务管理

> 开启事务,就是关闭自动提交,开启手动提交

```sql
# 开始事务,同时打开手动提交start transaction;#sql1sql1 指令的结果会先存放在缓存中#sql2这里如果执行完 sql2 之后报错,那么就会执行`rollback;`操作,然后进行回滚操作,清空缓存并结束当前事务rollback;#sql3当 sql1 和 sql3 指令都没有发生错误,这时候指令的结果都已经存放进了缓存中,我们要进行手动提交`commit;`commit;至此整个事务结束,符合数据库事务的特性
```



### 3. 事务的隔离级别

> 数据库允许多个事务并行,多个事务之间是隔离的、相互独立的；如果事务之间不相互隔离并且操作统一数据库时，可能会导致一致性被破坏

MySQL 数据库事务隔离级别:

+ 读未提交(read uncommitted)

  T2 可以读取 T1 未执行提交的数据局;可能会导致脏读

  ![image-20211023153648537](https://i.loli.net/2021/10/23/DOSQibVBxjfr8cz.png)

+ 读已提交(read committed)

  T2 只能读取到 T1 已经提交后的数据;避免了脏读数据,但是可能会呆滞不可复读(虚读)

  > 不可复读(虚读): 在同一个事务中,两次查询语句读取到的数据不一致
  >
  > eg: T2 进行第一次查询之后胡在第二次查询之前,T1 修改并提交了数据,T2 第二次查询时读取到的数据和第一次查询读取到的数据不一致。

  ![image-20211023154229290](https://i.loli.net/2021/10/23/V6nGLuE8sUQtYbF.png)

+ 可重复读(repeatable read)

  T2执行第一次查询之后,在事务结束之前不能修改对应的数据;避免了不可重复读(虚读),但是可能会导致幻读

  > 幻读: T2 对数据表中的数据进行了修改然后查询,在查询之前 T1 向数据库表中新增了一条数据,就导致了 T2 以为修改了所有的数据,但是却查询出了和修改不一致的数据(T1 事务新增的数据)

  ![image-20211023154651545](https://i.loli.net/2021/10/23/pLswfOImQ2N9qvY.png)

+ 串行化(serializable): 同时只允许一个事务对数据表进行操作;避免了脏读、虚读、幻读

  |     隔离级别     | 脏读 | 虚读 | 幻读 |
  | :--------------: | :--: | :--: | :--: |
  | read uncommitted |  ✅   |  ✅   |  ✅   |
  |  read committed  |  ❎   |  ✅   |  ✅   |
  |    repeatable    |  ❎   |  ❎   |  ✅   |
  |   serializable   |  ❎   |  ❎   |  ❎   |

### 4. 设置数据库事务隔离级别

> 我们可以通过设置数据库默认的事务隔离级别来控制事务间的隔离性;
>
> 也可以通过客户端与数据库连接设置来设置事务间的隔离性(在应用程序中设置--Spring)
>
> MySQL 数据库默认的隔离级别为: `可重复读`

+ 查看 MySQL 数据库的默认隔离级别

  ```sql
  # 在 MySQL8.0.3 之前select @@tx_isolation;# mysql8.0.3 之后select @@transaction_isolatioin;
  ```

+ 设置 MySQL 默认级别

  ```sql
  set session transaction isolation level <read committed>;
  ```



## 十四、数据库设计

> 能够完成数据的存储, 同时能够方便的提供系统查询所需的数据

### 1. 数据库的设计流程

> 数据库是为了应用系统服务的, 数据库存储什么样的数据也是有应用系统决定的
>
> 当我们进行应用系统开发时,我们首先要明确应用系统功能需求--软件系统的需求分析

1. 根据应用系统的功能, 分析数据实体(实体: 就是要存储的数据对象)

电商系统: 商品、用户、订单…

教务管理系统: 学生、课程、成绩…

2. 提取实体的数据项(数据项: 就是实体的属性)

   商品(商品名称、商品图片、商品描述…)

   用户（姓名、登录名、登录密码…）

3. **根据数据库设计范式规范视图的数据项** 检查实体的数据项是否男足数据库设计三范式

   `如果实体的数据项不满足三范式,可能会导致数据的冗余,从而引起数据库维护困难等问题`

4. **绘制 E-R 图**(实体关系图,直观的展示实体和实体之间的关系)

5. 数据库建模

   1. 三线图进行数据表设计
   2. PowerDesigner
   3. PDman

6. **建库建表**

7. **添加测试数据,sql 测试**

## 2. 数据库设计案例

> 图书管理系统(借书)

1. 数据实体

   + 学生
   + 类别
   + 图书
   + 借书记录
   + 管理员

2. 提取数据项

   + 学生(学号、姓名、性别、年龄、院系编号、院系名称、院系说明……)
   + 类别（类别 ID、类别名称、类别描述……）
   + 图书（图书 ID、图书名称、图书作者、图书封面、图书价格、图书库存）
   + 借书记录（记录 ID、学号、图书编号、数量、是否归还、借书日期、还书日期）
   + 管理员（管理员 ID、登录名、登录密码、员工编号、员工姓名、员工联系方式（手机、qq、邮箱））

3. 数据库设计三范式

   + 第一范式: 要求数据表中的字段不可再分

     ![image-20211023195541823](https://i.loli.net/2021/10/24/oI7mUelAfSdpr9i.png)

   + 第二范式: 不存在非关键字段对关键字段的部分依赖(不满足会出现数据冗余)

   + 第三范式: 不存在非关键字段之间的传递依赖

     ![image-20211023205218021](https://i.loli.net/2021/10/24/PdyfLqUD8gHju2a.png)

   + 绘制 E-R图(Entity-Relationship)实体关系图,用于直观的体现实体与实体(一对一,一对多,多对多)

     **E-R 图基本图例**

     ![image-20211023210550133](https://i.loli.net/2021/10/24/KTuZg1W49vYdx7s.png)

4. 数据库建模

   > E-R 图实际上就是数据建模的一部分:
   >
   > + E-R 图 数据表设计 建库建表
   > + PowerDesigner 建模工具 导出数据表
   > + PDman 建模工具

   