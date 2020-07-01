## mysqld的四种连接查询
-- 创建数据库
`create database testJoin;`

-- 创建两个表
-- person表
```sql
-- id,
-- name,
-- cardId
create table person(
    id int,
    name varchar(20),
    cardId int
);
```
-- card表
```sql
-- id,
-- name
create table card(
    id int,
    name varchar(20)
);
```

-- 插入数据
```sql
insert into card values(1, '饭卡');
insert into card values(2, '建行卡');
insert into card values(3, '农行卡');
insert into card values(4, '工商卡');
insert into card values(5, '邮政卡');

insert into person values(1, '张三', 1);
insert into person values(2, '李四', 3);
insert into person values(3, '王五', 6);

select * from person;
+------+--------+--------+
| id   | name   | cardId |
+------+--------+--------+
|    1 | 张三   |      1 |
|    2 | 李四   |      3 |
|    3 | 王五   |      6 |
+------+--------+--------+

select * from card;
+------+-----------+
| id   | name      |
+------+-----------+
|    1 | 饭卡      |
|    2 | 建行卡    |
|    3 | 农行卡    |
|    4 | 工商卡    |
|    5 | 邮政卡    |
+------+-----------+

-- 可以看到,两个表之间并没有创建外键
```
### 一. 内连接
#### `inner join`或`join` 查询
内联查询,其实就是两张表中的数据,通过某个字段相等,查询出相关记录数据.
```sql
-- inner join... on ...
select * from person inner join card on person.cardId = card.id;
+------+--------+--------+------+-----------+
| id   | name   | cardId | id   | name      |
+------+--------+--------+------+-----------+
|    1 | 张三   |      1 |    1 | 饭卡      |
|    2 | 李四   |      3 |    3 | 农行卡    |
+------+--------+--------+------+-----------+

-- 或join ... on ...:
select * from person join card on person.cardId = card.id;
```

### 二. 外连接
#### 1. 左连接 `left join`或者`left outer join`
左外连接,会把左边的表里面的所有数据取出来,而右边表中的数据,如果有相等的,就显示出来,如果没有,就会补 NULL
```sql
-- left join ... on ...
select * from person left join card on person.cardId = card.id;
+------+--------+--------+------+-----------+
| id   | name   | cardId | id   | name      |
+------+--------+--------+------+-----------+
|    1 | 张三   |      1 |    1 | 饭卡      |
|    2 | 李四   |      3 |    3 | 农行卡    |
|    3 | 王五   |      6 | NULL | NULL      |
+------+--------+--------+------+-----------+

-- 或left outer join ... on ...:
select * from person left outer join card on person.cardId = card.id;
```
#### 2. 右连接`right join`或者`right outer join`
右外连接,会把右边的表里面的所有数据取出来,而左边表中的数据,如果有相等的,就显示出来,如果没有,就会补 NULL
```sql
-- right join ... on ...
select * from person right join card on person.cardId = card.id;
+------+--------+--------+------+-----------+
| id   | name   | cardId | id   | name      |
+------+--------+--------+------+-----------+
|    1 | 张三   |      1 |    1 | 饭卡      |
|    2 | 李四   |      3 |    3 | 农行卡    |
| NULL | NULL   |   NULL |    2 | 建行卡    |
| NULL | NULL   |   NULL |    4 | 工商卡    |
| NULL | NULL   |   NULL |    5 | 邮政卡    |
+------+--------+--------+------+-----------+

-- right outer join ... on ...:
select * from person right outer join card on person.cardId = card.id;
```

#### 3. 完全外连接`full join`或者`full outer join`
```sql
-- mysql不支持以下语法的full join
select * from person full join card on person.cardId = card.id;
ERROR 1054 (42S22): Unknown column 'person.cardId' in 'on clause'
```
```sql
-- mysql中使用union实现全连接
-- 左连接 union 右连接
select * from person left join card on person.cardId = card.id
union
select * from person right join card on person.cardId = card.id;
+------+--------+--------+------+-----------+
| id   | name   | cardId | id   | name      |
+------+--------+--------+------+-----------+
|    1 | 张三   |      1 |    1 | 饭卡      |
|    2 | 李四   |      3 |    3 | 农行卡    |
|    3 | 王五   |      6 | NULL | NULL      |
| NULL | NULL   |   NULL |    2 | 建行卡    |
| NULL | NULL   |   NULL |    4 | 工商卡    |
| NULL | NULL   |   NULL |    5 | 邮政卡    |
+------+--------+--------+------+-----------+
```
