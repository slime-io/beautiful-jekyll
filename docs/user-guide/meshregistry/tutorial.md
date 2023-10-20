作为slime module，使用上的流程大体接近：

1. 启用该模块、被slime纳管（slime-boot方式 or bundle方式）

2. 准备好module config对模块行为进行必要的配置

### bundle部署方式

1. 在bundle中将本模块加入，打包、出镜像

2. 本模块支持env方式设置少量行为，如有必要可以添加在bundle的部署材料（deployment）中 （可选）

3. 在bundle的配置configmap中加入本模块部分，类似如下：

   ```yaml
   apiVersion: v1
   data:
     cfg: |
       bundle:
         modules:
         - name: meshregistry
           kind: meshregistry
       enable: true
     cfg_meshregistry: |
       name: meshregistry
       kind: meshregistry
       enable: true
       mode: BundleItem
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

   > 其他配置内容见bundle部署模式介绍
   >
   > 配置内容详见 `pkg/bootstrap/args.go`

### 单独部署样例

1. 部署对接nacos的meshregistry子模块

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
       tag: v0.8.2
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
             - "http://nacos.test.com"
             Mode: polling
   ```

2. istiod 配置configSources, 从meshregistry获取服务信息

   ```yaml
   configSources:
   - address: xds://meshregistry.mesh-operator.svc:16010
   ```

3. 利用接口查询meshregistry已获取服务信息：`xx:8081/meshregistry/xdsCache`,

   ```json
   {
     "networking.istio.io/v1alpha3/ServiceEntry": [
       {
         "type": "networking.istio.io/v1alpha3/ServiceEntry",
         "name": "nacos.naming.servicename",
         "namespace": "naming",
         "labels": {
           "registry": "nacos"
         },
         "annotations": {
           "ResourceVersion": "2023-04-07 09:14:37.971015359 +0000 UTC m=+246.064642097"
         },
         "creationTimestamp": "2023-04-07T09:14:37.970977169Z",
         "Spec": {
           "hosts": [
             "nacos.naming.servicename"
           ],
           "addresses": [],
           "ports": [
             {
               "number": 80,
               "protocol": "HTTP",
               "name": "http"
             }
           ],
           "resolution": "STATIC",
           "endpoints": [
             {
               "address": "20.18.7.10",
               "ports": {
                 "http": 8080
               },
               "labels": {}
             }
           ]
         }
       }
     ]
   }
   ```

4.  利用接口查询istiod已获取服务信息 `xx:15014/debug/configz `

```json
[
  {
    "kind": "ServiceEntry",
    "apiVersion": "networking.istio.io/v1alpha3",
    "metadata": {
      "name": "nacos.naming.servicename",
      "namespace": "naming",
      "resourceVersion": "2023-04-07 09:27:15.69926204 +0000 UTC m=+0.090193089",
      "creationTimestamp": "2023-04-07T09:15:07Z",
      "labels": {
        "registry": "nacos"
      },
      "annotations": {
        "ResourceVersion": "2023-04-07 09:15:07.969794894 +0000 UTC m=+276.063421634"
      }
    },
    "spec": {
      "hosts": [
        "nacos.naming.servicename"
      ],
      "ports": [
        {
          "name": "http",
          "number": 80,
          "protocol": "HTTP"
        }
      ],
      "resolution": "STATIC"
    }
  }
]
```