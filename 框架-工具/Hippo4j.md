## Hippo4j 

### 简介
* 动态可观测线程池框架
* Hippo-4J 通过对 JDK 线程池增强，以及扩展三方框架底层线程池等功能，为业务系统提高线上运行保障能力。
⚡️ 动态变更 - 应用运行时动态变更线程池参数，包括不限于：核心、最大线程数、阻塞队列容量、拒绝策略等；
...

### 核心原理 （以 Nacos 为例）
* 通过 Spring 的事件机制 
* NacosRefresherHandler (`AbstractCoreThreadPoolDynamicRefresh`) 使用 Spring Bean 生命周期的钩子方法 afterPropertiesSet 添加 nacos(`configServer`） 配置 Nacos 变更的监听器 ->  Web容器线程池(TomcatWebThreadPoolHandler)/ThreadPoolExecutor 参数变更 发布事件 `Hippo4jCoreDynamicRefreshEvent` , 由监听器 `WebExecutorListener`/`ExecutorsListener` 监听事件，获取 Spring BeanFactory 中对应线程池实例 修改参数

#### ExecutorsListener
* 利用 Spring BeanPostProcessor `DynamicThreadPoolPostProcessor` , postProcessAfterInitialization 生命周期方法向全局线程池管理工具 `GlobalThreadPoolManage` 和全局线程池核心参数配置 `GlobalCoreThreadPoolManage` 注册 对应的线程池 和 线程池参数，后续修改参数直接通过 poolId 拿取对应实例进行修改参数


### 该框架大量使用了 Spring 的特性，条件加载是springboot自动配置的刚需

##### MarkerConfiguration
* starter 自动装配类 `DynamicThreadPoolCoreAutoConfiguration`, `@ConditionalOnBean(MarkerConfiguration.Marker.class)` 通过 ConditionalOnBean 主键实现开关式引入Hippo4j，@EnableDynamicThreadPool 通过 `@Import({BeforeCheckConfiguration.class, MarkerConfiguration.class})`导入 MarkerConfiguration 开关标志类



### 设计模式 

#### 策略模式

* `ConfigParserHandler` -> `ConfigParser` 
```java
public Map<Object, Object> parseConfig(String content, ConfigFileTypeEnum type) throws IOException {
	for (ConfigParser parser : PARSERS) {
	    if (parser.supports(type)) {
	        return parser.doParse(content);
	    }
	}

	return Collections.emptyMap();
}
```

#### tip 
* 利用 `SPI` 扩展机制来提供 `ConfigParser` 的实现
```java

 private ConfigParserHandler() {
    ServiceLoader<ConfigParser> loader = ServiceLoader.load(ConfigParser.class);
    for (ConfigParser configParser : loader) {
        PARSERS.add(configParser);
    }

    PARSERS.add(new PropertiesConfigParser());
    PARSERS.add(new YamlConfigParser());
}
```

#### 建造者模式

Builder