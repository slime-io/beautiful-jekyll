## 介绍

`meshregistry`模块的角色大约为`serviceregistry`，它对接了多种服务注册中心，对不同的服务数据转换为统一的`ServiceEntry`数据（也即istio服务模型），然后以服务的方式通过指定协议提供给下游。

本模块可以认为是istio `galley`组件的延续，也有一些区别：

* 出于单一职责的考虑，不再代理服务数据以外的资源（`galley`支持获取k8s中的各种配置资源，转发给下游）

* 跟进istio的演进，支持的协议从`MCP`切换为`MCP-over-xDS` （这是通过 [istio-mcp](https://github.com/slime-io/istio-mcp) 来实现的，该库也支持HTTP协议等）

* 根据落地实践的经验和需要，做了一些改进增强，其中部分可能需要下游（istio侧）对应改造，但不是必要