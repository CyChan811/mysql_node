## mysql查询练习
### 创建数据表
新建数据库:`create database select_test;`
选择数据库:`use select_test;`

-- 学生表
-- Student
-- 学号
-- 姓名
-- 性别
-- 出生年月日
-- 所在班级
```sql
create table student(
    sno varchar(20) primary key,
    sname varchar(20) not NULL,
    sgender varchar(10) not NULL,
    sbirthday datetime,
    class varchar(20)
);
```

-- 教师表
-- Teacher
-- 教师编号
-- 教师名字
-- 教师性别
-- 出生年月日
-- 职称
-- 所在部门
```sql
create table teacher(
    tno varchar(20) primary key,
    tname varchar(20) not NULL,
    tgender varchar(10) not NULL,
    tbirthday varchar(20) not NULL,
    prof varchar(20) not NULL,
    depart varchar(20) not NULL
);
```

-- 课程表
-- Course
-- 课程号
-- 课程名称
-- 教师编号
```sql
create table course(
    cno varchar(20) primary key,
    cname varchar(20) not NULL,
    tno varchar(20) not NULL,
    foreign key(tno) references teacher(tno)
);
```

-- 成绩表
-- Score
-- 学号
-- 课程号
--成绩
```sql
create table score(
    sno varchar(20) not NULL,
    cno varchar(20) not NULL,
    degree decimal,
    foreign key(sno) references student(sno),
    foreign key(cno) references course(cno),
    primary key(sno, cno)
);
```

### 往数据表中添加数据
-- 添加学生信息
```sql
insert into student values('101', '曾华', '男', '1977-09-01', '95033');
insert into student values('102', '匡明', '男', '1975-10-02', '95031');
insert into student values('103', '王丽', '女', '1976-01-23', '95033');
insert into student values('104', '李军', '男', '1976-02-20', '95033');
insert into student values('105', '王芳', '女', '1975-02-10', '95031');
insert into student values('106', '陆君', '男', '1974-06-03', '95031');
insert into student values('107', '王尼玛', '男', '1976-02-20', '95033');
insert into student values('108', '张全蛋', '男', '1975-02-10', '95031');
insert into student values('109', '赵铁柱', '男', '1974-06-03', '95031');
```

--添加教师信息
```sql
insert into teacher values('804', '李诚', '男', '1958-12-02', '副教授', '计算机系');
insert into teacher values('856', '张旭', '男', '1969-03-12', '讲师', '电子工程系');
insert into teacher values('825', '王萍', '女', '1972-05-05', '助教', '计算机系');
insert into teacher values('831', '刘冰', '女', '1977-08-14', '助教', '电子工程系');
```

-- 添加课程表
```sql
insert into course values('3-105', '计算机导论', '825');
insert into course values('3-245', '操作系统', '804');
insert into course values('6-166', '数字电路', '856');
insert into course values('9-888', '高等数学', '831');
```

-- 添加成绩表
```sql
insert into score values('103', '3-105', '92');
insert into score values('103', '6-166', '85');
insert into score values('103', '3-245', '86');
insert into score values('105', '3-105', '88');
insert into score values('105', '3-245', '75');
insert into score values('105', '6-166', '79');
insert into score values('109', '3-105', '76');
insert into score values('109', '3-245', '68');
insert into score values('109', '6-166', '81');
```

### 查询练习
```sql
-- 1.查询student表的所有记录
select * from student;
```
```sql
-- 2.查询student表中的所有记录的sname,sgender和class列
select sname, sgender, class from student;
+-----------+---------+-------+
| sname     | sgender | class |
+-----------+---------+-------+
| 曾华      | 男      | 95033 |
| 匡明      | 男      | 95031 |
| 王丽      | 女      | 95033 |
| 李军      | 男      | 95033 |
| 王芳      | 女      | 95031 |
| 陆君      | 男      | 95031 |
| 王尼玛    | 男      | 95033 |
| 张全蛋    | 男      | 95031 |
| 赵铁柱    | 男      | 95031 |
+-----------+---------+-------+
```
```sql
-- 3.查询教师所有的单位即不重复的depart列
-- distinct:有区别的,不同种类的  用来排除重复的
select distinct depart from teacher;
+-----------------+
| depart          |
+-----------------+
| 计算机系         |
| 电子工程系        |
+-----------------+
```
```sql
-- 4.查询score表中成绩在60到80之间的所有记录
-- 注意:betweent ... and ... 是包含边界值的
select * from score where degree between 60 and 80;
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 105 | 3-245 |     75 |
| 105 | 6-166 |     79 |
| 109 | 3-105 |     76 |
| 109 | 3-245 |     68 |
+-----+-------+--------+

-- 或:直接使用运算符比较
select * from score where degree > 60 and degree < 80;
```
```sql
-- 5.查询score表中成绩为85,86和88的记录
-- 表示或者关系的查询 in
select * from score where degree in(85, 86, 88);
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 103 | 3-245 |     86 |
| 103 | 6-166 |     85 |
| 105 | 3-105 |     88 |
+-----+-------+--------+
```
```sql
-- 6.查询student表中"95031"班或性别为"女"的同学记录
-- or 表示或者
select * from student where class = '95031' or sgender = '女';
+-----+-----------+---------+---------------------+-------+
| sno | sname     | sgender | sbirthday           | class |
+-----+-----------+---------+---------------------+-------+
| 102 | 匡明      | 男      | 1975-10-02 00:00:00 | 95031 |
| 103 | 王丽      | 女      | 1976-01-23 00:00:00 | 95033 |
| 105 | 王芳      | 女      | 1975-02-10 00:00:00 | 95031 |
| 106 | 陆君      | 男      | 1974-06-03 00:00:00 | 95031 |
| 108 | 张全蛋    | 男      | 1975-02-10 00:00:00 | 95031 |
| 109 | 赵铁柱    | 男      | 1974-06-03 00:00:00 | 95031 |
+-----+-----------+---------+---------------------+-------+
```
```sql
-- 7.以class降序查询student表的所有记录
-- 降序: desc
select * from student order by class desc;
+-----+-----------+---------+---------------------+-------+
| sno | sname     | sgender | sbirthday           | class |
+-----+-----------+---------+---------------------+-------+
| 101 | 曾华      | 男      | 1977-09-01 00:00:00 | 95033 |
| 103 | 王丽      | 女      | 1976-01-23 00:00:00 | 95033 |
| 104 | 李军      | 男      | 1976-02-20 00:00:00 | 95033 |
| 107 | 王尼玛    | 男      | 1976-02-20 00:00:00 | 95033 |
| 102 | 匡明      | 男      | 1975-10-02 00:00:00 | 95031 |
| 105 | 王芳      | 女      | 1975-02-10 00:00:00 | 95031 |
| 106 | 陆君      | 男      | 1974-06-03 00:00:00 | 95031 |
| 108 | 张全蛋    | 男      | 1975-02-10 00:00:00 | 95031 |
| 109 | 赵铁柱    | 男      | 1974-06-03 00:00:00 | 95031 |
+-----+-----------+---------+---------------------+-------+

-- 升序: asc 默认是升序排列的,所以一般情况下不写asc;
select * from student order by class;
+-----+-----------+---------+---------------------+-------+
| sno | sname     | sgender | sbirthday           | class |
+-----+-----------+---------+---------------------+-------+
| 102 | 匡明      | 男      | 1975-10-02 00:00:00 | 95031 |
| 105 | 王芳      | 女      | 1975-02-10 00:00:00 | 95031 |
| 106 | 陆君      | 男      | 1974-06-03 00:00:00 | 95031 |
| 108 | 张全蛋    | 男      | 1975-02-10 00:00:00 | 95031 |
| 109 | 赵铁柱    | 男      | 1974-06-03 00:00:00 | 95031 |
| 101 | 曾华      | 男      | 1977-09-01 00:00:00 | 95033 |
| 103 | 王丽      | 女      | 1976-01-23 00:00:00 | 95033 |
| 104 | 李军      | 男      | 1976-02-20 00:00:00 | 95033 |
| 107 | 王尼玛    | 男      | 1976-02-20 00:00:00 | 95033 |
+-----+-----------+---------+---------------------+-------+
```
```sql
-- 8.以cno升序,degree降序查询score表的所有记录
-- 先cno升序,重复的情况下再degree降序
select * from score order by cno asc, degree desc;
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 103 | 3-105 |     92 |
| 105 | 3-105 |     88 |
| 109 | 3-105 |     76 |
| 103 | 3-245 |     86 |
| 105 | 3-245 |     75 |
| 109 | 3-245 |     68 |
| 103 | 6-166 |     85 |
| 109 | 6-166 |     81 |
| 105 | 6-166 |     79 |
+-----+-------+--------+
```
```sql
-- 9.查询"95031"班的学生人数
-- 统计 count
select count(*) from student where class = '95031';
+----------+
| count(*) |
+----------+
|        5 |
+----------+
```
```sql
-- 10.查询score表中的最高分的学生学号和课程号.(子查询或者排序)
select sno, cno from score where degree = (select max(degree) from score);
+-----+-------+
| sno | cno   |
+-----+-------+
| 103 | 3-105 |
+-----+-------+

-- 子查询方式:
-- 1. 找到最高分
select max(degree) from score;
--2. 找最高分的 sno 和 cno
select sno, cno from score where degree = (select max(degree) from score);

-- 排序方式:(最高分有多个的情况下可能有数据问题)
select sno, cno, degree from score order by degree desc;
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 103 | 3-105 |     92 |
| 105 | 3-105 |     88 |
| 103 | 3-245 |     86 |
| 103 | 6-166 |     85 |
| 109 | 6-166 |     81 |
| 105 | 6-166 |     79 |
| 109 | 3-105 |     76 |
| 105 | 3-245 |     75 |
| 109 | 3-245 |     68 |
+-----+-------+--------+

-- limit 第一个数字表示从多少开始  第二个数字表示查多少条数据
select sno, cno, degree from score order by degree desc limit 0, 1;
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 103 | 3-105 |     92 |
+-----+-------+--------+
```
```sql
-- 11.查询每门课的平均成绩
-- avg()
select avg(degree) from score where cno = '3-105';
+-------------+
| avg(degree) |
+-------------+
|     85.3333 |
+-------------+

-- 我能不能在一个 sql 语句中写呢?而不是一条一条地写
-- group by 分组
select cno, avg(degree) from score group by cno;
+-------+-------------+
| cno   | avg(degree) |
+-------+-------------+
| 3-105 |     85.3333 |
| 3-245 |     76.3333 |
| 6-166 |     81.6667 |
+-------+-------------+
```
```sql
-- 12.查询score表中至少有2名学生选修的并以3开头的课程的平均分数
select cno from score group by cno;
+-------+
| cno   |
+-------+
| 3-105 |
| 3-245 |
| 6-166 |
+-------+

-- having 表示特有
select cno from score group by cno
having count(cno) >= 2;
+-------+
| cno   |
+-------+
| 3-105 |
| 3-245 |
| 6-166 |
+-------+

-- like 模糊查询,'%'是一个通配符,匹配'3'后面的任意字符
select cno from score group by cno
having count(cno) >= 2 
and cno like '3%';
+-------+
| cno   |
+-------+
| 3-105 |
| 3-245 |
+-------+

select cno, avg(degree) from score group by cno
having count(cno) >= 2 and cno like '3%';
+-------+-------------+
| cno   | avg(degree) |
+-------+-------------+
| 3-105 |     85.3333 |
| 3-245 |     76.3333 |
+-------+-------------+
```
```sql
-- 13.查询分数大于70,小于90的sno列
select sno, degree from score where degree > 70 and degree < 90;
+-----+--------+
| sno | degree |
+-----+--------+
| 103 |     86 |
| 103 |     85 |
| 105 |     88 |
| 105 |     75 |
| 105 |     79 |
| 109 |     76 |
| 109 |     81 |
+-----+--------+

-- 再次提醒,between ... and ... 包含了边界值
select sno, degree from score where degree between 71 and 89;
```
**多表查询1:**
```sql
-- 14.查询所有学生的sname, cno 和 degree列
-- 先分别查询
select sno, sname from student;
+-----+-----------+
| sno | sname     |
+-----+-----------+
| 101 | 曾华      |
| 102 | 匡明      |
| 103 | 王丽      |
| 104 | 李军      |
| 105 | 王芳      |
| 106 | 陆君      |
| 107 | 王尼玛    |
| 108 | 张全蛋    |
| 109 | 赵铁柱    |
+-----+-----------+

select sno, cno, degree from score;
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 103 | 3-105 |     92 |
| 103 | 3-245 |     86 |
| 103 | 6-166 |     85 |
| 105 | 3-105 |     88 |
| 105 | 3-245 |     75 |
| 105 | 6-166 |     79 |
| 109 | 3-105 |     76 |
| 109 | 3-245 |     68 |
| 109 | 6-166 |     81 |
+-----+-------+--------+

-- 多表查询
select sname, cno, degree from student, score where student.sno = score.sno;
+-----------+-------+--------+
| sname     | cno   | degree |
+-----------+-------+--------+
| 王丽      | 3-105 |     92 |
| 王丽      | 3-245 |     86 |
| 王丽      | 6-166 |     85 |
| 王芳      | 3-105 |     88 |
| 王芳      | 3-245 |     75 |
| 王芳      | 6-166 |     79 |
| 赵铁柱    | 3-105 |     76 |
| 赵铁柱    | 3-245 |     68 |
| 赵铁柱    | 6-166 |     81 |
+-----------+-------+--------+
```
**多表查询2:**
```sql
-- 15.查询所有学生的sno,cname和degree列
select sno, cname, degree from course, score where course.cno = score.cno;
+-----+-----------------+--------+
| sno | cname           | degree |
+-----+-----------------+--------+
| 103 | 计算机导论      |     92 |
| 103 | 操作系统        |     86 |
| 103 | 数字电路        |     85 |
| 105 | 计算机导论      |     88 |
| 105 | 操作系统        |     75 |
| 105 | 数字电路        |     79 |
| 109 | 计算机导论      |     76 |
| 109 | 操作系统        |     68 |
| 109 | 数字电路        |     81 |
+-----+-----------------+--------+
```
**三表关联查询:**
```sql
-- 16.查询所有学生的sname, cname和degree列
-- sname->student
-- cname->course
-- degree->score
select sname, cname, degree from student, course, score where student.sno = score.sno 
and course.cno = score.cno;
+-----------+-----------------+--------+
| sname     | cname           | degree |
+-----------+-----------------+--------+
| 王丽      | 计算机导论      |     92 |
| 王丽      | 操作系统        |     86 |
| 王丽      | 数字电路        |     85 |
| 王芳      | 计算机导论      |     88 |
| 王芳      | 操作系统        |     75 |
| 王芳      | 数字电路        |     79 |
| 赵铁柱    | 计算机导论      |     76 |
| 赵铁柱    | 操作系统        |     68 |
| 赵铁柱    | 数字电路        |     81 |
+-----------+-----------------+--------+

-- 有歧义的字段要加作用域
select sname, cname, degree, student.sno, course.cno 
from student, course, score 
where student.sno = score.sno 
and course.cno = score.cno;
+-----------+-----------------+--------+-----+-------+
| sname     | cname           | degree | sno | cno   |
+-----------+-----------------+--------+-----+-------+
| 王丽      | 计算机导论      |     92 | 103 | 3-105 |
| 王丽      | 操作系统        |     86 | 103 | 3-245 |
| 王丽      | 数字电路        |     85 | 103 | 6-166 |
| 王芳      | 计算机导论      |     88 | 105 | 3-105 |
| 王芳      | 操作系统        |     75 | 105 | 3-245 |
| 王芳      | 数字电路        |     79 | 105 | 6-166 |
| 赵铁柱    | 计算机导论      |     76 | 109 | 3-105 |
| 赵铁柱    | 操作系统        |     68 | 109 | 3-245 |
| 赵铁柱    | 数字电路        |     81 | 109 | 6-166 |
+-----------+-----------------+--------+-----+-------+

-- 为sno和cno取别名
select sname, cname, degree, student.sno as stu_sno, course.cno as cou_no
from student, course, score 
where student.sno = score.sno 
and course.cno = score.cno;
+-----------+-----------------+--------+---------+--------+
| sname     | cname           | degree | stu_sno | cou_no |
+-----------+-----------------+--------+---------+--------+
| 王丽      | 计算机导论      |     92 | 103     | 3-105  |
| 王丽      | 操作系统        |     86 | 103     | 3-245  |
| 王丽      | 数字电路        |     85 | 103     | 6-166  |
| 王芳      | 计算机导论      |     88 | 105     | 3-105  |
| 王芳      | 操作系统        |     75 | 105     | 3-245  |
| 王芳      | 数字电路        |     79 | 105     | 6-166  |
| 赵铁柱    | 计算机导论      |     76 | 109     | 3-105  |
| 赵铁柱    | 操作系统        |     68 | 109     | 3-245  |
| 赵铁柱    | 数字电路        |     81 | 109     | 6-166  |
+-----------+-----------------+--------+---------+--------+
```
**子查询 加 分组:**
```sql
-- 17.查询'95031'班学生每门课的平均分
select * from student where class = '95031';
+-----+-----------+---------+---------------------+-------+
| sno | sname     | sgender | sbirthday           | class |
+-----+-----------+---------+---------------------+-------+
| 102 | 匡明      | 男      | 1975-10-02 00:00:00 | 95031 |
| 105 | 王芳      | 女      | 1975-02-10 00:00:00 | 95031 |
| 106 | 陆君      | 男      | 1974-06-03 00:00:00 | 95031 |
| 108 | 张全蛋    | 男      | 1975-02-10 00:00:00 | 95031 |
| 109 | 赵铁柱    | 男      | 1974-06-03 00:00:00 | 95031 |
+-----+-----------+---------+---------------------+-------+

-- 子查询
-- 用到了in表示或者关系的条件
select * from score where sno in(select sno from student where class = '95031');
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 105 | 3-105 |     88 |
| 105 | 3-245 |     75 |
| 105 | 6-166 |     79 |
| 109 | 3-105 |     76 |
| 109 | 3-245 |     68 |
| 109 | 6-166 |     81 |
+-----+-------+--------+

-- 分组求平均分
select cno, avg(degree) from score where sno in(select sno from student where class = '95031') group by cno;
+-------+-------------+
| cno   | avg(degree) |
+-------+-------------+
| 3-105 |     82.0000 |
| 3-245 |     71.5000 |
| 6-166 |     80.0000 |
+-------+-------------+
```
```sql
-- 18.查询选修'3-105'课程的成绩高于'109'号同学'3-105'成绩的所有同学的记录
select degree from score where sno = '109' and cno = '3-105';
+--------+
| degree |
+--------+
|     76 |
+--------+

select * from score where degree > (select degree from score where sno = '109' and cno = '3-105') and cno = '3-105';
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 103 | 3-105 |     92 |
| 105 | 3-105 |     88 |
+-----+-------+--------+
```
```sql
-- 19.查询成绩高于学号为'109',课程号为'3-105'的成绩的所有记录
select * from score where degree > (select degree from score where sno = '109' and cno = '3-105');
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 103 | 3-105 |     92 |
| 103 | 3-245 |     86 |
| 103 | 6-166 |     85 |
| 105 | 3-105 |     88 |
| 105 | 6-166 |     79 |
| 109 | 6-166 |     81 |
+-----+-------+--------+
```
```sql
-- 20.查询和学号为108,101的同学同年出生的所有同学的sno,sname和sbirthday列
-- year() 提取年份
select year(sbirthday) from student where sno in (108, 101);
+-----------------+
| year(sbirthday) |
+-----------------+
|            1977 |
|            1975 |
+-----------------+

select * from student where year(sbirthday) in(select year(sbirthday) from student where sno in (108, 101));
+-----+-----------+---------+---------------------+-------+
| sno | sname     | sgender | sbirthday           | class |
+-----+-----------+---------+---------------------+-------+
| 101 | 曾华      | 男      | 1977-09-01 00:00:00 | 95033 |
| 102 | 匡明      | 男      | 1975-10-02 00:00:00 | 95031 |
| 105 | 王芳      | 女      | 1975-02-10 00:00:00 | 95031 |
| 108 | 张全蛋    | 男      | 1975-02-10 00:00:00 | 95031 |
+-----+-----------+---------+---------------------+-------+
```
```sql
-- 21.查询"张旭"教师任课的学生成绩
-- 嵌套子查询
select tno from teacher where tname = "张旭";

select cno from course where tno = (select tno from teacher where tname = "张旭");

select * from score where cno = (select cno from course where tno = (select tno from teacher where tname = "张旭"));
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 103 | 6-166 |     85 |
| 105 | 6-166 |     79 |
| 109 | 6-166 |     81 |
+-----+-------+--------+
```
```sql
-- 22.查询选修某课程的同学人数多于5人的教师姓名
-- 一开始的时候没有符合条件的数据,所以添加一些数据先
insert into score values('101', '3-105', '90');
insert into score values('102', '3-105', '91');
insert into score values('104', '3-105', '89');

-- 先查找学生人数大于5的课程号
select cno from score group by cno having count(*) > 5;
+-------+
| cno   |
+-------+
| 3-105 |
+-------+

-- 再找到教这些课程的教师号
-- 注意用"in"更合适,因为可能不止一个老师符合条件
select tno from course where cno in(select cno from score group by cno having count(*) > 5);
+-----+
| tno |
+-----+
| 825 |
+-----+

-- 最后找到该教师号对应的教师名字
select tname from teacher where tno in(select tno from course where cno in(select cno from score group by cno having count(*) > 5));
+--------+
| tname  |
+--------+
| 王萍   |
+--------+
```
```sql
-- 23.查询95033班和95031班全体学生的记录.
-- 为了效果,增加一些数据
insert into student values('110', '张飞', '男', '1974-06-03', '95038');

select * from student where class in('95031', '95033');
+-----+-----------+---------+---------------------+-------+
| sno | sname     | sgender | sbirthday           | class |
+-----+-----------+---------+---------------------+-------+
| 101 | 曾华      | 男      | 1977-09-01 00:00:00 | 95033 |
| 102 | 匡明      | 男      | 1975-10-02 00:00:00 | 95031 |
| 103 | 王丽      | 女      | 1976-01-23 00:00:00 | 95033 |
| 104 | 李军      | 男      | 1976-02-20 00:00:00 | 95033 |
| 105 | 王芳      | 女      | 1975-02-10 00:00:00 | 95031 |
| 106 | 陆君      | 男      | 1974-06-03 00:00:00 | 95031 |
| 107 | 王尼玛    | 男      | 1976-02-20 00:00:00 | 95033 |
| 108 | 张全蛋    | 男      | 1975-02-10 00:00:00 | 95031 |
| 109 | 赵铁柱    | 男      | 1974-06-03 00:00:00 | 95031 |
+-----+-----------+---------+---------------------+-------+
```
```sql
-- 24.查询存在有85分以上成绩的课程cno
select cno, degree from score where degree > 85;
```
```sql
-- 25.查询出"计算机系"教师所教课程的成绩表
-- 先找出这个系老师的tno
select * from teacher where depart = '计算机系';
+-----+--------+---------+------------+-----------+--------------+
| tno | tname  | tgender | tbirthday  | prof      | depart       |
+-----+--------+---------+------------+-----------+--------------+
| 804 | 李诚   | 男      | 1958-12-02 | 副教授    | 计算机系     |
| 825 | 王萍   | 女      | 1972-05-05 | 助教      | 计算机系     |
+-----+--------+---------+------------+-----------+--------------+

-- 查出所教课程
select * from course where tno in(select tno from teacher where depart = '计算机系');
+-------+-----------------+-----+
| cno   | cname           | tno |
+-------+-----------------+-----+
| 3-245 | 操作系统        | 804 |
| 3-105 | 计算机导论      | 825 |
+-------+-----------------+-----+

-- 最后查出成绩表
select * from score where cno in(select cno from course where tno in(select tno from teacher where depart = '计算机系'));
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 103 | 3-245 |     86 |
| 105 | 3-245 |     75 |
| 109 | 3-245 |     68 |
| 101 | 3-105 |     90 |
| 102 | 3-105 |     91 |
| 103 | 3-105 |     92 |
| 104 | 3-105 |     89 |
| 105 | 3-105 |     88 |
| 109 | 3-105 |     76 |
+-----+-------+--------+
```
```sql
-- 26.查询"计算机系"与"电子工程系"不同职称的教师的tname和prof
select * from teacher where depart = '计算机系' and prof not in(select prof from teacher where depart = '电子工程系');
+-----+--------+---------+------------+-----------+--------------+
| tno | tname  | tgender | tbirthday  | prof      | depart       |
+-----+--------+---------+------------+-----------+--------------+
| 804 | 李诚   | 男      | 1958-12-02 | 副教授    | 计算机系     |
+-----+--------+---------+------------+-----------+--------------+

select * from teacher where depart = '电子工程系' and prof not in(select prof from teacher where depart = '计算机系');
+-----+--------+---------+------------+--------+-----------------+
| tno | tname  | tgender | tbirthday  | prof   | depart          |
+-----+--------+---------+------------+--------+-----------------+
| 856 | 张旭   | 男      | 1969-03-12 | 讲师   | 电子工程系      |
+-----+--------+---------+------------+--------+-----------------+

-- union 联合 求并集
select * from teacher where depart = '计算机系' and prof not in(select prof from teacher where depart = '电子工程系')
union
select * from teacher where depart = '电子工程系' and prof not in(select prof from teacher where depart = '计算机系');
+-----+--------+---------+------------+-----------+-----------------+
| tno | tname  | tgender | tbirthday  | prof      | depart          |
+-----+--------+---------+------------+-----------+-----------------+
| 804 | 李诚   | 男      | 1958-12-02 | 副教授    | 计算机系        |
| 856 | 张旭   | 男      | 1969-03-12 | 讲师      | 电子工程系      |
+-----+--------+---------+------------+-----------+-----------------+
```
```sql
-- 27.查询选修编号为"3-105"课程且成绩至少高于选修编号为"3-245"的同学的cno,sno和degree
-- 并按degree从高到低次序排序
select * from score where cno = '3-245';
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 103 | 3-245 |     86 |
| 105 | 3-245 |     75 |
| 109 | 3-245 |     68 |
+-----+-------+--------+

select * from score where cno = '3-105';
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 101 | 3-105 |     90 |
| 102 | 3-105 |     91 |
| 103 | 3-105 |     92 |
| 104 | 3-105 |     89 |
| 105 | 3-105 |     88 |
| 109 | 3-105 |     76 |
+-----+-------+--------+

-- 至少,即大于其中任意一个,用any
-- any 符合sql语句任意一个条件即可
select * from score 
where cno = '3-105' 
and degree > any(select degree from score where cno = '3-245')
order by degree desc;
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 103 | 3-105 |     92 |
| 102 | 3-105 |     91 |
| 101 | 3-105 |     90 |
| 104 | 3-105 |     89 |
| 105 | 3-105 |     88 |
| 109 | 3-105 |     76 |
+-----+-------+--------+
```
```sql
-- 28.查询选修编号为"3-105"且成绩高于选修编号为"3-245"课程的同学的cno,sno和degree
-- all 需要负荷sql语句的所有条件
select * from score 
where cno = '3-105' 
and degree > all(select degree from score where cno = '3-245');
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 101 | 3-105 |     90 |
| 102 | 3-105 |     91 |
| 103 | 3-105 |     92 |
| 104 | 3-105 |     89 |
| 105 | 3-105 |     88 |
+-----+-------+--------+
```
```sql
-- 29.查询所有教师和同学的name,gender和birthday
-- 别名 as
select tname as name, tgender as gender, tbirthday as birthday from teacher 
union 
select sname, sgender, sbirthday from student;
+-----------+--------+---------------------+
| name      | gender | birthday            |
+-----------+--------+---------------------+
| 李诚      | 男     | 1958-12-02          |
| 王萍      | 女     | 1972-05-05          |
| 刘冰      | 女     | 1977-08-14          |
| 张旭      | 男     | 1969-03-12          |
| 曾华      | 男     | 1977-09-01 00:00:00 |
| 匡明      | 男     | 1975-10-02 00:00:00 |
| 王丽      | 女     | 1976-01-23 00:00:00 |
| 李军      | 男     | 1976-02-20 00:00:00 |
| 王芳      | 女     | 1975-02-10 00:00:00 |
| 陆君      | 男     | 1974-06-03 00:00:00 |
| 王尼玛    | 男     | 1976-02-20 00:00:00 |
| 张全蛋    | 男     | 1975-02-10 00:00:00 |
| 赵铁柱    | 男     | 1974-06-03 00:00:00 |
| 张飞      | 男     | 1974-06-03 00:00:00 |
+-----------+--------+---------------------+
```
```sql
-- 30.查询所有"女"教师和"女"同学的name,gender和birthday
select tname as name, tgender as gender, tbirthday as birthday from teacher where tgender = '女' 
union 
select sname, sgender, sbirthday from student where sgender = '女';
+--------+--------+---------------------+
| name   | gender | birthday            |
+--------+--------+---------------------+
| 王萍   | 女     | 1972-05-05          |
| 刘冰   | 女     | 1977-08-14          |
| 王丽   | 女     | 1976-01-23 00:00:00 |
| 王芳   | 女     | 1975-02-10 00:00:00 |
+--------+--------+---------------------+
```
**复制表数据做条件查询:**
```sql
-- 31.查询成绩比该课程平均成绩低的同学的成绩表
select cno, avg(degree) from score group by cno;
+-------+-------------+
| cno   | avg(degree) |
+-------+-------------+
| 3-105 |     87.6667 |
| 3-245 |     76.3333 |
| 6-166 |     81.6667 |
+-------+-------------+

-- 注意:这里的score a 的意思和score as a一样
select * from score a where degree < (select avg(degree) from score b where a.cno = b.cno);
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 105 | 3-245 |     75 |
| 105 | 6-166 |     79 |
| 109 | 3-105 |     76 |
| 109 | 3-245 |     68 |
| 109 | 6-166 |     81 |
+-----+-------+--------+
```
```sql
-- 32.查询所有任课老师的tname和depart
-- 课程表中安排了课程的教师
select * from course;
+-------+-----------------+-----+
| cno   | cname           | tno |
+-------+-----------------+-----+
| 3-105 | 计算机导论      | 825 |
| 3-245 | 操作系统        | 804 |
| 6-166 | 数字电路        | 856 |
| 9-888 | 高等数学        | 831 |
+-------+-----------------+-----+

-- 找出这些老师的tname和depart
select tname, depart from teacher where tno in(select tno from course);
+--------+-----------------+
| tname  | depart          |
+--------+-----------------+
| 李诚   | 计算机系        |
| 王萍   | 计算机系        |
| 刘冰   | 电子工程系      |
| 张旭   | 电子工程系      |
+--------+-----------------+
```
**条件加分组筛选:**
```sql
-- 33.查询至少有2名男生的班号
select class from student where sgender = '男' group by class having count(*) > 1;
+-------+
| class |
+-------+
| 95031 |
| 95033 |
+-------+
```
```sql
-- 34.查询student表中不姓"王"的同学记录
-- not like 模糊查询取反
select * from student where sname not like '王%';
+-----+-----------+---------+---------------------+-------+
| sno | sname     | sgender | sbirthday           | class |
+-----+-----------+---------+---------------------+-------+
| 101 | 曾华      | 男      | 1977-09-01 00:00:00 | 95033 |
| 102 | 匡明      | 男      | 1975-10-02 00:00:00 | 95031 |
| 104 | 李军      | 男      | 1976-02-20 00:00:00 | 95033 |
| 106 | 陆君      | 男      | 1974-06-03 00:00:00 | 95031 |
| 108 | 张全蛋    | 男      | 1975-02-10 00:00:00 | 95031 |
| 109 | 赵铁柱    | 男      | 1974-06-03 00:00:00 | 95031 |
| 110 | 张飞      | 男      | 1974-06-03 00:00:00 | 95038 |
+-----+-----------+---------+---------------------+-------+
```
```sql
-- 35.查询student表中每个学生的姓名和年龄
-- 年龄:当前年份 - 出生年份
-- 当前年份:
select year(now());

-- 出生年份
select year(sbirthday) from student;

select sname, year(now()) - year(sbirthday) as '年龄' from student;
+-----------+--------+
| sname     | 年龄   |
+-----------+--------+
| 曾华      |     43 |
| 匡明      |     45 |
| 王丽      |     44 |
| 李军      |     44 |
| 王芳      |     45 |
| 陆君      |     46 |
| 王尼玛    |     44 |
| 张全蛋    |     45 |
| 赵铁柱    |     46 |
| 张飞      |     46 |
+-----------+--------+
```
```sql
-- 36.查询student表中最大和最小的sbirthday日期值
select sbirthday from student order by sbirthday;

-- max min
select max(sbirthday) as '最大', min(sbirthday) as '最小' from student; 
+---------------------+---------------------+
| 最大                | 最小                |
+---------------------+---------------------+
| 1977-09-01 00:00:00 | 1974-06-03 00:00:00 |
+---------------------+---------------------+
```
```sql
-- 37.以班号和年龄从大到小的顺序查询student表中的全部记录
select * from student order by class desc, sbirthday;
+-----+-----------+---------+---------------------+-------+
| sno | sname     | sgender | sbirthday           | class |
+-----+-----------+---------+---------------------+-------+
| 110 | 张飞      | 男      | 1974-06-03 00:00:00 | 95038 |
| 103 | 王丽      | 女      | 1976-01-23 00:00:00 | 95033 |
| 104 | 李军      | 男      | 1976-02-20 00:00:00 | 95033 |
| 107 | 王尼玛    | 男      | 1976-02-20 00:00:00 | 95033 |
| 101 | 曾华      | 男      | 1977-09-01 00:00:00 | 95033 |
| 106 | 陆君      | 男      | 1974-06-03 00:00:00 | 95031 |
| 109 | 赵铁柱    | 男      | 1974-06-03 00:00:00 | 95031 |
| 105 | 王芳      | 女      | 1975-02-10 00:00:00 | 95031 |
| 108 | 张全蛋    | 男      | 1975-02-10 00:00:00 | 95031 |
| 102 | 匡明      | 男      | 1975-10-02 00:00:00 | 95031 |
+-----+-----------+---------+---------------------+-------+
```
```sql
-- 38.查询'男'教师及其所上的课程
select * from teacher where tgender = '男';
+-----+--------+---------+------------+-----------+-----------------+
| tno | tname  | tgender | tbirthday  | prof      | depart          |
+-----+--------+---------+------------+-----------+-----------------+
| 804 | 李诚   | 男      | 1958-12-02 | 副教授    | 计算机系        |
| 856 | 张旭   | 男      | 1969-03-12 | 讲师      | 电子工程系      |
+-----+--------+---------+------------+-----------+-----------------+

select * from course where tno in(select tno from teacher where tgender = '男');
+-------+--------------+-----+
| cno   | cname        | tno |
+-------+--------------+-----+
| 3-245 | 操作系统     | 804 |
| 6-166 | 数字电路     | 856 |
+-------+--------------+-----+

-- 加上教师姓名
select tname, tgender,cname from course, teacher where course.tno = teacher.tno and tgender = '男';
+--------+---------+--------------+
| tname  | tgender | cname        |
+--------+---------+--------------+
| 李诚   | 男      | 操作系统     |
| 张旭   | 男      | 数字电路     |
+--------+---------+--------------+
```
```sql
- 39.查询最高分同学的sno,cno和degree列
select * from score where degree = (select max(degree) from score);
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 103 | 3-105 |     92 |
+-----+-------+--------+
```
```sql
-- 40.查询和"李军"同性别的所有同学的sname
select sname from student where sgender = (select sgender from student where sname = '李军');
+-----------+
| sname     |
+-----------+
| 曾华      |
| 匡明      |
| 李军      |
| 陆君      |
| 王尼玛    |
| 张全蛋    |
| 赵铁柱    |
| 张飞      |
+-----------+
```
```sql
-- 41.查询和"李军"同性别并同班的同学sname
select sname from student 
where sgender = (select sgender from student where sname = '李军') 
and class = (select class from student where sname = '李军');
+-----------+
| sname     |
+-----------+
| 曾华      |
| 李军      |
| 王尼玛    |
+-----------+
```
```sql
-- 42.查询所有选修"计算机导论"课程的"男"同学的成绩表
select * from score 
where cno = (select cno from course where cname = '计算机导论') 
and sno in (select sno from student where sgender = '男');
+-----+-------+--------+
| sno | cno   | degree |
+-----+-------+--------+
| 101 | 3-105 |     90 |
| 102 | 3-105 |     91 |
| 104 | 3-105 |     89 |
| 109 | 3-105 |     76 |
+-----+-------+--------+
```
```sql
-- 43.假设使用如下命令建立类一个grade表
create table grade(
    low int(3),
    upp int(3),
    grade char(1)
);

insert into grade values(90, 100, 'A');
insert into grade values(80, 89, 'B');
insert into grade values(70, 79, 'C');
insert into grade values(60, 69, 'D');
insert into grade values(0, 59, 'E');

-- 现查询所有同学的sno,cno和grade列
select sno, cno, degree, grade from score, grade where degree between low and upp;
+-----+-------+--------+-------+
| sno | cno   | degree | grade |
+-----+-------+--------+-------+
| 101 | 3-105 |     90 | A     |
| 102 | 3-105 |     91 | A     |
| 103 | 3-105 |     92 | A     |
| 103 | 3-245 |     86 | B     |
| 103 | 6-166 |     85 | B     |
| 104 | 3-105 |     89 | B     |
| 105 | 3-105 |     88 | B     |
| 105 | 3-245 |     75 | C     |
| 105 | 6-166 |     79 | C     |
| 109 | 3-105 |     76 | C     |
| 109 | 3-245 |     68 | D     |
| 109 | 6-166 |     81 | B     |
+-----+-------+--------+-------+
```