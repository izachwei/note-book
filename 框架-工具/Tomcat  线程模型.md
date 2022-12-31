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

Tomcat 线程池

重写了 LinkedBlockingQueue ，TaskQueue  

```java
class TaskQueue extends LinkedBlockingQueue{
    @Override
    public boolean offer(Runnable o) {
      //we can't do any checks
        if (parent==null) {
            return super.offer(o);
        }
        //we are maxed out on threads, simply queue the object
        if (parent.getPoolSize() == parent.getMaximumPoolSize()) {
            return super.offer(o);
        }
        //we have idle threads, just add it to the queue
        if (parent.getSubmittedCount()<=(parent.getPoolSize())) {
            return super.offer(o);
        }
        //if we have less threads than maximum force creation of a new thread
        if (parent.getPoolSize()<parent.getMaximumPoolSize()) {
            return false;
        }
        //if we reached here, we need to add it to the queue
        return super.offer(o);
    }
}
```

重写 offer 方法，没有达到线程池的最大线程数，则返回false ，继续创建线程，如果大于则加入阻塞队列，队列满了 则抛出 拒绝策略，如果 已提交的任务数小于当前线程池中的线程数 则加入队列（不创建新的线程）

Tomcat 线程池扩展了原生的 ThreadPoolExecutor，通过重写 execute 方法实现了自己的任务处理逻辑：

 ● 前 corePoolSize 个任务时，来一个任务就创建一个新线程。
 ● 还有任务提交，直接放到队列，队列满了，但是没有达到最大线程池数则创建临时线程救火。
 ● 线程总线数达到 maximumPoolSize ，继续尝试把任务放到队列中。如果队列也满了，插入任务失败，才执行拒绝策略。
最大的差别在于 Tomcat 在线程总数达到最大数时，不是立即执行拒绝策略，而是再尝试向任务队列添加任务，添加失败后再执行拒绝策略。
