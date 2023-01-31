# Nacos如何实现动态配置刷新

1. 利用 @RefreshScope，@RefreshScope 底层通过 @Scope("refresh")、ScopedProxyMode proxyMode()  来实现当前Bean 的配置刷新。底层会对当前Bean生成一个代理对象，@Scope("refresh") 的Bean 会有一个单独的Map进行缓存，独立于 singletonObjects ，
2. 核心发送了一个RefreshEvent事件：applicationContext.publishEvent(new RefreshEvent(this, null, "Refresh Nacos config"));
3. RefreshEventListener 监听该事件，利用`ContextRefresher.refresh()`刷新环境配置信息、清除 @RefresScope Bean缓存，注意：`ContextRefresher` refresh方法中 refreshEnvironment方法会重启Application容器
4. 客户端拿到最新的配置后,将配置更新到Spring容器中的Environment对象中.
5. 清除bean缓存,这里的缓存和通常说的三级缓存有点不一样,是BeanLifecycleWrapperCache，缓存了所有@RefreshScope 标注的对象.
6. 对所有需要动态更新的bean重新执行实例化,初始化的过程,执行完毕重新放到缓存
