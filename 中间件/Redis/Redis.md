#  Redis

## 数据结构

1. String：SDS
2. 字典（dict）：ziplist、hash
3. list：ziplist、双向链表
4. set：整数集合（int[]）、hash
5. zset：ziplist、跳表

## 特性

Redis 事务不支持原子性，持久性也需要 AOF 配置的支持，修改成 always

## 优缺点

### 优点

1. 支持的数据结构丰富
2. 基于内存操作、响应快
3. 支持主从、分布式满足高可用
4. 读写数据是单线程操作、避免了上下文切换 保证了原子性
5. 支持持久化操作、RDB AOF

### 缺点

1. 数据库容量受到物理内存的限制，内存资源较为珍贵，支持少量数据的读写，Redis适合热点数据、较少的数据量上的读写操作。
2. 主机宕机，主从同步之间存在延时，会导致丢数据
3. 在线扩容很复杂
