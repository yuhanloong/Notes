# Mac安装

官网下载 8.0 的最高版本的 .dmg 直接安装。

配置环境变量

```bash
# 打开 bash shell 和 zsh shell 的环境变量文件
open -e ~/.bash_profile
open -e ~/.zshrc

# 将环境变量添加进去
# MySQL
export PATH=${PATH}:/usr/local/mysql/bin

# 保存
source ~/.bash_profile
source ~/.zshrc
```

启动

```bash
# 启动 mysql 服务
sudo /usr/local/mysql/support-files/mysql.server start

# 停止 mysql 服务
sudo /usr/local/mysql/support-files/mysql.server stop

# 重启 mysql 服务
sudo /usr/local/mysql/support-files/mysql.server restart
```

登陆

```bash
# 登陆
mysql -uroot -padministrator
```

# SQL语句的分类

DQL(Data Query Language)：

- 数据查询语言：用来查询数据库中表的记录(带有select的都是查询语句)
- select、from、where...

DML(Data Manipulation Language)：

- 数据操作语言：用来对数据库中表的记录进行更新(对表中的数据增删改)
- insert增、delete删、update改...

DDL(Data Definition Language)：

- 数据定义语言：用来定义数据库对象：数据库，表，列等(对数据库对象增删改)
- create新建、drop删除、alter修改...
- 这个增删改和DML不同,这个主要对表的结构进行操作

DCL(Data Control Language)：

- 数据控制语言：用来定义数据库的访问权限和安全级别，及创建用户
- grant授权、revoke撤销权限...

TCL(Transaction Control Language)：

- 事务控制语言
- commit：事务提交
- rollback：事务回滚

# 数据库操作

```sql
# 查看所有数据库
show databases;
# 查看 test 数据库的定义的信息
show create database test;

# 使用 test 数据库
use test;
# 查看正在使用的数据库
select database();

# 创建 test 数据库
create database test;
# 如果不存在则创建 test 数据库
create database if not exists test;

# 删除 test 数据库
drop database test;
# 如果存在则删除 test 数据库
drop database if exists test;

# 查看 mysql 版本号
select version();

# 执行 sql 文件
source 文件路径;
```

# 数据类型

- varchar(最长255)
  - 可变长字符串
  - 定义为10长度后,添加一个3长度的'abc'就分配3个长度
- char(最长255)
  - 定长字符串
  - 定义为10长度后,添加一个3长度的'abc'就分配10个长度后面补7个空格
- tinyint(最长3)
  - java中的byte
- int(最长11)
  - java中的int
- bigint(最长20)
  - java中的long
- float
  - 单精度浮点型
- double
  - 双精度浮点型
- date
  - 短日期
- datetime
  - 长日期
- clob
  - 字符大对象,能存4G字符串
- blob
  - 二进制大对象,存图片、音乐、视频
  - 添加数据时要用IO流
- 日期格式
  - %Y 年
  - %m 月
  - %d 日
  - %h 时
  - %i 分
  - %s 秒
- 日期默认格式
  - %Y-%m-%d
  - %Y-%m-%d %h:%i:%s

# 表操作

## 增删改

```sql
# 创建 t_user 表
create table t_user(
    id bigint primary key auto_increment comment '主键ID',
    name varchar(10) unique comment '名称',
    gender char(1) default '男' comment '性别',
    age tinyint comment '年龄',
    phone varchar(11) unique comment '电话'
) engine=InnoDB default charset=utf8 comment='用户表'; # 指定存储引擎为InnoDB，默认编码为utf8，注释这张表为用户表
# 如果不存在则创建 t_user 表
create table if not exists t_user(
    id bigint primary key auto_increment comment '主键ID',
    name varchar(10) unique comment '名称',
    gender char(1) default '男' comment '性别',
    age tinyint comment '年龄',
    phone varchar(11) unique comment '电话'
) engine=InnoDB default charset=utf8 comment='用户表'; # 指定存储引擎为InnoDB，默认编码为utf8，注释这张表为用户表
# 将 t_user 的查询结果作为一张表创建出来(复制表)
create table t_user_copy as select * from t_user;

# 删除 t_user 表
drop table t_user;
# 如果存在则删除 t_user 表
drop table if exists t_user;

# 修改表名为 t_new_user，as 可以不加
alter table t_user rename as t_new_user;
# 添加 detail 字段
alter table t_user add detail varchar(255) comment '描述';
# 将 gender 添加到 name 前面
alter table t_user add gender char(1) before name;
# 将 gender 添加到 name 后面
alter table t_user add gender char(1) after name;
# 删除 name 字段
alter table t_user drop name;
# 修改 name 字段的类型和属性
alter table t_user modify name varchar(32) unique comment '名称';
# 修改 name 字段的名称、类型和属性
alter table t_user change name new_name varchar(32) unique comment '名称';

# 插入单条数据，按顺序填写所有字段
insert into t_user values(0, 'admin', '男', 18, '17374000851');
# 插入单条数据，只填写指定字段
insert into t_user(name, age, phone) values('admin', 18, '17374000851');
# 插入多条数据，用逗号(,)隔开
insert into t_user values(0, 'admin', '男', 18, '17374000851'), (0, 'zhangsan', '男', 20, '13200000000');
# 将右边的查询结果插入到表里
insert into t_user_copy select * from t_user;

# 修改满足条件的数据，不加条件会修改所有的数据
update t_user set name = 'zhangsan', age = 20 where id = 1;

# 删除满足条件的数据，不加条件会删除表里所有的数据，不会重置auto_increment计数器，效率低支持回滚
delete from t_user where id = 1;
# 清空表里的数据，会重置auto_increment计数器，效率高不支持回滚
truncate table t_user;
```

## 普通查询

```sql
# 查看所有表
show tables;

# 查看建表时的完整语句
show create table 表名;

# 查看表结构
desc 表名;

# 查询表里所有的字段
select * from 表名;
# 查询一个字段
select 字段名 from 表名;
# 查询多个字段，用逗号隔开
select 字段名, 字段名 from 表名;
# 将查询的字段改名
select 字段名 as '新名称' from 表名;
# 将查询的字段乘以 10
select 字段名 * 10 from 表名;
# 将查询的字段乘以 10 后改名
select 字段名 * 10 as '新名称' from 表名;
```

## 条件查询

- `=`等于
- `!=`或`<>`不等于
- `<`小于
- `<=`小于等于
- `>`大于
- `>=`大于等于
- `and`并且
- `or`或者
- `not`取反
- `in`包含
- `not in`不包含
- `is`是
- `is not`不是

`and`和`or`在一起时`and`优先。

```sql
# 查询工资等于 800 的员工
select 工资
from 表名
where 工资 = 800;

# 查询工资大于 800 且小于 1000 的员工
select 工资
from 表名
where 工资 > 800 and 工资 < 1000;

# 查询补助为 null 的员工
select 补助
from 表名
where 补助 is null;

# 查询补助不为 null 的员工
select 补助
from 表名
where 补助 is not null;

# 查询工资大于 500 且部门编号为 10 或 20 的员工
select 工资,编号
from 表名
where 工资 > 500 and (编号 = 10 or 编号 = 20);

# 查询工资为 500 和 800 的员工
select 工资
from 表名
where 工资 in(500, 800);

# 查询工资不为 500 和 800 的员工
select 工资
from 表名
where 工资 not in(500, 800);

# 查询工资大于 800 且小于 1000 的员工
select 工资
from 表名
where 工资 between 800 and 1000;
```

## 模糊查询

- `%`代表任意多个字符，不区分大小写
  - `T%`以T开头
  - `%T`以T结尾
  - `%T`%包含T
- `_`代表任意一个字符
- `\`转义

```sql
# 查询名字以 T 开头的员工
select 姓名
from 表名
where 姓名 like 'T%';

# 查询名字第 2 个字母是 T 的员工
select 姓名
from 表名
where 姓名 like '_T%';

# 查询名字以 _ 开头的员工
select 姓名
from 表名
where 姓名 like '\_%';
```

## 排序查询

排序总是在最后执行。

- `asc`升序
- `desc`降序

```sql
# 升序查询所有员工的工资
select 工资
from 表名
order by 工资 asc;

# 降序查询所有员工的工资
select 工资
from 表名
order by 工资 desc;

# 升序查询所有员工的姓名和工资，如果工资相同再按姓名排序
select 姓名, 工资
from 表名
order by 工资 asc, 姓名 asc;

# 升序查询工资在 1250 到 3000 之间的员工
select 工资
from 表名
where 工资 between 1250 and 3000
order by 工资 asc;
```

## 单行处理函数

- `lower()`转换小写
- `upper()`转换大写
- `substr()`截取字符串，起始下标为1
- `concat()`字符串拼接
- `length()`取长度
- `trim()`去空格
- `str_to_date()`将字符串转换成日期
- `date_format()`格式化日期
- `format()`设置千分位
- `round()`四舍五入
- `rand()`生成随机数
- `ifnull`可以将 null 转换成一个具体值(null 只要参与运算结果都是 null)
- `convert()`字符串转 int
- `cast()`字符串转 int
- `curdate()`获取当前日期(格式：%y-%m-%d)
- `curtime()`获取当前时间(格式：%h:%i:%s)
- `now()`获取当前日期时间(格式：%y-%m-%d %h:%i:%s)
- `year()`获取一个日期里的年
- `month()`获取一个日期里的月
- `day()`获取一个日期里的日
- `time()`获取一个日期里的时间(时分秒)
- `hour()`获取一个日期里的时
- `minute()`获取一个日期里的分
- `second()`获取一个日期里的秒
- `week()`获取第一个日期里的第几周(从星期一到星期天：0123456)
- `weekday()`获取第一个日期里的第几周(从星期一到星期天：2345671)
- `timestampdiff()`获取一个日期到另一个日期之间的(年月日时分秒)数
- `date_add()`给一个日期加时间

```sql
# 将 date 日期转换成 varchar 并指定格式
select date_format(日期, '%d-%m-%y') from 表名;
```

## 多行处理函数

- `count()`计数
- `sum()`求和
- `avg()`平均值
- `max()`最大值
- `min()`最小值
- `group by`分组
- `having`对分完组的数据进行过滤
  - `where`和`having`之间优先选择`where`

```sql
# 根据工作分组，查询每个工作的总工资
select 工作, sum(工资)
from 表名
group by 工作;

# 先根据部门分组，再根据工作分组
select 部门, 工作, max(工资)
from 表名
group by 部门, 工作;

# 查询每个部门最大工资大于 3000 的员工
select 部门, max(工资)
from 表名
group by 部门
having max(工资) > 3000;
```

## 连接查询

内连接：

- 等值连接
- 非等值连接
- 自连接

外连接：

- 左外连接(左连接)
- 右外连接(右连接)

`distinct`去重复(不会修改表里的数据)。

```sql
# 查询所有工作岗位去掉重复的
select distinct 工作 from 表名;

# 如果有多个字段联合起来去重复
select distinct 姓名, 工作 from 表名;

# 统计所有工作岗位的数量
select count(distinct 工作) from 表名;

# 内连接.等值连接：查询员工表，并连接查询每个员工对应的部门
select *
from 员工表 as 别名 inner join 部门表 as 别名
on 员工表.部门编号 = 部门表.部门编号;

# 内连接.非等值连接：查询所有员工的工资等级
select 员工表.员工名, 员工表.工资, 等级表.等级
from 员工表 as 别名 inner join 等级表 as 别名
on 员工表.工资 between 等级表.最小工资 and 等级表.最大工资;

# 内连接.自连接：查询所有员工的领导名称
select 表a.姓名, 表b.姓名
from 表名 as 别名 inner join 表名 as 别名
on 表a.员工的领导编号 = 表b.领导的员工编号;

# 外连接.左外连接：查询两张表相同的字段，值也相同的所有信息，左边匹配不上的最后也会查询出来
select 员工表.员工名, 部门表.部门编号
from 员工表 as 别名 left outer join 部门表 as 别名
on 员工表.部门编号 = 部门表.部门编号;

# 外连接.右外连接：查询两张表相同的字段，值也相同的所有信息，右边匹配不上的最后也会查询出来
select 员工表.员工名, 部门表.部门编号
from 员工表 as 别名 right outer join 部门表 as 别名
on 员工表.部门编号 = 部门表.部门编号;

# 连接多张表：查询所有员工的员工名，工资，部门名，工资等级
select 员工表.员工名, 员工表.工资, 部门表.部门名, 等级表.等级
from 员工表 as 别名
join 部门表 as 别名
on 员工表.部门编号 = 部门表.部门编号
join 等级表 as 别名
on 员工表.工资 between 等级表.最小工资 and 等级表.最大工资;
```

## 子查询、union、limit、伪列

- 子查询：select 里面再嵌套 select
- union：将两个查询结果连接起来
- limit：分页
- 伪列：自定义列数据

```sql
# 查询比最低工资高的员工
select 姓名, 工资
from 表名
where 工资 > (select min(工资) from 表名);

# 找出工作是 SALESMAN 和 MANAGER 的员工
select ename, job from emp where job = 'SALESMAN'
union
select ename, job from emp where job = 'MANAGER';

# 查询前 5 个
select ename from emp limit 5;
# 查询第 3 个到第 7个
select ename from emp limit 2, 5;

# 自定义'年级级别'列
select
    a.cid as '班级ID',
    a.caption as '班级名称',
    b.gname as '年级',
    case
  			when b.gid in(1, 2) then '低' # 一二年级为低年级
  			when b.gid in(3, 4) then '中' # 三四年级为中年级
  			when b.gid in(5, 6) then '高' # 五六年级为高年级
  	end '年级级别'
from
  	class as a
join
  	class_grade as b
on
  	a.grade_id = b.gid;
```

# 查询语句顺序

代码顺序：

1. select
2. from
3. where
4. group by
5. having
6. order by
7. limit

执行顺序：

1. from
2. where
3. group by
4. having
5. select
6. order by
7. limit

子查询可以存在的位置：

- select (select)，在这里的子查询必须是返回一条记录的
- from (select)
- where (select)

# 约束

- 非空约束：not null
- 唯一性约束：unique
- 主键约束：primary key(简称PK)
- 自然主键 primary key auto_increment
- 外键约束：foreign key(简称FK)
- 不允许为负数 unsigned
- 0填充 zerofill，如类型int(3)，值填5则为005
- 注释 comment

```sql
create table t_user(
    id int unique, # 跟在字段后面的是列级约束
    name varchar(32),
    unique(id, name) # 独自占一行的是表级约束，可以设置多个字段联合起来
);

# 外键约束
create table t_class(
    id int primary key,
    classname varchar(255)
);
create table t_student(
    id int primary key,
    name varchar(255),
    class_id int,
    foreign key(class_id) references t_class(id) # 将 t_class 表的 id 作为外键
);

# 给 name 添加 unique 约束，右边的 t_user 可以不写
alter table t_user add unique t_user(name);

# 删除表里的主键(只能删主键)
alter table t_user drop primary key;

# 添加一个 class_id 外键字段，将 t_class 表的 id 作为外键
alter table t_student add constraint class_id foreign key(class_id) references t_class(id);
# 删除表里的外键
alter table t_student drop foreign key class_id;
```

# 存储引擎

MyISAM 存储引擎：

- 使用三个文件表示每个表：
  - 格式文件(mytable.frm)
  - 数据文件(mytable.MYD)
  - 索引文件(mytable.MYI)：只要是不能重复的字段上会自动创建索引
- MyISAM 的特点：
  - 可被转换为压缩、只读表来节省空间
- MyISAM 不支持事务机制，安全性低

InnoDB 存储引擎：

- 这是 mysql 默认的存储引擎，同时也是一个重量级的存储引擎
- InnoDB 支持事务，支持数据库崩溃后自动恢复机制
- InnoDB 存储引擎最主要的特点是非常安全
- 它管理的表具有下列主要特征：
  - 每个 InnoDB 表在数据库目录中以 .frm 格式文件表示
  - InnoDB 表空间 tablespace 被用于存储表的内容
  - 提供一组用来记录事务性活动的日志文件
  - 用 COMMIT(提交)、SAVEPOINT 及 ROLLBACK(回滚) 支持事务处理
  - 提供全 ACID 兼容
  - 在 MySQL 服务器崩溃后提供自动恢复
  - 多版本(MVCC)和行级锁定
  - 支持外键及引用的完整性，包括及关联删除和更新
- InnoDB 最大的特点就是支持事务：
  - 以保证数据的安全，效率不高，不能压缩，不能转换为只读
  - 不能很好的节省存储空间

MEMORY 存储引擎：

- 使用 MEMORY 存储引擎的表其数据存储在内存中，且行的长度固定
- 这两个特点使得 MEMORY 存储引擎非常快
- MEMORY 存储引擎管理的表具有下列特征：
  - 在数据库目录内，每个表均以 .frm 格式的文件表示
  - 表数据及索引被存储在内存中
  - 表级锁机制
  - 不能包含 TEXT 或 BLOB 字段
- MEMORY 存储疫情以前被称为HEAP引擎
- MEMORY 引擎优点：查询效率是最高的，不需要和硬盘交互
- MEMORY 引擎缺点：不安全，关机后数据消失，因为数据和索引都是在内存当中

```sql
# 查看 mysql 支持哪些存储引擎
show engines \G

# 建表时指定存储引擎和字符编码
create table t_user(
  id int primary key,
  name varchar(32)
) engine=InnoDB default charset=utf8; # engine：存储引擎，default charset：默认字符编码

# 修改存储引擎
alter table t_user engine=MyISAM;
```

# 事务

事务和事务之间的隔离级别：

- 读未提交：read uncommitted，没提交就读到了
  - 事务A可以读取到事务B未提交的数据
  - 这种隔离级别存在的问题是：
    - 脏读现象(Dirty Read)
  - 这种隔离级一般都是理论上的，大多数的数据库隔离级别都是二挡起步
- 读已提交：read committed，提交之后才读到
  - 事务A只能读取到事务B提交之后的数据
  - 解决了脏读的现象
  - 这种隔离级别存在的问题是：
    - 不可重复读取数据，当事务开启之后，第一次读到的数据是3条，当前事务还没有结束，可能第二次再读取得时候
    - 读到的数据是4条，3不等于4称为不可重复读取
- 可重复读：repeatable read，提交之后也读不到，永远读取的都是刚开启事务是的数据
  - 事务A开启之后，不管是多久，每一次在事务A中读取到的数据都是一致的，即使事务B将数据已经修改
  - 并且提交了，事务A读取到的数据还是没有发生改变，这就是可重复读
  - 可重复读解决了不可重复读取数据的问题
  - 可重复读存在的问题是：
    - 可能会出现幻影读，每一次读取到的数据都是幻象，不够真实
  - mysql 默认的事务隔离级别就是这个
- 序列化/串行化：serializable(最高隔离级别)
  - 这是最高隔离级别，效率低，解决了所有问题
  - 这种隔离级别表示事务排队，不能并发
  - 类似于synchronized，线程同步
  - 每一次读取到的数据都是最真实的，并且效率是最低的

```sql
# 关闭自动提交事务
set autocommit=0;
# 开启自动提交事务
set autocommit=1;

# 开始事务
start transaction;
# 提交事务
commit;
# 回滚事务
rollback;

# 标记数据段为 mark1
savepoint mark1;
# 回滚到 mark1
rollback to savepoint mark1;

# 查看事务隔离级别
select @@tx_isolation;

# 设置隔离级别为 read nucommitted
set global transaction isolation level read uncommitted;
# 设置隔离级别为 read committed
set global transaction isolation level read committed;
# 设置隔离级别为 repeatable read
set global transaction isolation level repeatable read;
# 设置隔离级别为 serializable
set global transaction isolation level serializable;
```

# 索引

索引分类：

- 单一索引(普通索引)：给一个字段添加索引
- 复合索引(普通索引)：给多个字段添加索引
- 主键索引：给主键添加索引
- 唯一索引：给有 unique 约束的字段添加索引

```sql
# 建表时添加索引
create table t_user(
    id int primary key, # id 会自动成为主键索引
    name varchar(32),
    first_name varchar(32),
    last_name varchar(32),
    email varchar(255),
    description text,
    index idx_name (name), # 为 name 创建索引 idx_name
    index idx_all_name (first_name, last_name), # 为 first_name 和 last_name 列创建复合索引 idx_all_name
    unique index idx_email (email), # 为 email 创建唯一索引 idx_email
);

# 为 name 创建索引 idx_name
create index idx_name on t_user(name);
# 为 first_name 和 last_name 列创建复合索引 idx_all_name
create index idx_all_name on t_user(first_name, last_name);
# 为 name 创建唯一索引 idx_name
create unique index idx_name on t_user(name);

# 删除 idx_name 索引
drop index idx_name on t_user;

# 查看一张表的索引
show index from t_user;
```

# 视图

站在不同的角度去看待同一份数据。

对视图对象增删改查，会导致原表被操作。

```sql
# 将右边的查询结果创建成一张视图
create view view_t_user as select * from t_user;
# 连接查询多张表创建成一张视图，用于同时修改多张表的数据
create view view_t_class_t_student
as select a.id, a.name, b.classid, b.classname
from t_student as a join t_class as b on a.classid = b.classid;

# 删除 view_t_user 视图
drop view view_t_user;
# 如果存在则删除 view_t_user 视图
drop view if exists view_t_user;

# 面向视图查询
select * from view_t_user;

# 面向视图增加
insert into view_t_user values(0, 'zhangsan', '男', 18, '17374000851');

# 面向视图修改
update view_t_user set name = 'zhangsan', age = 20 where id = 1;

# 面向视图删除
delete from view_t_user where id = 1;
```

# 数据库设计三范式

- 第一范式：要求任何一张表必须有主键，没有一个字段原子性不可再分
  - 每列都是不可再分的最小数据单元
  - 一对一
- 第二范式：建立在第一方式基础之上，要求所有非主键字段完全依赖主键，不要产生部分依赖
  - 要求每个表只描述一件事情
  - 多对多
- 第三范式：建立在第二范式基础之上，要求所有非主键字段直接依赖主键，不要产生传递依赖
  - 如果一个关系满足2NF，并且除了主键以外的其他列都不传递依赖于主键列
  - 一对多

# 自定义函数

```sql
# 删除 function_now_date 函数
drop function function_now_date;
# 如果存在则删除 function_now_date 函数
drop function if exists function_now_date;

# 创建无参函数
create function function_now_date()
    returns varchar(30)
    return date_format(now(), '%y年%m月%d日 %h时%i分%s秒'); # 返回当前日期的中文格式
# 调用
select function_now_date();

# 创建有参函数
create function function_sum(num1 int, num2 int)
    returns varchar(30)
    return num1 + num2; # 返回两个数的和
# 调用
select function_sum(1, 2);
```
