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

select ename,sal*12 as yearsal from aaa;
select ename,sal*12 as '年薪' from aaa;
select ename,sal*12 年薪, from aaa;

#### 删库

drop databaes aaa;

