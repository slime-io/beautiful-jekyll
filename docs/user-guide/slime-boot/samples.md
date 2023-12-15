###  lazyload安装样例

部署支持`cluster`级别的懒加载模块，成功后会在`mesh-operator`命名空间下部署名为`lazyload`和`global-sidecar`的`deployment`

- istioNamespace：用户集群中，`istio`部署的`ns`
- module: 指定`lazyload`部署的相关参数
  - name: 模块名称
  - kind：模块类别，目前只支持 lazyload/plugin/limiter/meshregistry
  - enable: 是否开启该模块
  - general: `lazyload`启动相关参数
  - global: `lazyload`依赖的一些全局参数，global具体参数可参考 [Config.global](#configglobal)
  - metric: `lazyload`服务间电泳关系依赖的指标信息
- component：懒加载模块中关于`globalSidecar`的配置，除镜像外一般不用改动

```yaml
apiVersion: config.netease.com/v1alpha1
kind: SlimeBoot
metadata:
  name: lazyload
  namespace: mesh-operator
spec:
  image:
    pullPolicy: Always
    repository: docker.io/slimeio/slime-lazyload
    tag: v0.9.0
  namespace: mesh-operator
  istioNamespace: istio-system
  module:
    - name: lazyload
      kind: lazyload
      enable: true
      general:
        autoPort: true
        autoFence: true
        defaultFence: true
        wormholePort: # replace to your application service ports, and extend the list in case of multi ports
          - "9080"
        globalSidecarMode: cluster # the mode of global-sidecar
        metricSourceType: accesslog # indicate the metric source          
      global:
        log:
          logLevel: info
        slimeNamespace: mesh-operator
  resources:
    requests:
      cpu: 300m
      memory: 300Mi
    limits:
      cpu: 600m
      memory: 600Mi
  component:
    globalSidecar:
      enable: true
      sidecarInject:
        enable: true # should be true
        mode: pod
        labels: # optional, used for sidecarInject.mode = pod
          sidecar.istio.io/inject: "true"
      resources:
        requests:
          cpu: 200m
          memory: 200Mi
        limits:
          cpu: 400m
          memory: 400Mi
      image:
        repository: docker.io/slimeio/slime-global-sidecar
        tag: v0.9.0
      probePort: 20000
```

### limiter安装样例

安装支持单机限流功能的限流模块，成功后会在`mesh-operator`命名空间下部署名为`limiter`的`deployment`

- image: 指定`limiter`的镜像，包括策略，仓库，tag
- module: 指定`limiter`部署的相关参数
  - name: 模块名称
  - kind：模块类别，目前只支持 lazyload/plugin/limiter/meshregistry
  - enable: 是否开启该模块
  - general: `limiter`启动相关参数
    - disableGlobalRateLimit：禁用全局共享限流
    - disableAdaptive: 禁用自适应限流
    - disableInsertGlobalRateLimit: 禁止模块插入全局限流相关的插件
  - global: `limiter`依赖的一些全局参数，global具体参数可参考 [Config.global](#configglobal)

```yaml
apiVersion: config.netease.com/v1alpha1
kind: SlimeBoot
metadata:
  name: limiter
  namespace: mesh-operator
spec:
  image:
    pullPolicy: Always
    repository: docker.io/slimeio/slime-limiter
    tag: v0.9.0
  module:
    - name: limiter
      kind: limiter
      enable: true
      general:
        disableGlobalRateLimit: true
        disableAdaptive: true
        disableInsertGlobalRateLimit: true
```

### plugin 安装样例

安装plugin模块

- image: 指定`plugin`的镜像，包括策略，仓库，tag
- module: 指定`plugin`部署的相关参数
  - name: 模块名称
  - kind：模块类别，目前只支持 lazyload/plugin/limiter/meshregistry
  - enable: 是否开启该模块
  - global: `plugin`依赖的一些全局参数，global具体参数可参考 [Config.global](#configglobal)

```yaml
apiVersion: config.netease.com/v1alpha1
kind: SlimeBoot
metadata:
  name: plugin
  namespace: mesh-operator
spec:
  image:
    pullPolicy: Always
    repository: docker.io/slimeio/slime-plugin
    tag: v0.9.0
  module:
    - name: plugin
      kind: plugin
      enable: true
```

### meshregistry安装样例

安装对接多注册中心的meshregistry模块

- image: 指定`meshregistry`的镜像，包括策略，仓库，tag
- module: 指定`meshregistry`部署的相关参数
  - name: 模块名称
  - kind：模块类别，目前只支持 lazyload/plugin/limiter/meshregistry
  - enable: 是否开启该模块
  - general: `meshregistry`启动相关参数，支持配置K8SSource、EurekaSource、NacosSource、ZookeeperSource
  - global: `meshregistry`依赖的一些全局参数，global具体参数可参考 [Config.global](#configglobal)

```yaml
apiVersion: config.netease.com/v1alpha1
kind: SlimeBoot
metadata:
  name: meshregistry
  namespace: mesh-operator
spec:
  image:
    pullPolicy: Always
    repository: docker.io/slimeio/slime-meshregistry
    tag: v0.9.0
  module:
    - name: meshregistry
      kind: meshregistry
      enable: true
      general:
        LEGACY:
          NacosSource:
            Enabled: true
            RefreshPeriod: 30s
            Address:
              - "http://nacos.test.com:8848"
            Mode: polling
          # EurekaSource:
          #   Enabled: true
          #   Address:
          #   - "http://test/eureka"
          #   RefreshPeriod: 15s
          #   SvcPort: 80
          # ZookeeperSource:
          #   Enabled: true
          #   RefreshPeriod: 30s
          #   WaitTime: 10s
          #   Address:
          #   - zookeeper.test.svc.cluster.local:2181
```

### bundle模式安装样例

在上面的样例中，我们部署了`lazyload`，`limiter`和`plugin`模块，现在我们用`bundle`的模式安装包含上面三个功能的`bundle`模块

```yaml
apiVersion: config.netease.com/v1alpha1
kind: SlimeBoot
metadata:
  name: bundle
  namespace: mesh-operator
spec:
  image:
    pullPolicy: Always
    repository: docker.io/slimeio/slime-bundle-all
    tag: v0.9.0
  module:
    - name: bundle
      enable: true
      bundle:
        modules:
          - name: bundle
            kind: lazyload
          - name: limiter
            kind: limiter
          - name: plugin
            kind: plugin
          - name: meshregistry
            kind: meshregistry
      global:
        log:
          logLevel: info
    - name: bundle #与上面的name一致TODO
      kind: lazyload
      enable: true
      mode: BundleItem
      general:
        autoPort: true
        autoFence: true
        defaultFence: true
        wormholePort: # replace to your application service ports, and extend the list in case of multi ports
          - "9080"
        globalSidecarMode: cluster # the mode of global-sidecar
        metricSourceType: accesslog # indicate the metric source        
      global:
        slimeNamespace: mesh-operator
    - name: limiter
      kind: limiter
      enable: true
      mode: BundleItem
      general:
        disableGlobalRateLimit: true
        disableAdaptive: true
        disableInsertGlobalRateLimit: true
    - name: plugin
      kind: plugin
      enable: true
      mode: BundleItem
    - name: meshregistry
      kind: meshregistry
      enable: true
      mode: BundleItem
      general:
        LEGACY:
          MeshConfigFile: ""
          RevCrds: ""
          Mcp: {}
          K8SSource:
            Enabled: false
  component:
    globalSidecar:
      replicas: 1
      enable: true
      sidecarInject:
        enable: true # should be true
        mode: pod
        labels: # optional, used for sidecarInject.mode = pod
          sidecar.istio.io/inject: "true"
      resources:
        requests:
          cpu: 200m
          memory: 200Mi
        limits:
          cpu: 400m
          memory: 400Mi
      image:
        repository: docker.io/slimeio/slime-global-sidecar
        tag: v0.9.0
      probePort: 20000 # health probe port
      port: 80 # global-sidecar default svc port
      legacyFilterName: true
```

