## 利用 Spring 扩展点实现框架对接的套路

在 Java 技术生态中 Spring 作为最流行的开发框架。很多中间件、框架都对接了 Spring。在我看了许多项目后，我总结了一下套路。

1. 利用 BeanPostProcessor 收集目标 Bean（打了标记的Bean对象，便于后面统一处理）
2. 定义一个 Component  用于存放目标 Bean
3. 根据场景统一处理收集的Bean。例如：动态线程池、动态配置监听器、监听器等。

