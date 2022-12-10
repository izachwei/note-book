# Nacos 特性

## Config 模块

* 是否设置了使用外部存储，是则使用外部存储，否  则判断是否开启内嵌数据源存储，默认单机模式则开启内嵌数据源存储，若没有开启则自动升级为外置数据源存储，和之前一样

* ```java
  // 内嵌数据源
  LocalDataSourceServiceImpl
  // 外部数据源
  ExternalDataSourceServiceImpl
  ```

## Auth 模块

* AuthFilter 实现接口的权限验证，注解 @Secued

## Core 模块

* `RequestHandlerRegistry` 注册定义的容器中的 `RequestHandler`

##### 监控数据指标

* `NacosMeterRegistryCenter `  基于 `CompositeMeterRegistry` （micrometer）实现

## Console 模块

* 用于管理nacos，监控，展示Namespace、服务状况

