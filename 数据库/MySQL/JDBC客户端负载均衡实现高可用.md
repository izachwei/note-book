## JDBC客户端负载均衡实现高可用

### failover协议

  Mysql Connector 支持 failover 协议：即 Client 链接失效时，将会尝试与其他 host 建立链接，这个过程对上层应用是透明的。Failover协议是“Multi-Host”链接模式中最基础的协议，“load balancing”、“replication”、“farbic”协议都基于Failover协议。其URL格式如下：	

```
jdbc:mysql://[primary-host]:[port],[secondary-host]:[port],.../[database]?[property=<value>]&[property=<value>]
```

**failOverReadOnly=false**   故障转移后，节点是否只读，默认为ture 建议设置成 false

Host列表中有两种hosts：primary（master）和secondaries（slaves），priamry需要位于hosts列表的第一个；当创建一个新的connection时，driver会首先尝试与primary链接，如果primary链接异常，将会依次与secondaries建立链接直到成功为止；即使与primary的链接失效，但是driver并不会丢失primary的特殊状态：比如它的访问模式、优先级等。

###  LoadBalance 协议

`jdbc:mysql:loadbalance://127.0.0.1:3306,127.0.0.1:4306,127.0.0.1:5306/mydb`

### Replication 协议

` jdbc:mysql:replication://[master-host]:[port],[slave-host]:[port],.../database?[property=<value>]`

 **1、选择合适的Driver：对于Failover和LB协议，可以选用NonRegisterDriver；对于replication协议，我们需要使用ReplicationDriver。对于单点host，普通的Driver类即可。**





## 坑

jdbc:mysql://node1:3306,node2:3306,node3:3306/testdb?failOverReadOnly=false

init status:

node1/2/3都ok, jdbc连接的是node1进行crud都ok, node2/3为standby, jdbc不访问他们

step1:

shutdown node1后, primary自动变为node2, 此时jdbc连接通过failover连接node2进行crud没问题

step2:

node1主机故障处理完毕, 启动node1的mysql, 这里就是罪恶的根源, 数据会全部乱掉

原因是此时刚刚启动node1的mysql, 还未加入到Group Replication(执行相关命令), 但是jdbc不知道, 因为连接串第一个是node1, 他认为能联通就是在node1上进行crud, 而更新操作不会同步到node2/3,这明显就是错误的, 应该仍然连接此时的primary节点就是node2, 不知道有没有好办法

原文链接：https://blog.csdn.net/weixin_36057741/article/details/113636609