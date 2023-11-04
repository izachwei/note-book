## 常用工具包 - Java

### Redis  Replicator

Redis Replicator是一款RDB解析以及AOF解析的工具. 此工具完整实现了Redis Replication协议. 支持SYNC, PSYNC, PSYNC2等三种同步命令. 还支持远程RDB文件备份以及数据同步等功能. 此文中提到的 `命令` 特指Redis中的写(比如 `set`,`hmset`)命令，不包括读命令(比如 `get`,`hmget`), 支持的redis版本范围从2.6到6.2。

```xml
<dependency>
    <groupId>com.moilioncircle</groupId>
    <artifactId>redis-replicator</artifactId>
    <version>2.6.2</version>
</dependency>
```

