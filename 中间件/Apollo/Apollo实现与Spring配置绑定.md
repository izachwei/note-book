# Apollo实现与Spring配置绑定

## Spring 容器初始化时，怎么实现读取远程配置参数

### SpringBoot

1. 利用` ApplicationContextInitializer `，（ "apollo.bootstrap.enabled" : true ）在Spring容器刷新前读取远程配置参数，刷新Environment配置信

### Spring

1. @EnableApolloConfig -> @Import(@Import.class) -> 导入 PropertySourcesProcessor implements `BeanFactoryPostProcessor` ->  postProcessBeanFactory() 读取远程配置参数，刷新Environment配置信息

2. ApolloAutoConfiguration -> "apollo.bootstrap.enabled" : true -> 导入 ConfigPropertySourcesProcessor implements `BeanFactoryPostProcessor`

   `导入 ApolloProcessor`

## 怎么实现动态配置刷新

利用`BeanPostProcessor`（ postProcessBeforeInitialization）

1. 在 Bean 初始化前收集当前 Bean 中 （SpringValueProcessor）@Value 打了标记的字段、方法 封装成 `SpringValue` (Bean、Field、Method)到 SpringValueRegistry 作为缓存。
2. 实现 Apollo 配置更新监听器：AutoUpdateConfigChangeListener implements ConfigChangeListener ，读取 ChangeEvent 具体修改的字段，使用 步骤1 中产生的动态配置字段/方法的缓存获取对应的SpringValue，通过反射修改字段、方法参数值
