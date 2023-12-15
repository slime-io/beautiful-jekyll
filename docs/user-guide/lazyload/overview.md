## 背景

懒加载即按需加载。

没有懒加载时，服务网格内服务数量过多时，Envoy 配置量太大，新上的应用长时间处于 Not Ready 状态。为应用配置 Custom Resource Sidecar，并自动的获取服务依赖关系，管理 Sidecar 可以解决此问题。但是社区的 Sidecar 资源没有自动管理的手段。

其次，在按需配置的场景下，应用必然面对第一次访问新的目标服务缺少相关 xds 配置的问题，需要解决。

## 思路

首先解决 `Sidecar` 资源的自动管理问题。

我们引入了新的 CRD `ServiceFence`， 来辅助管理  `Sidecar` 资源。我们可以在 `ServiceFence` 中存储服务的静态、动态依赖关系，从而管理相应的 `Sidecar` 资源。 `lazyload controller` 组件是控制器，它会为启用懒加载的服务创建 `ServiceFence` 和 `Sidecar`，根据配置获取到服务调用关系，更新 `ServiceFence` 和 `Sidecar` 。

其次，解决首次访问的兜底问题。

我们引入了一个组件 `global-sidecar`，来负责流量兜底。它会被集群的 Istiod 注入标准的 sidecar 容器，因此拥有全量的配置和服务信息。然后，通过 `EnvoyFilter` 将兜底路由替换为指向 `global-sidecar` ，实现对请求的兜底和正确转发。

具体可参考 [http懒加载](./feature/http%E6%87%92%E5%8A%A0%E8%BD%BD.md)


## 架构

整个模块由 `lazyload controller` 和 `global-sidecar` 两部分组成。`lazyload controller` 无需注入 sidecar ，global-sidecar 则需要注入。

![](../../assets/lazyload/arch-1212.png)

首次访问流程说明如下：

前提：部署Lazyload模块

```
1. Service A发起访问Service B，由于Service A没有Service B的配置信息，请求发到global-sidecar的sidecar

2. global-sidecar处理请求

   - 2.1 入流量拦截，如果是accesslog模式，sidecar会生成包含服务调用关系的accesslog

   - 2.2 global-sidecar应用根据请求头等信息，转换访问目标为Service B

   - 2.3 出流量拦截，sidecar拥有所有服务配置信息，找到Service B目标信息，发出请求

3. 请求正确到达Service B

4. global-sidecar通过accesslog方式上报调用关系Service A->Service B，如果是prometheus模式，则由应用方的sidecar生成、上报metric

5. lazyload controller获取到调用关系,更新ServiceFence A，添加关于B的metric

6. 更新Sidecar A，egress.hosts添加B信息

7. Istiod从ApiServer获取Sidecar A新内容

8. Istiod下发Sidecar A限定范围内的配置给Service A的sidecar，新增了B的xDS内容

9. 后续调用，Service A直接访问Service B成功
```

------

NOTE: 根据服务依赖关系指标来源的不同，分为两种模式：

- Accesslog 模式：`global-sidecar` 通生成包含服务依赖关系的 `accesslog` 作为指标
- Prometheus 模式：业务应用在完成访问后，生成 promtheus metric ，作为指标

我们推荐上面流程图介绍的Accesslog模式。具体的细节说明可以参见[教程](./tutorial.md##基于accesslog开启懒加载)
