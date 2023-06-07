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



## 四、Slime v0.7.0（已发布）

优化处理了很多实践过程中遇到的问题

**运维管理**

- 【配置懒加载】支持workload servicefence，结合meshregistry，可以支持serviceEntry类型服务的配置懒加载
- 【基础能力】支持作为xds server,对外提供从istio处获取的xds数据

**流量管理**

- 【智能限流】限流模块支持workloadselector
- 【智能限流】支持请求参数匹配条件
- 【智能限流】持限流触发时，自定义响应头

**扩展管理**

- 【注册中心】支持对接开启认证的nacos，支持根据元数据筛选服务实例

详见RELEASE NOTE [V0.7.0](https://github.com/slime-io/slime/releases/tag/v0.7.0)

## Slime v0.7.1（已发布）

- 优化处理了很多实践过程中遇到的问题

详见RELEASE NOTE [V0.7.1](https://github.com/slime-io/slime/releases/tag/v0.7.1)

## Slime v0.7.2（已发布）

- 优化处理了很多实践过程中遇到的问题

详见RELEASE NOTE [V0.7.2](https://github.com/slime-io/slime/releases/tag/v0.7.2)


## Slime v0.8.0（6月底）

**运维管理**

- 【配置懒加载】支持grpc懒加载
-  前端控制台支持下发Smartlimiter

**扩展管理**

- 【插件管理】支持服务级别插件下发

## Slime v0.8.1（7月底）
- bugfix

## Slime v0.8.2（8月底）
- bugfix

## Slime v0.9.0（9月底）

**运维管理**

- 前端控制台


**工程**
- 支持 Istio Ambient 路线
- 适配 SMI
