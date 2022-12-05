# ThreadLocal

* 在多线程访问同一份数据量时，会产生线程安全问题，ThreadLocal 以创建副本的方式来避免线程冲突。空间换时间的思想

## Spring 中的应用

* Spring 中事务功能便用到了 ThreadLocal，缓存事务数据库连接（ConnectionHodler）、事务信息（隔离级别、事务名、是否只读、事务激活状态）。用与实现事务传播行为。TransactionSynchronizationManager
* Spring-Security 中的登录会话信息也是以 ThreadLocal 保存，在执行业务逻辑，使用异步线程处理IO逻辑时 会在异步线程找不到 会话信息


## 
