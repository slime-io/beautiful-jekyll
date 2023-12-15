## 介绍

`slime-boot`可以理解成一个`Controller`，它会一直监听`SlimeBoot CR`，当用户提交一份`SlimeBoot CR`后，`slime-boot Controller`会根据`CR`的内容渲染`slime`相关的部署材料。本文将介绍`SlimeBoot`的使用方式，并给出使用样例，指引用户安装并使用`slime`组件。

## 准备

在安装`slime`组件前，需要安装`SlimeBoot CRD`、`deployment/slime-boot`以及各子模块对应的`CRD`

**注意**：在k8s v1.22以及之后的版本中，只支持`apiextensions.k8s.io/v1`版本的`CRD`，不再支持`apiextensions.k8s.io/v1beta1`版本`CRD`，详见[k8s官方文档](https://kubernetes.io/docs/reference/using-api/deprecation-guide/#customresourcedefinition-v122)。

由于网络访问等因素影响，我们提供了两种SlimeBoot安装方式，用户既可以选择在线安装，也可以选择利用slime源码离线安装。

### 在线安装

对于k8s v1.22以及之后版本，需要手动安装v1版本crd，而之前版本的k8s既可以使用v1版本的crd也可以使用v1beta1版本的crd

如果k8s version >= v1.22

```shell
export tag_or_commit=$(curl -s https://api.github.com/repos/slime-io/slime/tags | grep 'name' | cut -d\" -f4 | head -1)
kubectl create ns mesh-operator
kubectl apply -f "https://raw.githubusercontent.com/slime-io/slime/$tag_or_commit/install/init/crds-v1.yaml"
kubectl apply -f "https://raw.githubusercontent.com/slime-io/slime/$tag_or_commit/install/init/deployment_slime-boot.yaml"
```

如果k8s v1.16 <= version < 1.22，以下两者都能使用

```shell
export tag_or_commit=$(curl -s https://api.github.com/repos/slime-io/slime/tags | grep 'name' | cut -d\" -f4 | head -1)
kubectl create ns mesh-operator
kubectl apply -f "https://raw.githubusercontent.com/slime-io/slime/$tag_or_commit/install/init/crds.yaml"
kubectl apply -f "https://raw.githubusercontent.com/slime-io/slime/$tag_or_commit/install/init/deployment_slime-boot.yaml"
```
或者

```shell
export tag_or_commit=$(curl -s https://api.github.com/repos/slime-io/slime/tags | grep 'name' | cut -d\" -f4 | head -1)
kubectl create ns mesh-operator
kubectl apply -f "https://raw.githubusercontent.com/slime-io/slime/$tag_or_commit/install/init/crds-v1.yaml"
kubectl apply -f "https://raw.githubusercontent.com/slime-io/slime/$tag_or_commit/install/init/deployment_slime-boot.yaml"
```

### 从源码中离线安装

下载最新的slime代码
```
git clone git@github.com:slime-io/slime.git

cd slime/install/init
```

安装合适的crd
```
// k8s version >= v1.22
kubectl apply -f crds-v1.yaml

or
// k8s v1.16 <= version < 1.22
kubectl apply -f crds.yaml
```

安装deployment/slime-boot
```
kubectl create ns mesh-operator

kubectl apply -f deployment_slime-boot.yaml
```

## 参数介绍
根据之前的章节，我们知道用户通过下发`SlimeBoot`的方式，安装`slime`组件，在正常使用过程中，用户使用的`SlimeBoot CR`主要包含以下几项

- image: 定义镜像相关的参数
- resources： 定义容器资源
- module: 定义需要启动的模块，以及对应的参数
  - name: 模块名称
  - kind：模块类别，目前只支持 lazyload/plugin/limiter/meshregistry
  - enable: 是否开启模块
  - global: 模块依赖的一些全局参数，见下文
  - general: 模块启动时需要的一些参数

样例如下：

```yaml
apiVersion: config.netease.com/v1alpha1
kind: SlimeBoot
metadata:
  name: xxx ## real name
  namespace: mesh-operator
spec:
  image:
    pullPolicy: Always
    repository: docker.io/slimeio/slime-xxx ## real image
    tag: xxx  ## real image
  module:
    - name: xxx
      kind: xxx
      enable: true
      global:
        log:
          logLevel: info
```

其中global参数介绍如下：

| Key                            | Default Value                                                                                                                              | Usages                                                                                                                                                                                                                                                                                                                        | Remark |
|--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| service                        | app                                                                                                                                        | servicefence匹配服务的label key，用来生成懒加载中sidecar的默认配置                                                                                                                                                                                                                                                                               |        |
| istioNamespace                 | istio-system                                                                                                                               | 部署istio组件的namespace，用来生成懒加载中sidecar的默认配置，应等于实际部署istio组件的namespace                                                                                                                                                                                                                                                             |        |
| slimeNamespace                 | mesh-operator                                                                                                                              | 部署slime模块的namespace，用来生成懒加载中sidecar的默认配置，应等于实际创建slimeboot cr资源的namespace                                                                                                                                                                                                                                                      |        |
| log.logLevel                   | ""                                                                                                                                         | slime自身日志级别                                                                                                                                                                                                                                                                                                                   |        |
| log.klogLevel                  | 0                                                                                                                                          | klog日志级别                                                                                                                                                                                                                                                                                                                      |        |
| log.logRotate                  | false                                                                                                                                      | 是否启用日志轮转，即日志输出本地文件                                                                                                                                                                                                                                                                                                            |        |
| log.logRotateConfig.filePath   | "/tmp/log/slime.log"                                                                                                                       | 本地日志文件路径                                                                                                                                                                                                                                                                                                                      |        |
| log.logRotateConfig.maxSizeMB  | 100                                                                                                                                        | 本地日志文件大小上限，单位MB                                                                                                                                                                                                                                                                                                               |        |
| log.logRotateConfig.maxBackups | 10                                                                                                                                         | 本地日志文件个数上限                                                                                                                                                                                                                                                                                                                    |        |
| log.logRotateConfig.maxAgeDay  | 10                                                                                                                                         | 本地日志文件保留时间，单位天                                                                                                                                                                                                                                                                                                                |        |
| log.logRotateConfig.compress   | false                                                                                                                                      | 本地日志文件轮转后是否压缩                                                                                                                                                                                                                                                                                                                 |        |
| misc                           | {"metrics-addr": ":8080", "aux-addr": ":8081"}, | 可扩展的配置集合，目前支持一下参数参数：1."metrics-addr"定义slime module manager监控指标暴露地址；2."aux-addr"定义辅助服务器暴露地址|
| seLabelSelectorKeys            | app                                                                                                                                        | 默认应用标识，se 涉及                                                                                                                                                                                                                                                                                                                  |        |
| xdsSourceEnableIncPush         | true                                                                                                                                       | 是否进行xds增量推送                                                                                                                                                                                                                                                                                                                   |
| pathRedirect                   | ""                                                                                                                                         | path从定向映射表                                                                                                                                                                                                                                                                                                                    |

其中我们可以在`misc`中定义一些扩展参数，这些参数会被传递给`slime`组件，目前支持以下参数：

* enableLeaderElection (bool): 是否开启leader选举，默认为false
* aux-addr (string): 辅助服务器暴露地址，默认为":8081"




## 安装子模块

Slime子模块支持两种安装方式：

- 1.模块部署：需要给每个子模块下发一份`Slimeboot`资源，用来部署一份`deployment`

以下yaml是一个模块部署样例，module中定义的第一个对象，就是该`SlimeBoot`应该具备的功能

```yaml
apiVersion: config.netease.com/v1alpha1
kind: SlimeBoot
metadata:
  name: xxx ## real name
  namespace: mesh-operator
spec:
  image:
    pullPolicy: Always
    repository: docker.io/slimeio/slime-xxx ## real image
    tag: xxx  ## real image
  module:
    - name: xxx
      kind: xxx
      enable: true
      global:
        log:
          logLevel: info
```

- 2.bundle部署：只需提交下发一份`Slimeboot`资源，部署出来的`deployment`将包含多个组件功能

以下yaml是一个bundle模式样例，其中module的第一个对象定义了这个服务拥有limiter和plugin功能，mudule中后两个对象分别对应limiter和plugin子模块的具体参数

```yaml
apiVersion: config.netease.com/v1alpha1
kind: SlimeBoot
metadata:
  name: bundle
  namespace: mesh-operator
spec:
  image:
    pullPolicy: Always
    repository: docker.io/slimeio/slime-bundle-example-all
    tag: v0.9.0
  module:
    - name: bundle
      enable: true
      bundle:
        modules:
          - name: limiter
            kind: limiter
          - name: plugin
            kind: plugin        
    - name: limiter
      kind: limiter
      enable: true
      mode: BundleItem
      general: {}
      global: {}
    - name: plugin
      kind: plugin
      enable: true
      mode: BundleItem
```


使用者可参考 [子模块安装](./samples.md) 部署`lazyload`，`limiter`和`plugin`模块