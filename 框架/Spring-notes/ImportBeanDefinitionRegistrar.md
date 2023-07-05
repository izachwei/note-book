# SpringBoot扩展点 ImportBeanDefinitionRegistrar

主要用于框架与Spring 对接时注入核心类

一般配合 @Import 使用，用于在 Spring 启动时创建 BeanDefinition 并导入 BeanDefinition

#### 主流框架使用案例：

- Mybatis   @MapperScan 导入 MapperScannerConfigurer  用于 (ClassPathMapperScanner) 扫描 @Mapper 利用  (BeanDefinitionRegistryPostProcesso) 生成 Mapper接口代理类 (MapperFactoryBean )]

> ```java
> 有两个渠道会注入 MapperScannerConfigurer  
> 1. @MapperScan 注解中的 @Import
> 2. mybatis-spring-boot-autoconfigure  自动配置 
>   @ConditionalOnMissingBean({ MapperFactoryBean.class, MapperScannerConfigurer.class })
>   MapperScannerRegistrarNotFoundConfiguration   
>   注入条件是当前不存在 MapperScannerConfigurer，意思说 @MapperScan 配置优先级最高。也符合自动配置中约定大于配置的思想。
> 
> ```
>
> 

- TkMybatis 
- Nacos
- Apollo @EnableApolloConfig -> @Import(ApolloConfigRegistrar.class) -> ApolloConfigRegistrar implements ImportBeanDefinitionRegistrar -> 注入核心控制类
- OpenFegin

