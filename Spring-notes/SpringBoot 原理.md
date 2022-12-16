# SpringBoot 原理
## SpringBoot启动流程
1. 创建 SpringApplication ：读取 spring.facories 中的 ApplicationContextInitializer、ApplicationListener、判断 Web容器类型（webflux、servlet）
2. 执行 Run 方法
3. 读取 环境配置信息、命令行参数
4. 创建对应的ServletWebServerApplicationContext
5. 预初始化 context：读取配置类
6. 调用refresh加载 context（ioc容器）
> 6.1 加载所有自动配置类 （@SpringBootApplication、@EableAutoConfiguration）
>
> 6.2 创建容器
7. 在这个启动流程中，会发布多个applicationEvent （ApplicationListener） ；调用监听器 ApplicationContextInitializer
[Spirng扩展点](Spring扩展点.md)

## 自动装配原理

### 原理

```java
// 标识 SpringBoot 应用注解
@SpringBootApplication
    // 自动装配的核心注解
    @EnableAutoConfiguration 
		// 默认注册当前 Springboot类包下和子包下的所有类
		@AutoConfigurationPackage
			// 读取 META-INF/spring.factories 下的自动配置类 （starter 自动装配核心）
			// 利用 import 导入类的特性，注册配置类
			@Import(AutoConfigurationImportSelector.class)
				List<String> configurations = SpringFactoriesLoader.loadFactoryNames(
						getSpringFactoriesLoaderFactoryClass(), getBeanClassLoader());
			
```

### 结论

SpringBoot不需要写配置文件的原因是，SpringBoot所有配置都是在启动的时候进行扫描并加载，SpringBoot的所有自动配置类都在Spring.factories里面，但是不一定会生效，生效前要判断条件是否成立，只要导入了对应的start，就有对应的启动器，有了启动器就能帮我们进行自动配置类。

### 总结

1、注解@SpringBootApplication中含有三个注解，其中@EnabelAutoConfiguration和自动配置有关；

2、@EnableAutoConfiguration会读取所有jar下META-INF/spring.factories文件的内容，获取”org.springframework.boot.autoconfigure.EnableAutoConfiguration“的配置，把这些配置注入到容器；

3、`@EnableAutoConfiguration`注入的类是否生效，需要看其上面的注解，主要配合@ConditionalXXX注解使用；

## SpringBoot 怎么集成 Tomcat 

SpringBoot 启动过程中会创建 ServletWebServerContext ，一般使用注解方式启动，创建的是 AnotationServletWebServerContext （extends ServletWebServerContext） ，该对象下有个成员变量 webServer ，具体的实现类：`TomcatWebServer`

TomcatWebServer 是 Spring 对原生 Tomcat 的包装类，其中就包含了 Tomcat 的功能。

