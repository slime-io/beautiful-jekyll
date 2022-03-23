---
layout: post
title: Announcing Lazyload v0.3.0
subtitle: Slime lazyload v0.3.0 release announcement.
cover-img: /assets/img/cover/natual-scene-3.jpg
thumbnail-img: /assets/img/lazyload/tangping-1.jpg
share-img: /assets/img/lazyload/tangping-1.jpg
tags: [lazyload]
categories: release-notes
---



本次更新是Slime Lazyload 2022年第一个版本。我们在去年12月发布的v0.2.0版本基础上，着重提升了模块的易用性，Prometheus模式也适配了1.13+的Istio，同时适配了framework v0.4.0版本，与框架层解耦得更加彻底。



## 新功能

- 支持自定义sidecar cr的workloadSelector，详见 [Support for customization workloadSelector of sidecar #33](https://github.com/slime-io/lazyload/pull/33) [Support customizing workloadSelector through fence.Spec.workloadSelector.Labels #35](https://github.com/slime-io/lazyload/pull/35)
- 支持开关ServiceFence的自动创建功能，详见 [Support for disable ServiceFence auto generating #36](https://github.com/slime-io/lazyload/pull/36)



## 工程增强

- 实现动态服务依赖关系持久化，详见 [Make dynamic service dependency info durable #37](https://github.com/slime-io/lazyload/pull/37)
- 完成懒加载配置项从Slime框架层迁移至本项目，详见 [Complete fence module config migration #39](https://github.com/slime-io/lazyload/pull/39)
- Prometheus metric模式支持Istio 1.12+版本，详见 [Support prometheus as lazyload metric source in istio 1.12+ #42](https://github.com/slime-io/lazyload/pull/42)



## 详情

[Release/v0.3.0](https://github.com/slime-io/lazyload/releases/tag/v0.3.0)

