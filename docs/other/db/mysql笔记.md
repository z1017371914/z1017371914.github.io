

## 数据库分类

### 关系型数据库（SQL）

ex:MySQL、Oracl、 Sql Server

### 非关系型数据库(NoSQL)

Not only SQL

ex：Redis、MongDb

## 基本指令

```mysql
登录：mysql -h localhost -P 3306 -u root -p

显示数据库：show databases;

进到某个数据库：use **;

展示表：show tables;

拼接函数(类似于字符串拼接): SELECT CONCAT(str1,str2) AS "?" FROM **;

显示表的所有数据： DESC **;

判断为空的函数: IFNULL(**,0);

转义函数: ESCAPE '*';

从高到低排序: SELECT * FROM * ORDER BY * DESC;
从低到高排序: SELECT * FROM * ORDER BY * ASC;
```

## 数据库操作

### 创建数据库

```mysql
create database if not exits database_test
```

### 删除数据库

```mysql
Drop database if exits database_test
```

### 使用数据库

```MYSQL
use `database_test`
```

### 查看数据库

```mysql
show databases       -- 查看所有数据库
```

## 数据库的数据类型(列类型)

### 数值

#### 整数

tinyint 														1Byte

smallint													  2Byte

mediumint												3Byte

**int 															  4Byte**

bigint														  8Byte

#### 浮点数

float															4Byte

double													   8Byte

decimal：字符串形式的浮点数

### 字符串

char  0~255

**varchar 可变字符串 ：0~65535**

tinytext 微型文本  2^8-1

**text     文本串 2^16-1**

### 时间日期

date          YYYY-MM-DD

time          HH:mm:ss

**datetime   YYYY-MM-DD   HH:mm:ss**

**timestamp  时间戳    1970.1.1到现在的毫秒数**

year 

### null

 ## 数据库的字段属性

### Unsinged

无符号类型。不能为负数

### 自增

自动在上一条记录的基础上加1

通常用来设计唯一的主键

### zerofill

不足的位数用0填充

### NotNull

非空，如果不给它赋值就会报错。

### Default

设置默认的值是多少。



## 创建数据库表

### SQL语句

```mysql
create table if not exists `student`(
	`id` INT(4) NOT NULL AUTO_INCREMENT COMMENT `学号`，
    `name` VARCHAR(30) NOT NULL DEFAULT `无名氏` COMMENT `姓名`,
    `pwd` VARCHAR(30) NOT NULL DEFAULT `123456` COMMENT `密码`，
    `sex` VARCHAR(2) NOT NULL DEFAULT `男` COMMENT `性别`,
    `address` VARCHAR(100) DEFAULT NULL COMMENT `住址`,
    PRIMARY KEY(`id`)
)ENGINE= INNODB DEFAULT CHARSET =utf8
```

引擎不声明的话也会**默认是INNODB**

**记得****要设计主键**

### 偷懒写法

当然也可以偷懒，使用其他已经存在的表的SQL语句作为模板

```mysql
show create table student
-- 这样就能得到创建某张表的SQL语句
```

### 显示表结构

```mysql
DESC student
```

## 数据表的类型

### MYISAM与INOODB

|                  | MYISAM             | MySQL                              |
| ---------------- | ------------------ | ---------------------------------- |
| 事务支持         | 不支持             | 支持                               |
| 数据行锁定(行锁) | 不支持             | 支持                               |
| 外键约束         | 不支持             | 支持                               |
| 全文索引         | 支持               | 支持（MySQL5.6以后支持）           |
| 表空间的大小     | 较小               | 较大                               |
| 特点             | 节约空间，速度较快 | 安全性高，支持事务，多表多用户操作 |

 ## 修改表

### 总览

```mysql
-- 重命名
ALTER TABLE student RENAME AS student1
-- 增加表的字段
ALTER TABLE student ADD age INT(11)
-- 修改表的字段的约束
ALTER TABLE student MODIFY age VARCHAR(11)
-- 修改表的字段的名字
ALTER TABLE student CHANGE age age1

-- 删除表的字段
ALTER TABLE student DROP age
```

### 表重命名

```mysql
ALTER TABLE student RENAME AS student1
```

### 增加表的字段

```mysq
ALTER TABLE student ADD age INT(11)
```

### 修改表的字段的约束

```mysql
ALTER TABLE student MODIFY age VARCHAR(11)
```

### 修改表的字段的名字

```mysql
ALTER TABLE student CHANGE age age1
```

### 删除表的字段

```mysql
ALTER TABLE student DROP age
```

## 基本组成

### 连接器

负责和客户端进行连接。进行权限认证，建立连接，保持连接，管理连接。

### 分析器

进行词法分析，语法分析。

### 优化器

获取分析器的结果后，选择优化的策略。

### 执行器

根据执行方案，调用引擎的接口来执行sql语句。

## 基本名词

### 脏读

读取到其他事务未提交的数据，其他事务更改后，自己却未改变。比如A，B事务。余额2000。B事务取出1000块，这时候A事务查余额，余额1000。但是B事务突然发现取款操作有问题，事务回滚，B事务看到的余额就为2000，而A只有1000。这时候A事务再充5000，显示余额为6000.但实际上应该是7000

### 不可重复读

前后多次读取，数据内容不一致。事务A比较庞大，需要的时间长。事务A第一次查询余额为2000，这时候去执行其他的。事务B修改了余额为5000，提交后跑路。事务A再查询余额时，发现余额变成了5000，前后发生了不一致。

### 幻读

前后多次读取，数据总量不一致。事务A使用Select查询，获得1000条数据，然后去执行其他任务。这时事务B新提交了1000数据然后跑路。A在查询时获得了2000条数据，像是产生了幻觉，凭空多产生了1000 条数据。

**不可重复读针对的是update语句，幻读针对的是insert和delete操作。**

### MVCC

多版本并发控制。是行级锁的变种。

优点：有时能避免进行加锁操作，减小了系统的开销。

应用：应用在**读提交**，**可重复读**这两个事务隔离级别。因为读未提交默认读取最新的行，串行化默认是加行级锁。

具体内容：保存数据在某个时间点的快照。在每一行都有版本号和删除版本号，保存新快照时，版本号加一。

## 事务

### 基本操作

BEGIN/START TRANSACTION：开启事务。

COMMIT/COMMIT WORK：提交事务。

ROLLBACK/ROLLBACK WORK：回滚事务。

SAVEPOINT IDENTIFIER：在事务中创建一个保存点。

RELEASE SAVEPOINT IDENTIFIER：删除一个事务的保存点，当没有指定的保存点时会抛出异常。

ROLLBACK TO IDENTIFIER：把事务回滚到保存点。

SET TRANSACTION 隔离级别：设置事务的隔离级别。READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ 和 SERIALIZABLE。

### 基本特性ACID

A：原子性。事务的操作要么全部成功，要么全部失败

C：一致性。事务开始前和结束后，数据库的完整性没被破坏。

I：隔离性。针对并发事务访问数据，隔离程度有四种。

D：持久性。事务结束后，对数据库的修改就是永久的

### 隔离级别

#### 读未提交

即使事务中的修改并没有提交，做出的修改也会被其他事务所看到并获取。

**问题：不可重复读，脏读，幻读。**

#### 读提交

事务只能看到已经提交过后进行的修改。解决了脏读问题

**问题：不可重复读，幻读**

#### 可重复读

MySQL默认的事务隔离级别。保证了在同一事物中，多次读取的数据的结果是一样的。解决了脏读和不可重复读。

**问题：幻读。**

##### MVCC的操作

###### Select

InnoDB查找的两个标准：

**1.所查找的行的版本号，小于当前事务数据行的版本号。**

原因：保证了所获取的数据，是在事务之前就已经存在，或者是事务自身插入或者修改过的。

**2.行的删除版本号要么为空，要么大于事务的版本号。**

原因：保证事务结束前，数据不会被删除。

###### Insert

**InnoDB将新插入的那一行的版本号，赋值当前的版本号**

原因：声明数据进行修改的时间。

###### Delete

**InnoDB将删除的行的删除版本赋值为当前的系统版本号。**

原因：记录删除的时间

###### Update

**InnoDB会新创建一行，将创建版本号记为当前版本号。而旧的那一行，将删除版本号记为当前版本号。**

原因：新建一行进行操作，这样既保留的旧的数据，又达到了更新的目的

##### 实现

使用MVCC + 封锁技术。为事务设置了一致性视图，视图中读取的数据不可能读到比事务要晚的其他事务的数据更新。

#### 串行化

强行让事务按行进行操作(使用了行锁)。**解决了幻读问题。**

### 事务日志

避免每次更改数据的操作，都要写入磁盘。

实现：引擎在修改数据的时候，只需修改内存拷贝。而修改操作，会以追加的方式写进硬盘里的事务日志。

写入磁盘次数：两次。1：写入到磁盘里的事务日志； 2：从事务日志中慢慢读取操作然后写入磁盘。

## 索引

相当于是文章的目录，通过查目录可以得到文章的页码，同理通过查索引能得到满足查询条件的列。

优化查询的最有效手段就是索引优化。

而索引可以是一列或者多个列的值，如果是多个列，那么列的顺序也会很重要，因此MySQL使用的是索引的最左前缀列。而索引的顺序一般是在建表语句那里确定。

**索引存储的层次：引擎层**

### 类型

#### B-TREE索引

MyISAM引擎使用B-tree索引时使用了前缀压缩技术，使得索引需要的空间更小。InnoDB时按原数据进行存储，需要的空间会更大。

InnoDB底层的索引实现是B+树。

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gokbohbc0hj30go0b0abs.jpg)



![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gogaxc71fmj30o70bbjs7.jpg)

**优点：**

1.引入B-Tree索引后，进行查询时不再需要扫描整张表，而是不断地检索索引节点来获取结果。

2.由于叶子节点的有序性，对于范围查找，比如查询名字从‘a到k’的人，查找效率会很高效。

**缺点：**

1.如果不是按照最左前缀原则进行查找，那么无法使用该索引。例如索引的列顺序为A-B-C，那么无法直接通过索引查询首列的B的查询条件

2.对于键值重复率很高的字段，查询的效率非常低

3.如果使用了like模糊查询，那么这个列后的其他列没有得到查询的优化。由于模糊查询，还是得需要扫描该列，从而导致索引后面的其他列并没有得到查询优化

#### 哈希索引

基于哈希表实现。所有的哈希索引中，都存有每一行数据的hashCode。哈希索引存着所有的哈希码，并保存指向所有数据行的指针，方便查找。

支持非唯一哈希码的存储引擎：Memory引擎。即同一个哈希码对应了多个数据行，和其他哈希冲突类似，使用拉链法将数据行展开。 

**缺点：**

1.哈希索引只存储了哈希值和行指针，所以不能通过索引的值来避免读取行。

2.不能通过索引来进行排序。因为哈希索引是按值查找。

3.不支持部分索引查询。比如查询A时不能用哈希索引A-B

4.哈希索引只支持等值比较查询，不支持范围查询，比如where id  > 2

5.出现哈希冲突时，需要遍历拉链法产生的链表中的所有行指针

##### InnoDB的改进：自适应哈希索引

![image](https://tva3.sinaimg.cn/large/0085EwgIgy1gokd93tdrjj30ij0c8n00.jpg)



InnoDB如果发现某些索引被使用得非常频繁时，会在B-TREE索引的基础上创建哈希索引。

#### 全文索引

 和常用的模糊匹配使用 like + % 不同，全文索引有自己的语法格式，使用 match 和 against 关键字，比如

```sql
select * from fulltext_test 
    where match(content,tag) against('xxx xxx');
```

查找的是文本中的关键词，而不是直接比较索引中的值。

#### 覆盖索引

一个索引包含了所有需要查询的字段的值，成为覆盖索引。

实现：将非主键的列联合组成一个多字段的联合索引，那么这时候就不需要进行回表操作(通过主键索引去查找)，就能查询到所有的字段得到值。

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gokf753apyj30n90et0v4.jpg)



##### 优点

1.避免了回表操作。(回表操作：如果查询到的是主键，需要用主键再查一次表)

2.在只需要读取索引的情况下，覆盖索引的优势很大：占用空间比数据要小。

3.覆盖索引按列值来顺序存储。I/O密集型的范围查询会比随机从磁盘里读取每一行的I/O次数少很多

### 存储结构

聚簇索引并不是一种特定的索引类型，而是一种数据的存储方式。

#### 聚簇索引

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1gogc32nfsjj30nu0lrk27.jpg)

聚簇索引是一种存储结构。InnoDB在聚簇索引中存储了B-Tree索引和数据行。

表内有聚簇索引时，数据行会放在索引的叶子节点。

InnoDB默认使用主键来聚集数据。没有主键的话就用某一个非空列来替代；如果仍然没有的话，就隐式的定义一个主键来作为聚簇索引。

InnoDB的主键索引和二级索引区别：

1主键索引

![image](https://tva1.sinaimg.cn/large/0085EwgIgy1goker0pd2cj308308dwff.jpg)

主键索引：通过唯一为空自增主键作为主键索引，也叫做聚簇索引。主键索引存的是字段的所有内容:主键ID以及其他所有数据。

2.二级索引

![image](https://tva3.sinaimg.cn/large/0085EwgIgy1goketyv31uj307d08pgn1.jpg)

二级索引：非主键索引就是二级索引。二级索引存的是索引值和主键值。查询时先使用索引值进行查询，如果无法查询到对应的值，就要进行回表操作，通过主键索引来查询对应的字段。

##### 优点

1.把具有相关性的数据保存在一起。例如按用户ID作为聚簇索引，可实现从磁盘读取少量的数据即可获得用户的邮件。

2.访问数据更快。因为数据存在和B+树的叶子节点

3.聚簇索引存了数据行，所以使用聚簇索引查询时，可以直接使用其中的主键值。即能直接读取数据。

##### 缺点

1.插入速度依赖于插入的顺序。按主键插入的话速度最快。所以最好使用OPTIMIZE TABLE来重新组织表结构。

2.更新聚簇索引列的代价很高。会强制InnoDB将被更新的行移到新的位置。

3.基于聚簇索引的表在插入新行，或者主键被更新需要移动时，可能存在页分裂的情况。

4.聚簇索引可能导致全表扫描变慢，特别是当行比较稀疏，或由于页分裂导致数据存储不联系时。

5.二级索引(非聚簇索引/辅助索引)的空间占用可能较大，因为二级索引叶子节点中包含主键列。

6.二级索引访问需要两次索引查找而非一次。

##### 使用场景

### 优化策略

#### 独立的列

查询语句应当选择独立的列，使得MySQL能解析语句并使用合适的索引进行查询

```mysql
select user_id from scool.student where user_id + 1 = 4
```

很明显可以用user_id = 3，但如果用上面的查询语句，那么mysql就无法使用引擎来优化查询，只能扫描整张表。

#### 索引选择性

索引选择性：不重复的索引值（也称为基数，cardinality）和数据表的记录总数（#T）的比值，范围从1/#T到1之间。

说人话，拿来做索引的列必须要有区分度，比如性别无非男女，拿来做索引区分度非常的低。

#### 前缀索引

索引的字符长度不应该太长。比如名字可以有很多字符，需要作为索引时，可以适当的选择合适的长度。如果选择的长度非常长，会导致索引文件很大。

#### 合适的索引列顺序

通过具体的业务逻辑来进行选择。因为B-tree索引是按照最左前缀顺序来查找的，所以列的顺序会直接影响索引的查询性能。

#### 考虑聚簇索引的特性

1.频繁变更的列不适合作为索引。每次更新，要保持b-tree的有序性， 需要的维护成本高。

2.使用自增ID作为主键。B-tree叶子节点数据是按照主键的顺序来存放的，使用自增ID作为主键，那么添加数据时是要在B-tree的叶子节点尾部进行添加即可。如果超过了负载因子，那就开辟新的一页。

#### 避免冗余和重复索引

如果已经有了某个唯一key(name,age)，又新建一个key(name)，那么name这个列就重复了。

#### 删除未使用的索引

建表时可能需要某个字段作为条件进行查询，但不断迭代后可能不再需要，那么就应该将这个字段删除掉。

### 索引扫描

如何判断MySQL是否使用了索引扫描进行排序？

EXPLAIN的结果中，**type类型为index**，那么就是用了索引扫描来排序。

索引的扫描是很快的，但是如果用于排序的话，如果不包含全部的字段，那么还是需要回表操作来慢慢查找。

所以：

1.只有当索引顺序和order by句子顺序一致时，并且所有列的方向都一样，才能使用索引来对排序进行扫描。

2.查询关联多个表的时候，只有order by子句引用的字段全部为第一个表时，才能用索引扫描进行排序

## 查询优化

### 执行查询的流程

1.客户端发送一条查询请求给服务器

2.服务器查询缓存。如果命中的话直接返回；如果没命中，进入到步骤3

3.执行器执行SQL解析，包括词法分析和语法分析。

4.优化器生成对应的执行步骤

5.执行器根据执行步骤，调用引擎的API去执行任务

6.将结果返回给客户端

### 查看执行计划(explain)

EXPLAIN关键字，能对select关键字进行解析。

![image](https://tva1.sinaimg.cn/large/0085EwgIgy1gokhx874ixj30n602tt96.jpg)

select type：查询的类型

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1gokib8quo7j30sa03paaf.jpg)

table：查询的表名

partitions：匹配的分区

type：join的类型

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gokibrkp1cj30mb0hjgme.jpg)

possible_keys：本次查询可能用到的索引

key：用到的索引

key_len:表示查询优化器使用了索引的字节数

ref：哪个字段或常数与key一起使用

rows：扫描了多少行

filtered: 表示此查询条件所过滤的数据的百分比

extra: 额外的信息

![image](https://tvax4.sinaimg.cn/large/0085EwgIgy1gokic7vd16j30mw0bawf6.jpg)

### 设计Schema

#### 原则

1.小。存储数据的数据类型刚好能满足数的类型即可。而INT类型代价比CHAR类型小，如果不是必要，可以选择INT类型

2.尽量将数据设为NOT NULL。除非不是有必要，否则尽量将字段设为非空，能显著降低查询、索引等方面的开销。

3.列不要设计得太多。

### 慢查询优化

#### 基本查询语句

1.查询已经执行成功的SQL

```sql
show variables like 'slow_query_log';
```

2.查询慢查询的日志存放位置

```sql
 show variables like 'slow_query_log_file';
```

3.查看慢查询的时间

```sql
 show variables like 'long_query_time';
```

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gokimrvebkj30fy042t96.jpg)

#### 优化前的判断逻辑

##### 是否请求了多余的数据

1.是否查询了不需要的数据。比如只需要某一列，却查询select *

2.是否查询了大量的结果，最后只取一部分。比如网页的分页操作查询，只需要显示10条结果，却先查询了所有的分页信息，再取十条。解决方法：限定返回数量(使用 LIMIT)

3.多表关联的查询，应当注意是否返回的是自己想要的结果，而不应该返回全部的数据。

4.如果多次执行结果相同的查询语句，应该考虑将结果存在缓存中， 而不是一直在内存中查找。

##### 是否扫描了多余的数据

判断指标：响应时间、扫描行数、返回行数

优化方法：

1.使用索引覆盖查询，将所有需要的列都加入到索引中，减少回表操作

2.重写这个查询语句，即下面的重构查询

3.改变库表的结构，使用单独的汇总表

### 重构查询

#### 复杂查询还是多个简单查询

当有一个复杂的查询时，也可以考虑是否有必要将其优化成简单的子查询。适当的进行分析对比。

#### 切分查询

如果一次性查询的结果太多，可以考虑分而治之大的思想，一次查询一小部门，然后分多次查询，减小同一时间内MySQL的压力。

典型的例子就是删除：

​		例如需要删除一个月内的数据，这时候如果直接按日期**一次性**删除，那么就会需要阻塞其他的事务以及小型但是非常重要的查询，也会占用系统资源，写满事务日志。

​		但是使用切分查询，即分而治之的思想，就能将服务器的压力分散的不同的时间段上。

#### 分解关联查询

多关联的查询语句分成多个不关联的查询语句，性能会更优。

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1gon52qr11pj30fa05mwfa.jpg)

原因：

1.缓存的命中率更高。关联的表如果发生了变化，那么就无法使用查询缓存了；而对于不关联的表，如果只发生很小的变化，是可以使用查询缓存的。

2.查询拆解后，能减少锁的竞争，从而能更快的执行

3.查询效率本身也有可能因为查询语句的变化而得到优化，比如上面的in()，减少了mysql的查询次数。

4.减少冗余记录的查询。例如在应用层做关联查询的时候，某条记录只需查询一次，而在数据库中做关联查询，是需要对某些数据做非常多次的查询。

### 执行查询相关的基础知识

查询过程：

![image](https://tvax1.sinaimg.cn/large/0085EwgIgy1gon5u1zdw7j30ii0edq5p.jpg)

1.客户端发送查询请求

2.缓存中查询是否发生命中。命中的话就直接返回

3.sql进入到解析器与预处理器

4.进入到查询优化器，得到查询执行方案

5.进入到查询的执行引擎执行查询计划

6.返回结果









## InnoDB

### 上锁

InnoDB使用的是两阶段锁协议。

#### 隐式锁定

事务执行过程中，可以随时上锁。但是只有执行Commit或者RollBack的时候，才会释放锁。

#### 显示锁定

使用sql语句控制。

```sql
SELECT .... LOCK IN SHARE MODE
```

### 对比MyISAM

事务：InnoDB支持事务，MyISAM不支持

外键：InnoDB支持，MyISAM不支持

索引：InnoDB使用聚簇索引和辅助索引，MyISAM使用非聚簇索引

锁：InnoDB默认使用行级锁，并支持表级锁。 MyISAM支支持表级锁。

## 日志

### redo log

InnoDB特有的日志。物理日志，记录了在某个数据也上进行了什么修改。

redo log是循环写的，空间随时会用完

**具体使用：**当记录需要更新的时候，将操作写入redo log，并更新内存即视为完成更改操作。redo log有四个文件，有写指针和读指针。当二者相遇的时候，就代表这4个G写满了，需要将记录写入到磁盘中。

### bin log

逻辑日志，记录的是语句的原始逻辑。如给ID = 2的这一行的A字段加一。

bin log是可追加的写，写到一定大小后会切换到下一个文件。

### 两阶段提交

#### 原理

redo log和bin log使用两阶段提交来配合。

#### 使用原因

