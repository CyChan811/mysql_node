## mysql事务
-- mysql中,事务其实是一个最小的不可分割的工作单元,事务能够保证一个业务的完整性.
-- 比如我们的银行转账:
```sql
-- a-> -100
update user set money = money - 100 where name = 'a';

-- b-> +100
update user set money = money + 100 where name = 'b';
```
-- 实际的程序中,如果只有一条语句执行成功了,而另外一条没有执行成功,则会出现数据前后不一致.
-- 因此,在执行多条有关联的sql语句时,**事务**可能会要求这些sql语句要么同时成功,要么就同时失败.

**mysql中如何控制事务?**
1. mysql**默认是开启事务的(自动提交)**
```sql
select @@autocommit;
+--------------+
| @@autocommit |
+--------------+
|            1 |
+--------------+
```
**默认事务开启(自动提交)的作用:** 
-- 当我们去执行一个sql语句的时候,效果会立即体现出来,且不能回滚.

-- 回滚的例子:
```sql
create database bank;

create table user(
    id int primary key,
    name varchar(20),
    money int
);

insert into user values(1, 'a', 1000);
```
可以看到，在执行插入语句后数据立刻生效，原因是 MySQL 中的**事务自动将它提交到了数据库**中。那么所谓**回滚**的意思就是，撤销sql语句的执行效果。

在mysql中使用`rollback`执行回滚:
```sql
rollback;

select * from user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
+----+------+-------+

-- 数据依然存在,说明现在不能回滚,因为上面的数据已经自动提交了,已经产生了实实在在的效果
```
**那么如何设置回滚呢?**
-- 设置mysql自动提交为false
```sql
set autocommit = 0;

select @@autocommit;
+--------------+
| @@autocommit |
+--------------+
|            0 |
+--------------+ 
```
-- 上面的操作,关闭了mysql的自动提交(commit)
```sql
-- 再插入数据
insert into user values(2, 'b', 1000);

select * from user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
|  2 | b    |  1000 |
+----+------+-------+

-- 执行回滚
rollback;

select * from user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
+----+------+-------+
-- 回滚成功,说明数据还没有真正被提交
```
关闭`autocommit`后,数据的变化是在一张**虚拟的临时数据表**中展示的,发生变化的数据并没有真正插入到数据表中.

那么如何将数据真正提交到数据库中呢,使用`commit`提交:
```sql
-- 插入数据
insert into user values(2, 'b', 1000);

-- 手动提交数据
commit;

-- 回滚
rollback;

-- 发现回滚无效,数据不可以撤销(持久性)
select * from user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
|  2 | b    |  1000 |
+----+------+-------+
```
**总结:**
1. 自动提交
   - 查看自动提交状态: `select @@autocommit;`
   - 设置自动提交状态:`set autocommit = 0;`
2. 手动提交
    `@@autocommit = 0`时,使用`commit;`命令手动提交事务
3. 事务回滚
   `@@autocommit = 0`时,使用`rollback`命令回滚事务

-- 说回到转账事务:
```sql
-- a-> -100
update user set money = money - 100 where name = 'a';

-- b-> +100
update user set money = money + 100 where name = 'b';

-- 执行转账后
select * from user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |   900 |
|  2 | b    |  1100 |
+----+------+-------+
```
如果这时候转账出现问题,就可以通过`rollback`使转账的两条语句都不成功
```sql
-- 事务回滚
rollback;

-- 数据回到了转账前的状态
select * from user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
|  2 | b    |  1000 |
+----+------+-------+
```
也就是说，事务给我们提供了一个可以反悔的机会。假设数据没有发生意外，这时可以通过`commit`手动将数据真正提交到数据表中。

**手动开启事务:`begin`或者`start transaction`**
事务的默认提交被开启(`@@autocommit = 1`)后,此时就不能使用事务回滚了,但是我们还可以开启一个事务处理事件,使其可以发生回滚:
```sql
-- 使用 begin 或者 start transaction 手动开启一个事务
begin;
update user set money = money - 100 where name = 'a';
update user set money = money + 100 where name = 'b';

select * from user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |   900 |
|  2 | b    |  1100 |
+----+------+-------+

rollback;
-- 回滚成功
select * from user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
|  2 | b    |  1000 |
+----+------+-------+
```
再次使用`commit`提交数据,提交后便不能事务回滚了
```sql
-- 手动开启事务
begin;
update user set money = money - 100 where name = 'a';
update user set money = money + 100 where name = 'b';

select * from user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |   900 |
|  2 | b    |  1100 |
+----+------+-------+

-- 手动提交数据
commit;

-- 测试回滚
rollback;

-- 回滚失败,因为数据已经提交了
select * from user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |   900 |
|  2 | b    |  1100 |
+----+------+-------+
-- 事务开启之后,一旦 commit 提交,就不可以回滚(也就是当前的这个事务在提交的时候就结束了)
```
#### 事务的四大特征 ACID
**A 原子性: 事务是最小的单位,不可以再分割**
**C 一致性: 事务要求,同一事务中的 sql 语句,必须保证同时成功或者同时失败**
**I 隔离性: 事务1 和 事务2 之间是具有隔离性的.**
**D 持久性: 事务一旦结束(commit),就不可以返回(rollback).** 

事务开启:
1. 修改默认提交 `set autocommit = 0;`
2. `begin;`
3. `start transaction;`

事务手动提交:
    `commit;`

事务手动回滚:
    `rollback;`

**事务的隔离性(隔离级别由低到高):**
1. `read uncommitted;`(读未提交的)
2. `read committed;`(读已经提交的)
3. `repeatable read;`(可以重复读)
4. `serializable;`(串行化)


**1. `read uncommitted;`(读未提交的)**
如果有事务a和事务b,a事务对数据进行操作,在操作的过程中,事务没有被提交,但是b可以看见a操作的结果.

bank数据库 user表
```sql
insert into user values(3, '小明',1000);
insert into user values(4, '淘宝店', 1000);

select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
+----+-----------+-------+
```
-- 如何查看数据库的隔离级别?
```sql
mysql 8.0:
--系统级别的
select @@global.transaction_isolation;
-- 会话级别的
select @@transaction_isolation;

-- mysql默认隔离级别 REPEATABLE-READ
select @@global.transaction_isolation;
+--------------------------------+
| @@global.transaction_isolation |
+--------------------------------+
| REPEATABLE-READ                |
+--------------------------------+

mysql 5.x:
select @@global.tx_isolation;
select @@tx_isolation;
```
-- 如何修改隔离级别?
```sql
set global transaction isolation level read uncommitted;

select @@global.transaction_isolation;
+--------------------------------+
| @@global.transaction_isolation |
+--------------------------------+
| READ-UNCOMMITTED               |
+--------------------------------+
```
-- 转账:小明在淘宝店买鞋子:800块钱
```sql
 小明->成都 ATM
 淘宝店-> 广州 ATM

begin;
update user set money = money - 800 where name = '小明';
update user set money = money + 800 where name = '淘宝店';

select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |   200 |
|  4 | 淘宝店    |  1800 |
+----+-----------+-------+
 
 -- 然后小明让淘宝店去查是否到帐
 -- 新建终端
 use bank;
 -- 淘宝店在广州查看账户
 select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |   200 |
|  4 | 淘宝店    |  1800 |
+----+-----------+-------+

-- 小明->成都
rollback;
select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
+----+-----------+-------+

-- 淘宝店->广州再查帐
select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
+----+-----------+-------+
-- 发现收到的钱没了
```
-- 如果两个不同的地方,都在进行操作,如果事务a开启之后,他的数据会被其他事务读取到,这样就会出现**脏读**
-- **脏读:** 一个事务读到了另外一个事务没有提交的数据,就叫做脏读
-- 实际开发是不允许脏读出现的.

**2. `read committed;`(读已经提交的)**
```sql
-- 修改隔离级别为 READ-COMMITTED
set global transaction isolation level read committed;

-- 查看级别
select @@global.transaction_isolation;
+--------------------------------+
| @@global.transaction_isolation |
+--------------------------------+
| READ-COMMITTED                 |
+--------------------------------+
```
```sql
-- 小张:银行的会计
begin;
select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
+----+-----------+-------+

-- 小张出去上厕所,此时,小王:
-- 新建终端
begin;
insert into user values(5, 'c', 100);
commit;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
|  5 | c         |   100 |
+----+-----------+-------+

-- 小张上完厕所,查看平均数
select avg(money) from user;
+------------+
| avg(money) |
+------------+
|   820.0000 |
+------------+
-- money的平均值不是1000,变少了
```
-- 虽然我只能读到另一个事务提交的数据,但还是会出现问题,就是读取同一个表的数据,发现前后不一致
-- 这就是不可重复读现象,在read committed的时候会出现
-- 意思就是: 你刚select完,想立马计算avg,但当你输入代码的时候有人提交新数据改变了表,这就是不可重复读

**3. `repeatable read;`(可以重复读)**
```sql
-- 修改隔离级别为 REPEATABLE-READ
set global transaction isolation level repeatable read;

-- 查看级别
select @@global.transaction_isolation;
+--------------------------------+
| @@global.transaction_isolation |
+--------------------------------+
| REPEATABLE-READ                |
+--------------------------------+
```
```sql
-- 张全蛋 ->成都
begin;
insert into user values(6, 'd', 1000);
commit;
select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
|  5 | c         |   100 |
|  6 | d         |  1000 |
+----+-----------+-------+

-- 王尼玛 ->北京
begin;
select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
|  5 | c         |   100 |
+----+-----------+-------+
-- 此时看不到有6号数据,所以王尼玛想插入6号数据
insert into user values(6, 'd', 1000);
ERROR 1062 (23000): Duplicate entry '6' for key 'PRIMARY'
-- 提示错误为6号数据已存在
```
-- 这种现象就叫做幻读!
-- 事务a和事务b同时操作一张表,事务a提交的数据,不能被事务b读到,就可以造成幻读.


**4. `serializable;`(串行化)**
```sql
-- 修改隔离级别为 SERIALIZABLE
set global transaction isolation level serializable;

-- 查看级别
select @@global.transaction_isolation;
+--------------------------------+
| @@global.transaction_isolation |
+--------------------------------+
| SERIALIZABLE                   |
+--------------------------------+
```
```sql
-- 张全蛋-成都
begin;

-- 王尼玛-北京
begin;

-- 张全蛋-成都
insert into user values(7, '赵铁柱', 1000);
commit;
select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
|  5 | c         |   100 |
|  6 | d         |  1000 |
|  7 | 赵铁柱    |  1000 |
+----+-----------+-------+

-- 王尼玛-北京
select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
|  5 | c         |   100 |
|  6 | d         |  1000 |
|  7 | 赵铁柱    |  1000 |
+----+-----------+-------+

-- 张全蛋-成都
begin;
insert into user values(8, '王小花', 1000);

-- 王尼玛-北京
begin;
insert into user values(8, '王小花', 1000);
-- 张全蛋还没有提交,此时王尼玛想要插入数据,但会发现此时的sql语句会被卡住了
-- 当user表被另外一个事务操作的时候,其他事务里面的写操作,是不可以进行的,进入排队状态(串行化),直到张全蛋那边的事务结束后,王尼玛这边的写入操作才会执行(在没有等待超时的情况下)
```
-- 串行化问题是,性能特差

-- 性能比较:
read uncommitted > read committed > repeatable read > serializable

-- 隔离级别越高,性能越差
mysql默认隔离级别是 repeatable read