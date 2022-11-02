## Spring API

### 基于插口 BeanPostProcessor 实现的一些 Spring 高级特性
* `AnnotationAwareAspectJAutoProxyCreator` -> AOP 接入 Spring 核心扩展点，带有 @Aspect 将在这里 `InstantiationAwareBeanPostProcessor` 生成代理对象
>```java
>AnnotationAwareAspectJAutoProxyCreator  extends... AbstractAutoProxyCreator
>AbstractAutoProxyCreator implements SmartInstantiationAwareBeanPostProcessor
>SmartInstantiationAwareBeanPostProcessor extends InstantiationAwareBeanPostProcessor
>
>```
>
>**InstanitiationAwareBeanPostProcessor** 含有 6 个扩展点
>
>* postProcessBeforeInstantiation // 实例化之前
>* postProcessAfterInstantiation // 实例化之后
>* postProcessProperties // 依赖自动注入之前
>* postProcessPropertyValues // 依赖自动注入之后
>* postProcessBeforeInitialization  // 初始化之前
>* postProcessAfterInitialization // 初始化之后
>* 
>
* `AsyncAnnotationBeanPostProcessor` (extends `AdviceModeImportSelector`) -> @Async ROLE_INFRASTRUCTURE
* `ProxyTransactionManagementConfiguration` (extends `AdviceModeImportSelector`) -> @Transcation
>> * `@Transcation` 声明式事务依赖 AOP, 核心原理是通过 @EnableTransactionManagement-> import(`TransactionManagementConfigurationSelector`->)`ProxyTransactionManagementConfiguration` -> 注入声明式事务的advisor BeanFactoryTransactionAttributeSourceAdvisor -> 通过advisor aop 完成事务的添加 `BeanFactoryTransactionAttributeSourceAdvisor` 

### AOP
#### Spring AOP 的实现是通过动态代理。核心是添加一堆的advisor (切点+通知)（@Aspect 切面会被解析成List<Advisor>）, 声明式事务 本质 添加一个 advisor 
#### 代码






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