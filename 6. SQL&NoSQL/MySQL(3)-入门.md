# MySQL入门3

# 一. DQL

## 1.1 创建表

准备四张表：dept(部门表)，emp(员工表)，salgrade(薪资等级表)，bonus(奖金表)：

```mysql
create table DEPT(  
  DEPTNO int(2) not null,  
  DNAME  VARCHAR(14),  
  LOC    VARCHAR(13)  
);  
alter table DEPT  
  add constraint PK_DEPT primary key (DEPTNO); 
        
create table EMP  
(  
  EMPNO    int(4) primary key,  
  ENAME    VARCHAR(10),  
  JOB      VARCHAR(9),  
  MGR      int(4),  
  HIREDATE DATE,  
  SAL      double(7,2),  
  COMM     double(7,2),  
  DEPTNO   int(2)  
);  
alter table EMP  
add constraint FK_DEPTNO foreign key (DEPTNO)  
references DEPT (DEPTNO);  

create table SALGRADE  
(  
  GRADE int primary key,  
  LOSAL double(7,2),  
  HISAL double(7,2)  
);  
create table BONUS  
(  
  ENAME VARCHAR(10),  
  JOB   VARCHAR(9),  
  SAL   double(7,2),  
  COMM  double(7,2)  
);  
insert into DEPT (DEPTNO, DNAME, LOC)  
values (10, 'ACCOUNTING', 'NEW YORK');  
insert into DEPT (DEPTNO, DNAME, LOC)  
values (20, 'RESEARCH', 'DALLAS');  
insert into DEPT (DEPTNO, DNAME, LOC)  
values (30, 'SALES', 'CHICAGO');  
insert into DEPT (DEPTNO, DNAME, LOC)  
values (40, 'OPERATIONS', 'BOSTON');  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7369, 'SMITH', 'CLERK', 7902, '1980-12-17', 800, null, 20);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7499, 'ALLEN', 'SALESMAN', 7698, '1981-02-20', 1600, 300, 30);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7521, 'WARD', 'SALESMAN', 7698, '1981-02-22', 1250, 500, 30);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7566, 'JONES', 'MANAGER', 7839, '1981-04-02', 2975, null, 20);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7654, 'MARTIN', 'SALESMAN', 7698, '1981-09-28', 1250, 1400, 30);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7698, 'BLAKE', 'MANAGER', 7839, '1981-05-01', 2850, null, 30);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7782, 'CLARK', 'MANAGER', 7839, '1981-06-09', 2450, null, 10);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7788, 'SCOTT', 'ANALYST', 7566, '1987-04-19', 3000, null, 20);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7839, 'KING', 'PRESIDENT', null, '1981-11-17', 5000, null, 10);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7844, 'TURNER', 'SALESMAN', 7698, '1981-09-08', 1500, 0, 30);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7876, 'ADAMS', 'CLERK', 7788, '1987-05-23', 1100, null, 20);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7900, 'JAMES', 'CLERK', 7698, '1981-12-03', 950, null, 30);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7902, 'FORD', 'ANALYST', 7566, '1981-12-03', 3000, null, 20);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7934, 'MILLER', 'CLERK', 7782, '1982-01-23', 1300, null, 10);  
insert into SALGRADE (GRADE, LOSAL, HISAL)  
values (1, 700, 1200);  
insert into SALGRADE (GRADE, LOSAL, HISAL)  
values (2, 1201, 1400);  
insert into SALGRADE (GRADE, LOSAL, HISAL)  
values (3, 1401, 2000);  
insert into SALGRADE (GRADE, LOSAL, HISAL)  
values (4, 2001, 3000);  
insert into SALGRADE (GRADE, LOSAL, HISAL)  
values (5, 3001, 9999);  
-- 查看表：
select * from dept; 
-- 部门表：dept:department 部分 ，loc - location 位置
select * from emp;
-- 员工表：emp:employee 员工   ,mgr :manager上级领导编号，hiredate 入职日期  firedate 解雇日期 ，common：补助
-- deptno 外键 参考  dept - deptno字段
-- mgr 外键  参考  自身表emp - empno  产生了自关联
select * from salgrade;
-- losal - lowsal
-- hisal - highsal
select * from bonus;
```

## 1.2 单表查询

### 1.2.1 简单的查询：

```mysql
-- 对emp表查询：
select * from emp; -- *代表所有数据
-- 显示部分列：
select empno,ename,sal from emp;
-- 显示部分行：where子句
select * from emp where sal > 2000;
-- 显示部分列，部分行：
select empno,ename,job,mgr from emp where sal > 2000;
-- 起别名：
select empno 员工编号,ename 姓名,sal 工资 from emp; -- as 省略，''或者""省略了
-- as alias 别名
select empno as 员工编号,ename as 姓名,sal as 工资 from emp;
select empno as '员工编号',ename as "姓名",sal as 工资 from emp;
-- > 1064 - You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '编号,ename as "姓 名",sal as 工资 from emp' at line 1
-- 错误原因：在别名中有特殊符号的时候，''或者""不可以省略不写
select empno as 员工 编号,ename as "姓 名",sal as 工资 from emp;
-- 算术运算符：
select empno,ename,sal,sal+1000 as '涨薪后',deptno from emp where sal < 2500;
select empno,ename,sal,comm,sal+comm from emp;  -- ？？？后面再说
-- 去重操作：
select job from emp;
select distinct job from emp;
select job,deptno from emp;
select distinct job,deptno from emp; -- 对后面的所有列组合 去重 ，而不是单独的某一列去重
-- 排序：
select * from emp order by sal; -- 默认情况下是按照升序排列的
select * from emp order by sal asc; -- asc 升序，可以默认不写
select * from emp order by sal desc; -- desc 降序
select * from emp order by sal asc, deptno desc; -- 在工资升序的情况下，deptno按照降序排列
```

### 1.2.2 where子句

**where搭配关系运算符：**



```mysql
-- 查看emp表：
select * from emp;
-- where子句：将过滤条件放在where子句的后面，可以筛选/过滤出我们想要的符合条件的数据：
-- where 子句 + 关系运算符
select * from emp where deptno = 10;
select * from emp where deptno > 10;
select * from emp where deptno >= 10;
select * from emp where deptno < 10;
select * from emp where deptno <= 10;
select * from emp where deptno <> 10;
select * from emp where deptno != 10;
select * from emp where job = 'CLERK'; 
select * from emp where job = 'clerk'; -- 默认情况下不区分大小写 
select * from emp where binary job = 'clerk'; -- binary区分大小写
select * from emp where hiredate < '1981-12-25';
-- where 子句 + 逻辑运算符：and 
select * from emp where sal > 1500 and sal < 3000;  -- (1500,3000)
select * from emp where sal > 1500 && sal < 3000; 
select * from emp where sal > 1500 and sal < 3000 order by sal;
select * from emp where sal between 1500 and 3000; -- [1500,3000]
-- where 子句 + 逻辑运算符：or
select * from emp where deptno = 10 or deptno = 20;
select * from emp where deptno = 10 || deptno = 20;
select * from emp where deptno in (10,20);
select * from emp where job in ('MANAGER','CLERK','ANALYST');
-- where子句 + 模糊查询：
-- 查询名字中带A的员工  -- %代表任意多个字符 0,1,2，.....
select * from emp where ename like '%A%' ;
-- -任意一个字符
select * from emp where ename like '__A%' ;
-- 关于null的判断：
select * from emp where comm is null;
select * from emp where comm is not null;
-- 小括号的使用  ：因为不同的运算符的优先级别不同，加括号为了可读性
-- 先and再or  and > or
select * from emp where job = 'SALESMAN' or job = 'CLERK' and sal >= 1500; 
select * from emp where job = 'SALESMAN' or (job = 'CLERK' and sal >= 1500); 
select * from emp where (job = 'SALESMAN' or job = 'CLERK') and sal >= 1500;
```

### 1.2.3 使用函数

MySQL 中提供了大量函数来简化用户对数据库的操作，比如字符串的处理、日期的运算、数值的运算等等。使用函数可以大大提高 SELECT 语句操作数据库的能力，同时也给数据的转换和处理提供了方便。 （在sql中使用函数） 		

函数只是对查询结果中的数据进行处理，不会改变数据库中数据表的值。MySQL中的函数主要分为单行函数和多行函数两大类，下面我们将详细讲解这两大类函数。 

**单行函数：**

单行函数是指对每一条记录输入值进行计算，并得到相应的计算结果，然后返回给用户，也就是说，每条记录作为一个输入参数，经过函数计算得到每条记录的计算结果。 			

常用的单行函数主要包括**字符串函数、数值函数、日期与时间函数、流程函数以及其他函数。** 

**多行函数：**

多行函数是指对一组数据进行运算，针对这一组数据（多行记录）只返回一个结果，也称为分组函数。 

```mysql
-- 函数举例：
select empno,ename,lower(ename),upper(ename),sal from emp;
-- 函数的功能：封装了特定的一些功能，我们直接拿过来使用，可以实现对应的功能
-- 函数作用：为了提高select的能力
-- 注意：函数没有改变数据自身的值，而是在真实数据的上面进行加工处理，展示新的结果而已。
select max(sal), min(sal), count(sal), sum(sal), avg(sal) from emp;
-- 函数的分类：
-- lower(ename),upper(ename) ：改变每一条结果，每一条数据对应一条结果  -- 单行函数
-- max(sal),min(sal),count(sal),sum(sal),avg(sal):多条数据，最终展示一个结果  -- 多行函数
```

### 1.2.4 group by

`group by` 是用来进行对字段进行分组。

```mysql
select * from emp;
-- 统计各个部门的平均工资 
-- 字段和多行函数不可以同时使用
select deptno,avg(sal) from emp; 
-- 字段和多行函数不可以同时使用,除非这个字段属于分组
select deptno,avg(sal) from emp group by deptno; 
select deptno,avg(sal) from emp group by deptno order by deptno desc;
-- 统计各个岗位的平均工资
select job,avg(sal) from emp group by job;
select job,lower(job),avg(sal) from emp group by job;
```

### 1.2.5 having

`having` 用于对分组过后的记录进行过滤，`where` 则是对分组之前的记录进行过滤。

```mysql
-- 统计各个部门的平均工资 ,只显示平均工资2000以上的  - 分组以后进行二次筛选 having
select deptno,avg(sal) from emp group by deptno having avg(sal) > 2000;
select deptno,avg(sal) 平均工资 from emp group by deptno having 平均工资 > 2000;
select deptno,avg(sal) 平均工资 from emp group by deptno having 平均工资 > 2000 
order by deptno desc;
-- 统计各个岗位的平均工资,除了MANAGER
-- 方法1：
select job,avg(sal) from emp where job != 'MANAGER' group by job;
-- 方法2：
select job,avg(sal) from emp group by job having job != 'MANAGER' ;
-- where在分组前进行过滤的，having在分组后进行后滤。
```

### 1.2.6 总结

select 语句总结：

```mysql
select 
	column, group_function(column) 
from 
	`table_name` 
[where condition]  					-- 过滤条件
[group by group_by_expression] 	   -- 分组表达式
[having group_condition] 			-- 分组后再过滤
[order by column]					-- 排序
[limit m, n];						-- 限制条数 m表示索引，从0开始，n表示记录条数
```

select 语句固定顺序：

```mysql
select -> from -> where -> group by -> having -> order by -> limit
```

## 1.3 多表查询

### 1.3.1 交叉连接，自然连接，内连接

实际开发中往往需要针对两张甚至更多张数据表进行操作，而这多张表之间需要使用主键和外键关联在一起，然后使用连接查询来查询多张表中满足要求的数据记录。

一条 SQL 语句查询多个表，得到一个结果，包含多个表的数据。效率高。在SQL99中，连接查询需要使用join关键字实现。 提供了多种连接查询的类型： cross  natural  using  on 		

交叉连接（CROSS JOIN）是对两个或者多个表进行笛卡儿积操作，所谓笛卡儿积就是关系代数里的一个概念，表示两个表中的每一行数据任意组合的结果。比如：有两个表，左表有 m 条数据记录，x 个字段，右表有n条数据记录，y 个字段，则执行交叉连接后将返回 m*n 条数据记录，x+y个字段。

**查询两个表所有记录：**

```mysql
-- 查询员工的编号，姓名，部门编号：
select * from emp;
select empno,ename,deptno from emp;
-- 查询员工的编号，姓名，部门编号,部门名称：
select * from emp; -- 14条记录
select * from dept; -- 4条记录 
```

**交叉连接**：

```mysql
-- 多表查询 ：
-- 交叉连接：cross join
select * 
from emp
cross join dept; -- 14*4 = 56条 笛卡尔乘积 ： 没有实际意义，有理论意义

-- or
select * 
from emp
join dept; -- cross 可以省略不写，mysql中可以，oracle中不可以
```

**自然连接：**

```mysql
-- 自然连接：natural join 
-- 优点：自动匹配所有的同名列 ,同名列只展示一次 ，简单
select * 
from emp
natural join dept;

select empno,ename,sal,dname,loc 
from emp
natural join dept;

-- 缺点： 查询字段的时候，没有指定字段所属的数据库表，效率低
-- 解决： 指定表名：
select emp.empno,emp.ename,emp.sal,dept.dname,dept.loc,dept.deptno
from emp
natural join dept;

-- 缺点：表名太长
-- 解决：表起别名
select e.empno,e.ename,e.sal,d.dname,d.loc,d.deptno
from emp e
natural join dept d;
```

**using子句：**

```mysql
-- 自然连接 natural join 缺点：自动匹配表中所有的同名列，但是有时候我们希望只匹配部分同名列：
-- 解决： 内连接 - using子句：
select * 
from emp e
inner join dept d -- inner可以不写
using(deptno) -- 这里不能写natural join了 ,这里是内连接
```

**on子句：**

```mysql
-- using缺点：关联的字段，必须是同名的 
-- 解决： 内连接 - on子句：
select * 
from emp e
inner join dept d
on (e.deptno = d.deptno);

-- 多表连接查询的类型： 
-- 1. 交叉连接  cross join  2. 自然连接  natural join  
-- 3. 内连接 - using子句    4. 内连接 - on子句
-- 综合看：内连接 - on子句
select * 
from emp e
inner join dept d
on (e.deptno = d.deptno)
where sal > 3500;
-- 条件：
-- 1.筛选条件  where  having
-- 2.连接条件 on,using,natural 
-- SQL99语法 ：筛选条件和连接条件是分开的
```

### 1.3.2 外连接查询

```mysql
-- inner join - on子句： 显示的是所有匹配的信息
select * 
from emp e
inner join dept d
on e.deptno = d.deptno;
select * from emp;
select * from dept;
```

**左外连接：**

```mysql
-- 问题：
-- 1.40号部分没有员工，没有显示在查询结果中
-- 2.员工scott没有部门，没有显示在查询结果中
-- 外连接：除了显示匹配的数据之外，还可以显示不匹配的数据
-- 左外连接： left outer join   -- 左面的那个表的信息，即使不匹配也可以查看出效果
select * 
from emp e
left outer join dept d
on e.deptno = d.deptno;
```

**右外连接：**

```mysql
-- 右外连接： right outer join   -- 右面的那个表的信息，即使不匹配也可以查看出效果
select * 
from emp e
right outer join dept d
on e.deptno = d.deptno;
```

**全外连接：**

```mysql
-- 全外连接  full outer join
-- 这个语法在mysql中不支持，在oracle中支持 -- 展示左，右表全部不匹配的数据
-- scott ，40号部门都可以看到
select * 
from emp e
full outer join dept d
on e.deptno = d.deptno;
```

**取并集：**

```mysql
-- 解决mysql中不支持全外连接的问题：
select * 
from emp e
left outer join dept d
on e.deptno = d.deptno
union -- 并集 去重 效率低
select * 
from emp e
right outer join dept d
on e.deptno = d.deptno;

select * 
from emp e
left outer join dept d
on e.deptno = d.deptno
union all-- 并集 不去重 效率高
select * 
from emp e
right outer join dept d
on e.deptno = d.deptno;
-- mysql中对集合操作支持比较弱，只支持并集操作，交集，差集不支持（oracle中支持）
-- outer可以省略不写
```

### 1.3.3 三表连接查询

```mysql
-- 查询员工的编号、姓名、薪水、部门编号、部门名称、薪水等级
select * from emp;
select * from dept;
select * from salgrade;

select e.ename,e.sal,e.empno,e.deptno,d.dname,s.* 
from emp e
right outer join dept d
on e.deptno = d.deptno
inner join salgrade s 
on e.sal between s.losal and s.hisal
```

### 1.3.4 自连接查询

```mysql
-- 查询员工的编号、姓名、上级编号,上级的姓名
select * from emp;
select e1.empno 员工编号,e1.ename 员工姓名,e1.mgr 领导编号,e2.ename 员工领导姓名
from emp e1
inner join emp e2
on e1.mgr = e2.empno;
-- 左外连接：
select e1.empno 员工编号,e1.ename 员工姓名,e1.mgr 领导编号,e2.ename 员工领导姓名
from emp e1
left outer join emp e2
on e1.mgr = e2.empno;
```

### 1.3.5 多表查询92语法

```mysql
-- 查询员工的编号，员工姓名，薪水，员工部门编号，部门名称：
select e.empno,e.ename,e.sal,e.deptno,d.dname
from emp e,dept d
-- 相当于99语法中的cross join ,出现笛卡尔积，没有意义
select e.empno,e.ename,e.sal,e.deptno,d.dname
from emp e,dept d
where e.deptno = d.deptno;
-- 相当于99语法中的natural join 
-- 查询员工的编号，员工姓名，薪水，员工部门编号，部门名称，查询出工资大于2000的员工
select e.empno,e.ename,e.sal,e.deptno,d.dname
from emp e,dept d
where e.deptno = d.deptno and e.sal > 2000;
-- 查询员工的名字，岗位，上级编号，上级名称（自连接）：
select e1.ename,e1.job,e1.mgr ,e2.ename 
from emp e1,emp e2
where e1.mgr = e2.empno;
-- 查询员工的编号、姓名、薪水、部门编号、部门名称、薪水等级
select e.empno,e.ename,e.sal,e.deptno,d.dname,s.grade 
from emp e,dept d,salgrade s
where e.deptno = d.deptno and e.sal >= s.losal and e.sal <= s.hisal;
-- 总结：
-- 1.92语法麻烦 
-- 2.92语法中 表的连接条件 和  筛选条件  是放在一起的没有分开
-- 3.99语法中提供了更多的查询连接类型：cross,natural,inner,outer 
```

## 1.4 子查询

### 1.4.1 不相关子查询

**什么是子查询？**

一条SQL语句含有多个select，把一个查询的结果作为另一个查询的条件。

```mysql
-- 引入子查询：
-- 查询所有比“CLARK”工资高的员工的信息  
-- 步骤1：“CLARK”工资
select sal from emp where ename = 'CLARK'  -- 2450
-- 步骤2：查询所有工资比2450高的员工的信息  
select * from emp where sal > 2450;
-- 两次命令解决问题 --》效率低 ，第二个命令依托于第一个命令，第一个命令的结果给第二个命令使用，但是
-- 因为第一个命令的结果可能不确定要改，所以第二个命令也会导致修改
-- 将步骤1和步骤2合并 --》子查询：
select * from emp where sal > (select sal from emp where ename = 'CLARK');
-- 一个命令解决问题 --》效率高
```

执行顺序：先执行子查询，再执行外查询。

不相关子查询：子查询可以独立运行，称为不相关子查询。

不相关子查询分类：根据子查询的结果行数，可以分为**单行子查询**和**多行子查询**。

**单行子查询：**

```mysql
-- 单行子查询：
-- 查询工资高于平均工资的雇员名字和工资。
select ename,sal
from emp
where sal > (select avg(sal) from emp);
-- 查询和CLARK同一部门且比他工资低的雇员名字和工资。
select ename,sal
from emp
where deptno = (select deptno from emp where ename = 'CLARK') 
      and 
      sal < (select sal from emp where ename = 'CLARK')
-- 查询职务和SCOTT相同，比SCOTT雇佣时间早的雇员信息  
select * 
from emp
where job = (select job from emp where ename = 'SCOTT') 
      and 
      hiredate < (select hiredate from emp where ename = 'SCOTT')
```

**多行子查询：**

【1】查询【部门20中职务同部门10的雇员一样的】雇员信息。

```mysql
-- 查询雇员信息
select * from emp;
-- 查询部门20中的雇员信息
select * from emp where deptno = 20;-- CLERK,MANAGER,ANALYST
-- 部门10的雇员的职务：
select job from emp where deptno = 10; -- MANAGER,PRESIDENT,CLERK
-- 查询部门20中职务同部门10的雇员一样的雇员信息。
select * from emp 
where deptno = 20 
and job in (select job from emp where deptno = 10)
-- > Subquery returns more than 1 row
select * from emp 
where deptno = 20 
and job = any(select job from emp where deptno = 10)
```

【2】查询工资比所有的“SALESMAN”都高的雇员的编号、名字和工资。

```mysql
-- 查询雇员的编号、名字和工资
select empno,ename,sal from emp
-- “SALESMAN”的工资：
select sal from emp where job = 'SALESMAN'
-- 查询工资比所有的“SALESMAN”都高的雇员的编号、名字和工资。
-- 多行子查询：
select empno,ename,sal 
from emp 
where sal > all(select sal from emp where job = 'SALESMAN');
-- 单行子查询：
select empno,ename,sal 
from emp 
where sal > (select max(sal) from emp where job = 'SALESMAN');
```

【3】查询工资低于任意一个“CLERK”的工资的雇员信息。  

```mysql
-- 查询雇员信息
select * from emp;
-- 查询工资低于任意一个“CLERK”的工资的雇员信息
select * 
from emp
where sal < any(select sal from emp where job = 'CLERK')
and job != 'CLERK'
-- 单行子查询：
select * 
from emp
where sal < (select max(sal) from emp where job = 'CLERK')
and job != 'CLERK'
```

### 1.4.2 相关子查询

【1】不相关的子查询引入： 	

不相关的子查询：子查询可以独立运行，先运行子查询，再运行外查询。 	

相关子查询：子查询不可以独立运行，并且先运行外查询，再运行子查询。

【2】不相关的子查询优缺点： 	

好处：简单  功能强大（一些使用不相关子查询不能实现或者实现繁琐的子查询，可以使用相关子查询实现） 

缺点：稍难理解 	

【3】sql展示：

```mysql
-- 【1】查询最高工资的员工  （不相关子查询）
select * from emp where sal = (select max(sal) from emp)
-- 【2】查询本部门最高工资的员工   （相关子查询）
-- 方法1：通过不相关子查询实现：
select * from emp where deptno = 10 
and sal = (select max(sal) from emp where deptno = 10)
union
select * from emp where deptno = 20 
and sal = (select max(sal) from emp where deptno = 20)
union
select * from emp where deptno = 30 
and sal = (select max(sal) from emp where deptno = 30)
-- 缺点：语句比较多，具体到底有多少个部分未知
-- 方法2： 相关子查询
select * from emp e where 
sal = (select max(sal) from emp where deptno = e.deptno) order by deptno
-- 【3】查询工资高于其所在岗位的平均工资的那些员工  （相关子查询）
-- 不相关子查询：
select * from emp where job = 'CLERK' 
and sal >= (select avg(sal) from emp where job = 'CLERK')
union ......
-- 相关子查询：
select * from emp e where sal >= (select avg(sal) from emp e2 where e2.job = e.job)
```

# 二. 数据库对象

## 2.1 事务

### 2.1.1 事务及其特征

事务（Transaction）是用来维护数据库完整性的，它能够保证一系列的 MySQL 操作要么全部执行，要么全不执行。 		

例子1： 		

举一个例子来进行说明，例如转账操作：A 账户要转账给 B 账户，那么A账户上减少的钱数和 B 账户上增加的钱数必须一致，也就是说 A 账户的转出操作和 B 账户的转入操作要么全部执行，要么全不执行；如果其中一个操作出现异常而没有执行的话，就会导致账户 A 和账户 B 的转入转出金额不一致的情况，为而事实上这种情况是不允许发生的，所以为了防止这种情况的发生，需要使用事务处理。 		

例子2： 		

在淘宝购物下订单的时候，商家库存要减少，订单增加记录，付款我的账号少 100 元...操作要么全部执行，要么全不执行。

【1】事务的概念 		

事务（Transaction）指的是一个操作序列，该操作序列中的多个操作要么都做，要么都不做，是一个不可分割的工作单位，是数据库环境中的逻辑工作单位，由DBMS（数据库管理系统）中的事务管理子系统负责事务的处理。 		

目前常用的存储引擎有InnoDB（MySQL5.5以后默认的存储引擎）和MyISAM（MySQL5.5之前默认的存储引擎），其中InnoDB支持事务处理机制，而MyISAM不支持。 		

【2】事务的特性 		

事务处理可以确保除非事务性序列内的所有操作都成功完成，否则不会永久更新面向数据的资源。通过将一组相关操作组合为一个要么全部成功要么全部失败的序列，可以简化错误恢复并使应用程序更加可靠。 		

但并不是所有的操作序列都可以称为事务，这是因为一个操作序列要成为事务，**必须满足事务的 ACID 四大特性：**

- **原子性（Atomicity）**
- **一致性（Consistency）**
- **隔离性（Isolation）**
- **持久性（Durability）**

这四个特性简称为 ACID 特性。

1) 原子性  		

原子是自然界最小的颗粒，具有不可再分的特性。事务中的所有操作可以看做一个原子，事务是应用中不可再分的最小的逻辑执行体。 使用事务对数据进行修改的操作序列，要么全部执行，要么全不执行。通常，某个事务中的操作都具有共同的目标，并且是相互依赖的。如果数据库系统只执行这些操作中的一部分，则可能会破坏事务的总体目标，而原子性消除了系统只处理部分操作的可能性。  		

2) 一致性  		

一致性是指事务执行的结果必须使数据库从一个一致性状态，变到另一个一致性状态。当数据库中只包含事务成功提交的结果时，数据库处于一致性状态。一致性是通过原子性来保证的。  		

例如：在转账时，只有保证转出和转入的金额一致才能构成事务。也就是说事务发生前和发生后，数据的总额依然匹配。  		

3) 隔离性  		

隔离性是指各个事务的执行互不干扰，任意一个事务的内部操作对其他并发的事务，都是隔离的。也就是说：并发执行的事务之间既不能看到对方的中间状态，也不能相互影响。  		

例如：在转账时，只有当 A 账户中的转出和 B 账户中转入操作都执行成功后才能看到 A 账户中的金额减少以及 B 账户中的金额增多。并且其他的事务对于转账操作的事务是不能产生任何影响的。 

4. 持久性  		

持久性指事务一旦提交，对数据所做的任何改变，都要记录到永久存储器中，通常是保存进物理数据库，即使数据库出现故障，提交的数据也应该能够恢复。但如果是由于外部原因导致的数据库故障，如硬盘被损坏，那么之前提交的数据则有可能会丢失。

【3】sql展示：使用事务保证转账安全

```mysql
-- 创建账户表：
create table account(
    id int primary key auto_increment,
    uname varchar(10) not null,
    balance double
);
-- 查看账户表：
select * from account;
-- 在表中插入数据：
insert into account values (null,'丽丽',2000),(null,'小刚',2000);
-- 丽丽给小刚 转200元：
update account set balance = balance - 200 where id = 1;
update account set balance = balance + 200 where id = 2;
-- 默认一个DML语句是一个事务，所以上面的操作执行了2个事务。
update account set balance = balance - 200 where id = 1;
update account set balance = balance2 + 200 where id = 2;
-- 必须让上面的两个操作控制在一个事务中：
-- 手动开启事务：
start transaction;
update account set balance = balance - 200 where id = 1;
update account set balance = balance + 200 where id = 2;
-- 手动回滚：刚才执行的操作全部取消：
rollback;
-- 手动提交：
commit;
-- 在回滚和提交之前，数据库中的数据都是操作的缓存中的数据，而不是数据库的真实数据
```

### 2.1.2 事务并发问题

**脏读：**

当一个事务正在访问数据并且对数据进行了修改，而这种修改还没有提交到数据库中，这时另外一个事务也访问了这个数据，然后使用了这个数据。因为这个数据是还没有提交的数据，那么另外一个事务读到的这个数据是“脏数据”，依据“脏数据”所做的操作可能是不正确的。

**不可重复读：**

（Unrepeatableread）：指在一个事务内多次读同一数据。在这个事务还没有结束时，另一个事务也访问该数据。那么，在第一个事务中的两次读数据之间，由于第二个事务的修改导致第一个事务两次读取的数据可能不太一样。这就发生了在一个事务内两次读到的数据是不一样的情况，因此称为不可重复读。

**幻读：**

（Phantom read）：幻读与不可重复读类似。它发生在一个事务（T1）读取了几行数据，接着另一个并发事务（T2）插入了一些数据时。在随后的查询中，第一个事务（T1）就会发现多了一些原本不存在的记录，就好像发生了幻觉一样，所以称为幻读。

不可重复读的重点是修改，幻读的重点在于新增或者删除。

### 2.1.3 事务隔离级别

事务的隔离级别用于决定如何控制并发用户读写数据的操作。数据库是允许多用户并发访问的，如果多个用户同时开启事务并对同一数据进行读写操作的话，有可能会出现脏读、不可重复读和幻读问题，所以 MySQL 中提供了四种隔离级别来解决上述问题。  		

  事务的隔离级别从低到高依次为 READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ 以及SERIALIZABLE，隔离级别越低，越能支持高并发的数据库操作。

| 隔离级别         | 脏读 | 不可重复读 | 幻读 |
| ---------------- | ---- | ---------- | ---- |
| READ UNCOMMITTED | √    | √          | √    |
| READ COMMITTED   | ×    | √          | √    |
| REPEATABLE READ  | ×    | ×          | √    |
| SERIALIZABLE     | ×    | ×          | ×    |

√  代表会出现问题  ， × 代表不会出现问题 = 解决问题

```mysql
-- 查看默认的事务隔离级别  MySQL默认的是repeatable read  
select @@transaction_isolation;  
-- 设置事务的隔离级别   （设置当前会话的隔离级别）
set session transaction isolation level read uncommitted;  
set session transaction isolation level read committed;  
set session transaction isolation level repeatable read;  
set session transaction isolation level serializable;  
start transaction ;
select * from account where id = 1;
```

## 2.2 视图

### 2.2.1 视图的概念

视图（view）是一个从单张或多张基础数据表或其他视图中构建出来的虚拟表。同基础表一样，视图中也包含了一系列带有名称的列和行数据，但是数据库中只是存放视图的定义，也就是动态检索数据的查询语句，而并不存放视图中的数据，这些数据依旧存放于构建视图的基础表中，只有当用户使用视图时才去数据库请求相对应的数据，即视图中的数据是在引用视图时动态生成的。因此视图中的数据依赖于构建视图的基础表，如果基本表中的数据发生了变化，视图中相应的数据也会跟着改变。 		

视图本质上就是：一个查询语句，是一个虚拟的表，不存在的表，你查看视图，其实就是查看视图对应的 sql语句

### 2.2.2 视图的好处

简化用户操作：视图可以使用户将注意力集中在所关心地数据上，而不需要关心数据表的结构、与其他表的关联条件以及查询条件等。 		

对机密数据提供安全保护：有了视图，就可以在设计数据库应用系统时，对不同的用户定义不同的视图，避免机密数据（如，敏感字段“salary”）出现在不应该看到这些数据的用户视图上。这样视图就自动提供了对机密数据的安全保护功能。

### 2.2.3 实操

**创建/替换单表视图：**

```mysql
create or replace view myview01
as
select empno,ename,job,deptno 
from emp
where deptno = 20
with check option;

-- 查看视图：
select * from myview01;

-- 在视图中插入数据：
insert into myview01 (empno,ename,job,deptno) values (9999,'lili','CLERK',20);
insert into myview01 (empno,ename,job,deptno) values (8888,'nana','CLERK',30);
insert into myview01 (empno,ename,job,deptno) values (7777,'feifei','CLERK',30);

create or replace view myview02
as 
select e.empno,e.ename,e.sal,d.deptno,d.dname
from emp e
join dept d
on e.deptno = d.deptno
where sal > 2000 ;
select * from myview02;
```

**创建统计视图：**

```mysql
create or replace view myview03
as
select e.deptno,d.dname,avg(sal),min(sal),count(1)
from emp e
join dept d
using(deptno)
group by e.deptno ;
select * from myview03;
```

**创建基于视图的视图：**

```mysql
create or replace view myview04
as
select * from myview03 where deptno = 20;
select * from myview04;
```

## 2.3 存储过程

### 2.3.1 什么是存储过程

通过前面章节的学习，我们已经知道 SQL 是一种非常便利的语言。从数据库抽取数据，或者对特定的数据集中更新时，都能通过简洁直观的代码实现。 		

但是这个所谓的“简洁”也是有限制，SQL 基本是一个命令实现一个处理，是所谓的非程序语言。 		

在不能编写流程的情况下，所有的处理只能通过一个个命令来实现。当然，通过使用连接及子查询，即使使用SQL 的单一命令也能实现一些高级的处理，但是，其局限性是显而易见的。例如，在 SQL 中就很难实现针对不同条件进行不同的处理以及循环等功能。 		

这个时候就出现了存储过程这个概念，简单地说，存储过程就是数据库中保存(Stored)的一系列 SQL 命令（Procedure）的集合。也可以将其看作相互之间有关系的 SQL 命令组织在一起形成的一个小程序。

### 2.3.2 存储过程有点

1) 提高执行性能。存储过程执行效率之所高，在于普通的 SQL 语句，每次都会对语法分析，编译，执行，而存储过程只是在第一次执行语法分析，编译，执行，以后都是对结果进行调用。 		

2) 可减轻网络负担。使用存储过程，复杂的数据库操作也可以在数据库服务器中完成。只需要从客户端(或应用程序)传递给数据库必要的参数就行，比起需要多次传递 SQL 命令本身，这大大减轻了网络负担。 	

3) 可将数据库的处理黑匣子化。应用程序中完全不用考虑存储过程的内部详细处理，只需要知道调用哪个存储过程就可以了。

### 2.3.3 实操

```mysql
-- 定义一个没有返回值 存储过程
-- 实现：模糊查询操作：
select * from emp where ename like '%A%';
create procedure mypro01(name varchar(10))
begin
	if name is null or name = "" then
    	select * from emp;
    else
    	select * from emp where ename like concat('%',name,'%');
    end if;	
end;

-- 删除存储过程：
drop procedure mypro01;
-- 调用存储过程：
call mypro01(null);
call mypro01('R');

-- 定义一个  有返回值的存储过程：
-- 实现：模糊查询操作：
-- in 参数前面的in可以省略不写
-- found_rows()mysql中定义的一个函数，作用返回查询结果的条数
create procedure mypro02(in name varchar(10), out num int(3))
begin
	if name is null or name = "" then
    	select * from emp;
    else
    	select * from emp where ename like concat('%',name,'%');
    end if;	
        select found_rows() into num;
end;
-- -- 调用存储过程：
call mypro02(null, @num);
select @num;
call mypro02('R', @aaa);
select @aaa;
```



# 三. 常用函数

## 3.1 字符串函数

| 名称                               | 描述                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| concat(...str)  `...` 表示可变参数 | 拼接字符串，并返回拼接后的字符串                             |
| insert(str, index, n, newStr)      | 将字符串str从第index位置开始的n个字符替换成字符串newStr      |
| length(str)                        | 获取字符串str的长度                                          |
| lower(str)                         | 将字符串str中的每个字符转换为小写                            |
| upper(str)                         | 将字符串str中的每个字符转换为大写                            |
| left(str, n)                       | 获取字符串str最左边的n个字符                                 |
| right(str, n)                      | 获取字符串str最右边的n个字符                                 |
| lpad(str, n,  pad)                 | 使用字符串pad在str的最左边进行填充，直到长度为n个字符为止    |
| rpad(str, n, pad)                  | 使用字符串pad在str的最右边进行填充，直到长度为n个字符为止    |
| ltrim(str)                         | 去除字符串str左侧的空格                                      |
| rtrim(str)                         | 去除字符串str右侧的空格                                      |
| trim(str)                          | 去除字符串str左右两侧的空格                                  |
| replace(str ,oldstr, newstr)       | 用字符串newstr替换字符串str中所有的子字符串oldstr            |
| reverse(str)                       | 将字符串str中的字符逆序                                      |
| strcmp(str)                        | 比较字符串str1和str2的大小                                   |
| substring(str)                     | 获取从字符串str的index位置开始的n个字符，字符编号从1开始计数 |

## 3.2 数值函数

| 函数              | 描述                           |
| ----------------- | ------------------------------ |
| abs(num)          | 返回绝对值                     |
| ceil(num)         | 返回向上取整                   |
| floor(num)        | 返回向下取整                   |
| mod(num1, num2)   | 返回$num1 \% num2$             |
| pi()              | 返回π                          |
| pow/power(num, n) | 返回$num^n$                    |
| rand(num)         | 返回$[0, 1)$随机数             |
| round(num, n)     | 返回对num四舍五入，保留n位小数 |
| truncate(num, n)  | 返回num被舍去至小数点后n位的值 |

## 3.3 日期与时间函数

| 函数                                       | 描述                                           |
| ------------------------------------------ | ---------------------------------------------- |
| curdate()                                  | 返回当前日期                                   |
| curtime()                                  | 返回当前时间                                   |
| now()                                      | 返回当前日期和时间                             |
| sysdate()                                  | 返回该函数执行时的日期时间                     |
| dayofyear(date)                            | 返回date为一年中的第几天                       |
| week/weekofyear(date)                      | 返回日期date为一年中的第几周                   |
| date_format(date, format)                  | 返回按字符串format格式化后的日期date           |
| date_add/adddate(date, interval expr unit) | 返回date加上一个时间间隔后的新时间值           |
| date_sub/subdate(date, interval expr unit) | 返回date减去一个时间间隔后的新时间值           |
| datediff(date1, date2)                     | 返回起始日期date1与结束日期date2之间的间隔天数 |

```mysql
-- 单行函数包含：
-- 1.字符串函数
select ename,length(ename),substring(ename,2,3) from emp;
-- substring字符串截取，2:从字符下标为2开始，3：截取长度3    （下标从1开始）
-- 2.数值函数
select abs(-5),ceil(5.3),floor(5.9),round(3.14) from dual; -- dual实际就是一个伪表 
-- 如果没有where条件的话，from dual可以省略不写
select abs(-5) 绝对值,ceil(5.3) 向上取整,floor(5.9) 向下取整,round(3.14) 四舍五入;  
select ceil(sal) from emp;
select 10/3,10%3,mod(10,3) ;
-- 3.日期与时间函数 
select * from emp;
select curdate(),curtime() ; -- curdate()年月日 curtime()时分秒
select now(),sysdate(),sleep(3),now(),sysdate() from dual; -- now(),sysdate() 年月日时分秒
insert into emp values (9999,'lili','SALASMAN',7698,now(),1000,null,30);
-- now()可以表示年月日时分秒，但是插入数据的时候还是要参照表的结构的
desc emp;
```

## 3.4 流程函数

| 函数                                                         | 描述                                                       |
| ------------------------------------------------------------ | ---------------------------------------------------------- |
| if(condition, t, f)                                          | 如果条件condition为真，则返回t，否则返回f                  |
| ifnull(value1, value2)                                       | 如果value1不为null，则返回value1，否则返回value2           |
| nullif(value1, value2)                                       | 如果value1等于value2，则返回null，否则返回value1           |
| case value when [value1] then result1...else result...end    | 如果value等于value1，则返回result1，···，否则返回result    |
| case when [condition] then result1 [when [condition 2] then result2...] [else result] end | 如果条件condition1为真，则返回result1，···，否则返回result |

## 3.5 JSON函数

| 函数            | 描述                           |
| --------------- | ------------------------------ |
| json_append()   | 在JSON文档中追加数据           |
| json_insert()   | 在JSON文档中插入数据           |
| json_replace()  | 替换JSON文档中的数据           |
| json_remove()   | 从JSON文档的指定位置移除数据   |
| json_contains() | 判断JSON文档中是否包含某个数据 |
| json_search()   | 查找JSON文档中给定字符串的路径 |

## 3.6 其他函数

| 函数                | 描述                                   |
| ------------------- | -------------------------------------- |
| database()          | 返回当前数据库名                       |
| version()           | 返回当前MySQL的版本号                  |
| user()              | 返回当前登录的用户名                   |
| inet_ntoa           | 返回数字代表的IP地址                   |
| inet_aton(ip)       | 返回IP地址的数字表示                   |
| password(str)       | 实现对字符串str的加密操作              |
| format(num, n)      | 实现对数字num的格式化操作，保留n位小数 |
| convert(data, type) | 实现将数据data转换成type类型的操作     |

```mysql
-- 4.流程函数
-- if相关
-- if-else 双分支结构
select empno,ename,sal,if(sal>=2500,'高薪','底薪') as '薪资等级' from emp; 
-- 如果comm是null，那么取值为0 -- 单分支
select empno,ename,sal,comm,sal+ifnull(comm,0) from emp; 
--  如果value1等于value2，则返回null，否则返回value1  
select nullif(1,1),nullif(1,2) from dual; 
-- case相关：
-- case等值判断
select empno,ename,job,
case job 
 when 'CLERK' then '店员'
 when 'SALESMAN'  then '销售'
 when 'MANAGER' then '经理'
 else '其他'
end '岗位',
sal from emp;
-- case区间判断:
select empno,ename,sal,
case 
 when sal<=1000 then 'A'
 when sal<=2000 then 'B'
 when sal<=3000 then 'C'
 else 'D'
end '工资等级',
deptno from emp;
from emp;
-- 5.JSON函数  
-- 6.其他函数
select database(),user(),version() from dual;
```

## 3.2 多行函数

| 函数         | 描述                   |
| ------------ | ---------------------- |
| count(field) | 统计表中记录的数目     |
| sum(field)   | 计算指定字段值的总和   |
| avg(field)   | 计算指定字段值的平均值 |
| max(field)   | 统计指定字段值的最大值 |
| min(field)   | 统计指定字段值的最小值 |

```mysql
-- 多行函数：
select max(sal),min(sal),count(sal),sum(sal),sum(sal)/count(sal),avg(sal) from emp;
select * from emp;
-- 多行函数自动忽略null值
select max(comm),min(comm),count(comm),sum(comm),sum(comm)/count(comm),avg(comm) from emp;
-- max(),min(),count()针对所有类型   sum(),avg() 只针对数值型类型有效
select max(ename),min(ename),count(ename),sum(ename),avg(ename) from emp;
-- count --计数   
-- 统计表的记录数：方式1：
select * from emp;
select count(ename) from emp;
select count(*) from emp;
-- 统计表的记录数：方式2
select 1 from dual;
select 1 from emp;
select count(1) from emp;
```