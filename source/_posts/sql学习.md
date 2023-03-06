---
title: SQL语法总结
date: 2021-09-14 21:35:11
tags: SQL
index_img: /img/page/sql.png
---

# 一. 打开
打开SQL sever management studio -> connect -> 新建一个query
# 二. SQL语句
> SQL语句对大小写不敏感，分为DDL（数据定义）语句，DML（数据操作）语句。
> #### DDL语句: 创建、删除、增加索引、键、约束
> * CREATE DATABASE - 创建新数据库
> * ALTER DATABASE - 修改数据库
> * CREATE TABLE - 创建新表
> * ALTER TABLE - 变更（改变）数据库表
> * DROP TABLE - 删除表
> * CREATE INDEX - 创建索引（搜索键）
> * DROP INDEX - 删除索引
> #### DML语句：查询和更新  
> * SELECT - 从数据库表中获取数据
> * UPDATE - 更新数据库表中的数据
> * DELETE - 从数据库表中删除数据
> * INSERT INTO - 向数据库表中插入数据

 ### 1. 切换数据库 
 `use database_name`
 ### 2. 创建数据库 
 `create database database_name`
 ### 3. 创建表
 > create table 表名(
 列名1 数据类型 是否为空（可省略）,
 列名2 数据类型 是否为空（可省略）,
 ...
 )
 ```sql
 //建表students, 主键为student__ID
 create table students(
 s_ID int not null primary key,
 s_name varchar(20) not null,
 s_sex char(2) not null,
 s_phoneNumber varchar(11)
)
 ```
 ### 4. 删除表
 `drop table table_name`
 ### 5. 插入数据
 > * 可不写全所有列  
 insert into table_name(列名1，列名2，..)values(值1，值2，..)  
 > * 省略列名，必须写全所有列  
insert into table_name values(值1，值2，..)
 > * 一次性插入多组值  
 insert into table_name values
 (值1，值2，..)，
 (值1，值2，..)，
 (值1，值2，..)  
 **注**：字符串用单引号括起来
 ```sql
 //方法1
insert into students(s_ID, s_name, s_sex, s_phoneNumber)values(01, 'tom', '男','121212121')
insert into students(s_ID, s_name, s_sex)values(06, 'luna', '女')
//方法2
insert into students values(02, 'jack', '男','221212121')
//方法3
insert into students values
(03, 'Amy', '女','201212121'),
(04, 'bree', '女','921212121'),
(05, 'mike', '男','821212121')
 ```
 ### 6. 删除数据
 > delete from 表名 where 条件，==没有条件的话，整张表都会被删除==
 ```sql
delete from students where s_ID = 01
delete from students
```
### 7. 修改表结构
> **增加列：** alter table 表名 add  字段名 数据类型  
> **删除列：** alter table 表名 drop column 字段名  
> **修改字段类型：** alter table 表名 alter column 字段名 新类型
```sql
//增加列
alter table students add s_age int
//删除列
alter table students drop column s_age
//修改字段类型
alter table students alter column s_age int
```
### 8. 更改数据
> update 表名 set 列名 = 列值, 列名 = 列值,.. where 条件, ==不写条件则修改全表==  
> `update students set s_age = 20`
### 9. 查询
- #### 查全表
> select * from 表名
- #### 查部分列
> select 列名1, 列名2 from 表名
- #### 列取别名
> select 列名1 as 新列名1, 列名2 新列名2 from 表名
**注**：as可以省略
- #### 列名加运算符
``` sql
//查询学生姓名以及学生两年前的年龄
select s_name as 姓名, s_age-2 两年前的年龄 from students
//查询学生姓名、两年前的年龄
//isnull(expr1,expr2)用于避免null值参与计算  MySQL用ifnull()
//如果expr1是null,函数的结果就是expr2;否则就是expr1
select s_name, isnull(s_age, 20)-2 from students
//可用+做字符串或列与列的拼接,列得是字符串型 
select '姓名:'+s_name+s_sex from students
```
- #### distinct关键字
> 去掉重复的行，所查字段都一样才会被去掉  
`select distinct class_ID, s_name from students`
- #### 条件查询
> 1. 比较运算：>、< 、>=、<=、=、= 、!=或<>  
> **注**： null值不能作比较  
> NULL值与任何其它值的比较（即使是NULL）永远不会为“真”
> 2. between ... and ... 数值范围条件  
> **注**：包括边界
> 3. in关键字，集合列表范围查询  
> **注**：not in集合里不能包含null值，因为要判断和集合里的所有元素都不等才会返回true，包含null值无法比较，任何与之比较的元素都会返回false
``` sql
 //查询tom的全部信息
 select * from students where s_name = 'tom'
 //查询不属于1班的学生
 select * from students where class_ID != 1
 //查询年龄在21-29之间的学生
 select * from students where s_age between 21 and 29
 //查Jack、linda、tom的信息
 select * from students where s_name in ('jack','linda','tom')
```
> 4. 逻辑运算：and、or、not  
> **注**：and的优先级高于or，也可以使用小括号强制优先级
``` sql
//查询属于1班且学号小于5的学生
select * from students where class_ID = 1 and s_ID < 5
//查询除tom之外的学生信息
select * from students where not s_name = 'tom'
//查询姓名为tom，或学号为2的学生
select * from students where s_name = 'tom' or s_ID = 2
```
> 5. 判空： is null、is not null
``` sql
 //查询电话号码为空的学生
 select * from students where s_phoneNumber is null
 ```
 > 5. 模糊查询：like + %或_ 
 %表示0个或多个字符，_表示一个字符
 ``` sql
  //查询姓名以l开头的学生
 select * from students where s_name like 'l%'
 //查询名字里含m的学生
 select * from students where s_name like '%m%'
 //查询名字第二个字符为o的
 select * from students where s_name like '_o%'
 ```
 - #### order by 对结果集排序
 > 默认升序asc,降序为desc  
 **注**：升序null值会排在最前面  
 select 列1， 列2 from 表名 order by 列名1
 ``` sql
  //先按年龄升序，年龄一样则按学号升序
 select s_name, s_age, s_ID from students order by s_age, s_ID 

 //先按年龄降序，年龄一样按学号升序
  select s_name, s_age, s_ID from students order by s_age desc, s_ID
  ```
  - #### top关键字：限定查询记录条数
  **注**：mysql用limit(a, b),表示从a开始条记录，a从0开始计数。limit(n)表示返回前n条
  ``` sql
  //查询前三名学生
  select top 3 * from students
  //查第n-m条记录
  //select top m-n+1 * from 表名 where 列名 not in (select top n-1 列名 from 表名)
  //查询2-7名学生
  select top 6 * from students 
  where s_ID not in (select top 1 s_ID from students)
  ```
  - #### 聚合函数：max、min、count、sum、avg
  > **注**：count(),只要括号里的不为null，都加1
  ``` sql
   //查年龄总值、最大值、电话不为空记录数
  select sum(s_age) sum, max(s_age) max, avg(s_age) avg, count(s_phoneNumber) count from students
  ```
  - #### 分组查询：group by
  > **注**：查询的列要在group by子句中；sql sever 不能取别名， MySQL可以
  ``` sql
  //按年龄分组
  select s_age from students group by s_age
  //计算每组记录数
  select s_age, count(*) from students group by s_age
  //按年龄和性别分组
  select s_age, s_sex from students group by s_age, s_sex
  ```
  **注**：在where语句中不能使用聚合函数，可以在having语句中使用聚合函数
 
 where是对每条表记录的条件限定，having是对结果集记录的条件限定
 ``` sql
 select s_sex, avg(s_age) from students 
 group bys_sex 
 having avg(s_age) > 25
 
 //先筛出年龄>25的，再按年龄分组，在筛出分组后年龄大于29的
  select s_age from students 
  where s_age > 25 
  group by s_age 
  having s_age > 29
 ```
 - #### 多表连接
 > 1. 等值连接
 ``` sql
  //查学生信息和专业
  select student.*, class.Speciality 
  from student, class 
  where student.clno = class.clno
  //取别名
  select * from student s, class c where s.clno = c.clno
  ```
  > 2. 内连接  
  select 列1，列2 from 表1 
  > inner join 表2
  > on 等值条件
  ``` sql
  select s.*, c.Speciality from student s 
  inner join class c on s.clno = c.clno 
  where sage > 20
  ```
  **注**：内连接的结果和等值连接的结果是相同的，只是写法不同
  只会显示符合连接条件的数据，不会显示所有的数据
  > 3. 外连接：
  > 分为左外连接、右外连接，左外连接会列出左表全部记录，右表没有符合的记录则用null值填充；右外连接会列出右表全部记录，左表没有符合的记录则用null值填充
  ``` sql
  select * from student s right outer join class c on c.clno = s.clno
  ```
  4. 全连接：表1 full join 表2 on 连接条件
   **注**：mysql没有full join，可用左连接union右连接代替
  - #### 子查询
  
  > - 相关子查询：子查询中需要用到父查询中的值
  ``` sql
  //查询某同学选修课程成绩高于他选修的所有课程平均成绩的课程号
  select cno from sc x where grade > 
  (select avg(grad) from sc y where x.sno = y.sno)
  ```
  工作原理：扫描父查询表（如 SC 表）中的每一条记录，然后将当前这条记录中的，在子查询中会用到的值代入到子查询中去，然后执行子查询并得到结果（可以看成是返回值），然后再将这个结果代入到父查询的条件中，判断父查询的条件表达式的值是否为 True，若为 True，则将当前 SC 表中的这条记录放到结果集中去。若为 False 则不放。
  > - 非相关子查询：脱离父查询单独执行
  >- 子查询的结果只有一行就是**单行子查询**；反之就是**多行子查询**。单行子查询只能进行比较。多行子查询可用in、any、all、exists、not exists  
> **exist原理**：[https://zhuanlan.zhihu.com/p/20005249](https://zhuanlan.zhihu.com/p/20005249)

- #### 取交集：union、union all 
  >union会排除重复行，并排序，union all不会
  >union all执行效率更高
  ``` sql  
  //找出id为奇数且name不以m开头的员工bonus为salary，否则为0，并排序
  select employee_id, salary bonus from Employees  
  where employee_id % 2 = 1 and name not like 'm%'  
  union all  
  select employee_id, salary*0 bonus from Employees  
  where employee_id % 2 = 0 or name like 'm%'
  order by employee_id
  ```  
# 三、例句
1. case用法
``` sql
select id,
    case when p_id is null then 'Root'
        when id in (select p_id from tree) then 'Inner'
        else 'Leaf'
    end as Type
from tree
```
2. **mysql** datadiff(date1, date2)函数
> 函数返回date1 -date2的计算结果;如果参数传递的是日期时间值，DATEDIFF函数仅将日期部分用于计算，并==忽略时间部分==
``` sql
SELECT activity_date day, COUNT(DISTINCT  user_id) active_users
FROM Activity
WHERE DATEDIFF("2019-07-27", activity_date) < 30 AND DATEDIFF("2019-07-27", activity_date) >= 0
GROUP BY activity_date;
```
> TIMESTAMPDIFF(DAY,start,end):返回两个日期指定单位的时间差(指定单位可以是年，季度，月，星期，天数，小时，分钟，秒等等)
3. if(条件, 为true时=, 为false时=)
``` sql
//性别转换
update Salary set sex = if(sex = 'm', 'f', 'm')
```
4. **注**：子查询要命名，不然会报错。  
  **注**：不能在同一个 sql 语句中，先 select 同一个表的某些值，然后再 update 这个表。
``` sql
DELETE from Person Where Id not in 
(select Id from 
( Select MIN(Id) as Id From Person Group by Email ) t)
```
5. concat(a, b)拼接a,b ;left(name,count)返回name前count字符。
``` sql
select user_id, 
concat(upper(left(name, 1)), lower(substr(name, 2))) name
from Users order by user_id
```
6. groupconcat()：将分组后每组的某一字段拼接起来。默认用,分割
``` sql
select sell_date,count(distinct product) num_sold,
group_concat(distinct product separator ',') products
from activities
group by sell_date
order by sell_date
```
7. with tableA as（select语句）：相当于建立了一个临时表tableA
```sql
with tmp as
(select employee_id from Employees
union all
select employee_id from Salaries)
select employee_id from tmp group by 1 having count(1) = 1 order by 1 
```
8. 
``` sql
//加引号表示将固定值store1放到列store
select product_id,"store1" store, store1 price from Products where store1 is not null
``` 

# 四、
1. e-r图与关系模式转换
   - 1对1关系：任一方的主键加入到另一方中。
   - 1对n关系：1方的主键加入到n方，并把联系上的属性也加入到n方。
   - m对n关系：联系自成一张表，并把双方的主键加进来当联合主键。
2. 数据库设计规范——三大范式
  - 第一范式：保持每列的原子性，即每个字段都是不可拆分的原子值。
  - 第二范式：确保每列都和主键相关，而不能和主键的某一部分相关（针对联合主键）。为避免数据冗余。  
  可能存在的问题：数据冗余、删除异常、插入异常、更新异常  
  例如：订单信息表中主键为订单编号和商品编号，属性包括，商品订购数量，商品价格，商品型号等。因为存在不同订单订购同一商品，因此同一个商品的信息可能被记录多次（数据冗余）；删除一条记录可能会删除一类商品的信息（删除异常）；插入一个商品信息必须要有订单号（插入异常）；更新一类商品信息要操作好几行（更新异常）。  
  它不满足第二范式，商品价格、商品型号只与商品有关，应该建一张商品表，订单信息表只保留商品编号、订购数量等。
  - 第三范式：必须满足第二范式。非主属性必须直接依赖于主键，不能传递依赖于非主属性。
3. 函数依赖
   - 部分函数依赖：（A,B）决定C,A也能决定C或B也能决定C，则C部分依赖于（A,B）
   - 完全函数依赖：（A,B）决定C,A不能决定C,B不能决定C，则C完全函数依赖于（A,B）
   - 传递依赖：A->B(B!->A),B->C，则C传递依赖于x  
  例：学号，宿舍，住宿费  
4. - mysql的执行过程
   [https://www.jianshu.com/p/338092a0a8c6](https://www.jianshu.com/p/338092a0a8c6)
   - 索引[https://bbs.huaweicloud.com/blogs/333163](https://bbs.huaweicloud.com/blogs/333163)
  
  

 