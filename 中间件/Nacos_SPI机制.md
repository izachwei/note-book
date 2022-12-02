# Nacos SPI 机制

* Nacos 源码中使用了大量的 SPI 来实现自定义扩展 
* 定义了 NacosServiceLoader 用来加载 SPI 服务，底层使用的还是 JDK ServeicLoader。其核心定义了一个 Map 缓存 ServiceLoader 加载的 Class ，避免二次加载，提高效率

## NacosApplicationListener 

提供在 Nacos application 启动 ，环境准备，上下文准备，容器刷新时监听方法

自定义 SpringApplicationRunListener 实现接口 Spring 原生的 应用监听器接口 SpringApplicationRunListener。字段 `nacosApplicationListeners` 利用 SPI 机制加载了扩展的所有 NacosApplicationListener  实例，SpringApplicationRunListener 实现的所有监听方法都是依次调用 `nacosApplicationListeners` 的方法。

### 默认两个实现类

### StartingApplicationListener

核心功能：注入 Spring 容器环境Bean，加载自定义配置文件（`spring.config.additional-location`），加载系统参数， 加载环境参数，

包括自定义的环境参数 CustomEnvironmentPluginManager 

### LoggingApplicationListener

* 用于初始化日志配置信息，META-INF/logback/nacos.xml

## CustomEnvironmentPluginService

CustomEnvironmentPluginManager 字段 SERVICE_LIST SPI  加载自定义环境 CustomEnvironmentPluginService

### 默认无实现
