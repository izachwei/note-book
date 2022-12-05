# Spring API

## 基于插口 BeanPostProcessor 实现的一些 Spring 高级特性
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

## AOP
#### Spring AOP 的实现是通过动态代理。核心是添加一堆的advisor (切点+通知)（@Aspect 切面会被解析成List<Advisor>）, 声明式事务 本质 添加一个 advisor 
#### 代码

## spring中expose-proxy的作用与原理

先看一下几个问题，下面是段代码：

```java
public interface UserService{
	public void a();
	public void a();
}

public class UserServiceImpl implements UserService{
	@Transactional(propagation = Propagation.REQUIRED)
	public void a(){
		this.b();
	}
	@Transactional(propagation = Propagation.REQUIRED_NEW)
	public void b(){
		System.out.println("b has been called");
	}
}
```

ps:代码参照《Spring源码解析》p173页。
Q1：b中的事务会不会生效？
A1：不会，a的事务会生效，b中不会有事务，因为a中调用b属于内部调用，没有通过代理，所以不会有事务产生。
Q2：如果想要b中有事务存在，要如何做？
A2：`<aop:aspectj-autoproxy expose-proxy=“true”> ，设置expose-proxy属性为true，或者在`@EnableAspectJAutoProxy(exposeProxy = true)`  将代理暴露出来，使用AopContext.currentProxy()获取当前代理，将this.b()改为((UserService)AopContext.currentProxy()).b()
还要一个方法：

`使用cglib的方式，是可以实现代理的，会执行成功!这个也是和jdk默认的动态代理的不同点`
