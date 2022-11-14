# MySQL学习笔记

### SQL通用语法

1. SQL语句可以单行或多行书写，以分号结尾
2. SQK语句可以使用空格/缩进增强语句的可读性
3. MySQL数据库语句不区分大小写，关键字建议使用大写
4. 注释：
   - 单行注释：--注释内容 或 #注释内容
   - 多行注释：/*注释内容

MySQL登录：

```
本地登录： mysql -u root -p
```

```
远程登录： mysql -h 主机地址 -u 用户名
```

更改配置：

1.cd /etc/mysql/mysql.conf.d

2.su do vi mysqld.cnf

3.43行注释掉  127.0.0.1 这一行

4.进入mysql    

5.use mysql    

6.改user表

7.user 表内2个字段 host ，user   localhost 表示只能本地主机才能访问，改成需要访问的ip；% 表示任何一个人都能访问。

8.新配置的myql 需要配置的话；user 还要更改plugin为cahing_sha2_password。



------

### 运算符/逻辑运算符:

```sql
没有赋值运算符，只写一个等于。
- BETWEE 在两数之间  例： age BETWEE 70 and 80
- NOT BETWEE  不在两数之间
- IN 在集合当中    例：name in ('ceshi1','ceshi2') 不区分大小写
- NOT IN
- IS NULL 为空
- IS NOT NULL 不为空
- 逻辑运算符： NOT AND OR 
```

------

### as语句：

在sql语句中as用于给字段或表重命名，相当于python的as 。

select name as ‘姓名’，xx as xx，from ，在填写表名地方填写as就可以重命名。只在当前语句生效；

------

### 数据库创建

查询系统所有数据库：

```
SHOW DATABASES；
```

查询当前数据库：

```
SELECT DATABASE()；查询当前所处的数据库
```

创建

```
CREATE DATABASE[IF NOT EXISTS]数据库名[character set utf8][COLLATE 排序规则]
```

IF NOT EXISTS 表示如果数据库存在则不创建，不存在则创建新的数据库

default charset 指定字符集

方括号内的都是可选内容

删除

```
DROP DATABASE[IF EXISTS]数据库名
```

使用

```
USE 数据库名
```

### 数据库备份：

数据库备份需要在终端执行,

```
mysqldump -u root -p 库名 >库名.sql
```

### 数据库导入：

```
mysql -u root -p  数据库名 < 需要导入的数据库路径
```

------



### 基础数据类型：

**数字类型：**

整数类型，浮点类型；float 单精度 小数点38位  bouble 双精度小数点308位 decimal（总位数，小占多位）100%精确  bit（相当于布尔类型，0，1）

![img](https://img-blog.csdnimg.cn/img_convert/bab7db2696cd0f2e6e7310c629cc9bfa.png)

**字符串类型：**

CHAR （最大存储字节）定长字符串，设置后固定占据字节。建议小于50个字节

VARCHAR （最大存储字节）变长字符串，存多少占多少  建议大于50字节

BLOB: 存储二进制，图片音频视频

TEXT:存储文本   比较长，不太发生变化，比较少用于查询的

**枚举类型：**

ENUM: 单选，给定选项，只能从给的选择

SET:  多选，

**时间类型：**

DATE (常用)   YYY-MM-DD

TIME（时间段 小时） HH:MM:SS

YEAR（年）

DATETIME(年月日时分秒) YYY-MM-DD HH:MM:SS

 TIMESTAMP(时间戳) YYY-MM-DD HH:MM:SS   

日期函数：now()  获取当前时间



------

### 约束:

```sql
UNSIGNED 无符号约束  不能为负数

NOT NULL  不能为空

DEFAULT 0 默认值

COMMENT  字段说明

PRIMARY KEY  设置主键，设置位置不能重复不能为空

AUTO_INCREMENT  设置主键自增。只作用于整型主键。
```

### 外键约束：

**存在一对多，关联到多的那边，多的那边叫主表，多是从表。与主表的数据类型一致**；

创建表的时候：

```
[CONSTRAINT SYMBOL] FOREIGN KEY (外键字段) REFERENCES dept(id)
```

```sql
CREATE TABLE index_test(
	id int auto_increment primary key,
	name varchar(32),
	age TINYINT UNSIGNED,
	salary DECIMAL(8,2),
	dept_id int
	[CONSTRAINT 定义名称] FOREIGN KEY (dept_id) REFERENCES dept(id)    《-可不定义名称》
);
```

**建立后增加外键：**

```sql
alter table 表名 add [CONSTRAINT 名称] FOREIGN KEY (dept_id) REFERENCES 主表(列) 
```

ps:建立后增加外键如果列内有非法值会创建失败；建立外键关系，会项目中可能会导致备份，操作等问题。可以用讨论是否添加；数据量较小时可以使用。看业务需求；

解除外键：

```sql
alter table 表名 drop foreign key 键名
```

级联动作：

从表关联主表键，默认主表被关联主键不能修改和删除；

cascade：当主表被删除时，被关联的也会被删掉整行，修改会变成你修改后的数；

添加在 增加的后面 on delete cascade on update cascade 

```sql
alter table 表名 add [CONSTRAINT 名称] FOREIGN KEY (dept_id) REFERENCES dept(id) on delete cascade on update cascade 
```

set null  主表发生改变时，从表键会变成null；

on delete set null on update set null

```sql
alter table 表名 add [CONSTRAINT 名称] FOREIGN KEY (dept_id) REFERENCES dept(id) on delete set null on update set null
```

主键不一定时数字整型，可以是身份证号，车牌号；关联字段要和从表数据类型一致

多对多关系可以使用关系表串联；依赖于关系建立的数据，放在关系表中；一对多多对多相互存在。根据业务需求

------

### DDL-表操作-创建：

ps：写完注释后面要加空格再加其他符号不然会报语法错误

```
CREATE TABLE 表名（
	字段1 字段1类型[COMMENT 字段1注释]
	字段2 字段2类型[COMMENT 字段2注释]
	字段3 字段2类型[COMMENT 字段3注释]
）[COMMENT 表注释] ；
```

### 创建索引：

索引查询速度快，会在系统中创建一个索引表，使用场景：数据量比较大，经常查询很少写操作。

主键索引 primary key（）   ：唯一，不能为空；

唯一索引  unique 索引名( ) ：字段不允许重复，能为空

普通索引   Index 索引名：

```
CREATE TABLE index_test(
	id int auto_increment,
	name varchar(30),
	primary key(id)
	unique nameIndex(name)   《- nameIndex 是自定义的索引名
);
```

```sql
show index from 表名
```

```sql
添加索引： create [unique] index nameIndex  on 表名（索引名）
删除索引： drop index 索引名 on 表名
```

------

### 表查询操作:

1. 确定存储内容
2. 明确字段构成
3. 确定字段数据类型

查询当前数据库所有的表

```sql
SHOW TABLES;
```

查询表结构

```sql
DESC 表名
```

查询指定的表的建表语句

```sql
SHOW CREATE TABLE 表名；
```

删除表：

```sql
DROP TABLE 表名
```

------

### 拷贝表：

```sql
create table 表名 select语句
```

复制出来的表不存在键和索引，他只复制了数据。

### 视图（创建一张映射表）：

视图是存储查询语句，使用的时候系统调用SELECT语句，创建一张虚拟的表，对视图进行修改的时候原表也会发生改变；视图当中数据遵循原表约束；

**创建视图：**

```sql
CREATE [OR REPLACE] VIEW [VIEV_NAME] AS SELECT语句   加 OR REPLACE 如果表存在则会覆盖；
```

表联合的视图不能进行删除，插入；表联合视图不建议采用写操作；名字建议XXX_view;

**查看所有视图：**

```SQL
SHOW FULL TABLES 库名 WHERE TABKE_BYPE LIKE"VIEW"
```

**删除视图：**

```sql
drop view [if exists] 视图名称  
```

**修改视图：**

```sql
alter view 视图名称 as select语句；
```

------

### INSERT 插入数据：

```sql
方式1： insert  into 表名 values （值1，值2...）,（值1，值2...） #行数据，一个括号就是一行,按照字段顺序全写，
```

```sql
方式2：insert  into 表名(字段1，字段2...) values （值1，值2...）,（值1，值2...） #行数据，NOT NALL必须出现在字段里。
```

------

### SELECT 查询数据：

```sql
查询表    select *[nane,score]  from 表名
```

```sql
条件查询表 select *[nane,score]  from 表名 where 条件：
```

模糊【条件】查询：

‘L%’   查找以什么是开头的  /  ‘%a% 查找包含有什么的  /  '___'  查找指定数量字符一个下划线表示一个字符

```sql
select *[nane,score]  from 表名 where 匹配列 like 【条件】
```

### MYSQL多表关联查询：

------

**多表查询分为 **

**1内连接 **

**2外连接（左连接，右连接）**

**简单的多表查询：**不推荐使用

```
select *[表名.nane,表名.score]  from 表名,表名  where 匹配列 like 【条件】
```

多表查询重复字段，表名.字段 

**内连接：两个表相互关联的数据**；

```sql
select * from 表1 inner join 表2 on 关联的数据=关联的数据
```

会筛选出2个表内有关联的数据，有关联的会被刨除；

**外连接：**

------

*左连接**   左边所有的数据与右边关联数据

```sql
select * from 表1 left join 表2 on 关联的数据=关联的数据
```

右连接   右边所有的数据与左表关联数据

```sql
select * from 表1 right join 表2 on 关联的数据=关联的数据
```

多张表连接：

```
select * from 表1 left join 表2 on 关联的数据=关联的数据 left join 表3 on  
```

多表查询分组的时候需要显示什么信息，就把信息都加入分组。

------

### MYSQL 高级查询：

排序的类型：时间，数字，纯英文。

写在where后面，如果没有则整个列表排序，默认是升序。

```
ORDER BY 列 [desc:降序]
```

**复合排序：ORDER BY**

```sql
ORDER BY 列 [desc:降序]，列2   当第一排序想相同的话再按第二排序项；可第一排序升序，相等时降序
```

**限制操作数量：LIMIT** 

```sql
select *[nane,score]  from 表名 limit 2（最多操作数量）offset 2
```

**跳过：OFFSET**

```sql
select *[nane,score]  from 表名 offset 1 limit 1（最多操作数量）
# 跳过1执行1个。打印第二个
```

ps：有可能会出现混合陷阱，中文出现可能性比较大。解决方法是增加一个排序项

**联合查询 UNION：**

```sql
第一条查询语句 UNION 第二条查询语句；结果重合时自动去重，UNION ALL 不去重
一般再联合查询后写排序。第一个查询语句作为表头。
```

子查询语句：

select 可以嵌套  select 可以作为数据集。

用法1：数据集提供给外层select

```sql
select *from（select...）as 名称   select作为数据集提供给外层select。必须给子查询语句起名字
```

用法2：作为值的提供者，只能有一个明确的值

```sql
select *from class where score>(子查询) select作为值提供给条件语句，可用于增删改
#例外情况：当逻辑运算符为IN的时候可以多个值
```

**聚合操作：**在筛选的结果上进一步的计算，计算出一个值，需展示1个以上字段某个字段全部值是一样的

avg () 该字段的平均值

max（）该字段的最大值

min（）该字段的最大值

sum（）该字段所有记录的和

count（）统计该字段记录的个数

```sql
select avg（列） from 表名 
```

**group by  分组：**

```sql
select 列 from sanguo group by 列  分谁查谁
group by 的意义:
对分组之后的聚合操作是对每个组都进行聚合操作；

##多字段创建分组后再分组：提交2组列
select 列 from sanguo group by 列，列
```

**having 分组后条件筛选** 

 对分组聚合后的结果进一步筛选：弥补了where的不足，在group by 后面。对聚合分组后的每一组数据分别筛选；

**having**

```sql
select distinct 列 from 表
#打印去重后的列
```

------

### UPDATE 修改数据：

```sql
修改： update  表名 set  字段1=值1,字段2=值2...  where 条件（筛选出结果修改）
```

不添加条件会把整个表格的字段都改成设置的数值。

------

### DELETE 删除数据：

```sql
delete from 表名 where 条件（筛选出结果删除）键是唯一标识，删了就没了，只能手动添加
```

------

### ALTER 表结构修改：

```sql
ALTER  TABLE  表名 执行动作

执行动作：

add    例句：ALTER  TABLE  表名 add 名称 数据类型 约束

after  在什么后面插入  例句 ：ALTER  TABLE  表名 add 名称 数据类型 约束 after 列名

drop 删除列    例句：ALTER  TABLE  表名 drop 列名

更改数据类型：alter table 表名 modify 列名 数据类型

ALTER  TABLE  表名 执行动作

执行动作：

增加列 add                例句：ALTER  TABLE  表名 add 名称 数据类型 约束

插入列 after              例句 ：ALTER  TABLE  表名 add 名称 数据类型 约束 after 列名

删除列 drop               例句：ALTER  TABLE  表名 drop 列名

更改数据类型 modify        例句alter table  例句：表名 modify 列名 数据类型 
ps（修改的数据类型必须能和以前的数据匹配）

修改列名    change  alter table 表名 change 列名 修改 数据类型
删除外键：   alter table 表名 drop foreign key 键名
```

------

### 函数/存储过程：

**有返回值的函数叫做函数，没有返回值的叫存储过程；**函数和存储过程可以重名；函数不能重名，存储过程和存储过程不能重名；存储过程必须没有返回值； 

```sql
delimiter 自定义符号，修改结尾符号；定义函数前改成别的，定义完改回来
CREATE FUNCTION 函数名 （形参列表） returns 返回值类型 
begin
	函数体
	retun val；
end	结尾符
```

函数中不能直接写查询操作；如果函数中有写操作只能select 函数名（）；where语句提供者返回结果必须是个明确的值；函数可以写在SELECT语句当中进行打印；有其他操作不能作为值给where子句当中作为值的提供者；

定义变量：用户变量  局部变量；

**定义用户变量：**

```sql
set @变量名=xx
```

```sql
declare 变量名 数据类型;  《---定义变量；
赋值方法1：
set 变量名=（查询语句）;
赋值方法2：
查询语句 into 变量名；
```

例子：

```sql
delimiter $$
create function ceshi(name varchar(20)) returns varchar(20)
begin
    declare xuesheng int;
    set xuesheng=(select name from class where name=name);
    return xuesheng;
end 
delimiter ;
```

```sql
直接调用 select 函数名（参数）  call 储存操作名 
```

```sql
作为参数条用 where xx>函数名()
```

存储过程：调用过程可以进行增删改查，相当于把一系列做操作包装起来；

**形参类型：**

in输入型形参，在内部的修改不回影响用户变量；

out 输出型形参（不能再函数语句内使用，传入的数据能被修改，被修改后可以使用，会影响外部的实参，不能传入常量） inout类型 可以使用也可以修改；  

```sql
create procedure 函数名（输入型 形参 数据类型）
begin
	执行语句；
end	

调用方法 call 函数名（）
```

查看函数构建代码：

```sql
SHOW CREATE function 函数名  / SHOW CREATE procedure 存储过程名
```

查看所有数据库函数和存储过程：

```sql
select name，type from mysql.proc db='数据库'
```

删除函数和存储过程

```sql
drop function 函数名  /drop procedure 存储过程名
```

------

### 事务控制：

保证数据的完整性和使用安全；主要是用于写操作。相当于游戏的存档点，创建事务会创建日志，操作和查看的都是日志；

事务四大特性：

原子性  ：完成 和不完成只能有一个

持久性：保存后修改了原数据

隔离性：2个开启事务后互不印象  隔离原理：事务日志

一致性：因为原子性保证了数据的一致性；

1.开启事务：

```sql
mysql>begin
```

2.增删改

3.结束事务：

```
mysql>commit #sql命令都执行成功提交数据库
mysql>rollback  #有sql命令执行失败，回滚到初始状态
```

事务锁；

当有人在事务改数据的时候，其他客户端不能再修改该数据表，需等待commit结束  。

幻像读：

当你操作修改之后，再查询。他会把你修改的数据和原数据重新载入。会出现幻读；

![image-20220716155526610](C:\Users\43549\AppData\Roaming\Typora\typora-user-images\image-20220716155526610.png)

------

### 创建用户：

```
careat  user "用户名"@ 可用ip IDENTIFIED BY '密码'  with grant option（加上这句才可以给下级继续分配权限）
```

修改密码：

```
alter user "用户名"@“可用ip” IDENTIFIED BY '密码
```

### 权限管理：

增加权限

```sql
grant 权限列表 on 库.表 to “用户”@“用户ip”；
```

删除权限

```sql
revoke insert，update，select on 库.表 from “用户”@“ 用户ip”；
```

刷新权限

```sql
flush privileges  具体权限要用就查；
```

------

修改远程登录权限：

修改配置文件—重启mysql—进入mysql表—修改usre 用户 远程连接的权限-刷新权限

### 数据库优化5大范式

第一范式：能拆分的都给他拆分成字段或表；一个字段内一个内容；

第二范式：表内要有主键；每条记录要有唯一标识

第三范式：合理设置外键；外键关联主属性字段。不依赖于非主属性；

主要是优化数据表减少冗余；

### MySQL存储引擎：

MySQL有多个引擎，根据压力测试去选择引擎，效率不一样；

查看所有引擎：

```sql
show engines
```

inndDB支持行级锁，支持事务，支持外键，根据数据

创建的时候指定引擎：

创建语句 后面增加：engine=引擎名

已创建修改：

```sql
alter table 表名 engine=引擎名；
```

字段数据类型和键的选择：

优先级 数字类型--》时间类型—》字符串

数据量大的时候不鼓励创建外键，可通过应用层控制；

### 语句优化：

使用主键索引或索引字段擦汗寻，加where进行初步筛选，尽量选择数据类型，并且再where，group by orderby中出现频率高的字段建立索引；使用limit控制查询数量；

尽量避免null值判断，尽量避免or 连接使用union

### 表优化：

当数据表数据内容太多，超过500万条的表就要进行拆表了；

垂直拆分：字段较多时，经常查询的放一张表，不经常查询的放一张表；

水平拆分：拆分为冷数据，热数据；

------

### 关系创建ER模型图：

矩形表示实体，菱形表示关系，椭圆形代表属性

实体：行  属性：列  关系：一对多，多对多



### 小技巧总结：

show 查询  

drop 删除   

创建 CREATE

数据库：DATABASES  

表：TABLE     

查找：SELECT

查看表结构：DESC

总结：关系型数据库：表    字段（列）  记录（行）

打开记录日志：set profiling=1   项目上线时不开启会降低性能；

印象较为浅：视图，事务，索引

------

### PYMYSQL:

连接数据库，操作关闭

connect（）---》读/写 —》close（）

读写：execute（）

写操作：单次 executemany（sql语句）多次，第二参数传递嵌套数组，一组执行一次

commit（）提交事务  follback（）回滚

读操作：fetchone 一条   fetchmany 多条  fetchall 所有

#### 数据库写操作：

```python
import pymysql
userParameters = {
    "host": "172.30.254.68",
    "port": 3306,
    "user": "root",
    "password": "123456",
    "database": "zhuxihong",
    "charset": "utf8"
}
db = pymysql.connect(**userParameters)   #连接数据库
cur = db.cursor()    # 生成游标对象
# 使用游标操作
try:
    modify = "insert into class values (8,'lala',22,'m',66);"
    cur.execute(modify)    #————会自动创建事务
    db.commit()			   #结束事务，可执行多条后提交
except Exception as e:
    print(e)
    #如果出错则回滚，放弃前面所有操作，标准的写操作
    db.rollback()    
# 关闭数据库连接：
cur.close()
db.close()
```

注意事项：

1.mysq当执行写操作的时候，默认会自动开启事务控制

2.如果数据表不支持事务execute修改会直接生效

3.如果数据表支持事务，则需要commit控制提交

execute使用技巧哪里需要数据加【%S】以数组形式传递给execute：

```
modify = f"insert into class(name,sex,score) values (%s,'m',%s);"  
cur.execute(modify,[name,score])
```

批量写操作：

```
cur.executemany(sql语句，嵌套数组或数组嵌套元组)循环执行，一组执行一次；
```

```python
db = pymysql.connect(**userParameters)   #连接数据库
cur = db.cursor()    # 生成游标对象
# 使用游标操作
data=[
    ("tonny",18,'m',77),
    ("livi",16,'m',76),
    ("chali",28,'m',88),
    ("dongdong",38,'m',87)
]
try:
    sql = f"insert into class(name,age,sex,score) values (%s,%s,%s,%s);"
    cur.executemany(sql,data)  #-等同于循环执行execute
    db.commit()
except Exception as e:
    print(e)
    db.rollback()
# 关闭数据库连接：
cur.close()
db.close()
```

#### 数据库读操作：

```python
userParameters = {
            "host": "172.17.167.136",
            "port": 3306,
            "user": "root",
            "password": "123456",
            "database": "zhuxihong",
            "charset": "utf8"
        }
db = pymysql.connect(**userParameters)   #连接数据库
cur = db.cursor()    # 生成游标对象
a="select name,age,score from class;"
cur.execute(a,[传递值])
for i in cur:              #迭代获取每一条记录
    print(i)             
print(cur.fetchall())       #所有记录  
print(cur.fetchmany(4))    #多条记录
print(cur.fetchone())      #一条记录   结果返回一个元组包裹查询信息
db.close()
cur.close()
```

### 文件存储方法：

存储文件路径（文件比较大，视频等，几十兆以上的都算大文件；实际使用中比较多）

优点：节省数据库空间，提取方便；

缺点：文件或数据库发生迁移会导致文件丢失；



存储文件本身：

优点：安全可靠，数据库在文件就在

缺点：占用数据库空间大，文件存取效率低

![image-20220718014504037](C:\Users\43549\AppData\Roaming\Typora\typora-user-images\image-20220718014504037.png)

![image-20220718014631055](C:\Users\43549\AppData\Roaming\Typora\typora-user-images\image-20220718014631055.png)