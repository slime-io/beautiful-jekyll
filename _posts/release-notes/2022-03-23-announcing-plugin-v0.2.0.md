---
layout: post
title: Announcing Plugin v0.2.0
subtitle: Slime plugin v0.2.0 release announcement.
cover-img: /assets/img/cover/natual-scene-5.jpg
thumbnail-img: /assets/img/plugin/Plugins.jpg
share-img: /assets/img/plugin/Plugins.jpg
tags: [plugin]
categories: release-notes
---



本次更新是Slime Plugin 2022年第一个版本。我们在去年12月发布的v0.1.0版本基础上，着重丰富了模块支持的匹配能力，修复了一些已知问题，同时适配了framework v0.4.0版本，与框架层解耦得更加彻底。



## 新功能

- EnvoyPlugin支持`ListenerType`，详见 [EnvoyPlugin supports ListenerType and fix FromJSONMap wrong proto type url #9](https://github.com/slime-io/plugin/pull/9)
- PluginManager支持`listener port`匹配，详见 [PluginManager and EnvoyPlugin match enhance #10](https://github.com/slime-io/plugin/pull/10)
- EnvoyPlugin支持自定义`gateway handler`，详见 [PluginManager and EnvoyPlugin match enhance #10](https://github.com/slime-io/plugin/pull/10)



## 工程增强

- 完成插件模块配置项从Slime框架层迁移至本项目，详见 [Move plugin module config from framework to module #11](https://github.com/slime-io/plugin/pull/11)

- `Envoyfilter patchContext`支持`gaterway`类型，详见 [Envoyfilter patchContext supports gateway #13](https://github.com/slime-io/plugin/pull/13)
- 修复enable字段不生效问题，详见 [Fix plugin enable field not effective error #14](https://github.com/slime-io/plugin/pull/14)
- 更新slime版本至v0.4.0，详见 [Update slime version to v0.4.0 #15](https://github.com/slime-io/plugin/pull/15)



## 详情

[Release/v0.2.0](https://github.com/slime-io/plugin/releases/tag/v0.2.0)

