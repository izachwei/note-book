##### SpringBoot 项目特性
###### springboot打包排除掉resources下的html,css,js,jpg图片等资源文件
* POM 文件修改 `<resources>...</resources>` 配置
```
<resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                	<include>**/*.properties</include>
                	<include>**/*.xml</include>
                </includes>
               <!--  <excludes>
                	 <exclude>**/*.css</exclude>
                    <exclude>**/*.js</exclude>
                    <exclude>**/*.html</exclude>
                </excludes> -->
                <filtering>false</filtering>
            </resource>
		</resources>
```

###### SpringBoot 项目创建非Web项目
* 是可以的，在Idea中new project 可以选择其他类型的项目
##### SpringBoot 禁用某个装配功能
###### 数据源，邮件，这些都是提供了自动配置的，我们需要排除Spring Boot的自动化配置，交给我们自己来自定义，该如何做呢?
* 使用@SpringBootApplication注解的时候，使用exclude 属性进行排除指定的类。

```java
@springBootApplication(exclude = {DataSourceAutoConfiguration.class,MaillSenderAutoConfiguration.class})
public class Application {

}
```

* 单独使用@EnableAutoconfiguration注解的时候。
```java
@EnableAutoConfiguration( exclude = { DataSourceAutoConfiguration.class,MaillSenderAutoConfiguration.class})
public class Application {
}
```
* 在配置文件中排除
```yml
spring:
  autoconfigure:
    exclude:
      - org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
```


##### SpringBoot是如何整合SpringMVC的？
```java
1.启动类上的@SpringBootApplication注解中包括了一个@EnableAutoConfiguration注解，它的作用是开启SpringBoot自动配置
2.这个自动配置注解 @EnableAutoConfiguration注解 上导入了一个选择器(@Impot(AutoConfigurationImportSelector.class),
3.这个导入选择器使用 SpringFactoriesLoader ，加载了 classpath下的jar中的spring.factories 中的很多的自动配置的类(全限定名)

@Override
  public String[] selectImports(AnnotationMetadata annotationMetadata) {
    if (!isEnabled(annotationMetadata)) {
      return NO_IMPORTS;
    }
    AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(annotationMetadata);
    return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
  }


4.比如说：DispatcherServletAutoConfiguration 就是对前端控制器的自动配置； WebMvcAutoConfiguration ；就是对web的自动配置 ； HttpEncodingAutoConfiguration 是针对于编码过滤器的配置 ；

DataSourceAutoConfiguration 数据源的自动配置； RedisAutoConfiguration redis的自动配置；
5.比如对于前端控制器就在 DispatcherServletAutoConfiguration通过定义Bea的方式就把DispatcherServlet定义好了，我们就不用配置了
```

###### WebMvcAutoConfiguration
* 创建 DispatcherServlet (`DispatcherServletConfiguration`)
```java
@Bean(name = DEFAULT_DISPATCHER_SERVLET_BEAN_NAME)
  public DispatcherServlet dispatcherServlet(WebMvcProperties webMvcProperties) {
    DispatcherServlet dispatcherServlet = new DispatcherServlet();
    dispatcherServlet.setDispatchOptionsRequest(webMvcProperties.isDispatchOptionsRequest());
    dispatcherServlet.setDispatchTraceRequest(webMvcProperties.isDispatchTraceRequest());
    dispatcherServlet.setThrowExceptionIfNoHandlerFound(webMvcProperties.isThrowExceptionIfNoHandlerFound());
    dispatcherServlet.setPublishEvents(webMvcProperties.isPublishRequestHandledEvents());
    dispatcherServlet.setEnableLoggingRequestDetails(webMvcProperties.isLogRequestDetails());
    return dispatcherServlet;
  }

```

* 通过 DispatcherServletRegistrationBean 注入 `DispatcherServlet` ,原理： RegistrationBean (注册 servlet 3.0+) 实现了 `ServletContextInitializer` 接口 servletContext 初始化生命周期方法
