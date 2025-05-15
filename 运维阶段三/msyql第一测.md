1，
破解数据库密码的方法和流程

方法一：删除/usr/lib/mysql/×   下所有文件

方式二：在/etc/my.cnf.d/mysql-server.cnf中，添加skip_grant_tables ，跳过密码验证

登录数据库后，在重设密码。



2.
建库的命令； 建表的命令； 对表内容的增删改查的语句分别是什么？

建库： create databases  db1;

键表： create  table  tb1 (
    id  int  auto_increment  primary  key,
    age       char(5),    
    name   varchar(50), 
    pw        varchar(50)
);

字段：

inset tb1 values('10','dc','123');

delete from tb1 where age='10' ;

select * from tb1;

update tb1 set pw='888' where id =1;





#### 数据库准备

```sql
use tarena;
CREATE TABLE score (
    id INT AUTO_INCREMENT PRIMARY KEY,
    NAME VARCHAR(20) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci,
    COURSE VARCHAR(20) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci,
    SCORE INT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

insert into score(NAME,COURSE,SCORE) values
('张三','语文',81),
('张三','数学',75),
('李四','语文',76),
('李四','数学',80),
('王五','语文',81),
('王五','数学',100),
('王五','英语',90);

DROP TABLE IF EXISTS depart;
CREATE TABLE depart (
    departid INT PRIMARY KEY,
    departname VARCHAR(20)
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
INSERT INTO depart (departid, departname) VALUES
(11001, '客服部'),
(11002, '财务部'),
(11004, '市场部');
CREATE TABLE staff (
    staffid INT PRIMARY KEY,
    staffname VARCHAR(20) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci,
    salary DECIMAL(10, 2),
    departid INT,
    FOREIGN KEY (departid) REFERENCES depart(departid)
);
INSERT INTO staff (staffid, staffname, salary, departid) VALUES
(202, '张一鸣', 3800.00, 11002),
(204, '王春花', 3800.00, 11002),
(208, '张德思', 5800.00, 11004),
(229, '张一鸣', 3780.00, 11004),
(201, '李子仪', 4800.00, 11004),
(228, '张豆豆', 6000.00, 11001),
(206, '张一鸣', 3900.00, 11001);
```





```sql
4.查看上图，用一条sql语句查询出语文课成绩大于80分的学生姓名及分数
SELECT NAME,SCORE from score s WHERE COURSE = '语文' and SCORE > 80 ;

5.用一条sql语句查询出语文课成绩大于80分的学生总人数
SELECT sum(id) as 语文成绩大于80人数 from score s WHERE COURSE = '语文' and SCORE > 80 ;

6.用一条sql语句查询出每门课都大于80分的学生姓名
SELECT NAME from score s  GROUP  BY  NAME 
HAVING MIN(SCORE) > 80;

7.查看上图，统计贵州省的用户数
SELECT sum(NAME) from info where PROVINCE = '贵州';

8.删除江苏省的用户记录
DELETE * from info where PROVINCE = '江苏';

9.把甘肃省的用户名字改为刘备
UPDATE info set NAME='刘备' where PROVINCE = '甘肃';

10.查询出创建时间大于2016年的记录
select * from info where CREATEDATE > '2016-12-31 23:59:59';


SELECT * from staff s left join depart d on s.departid =d.departid 
where salary > 4000 and d.departname = '市场部';


SELECT * from staff s left join depart d on s.departid =d.departid 
where d.departname = '财务部' and staffname LIKE '张%' and salary > 3000;


UPDATE staff s left join depart d on s.departid = d.departid
set s.salary = 4000
where d.departname ='客服部' and staffname = '张一鸣'; 


DELETE  from  staff WHERE staffname = '李子仪'
and salary < 5000 
and departid 
IN ( SELECT departid from depart where departname = '市场部' );


insert into staff(staffid,staffname,salary,departid) values
(211,'张小三',3600,11004);
```













