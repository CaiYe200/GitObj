# 数据库

## 数据库

##### 创建数据库

create database db；(如果该数据库已存在会报错，可加上create database db if not exists db，即不会报错但也不会创建相同的)

##### 删除数据库

drop database db；(与创建同理)

##### 显示所有的数据库

show databases；

##### 查看当前所操作的数据库

select database()；

## 数据表

##### 创建表

```mysql
create table db_user(
    id int primary key auto_increment comment '用户id',
    username varchar(10) not null comment '员工姓名',
    gender char(1) not null comment '性别 1男 2女',
    image varchar(300) not null comment '图片url地址',
    job char(1) comment '职位',
    entrydate date comment '入职日期',
    createdata datetime comment '表创建时间',
    updatadate datetime comment '表修改时间'
)comment '用户表';
```

##### 删除表

drop table db_user;

##### 重命名表

rename db_user to user;

##### 增加字段

alter table db_user add test int;

##### 修改字段类型

alter table db_user modify username varchar(11);

##### 修改字段名

alter table db_user change image img varchar(300);

##### 删除字段

alter table db_user drop test;

## 数据

### 增删改

```mysql
insert into db_user(id,username,gender) value (1,'cyz','1');
delete from db_user where id='2';
update db_user set username='yz' where id='1';
```

### 查询

#### 单表查询

##### 条件查询

```mysql
select username from db_user;
select * from db_user;
select username '姓名' from db_user;-- 别名
select distinct username from db_user;-- 不重复
select username from db_user where job in(2,3,4);
-- select username from db_user where job=2 or job=3 or job=4
select username from db_user where entrydate between '2001-02-03' and '2010-03-03';
-- select username from db_user where entrydate <='2001-02-03' and entrydate>='2010-03-03';
select id from db_user where username ='__'; -- 两个字
select id from  db_user where username='张%';
```

##### 聚合函数

```mysql
select count(id) from db_user;-- id有多少列
select count(*) from db_user;
select max(entrydate) from db_user;
select min(entrydate) from db_user;
select avg(id) from db_user;
select sum(id) from db_user;
```

##### 分组查询

```mysql
select job,count(*) from db_user where entrydate <='2015-01-01' group by job having count(*)>2;
-- 查询2015年前，数量大于两个人的职位
```

##### 排序查询

```mysql
select * from db_user order by id desc ;-- 降序查询
select * from db_user order by id asc ;-- 升序查询
```

##### 分页查询

```mysql
select * from db_user limit 0,5;-- 查询第一页
select * from db_user limit 5,5;-- 查询第二页
select * from db_user limit 10,5;-- 查询第三页
```

##### 示例

```mysql
select *
from db_user
where username = '% 张%'
  and gender = '1'
  and entrydate between '2001-01-01' and '2015-12-31'
order by entrydate desc
limit 0,10;

select if(gender=1,'男','女') 性别,count(*) from db_user group by gender;

select (case job when 1 then '1' when 2 then '2' end) 职位,count(*) from db_user group by job;
```

#### 多表查询

category 分类表

dish 菜品表

setmeal_dish 维护菜品和套餐多对多关系的表

setmeal 套餐表

```mysql
-- 1. 查询价格低于 10元 的菜品的名称 、价格 及其 菜品的分类名称 .

select d.name,d.price,c.name from dish d,category c where d.category_id=c.id and d.price<10;

-- 2. 查询所有价格在 10元(含)到50元(含)之间 且 状态为'起售'的菜品, 展示出菜品的名称、价格 及其
-- 菜品的分类名称 (即使菜品没有分类 , 也需要将菜品查询出来).
select d.name,d.price,c.name from dish d left join category c  on d.category_id = c.id where d.price between 10 and 50 and c.status=1;


-- 3. 查询每个分类下最贵的菜品, 展示出分类的名称、最贵的菜品的价格 .
select c.name,max(d.price) from category c,dish d where c.id=d.category_id group by c.name;
-- 4. 查询各个分类下 菜品状态为 '起售' , 并且 该分类下菜品总数量大于等于3 的 分类名称 .
select c.name from dish d, category c where d.category_id=c.id and c.status=1 group by c.name having count(*)>=3;
-- 5. 查询出 "商务套餐A" 中包含了哪些菜品 （展示出套餐名称、价格, 包含的菜品名称、价格、份数）.
select s.name,s.price,d.name,d.price,sd.copies
from dish d,setmeal_dish sd,setmeal s where d.id=sd.dish_id and s.id=sd.setmeal_id and s.name='商务套餐A';
-- 6. 查询出低于菜品平均价格的菜品信息 (展示出菜品名称、菜品价格).

select d.name,d.price from dish d where price<(select avg(price) from dish);
```



内连接：显示内连接和隐式内连接

隐式：select * from a,b where a.id=b.id;

显示：select * from a inner join b on a.id=b.id;

外连接：左连接和右连接

select  from d left joinc  on d.id = c.id ；





-- ========================= 子查询 ================================
-- 标量子查询
-- A. 查询 "教研部" 的所有员工信息
-- a. 查询 教研部 的部门ID - tb_dept
select id from tb_dept where name = '教研部';

-- b. 再查询该部门ID下的员工信息 - tb_emp
select * from tb_emp where dept_id = (select id from tb_dept where name = '教研部');


-- B. 查询在 "方东白" 入职之后的员工信息
-- a. 查询 方东白 的入职时间
select entrydate from tb_emp where name = '方东白';

-- b. 查询在 "方东白" 入职之后的员工信息
select * from tb_emp where entrydate > (select entrydate from tb_emp where name = '方东白');




-- 列子查询
-- A. 查询 "教研部" 和 "咨询部" 的所有员工信息
-- a. 查询 "教研部" 和 "咨询部" 的部门ID - tb_dept
select id from tb_dept where name = '教研部' or name = '咨询部';

-- b. 根据部门ID, 查询该部门下的员工信息 - tb_emp
select * from tb_emp where dept_id in (select id from tb_dept where name = '教研部' or name = '咨询部');

-- 行子查询
-- A. 查询与 "韦一笑" 的入职日期 及 职位都相同的员工信息 ;
-- a. 查询 "韦一笑" 的入职日期 及 职位
select entrydate,job from tb_emp where name = '韦一笑';

-- b. 查询与其入职日期 及 职位都相同的员工信息 ;
-- 方式一
select * from tb_emp where entrydate = (select entrydate from tb_emp where name = '韦一笑') and job = (select job from tb_emp where name = '韦一笑');

select * from tb_emp where entrydate = '2007-01-01' and job=2;

-- 方式二
select * from tb_emp where (entrydate,job)=(select entrydate,job from tb_emp where name = '韦一笑');

-- 表子查询
-- A. 查询入职日期是 "2006-01-01" 之后的员工信息 , 及其部门名称
-- a. 查询入职日期是 "2006-01-01" 之后的员工信息
select * from tb_emp where entrydate > '2006-01-01';

-- b. 查询这部分员工信息及其部门名称 - tb_dept
select e.* , d.name from (select * from tb_emp where entrydate > '2006-01-01') e , tb_dept d where e.dept_id = d.id;



### 索引

mysql默认索引结构为B+树

create index n on db_emp(name)//创建索引，n为索引名，db_emp为表，name为字段名

show index on db_emp//展示表中的索引

drop index n on db_emp//删除索引











































































```MYSQL
create table db_user(
    id int primary key auto_increment comment '用户id',
    username varchar(10) not null comment '员工姓名',
    gender char(1) not null comment '性别 1男 2女',
    image varchar(300) not null comment '图片url地址',
    job char(1) comment '职位',
    entrydate date comment '入职日期',
    createdata datetime comment '表创建时间',
    updatadate datetime comment '表修改时间'
)comment '用户表';

show tables;

show create table db_user;

show columns from db_user;

alter table db_user add test int;
alter table db_user modify username varchar(11);
alter table db_user change image img varchar(300);

alter table db_user drop test;

insert into db_user(id,username,gender) value (1,'cyz','1');
insert into db_user(id,username,gender) value (2,'kk','2');

select * from db_user where id='1';

delete from db_user where id='2';
update db_user set username='yz' where id='1';
select * from db_user where id='1';

select username from db_user;
select * from db_user;
select username '姓名' from db_user;-- 别名
select distinct username from db_user;-- 不重复
select username from db_user where job in(2,3,4);
-- select username from db_user where job=2 or job=3 or job=4
select username from db_user where entrydate between '2001-02-03' and '2010-03-03';
-- select username from db_user where entrydate <='2001-02-03' and entrydate>='2010-03-03';
select id from db_user where username ='__'; -- 两个字
select id from  db_user where username='张%';


select count(id) from db_user;
select count(*) from db_user;
select max(entrydate) from db_user;
select min(entrydate) from db_user;
select avg(id) from db_user;
select sum(id) from db_user;

select job,count(*) from db_user where entrydate <='2015-01-01' group by job having count(*)>2;

select * from db_user order by id desc ;-- 降序查询
select * from db_user order by id asc ;-- 升序查询

select *
from db_user
where username = '% 张%'
  and gender = '1'
  and entrydate between '2001-01-01' and '2015-12-31'
order by entrydate desc
limit 0,10;

select if(gender=1,'男','女') 性别,count(*) from db_user group by gender;

select (case job when 1 then '1' when 2 then '2' end) 职位,count(*) from db_user group by job;
```
