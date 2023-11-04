MVCC 机制保证  RC RR

### Read View

**read view**就是事务进行快照读操作的时候生成的读视图（并不是每次快照读都会生成），read view主要包含以下四个属性：

* trx_list：活跃的事务ID列表；
* creator_trx_id：当前事务ID；
* up_limit_id：记录trx_list中最小的事务ID；
* low_limit_id：尚未分配的下一个事务ID。

当读取一条数据时，会先将这条数据的最新记录的DB_TRX_ID取出来，和read view中的属性进行可用性判断，如果不符合可见性的话，那就会通过DB_ROLL_PTR去undo log中取出上一个版本记录中的事务ID再进行可见性判断，以此类推，直到找到可见的记录为止。

可见性判断是使用可见性算法实现的，具体判断过程如下： 

1. 如果 DB_TRX_ID < up_limit_id 或 DB_TRX_ID == creator_trx_id，则该记录可见；
2. 如果 DB_TRX_ID >= low_limit_id，则该记录不可见；
3. 判断DB_TRX_ID是否在trx_list中；如果在，说明生成read view的时候，修改这个记录的事务还没提交，所以该记录不可见；如果不在trx_list中，说明生成read view之前修改这个记录的事务已经提交了，所以该记录可见。
   

RC 隔离级别下

每次查询都会产生一个新的 read view （当前读）

RR 隔离级别下

在一个事务内，只会生成一次 read view（快照读）

