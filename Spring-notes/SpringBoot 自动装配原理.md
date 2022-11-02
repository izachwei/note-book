## SpringBoot 自动装配原理

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

### **总结

1、注解@SpringBootApplication中含有三个注解，其中@EnabelAutoConfiguration和自动配置有关；

2、@EnableAutoConfiguration会读取所有jar下META-INF/spring.factories文件的内容，获取”org.springframework.boot.autoconfigure.EnableAutoConfiguration“的配置，把这些配置注入到容器；

3、@EnableAutoConfiguration注入的类是否生效，需要看其上面的注解，主要配合@ConditionaleXXX注解使用；