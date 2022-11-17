## SpringBoot扩展点之EnvironmentPostProcessor

### 使用

* 实现 `EnvironmentPostProcessor` 接口，重写 `postProcessEnvironment`

* 在 classpath:META-INF/spring.factories 中定义 org.springframework.boot.env.EnvironmentPostProcessor=类全路径

使用场景



### 原理

```java
SpringBoot 启动 -> prepareEnvironment(listeners, applicationArguments) -> push ApplicationEnvironmentPreparedEvent 事件 -> ConfigFileApplicationListener.onApplicationEnvironmentPreparedEvent 

/**
 * 调用 EnvironmentPostProcessor 方法，利用事件发布订阅机制解耦
*/
private void onApplicationEnvironmentPreparedEvent(ApplicationEnvironmentPreparedEvent event) {
 
		List<EnvironmentPostProcessor> postProcessors = loadPostProcessors();
 
		postProcessors.add(this);
 
		AnnotationAwareOrderComparator.sort(postProcessors);
		for (EnvironmentPostProcessor postProcessor : postProcessors) {
 
			postProcessor.postProcessEnvironment(event.getEnvironment(), event.getSpringApplication());
		}
	}
```



