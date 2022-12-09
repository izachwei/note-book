# Spring 扩展点

## ApplicationContextInitializer

这个类的主要目的就是在ConfigurableApplicationContext类型（或者子类型）的ApplicationContext做refresh之前，允许我们对ConfigurableApplicationContext的实例做进一步的设置或者处理。。

## Condition 

Spring 提供了满足条件注入Bean的能力，SpringBoot 预设常用的条件注入注解，用于实现自动装置。例如：`ConditionalOnClass`，`ConditionalOnBean`,`ConditionalOnProperty`。

如果我们想要自定义一个条件注入的类，应该怎么操作？`使用注解 @Conditional ，同时 实现接口 Condition ，将实现类传入注解 @Conditional`

## ApplicationListener （观察者模式）

订阅Spring运行中发布事件，例如：ContextRefreshedEvent 
```java
public class RequestHandlerRegistry implements ApplicationListener<ContextRefreshedEvent> {
  @Override
    public void onApplicationEvent(ContextRefreshedEvent event) {
    xxx
    }
    }
```
