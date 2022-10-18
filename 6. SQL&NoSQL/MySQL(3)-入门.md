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

MySQL中提供了大量函数来简化用户对数据库的操作，比如字符串的处理、日期的运算、数值的运算等等。使用函数可以大大提高SELECT语句操作数据库的能力，同时也给数据的转换和处理提供了方便。 （在sql中使用函数） 		

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





### 1.2.5 having







### 1.2.6 总结







## 1.3 多表查询







## 1.4 子查询



























# 二. 数据库对象









# 三. 常用函数

## 3.1 字符串函数

| 名称                               | 描述                                                      |
| ---------------------------------- | --------------------------------------------------------- |
| concat(...str)  `...` 表示可变参数 | 拼接字符串，并返回拼接后的字符串                          |
| insert(str, index, n, newStr)      | 将字符串str从第index位置开始的n个字符替换成字符串newStr   |
| length(str)                        | 获取字符串str的长度                                       |
| lower(str)                         | 将字符串str中的每个字符转换为小写                         |
| upper(str)                         | 将字符串str中的每个字符转换为大写                         |
| left(str, n)                       | 获取字符串str最左边的n个字符                              |
| right(str, n)                      | 获取字符串str最右边的n个字符                              |
| lpad(str, n,  pad)                 | 使用字符串pad在str的最左边进行填充，直到长度为n个字符为止 |
| rpad(str, n, pad)                  | 使用字符串pad在str的最右边进行填充，直到长度为n个字符为止 |
| ltrim(str)                         | 去除字符串str左侧的空格                                   |
| rtrim(str)                         | 去除字符串str右侧的空格                                   |
| trim(str)                          | 去除字符串str左右两侧的空格                               |
| replace(str ,oldstr, newstr)       | 用字符串newstr替换字符串str中所有的子字符串oldstr         |
| reverse(str)                       | 将字符串str中的字符逆序                                   |
| strcmp(str)                        | 比较字符串str1和str2的大小                                |
| substring(str)                     | 获取从字符串str的index位置开始的n个字符                   |

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









## 3.4 流程函数











## 3.5 JSON函数









## 3.6 其他函数











## 3.2 多行函数