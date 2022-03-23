---
layout: post
title: Announcing Framework v0.4.0
subtitle: Slime framework v0.4.0 release announcement.
cover-img: /assets/img/cover/natual-scene-1.jpg
thumbnail-img: /assets/img/framework/Framework-logo-1.png
share-img: /assets/img/framework/Framework-logo-1.png
tags: [framework]
categories: release-notes
---



本次更新是Slime framework 2022年第一个版本。我们在去年12月发布的v0.3.7版本基础上，着重提升了Slime framework项目作为框架层的扩展能力，以及和各个模块之间的解耦。



### 新功能

- 支持快速生成slime新的模块项目，新项目中可以是单个模块，也可以是多个模块聚合，详见 [Add scripts for new module or bundle init #128](https://github.com/slime-io/slime/pull/128)

  1.支持快速生成单个模块的项目

  ```sh
  cd bin
  bash gen_module.sh my_module
  ```

  2.支持快速生成包含多模块的项目

  ```sh
  cd bin
  bash gen_bundle.sh my_bundle lazyload limiter
  ```

  把新项目变成git项目，第一次提交后，你就可以构建镜像了。

- 支持自定义启动参数和环境变量，详见 [Support custom args and env for module #133](https://github.com/slime-io/slime/pull/133)

- 支持给模块添加自定义API接口，详见 [Support for adding custom http handlers in module level #134](https://github.com/slime-io/slime/pull/134)

  使用样例：

  只要在lazyload的`InitManager`函数中添加自定义http handler，如下所示，就可以得到一个新的api接口`/lazyload/xxx`。

  ```go
  func (mo *Module) InitManager(mgr manager.Manager, env bootstrap.Environment, cbs module.InitCallbacks) error {
  
  	// register custom api interface
  	env.HttpPathHandler.Handle("xxx", livezHandler())
  
         // ...
         return nil
  }
  
  func livezHandler() http.Handler {
  	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
  		if _, err := w.Write([]byte("Healthy!")); err != nil {
  			log.Errorf("livez probe error, %+v", err)
  		}
  	})
  }
  ```

- 新增`kind`字段，模块名字可以是任意值，从而支持单模块多revision聚合打包，即以一个deployment运行多个不同revision的模块，详见 [Module name and kind decoupling #140](https://github.com/slime-io/slime/pull/140)

  现在你可以选择任意Slime模块聚合成一个项目，生成镜像，以一个Deployment形式运行！这大大简化了使用多个Slime模块时的部署维护难度。

  除此以外，framework还支持同一个模块选择多次，配置不同的Revision，以处理不同类别的网络资源，就像Istio Revision所做的那样。



### 工程增强

- 模块独有的helm文件拆分到各个模块项目中，详见 [Split helm templates accroding to modules #127](https://github.com/slime-io/slime/pull/127)
- 支持打多架构镜像，详见 [support singel build and multiarch build #136](https://github.com/slime-io/slime/pull/136)
- 将模块专用配置从框架层解耦，详见 [Support getting module config from slimeboot general field #137](https://github.com/slime-io/slime/pull/137)
- 模块配置支持yaml格式，详见 [Support yaml format module config #141](https://github.com/slime-io/slime/pull/141)



### 详情

[Release/v0.4.0](https://github.com/slime-io/slime/releases/tag/v0.4.0)

