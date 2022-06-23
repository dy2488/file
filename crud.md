### sql语句


* DQL(数据查询语言) ---> 查询语句,凡是select语句都是DQL. 
* DML(数据操作语言) ---> insert,delete,update,对表当中的数据进行增删改
* DDL(数据定义语言) ---> create,drop,alter,对表结构的增删改
* TCL(事务控制语言) ---> commit提交事物,rollback回滚事务
* DCL(数据控制语言) ---> grant授权,revoke撤销权限等

#### 导入数据库
source D:\xxxx\ddd.sql

#### 创建数据库

create database aaa;

#### desc用法

1. 查看sql脚本的执行情况，与explain命令效果一致  
desc select * from table_name;
2. 查看表结构  
desc table_name;
3. 对表某列数据进行排序  
select * from table_name order by col_name desc;

#### 查询当前使用的数据库

select database();
select version();

#### 查看创建表的语句

show create table aaa;

#### 终止一条正在编写的语句

\c

#### 使用数据库

use aaa;

#### 查

##### 给查询结果的列重命名

* 执行顺序

1. from 
2. where
3. group by
4. having
5. select
6. order by


```sql

select ename,sal*12 as yearsal from aaa;
select ename,sal*12 as '年薪' from aaa;
select ename,sal*12 年薪, from aaa;

```

##### 条件查询

```sql

select ename from aaa where sal=5000;

select sal from aaa where ename='bbb';

select ename,sal from aaa where sal>3000;

select ename,sal from aaa where sal != 3000;

select ename,sal from aaa where sal <> 3000;//!=和<>是一个意思

select ename,sal from aaa where sal >=1100 and sal <= 3000;

select ename,sal from aaa where sal between 1100 and 3000; between...and...是闭区间

/* 在数据库当中NULL不是一个值，代表什么也没有，为空。 空不是一个值u，不能用等号衡量。必须使用 is null或者 is not null */

select ename, sal, comm from aaa where comm is null; /*不能使用comm=null */

select ename, sal, comm from aaa where comm is not null;

select ename,sal,comm from aaa where comm is null or comm =0;

select ename,sal,deptno from aaa where sal >1000 and (deptno =20 or deptno =30); /* 小括号很关键  不知道谁优先级高的时候就加小括号来限定 */

/* in等同于or: */
/* not in:不在这几个值当中 */
select ename,job from aaa where job ='SALESMAN' or job ='MANAGER';
select ename,job from aaa where job in('SALESMAN','MANAGER');

/* %代表任意多个字符, _代表任意一个字符 */

select ename from where ename like '%O%';

select ename from where ename like '_A%';

select ename from where ename like '%\_%'; /* 寻找名字带有下划线的 */

select ename from where ename like '%T'; /* 最后一个字母带T的 */

select ename sal from aaa order by sal; /* 默认升序排列   asc升序, desc降序*/

select ename sal from aaa order by sal asc; /* 升序 */

select ename sal from aaa order by sal desc;/* 降序 */

select ename,sal from aaa order by sal desc, enmae asc; /* 按照工资的降序排列，当工资相同的时候再按照名字的升序排列 越考前的字段越能起到主导作用，只有当前面的字段无法完成排序的时候，才会启用后面的字段*/

select ename,sal from aaa order by 2/*按照第二列排序*/;

```
#### 分组函数

> 分组函数自动忽略NULL  
> 只要有NULL参与的运算结果一定是NULL

* count 计数
* sum 求和
* avg 平均值
* max 最大值
* min 最小值

```sql

select sum(sal) from aaa;

select count(enmae) from aaa; /* 用户的个数 */

/* ifnull()空处理函数? ifnull(可能为NULL的数据,被当作什么处理) */

select ename,ifnull(comm,0) as comm from aaa;

/* 找出比平均工资高的 */
select 

```

#### group by 和 having

group by: 按照某个字段或者某些字段进行分组。
having: having是对分组之后的数据进行再次过滤
分组函数一般都会和group by联合使用

```sql
select max(sal) from aaa group by job;

select ename,sal from aaa where sal > (select avg(sal) from emp);/* 找出高于平均工资的员工 */

select max(sal),job from aaa group by job; /* 找出每个工作岗位的最大薪资 */

/* 当一条语句中有group by的话, select后面只能跟分组函数和参与分组的字段 */

/* 找出不同部门不同工作岗位的最高薪资 */
select detno,job max(sal) from aaa group by deptna,job;

/* 找出每个部门的最高薪资,要求显示薪资大于2900的数据 */
select max(sal),deptno from aaa group by deptno having max(sal)>2900  /*这种效率低 */
select max(sal),deptno from aaa where sal >2900 group by deptno;/* 这种效率高  建议能使用where就尽量使用where*/
/* 找出每个部门的平均薪资, 要求显示薪资大于2000的数据 */
select deptno, avg(sal) from aaa group by deptno having avg(sal)>2000;

```

#### 查询结果集的去重

```sql

select distinct job from aaa; /* distinct关键字是去除重复 distinct只能出现在所有字段的最前面 */
select distinct deptno,job from aaa; /* deptno和job都去重了 */

/* 统计岗位的数量 */
select count(distinct job) from aaa;

```

#### 表的连接方式

**笛卡尔积现象**: 当两张表进行连接查询的时候，没有任何条件限制，最终的查询结果数条是两张表记录条数的乘机

**避免笛卡尔积现象**: 加条件过滤。避免了笛卡尔积现象.不会减少匹配次数只不过是显示的是有效记录


##### 内连接

**等值连接**

/* 找出每一个员工的部门名称, 要求显示员工和部门名 */
```sql

select e.ename,d.dname from emp e, dept d where e.deptno=d.deptno;

```
**sql99语法**: 
```sql

select e.ename,d.dname from emp e join dept d on e.deptno= d.deptno;

```


**表的别名**

```sql

select e.ename,d.dname from emp e, dept d;
/* 好处  第一  执行效率高.  第二   可读性好.*/

```

**非等值连接**

```sql

select e.ename,e.sal,s.grade from emp e join salgrade s on e.sal between s.losal and s.hisal;

```
**自连接**
```sql

/* 找出每个员工的上级领导 要求显示员工和对应的领导名*/ 
select a.ename as '员工名',b.ename as '领导名' from emp a join emp b on a.mgr =b.empno;

```
##### 外连接

假设A和B表进行连接,使用外连接的话，AB两张表中有一张表是主表,一张表是副表,主要是查询主表中的数据,顺便查副表,当副表中的数据没有和主表中的数据匹配上,副表自动模拟出NULL与之匹配.

**左外连接(左连接)**
**右外连接(右连接)**
```sql

/*找出每个员工的上级领导(所有员工必须都查询出来) */
select a.ename '员工', b.ename '领导' from emp a left join emp b on a.mgr=b.empno;

/* 找出哪个部门没有员工 */
select d.* from emp e right join dept d on e.deptno =d.deptno where e.emptno is null;

/* 找出每个员工的部门名称以及工资等级 */

A join B join C on .... /* A先和B连接然后A再和C连接 */

select e.ename,d.dname,s.grade from emp e join dept d on e.deptno=d.deptno join salgrade s on e.sal between s.losal and hisal;

```
#### from后面嵌套子查询

```sql

/* 找出每个部门平均薪水的薪资等级 */
select deptno,avg(sal) as avgsal from emp group by deptno;

select t.*,s.grade from (select deptno,avg(sal) as avgsal from emp group by deptno) t join salgrade s on t.avgsal between s.losal and s.hisal;

/* 每个部门平均的薪资等级 */
select e.deptno,avg(s.grade) from emp e join salgrade s on e.sal between s.losal and s. hisal group by e.deptno;

```

#### select后面嵌套子查询

```sql

/* 找出每个员工所在的部门名称, 要求显示员工和部门名 */
select e.ename,(select d.dname from dept d where e.deptno=d.deptno) as dname from emp e;

```

#### union可以将查询结果集相加)

```sql

/* 找出工作岗位是salesman和manager的员工? */
select ename,job from emp where job='salesman' or 'manager'
select ename,job from emp where job in('manager','salesman')

select ename,job from emp where job='salesman'
union
select ename,job from emp where job='manager';

/* 可以两张不相干的表拼接在一起 */

```

#### limit

limit取结果集中的部分数据,这是它的作用
limit是sql语句最后执行的一个环节

语法机制 ---> limit startIndex(表示起始位置),length(表示有几个)

```sql
/* 取出工资前5名的员工(降序前5个) */
select ename,sal from emp order by sal desc limit 0,5;

select ename,sal from emp order by sal desc limit 5;/* 跟上面的sql语句结果一样 */

/* 找出工资排名在第4到第9名的员工 */
select ename,sal from emp order by sal desc limit 3,6;

/*每页显示的数据 */

/* 第pageNo页: (pageNo -1)* pageSize,pageSize */

```

#### 删库

```sql
drop databaes aaa;

```
