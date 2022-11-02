## Tomcat  线程模型

### NioEndpoint 

#### 设计模式

* 模板设计模式 （ NioEndpoint extends AbstractEndpoint）

#### 线程模型

```java
Acceptor ( getPoller0().register(channel); )
    
    -> poller register() add(NioChannel , SelectionKey) (for in NioChannelList) 
    
    -> selector.selectedKeys() 判断 channel 状态 可写 可读
    	Executor executor = getExecutor();
            if (dispatch && executor != null) {
                executor.execute(sc);
            } else {
                sc.run();
            }
```

