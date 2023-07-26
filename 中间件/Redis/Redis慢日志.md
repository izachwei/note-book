## Redis 慢日志

Redis 也有慢日志记录，方便排查线上问题。

### redis的慢日志配置

在上图中我们可以看到redis的slowlog(慢日志)的默认配置,其中有两个地方需要了解:

1. slowlog-log-slower-than: 这个配置表示执行时间,在redis执行命令时，只要有任何一条命令超过了设置的执行时间，redis就会将这条命令记录到慢日志中。
2. slowlog-max-len: 这个配置表示该日志的大小，redis的日志是一个FIFO队列，当该队列达到设定的长度大小之后，后面记录的日志会覆盖掉之前记录的日志

### 第二种查看redis的慢日志配置:

使用命令 config get slowlog*，可以查看到当前redis的slowlog的配置信息:

`127.0.0.1:6379> config get slowlog*`
`1) "slowlog-max-len"`
`2) "128"`
`3) "slowlog-log-slower-than"`
`4) "10"`

使用命令 **slowlog get [n]** 获取前n条[慢查询](https://so.csdn.net/so/search?q=慢查询&spm=1001.2101.3001.7020)日志，不指定长度获取全部。
