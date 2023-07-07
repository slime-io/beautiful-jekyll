1. 场景丰富：支持动态和静态管理 `Sidecar` 资源
2. 治理完善：支持 Istio 所有流量治理能力
3. 对 Istio 友好：无侵入性，支持1.8以上的 Istio
4. 使用简单：可自动对接整个服务网格

没有懒加载时，服务网格内服务数量过多时，Envoy 配置量太大，新上的应用长时间处于 Not Ready 状态。为应用配置 Custom Resource `Sidecar`，并自动的获取服务依赖关系，管理 `Sidecar` 可以解决此问题。但是社区的 `Sidecar` 资源没有自动管理的手段。其次，在按需配置的场景下，应用必然面对第一次访问新的目标服务缺少相关 xds 配置的问题，需要解决。


------

## 思路

首先解决 `Sidecar` 资源的自动管理问题。

我们引入了新的 CRD `ServiceFence`， 来辅助管理  `Sidecar` 资源。我们可以在 `ServiceFence` 中存储服务的静态、动态依赖关系，从而管理相应的 `Sidecar` 资源。 `lazyload controller` 组件是控制器，它会为启用懒加载的服务创建 `ServiceFence` 和 `Sidecar`，根据配置获取到服务调用关系，更新 `ServiceFence` 和 `Sidecar` 。

其次，解决首次访问的兜底问题。

我们引入了一个组件 `global-sidecar`，来负责流量兜底。它会被集群的 Istiod 注入标准的 sidecar 容器，因此拥有全量的配置和服务信息。然后，通过 `EnvoyFilter` 将兜底路由替换为指向 `global-sidecar` ，实现对请求的兜底和正确转发。

具体特性参见 [教程](./tutorial.md)