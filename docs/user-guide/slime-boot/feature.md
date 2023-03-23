## 多副本

以limiter为例，安装带有两个副本的limiter模块。

我们需要设置`enable-leader-election: "on"`以及`replicaCount: 2` 可开启多副本模式。

```yaml
apiVersion: config.netease.com/v1alpha1
kind: SlimeBoot
metadata:
  name: limiter
  namespace: mesh-operator
spec:
  replicaCount: 2   #多副本
  image:
    pullPolicy: Always
    repository: docker.io/slimeio/slime-limiter
    tag: v0.7.0
  module:
    - name: limiter
      kind: limiter
      enable: true
      general:
        disableGlobalRateLimit: true
        disableAdaptive: true
        disableInsertGlobalRateLimit: true
        misc:
          enable-leader-election: "on"  #多副本
```

## TODO