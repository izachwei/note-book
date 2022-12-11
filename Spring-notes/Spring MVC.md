# Spring MVC

## Servlet容器的扩展机制

```java
/**
	生命周期由 Servlet 容器管理
	Servlet 3.0 特性，SPI 机制提供  ServletContainerInitializer 扩展点
	
*/
SpringServletContainerInitializer implements ServletContainerInitializer
    -> WebApplicationInitializer (用于自定义 提供扩展。SpringBoot web项目想要以 war 包方式部署需要用到这个机制、SpringBootServletInitializer)
/**
 生命周期由Spring容器管理，Spring web容器(ServletWebServerApplicationContext)管理，
*/
ServletContextInitializer

```

![image-20221211135720576](imgs\serveltContextInitializer.png)

```java
public class TomcatStartSpringBoot extends SpringBootServletInitializer {
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        return builder.sources(Application.class);
    }
}
```

### 创建 ServletWebServerApplicationContext 

* `AbstractApplicationContext.refresh() -> onRefresh() -> createWebServer() -> selfInitialize() 这里会读取 beanFactory 自动配置配类 `DispatcherServletAutoConfiguration` -> DispatcherServletRegistrationBean (底层实现接口`ServletContextInitializer`)-> 执行 onStartup() -> 导入DispacherServlet`
* 在第一次访问到 Servlet 时，调用 init() -> 加载HandlerMapping、HandlerAdpter
```java
onRefresh() -> initStrategies()

initMultipartResolver(context);
initLocaleResolver(context);
initThemeResolver(context);
initHandlerMappings(context);
initHandlerAdapters(context);
initHandlerExceptionResolvers(context);
initRequestToViewNameTranslator(context);
initViewResolvers(context);
initFlashMapManager(context);

```

* tip

* Spring MVC 的配置（默认值）：

* ```yml
  spring:
    mvc:
      servlet:
        load-on-startup: 2
  ```

* 

* servlet init 方法的调用时机，依赖 load-on-startup的配置信息

* init 方法是随 Servlet 实例化而被调用的，因为 load-on-startup 就是用来设置 Servlet 实例化时间的。

  * 因此，init 方法执行的时刻有两种：

  * （1） load-on-startup 的值大于等于0，则伴随 Servlet 实例化后执行。

  * （2） load-on-startup 的值小于0 或者 不配置， 则在第一次 Servlet 请求的时候执行。

### tips

###### WebMvcAutoConfiguration

* 静态内部类 `EnableWebMvcConfiguration extends DelegatingWebMvcConfiguration` 用来 注册 DispatcherServlet 各个handler (RequestMappingHandlerMapping)
```java
@Bean
@Primary
@Override
public RequestMappingHandlerMapping requestMappingHandlerMapping(
    @Qualifier("mvcContentNegotiationManager") ContentNegotiationManager contentNegotiationManager,
    @Qualifier("mvcConversionService") FormattingConversionService conversionService,
    @Qualifier("mvcResourceUrlProvider") ResourceUrlProvider resourceUrlProvider) {
    // Must be @Primary for MvcUriComponentsBuilder to work
    return super.requestMappingHandlerMapping(contentNegotiationManager, conversionService,
                                              resourceUrlProvider);
}
```

* Controller 在注入 Bean 以后，什么时候放入 RequestMappingHandlerMapping.mappingRegistry 

  ```java
  /**
      RequestMappingHandlerMapping extends AbstractHanlerMapping 
      AbstractHanlerMapping implements InitializingBean
      在 Bean 生命周期 afterPropertiesSet() 方法 注入 Controller 中的 Method
      重写 RequestMappingHandlerMapping 中的 isHandler 方法即可注入其他注解或者类型的Bean
  **/
  @Override
  protected boolean isHandler(Class<?> beanType) {
      return (AnnotatedElementUtils.hasAnnotation(beanType, Controller.class) ||
              AnnotatedElementUtils.hasAnnotation(beanType, RequestMapping.class));
  }
  ```

  

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