

# Slime 简介

## 定位



![slime-logo](../assets/slime-logo.png)

 [![Go Report Card](https://goreportcard.com/badge/github.com/slime-io/slime)](https://goreportcard.com/report/github.com/slime-io/slime) [![License](https://img.shields.io/badge/License-Apache%202.0-green.svg)](https://github.com/slime-io/slime/blob/master/LICENSE) ![GitHub release (latest by date)](https://img.shields.io/github/v/release/slime-io/slime?color=green)

---
Slime 是基于服务网格的智能管理器。通过 Slime ，我们可以定义动态的服务治理策略，从而达到自动便捷使用服务网格高阶功能的目的。





## 价值

服务网格作为新一代微服务架构，实现了业务逻辑和微服务治理逻辑的物理解耦，降低微服务框架的开发与运维成本，但是在本地限流，黑白名单，降级等微服务治理的高阶特性存在缺陷。针对服务网格目前的一些弊端，我们推出了 Slime 项目。**该项目可以无缝对接服务网格，无需任何的定制化改造**。

Slime 内部采用了模块化的架构。目前包含了四个非常实用的子模块。

- 懒加载：无须配置SidecarScope，自动按需加载配置和服务发现信息 ，解决了全量推送的问题
- 智能限流：实现了本地限流，同时可以结合监控信息自动调整限流策略，填补了服务网格限流功能的短板

- Http插件管理：使用新的的CRD pluginmanager/envoyplugin包装了可读性及可维护性差的envoyfilter，使得插件扩展更为便捷

- i9s：Istio Terminal UI工具，半命令行半图形化界面，可提升运维效率

后续我们会开源更多的功能模块。




