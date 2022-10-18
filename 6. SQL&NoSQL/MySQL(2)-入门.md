# MySQL入门2

# 一. SQL入门

## 1.1 介绍

我们都知道，数据库管理人员（DBA）通过数据库管理系统（DBMS）可以对数据库（DB）中的数据进行操作，但具体是如何操作的呢？这就涉及到我们本节要讲的SQL语言。  			

SQL（Structured Query Language）是结构化查询语言的简称，它是一种数据库查询和程序设计语言，同时也是目前使用最广泛的关系型数据库操作语言。在数据库管理系统中，使用SQL语言来实现数据的存取、查询、更新等功能。SQL 是一种非过程化语言，只需提出“做什么”，而不需要指明“怎么做”。 			

   SQL 是由 IBM 公司在 1974~1979 年之间根据 E.J.Codd 发表的关系数据库理论为基础开发的，其前身是“SEQUEL”，后更名为 SQL。由于 SQL 语言具有集数据查询、数据操纵、数据定义和数据控制功能于一体，类似自然语言、简单易用以及非过程化等特点，得到了快速的发展，并于 1986 年 10 月，被美国国家标准协会（American National Standards Institute，ANSI）采用为关系数据库管理系统的标准语言，后为国际标准化组织（International Organization for Standardization，ISO）采纳为国际标准。

## 1.2 分类

数据查询语言（Data Query Language，DQL）：DQL 主要用于数据的查询，其基本结构是使用 SELECT 子句，FROM 子句和 WHERE 子句的组合来查询一条或多条数据。

数据操作语言（Data Manipulation Language，DML）：DML主要用于对数据库中的数据进行增加、修改和删除的操作，其主要包括：

1. INSERT：增加数据  			

2. UPDATE：修改数据  			

3. DELETE：删除数据

数据定义语言（Data Definition Language，DDL）：DDL主要用针对是数据库对象（数据库、表、索引、视图、触发器、存储过程、函数）进行创建、修改和删除操作。其主要包括：

1. CREATE：创建数据库对象
2. ALTER：修改数据库对象  			
3. DROP：删除数据库对象  			

数据控制语言（Data Control Language，DCL）：DCL用来授予或回收访问 数据库的权限，其主要包括：1. 1. 

1. GRANT：授予用户某种权限
2. REVOKE：回收授予的某种权限

事务控制语言（Transaction Control Language，TCL）：TCL用于数据库的事务管理。其主要包括：

1. START TRANSACTION：开启事务  			
2. COMMIT：提交事务  			
3. ROLLBACK：回滚事务  			
4. SET TRANSACTION：设置事务的属性 

# 二. DDL

## 2.1 认识数据库表

表（Table）是数据库中数据存储最常见和最简单的一种形式，数据库可以将复杂的数据结构用较为简单的二维表来表示。二维表是由行和列组成的，分别都包含着数据，如表所示。

每个表都是由若干行和列组成的，在数据库中表中的行被称为记录，表中的列被称为是这些记录的字段。  	

记录也被称为一行数据，是表里的一行。在关系型数据库的表里，一行数据是指一条完整的记录。  			

字段是表里的一列，用于保存每条记录的特定信息。如上表所示的学生信息表中的字段包括“学号”、“姓名”、“性别”和“年龄”。数据表的一列包含了某个特定字段的全部信息。 

## 2.2 创建数据库表

- 建立一张用来存储学生信息的表 				
- 字段包含学号、姓名、性别，年龄、入学日期、班级，email等信息 				
- 学号是主键 = 不能为空 + 唯一 				
- 姓名不能为空 				
- 性别默认值是男 				
- Email 唯一

（1）创建数据库：

名称设置为： `t_student`，字符集设置为：`utf8mb4`。

（2）新建查询：

```mysql
# 这是一个单行注释
/*
多行注释
多行注释
多行注释
*/
/*
建立一张用来存储学生信息的表
字段包含学号、姓名、性别，年龄、入学日期、班级，email等信息
*/
-- 创建数据库表：
create table t_student(
    sno int(6), -- 6显示长度 
    s_name varchar(5), -- 5个字符
    sex char(1),
    age int(3),
    enter_date date,
    class_name varchar(10),
    email varchar(15)
);
-- 查看表的结构：展示表的字段详细信息
desc t_student;
-- 查看表中数据：
select * from t_student;
-- 查看建表语句：
show create table t_student;
/*
CREATE TABLE `t_student` (
  `sno` int DEFAULT NULL,
  `s_name` varchar(5) DEFAULT NULL,
  `sex` char(1) DEFAULT NULL,
  `age` int DEFAULT NULL,
  `enter_date` date DEFAULT NULL,
  `class_name` varchar(10) DEFAULT NULL,
  `email` varchar(15) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
*/
```

## 2.3 修改删除数据集表

```mysql
-- 修改表的结构：
-- 增加一列：
alter table t_student add score double(5,2) ; -- 5:总位数  2：小数位数 
update t_student set score = 123.5678 where sno = 1 ;
-- 增加一列（放在最前面）
alter table t_student add score double(5,2) first;
-- 增加一列（放在sex列的后面）
alter table t_student add score double(5,2) after sex;
-- 删除一列：
alter table t_student drop score;
-- 修改一列：
-- modify修改是列的类型的定义，但是不会改变列的名字
alter table t_student modify score float(4,1); 
-- change修改列名和列的类型的定义
alter table t_student change score score1 double(5,1); 
-- 删除表：
drop table t_student;
```

# 三. 数据库表数据类型

## 3.1 整数类型

| 类型        | 大小(Byte) | 有符号范围            | 无符号范围      | 作用       |
| ----------- | ---------- | --------------------- | --------------- | ---------- |
| tinyint     | 1          | $[-2^7, 2^7-1]$       | $[0, 2^8-1]$    | 小整数值   |
| smallint    | 2          | $[-2^{15}, 2^{15}-1]$ | $[0, 2^{16}-1]$ | 大整数值   |
| mediumint   | 3          | $[-2^{23}, 2^{23}-1]$ | $[0, 2^{24}-1]$ | 大整数值   |
| int/integer | 4          | $[-2^{31}, 2^{31}-1]$ | $[0, 2^{31}-1]$ | 大整数值   |
| bigint      | 8          | $[-2^{63}, 2^{63}-1]$ | $[0, 2^{64}-1]$ | 极大整数值 |

MySQL 支持选择在该类型关键字后面的括号内指定整数值的显示宽度(例如，INT(4))。显示宽度并不限制可以在列内保存的值的范围，也不限制超过列的指定宽度的值的显示 				

主键自增：不使用序列，通过 **auto_increment**，要求是整数类型。

## 3.2 浮点数类型

| 浮点数类型 | 大小(Byte) | 作用           |
| ---------- | ---------- | -------------- |
| float      | 4          | 单精度浮点数值 |
| double     | 8          | 双精度浮点数值 |

需要注意的是与整数类型不一样的是，浮点数类型的宽度不会自动扩充。	

```mysql
score double(4,1)
```

小数部分为 1 位，总宽度 4 位，并且不会自动扩充。

## 3.3 字符串类型

| 字符串类型 | 大小(字符)          | 描述                                          |
| ---------- | ------------------- | --------------------------------------------- |
| char(M)    | 0~255               | 允许长度为 0~M 个字符的定长字符串             |
| varchar(M) | 0~65535             | 允许长度为 0~M 个字符的边长字符串             |
| binary(M)  | 0~255               | 允许长度为 0~M 个字节的定长二进制字符串       |
| varbinary  | 0~65535             | 允许长度为 0~M 个字节的变长二进制字符串       |
| tinyblob   | 0~255               | 二进制形式的短文本数据，长度不超过 255 个字符 |
| tinytext   | 0~255               | 短文本数据                                    |
| blob       | binary/large/object | 二进制形式的长文本数据                        |
| text       |                     | 长文本数据                                    |
| mediumblob | 0~16777215          | 二进制形式的中等长度文本数据                  |
| mediumtext | 0~16777215          | 中等长度文本数据                              |
| longblob   | 0~4294967295        | 二进制形式的极大文本数据                      |
| longtext   | 0~4294967295        | 极大文本数据                                  |

char 和 varchar 类型相似，均用于存于较短的字符串，主要的不同之处在于存储方式。char 类型长度固定，varchar 类型的长度可变。 				

因为 varchar 类型能够根据字符串的实际长度来动态改变所占字节的大小，所以在不能明确该字段具体需要多少字符时推荐使用 varchar 类型，这样可以大大地节约磁盘空间、提高存储效率。 				

char 和 varchar 表示的是字符的个数，而不是字节的个数。

## 3.4 日期和时间类型

| 类型      | 格式                  | 取值范围                                              | 0值                   |
| --------- | --------------------- | ----------------------------------------------------- | --------------------- |
| time      | 'HH:MM:SS'            | ('-838:59:59','838:59:59')                            | '00:00:00'            |
| date      | 'YYYY-MM-DD'          | ('1000-01-01','9999-12-31')                           | '0000-00-00'          |
| year      | 'YYYY'                | (1901,2155)                                           | 0000                  |
| datetime  | 'YYYY-MM-DD HH:MM:SS' | ('1000-01-01 00:00:00','9999-12-31 23:59:59')         | '0000-00-00 00:00:00' |
| timestamp | 'YYYY-MM-DD HH:MM:SS' | ('1970-01-01 00:00:01' UTC,'2038-01-19 03:14:07' UTC) | '0000-00-00 00:00:00' |

timestamp 类型的数据指定方式与 datetime 基本相同，两者的不同之处在于以下几点： 			

(1) 数据的取值范围不同，timestamp 类型的取值范围更小。 			

(2) 如果我们对 timestamp 类型的字段没有明确赋值，或是被赋与了 null 值，MySQL 会自动将该字段赋值为系统当前的日期与时间。 			

(3) timestamp 类型还可以使用 current_timestamp 来获取系统当前时间。 			

(4) timestamp 类型有一个很大的特点，那就是时间是根据时区来显示的。例如，在东八区插入的 timestamp 数据为 2017-07-11 16:43:25，在东七区显示时，时间部分就变成了15:43:25，在东九区显示时，时间部分就变成了17:43:25。  

# 四. DML

## 4.1 添加数据

注意事项：

1. int 宽度是显示宽度，如果超过，可以自动增大宽度 int底层都是4个字节 				
2. 时间的方式多样 '1256-12-23' "1256/12/23" "1256.12.23" 				
3. 字符串不区分单引号和双引号 				
4. 如何写入当前的时间 `now()` , `sysdate()` , `CURRENT_DATE()` 				
5. char varchar 是字符的个数，不是字节的个数，可以使用 binary，varbinary 表示定长和不定长的字节个数。 				
6. 如果不是全字段插入数据的话，需要加入字段的名字

```mysql
-- 查看表记录：
select * from t_student;
-- 在t_student数据库表中插入数据：
insert into t_student values (1,'张三','男',18,'2022-5-8','软件1班','123@126.com');
insert into t_student values (10010010,'张三','男',18,'2022-5-8','软件1班','123@126.com');
insert into t_student values (2,'张三','男',18,'2022.5.8','软件1班','123@126.com');
insert into t_student values (2,"张三",'男',18,'2022.5.8','软件1班','123@126.com');
insert into t_student values (7,"张三",'男',18,now(),'软件1班','123@126.com');
insert into t_student values (9,"易烊千玺",'男',18,now(),'软件1班','123@126.com');
insert into t_student (sno,s_name,enter_date) values (10,'李四','2023-7-5');
```

## 4.2 修改删除数据

注意事项：

1. 关键字，表名，字段名不区分大小写 	

2. 默认情况下，内容不区分大小写 	

3. 删除操作 from 关键字不可缺少 	

4. 修改，删除数据别忘记加限制条件

```mysql
-- 修改表中数据
update t_student set sex = '女' ;
update t_student set sex = '男' where sno = 10 ;
UPDATE T_STUDENT SET AGE = 21 WHERE SNO = 10;
update t_student set CLASS_NAME = 'java01' where sno = 10 ;
update t_student set CLASS_NAME = 'JAVA01' where sno = 9 ;
update t_student set age = 29 where class_name = 'java01';
-- 删除操作：
delete from t_student where sno = 2;
```

# 五. 表的完整性约束

## 5.1 非外键约束

约束从作用上可以分为两类：

1. 表级约束：可以约束表中任意一个或多个字段。与列定义相互独立，不包含在列定义中；与定义用‘，’分隔；必须指出要约束的列的名称。
2. 列级约束：包含在列定义中，直接跟在该列的其它定义之后 ，用空格分隔；不必指定列名。

在创建表的时候添加约束：

```mysql
-- 删除表：
drop table t_student;
-- 创建数据库表：
create table t_student(
    sno int(6) auto_increment, 
    sname varchar(5) not null, 
    sex char(1) default '男',
    age int(3),
    enterdate date,
    classname varchar(10),
    email varchar(15),
    constraint pk_stu primary key (sno),  -- pk_stu 主键约束的名字
    constraint ck_stu_sex check (sex = '男' || sex = '女'),
    constraint ck_stu_age check (age >= 18 and age <= 50),
    constraint uq_stu_email unique (email)
);
-- 添加数据：
insert into t_student values (1,'张三','男',21,'2023-9-1','java01班','zs@126.com');
-- > 3819 - Check constraint 'ck_stu_sex' is violated.
-- > 3819 - Check constraint 'ck_stu_age' is violated.
-- > 1062 - Duplicate entry 'zs@126.com' for key 't_student.uq_stu_email'
insert into t_student values (3,'李四','男',21,'2023-9-1','java01班','zs@126.com');
-- 查看数据：
select * from t_student;
```

在创建表之后添加约束：

```mysql
-- 删除表：
drop table t_student;
-- 创建数据库表：
create table t_student(
    sno int(6), 
    sname varchar(5) not null, 
    sex char(1) default '男',
    age int(3),
    enterdate date,
    classname varchar(10),
    email varchar(15)
);
-- > 1075 - Incorrect table definition; there can be only one auto column and 
-- > it must be defined as a key
-- 错误的解决办法：就是auto_increment去掉
-- 在创建表以后添加约束：
alter table t_student add constraint pk_stu primary key (sno) ; -- 主键约束
alter table t_student modify sno int(6) auto_increment; -- 修改自增条件
alter table t_student add constraint ck_stu_sex check (sex = '男' || sex = '女');
alter table t_student add constraint ck_stu_age check (age >= 18 and age <= 50);
alter table t_student add constraint uq_stu_email unique (email);
-- 查看表结构：
desc t_student;
```

总结：

1. 主键约束：

主键约束（PRIMARY KEY，缩写PK），是数据库中最重要的一种约束，其作用是约束表中的某个字段可以**唯一标识一条记录**。因此，使用主键约束可以快速查找表中的记录。就像人的身份证、学生的学号等等，**设置为主键的字段取值不能重复(唯一)，也不能为空(非空)**，否则无法唯一标识一条记录。  				

主键可以是单个字段，也可以是多个字段组合。对于单字段主键的添加可使用表级约束，也可以使用列级约束；而对于多字段主键的添加只能使用表级约束。

2. 非空约束：

非空约束（NOT NULL，缩写NK）规定了一张表中指定的某个字段的值不能为空（NULL）。设置了非空约束的字段，在插入的数据为 NULL 时，数据库会提示错误，导致数据无法插入。  			

无论是单个字段还是多个字段非空约束的添加只能使用列级约束（非空约束无表级约束）。

为已存在表中的字段添加非空约束：

```mysql
 alter table t_student modify stu_sex varchar(1) not null;
```

使用 ALTER TABLE 语句删除非空约束：

```mysql
alter table t_student modify stu_sex varchar(1) null;
```

3. 唯一约束：

唯一约束（UNIQUE，缩写UK）比较简单，它规定了一张表中指定的某个字段的值不能重复，即这一字段的每个值都是唯一的。如果想要某个字段的值不重复，那么就可以为该字段添加为唯一约束。  			

无论单个字段还是多个字段唯一约束的添加均可使用列级约束和表级约束。

4. 检查约束：

检查约束（CHECK）用来限制某个字段的取值范围，可以定义为列级约束，也可以定义为表级约束。MySQL8开始支持检查约束。 

5. 默认值约束：

默认值约束（DEFAULT）用来规定字段的默认值。如果某个被设置为 DEFAULT 约束的字段没插入具体值，那么该字段的值将会被默认值填充。  			

默认值约束的设置与非空约束一样，也只能使用列级约束。

6. 字段值自动增加约束：

自增约束（AUTO_INCREMENT）可以使表中某个字段的值自动增加。一张表中只能有一个自增长字段，并且该字段必须定义了约束（该约束可以是主键约束、唯一约束以及外键约束），如果自增字段没有定义约束，数据库则会提示 "Incorrect table definition; there can be only one auto column and it must be defined as a key" 错误。

由于自增约束会自动生成唯一的ID，所以自增约束通常会配合主键使用，并且只适用于整数类型。一般情况下，设置为自增约束字段的值会从 1 开始，每增加一条记录，该字段的值加 1。

为已存在表中的字段添加自增约束：

```mysql
alter table t_student modify stu_id int(10) auto_increment;
```

使用ALTER TABLE语句删除自增约束：

```mysql
alter table t_studen modify stu_id int(10);
```

## 5.2 外键约束

什么是外键约束？

外键约束（FOREIGN KEY，缩写FK）是用来实现数据库表的参照完整性的。外键约束可以使两张表紧密的结合起来，特别是针对修改或者删除的级联操作时，会保证数据的完整性。 			

外键是指表中某个字段的值依赖于另一张表中某个字段的值，而被依赖的字段**必须具有主键约束或者唯一约束**。被依赖的表我们通常称之为父表或者主表，设置外键约束的表称为子表或者从表。 			

举个例子：如果想要表示学生和班级的关系，首先要有学生表和班级表两张表，然后学生表中有个字段为stu_clazz（该字段表示学生所在的班级），而该字段的取值范围由班级表中的主键 cla_no 字段（该字段表示班级编号）的取值决定。那么班级表为主表，学生表为从表，且 stu_clazz 字段是学生表的外键。通过stu_clazz字段就建立了学生表和班级表的关系。

```mysql
-- 先创建父表：班级表：
create table t_class(
    cno int(4) primary key auto_increment,
    cname varchar(10) not null,
    room char(4)
)
-- 添加班级数据：
insert into t_class values (null,'java001','r803');
insert into t_class values (null,'java002','r416');
insert into t_class values (null,'大数据001','r103');
-- 可以一次性添加多条记录：
insert into t_class values (null,'java001','r803'),(null,'java002','r416'),(null,'大数据001','r103');
-- 查询班级表：
select * from t_class;
-- 学生表删除：
drop table t_student;
-- 创建子表,学生表：
create table t_student(
    sno int(6) primary key auto_increment, 
    sname varchar(5) not null, 
    classno int(4)  -- 取值参考t_class表中的cno字段，不要求字段名字完全重复，但是类型长度定义 尽量要求相同。
);
-- 添加学生信息：
insert into t_student values (null,'张三',1),(null,'李四',1),(null,'王五',2);
-- 查看学生表：
select * from t_student;
-- 出现问题：
-- 1.添加一个学生对应的班级编码为4：
insert into t_student values (null,'丽丽',4);
-- 2.删除班级2：
delete from t_class where cno = 2;
-- 出现问题的原因：
-- 因为你现在的外键约束，没用语法添加进去，现在只是逻辑上认为班级编号是外键，没有从语法上定义
-- 解决办法，添加外键约束：
-- 注意：外键约束只有表级约束，没有列级约束：
create table t_student(
    sno int(6) primary key auto_increment, 
    sname varchar(5) not null, 
    classno int(4),-- 取值参考t_class表中的cno字段，不要求字段名字完全重复，但是类型长度定义 尽量要求相同。
    constraint fk_stu_classno foreign key (classno) references t_class (cno)
);
create table t_student(
    sno int(6) primary key auto_increment, 
    sname varchar(5) not null, 
    classno int(4)
);
-- 在创建表以后添加外键约束：
alter table t_student add constraint fk_stu_classno foreign key (classno) references t_class (cno)
-- 上面的两个问题都解决了：
-- 添加学生信息：
-- > 1452 - Cannot add or update a child row: a foreign key constraint fails (`mytestdb`.`t_student`, CONSTRAINT `fk_stu_classno` FOREIGN KEY (`classno`) REFERENCES `t_class` (`cno`))
insert into t_student values (null,'张三',1),(null,'李四',1),(null,'王五',2);
-- 删除班级1：
-- 2.删除班级2：
insert into t_student values (null,'张三',3),(null,'李四',3),(null,'王五',3);
-- > 1451 - Cannot delete or update a parent row: a foreign key constraint fails (`mytestdb`.`t_student`, CONSTRAINT `fk_stu_classno` FOREIGN KEY (`classno`) REFERENCES `t_class` (`cno`))
delete from t_class where cno = 3;
```

## 5.3 外键策略

```mysql
-- 学生表删除：
drop table t_student;
-- 班级表删除：
drop table t_class;
-- 注意：先删除从表，再删除主表。（视频中这个位置笔误，笔记现在已经更正）
-- 先创建父表：班级表：
create table t_class(
    cno int(4) primary key auto_increment,
    cname varchar(10) not null,
    room char(4)
)
-- 可以一次性添加多条记录：
insert into t_class values 
(null,'java001','r803'),(null,'java002','r416'),(null,'大数据001','r103');
-- 添加学生表，添加外键约束：
create table t_student(
    sno int(6) primary key auto_increment, 
    sname varchar(5) not null, 
    classno int(4),-- 取值参考t_class表中的cno字段，不要求字段名字完全重复，但是类型长度定义 尽量要求相同。
    constraint fk_stu_classno foreign key (classno) references t_class (cno)
);
-- 可以一次性添加多条记录：
insert into t_student values 
(null,'张三',1),(null,'李四',1),(null,'王五',2),(null,'朱六',3);
-- 查看班级表和学生表：
select * from t_class;
select * from t_student;
-- 删除班级2：如果直接删除的话肯定不行因为有外键约束：
-- 加入外键策略：
-- 策略1：no action 不允许操作
-- 通过操作sql来完成：
-- 先把班级2的学生对应的班级 改为null 
update t_student set classno = null where classno = 2;
-- 然后再删除班级2：
delete from t_class where cno = 2;
-- 策略2：cascade 级联操作：操作主表的时候影响从表的外键信息：
-- 先删除之前的外键约束：
alter table t_student drop foreign key fk_stu_classno;
-- 重新添加外键约束：
alter table t_student add constraint fk_stu_classno foreign key (classno) 
references t_class (cno) on update cascade on delete cascade;
-- 试试更新：
update t_class set cno = 5 where cno = 3;
-- 试试删除：
delete from t_class where cno = 5;
-- 策略3：set null  置空操作：
-- 先删除之前的外键约束：
alter table t_student drop foreign key fk_stu_classno;
-- 重新添加外键约束：
alter table t_student add constraint fk_stu_classno foreign key (classno) 
references t_class (cno) on update set null on delete set null;
-- 试试更新：
update t_class set cno = 8 where cno = 1;
-- 注意：
-- 1. 策略2 级联操作  和  策略2 的  删除操作  可以混着使用：
alter table t_student add constraint fk_stu_classno foreign key (classno) 
references t_class (cno) on update cascade on delete set null ;
-- 2.应用场合：
-- （1）朋友圈删除，点赞。留言都删除  --  级联操作
-- （2）解散班级，对应的学生 置为班级为null就可以了，-- set null
```

## 5.4 DDL、DML补充

```mysql
-- 创建表：
create table t_student(
    sno int(6) primary key auto_increment, 
    sname varchar(5) not null, 
    sex char(1) default '男' check(sex='男' || sex='女'),
    age int(3) check(age>=18 and age<=50),
    enterdate date,
    classname varchar(10),
    email varchar(15) unique
);

-- 添加数据：
insert into t_student values (null,'张三','男',21,'2023-9-1','java01班','zs@126.com');
insert into t_student values (null,'李四','男',21,'2023-9-1','java01班','ls@126.com');
insert into t_student values (null,'露露','男',21,'2023-9-1','java01班','ll@126.com');
-- 查看学生表：
select * from t_student;
-- 添加一张表：快速添加：结构和数据跟t_student 都是一致的
create table t_student2
as
select * from t_student;
select * from t_student2;
-- 快速添加，结构跟t_student一致，数据没有：
create table t_student3
as
select * from t_student where 1=2;
select * from t_student3;
-- 快速添加：只要部分列，部分数据：
create table t_student4
as
select sno,sname,age from t_student where sno = 2;
select * from t_student4;
-- 删除数据操作 :清空数据
delete from t_student;
truncate table t_student;
```

**delete 和 truncate 的区别：**

从最终的结果来看，虽然使用 TRUNCATE 操作和使用 DELETE 操作都可以删除表中的全部记录，但是两者还是有很多区别的，其区别主要体现在以下几个方面：  		

(1) DELETE 为数据操作语言 DML；TRUNCATE为数据定义语言 DDL。  		

(2) DELETE 操作是将表中所有记录一条一条删除直到删除完；TRUNCATE 操作则是保留了表的结构，重新创建了这个表，所有的状态都相当于新表。因此，TRUNCATE 操作的效率更高。  		

(3) DELETE 操作可以回滚；TRUNCATE 操作会导致隐式提交，因此不能回滚。  		

(4) DELETE 操作执行成功后会返回已删除的行数（如删除4行记录，则会显示“Affected rows：4”）；截断操作不会返回已删除的行量，结果通常是“Affected rows：0”。DELETE操作删除表中记录后，再次向表中添加新记录时，对于设置有自增约束字段的值会从删除前表中该字段的最大值加 1 开始自增；TRUNCATE 操作则会重新从 1 开始自增。

