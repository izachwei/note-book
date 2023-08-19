## Spring 事务
### Spring 事务失效场景之Bean对象调用自己方法
* Bean 对象调用自己的方法，会导致自己的方法事务失效
* 多线程导致事务失效，这样会导致两个方法不在同一个线程中，获取到的数据库连接不一样，从而是两个不同的事务。如果想doOtherThing方法中抛了异常，add方法也回滚是不可能的
* Spring 声明式事务依赖的是 动态代理对象，在动态代理对象中this.method 调用的不是代理方法而是调用对象本身的方法，导致没有添加事务方法 ，形成了事务失效
*  @Transactional 注解标记的方法只能是 public，其他访问权限的方法事务将不起作用。
* 异常被吃掉

#### 为什么在我们日常开发中事务失效了，但是没有暴露出线上问题呢？

1. 因为事务的传播机制，Spring默认的事务行为是：`propagation_required`,
客户端与调用端存在同一个事务中，如果被调用端发生异常，那么调用端和被调用端事务都将回滚


### Spring 支持7种事务传播行为
```
propagation_required（xml文件中为required)	表示当前方法必须在一个具有事务的上下文中运行，如有客户端有事务在进行，那么被调用端将在该事务中运行，否则的话重新开启一个事务。（如果被调用端发生异常，那么调用端和被调用端事务都将回滚
propagation_supports(xml文件中为supports）	表示当前方法不必需要具有一个事务上下文，但是如果有一个事务的话，它也可以在这个事务中运行
propagation_mandatory(xml文件中为mandatory）	表示当前方法必须在一个事务中运行，如果没有事务，将抛出异常
propagation_nested(xml文件中为nested)	表示如果当前方法正有一个事务在运行中，则该方法应该运行在一个嵌套事务中，被嵌套的事务可以独立于被封装的事务中进行提交或者回滚。如果封装事务存在，并且外层事务抛出异常回滚，那么内层事务必须回滚，反之，内层事务并不影响外层事务。如果封装事务不存在，则同propagation_required的一样
propagation_never（xml文件中为never)	表示当方法务不应该在一个事务中运行，如果存在一个事务，则抛出异常
propagation_requires_new(xml文件中为requires_new）	表示当前方法必须运行在它自己的事务中。一个新的事务将启动，而且如果有一个现有的事务在运行的话，则这个方法将在运行期被挂起，直到新的事务提交或者回滚才恢复执行
propagation_not_supported（xml文件中为not_supported）	表示该方法不应该在一个事务中运行。如果有一个事务正在运行，它将在运行期被挂起，直到这个事务提交或者回滚才恢复执行
```

## TransactionSynchronizationManager（事务信息的管理器，用于实现事务传播行为）

Spring嵌套事务依赖Spring实现事务挂起功能（`suspend()`）底层实现：（事务的执行过程，无论是嵌套还是其他的事务传播行为，都是同一个线程因此需要事务的挂起）

其实就是将线程变量里面的事务信息拿出来，再置空。 待事务提交或回滚后再放回线程变量中  

TransactionSynchronizationManager 中的事务信息拿出来，存放在 SuspendedResourceHolder，等嵌套事务执行完，在将其放回 TransactionSynchronizationManager 

### 嵌套事务还有一个特性，外层事务影响内层事务，内层不会影响外层，怎么实现？

利用 JDBC  **Savepoint**，JDBC Savepoint帮我们在事务中创建检查点（checkpoint），这样就可以回滚到指定点

### tip

#### Spring 引入事务：
* starter
```XML
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

* jar
```XML
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
</dependency>
```