原文地址：https://mp.weixin.qq.com/s/XpukKx9Ovl4bqil8cNDP0g

导读



本文旨在深入探讨MySQL（8.0.26）数据库中索引的设计与优化方法。

# 

引言

# 

有一张表user（无索引）：

假如要执行的sql语句为：select * from user where age = 45;

需要从第一行开始，一直扫描到最后一行，称为全表扫描，性能很低；有没有提升性能，减少搜索时间的方法呢？


## 索引介绍

# 





**1. B+tree 结构介绍**



## 

在Mysql中，索引就是帮助搜索数据的一种有序的数据结构，它以某种方式引用（指向）数据。

Mysql中的索引是在存储引擎层实现的，因此不同的存储引擎又有着不同的索引结构，主要包含以下几种：

![图片](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6STTYQedibvrjRoRjmpAK8EHiaY6JPxrub4ZVkjibWW2Lx6xGQ7MLdGdzA/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)

简单介绍下经典的B+tree 的结构：

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6GD0qBF2iclZiblQ4HpuBIw6ThL1bda1pk9jxh4TRnmCcgaMqDLCxwAIg/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

可以看出：

- 所有的数据都会出现在叶子节点，叶子节点形成一个单向链表；
- 非叶子节点仅仅索引数据，具体的数据都是在叶子节点存放的；

Mysql索引数据结构对经典的B+tree进行了优化，在原有B+tree的基础上，增加了一个指向相邻叶子节点的链表指针，形成了带有顺序指针的B+tree，提高区间访问的性能，利于排序；

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx65Q3tfC6EBvbTUouwIr4TicOrVQ1lgxqGfMdB9xVkOJ2LrA7P61HCzjQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿



**2. 索引分类**



## 

在mysql数据库，将索引的具体类型主要分为以下几类：

![图片](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6s79LbHLkGJt1nLUEWve4hLMiapvNOxqUT00zJUIFwqMtfCTGqGtSVZQ/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)

在Innodb 存储引擎中，根据索引的存储形式，分为两种：

![图片](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6sKPySncC4jy8mE1tUlNoRC3hh0q1P3cqtmqJqKVDOcq7apVQGbk8fQ/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)

执行一条查询语句，我们分析一下具体的查找过程：

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx62CC8Jby9mCd4tNDAR1rxvibUOtM3jiaQV26pyw0rG5jo3jia4fyGYHwjQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

具体过程如下:

\1. 因为是根据name字段查询，所以先根据 name='Arm' 到name字段的二级索引中进行匹配查找。但是在二级索引中只能查找到 Arm 对应的主键值 10；

\2. 由于查询返回的数据是*，所以此时，还需要根据主键值10，到聚集索引中查找10对应的记录，最终找到10对应的行row；

\3. 最终拿到这一行的数据，直接返回即可。



**3. 索引语法**



## 

创建索引：

create  [unique|fulltext]  index 索引名 on 表名 （字段名1，字段名2,……）;

查看索引：

show index from 表名；

删除索引：

drop index 索引名 on 表名；

SQL性能分析

# 





**1. sql执行频率**



## 

Mysql客户端链接成功后，通过以下命令可以查看当前数据库的  insert/update/delete/select  的访问频次：

show [session|global] status like ‘com_____’;

session: 查看当前会话；

global: 查看全局数据；

com insert: 插入次数；

com select: 查询次数；

com delete: 删除次数；

com updat: 更新次数；

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx629GqdZhTX6LBFytOUWpMEuAc6c4VQ04D7mXkciaR8qC7QicX2cqGv8ag/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

通过查看当前数据库是以查询为主，还是以增删改为主，从而为数据库优化提供参考依据，如果以增删改为主，可以考虑不对其进行索引的优化；如果以查询为主，就要考虑对数据库的索引进行优化。﻿





**2. 慢查询日志**



## 

慢查询日志记录了所有执行时间超过指定参数（long_query_time,单位秒，默认10秒）的所有sql日志：

开启慢查询日志前，需要在mysql的配置文件中（/etc/my.cnf）配置如下信息：

\# 1. 开启mysql慢日志查询开关：

slow_query_log = 1

\#2. 设置慢日志的时间，假设为2秒，超过2秒就会被视为慢查询，记录慢查询日志：

long_query_time=2

\#3.  配置完毕后，重新启动mysql服务器进行测试：

systemctl restarmysqld

\#4. 查看慢查询日志的系统变量，是否打开：

show variables like “slow_query_log”;

\#5.  查看慢日志文件中（/var/lib/mysql/localhost-slow.log）记录的信息：

Tail -f localhost-slow.log

最终发现，在慢查询日志中，只会记录执行时间超过我们预设时间（2秒）的sql，执行较快的sql不会被记录。

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx65qPUUWicb920bmhC4w1JOFQ1e7ibDA2I2F7x91c6NXF0zzPkhJQS2Htg/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6FAb1XlhmpckQCnatro8WSAAiaeof5udLhVNibQNCwnOdicjIzmKNLWxiaA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿



**3. Profile 详情**



## 

show profiles 能够在做SQL优化时帮助我们了解时间都耗费到哪里去了。﻿

\#1. 通过 have_profiling 参数，可以看到mysql是否支持profile 操作：

select @@have_profiling;

\#2.  通过set 语句在session/global 级别开启profiling:

set profiling =1;

开关打开后，后续执行的sql语句都会被mysql记录，并记录执行时间消耗到哪儿去了。比如执行以下几条sql语句：﻿

select * from tb_user; 

select * from tb_user where id = 1; 

select * from tb_user where name = '白起'; 

select count(*) from tb_sku;

\#3. 查看每一条sql的耗时基本情况：

show profiles;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx63yicjDaia4YdXT9oTeD4djCYbFHsZAcL4v7sP6ROibkMSCyicdAjt7ibqNg/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿﻿

\#4. 查看指定的字段的sql 语句各个阶段的耗时情况：

show profile for query Query_ID;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6naNeazc1WYiauQickN795HfIOvusxojNwTib3mTueVibUaNzsJRTARClog/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿﻿

\#5. 查看指定字段的sql语句cpu 的使用情况：

show profile cpu for query Query_ID;





**4. explain 详情**



## 

EXPLAIN 或者 DESC命令获取 MySQL 如何执行 SELECT 语句的信息，包括在 SELECT 语句执行过程中，表如何连接和连接的顺序。﻿

语法 :直接在 select 语句之前加上关键字 explain/desc;

\# explain select 字段 from 表名 where 条件;

![图片](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6MzxHeuVQvKibW3N9LU5IILTnN0bzxWNEJ3I38S2Kg6uKtHJVjTcZelQ/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)



索引使用

# 



**1. 索引失效的情况**



## 

### 1.1 最左前缀法则

如果存在联合索引，要遵守最左前缀法则。即查询从索引的最左列开始，并且不跳过索引中的列，如果跳跃其中某一列，索引将会部分失效（后面的字段索引失效)。

假设在 tb_user 表中

联合索引，涉及三个字段，顺序为：profession（索引长度为47）, age（索引长度为2）, status（索引长度为5）。

a)explain select * from tb_user where profession = '软件工程 ' and age = 31 and status='0';

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6o5EuLPKvVyQFyxxIBPIPR44u0xyNUkaiblU4nrzP4YVTEzxYQcFBUrw/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

b)explain select * from tb_user where profession = '软件工程 ' and age = 31 ;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx62iaTvmRoxjBMDColytAKNPj3Ce8T7icicCN3DEH6tXPMe58OY4XvjnvgQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

c)explain select * from tb_user where profession = '软件工程‘;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6xcSKGALe2ygwsHPwc5ICSMXqRQEGicjgfbz2gbfBBQkPnfs1Y7DKpyg/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

以上的这三组测试中，我们发现只要联合索引最左边的字段profession存在，索引就会生效；

a)explain select * from tb_user where age = 31 and status='0';

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6Az9Bot89cBhr6cy1JqFoQqfnibh0icZpX6rUJ33ZIU9jPibCrVQ4xJKhQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

b)explain select * from tb_user where status='0';

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6UdMUdlj3DiaTK4gGnVLlYUUbBwhhEVH65PicicStYTjVOs7gibQmJcUhgw/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

以上的这两组测试中，我们发现只要联合索引最左边的字段 profession不存在，索引并未生效；﻿

explain select * from tb_user where profession = '软件工程 ' and status='0';

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx606tt93LwKpic9ccb9wibQ9icaaSbx9oo5PJfPLeP1JaAPztHBNtAqGdNA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

上述的一条SQL查询时，联合索引最左边的列profession字段是存在的，索引满足最左前缀法则的基本条件。但是查询时，**跳过了age这个列，所以后面的列索引是不会使用的，也就是索引部分生效，所以索引的长度为47；**﻿

explain select * from tb_user where age = 31 and status='0' and profession = '软件工程’;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx699FWFQqiagK49fvPsA1F6Vta3wgDbqGywia0LxDe1eiclHVcRdXzpywQg/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

可以看到，索引长度54，完全满足最左前缀法则，联合索引生效。

⚠️ 最左前缀法则中是指查询时，联合索引的最左边的字段（即第一个字段）必须存在，与编写sql时，条件编写的先后顺序无关。

### 1.2 范围查询

联合索引中，出现范围查询（>,<），范围查询右侧的列索引失效。﻿

explain select * from tb_user where profession = '软件工程 ' and age >31 and status='0';

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6GjVT5v9Kl8sXw6IkrOqJIvvRBC2YRsmwiajMKZkWxeCQpOPmnZSkWQQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

上述可以看到，当范围查询使用>或<时，查询走联合索引了，但是索引长度为49，说明范围查询右边的status字段没有走索引；﻿

explain select * from tb_user where profession = '软件工程 ' and age >= 31 and status='0';

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6t6kKMibib5jOQib6dnyAiaIZlnpRFoI922zeQPOKFn9Qj8JqmfWnicKo9mw/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

上述可以看到，当范围查询使用>=或<=时，查询走联合索引了，但是索引长度为54，说明所有字段都是走索引的；

⚠️ 所以，在业务允许的情况下，尽可能使用类似于>=或<=这类的范围查询，避免使用>或<。

### 1.3 索引列运算

在索引列上进行运算操作，索引将失效。

假设在 tb_user 表中，存在单列索引：phone

explain select * from tb_user where phone = '17799990015';

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6qhbibQzOacmuLYe5Jia2VUW57icueYJark9LBcbmbm13A7L5nYzLWw62A/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

上述看到，当根据phone字段进行等值匹配查询时，索引生效。

explain select * from tb_user where substring(phone,10,2)=’15’;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6wHUYtzKRLn5OAnVrtsFDjrMhia3TcNSk9Up5IS36kV9Wzbt5G5UFnSg/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

上述看到，当根据phone字段进行函数运算操作之后，索引失效。

### 1.4 字符串不加引号

字符串类型字段使用时，不加引号，索引将失效。

explain select * from tb_user where profession = '软件工程 ' and age = 31 and status = '0 ';

explain select * from tb_user where profession = '软件工程 ' and age = 31 and status = 0;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6eBjFl9ibV1hjHNUOibME86Neev5ulaPwgvjfP8de0Vl4Ticlo03xtPSZw/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

explain select * from tb_user where phone = ‘1779990015’;

explain select * from tb_user where phone = 1779990015;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6Vl5sjVz6DteNq4c1RU0zEDWsswO6cyYT1FLEbIcdiaP6tq4QvIxZnKg/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

上述看到，字符串不加单引号时，对于查询结果没有影响，但是由于数据库存在隐式类型转换，索引将失效。

### 1.5 模糊查询

尾部模糊匹配，索引不会失效；头部模糊匹配，索引失效。

explain select * from tb_user where profession like ‘软件%’;

explain select * from tb_user where profession like ‘%工程’;

explain select * from tb_user where profession like ‘%工%’;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6YUWf5095gHZCaMTWGdA3ib1dK3IyEAG5Ht8pmTtUxBkicg91gBzHgufA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

上述看到，在like模糊查询中，在关键字后面加%，索引可以生效；在关键字前面加%，索引将会失效。

### 1.6 or 连接条件

or 前的条件中的列有索引，后面的列中没有索引，则涉及的所有索引都不会被用到。﻿

**步骤一：**存在单列索引：phone

explain select * from tb_user where id=10 or age=23;

explain select * from tb_user where phone=’1779990017’ or age=23;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6tVHxzYRaSsTkjyaS3V0CZaXGavpXuzWshCKeVTe2OjVzib7NibVBcoog/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

上述看到，由于age 没有索引，所以即使id, phone有索引，索引也会失效。

**步骤二：**对age字段 建立索引

create index idx_user on tb_user(age);

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6YbYOQdtJ76icLR0YA7Qr9aksJJ08SvdU0bibibLdnVSOYdJTmnPOjibk1A/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

**步骤三：**再次执行上述的sql语句

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx60FyvibWtnsyEtyegiarf1mhYIbkCltror5qZVGa1AbsOnZS7O20tEtkA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

可以看到，当or 连接的条件，左右两侧字段都有索引时，索引才会生效。﻿

### 1.7 数据分布影响

Mysql评估使用索引会比全表更慢，则不会使用索引。﻿

explain * from tb_user where phone >= ‘1779999005’;

explain * from tb_user where phone >= ‘1779999015’;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6XY85R6Px7KtiaLwOrHpCtNaMBrXEsY8nH5gIR9YaiaMsgJpLC7uwwclg/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

上述看到，相同的sql语句，传入字段值不同时，所执行的计划也不同，这是因为：

mysql在查询时，会评估使用索引的效率与走全表扫描的效率，哪种效率高使用哪种。因为索引是用来索引少量数据的，如果通过索引查询返回大批量的数据，则不如走全表扫描来的快，此时索引就会失效。

再来看下，is null & is not null 的操作是否会走索引：

**步骤一：**

explain select * from tb_user where profession is null;

explain select * from tb_user where profession is not null;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6BtNPWLXFzJh9JcZxbluEpia5rq8SsFn5R8FNibPjFRcjMTfxUFdQyjibQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

**步骤二：**把profession 字段全部更新为null：

update tb_user set profession = null;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6FXvdYwEcicY1PTPfVjrhdRzrs5bNia3dUEBibkFDhCYUYoJaoNknApocA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿﻿

**步骤三：**再次执行上述语句

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6bYiaLsI6ysTxmib860FD78Rc66xIo50JXBobk2AqBL1MnbGFkZEib0rpQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

最终看到，相同的sql语句，先后执行两次，查询的计划不同。

这是因为和数据库的数据分布有关系，查询是mysql会评估，走索引还是全表扫描，如果全表扫描块，则放弃索引走全表扫描，因此，is null & is not null 是否走索引，得具体情况具体分析，不是固定的。﻿



**2. SQL提示**



## 

字段profession 存在联合索引(idx_user_pro_sta) & 单列索引（idx_user_pro）。

explain select * from tb_user where profession=’软件工程’;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6F1M0OolcaR7UvdG0Jwa667oEyELZMlsWt5iaqdouVbTsW7eKOAtd22w/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

可以看出，mysql 最终选择了idx_user_pro_age_sta 联合索引，这是mysql自动选择的结果。

那么，我们在查询时，可以使用mysql的sql提示，加入一些人为的提示来达到优化操作的目的：

- user index：建议mysql使用哪一个索引完成此次查询（仅仅是建议，mysql内部还会再次进行评估）；
- ignore index：忽略指定的索引；
- force index：强制使用索引。

演示：

1）explain select * from tb_user use index(idx_user_pro) where profession=’软件工程’;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6gBXEMrjofDr6NrvcFnH4PfcicM97vkn8oocK0iaXjYCIuzsQU7Gsb0Hg/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

2）explain select * from tb_user ignore index(idx_user_pro) where profession=’软件工程’;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6CdKN0dVLnDr22ZtzpfS3ibickLxP7icb57zQ41QJa4oA8Tv6WQpAzCUOA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

3）explain select * from tb_user force index(idx_user_pro) where profession=’软件工程’;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6p327RygmyqR7AFUbHQgvQbsn9RvVS2vd5icTbiaxyv8a9RfkibP4cRrxA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿



**3. 覆盖索引**



## 

覆盖索引是指查询使用了索引且返回需要的列，在该索引列中已经全部能够找到。

在查询时，尽量使用覆盖索引，减少select *。

表中存在联合索引 idx_user_pro_age_sta (关联了三个字段profession, age, status)，该索引也是一个二级索引，该叶子节点下面的是这一行的主键id。当查询返回的数据在id, profession, age, status 中，则直接走二级索引返回数据，如果查询字段超出这个范围，就需要拿到主键id, 再去扫描聚集索引获取额外的数据，这个过程就是回表。

当我们一直使用 select * 查询返回所有字段值，很容易造成回表查询（除非根据主键查询，此时只会扫描聚集索引）。

演示如下：

- explain select id, profession, from tb_user where profession=’软件工程’ and age=31 and status=’0’;
- explain select id, profession, age, status from tb_user where profession=’软件工程’ and age=31 and status=’0’;
- explain select id, profession, age, status, name from tb_user where profession=’软件工程’ and age=31 and status=’0’;
- explain select * from tb_user where profession=’软件工程’ and age=31 and status=’0’;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6qQYPWfaDN8a8kyibxxKYGXJfPs4ickfXtATXzevNj2TSA7n02mEGicEicA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

为了让大家更清楚地理解，什么是覆盖索引和回表查询，我们一起来看下一组sql的执行过程：

A. 表结构及索引示意图:

id是主键，是一个聚集索引。name字段建立了普通索引，是一个二级索引(辅助索引)。

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6icvORNXPUHMdia0ZK4ATNQic1yEl9ZPK8zapScNFdNuEoe7tKPxs9tSkw/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

B. 执行SQL:  select * from tb_user where id = 2;

根据id查询，直接走聚集索引查询，一次索引扫描，直接返回数据，性能高。

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6BVGP79OslpX4w7HZYkaaQ6JmP9fBytLn9ceqaTdh6vlrQIt7qjAytg/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

C. 执行SQL:  selet id,name from tb_user where name = 'Arm';

虽然是根据name字段查询，查询二级索引，但是由于查询返回在字段为 id，name，在name的二级索 引中，这两个值都是可以直接获取到的，因为覆盖索引，所以不需要回表查询，性能高。

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6ibdRsTIicQT9f5ECZGKbg5rU08UkbMIgq7NkfnBqcoWSBqZYtqRnbibtQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿﻿

D. 执行SQL: selet id,name,gender from tb_user where name = 'Arm';

由于在name的二级索引中，不包含gender，所以，需要两次索引扫描，也就是需要回表查询，性能相对较差一点。

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6sSRHB0oyibHliaCaLedTUXQJL5MiavLrCggdMxjUET4JBfTuJJhfpbliaQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿﻿

🤔思考一下：一张表四个字段（id, username, password, status）,如何对sql优化最优？

select id, username, password from tb_user where username=’itcast’;

答案：针对username, password 建立联合索引，避免回表查询。



**4. 前缀索引**



## 

当字段类型为字符串（varchar, text, longtext等）时，有时候需要索引很长的字符串，导致索引较大，查询是浪费大量的磁盘IO，影响查询效率。此时可以只对字符串的一部分前缀建立索引，节约索引空间，提高索引效率。

1）语法：create index idx_xxx on 表名（column(n)）;

create index idx_email on tb_user(email(5));    # 为表tb_user的 email 字段建立长度为5的前缀索引。

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6NsesUNwiadadOC2Lnm2FSHDdiaYDgXX7CMPRNdFHPG8bjZQSgy1dcMFw/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

2）前缀长度

可以根据索引的选择性来决定，选择性=不重复的索引值（基数）/数据表的记录总数。

索引选择性越高则查询效率最高，唯一索引的选择性是1，这是最好的索引选择性，性能也是最好的。

select count(distinct email)/count(*) from tb_user;

select count(distinct substring(email, 1, 5)/count(*) from tb_user;﻿

3)前缀索引的查询流程

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6cibQp0poicBGOkkMiamzrmK8ZGTefvvLHJNybzkwmxc3ONpR0I0OwB04A/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿﻿



**5. 单列索引&联合索引**



## 

单列索引：一个索引只包含单个列

联合索引：一个索引包含了多个列

当and 连接的两个字段 phone、name上都有单列索引，mysql最终只会选择一个索引，也就是说只能走一个字段的索引，此时会进行回表查询的。

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6g10PmIRQJkfBRk7icZNhOLpZjQ8L7QabRo5QwfxEmanXDrVugHz31gg/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

此时，我们创建一个phone和name字段的联合索引。

create unique index_user_phone_name on tb_user(phone, name);

查询以下执行计划：

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6Jc1mptXGp8Oowab6rsrExFveGHKmp5nUick5xVJhHn47OBJwQKGNibqw/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

此时查询时，走了联合索引，在联合索引中包含phone, name的信息，在叶子节点下挂的是对应的主键id，所以查询不需要回表查询。

⚠️ 所以在业务场景中，若存在多个查询条件，考虑针对于查询字段建立索引时，建议建立联合索引，而非单列索引。﻿

查询使用联合索引时，具体的结构示意图如下：

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6MpIz4ODznqxcClPxkgUy3VSxooibVYOV3YcDTwrJVUqGZSptxVHZxnQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿



**6. 索引设计原则**



## 

1)针对数据量较大，且查询比较繁琐的表建立索引；

2)针对于常作为查询条件（where），排序（order by），分组(group by)操作的字段，建立索引；

3)尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高；

4)如果是字符串类型的字段，字段的长度过长，可以针对字段的特点，建立前缀索引；

5)尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率；

6)要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增删改的效率；

7)如果索引列不能存储null值，在创建表时使用not null约束它。当优化器知道每列是否包含null值时，它可以更好地确定哪个索引最有效地用于查询。

SQL优化

# 



**1. 主键优化**



## 

### 1.1 主键顺序插入

\1. 从磁盘中申请页， 主键顺序插入；

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6rydJZGIcJepKVuS5o2KBoJGIQo8ZOeXYibrZdr6kUxaZrM3ziapKKNAQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

\2. 第一个页没有满，继续往第一页插入；

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6r15wianAZ4qwRCVwkuEf2gPVQ6siaianPCkphEXeO2eYoMIJvu5mSs1ng/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿﻿

\3. 当第一个也写满之后，再写入第二个页，页与页之间会通过指针连接；

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6T0jib2762icjAyjkdP6m70slBdFJqmTROTrI14q8obtQqw9MRC0UGTXA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿﻿

\4. 当第二页写满了，再往第三页写入；

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6g0cEv70T22tvRTdk60H9iaVqwg2v80JAwqXAq7Bgg04dRkRKuJ0tORA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

### 1.2 主键乱序插入

#### **1.2.1 页分裂**

1.假如1#,2#页都已经写满了，存放了如图所示的数据；

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6NUn8QfgWpIxoeGNUopcvPMhQn0Nicib5NVs3vMHH4NUcD0RibO3LEQZ8g/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

2.此时再插入id为50的记录，我们来看看会发生什么现象 会再次开启一个页，写入新的页中吗?

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6NwTiahrKXNw5xxBOCt4n0Fnvqh12ia9iaat6Vpagics0kM1xPM9v1M63vA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

不会。因为，索引结构的叶子节点是有顺序的。按照顺序，应该存储在47之后。

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx691mrLyQ1icZvxN3Y8iculKG3hfdrhtG0Q6tyzdSYxrJX4yHicD2gIc8tw/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

3.但是47所在的1#页，已经写满了，存储不了50对应的数据了。 那么此时会开辟一个新的页 3#。

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6mFzFdxia5NvRIUibQgekK0Vkgevc5EMZeW0rBdAPrEjhXH6tpENUXnPg/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

4.但是并不会直接将50存入3#页，而是会将1#页后一半的数据，移动到3#页，然后在3#页，插入50。

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6jXPydckoFPfbnTQDPPDhpnSA7TO7cHK0N5nCHwVlGHzPxEia9qVaXzw/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

5.移动数据，并插入id为50的数据之后，那么此时，这三个页之间的数据顺序是有问题的。 1#的下一个页，应该是3#， 3#的下一个页是2#。 所以，此时，需要重新设置链表指针。 这种现象，称之为 "页分裂"，是比较耗费性能的操作。

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6hy7G3Yv4OJicjxQkicfXlHCN0Tpq4djk85q5dt0QH2BXcQo1chuIOTvw/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

#### **1.2.2 页合并**

1.目前表中已有数据的索引结构(叶子节点)如下:

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6c9wq2xfwfCUaiawrnFDb1WGm2SlrB2NnjEFGxYXoOiabFXs4ecVH72OA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

2.当删除一行记录时，实际上记录并没有被物理删除，只是记录被标记(flaged)为删除并且它的空间变得允许被其他记录声明使用。

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6DUCqDlYIpc5neHUkP1A8qcNWD0ibdmRpiaXWCxgKr6qAUAVl30W2SBfw/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

3.继续删除2#数据，当页中删除的记录达到 MERGE_THRESHOLD(默认为页的50%)，InnoDB会开始寻找最靠近的页(前或后)看看是否可以将两个页合并以优化空间使用。

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6J1wZC5GQrl640WuOYicPRKJH5gLLyEyXZsXJYyuFRvHYBDibmUSKmibPQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿﻿

4.删除数据，并将页合并之后，再次插入新的数据21，则直接插入3#页。

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6PwqNVmZibTK3jLmiaPUaU2BGXrhTvcicGZduibPqvYDskun7pBcvI2Av3g/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

这个里面所发生的合并页的这个现象，就称之为 "页合并"。

#### **1.2.3 索引设计原则**

- 满足业务需求的情况下，尽量降低主键的长度；
- 插入数据时，尽量选择顺序插入，选择使用AUTO_INCREMENT自增主键；
- 尽量不要使用UUID做主键或者是其他自然主键，如身份证号；
- 业务操作时，避免对主键的修改。



**2.  order by优化**



## 

MySQL的排序，有两种方式:

![图片](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6lgD2RcMTSnmic1zwW1lpCA0Ik1ic587AL34oEHCBQYjH4RU2ibaOKVQ5Q/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)

对于以上的两种排序方式，Using index的性能高，而Using filesort的性能低，我们在优化排序操作时，尽量要优化为 Using index。

接下来，我们来做一个测试:  

 A. tb_user表中所建立的部分索引删除掉

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6OoIibib1KwLibvjh0Sr95aiba6X7NP2KFYibBVfd2Q7mQ2icGqJB71hMahuQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

B. 执行排序SQL 

explain select id,age,phone from tb_user order by age ;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx69icXWsjbN1Phpf1Dncodvpdm7nlTgd8Jy60AVGnskUhWSKmtBQqpIiaQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

explain select id,age,phone from tb_user order by age, phone ;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6jOyLtommXs9ibiakvLtJUBfpKWLF3kPL9mXsGicga2zF5rtXN7uhHsibnQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

由于 age, phone 都没有索引，所以此时再排序时，出现Using filesort，排序性能较低。

C. 创建索引

create index idx_user_age_phone_aa on tb_user(age, phone);

D. 创建索引后，根据age, phone进行升序排序

explain select id,age,phone from tb_user order by age;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6uINwIRDtf8QbClRiaiaqicunx6j04TQNmt1kpwDyfMyvW3NwicJsia3wpgQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

explain select id,age,phone from tb_user order by age , phone;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6P3AATJhE67PAAj5o0wAjIoU0Sm1oqlBVWeibeHud0xQGxx74cichK4bA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

建立索引之后，再次进行排序查询，就由原来的Using filesort， 变为了 Using index，性能 就是比较高的了。

E. 创建索引后，根据age, phone进行降序排序

explain select id,age,phone from tb_user order by age desc , phone desc ;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6ia1j04dFou1toic64CPdv7po8KozPM1aDoAC5Gnj1jK9mnozVZwyef6Q/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

也出现 Using index， 但是此时Extra中出现了 Backward index scan，这个代表反向扫描索引。因为在Mysql中我们创建的索引，默认索引的叶子节点是从小到大排序的，而此时我们查询排序时，是从大到小，所以，在扫描时，就是反向扫描，就会出现 Backward index scan。在 MySQL8版本中，支持降序索引，我们也可以创建降序索引。

F. 根据phone，age进行升序排序，phone在前，age在后

explain select id,age,phone from tb_user order by phone , age;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6WaPQeVcBqQ4rgJs1X9jWLs8AYE2SFia2VSTa46qN7oWvtAd0XQntkrw/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

排序时,也需要满足最左前缀法则,否则也会出现 filesort。因为在创建索引的时候， age是第一个字段，phone是第二个字段，所以排序时，也就该按照这个顺序来，否则就会出现 Using filesort。﻿

G. 根据age, phone进行降序一个升序，一个降序

explain select id,age,phone from tb_user order by age asc, phone desc ;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6q7YO1NJ8LibxdLcktV7XvEwWBpoFicBwAtuENAGkcYdL9G0D2YQ3cDIw/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

因为创建索引时，未指定顺序，所以默认都是按照升序排序的，

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6jAXdpKwK1SGnHXx6HhJUDEoI1N8FWjsiamlrBI2t1Vd3ZmOAACuhImA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

而查询时，一个升序，一个降序，此时就会出现Using filesort。为了解决上述的问题，我们可以创建一个索引，这个联合索引中 age 升序排序，phone 倒序排序。﻿

H. 创建联合索引(age 升序排序，phone 倒序排序)

create index idx_user_age_phone_ad on tb_user(age asc ,phone desc);

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6jTOQskaTH0rAFyWo1cePxGwUicAnBtJicmcsaycqeLbyIawRmJic3Is8g/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

I. 然后再次执行如下SQL

explain select id,age,phone from tb_user order by age asc , phone desc ;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6iaCGLIINTO4Zu51dI0Jf40wph3Bb6zBymoiberJhJazh9iaoDhDUFqDog/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

升序/降序联合索引结构图示:

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx66r1o1Mu965lWzuhHrxSY46V6QKE3I27og4ZCnEXezvrSv5x0JuhB2g/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6LCzAvRW3MAfXCpFsmt5q4g4OZGtcmHfNic0AS0scMicjeTUv3uwsvpFg/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

由上述的测试,我们得出order by优化原则:

1）根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则；

2）尽量使用覆盖索引；

3）多字段排序, 一个升序一个降序，此时需要注意联合索引在创建时的规则(ASC/DESC)；

4）如果不可避免的出现filesort，大数据量排序时，可以适当增大排序缓冲区大小sort_buffer_size(默认256k)。



**3. group by优化**



## 

分组操作，我们主要来看看索引对于分组操作的影响。

**步骤一：**在没有索引的情况下，执行如下SQL，查询执行计划:

explain select profession , count(*) from tb_user group by profession ;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6jEK1z5tNicStic69MPia2hCDU8PS2ficJww7EemNwVib8yAVvmUE467kBeQ/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

**步骤二：**针对 profession ，age，status 创建一个联合索引。

create index idx_user_pro_age_sta on tb_user(profession , age , status);

**步骤三：**再次执行前面相同的SQL查看执行计划。

explain select profession , count(*) from tb_user group by profession ;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6cOYnCU5Lea1tQQMLItqRxvBPeTa0oCwXugsFkXCniciajTRuBrC7nKJw/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

**步骤四：**执行如下的分组查询SQL，查看执行计划:

explain select profession , count(*) from tb_user group by profession, age ;

explain select profession , count(*) from tb_user group by age ;

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6WyI1JwN24ZZicLV69jCAKYzRWDuF5SK6zaJW8BEC4Uj50mXJdDMJ2DA/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

我们发现，如果仅仅根据age分组，就会出现 Using temporary ;而如果是根据 profession,age两个字段同时分组，则不会出现Using temporary。原因是对于分组操作, 在联合索引中，也是符合最左前缀法则的。﻿

⚠️ 所以，在分组操作中，我们需要通过以下两点进行优化，以提升性能:

1）在分组操作时，可以通过索引来提高效率；

2）分组操作时，索引的使用也是满足最左前缀法则的。



**4. limit优化**



## 

在数据量比较大时，如果进行limit分页查询，在查询时，越往后，分页查询效率越低。﻿

我们一起来看看执行limit分页查询耗时对比:

﻿![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6JOVIoibj6pEiaqFkx0ib2ibjREeE857UhoUhb8R4YkIPBdGUuewlNPiaX7Q/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)﻿

通过测试我们会看到，越往后，分页查询效率越低，这就是分页查询的问题所在：

因为，当在进行分页查询时，如果执行 limit 2000000,10 ，此时需要MySQL排序前2000010 记录，仅仅返回 2000000 - 2000010 的记录，其他记录丢弃，查询排序的代价非常大 。

优化思路: 一般分页查询时，通过创建覆盖索引能够比较好地提高性能，可以通过覆盖索引加子查询形式进行优化。

explain select * from tb_sku t , (select id from tb_sku order by id limit 2000000,10) a where t.id = a.id;



**5. count优化**



## 

### 5.1 概述

在之前的测试中，我们发现，如果数据量很大，在执行count操作时，是非常耗时的。InnoDB 引擎中，它执行 count(*) 的时候，需要把数据一行一行地从引擎里面读出来，然后累积计数。

如果说要大幅度提升InnoDB表的count效率，主要的优化思路: 自己计数(可以借助于redis这样的数据库进行,但是如果是带条件的count又比较麻烦了)。

### 5.2 count用法

count() 是一个聚合函数，对于返回的结果集，一行行地判断，如果 count 函数的参数不是 NULL，累计值就加 1，否则不加，最后返回累计值。

用法:count(*)、count(主键)、count(字段)、count(数字)

![图片](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJGqLNMWsQ1md1Bw1RF1Lx6m6Go4zz7P6nauOEvicnehbnWLuxztrQLR2QxeTCAdyCNbM0V8RtE8SA/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)

按照效率排序的话，count(字段) < count(主键 id) < count(1) ≈ count(*)，所以尽量使用count(*)。



**6. update优化**



## 

我们主要需要注意一下update语句执行时的注意事项。

update course set name = 'javaEE' where id = 1 ;

当我们在执行删除的SQL语句时，会锁定id为1这一行的数据，然后事务提交之后，行锁释放。﻿

当我们开启多个事务，再执行如下SQL时：

update course set name = 'SpringBoot' where name = 'PHP' ;

我们发现行锁升级为了表锁。导致该update语句的性能大大降低。

Innodb的行锁是针对索引加的锁，不是针对记录加的锁，并且该索引不能失效，否则会从行锁升级成表锁。

**参考文档：**

https://blog.csdn.net/weixin_42802447/article/details/124267211

https://blog.csdn.net/kybabcde/article/details/128680998
