原文地址：https://mp.weixin.qq.com/s/ZqM3-PC_vPCNOh3nqWSbfQ



# 后端程序员必备：15个MySQL表设计的经验准则



## **1. 设计表时，尽量都有这几个通用字段**

表必备一般来说，或具备这几个字段：

- id：主键，一个表必须得有主键，必须
- create_time：创建时间，必须
- modifed_time: 修改时间，必须，更新记录时，就更新它。
- version : 数据记录的版本号，一般用于乐观锁，非必须
- modifier :修改人，非必须
- creator ：创建人，非必须

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/PoF8jo1PmpxLwpugXhicDPDvgRXjFPRn4ygqEGKDbHhdAIxmtedSCTZPdicJOLibpFgcicCqC72aTblxSWiaKL1gNow/640?wx_fmt=other&tp=wxpic&wxfrom=13&wx_lazy=1&wx_co=1)

## **2. 每个字段都要有注释，尤其涉及枚举这些时**

我们在设计表的时候，**每个字段，都要写上注释哈**，尤其涉及到一个枚举字段的时候，更要把每个枚举值写出来，**后面如果有变更，也要维护到这里来**~

反例：

```javascript
CREATE TABLE order_tab (  
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_id BIGINT UNIQUE, 
    user_id BIGINT NOT NULL,  
    total_amount DECIMAL(10, 2) NOT NULL, 
    status VARCHAR(20) NOT NULL DEFAULT 'PENDING',   
    payment_status VARCHAR(20) DEFAULT 'not_paid',  
    version INT DEFAULT 0,
    created_time DATETIME,
    updated_time DATETIME,
    creator VARCHAR(255),
    modifier VARCHAR(255)  
);
```

正例：

```javascript
CREATE TABLE order_tab (  
    id BIGINT AUTO_INCREMENT PRIMARY KEY COMMENT '订单项的唯一标识符，自增主键',  
    order_id BIGINT UNIQUE COMMENT '订单的唯一标识符，在整个系统中唯一',  
    user_id BIGINT NOT NULL COMMENT '用户的唯一标识符，关联到用户表',  
    total_amount DECIMAL(10, 2) NOT NULL COMMENT '订单的总金额，精确到小数点后两位',  
    status VARCHAR(20) NOT NULL DEFAULT 'PENDING' COMMENT '订单的状态，例如：PENDING（待处理）、COMPLETED（已完成）等',  
    payment_status VARCHAR(20) DEFAULT 'not_paid' COMMENT '订单的支付状态，如：not_paid（未支付）、paid（已支付）等',  
    version INT DEFAULT 0 COMMENT '乐观锁版本号，用于并发控制',  
    created_time DATETIME COMMENT '订单的创建时间',  
    updated_time DATETIME COMMENT '订单的最后一次更新时间',  
    creator VARCHAR(255) COMMENT '订单的创建者，通常记录创建订单的用户或系统的用户名',  
    modifier VARCHAR(255) COMMENT '订单的修改者，通常记录最后修改订单的用户或系统的用户名'  
);
```

## **3. 命名规范**

数据库表名、字段名、索引名等都需要命名规范，可读性高(一般要求用英文)，让别人一看命名，就知道这个字段表示什么意思。

比如一个表的账号字段，反例如下：

```
acc_no,1_acc_no,zhanghao
```

正例：

```
account_no,account_number
```

- 表名、字段名必须使用小写字母或者数字，禁止使用数字开头，禁止使用拼音，并且一般不使用英文缩写。
- 主键索引名为pk_字段名；唯一索引名为uk_字段名；普通索引名则为idx_字段名。

## **4. 选择合适的字段类型**

设计表时，我们需要选择合适的字段类型，比如：

- 尽可能选择存储空间小的字段类型，就好像数字类型的，从tinyint、smallint、int、bigint从左往右开始选择
- 小数类型如金额，则选择 decimal，禁止使用 float 和 double。
- 如果存储的字符串长度几乎相等，使用 char 定长字符串类型。
- varchar是可变长字符串，不预先分配存储空间，长度不要超过5000。
- 如果存储的值太大，建议字段类型修改为text，同时抽出单独一张表，用主键与之对应。
- 同一表中，所有varchar字段的长度加起来，不能大于65535. 如果有这样的需求，请使用TEXT/LONGTEXT 类型。

## **5. 主键设计要合理**

主键设计的话，最好不要与业务逻辑有所关联。有些业务上的字段，比如身份证，虽然是唯一的，一些开发者喜欢用它来做主键，但是不是很建议哈。主键最好是毫无意义的一串独立不重复的数字，比如UUID，又或者Auto_increment自增的主键，或者是雪花算法生成的主键等等;

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/PoF8jo1PmpxLwpugXhicDPDvgRXjFPRn4jFgk9O0befPpt96IGVIeGG30rR51ibsLWp0RGv8WUNkmBCDteUTXYEA/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=wxpic)

## **6.选择合适的字段长度**

先问大家一个问题，大家知道数据库字段长度表示字符长度还是字节长度嘛？

> 其实在mysql中，varchar和char类型表示字符长度，而其他类型表示的长度都表示字节长度。比如char(10)表示字符长度是10，而bigint（4）表示显示长度是4个字节，但是因为bigint实际长度是8个字节，所以bigint（4）的实际长度就是8个字节。

我们在设计表的时候，需要充分考虑一个字段的长度，比如一个用户名字段（它的长度5~20个字符），你觉得应该设置多长呢？可以考虑设置为 username varchar（32）。字段长度一般设置为2的幂哈（也就是2的n次方）。

## **7. 优先考虑逻辑删除，而不是物理删除**

什么是物理删除？什么是逻辑删除？

- 物理删除：把数据从硬盘中删除，可释放存储空间
- 逻辑删除：给数据添加一个字段，比如is_deleted，以标记该数据已经逻辑删除。

物理删除就是执行delete语句，如删除account_no =‘666’的账户信息SQL如下：

```
delete from account_info_tab whereaccount_no ='666';
```

逻辑删除呢，就是这样：

```
update account_info_tab set is_deleted = 1 where account_no ='666';
```

为什么推荐用逻辑删除，不推荐物理删除呢？

- 为什么不推荐使用物理删除，因为恢复数据很困难
- 物理删除会使自增主键不再连续
- 核心业务表 的数据不建议做物理删除，只适合做状态变更。

## **8. 一张表的字段不宜过多**

我们建表的时候，要牢记，一张表的字段不宜过多哈，一般尽量不要超过20个字段哈。笔者记得上个公司，有伙伴设计开户表，加了五十多个字段。。。

如果一张表的字段过多，表中保存的数据可能就会很大，查询效率就会很低。因此，一张表不要设计太多字段哈，如果业务需求，实在需要很多字段，可以把一张大的表，拆成多张小的表，它们的主键相同即可。

当表的字段数非常多时，可以将表分成两张表，一张作为条件查询表，一张作为详细内容表 (主要是为了性能考虑)。

## **9. 尽可能使用not null定义字段**

如果没有特殊的理由， 一般都建议将字段定义为 NOT NULL 。

为什么呢？

- 首先， NOT NULL 可以防止出现空指针问题。
- 其次，NULL值存储也需要额外的空间的，它也会导致比较运算更为复杂，使优化器难以优化SQL。
- NULL值有可能会导致索引失效
- 如果将字段默认设置成一个空字符串或常量值并没有什么不同，且都不会影响到应用逻辑， 那就可以将这个字段设置为NOT NULL。

## **10. 设计表时，评估哪些字段需要加索引**

首先，评估你的表数据量。如果你的表数据量只有一百几十行，就没有必要加索引。否则设计表的时候，如果有查询条件的字段，一般就需要建立索引。但是索引也不能滥用：

- 索引也不要建得太多，一般单表索引个数不要超过5个。因为创建过多的索引，会降低写得速度。
- 区分度不高的字段，不能加索引，如性别等
- 索引创建完后，还是要注意避免索引失效的情况，如使用mysql的内置函数，会导致索引失效的
- 索引过多的话，可以通过联合索引的话方式来优化。然后的话，索引还有一些规则，如覆盖索引，最左匹配原则等等。。

假设你新建一张用户表，如下：

```
CREATE TABLE user_info_tab (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_id` int(11) NOT NULL,
  `age` int(11) DEFAULT NULL,
  `name` varchar(255) NOT NULL,
  `create_time` datetime NOT NULL,
  `modifed_time` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

对于这张表，很可能会有根据user_id或者name查询用户信息，并且，user_id是唯一的。因此，你是可以给user_id加上唯一索引，name加上普通索引。

```
CREATE TABLE user_info_tab (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_id` int(11) NOT NULL,
  `age` int(11) DEFAULT NULL,
  `name` varchar(255) NOT NULL,
  `create_time` datetime NOT NULL,
  `modifed_time` datetime NOT NULL,
  PRIMARY KEY (`id`),
  KEY `idx_name` (`name`) USING BTREE,
  UNIQUE KEY un_user_id (user_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

## **11. 避免使用MySQL保留字**

如果库名、表名、字段名等属性含有保留字时，SQL语句必须用反引号来引用属性名称，这将使得SQL语句书写、SHELL脚本中变量的转义等变得非常复杂。

因此，我们一般避免使用MySQL保留字，如select、interval、desc等等

## **12. 一般都选择INNODB存储引擎**

建表是需要选择存储引擎的，我们一般都选择INNODB存储引擎，除非读写比率小于1%, 才考虑使用MyISAM 。

有些小伙伴可能会有疑惑，不是还有MEMORY等其他存储引擎吗？什么时候使用它呢？其实其他存储引擎一般除了都建议在DBA的指导下使用。

我们来复习一下这MySQL这三种存储引擎的对比区别吧：

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/cEDP0gXG22luqSscdpdhvxibt62PBu0WRjyICcPMylyicibhkTwU6Fk4ThnWHa2WkXmCqhy6Du1HVKKjJib1gTq11A/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## **13. 选择合适统一的字符集**

数据库库、表、开发程序等都需要统一字符集，通常中英文环境用utf8。

MySQL支持的字符集有utf8、utf8mb4、GBK、latin1等。

- utf8：支持中英文混合场景，国际通过，3个字节长度
- utf8mb4:  完全兼容utf8，4个字节长度，一般存储emoji表情需要用到它。
- GBK ：支持中文，但是不支持国际通用字符集，2个字节长度
- latin1：MySQL默认字符集，1个字节长度

## **14. 时间的类型选择**

我们设计表的时候，一般都需要加通用时间的字段，如create_time、modified_time等等。那对于时间的类型，我们该如何选择呢？

对于MySQL来说，主要有date、datetime、time、timestamp 和 year。

- date ：表示的日期值, 格式yyyy-mm-dd,范围1000-01-01 到 9999-12-31，3字节
- time ：表示的时间值，格式 hh:mm:ss，范围-838:59:59 到 838:59:59，3字节
- datetime：表示的日期时间值，格式yyyy-mm-dd hh:mm:ss，范围1000-01-01 00:00:00到9999-12-31 23:59:59```,8字节，跟时区无关
- timestamp：表示的时间戳值，格式为yyyymmddhhmmss，范围1970-01-01 00:00:01到2038-01-19 03:14:07，4字节，跟时区有关
- year：年份值，格式为yyyy。范围1901到2155，1字节 推荐优先使用datetime类型来保存日期和时间，因为存储范围更大，且跟时区无关。

## **15. 安全性考虑**

- **数据加密**：敏感信息，如用户密码，应进行加密存储。如果是手机号、邮箱这些，则建议脱敏
