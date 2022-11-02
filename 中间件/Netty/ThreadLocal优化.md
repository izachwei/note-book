## FastThreadLocal

### 核心原理

```java
// 定义自己的 Thread
FastThreadLocalThread 
	private InternalThreadLocalMap threadLocalMap;

FastThreadLocal 初始化时获取一个定值的Index 
    index = InternalThreadLocalMap.nextVariableIndex()
    	index 便是 threadLocalMap 查找对应元素的索引
```

 

### 优化点

#### 高效查找

```java
FastThradLocal 
    Object[]
    	Object[0] == Set<FastThreadLocal<?>> (addToVariablesToRemove) // 方便清除
```

#### 安全（内存泄露）

```java
ThreadLocal Key 弱引用存在内存泄露的问题，虽然针对这个问题 JDK 有优化，在 set()/get() 会清除无引用的 value，这种主动清除的方式会有一定的性能消耗。
针对这个问题，Netty 定义 FastThreadLocal ，定义 FastThreadLocalRunnable 在多线程的情况下，线程运行完自动调用 FastThreadLocal.removeAll() 清除相关内存引用（Set<FastThreadLocal<?>>）
// 利用装饰者模式 保证 Runnable
@Override
public void run() {
    try {
        runnable.run();
    } finally {
        FastThreadLocal.removeAll();
    }
}
```

