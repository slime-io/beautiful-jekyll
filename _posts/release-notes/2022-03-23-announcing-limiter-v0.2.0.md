---
layout: post
title: Announcing Limiter v0.2.0
subtitle: Slime limiter v0.2.0 release announcement.
cover-img: /assets/img/cover/natual-scene-4.jpg
thumbnail-img: /assets/img/limiter/smart-limit.jpg
share-img: /assets/img/limiter/smart-limit.jpg
tags: [limiter]
categories: release-notes
---



本次更新是Slime Limiter 2022年第一个版本。我们在去年12月发布的v0.1.1版本基础上，着重丰富了模块的功能，修复了一些已知问题，同时适配了framework v0.4.0版本，与框架层解耦得更加彻底。



## 新功能

- 支持对所有端口入流量侧添加限流规则，详见 [Support any port #21](https://github.com/slime-io/limiter/pull/21)
- 支持开关全局均分限流功能，详见 [Support disable global average rate limit #22](https://github.com/slime-io/limiter/pull/22)
- 支持开关全局共享限流功能，详见 [Support disable global shared rate limit #22 ](https://github.com/slime-io/limiter/pull/22)

## 工程增强

- 修复Envoyfilter转换问题，详见 [Fix headerMatch and refresh problem #16](https://github.com/slime-io/limiter/pull/16)
- Envoyfilter转换兼容老版本istio，详见 [Compatible problem,use typeurl #20](https://github.com/slime-io/limiter/pull/20)
- 适配新版本slime metric，详见 [Adapter new metric framework #22](https://github.com/slime-io/limiter/pull/22)
- 完成模块配置项从Slime框架层迁移至本项目，详见 [Move limiter module config from framework to module #23](https://github.com/slime-io/limiter/pull/23)





## 详情

[Release/v0.2.0](https://github.com/slime-io/limiter/releases/tag/v0.2.0)

