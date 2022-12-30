# JMX

## Mbean

用JMX把需要配置的属性集中在一个类中，然后写一个MBean，再进行相关配置。另外JMX还提供了一个工具页，以方便我们对参数值进行修改。

```java
final ObjectName beanConfigName = new ObjectName("com.zaxxer.hikari:type=PoolConfig (" + poolName + ")");
final ObjectName beanPoolName = new ObjectName("com.zaxxer.hikari:type=Pool (" + poolName + ")");
if (!mBeanServer.isRegistered(beanConfigName)) {
    mBeanServer.registerMBean(config, beanConfigName);
    mBeanServer.registerMBean(hikariPool, beanPoolName);
}
```

