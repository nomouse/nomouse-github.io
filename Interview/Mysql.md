# 概述
## 概念

## 基本架构
1. Server层
   维护连接，接受、解释、优化、执行SQL语句，日志记录，主从同步等
   a. 连接器。权限，连接建立和管理
   b. 分析器。词法分析，语法分析
   c. 优化器。
   b. 执行器
   
2. Engine层
   数据储存、管理、检索，事务实现等。
   a. Myisam
   b. Innodb


# 高级应用

## 高并发数据库

[AliSQL秒杀场景测试报告样例](https://github.com/alibaba/AliSQL/wiki/AliSQL-Performance-benchmark-for-inventory)

## 热点合并update



## 使用hint

### 第一个是如何控制和限制热点

第一个是如何控制和限制进入到MTSQL InnoDB事务引擎层的热点数量，这里主要涉及一个热点ID的标识，使用QUEUE_ON_PK_H标识

QUEUE_ON_PK_H：

代码块： update /*+ QUEUE_ON_PK_H(12345) */ tb1 set amount = amount - 1 where id=12345 and amount > 10

如上所示，热点ID的标识依靠用户在SQL中以hint的方式注入，该方式有两大优点：

业务人员可以根据业务自身情况，只对可能有热点更新的语句使用该优化。 不支持该hint的社区版本，也能正常执行该SQL，只是少了秒杀效果。

实现上，数据库内部提取SQL中的hint以及对应的数值，每个数值都对应一个等待队列，如果该数值无法在内部的哈希表中查找到，则根据该值创建一个等待队列，然后直接执行，否则在该等待队列上休眠。在update执行完之后，唤醒相关等待线程继续执行。

### 如何降低update语句与commit或者rollback语句之间的时间差。

对于一般业务来说，事务提交需要显式的执行set
auto_commit=1或者commit语句，那么热点行持有的锁，最少会持续一个网络来回时间。特别是应用服务器与数据库服务器不在同一个机房时，网络来回时间大概为2ms，由于行锁的存在，那么对于单行更新tps的理论上限为500。如何减少或避免这个网络交互开销就非常重要。

通过调查发现，业务一般会根据update语句是否成功，来决定业务逻辑。比如在扣库存情况下，如果update成功执行，即affect_row为1，则提交事务，否则认为失败，则回滚事务。因此如果我们在SQL中通过hint指导数据库进行相应操作，就可以尽早的释放锁，提升数据库TPS的理论上限。为此我们引入了三个SQL
Hint来用传递指令，如下表所示：

# SQL Hint：

COMMIT_ON_SUCCESS_H ：如果SQL执行不报错，并且更新记录数符合TARGET_AFFECT_ROW_H要求，则自动提交事务。 ROLLBACK_ON_FAIL_H
：如果SQL执行失败，或更新记录数不符合TARGET_AFFECT_ROW_H要求，则自动回滚事务。 TARGET_AFFECT_ROW_H
：用来指定Update必须成功更新的记录数，如果记录数不匹配视为更新不成功。

在不加上面3个hint的情况下，需要业务主动提交。如果加上COMMIT_ON_SUCCESS_H(1) TARGET_AFFECT_ROW_H(1)
，表示当update成功更新一行时，则立即提交，否则等待业务主动发起commit或者rollback操作；如果加上ROLLBACK_ON_FAIL_H(1) TARGET_AFFECT_ROW_H(
1)
，则表示如果update更新的行数不为1，则立即回滚，否则等待业务主动发起commit或者rollback操作；如果三个hint一起加上，则表示假如update更新的行数为1，则立即提交，否则立即回滚。

