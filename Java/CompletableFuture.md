## CompletableFuture

### 并行加载的实现方式

从IO模型上分为**同步模型**和**异步模型**

#### 同步模型的缺陷

- **CPU资源大量浪费在阻塞等待上**，导致CPU资源利用率低。在Java 8之前，一般会通过回调的方式来减少阻塞，但是大量使用回调，又引发臭名昭著的**回调地狱**问题，导致代码可读性和可维护性大大降低。
- **为了增加并发度，会引入更多额外的线程池**，随着CPU调度线程数的增加，会导致更严重的资源争用，宝贵的CPU资源被损耗在上下文切换上，而且线程本身也会占用系统资源，且不能无限增加。

### NIO 异步模型

使用 CompletableFuture 相较于 Future、RxJava、Reactor 具有 学习成本低、满足我们异步、可组合的需求

假设有三个操作step1、step2、step3存在依赖关系，其中step3的执行依赖step1和step2的结果。

```java
   CompletableFuture<String> step1 = CompletableFuture.supplyAsync(() -> "123");
        CompletableFuture<String> step2 = CompletableFuture.supplyAsync(() -> "abc");
        uCompletableFuture.thenCombine(stringCompletableFuture, (s1, s2) -> {
            return s1 + s2;
        }).thenAccept(s -> {
            System.out.println(s);
        });
```

### 设计思想

类似“观察者模式”的设计思想

每个 Completable 可以看做一个被观察者，其内部有一个 Completion 类型的链表成员变量 stack。

<img src="https://p1.meituan.net/travelcube/606323a07fb7e31cb91f46c879d99b8d735272.gif" />
