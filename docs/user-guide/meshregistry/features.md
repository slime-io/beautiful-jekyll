## 对接的服务注册中心

目前支持的服务注册中心有：

* zk（dubbo）

* eureka

* nacos

* k8s


## 支持的协议

* `MCP-over-xDS`

* [istio-mcp](https://github.com/slime-io/istio-mcp) 支持的其他协议



## 增量推送

该特性由[istio-mcp](https://github.com/slime-io/istio-mcp) 支持，需要client-server双端支持。 目前因为还没进入社区istio，所以如果对接社区istio，需要关闭该特性。 如果期望使用，需要对istio进行修改用该库替换社区的`Adsc`。



> 后续有计划将istio-mcp增量推送改造为delta xDS实现然后推进社区adsc支持delta xDS。 可以关注该库的进展



## dubbo支持

### dubbo `Sidecar`生成

可选的可以开启dubbo `Sidecar`生成特性，开启后会根据zk中的dubbo consumers信息来分析出dubbo application的interface依赖关系，然后生成标准的istio `Sidecar`资源，可以极大的减少下发给数据面的配置量
