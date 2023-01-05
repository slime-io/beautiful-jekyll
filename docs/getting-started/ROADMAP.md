# 路线图

## 一、Slime v0.4.0 （已发布）

**流量管理**

- 【智能限流】支持对入口流量添加限流规则

**运维管理**

- 【配置懒加载】实现动态服务依赖关系持久化

- 【配置懒加载】Prometheus Metric 模式支持 Istio 1.12+ 版本

**扩展管理**

- 【插件管理】PluginManager支持端口匹配

**工程**

- 支持给模块添加自定义 HTTP 接口

- 支持快速生成 Slime 空白新模块

- 支持多架构镜像

详见 [v0.4.0](https://github.com/slime-io/slime/releases/tag/v0.4.0)

## 二、Slime v0.5.0（已发布）

**流量管理**
- 【智能限流】限流模块支持了网关场景下的单机限流和全局均分限流
- 【智能限流】限流模块支持 ServiceEntry ，可对接更多种类的服务注册中心

**运维管理**

- 【配置懒加载】懒加载模块支持自动管理网格内所有 namespace ，不再需要手动指定启用懒加载的 namespace 列表
- 【配置懒加载】懒加载模块支持自动管理所有服务的端口，不再需要手动指定启用懒加载的端口列表
- 【配置懒加载】支持同网络多集群场景
- 【i9s】支持 Istio Debug API 视图
- 【i9s】支持 Envoy Debug API 视图

**工程**

- 更友好的版本管理，Slime 子模块代码聚合至主项目
- 支持注册 HTTP 重定向接口
- 插件管理模块支持了 wasm extension 和 hango Rider Extension
- 适配 Kubernetes 1.22+

详见 [v0.5.0](https://github.com/slime-io/slime/releases/tag/v0.5.0)

## 三、Slime v0.6.0（已发布）

**扩展管理**

- 【注册中心】发布新模块meshregistry，用于对接多注册中心

**运维管理**
- 【i9s】提供通用扩展接口

**工程**

- 规范issue处理、pr处理、发版、社区会议机制等内容
- 完善教程，帮助新用户加入社区

详见 [v0.6.0](https://github.com/slime-io/slime/releases/tag/v0.6.0)

## 四、Slime v0.7.0（规划中）

**流量管理**
- 【配置懒加载】支持 ServiceEntry 服务
- 【智能熔断】发布服务网格熔断标准 API
- 【智能熔断】发布智能熔断新特性
- 【插件管理】支持服务级别插件下发


**运维管理**

- 实现 WASM 插件分发端到端功能

**产品化**

- 【UI】新增 UI 模块，实现资源 CRUD
- 【UI】展示上下游资源关联性


## 五、Slime v0.8.0（规划中）

**流量管理**

- 【智能降级】发布智能降级新特性

**运维管理**

- 【配置懒加载】发布服务网格配置精准推送标准 API

**工程**

- 支持 Istio Ambient 路线
- 适配 SMI

**产品化**

- 【UI】重点模块特化展示，如懒加载的服务拓扑图、限流触发状态、排障操作界面、wasm插件分发等

