# ThreadLocalRandom 的坑

这是 JDK 提供的多线程环境下保证线程安全生成随机数的工具类

## 结论（使用注意点）

在线程自己的方法中调用 `ThreadLocalRandom.current() `。（这样才能保证线程本身生成随机数的独立性）

禁止将 `ThreadLocalRandom` 提升为全局变量，例如：`public static final ThreadLocalRandom random =  ThreadLocalRandom.current()` 这样会导致使用该变量的多线程生成随机策略一样的，核心底层随机策略的种子值 `probe` 不会再次初始化。

使用 ThreadLocalRandom，不能多个线程共享某线程返回的 ThreadLocalRandom 实例，需要每个线程分别调用`ThreadLocalRandom.current().nextInt(1000)`，这样才能初始化每个线程的种子，才可以起到随机数的效果。
