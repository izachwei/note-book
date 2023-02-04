### 什么是 服务网格？

#### Service Mesh

* 在  Service Mesh 架构出来之前，传统的微服务架构存在底层服务交互与业务逻辑耦合在一起，依赖冲突，考虑：透明升级，多语言服务通信等问题。
* Service Mesh 通过部署独立的 Sidecar组件来拦截所有的出口与入口流量，集成丰富的负载均衡策略，开发本身只关注业务逻辑，开发更纯粹。
* Service Mesh 解决了什么问题？

> 透明升级
>
> 多语言
>
> 依赖冲突
>
> 流量治理

* 经典 Mesh 在实施层面也面临成本过高的问题
  1. 需要运维控制面（Control Panel）
  2. 需要运维 Sidecar
  3. 需要考虑如何从原有 SDK 迁移到 Sidecar
  4. 需要考虑引入 Sidecar 后整个链路的性能损耗

#### Proxyless Mesh

* 为了解决 Service Mesh 的问题引入全新的 Proxyless Mesh 架构，Proxyless Mesh 指的是没有 Sidecar 部署，由 Dubbo SDK 直接与控制面交互。
