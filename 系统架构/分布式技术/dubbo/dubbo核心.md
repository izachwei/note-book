## 核心

在 Dubbo 的核心领域模型中：

- Protocol 是服务域，它是 Invoker 暴露和引用的主功能入口，它负责 Invoker 的生命周期管理。
- Invoker 是实体域，它是 Dubbo 的核心模型，其它模型都向它靠拢，或转换成它，它代表一个可执行体，可向它发起 invoke 调用，它有可能是一个本地的实现，也可能是一个远程的实现，也可能一个集群实现。
- Invocation 是会话域，它持有调用过程中的变量，比如方法名，参数等

<img src="https://dubbo.incubator.apache.org/imgs/v3/concepts/invoke-arch.jpg">



#### 三个核心中心化组件

<img src="https://dubbo.apache.org/imgs/v3/concepts/threecenters.png">

<img src="../image/dubbo部署架构.jpg">

