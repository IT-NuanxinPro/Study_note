# MYSQL总结

## 1. DDL

> **create创建，drop 删除，alter 修改** 

* **查看数据库**

   ```sql
   show databases;
   ```

* **创建数据库**

   ```sql
   create database demo character set utf8 ;
   ```

* **删除数据库**

   ```sql
   drop database demo;
   ```

* **选择数据库**

   ```sql
   use demo;
   ```

* **创建表结构**

   ```sql
   create table demo(
   id int,
   name varchar(50)
   );
   ```

* **查看所有表**

   ```sql
   show tables;
   ```

* **查看表结构 (Field 字段,Type 数据类型)**

   ```sql
   desc demo;
   ```

* **删除表**

   ```sql
   drop table demo;
   ```

* **查看创建表结构的信息**

   ```sql
   show create table demo;
   ```

* **修改字段属性**

   ```sql
   #在admin表中 修改 name字段的数据类型 并且修改在密码字段后面
   alter table admin modify 
   name varchar(50) not null after password;
   ```

* **修改字段名**

   ```sql
   #在admin表中 将password 字段名改成 pass 字段名 
   alter table admin change password pass char(50) after name;
   ```

* **增加字段**

   ```sql
   #在表中 添加 n1 varchar(30) n2 varchar(50) 放到 id 前面
   alter table admin 
   add n1 varchar(30) not null first,
   add n2 varchar(50) not null first;
   ```

* **删除字段**

   ```sql
   #删除admin表中的 n1 n2 字段
   alter table admin
   drop n1,
   drop n2;
   ```

* **表的重命名**

   ```sql
   alter table admin rename to Admin;
   ```

   

## 2. DML

> **增insert   删delete   改update    查 select**

* **插入数据**

   > **==说明==: 如果没写字段那么所有字段值按顺序必须都写.**

   ```sql
   insert demo(id,name) values
   (1,"暖心")，
   (2,"五花肉")
   ```

* **删除数据**

   ```sql
   delete from demo where id = 2;
   #清空表数据
   delete from user;
   
   #d elete 清空数据后不能重置  auto_increment.
   delete from admin;
   
   # truncate 清空数据后能重置  auto_increment.
   truncate admin;
   ```

* **查询数据**

   ```sql
   select * from demo;
   
   select id,username,salary from admin; # 常用
   
   select id,username,salary,salary*12 as 年薪 from admin; # 起别名
   
   # 给表.字段名称 (说明字段来自于哪个表) SELECT 表.字段名称|expr FROM 表名
   select a.id,a.username,a.salary from admin as a;
   
   # 库名.表(表来自于哪个数据库)SELECT 字段名称|expr FROM 库名.表名;
   select * from demo.admin;
   
   SELECT 字段名称 FROM 表名
   #      [WHERE 条件] 过滤|
    比较运算符  = 等于 > >= < <=  != 不等于 <> 不等于 <=>(判断null值)
   #      [GROUP BY 字段名称] 分组|
   #      [HAVING 条件] 二次过滤|
   #      [ORDER BY 字段名称] 排序|
   #      [LIMIT OFFSET,LENGTH] 截取
   ```

* **更新数据**

   ```sql
   #更新编号是3 的记录中 name的值
   update demo set name = "暖" where id =1;
   #更新id 字段的值都增加1
   update demo set id = id + 1;
   ```

   

## 3. DQL

* ```sql
   1. 创建员工信息表  employee
   #       id    编号 自动增长
   # (1) job_id    int 工种   (电焊1, 汽修2  锅炉3)
   # (2) name varchar(30)  员工名称
   #   (3)  department_id  int  部门编号(101,102)
   # (4)salary  decimal(10,2)  薪水
   #  (5)  bonus   decimal(10,2)  奖金
   
   create table employee(
   id int unsigned primary key auto_increment comment '编号',
   name varchar(30) not null comment '名称',
   job_id int unsigned not null comment '工种编号',
   department_id int unsigned not null comment '部门编号',
   salary decimal(10,2) not null default 0 comment '月薪',
   bonus decimal(10,2) not null default 0 comment '奖金',
   foreign key (job_id) references job(id),
   foreign key (department_id) references department(id)
   );
   
   insert employee (name,job_id,department_id,salary,bonus) values 
   ('tom',1,101,8888.88,1000),
   ('alice',2,102,7788.88,1000),
   ('jerry',3,101,6666.88,1000),
   ('tina',1,101,9999.88,1000);
   ```

* ```sql
   (1)  使用alter, 添加  se(性别 tinyint 值 0,1 ) age(年龄  int )  字段并且放在 name 后面
   alter table employee
   add se tinyint(1) unsigned not null default 0 after name,
   add age tinyint unsigned not null default 18 after name;
   
   (2)  使用alter,修改 se 字段名为   sex 
   alter table employee
   change se sex tinyint(1) unsigned not null default 0;
   
   desc employee;
   
   (3)查找 employee  中   年龄大于等于 20并且 薪水大于5000的记录
   select * from employee where age>=20  and salary>5000;
   
   (4)查找 employee  中 编号在2-9  也可以  sex 是 1的记录
   select * from employee where id between 2 and 9 or sex = 1;
   
   select * from employee where (id>=2 and id<=9) or sex= 1;
   (5) 查找 employee  中 编号是偶数并且年龄大于22的记录
   
   select * from employee where id%2=0 and age>22;
   ```

* **模糊查询**

   ```sql
   #g. [NOT] LIKE string [ESCAPE string]
   # 说明:模糊查询 关键字:%:代表0个或1个或多个任意字符 _: 代表1个任意字符
   
   # 1. 查询 name 字段 首字母是 't'的记录
   select * from employee where name like 't%';
   # 2. 查询 name 字段 包含字母'e'的记录 (搜索用)
   select * from employee where name like '%e%';
   # 3. 查询 name字段值是5个字母的记录 
   select * from employee where name like '_____';
   # 4. 查询 name 字段值  第二个字母'_'的记录.
   select * from employee where name like '_#_%' escape '#';
   
   # 5.  查询 name 字段值  第三个字母是'r'的记录.
   select * from employee where name like  '__r%'; 
   ```

* **分组查询**

   ```js
   # 汇总筛查, 分组后只能查看分组的那个字段,和聚合函数的汇总	
   # 1.查询 男女的编号,人数,最大年龄和最小年龄
   select sex,count(*) as 人数,max(age),min(age)
   from employee
   group by sex;
   
   # 2.查询 工种的编号,人数,最高工资,最低工资,平均工资,工资和.
   select job_id,count(*)as 人数,max(salary),min(salary),avg(salary),sum(salary)
   from employee
   group by job_id;
   
   # 3.查询 部门编号,人数,最高工资
   select department_id,count(*) as 人数,max(salary)
   from employee
   group by department_id;
   # 4. 查询公司的总人数和最高工资(一个结果)
   select count(*) as 人数,max(salary) from employee;
   
   # [HAVING 条件]:对分组后的信息二次过滤
   #说明:[HAVING 条件]必须和 GROUP BY一起使用
   
   # where 是分组前过滤,having 分组后过滤
   # 5. 查询(最高工资大于8000 的)部门的编号,人数,最高工资
   select department_id,count(*) as 人数,max(salary) as m
   from employee
   group by department_id
   having m>8000;
   
   # 6. 查询 (3-7编号的)(最大工资大于8000 的)部门的编号,人数,最高工资
   select department_id,count(*) as 人数,max(salary) as m
   from employee
   where id>=3 and id<=7
   group by department_id
   having m>8000;
   
   # 7 查询 (每组人数大于2人的)部门的编号,人数,最高工资
   select department_id,count(*) as c,max(salary)
   from employee
   group by department_id
   having c>2;
   
   # 8 查询 (有奖金员工和)(每组人数大于2人的)部门的编号,人数,最高工资
   select department_id,count(*) as c,max(salary)
   from employee
   where bonus != 0
   group by department_id
   having c>2;
   ```

* **排序**

   ```sql
   # [ORDER BY 字段名称]:排序 ORDER BY 字段名称  [ASC升序默认值|DESC 降序]
   # 查询 employee表并且 对salary 字段降序排序
   select * from employee order by salary desc;
   
   # 查询 employee表并且 对salary 字段降序,id 字段降序排序(多个字段以第一个字段排序,如果值相同再以第二个字段排序)
   
   select * from employee order by salary desc,id desc;
   
   
   SELECT * from employee where salary in(SELECT min(salary) FROM employee
   GROUP BY department_id);
   
   //每个部门平均工资最低的员工信息
   select * from employee where department_id = (
           select department_id from employee
           group by department_id 
           order by avg(salary) desc limit 0,1
   )
   ```

* **==作业==（参考employee表）**

   ```sql
   # (1) 查询员工号,姓名,工资以及工资提高百分20%后的结果.
   select id,name,salary,salary*1.2 from employee;
   
   # (2) 查询员工最高工资和最低工资的差距 (提示写运算得一个结果,别名diff)
   select max(salary) -min(salary) as diff from employee;
   
   # (3)选择工资不在5000-10000的员工的姓名和工资
   select name,salary from employee where salary not between 5000 and 10000;
   
   # (4)查询各工种 job_id 员工的工资最大值,平均值  总和  并按 job_id 降序排序
   select job_id,max(salary),avg(salary),sum(salary)
   from employee
   group by job_id
   order by job_id desc;
   
   # (5) 查询所有部门的编号,员工数量和工资的平均 并按平均工资降序
   select department_id,count(*) as 人数,avg(salary) as a
   from employee
   group by department_id
   order by a desc;
   
   # (6)查询员工的名称和部门编号和年薪,按年薪降序并按姓名升序
   select name,department_id ,(salary+bonus)*12 as yearsalary from employee
   order by yearsalary desc,name asc;
   
   # (7)查询部门编号为101 的员工个数 (不是每组编号,条件查询101)
   select count(*) from employee where department_id= 101;
   ```

   

* **截取**

   ```sql
   # [LIMIT OFFSET,LENGTH] 截取
   #   说明:a. OFFSET:偏移量,起始编号,编号从0开始
           #  b. LENGTH:显示的条数(记录数)
           #  c.作用:实现WEB页面的分页效果,计算每页中的起始编号
           #  OFFSET:(当前页-1)*显示的条数
   		
   
   # 1. 查询 employee 记录的前三条
   select * from employee where id <=3; # 不准确
   
   select * from employee limit 0,3; # 正确
   
   # 2. 查询 employee 记录的后三条(获得前5条件最新新闻)
   
   select  * from employee order by id desc limit 0,3; 
   # 3.  分页原理: 每页3条
   #   第一页:
   select * from employee order by id desc limit 0,3;
   #   第二页 : 计算offset 索引= (当前页-1)*显示的条数  (2-1)*3
   select * from employee order by id desc limit 3,3;
   #   第三页 : 计算offset 索引= (当前页-1)*显示的条数  (3-1)*3
   select * from employee order by id desc limit 6,3;
   # let offset = (page-1)*length;
   # select * from employee order by id desc limit offset,length;
   
   # 4. 查询 部门中平均工资的最大值.
   select avg(salary) as avg
   from employee
   group by department_id
   order by avg desc
   limit 0,1;
   
   # 5. 查询有奖金员工的最高基本工资的前三名员工信息.
   select * from employee
   where bonus != 0 
   order by salary desc 
   limit 0,3;
   ```



## 4 多表联合查询

> **内连接(inner join),外连接(left join左外连接,right join 右外连接)**

* **创建department和job两张表**

   ```sql
   #  部门表 department  (id 编号(与employee 中department_id 类型一致),dname 部门名)
   create table department(
   id int unsigned primary key auto_increment,
   dname varchar(50) not null
   );
   
   insert department(id,dname) value 
   (101,'技术'),
   (102,'会计'),
   (103,'销售'),
   (104,'人力');
   #  工种表 job  (id 编号(与employee 中job_id 类型一致),jname 工种名)
   
   create table job(
   id int unsigned primary key auto_increment,
   jname varchar(50) not null
   );	
   	
   insert job(jname) value 
   ('电工'),
   ('瓦工'),
   ('汽修'),
   ('泥工');
   
   ```

* **==内连接==**

   ```sql
   # SELECT 表.字段名称,表.字段名称...
   #       FROM 表1
   #              inner join 表2 ON 条件   
   #              inner join 表3 ON 条件...
                  
   #  (1)内连接: [INNER] JOIN  查找符合条件的信息
   #条件分类:等值 (常用) , 非等值,自连接(菜单级联)
   
   
   # 内连接:  找符合条件的信息
   # 6. 查询表中员工编号,员工名称,部门名称的记录
   select e.id,e.name,d.dname
   from employee as e
   inner join department as d  on e.department_id = d.id;
   
   # 7. 查询表中员工编号,员工名称,部门名称,工种名称的记录
   
   select e.id,e.name,d.dname,j.jname
   from employee as e
   inner join department as d  on e.department_id = d.id
   inner join job as j on e.job_id = j.id;
   
   # sql92 (老写法)
   select e.id,e.name,d.dname,j.jname 
   from employee as e,department as d,job as j 
   where e.department_id = d.id and e.job_id = j.id;
   
   # 8. 查询 (薪水大于7000 的)员工编号,名称,薪水及部门名称的记录.
   select e.id,e.name,e.salary,d.dname
   from employee as e
   inner join department as d on e.department_id = d.id
   where e.salary > 7000; 
   ```

   

* **==作业==（参考employee，department，job三张表）**

   ```sql
   #(1)每个工种(有奖金的)员工的(最高工资>5000的)工种编号和最高工资,按最高工资升序
   select job_id,max(salary) as max
   from employee
   where bonus !=0
   group by job_id
   having max>5000
   order by max desc;
   # 扩展 工种名称
   select j.jname,max(e.salary) as max
   from employee as e 
   inner join job as j on e.job_id = j.id
   where bonus !=0
   group by e.job_id
   having max >5000 
   order by max desc;
   
   #(2)查询(名字中包含a的)员工名和工种名(添加条件)  inner join
   select e.name,j.jname
   from employee as e 
   inner join job as j on e.job_id = j.id
   where e.name like '%a%';
   
   # (3)查询哪个(部门员工大于2)的部门名称和员工个数
   select d.dname,count(*) as c
   from employee as e 
   inner join department as d on e.department_id = d.id
   group by e.department_id
   having c >2;
   
   # (4) 查询(部门名为'技术','销售')的员工信息
   select  e.*,d.dname 
   from employee as e 
   inner join department as d on e.department_id = d.id
   where d.dname in ('技术','销售');
   ```

* **==外连接==**

   > **\# 外连接( left join 左外连接, right join 右外连接)**
   >
   > 
   >
   > **\# 外连接的查询结果为主表中的所有记录如果从表中有和它匹配的,则显示匹配的值**
   >
   > **\# 如果从表中没有和它匹配的,则显示null**
   >
   > **\# 外连接查询结果=内连接结果+主表中有而从表没有的记录**

   ```sql
   1. left join 左外连接
   查询 员工所有信息及所有的部门名
   
   select d.dname,e.* from department as d
   left join employee as e on d.id = e.department_id;
   
   2. right join 右外连接
   查询 员工所有信息及所有的部门名
   
   select d.dname,e.* from  employee as e
   right join department as d on d.id = e.department_id;
   ```

* **==自连接==（重要）**

   > **\#  menu 表 :  子菜单的pid 等于 上级菜单的 id** 
   >
   > **\#  id(当前菜单id )  name (菜单名称)  pid (父菜单的id)**

   ```sql
         1               家用电器        0 
   	  2               手机           0
   	  3               电脑           0
   	  4               电视           1
   	  5               空调           1 
   	  6               洗衣机         1 
   	  7               TCL           4
   	  8               长虹           4 
   	  9               格力           5
   	  10              美的           5
   ```

   ```sql
   create table menu(
   id int unsigned primary key auto_increment,
   name varchar(50) not null,
   pid int unsigned not null default 0
   );
   insert menu(id,name,pid) values 
   (1,'家用电器',0),
   (2,'手机',0),
   (3,'电脑',0),
   (4,'电视',1),
   (5,'空调',1),
   (6,'洗衣机',1),
   (7,'TCL',4),
   (8,'长虹',4),
   (9,'格力',5),
   (10,'美的',5);
   # 3. 查询子菜单名称及对应的父菜单的名称(子菜单的pid 等于 上级菜单的 id )
   select p.name as 上级菜单, s.name as 子菜单
   from menu as p 
   inner join menu as s on p.id = s.pid;
   ```

* **==子查询==**

> **==按子查询出现的位置==**:
>
>  **select后面**:仅仅支持标量子查询(生成新字段,不常用)
>
>  **from后面**:将子查询的结果生成一个新表,必须写别名.(也可以是 inner join后);支持表子查询
>
>  **where或having后面**:★
>
>   标量子查询(单行,一个结果) √
>
>   列子查询  (一列多行) √
>
>   行子查询  (一行多列)
>
>   **exists后面** :
>
>  表子查询 (相关子查询)
>
>  **==按结果集的行列数不同:==**
>
>  标量子查询(结果集只有一行一列,一个结果)
>
>  列子查询(结果集只有一列多行)
>
>  行子查询(结果集有一行多列)
>
>  表子查询(结果集一般为多行多列)

* **标量子查询(单行,一个结果)  ,比较运算符**

```sql
select * from employee  where salary > 7000;
select * from employee  where salary >(select avg(salary) from employee);

# 2. 查询(薪水最低的)员工信息
select * from employee where salary = (select min(salary) from employee);
```

* **列子查询  (一列多行)**

> **in 、any / some、all**
>
> 
>
> **子查询的执行优先于主查询执行,主查询的条件用到了子查询的结果**
>
> \> >= any/some(大于最小值)  任意
>
> \> >= all (大于最大值)     所有
>
> < <= any/some(小于最大值)
>
> < <= all (小于最小值)说明: 
>
> = any/some  **等同**  in 

```sql
# 3. 查询 (每个部门的最低薪水的)员工信息

# 3.1  每个部门的最低薪水的(子查询)
select min(salary)
from employee 
group by department_id;

# 3.2  
select * from employee where salary in (select min(salary)
from employee group by department_id);

# 4. 查询 (每个工种的最高薪水的)员工信息

select * from employee where salary in (
select max(salary) from employee group by job_id
);

# 5. 查询 (每个工种的平均薪水最高的)员工信息 (salary = XXX平均工资最高)

# 5.1 每个工种的平均薪水最高的
select salary from employee group by job_id 
order by avg(salary) desc limit 0,1

select * from employee where job_id = (
select job_id from employee group by job_id 
order by avg(salary) desc limit 0,1
);

# 6. 查询(平均工资最低的)部门信息

# 6.1  每个部门平均工资最低的 (一个结果)
select department_id 
from employee group by department_id
order by avg(salary) asc limit 0,1;

select * from department where id = (select department_id 
from employee group by department_id
order by avg(salary) asc limit 0,1);
```

* **==作业==**

```sql
#1.查询(课程和小胖一样)的学生最低分
SELECT min(score) from score where cid in(
SELECT s.cid from score as s 
join student as st on s.cid = st.id
where st.uname = "小胖"
)

#2.查询没有学过"刘老师"课的同学的学号、姓名
(a) 查询学过刘老师课的同学的学号
    select s.uid from student as st
    join score as s on s.uid = st.id 
    join course as c on s.cid = c.id 
    join teacher as t on t.id = c.id  
    where t.tname = "刘老师";

# (b) 查询学号、姓名, 条件 没有学过"刘老师"不在(a)的结果
    select id,uname from student 
    where id not in(
    select s.uid from student as st
    join score as s on s.uid = st.id
    join course as c on s.cid = c.id
    join teacher as t on t.id = c.id
    where t.tname = "刘老师"
)

# 3查询有课程成绩小于60分的同学的学号、姓名
# (a) 课程成绩小于60分
    select uid from score where score < 60;

# (b)查询同学的学号、姓名
#where id
#方法一
# select id,uname from student where id in(
  select uid from score where score < 60
)
#方法二
select id,uname from student where id in(
select s.uid from student as st
join score as s on st.id = s.uid 
where s.score < 60
)

#4.查询工资最低的员工信息
select * from employee where salary = (
select min(salary) from employee
)

#5.查询平均工资最低的部门信息
select * from department where id = (
select department_id from employee 
group by department_id 
order by avg(salary) asc limit 0,1
)

#6.查询平均工资高于(公司平均工资)的部门有哪些
select department_id from employee 
group by department_id having avg(salary) > (
select avg(salary) from employee
)

# 返回其他工种比工种为2任一工资低的员工号,姓名,工种,薪水
# (a) 工种为2的员工薪水
select salary from employee where job_id = 2;

# (b) where 条件 薪水 任一工资低的 < any(小于最大值) 
#方法一
select id,name,job_id,salary from employee 
where salary < any(
select salary from employee where job_id = 2
)and job_id != 2;

#方法二
select id,name,job_id,salary from employee 
where salary < (
select max(salary) from employee where job_id = 2
)and job_id != 2;
```

* **行子查询**

```sql
# 查询员工编号最小并且薪水最高的员工信息
where id salary
#方法一(用)
select * from employee where id=(select min(id) from employee) and
salary =(select max(salary) from employee);

#查询每个部门的员工个数及部门所有的信息
select count(*) as 人数 from employee as e
join department as d on e.department_id = d.id;
group by department_id;

#方法二(了解)
select count(*) from employee where department_id = 101

select (select count(*) from employee where department_id = d.id) as 人数,d* from department as d;
```

* **作业**

> (1) 每个班第一名的同学的名称和分数
>
> 名称  班级  分数
>
> A    1   88
>
> B    1   90
>
> C    2   85
>
> D    2   95
>
> (2) 查询公司最高工资和最低工资的员工信息.

```sql
create table stu(
id int unsigned primary key auto_increment comment '学生名称',
name varchar(20) not null comment '姓名',
class int unsigned not null comment '班级',
Score int unsigned not null comment '分数'
);

insert stu(name,class,Score)values
('A',1,88),
('B',1,90),
('C',2,85),
('D',2,95)

#每个班第一名的同学的名称和分数
select name,class from stu where Score in
(select max(Score) from stu GROUP BY class);

#查询公司最高工资和最低工资的员工信息
select * from employee where salary 
=(select max(salary) from employee) or salary 
= (select min(salary) from employee);
```

* **表子查询**

```sql
# (查询1课程比2课程成绩高的)所有学生的学号,成绩
# 查询编号是1的课程
select uid,score from score where cid=1;
#查询编号是2的课程
select uid,score from score where cid=2;

select a.uid,a.score
from (select uid,score from score where cid=1) as a
join (select uid,score from score where cid=2) as b
on a.uid=b.uid
where a.score>b.score;
#查询平均工资最低的部门信息和该部门的平均工资
#方法一:
select avg(salary),d.*
from employee as e
join department as d on e.department_id= d.id
group by department_id order by avg(salary) asc limit 0,1;

#方法二:
select a.avg,d.*
from department as d
join (select avg(salary) as avg,department_id from employee 
group by department_id) as a
on d.id=a.department_id;
```





## 5.DQL

> 1. **grant(授权)**
> 2. **revoke(撤销授权)**

```sql
  (1)创建用户
      CREATE USER 'jerry'@'localhost' IDENTIFIED BY '123456';
  (2)赋予权限 select,insert,delete,create,alter,drop...
      GRANT ALL ON class2.* TO 'jerry'@'localhost'

  (3)查看用户权限
     SHOW GRANTS FOR jerry@localhost;
        
  (4) 在 root 用户下查看所有 有哪些用户
        
     SELECT User, Host FROM mysql.user;

     //更新用户密码(已知旧密码)
     ALTER USER 'jerry'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
     //更改密码
     ALTER USER 'jerry'@'localhost' IDENTIFIED BY '123456';
     //刷新权限
     FLUSH PRIVILEGES;

     //撤销授权
     REVOKE ALL PRIVILEGES ON *.* FROM 'jerry'@'localhost';
     //删除用户
     DROP USER 'jerry'@'localhost';
     //导出到D盘
     mysqldump -u root -p demo2 > D:\mysql_demo2.sql
     //导入到D盘
     mysql -u root -p mytest < D:\mysql_demo2.sql
     
   # revoke 权限 on 数据库名.表名 from 用户名@登陆方式;
     REVOKE  ALL privileges ON class1.* from 'jerry'@'localhost';
```

* **数据库的导入和导出**

   * **命令,不登录MySQL**

   ```sql
     mysqldump -uroot -p  class2 > d:/back.sql ;
        
    # 导入  <  ,创建一个新数据库
     mysql -uroot -p myt1 < d:/back.sql
   ```

   * **navicat实现**

   ```sql
    # 导出:
      打开数据库->右键转储SQL文件 ->结构和数据-> xx.sql
      
    # 导入
      打开新数据库->右键运行SQL文件 ->选择 xx.sql
   ```

   * **备份与恢复(运维: 配置二进制日志- 恢复具体时间点)**

   

## 6. 外键约束

>   **1.约束:实现对字段的非空,唯一,完整性的约束.**
>
> **2.约束分类**
>
>   **(1)按功能分**
>
> ​      **NOT NULL(非空)**
>
> ​      **DEFAULT (默认值)**
>
> ​      **PRIMARY KEY (主键)**
>
> ​      **UNIQUE KEY (唯一)**
>
> ​       **FOREIGN KEY (外键)**
>
>    **(2)按约束字段个数分**
>
> ​       **a.列约束:在字段后面实现约束 NOT NULL ,DEFAULT 必须用列约束实现**
>
> ​       **b.表约束:对两个字段或两个字段以上 :实现的约束PRIMARY KEY ,**
>
> ​       **UNIQUE KEY,FOREIGN KEY可以实现表约束**



* **外键**

   >    **1.实现对两个表的字段完整性和一致性约束**
   >
   > 2. **FOREIGN KEY (外键列名称) REFERENCES 参考表名称(字段名称)**
   >
   > ​     **说明:**
   >
   > ​      **a.MYSQL引擎为 InnoBD**
   >
   > ​       **alter table employ engine=InnoDB;**
   >
   > ​      **b.数据类型必须一致,如果是整型时**
   >
   > ​         **大小,UNSIGNED 必须一致,如果字符**
   >
   > ​         **型长度可以不同,但编码必须一致**
   >
   > ​      **c.外键列如果不是索引,MYSQL引擎 会自动将外键列定义为索引**
   >
   > ​      **d.外键列(子表)添加信息,参考表必须有相应信息,**
   >
   > ​       **参考表不能删除/更新在子表中已经使用的外键值.**

```text
注意先有参考表(父表),再有子表
```

```sql
#在创建 employee表添加外键
foreign key (job_id) references job(id),
foreign key (department_id) references department(id)
```

> **外键列(子表)添加信息,参考表必须有相应信息,**
>
> **参考表不能删除/更新在子表中已经使用的外键值.**

```sql
insert employee (name,job_id,department_id,salary,bonus) values 
('hello',1,108,9999.88,1000);

delete from department where id= 101;
update department set id = 108  where id = 101;
```



* **==作业==（参考学生表，课程表，成绩表）**

   > Score 表
   >
   > Id   uid  cid  score
   >
   > 1       1    1
   >
   > 2       1    2
   >
   > 3       1    3
   >
   > 4       2    1  
   >
   > 5       2    2
   >
   > 6       2    3

   * **创建三张表**

   ```sql
   create table student(
   id int unsigned primary key auto_increment comment '学生编号',
   uname varchar(30) not null comment '学生名称',
   age tinyint unsigned not null default 18 comment '年龄',
   sex tinyint(1) unsigned not null default 0 comment '性别',
   addr varchar(30) not null default '' comment '地址'
   );
   insert student (uname,addr) values 
   ('zhangs','天津'),
   ('lis','山东'),
   ('zhaowu','山西'),
   ('liuli','河北');
   
   
   create table course(
   id int unsigned primary key auto_increment comment '课程编号',
   cname varchar(30) not null comment '课程名称'
   );
   insert course (cname) values 
   ('数学'),
   ('语文'),
   ('英语'),
   ('经济学');
   
   create table score(
   id int unsigned primary key auto_increment comment '成绩单编号',
   score float(5,2) not null default -1 comment '成绩',
   uid int unsigned not null comment '学生编号',
   cid int unsigned not null comment '课程编号',
   foreign key(uid) references student(id),
   foreign key(cid) references course(id)
   );
   
   insert score (score,uid,cid) values 
   (88,1,1),
   (91,1,2),
   (85,1,3),
   (95,2,1),
   (79,2,2),
   (81,2,3),
   (90,3,1),
   (85,3,2),
   (77,3,3),
   (85,3,4);
   ```

   * **问题**

   ```sql
   (1)查询每个课程的学生人数
   select cid,count(*) as 学课程的人数
   from score
   group by cid;
   
   (2)查询(参加考试)的学生中,每个学生的平均分、最高分 (Score 表中 uid编号 分组)
   select uid,avg(score),max(score)
   from score 
   where score !=-1
   group by uid;
   扩展:查询(参加考试)的学生中,学生名称,平均分、总成绩最高的前三名
   select st.uname,avg(s.score),sum(s.score) as sum
   from score as s 
   inner join student as st on s.uid = st.id
   where s.score !=-1
   group by s.uid
   order by sum  desc
   limit 0,3;
   
   (3)查询大于60分的学生的姓名、课程名(Score ,student,course)
   select st.uname,c.cname,s.score
   from score as s
   inner join student as st on s.uid = st.id 
   inner join course as  c  on s.cid = c.id
   where s.score > 60;
   
   (4)查询学生名、课程名、分数 (student,course,Score )
   select st.uname,c.cname,s.score
   from score as s
   inner join student as st on s.uid = st.id 
   inner join course as  c  on s.cid = c.id;
   ```



## 7. 三范式

> **1.第一范式:要求有主键,并且要求每一个字段原子性不可再分 (id,name,age,sex info信息X)**
>
> **2.第二范式:要求所有非主键字段完全依赖主键,不能产生部分依赖 (拆分表)**
>
>  **(employee  : id , name, salary, job_id, jname(依赖job_id,放到job表中))**
>
> **3..第三范式:所有非主键字段和主键字段之间不能产生传递依赖**

* **表设计模式**

   > **自连接、 一对一、 一对多、 多对多**

* **一对一**

   > 1. **第一种方案:分两张表存储,共享主键(用)**
   > 2. **第二种方案:分两张表存储,外键唯一**

   

   ```sql
   eg: article (id编号,author作者,title标题,content内容,created创建日期)
        aritcle1(id编号,author作者,title标题,created创建日期)
   	 article2(id编号,content内容) 
   	 注意:  id编号值一致
   ```

* **一对多**

   > 1. **分两张表存储,在多的一方添加外键,**
   > 2. **这个外键字段引用一方中的主键字段**

   ```sql
   eg1: 参考表:job表(id 不重复值), employee 表(job_id 重复多值:一个工种可以有多个员工) 
   eg2: 参考表:department 表(id 不重复值), employee 表(department_id 重复多值:一个部门可以有多个员工)  
   eg3:  参考表:category 分类表(id 不重复值), goods 商品表,(cate_id重复多值: 一个分类可以有多个商品) 
   ```

* **多对多**

   > 1. **分三张表存储,在学生表中存储学生信息,在课程表中存储课程信息**
   > 2. **在学生成绩表中存储学生和课程的关系信息**

```sql
eg1:student 学生表(id不重复), course课程表(id不重复的), 

    第三个表: score(成绩表: uid(重复多值) , cid(重复多值))	
    一个学生可以学多门课程,一个课程可以被多个学生学习
	
eg2:商品收藏功能: 一个会员可以收藏多个商品,一个商品可以被多个会员收藏
    goods商品表(id不重复的), user会员表(id不重复的)
	第三个表完成 favi ( good_id(重复多值),user_id (重复多值))
```



* **==作业==（参考student,course,score,teacher）**

   > 一对多  Teacher (一个老师讲一门课,一门课可以有多个老师讲 )
   >
   > id  tname   cid
   >
   > 1        1
   >
   > 2        2
   >
   > 3        2
   >
   > 4        5
   >
   > 
   >
   > 多对多  score (一个老师讲多门门课,一门课可以有多个老师讲 )
   >
   > id  score  uid  cid  tid
   >
   > 1       1       1   1
   >
   > 2       1       2   1
   >
   > 3       1       3   3
   >
   > 4       2       1   2

   ```sql
   # 一对多: 一个老师讲一门课,一门课可以有多个老师讲
   create table teacher(
   id int unsigned primary key auto_increment comment '教师编号',
   tname varchar(30) not null comment '教师名称',
   cid  int unsigned not null comment '课程编号',
   foreign key(cid) references course(id)
   );
   insert teacher (tname,cid) values 
   ('李丽',1),
   ('王红',1),
   ('陈曦',2);
   ```

   ```sql
   # 多对多:一个老师讲多门门课,一门课可以有多个老师讲 
   create table teacher(
   id int unsigned primary key auto_increment comment '教师编号',
   tname varchar(30) not null comment '教师名称'
   );
   
   create table score(
   id int unsigned primary key auto_increment comment '成绩单编号',
   score float(5,2) not null default -1 comment '成绩',
   uid int unsigned not null comment '学生编号',
   cid int unsigned not null comment '课程编号',
   tid int unsigned not null comment '教师编号',
   foreign key(uid) references student(id),
   foreign key(cid) references course(id),
   foreign key (tid) references teacher2(id)
   );
   ```

   * **==问题==**

      ```sql
      # 1.查询"001"课程的所有学生的学号与分数
      
      # 2查询"001"课程的所有学生的名称与分数;
      
      # 3.查询平均成绩大于60分的同学的学号和平均成绩;
      
      #4.查询所有同学的学号、姓名(student)、选课数、总成绩(sC)
      
      #5.查询姓"李"的老师的个数;
      
      #6.查询(学过"张三"老师课的)同学的学号、姓名(难)
      
      #7.查询年级总分前三名的学生记录
      ```

## 8. 索引

* **索引分类**

   > 1. **普通索引 , 唯一索引,  主键索引, 外键索引(like 查询起不作用)**
   > 2. **全文索引fulltext (like 查询起作用)**

* **==普通索引==**

   * **创建索引**

      ```sql
      # CREATE INDEX indexName ON employee(name[(length)]);
      
      create index  index_salary on employee(salary);  --不推荐
      ```

   * **修改表结构（添加索引）**

      ```sql
      # ALTER table employee ADD INDEX indexName2(name);
      
      alter table employee add index  index_bonus(bonus); --推荐
      ```

   * **创建表的时候直接指定**

      ```sql
      CREATE TABLE myta1(  
      ID INT NOT NULL,   
      username VARCHAR(16) NOT NULL,  
      INDEX  index_username(username)  
      ); 
      ```

* **删除索引**

   ```sql
   drop index index_salary on employee ;
   
   alter table employee drop index index_bonus;
   ```

* **==唯一索引==****

   > **唯一索引与普通索引类似**
   >
   > **不同点**: 索引列的值必须唯一,但==允许有空值==，
   >
   > ​            如果是组合索引,则列值的组合==必须唯一==:

  * **添加唯一性索引**
  
     ```sql
     alter table employee add unique uni_name(name);
     ```
  
  * **删除唯一性索引**
  
     ```sql
     alter table employee drop index uni_name;
     ```
  
     

* **==主键索引==**

   * **添加主键索引**

      ```sql
      alter table employee add primary key(id);
      ```

   * **删除主键索引**

      ```sql
      alter table employee drop primary key;
      注意: 如果主键是自增的列,应该先取消自增,才能删除
      ALTER TABLE employee MODIFY id int;
      ```

* **==外键索引==**

   * **添加外键索引**

      ```sql
      alter table employee add foreign key(job_id) references job(id);
      ```

   * **删除外键索引**

      ```sql
      alter table employee drop foreign key employee_ibfk_1; # 删外键别名
      alter table employee drop index job_id; # 删对应索引
      ```



* **全文索引**

   > \# ALTER TABLE tbl_name ADD FULLTEXT index_name (column_list): =>该语句指定了索引为 FULLTEXT ,用于全文索引.
   >
   > \#注意:如果希望通过关键字的匹配来进行查询过滤,那么就需要基于**相似度的查询,**而不是原来的精确数值比较.
   >
   > \#全文索引就是为这种场景设计的.**只有字段的数据类型**为 **==char、varchar、text==** 及其系列才可以建全文索引.

  ```sql
  # 创建
  alter table employee add fulltext full_name(name);
  # 删除 
  alter table employee drop index full_name;
  # 目的: 模糊查询提高 name字段的查询效率
  select * from employee where name like '%a%';
  ```
  
* **组合索引**

  > 1. 组合索引遵循**==最左优先原则==**(Btree 方法)
  > 2. 索引方法是**Btree**,树状的,搜索时需要从根节点出发,上层节点对应靠左的值,所以有最左优先原则.

  ```sql
  CREATE TABLE test12(  
  ID INT NOT NULL,   
  col_a VARCHAR(16) NOT NULL, 
  col_b VARCHAR(16) NOT NULL, 
  col_c VARCHAR(16) NOT NULL,  
  INDEX abc_index (col_a,col_b,col_c)  
  ); 
  
  # 不起作用:
  --where  col_b = "some value"
  --where  col_c = "some value"
  --where  col_b = "some value" and col_c = "some value
  --where  col_c = "some value" and col_b = "some value"
  
  # 起作用
  where col_a ="value" and col_b="value" and col_c="value";
  # 可以没顺序(col_a 必须有)
  where col_b ="value" and col_c="value" and col_a="value";
  # col_a 和col_b 
  where col_a ="value" and col_b="value";
  
  # col_a 起作用,断点 col_c 不起作用
  where col_a ="value" and col_c="value";
  
  # col_a 起作用
  where col_a ="value";

* **expain**

   > **执行计划,可以分析查询语句的信息  (了解trace 语句,配置慢查询...)**

   * **注意 type 值:  system > const > eq_ref > ref >range >index >all**

   ```sql
   explain select name,salary from employee where id between 2 and 5;
   ```




## 9. 视图

```sql
#创建视图
create view myView as
select e.id,e.name,d.dname,j.jname
from employee as e
join department as d on e.department_id=d.id
join job as j on e.job_id=j.id;

#查询视图
select* from myView;

#查询name字段名包含'a'的员工信息
select * from myView where name like '%a%';

#删除视图
drop view myView;

#查看视图
desc myView;
show create view myView;
```



## 10. 事务

> 事务 :多条语句同时执行,要成功都成功要失败都失败.(转钱,装软件)
>
> 事务的四个特性是: **原子性(Atomicity)**、**一致性(Consistency)**、**隔离性(Isolation)**和**持久性(Durability),**简称**ACID**.

```sql
#  1.原子性(Atomicity):执行成功(commit),失败(rollback)
#  2.一致性(Consistency):数据一致性
#  3. 隔离性(Isolation):A事务和B事务冲突规范,设置级别
#  4. 持久性(Durability):不可逆
```

```sql
#开启事务
SET autocommit=0; //关闭自动提交
START TRANSACTION;
#编写一组事务的语句
UPDATE employee SET salary = 8000 WHERE id=1;
UPDATE employee SET salary = 5001 WHERE id=3;

#结束事务: 二选一
ROLLBACK;
#commit;


#2.演示savepoint 的使用
SET autocommit=0;
START TRANSACTION;
DELETE FROM employee WHERE id=5;
SAVEPOINT a;#设置保存点
DELETE FROM employee WHERE id=8;
ROLLBACK TO a;#回滚到保存点
```



