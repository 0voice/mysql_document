#  MySQL衍生开源项目推荐

## MariaDB MySQL 分支
项目简介：MariaDB是MySQL的一个分支，MariaDB打算保持与MySQL的高度兼容性，确保具有库二进制奇偶校验的直接替换功能，以及与MySQL API和命令的精确匹配，并从MySQL迁移。

MariaDB由MySQL的创始人 Michael Widenius 主导开发，他早前曾以10亿美元的价格，将自己创建的公司MySQL AB卖给了SUN，此后，随着SUN被甲骨文收购，MySQL的所有权也落入Oracle的手中。MariaDB名称来自Michael Widenius的女儿Maria的名字，就像MySQL是以他另一个女儿My命名的一样。

MariaDB 自带一个新的 Aria 存储引擎，替换了 MySQL 的 MyISAM 存储引擎，成为默认的事务和非事务引擎。它使用了 Percona 的 XtraDB，InnoDB 的变体，分支的开发者希望提供访问即将到来的MySQL 5.4 InnoDB性能，但是在10.2改回InnoDB引擎。

这意味着在很多情况下，你可以卸载MySQL并安装MariaDB。通常不需要转换任何数据文件。但是，您仍必须运行mysql_upgrade才能完成升级。这是确保使用MariaDB使用的新字段更新mysql特权和事件表所必需的。我们每月与MySQL代码库合并以确保MariaDB在MySQL中添加了任何相关的错误修复。也就是说，MariaDB有很多新的选项，扩展，存储引擎和错误修复，而不是MySQL。您可以在不同的MariaDB版本页面上找到不同MariaDB版本的功能集。

项目地址：https://gitee.com/mirrors/mariadb


## Percona Server MySQL 衍生版
项目简介：Percona 为 MySQL 数据库服务器进行了改进，在功能和性能上较 MySQL 有着很显著的提升。该版本提升了在高负载情况下的 InnoDB 的性能、为 DBA 提供一些非常有用的性能诊断工具；另外有更多的参数和命令来控制服务器行为。

Percona Server 只包含 MySQL 的服务器版，并没有提供相应对 MySQL 的 Connector 和 GUI 工具进行改进。

Percona Server 使用了一些 google-mysql-tools, Proven Scaling, Open Query 对 MySQL 进行改造。

项目地址：https://gitee.com/mirrors/percona-server


## AliSQL 开源数据库
项目简介：AliSQL是基于MySQL官方版本的一个分支，由阿里云数据库团队维护，目前也应用于阿里巴巴集团业务以及阿里云数据库服务。该版本在社区版的基础上做了大量的性能与功能的优化改进。尤其适合电商、云计算以及金融等行业环境。

阿里云数据库资深专家丁奇介绍，AliSQL版本在强度和广度上都经历了极大的考验。最新的AliSQL版本不仅从其他开源分支比如：Percona，MariaDB，WebScaleSQL等社区汲取精华，也沉淀了阿里巴巴多年在MySQL领域的经验和解决方案。AliSQL增加更多监控指标，并针对电商秒杀、物联网大数据压缩、金融数据安全等场景提供个性化的解决方案。

项目地址：https://github.com/alibaba/AliSQL/


## InnoSQL 网易的MySQL数据库分支
项目简介：InnoSQL是杭研开发维护的MySQL分支，目前基于MySQL 5.5。InnoSQL的主要目标是提供更好的性能以及高可用性，同时便于DBA的运维以及监控管理。其完全兼容于原版MySQL数据库，所有添加的功能 都是动态的。若不开启这些功能，与原版MySQL数据库的工作方式完全相同。

主要特性包括：
InnoDB Flash CacheMySQL ProfilerVirtual Sync Replication with group commitShare Memory for InnoDB

项目地址：https://github.com/NetEase/InnoSQL

CascaDB MySQL 存储引擎项目简介：CascaDB 是另外一个写优化的存储引擎，使用带缓冲的 B-tree 算法优化，灵感来自于 TokuDB。

项目地址：https://github.com/weicao/cascadb


## Cotton 可扩展 MySQL 服务
项目简介：Cotton（原名 Mysos）是一个 Apache Mesos 框架，用来运行 MySQL 实例！

Twitter 为了提高 MySQL 集群的可扩展性，他们正在开发一个名为Mysos的新框架。Mysos项目基于Apache Mesos构建一个面向MySQL的可扩展的数据库服务。Mesos为Mysos提供了调度、监控MySQL实例及与之通信的原语，极大的简化了MySQL集群的管理。

根据设计，Mysos 将提供如下特性：
通过多租户实现高效的硬件利用率；出现故障时保留MySQL状态，并可以自动备份到HDFS或从HDFS恢复，具备高可靠性；有一个自动化的自助服务选项，可以启动新的MySQL集群；借助MySQL主数据库故障自动转移实现高可用性；允许用户通过更改从数据库实例的数量实现MySQL集群的扩展和收缩。

项目地址：https://github.com/apache/incubator-retired-cotton


## MyRocks MySQL 分支
项目简介：RocksDB是facebook基于LevelDB实现的，目前为facebook内部大量业务提供服务。经过facebook大量工作，将RocksDB为MySQL的一个存储引擎移植到MySQL，称之为MyRocks。经过两年的发展，MyRocks已经比较成熟（RC阶段），现已进入了facebook MySQL的主分支了。MyRocks是开源的,参见git 。下面对MyRocks做一个简单介绍。

项目地址：https://github.com/facebook/mysql-5.6/


## WebScaleSQL MySQL开源通用分支
项目简介：WebScaleSQL 是由FB/谷歌/LinkedIn/Twitter四家公司将共享一组改编自上游MySQL的开源通用分支。该项目包括了来自这四家公司的MySQL工程师团队的工作成果，由于它是开源的，因此其他感兴趣的个人和公司也能够基于自身的资源和规模进行定制。

Facebook公布了到目前为止，其工程师为WebScaleSQL新分支所做的改动：
面向内建测试系统的一个自动化框架，可呈现每次改动(change)、运行(run)和发布(publish)的结果；一套完整的压力测试套件，以及一个自动化性能测试原型；对MySQL现有测试中发现的问题架构代码做出了一些改动，以避免可能导致的失败或错误；WebScaleSQL的性能改进，包括缓冲池清洗/buffer pool flushing，对某些查询类型的优化，以及支持NUMA交错政策的支持等；可使WebScaleSQL的true web scale更易用的新功能，如super_read_only、以及指定次秒级客户端超时时间(sub-second clients timeouts)的支持。

项目地址：https://github.com/webscalesql/webscalesql-5.6
