1 把/etc/passwd文件的内容保存到testdb库的user表里,需要自己创建testdb数据库和user表，导入文件到数据库的操作为：load data infile "/var/lib/mysql/myfiles/passwd" into table user fields terminated by ":" lines terminated by "\n";

```sql
cp /etc/passwd  /myload/passwd
create database testdb;
create table testdb.user(
username varchar(30),
password char(1),
uid int,
gin int,
comment varchar(200),
homedir varchar(50),
shell varchar(30)
);
load data infile "/myload/passwd" into table user fields terminated by ":" lines terminated by "\n";
```



2 复制user表的所有记录到teadb库的teacher表里（复制表）

```sql
create table teacher select * from user;
```

3 只复制user表的表结构给新teadb库的tea2表 (快速建表)    

```sql
create table tea2  like user;
```

4 查看teadb库的tea2表的表结构，并删除 id 字段    

对teadb库的teacher表执行如下操作：

```sql
desc teadb.tea2;
alter table teadb.tea2 drop id;
```

对teadb库的teacher表执行如下操作：

5.把username字段设置为index索引

```sql
alter table tea2 add index in_name (username);
```

6.在username字段下方添加s_year字段 存放出生年份 默认值是1990

```sql
alter table tea2 add s_year year default 1990 after username;
```

7.在username字段下方添加字段名sex字段值只能是 girl 或 boy 默认值是 boy

```sql
alter table tea2 add sex enum('girl','boy') default 'boy' after username; 
```

8在sex字段下方添加 age 字段  存放年龄 不允许输入负数。默认值 是 21

```sql
alter table tea2 add age int unsigned default 21 after sex;
```

9把id字段值是1到10之间的用户的性别修改为 girl

```sql
update tea2 set sex='girl' where id BETWEEN 1 and 10 ;
```

10统计性别是girl的用户有多少个。

```sql
SELECT count(*) from tea2 where sex='girl';
```

11查看性别是girl用户里 最大的uid号 是什么。

```sql
SELECT MAX(uid) from tea2 where sex='girl';  
```

12添加一条新记录只给username、uid 字段赋值 值为rtestd  1000

```sql
insert into tea2(username,id) values('rtestd',100);
```

13显示 uid 是四位数的用户的用户名和uid值。

```sql
SELECT username,uid from tea2 where uid >= 1000;
```

14显示名字是以字母 r 开头 且是以字母d结尾的用户名和uid。

```sql
SELECT username,uid from teacher where username like 'r%d';
```

15查看是否有名字以字母 a 开头 并且是以字母 c 结尾的用户。

```sql
SELECT * from teacher where username like 'a%c';
```

16把 gid 在100到500间用户的家目录修改为/root

```sql
update teacher set homedir='/root' where gid BETWEEN 100 and 500; 
```

 17把用户是 root 、 bin 、  sync 用户的shell 修改为  /sbin/nologin

```sql
update teacher set shell='/sbin/nologin' where username in ('root','bin','sync');
```

 18查看  gid 小于10的用户 都使用哪些shell

```sql
SELECT shell from teacher   where gid < 10;
```

19删除  名字以字母d开头的用户。

```sql
delete  from teacher where username like 'd%';
```

20查询  gid 最大的前5个用户 使用的 shell

```sql
SELECT shell from teacher order by gid limit 5;
```

21查看哪些用户没有家目录

```sql
SELECT * from teacher where homedir is null;
```

 22显示 gid 大于500的用户的用户名 家目录和使用的shell

```sql
select username,homedir,shell from teacher t  where gid > 500;
```

23删除uid大于100的用户记录

```sql
DELETE from teacher WHERE uid > 100;
```

24显示uid号在10到30区间的用户有多少个。

```sql
SELECT count(*) from teacher t where uid BETWEEN 10 and 30;
```

25显示uid号是100以内的用户使用shell的类型。

```sql
SELECT shell from teacher where uid < 100;
```

26 显示uid号小于50且名字里有字母a  用户的详细信息

```sql
SELECT * from teacher where uid < 50 and username like '%a%';
```

27只显示用户 root   bin   daemon  3个用户的详细信息。

```sql
SELECT * from teacher where username in ('root','bin','daemon');
```

28显示除root用户之外所有用户的详细信息。

```sql
SELECT * from teacher where username not in ('root');
```

29统计username 字段有多少条记录

```sql
SELECT count(username) from teacher;
```

30显示名字里含字母c  用户的详细信息

```sql
SELECT * from teacher t where username like '%c%';
```

31在sex字段下方添加名为pay的字段，用来存储工资，默认值    是5000.00

```sql
alter table tea2 add pay float default 5000.00 after sex;
```

32把所有女孩的工资修改为8000

```sql
update teacher set pay=8000 where sex='gril'; 
```

33查看所有用户的名字和工资

```sql
SELECT username,pay from teacher;
```

34查看工资字段的平均值

```sql
select avg(pay) from teacher;
```

