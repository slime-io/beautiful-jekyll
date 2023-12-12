## 介绍

`meshregistry`模块的角色大约为`serviceregistry`，它对接了多种服务注册中心，对不同的服务数据转换为统一的`ServiceEntry`数据（也即istio服务模型），然后以服务的方式通过指定协议提供给下游。

本模块可以认为是istio `galley`组件的延续，也有一些区别：

* 出于单一职责的考虑，不再代理服务数据以外的资源（`galley`支持获取k8s中的各种配置资源，转发给下游）

* 跟进istio的演进，支持的协议从`MCP`切换为`MCP-over-xDS` （这是通过 [istio-mcp](https://github.com/slime-io/istio-mcp) 来实现的，该库也支持HTTP协议等）

* 根据落地实践的经验和需要，做了一些改进增强，其中部分可能需要下游（istio侧）对应改造，但不是必要



## 架构

实现Mcp Server，从注册中心获取服务并推送至控制面，架构如下。具体可参考blog [Slime化解服务网格多注册中心兼容之痛](../../blog/Slime化解服务网格多注册中心兼容之痛.md)


![](../../assets/meshregistry/img_3.png)

## 特性

### 对接的服务注册中心

目前支持的服务注册中心有：

* zk（dubbo）

* eureka

* nacos

* k8s


### 支持的协议

* `MCP-over-xDS`

* [istio-mcp](https://github.com/slime-io/istio-mcp) 支持的其他协议



### 增量推送

该特性由[istio-mcp](https://github.com/slime-io/istio-mcp) 支持，需要client-server双端支持。 目前因为还没进入社区istio，所以如果对接社区istio，需要关闭该特性。 如果期望使用，需要对istio进行修改用该库替换社区的`Adsc`。



> 后续有计划将istio-mcp增量推送改造为delta xDS实现然后推进社区adsc支持delta xDS。 可以关注该库的进展



### dubbo支持

#### dubbo `Sidecar`生成

可选的可以开启dubbo `Sidecar`生成特性，开启后会根据zk中的dubbo consumers信息来分析出dubbo application的interface依赖关系，然后生成标准的istio `Sidecar`资源，可以极大的减少下发给数据面的配置量
