## 安装和使用

作为slime module，使用上的流程大体接近：

1. 启用该模块、被slime纳管（slime-boot方式 or bundle方式）

2. 准备好module config对模块行为进行必要的配置

## bundle部署方式

1. 在bundle中将本模块加入，打包、出镜像

2. 本模块支持env方式设置少量行为，如有必要可以添加在bundle的部署材料（deployment）中 （可选）

3. 在bundle的配置configmap中加入本模块部分，类似如下：

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
    tag: v0.6.0_linux_amd64
  module:
    - name: meshregistry
      kind: meshregistry
      enable: true
      global:
        log:
          logLevel: info
      general:
        LEGACY:
         MeshConfigFile: ""
         RevCrds: ""
         Mcp:
           EnableIncPush: false
         K8SSource:
           Enabled: false
         EurekaSource:
           Enabled: true
           Address:
           - "http://eureka.myeureka.com/eureka"
           RefreshPeriod: 15s
           SvcPort: 80
         ZookeeperSource:
           Enabled: true
           RefreshPeriod: 30s
           WaitTime: 10s
           # EnableDubboSidecar: false
           Address:
           - zookeeper.myzk.cluster.local:2181
```