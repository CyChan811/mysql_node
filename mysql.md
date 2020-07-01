## 一.登录和退出数据库服务器  
   - 登录:
    `$ mysql -uroot -p123456  `  
   - 退出:
    `exit;`

## 二.基本语法
-- 显示所有数据库
`show databases;`

-- 创建数据库
`create database test;`

-- 选择需要操作的数据库
`use test;`

-- 显示数据库中的所有数据表
`show tables;`

---
-- 创建数据表
```sql
create table pet(
    name varchar(20),
    owner varchar(20),
    species varchar(20),
    sex char(1),
    birth date,
    death date);
```
**注意事项:** 
1. var()与varchar()的区别在于var()是定长的,哪怕存储的字符串没有达到"()"中数字的上限,var()依然会占用空格来填充空间,而varchar()是不定长的,没有达到"()"中的上限则会自动去掉后面的空格;
2. 定义最后一个字段的时候不要加",";

---
-- 查看创建好的数据表的结构
-- describe pet;
`desc pet;`
说明:
```
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
```
Field: 字段的名称
Type: 字段的类型,可以有int var varchar
Key: 是否是关键字,如可以定义为 primary key
Default: 若该字段没有主动设置值的时候,该字段的默认值是什么

---
-- 查看数据表中的数据
`select * from pet;`

---
-- 往数据表中插入数据
`insert into pet values('Puffball', 'Diane', 'hamster', 'f', '1999-03-30', NULL);`
`insert into pet values('旺财', '周星驰', '狗', '公', '1990-01-01', NULL);`
-- 还有一种写法,代表我只在name和owner字段上面插入数据,其他皆为NULL/默认值的数据
`insert into pet(name, owner) values ('xx','cc');`

---
-- 删除数据
-- 语法: delete from tableName where 条件;
`delete from pet where name = '旺财';`

-- 修改数据
-- 语法:update tableName set 字段1=值1, 字段2=值2, ... where 条件;
`update pet set name = '旺旺财' where owner = '周星驰';`

-- 删除表
-- 语法:drop table tableName;
`drop table test;`

---
#### mysql 常用数据类型  
见 [菜鸟教程](https://www.runoob.com/mysql/mysql-data-types.html)

-- 数据类型如何选择?
- 日期选择按照格式
- 数值和字符串按照大小(常用:int float double char varchar text)
 
**注意:** 金钱最好用int/bigint(整数,单位为分,拿出来进行*100换成元),千万不要直接用浮点,会有精度丢失.

---
### 总结:
1. 增加: insert
2. 删除: delete
3. 修改: update
4. 查询: select

## 三.mysql建表约束

### 主键约束    
**1. 主键约束**   
它能够唯一确定一张表中的一条记录,也就是我们通过给某个字段添加约束,就可以使得该字段**不重复且不为空**
```sql
create table user(
    id int primary key,
    name varchar(20)
);

insert into user values(1, '张三');
```
运行`desc user`后:
```
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | NO   | PRI | NULL    |       |
| name  | varchar(20) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
可以看到id是不可以为NULL 而且 key的值 也变为:PRI(primary)
```

-- 修改表结构,添加主键(比较少用到,要用的时候再百度就行)
`alter table user add primary key(id);`

-- 删除主键(比较少用到,要用的时候再百度就行)
`alter table user drop primary key;`

-- 使用modify修改字段,添加约束(比较少用到,要用的时候再百度就行)
`alter table user modify id int primary key;`

**2. 联合主键**
联合主键中的每个字段都不为空,且联合的主键加起来不重复
```sql
create table user2(
    id int,
    name varchar(20),
    password varchar(20),
    primary key(id, name)
);

insert into user2 values(1, '张三', '123');
insert into user2 values(2, '张三', '123');
```

### 自增约束
-- auto_increment
-- 管控字段,让字段自动增长
```sql
create table user3(
    id int primary key auto_increment,
    name varchar(20)
);

insert into user3 (name) values('张三');
insert into user3 (name) values('张三');

select * from user3;
+----+--------+
| id | name   |
+----+--------+
|  1 | 张三   |
|  2 | 张三   |
+----+--------+
没有自定义id值,但自动生成了id值且自动增长
```

### 唯一约束
-- 约束修饰的字段的值不可以重复
```sql
create table user5(
    id int,
    name varchar(20)
);
alter table user5 add unique(name);

或:
create table user5(
    id int,
    name varchar(20) unique
);

或:
create table user5(
    id int,
    name varchar(20),
    unique(name)
);

-- unique(id, name),表示两个键加在一起不重复就行
create table user5(
    id int,
    name varchar(20),
    unique(id, name)
);

insert into user5 values(1, 'zhangsan');
insert into user5 values(2, 'zhangsan');
insert into user5 values(1, 'lisi');
```

-- 删除唯一约束
`alter table user5 drop index name;`

-- modify 添加
`alter table user5 modify name varchar(20) unique;`

**总结:**
1. 主键约束包含了唯一约束.
2. 建表的时候就添加约束
3. 可以使用`alter table tableName add unique(字段);`
4. 可以使用`alter table tableName modify 字段名 字段类型 unique;`


---
**应用场景:**
业务需求:设计一张用户注册表,用户姓名必须要用手机号来注册,而且手机号和用户名称都不能为空,那么:
```sql
CREATE TABLE user_test(
    id INT PRIMARY KEY AUTO_INCREMENT COMMENT'主键id',
    name VARCHAR(20) NOT NULL COMMENT'用户姓名,不能为空',
    phone_number VARCHAR(20) UNIQUE NOT NULL COMMENT'用户手机,不能重复且不能为空'
);
```
运行 `DESCRIBE user_test;`
```
+--------------+-------------+------+-----+---------+----------------+
| Field        | Type        | Null | Key | Default | Extra          |
+--------------+-------------+------+-----+---------+----------------+
| id           | int(11)     | NO   | PRI | NULL    | auto_increment |
| name         | varchar(20) | NO   |     | NULL    |                |
| phone_number | int(11)     | NO   | UNI | NULL    |                |
+--------------+-------------+------+-----+---------+----------------+
```
这样的话就达到了每一个手机号都只能出现一次,达到了每个手机号只能被注册一次.
用户姓名可以重复,但是手机号码却不能重复,符合正常的逻辑需求

---

### 非空约束
-- 修饰的字段不能为空 NULL
```sql
create table user6(
    id int,
    name varchar(20) not NULL
);
```

-- 移除非空约束
`alter table user6 modify name varchar(20);`

### 默认约束
-- 就是当我们插入字段值的时候,如果没有传值,就会使用默认值
-- 传了值,就不会使用默认值
```sql
create table user7(
    id int,
    name varchar(20),
    age int default 10
);
```
-- 移除默认约束
`alter table user7 modify age int;`
### 外键约束
-- 涉及到两个表:父表,子表(主表,副表)
```sql
--班级
create table classes(
    id int primary key,
    name varchar(20)
);

-- 学生表
create table students(
    id int primary key,
    name varchar(20),
    class_id int,
    foreign key(class_id) references classes(id)
);
//foreign :外来  references:参考

-- 插入数据
insert into classes values(1, '一班');
insert into classes values(2, '二班');
insert into classes values(3, '三班');
insert into classes values(4, '四班');

insert into students values(1001, '张三', 1);
insert into students values(1002, '李四', 2);
insert into students values(1003, '王五', 3);
insert into students values(1004, '赵六', 4);
```
**总结:**
1. 主表classes中没有的数据,在副表中,是不可以使用的.
2. 主表中的记录被副表引用,则主表中该记录是不可以被删除的.(如副表(students)中引用了4班,则主表(classes)中的4班那条记录不能被删除)
3. 若要想删除,先将副表中的数据删除再删除主表数据

## 四.数据库的三大设计范式
### 1.第一范式
#### 1NF
-- 数据表中的所有字段都是不可分割的原子值

```sql
create table student2(
    id int primary key,
    name varchar(20),
    address varchar(30)
);

insert into student2 values(1, '张三', '中国广东省罗定市围底镇');
insert into student2 values(2, '李四', '中国广东省罗定市罗平镇');
insert into student2 values(3, '王五', '中国广东省罗定市素龙镇');

-- 只要字段值还可以继续拆分，就不满足第一范式
```
```sql
create table student3(
    id int primary key,
    name varchar(20),
    country varchar(30),
    province varchar(30),
    city varchar(30),
    details varchar(30)
);

insert into student3 values(1, '张三', '中国','广东省','罗定市','围底镇');
insert into student3 values(2, '李四', '中国','广东省','罗定市','罗平镇');
insert into student3 values(3, '王五', '中国','广东省','罗定市','素龙镇');
```
**总结:** 范式设计得越详细，对某些实际操作可能会更好(可以单独对某个字段进行操作,如只选中国,或只选广东省等)，但并非都有好处(如取完整地址时需要一个个拼接)，需要对项目的实际情况进行设定。

### 2.第二范式
#### 2NF
-- 必须在**满足第一范式的前提下**,第二范式要求,除主键外的每一列都必须**完全依赖于主键(依赖就是相关的意思)**.
-- 如果要出现不完全依赖,只可能发生在**联合主键**的情况下.
```sql
-- 订单表
create table myOrder(
    product_id int,
    customer_id int,
    product_name varchar(20),
    customer_name varchar(20),
    primary key(product_id, customer_id)
);
```
实际上，在这张订单表中，`product_name` 只依赖于 `product_id` ，`customer_name` 只依赖于 `customer_id` 。也就是说，`product_name` 和 `customer_id` 是没有关系的，`customer_name` 和 `product_id` 也是没有关系的。

-- 存在问题:除主键外的其他列,只依赖于主键的部分字段
-- 解决:拆表
```sql
create table myOrder(
    order_id int primary key,
    product_id int,
    customer_id int
);

create table product(
    id int primary key,
    ame varchar(20)
);

create table customer(
    id int primary key,
    name varchar(20)
);
```
-- 分成三个表之后,就满足了第二范式的设计,因为`myOrder`表中的`product_id`和`customer_id`完全依赖于主键`order_id`,而`product`和`customer`中的其他字段也完全依赖于主键,满足第二范式的设计.

### 3.第三范式
#### 3NF
-- 必须在**满足第二范式的前提下**,除开主键外的其他列之间不能有传递依赖关系.
```sql
create table myOrder(
    order_id int primary key,
    product_id int,
    customer_id int,
    customer_phone varchar(15)
);
```
-- 存在问题: 在myOrder中,`customer_phone`依赖于`order_id`,但它还依赖于`customer_id`,而`customer_id`又依赖于`order_id`,所以存在传递依赖,不满足第三范式的要求.
-- 解决:
```sql
create table customer(
    id int primary key,
    name varchar(20),
    phone varchar(15)
);
```
-- 修改后就不存在其他列之间的传递依赖关系，其他列都只依赖于主键列，满足了第三范式的设计！