本教程演示为bookinfo的productpage服务开启懒加载。

### 安装 istio

自行安装好 istio

### 安装 slime-boot

请参考 [slimeboot安装](../slime-boot/tutorial.md)

### 安装 lazyload

确保以上两步完成，之后创建 `SlimeBoot` CR

```sh
$ echo '
apiVersion: config.netease.com/v1alpha1
kind: SlimeBoot
metadata:
  name: lazyload
  namespace: mesh-operator
spec:
  image:
    pullPolicy: Always
    repository: docker.io/slimeio/slime-lazyload
    tag: v0.8.2
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
        tag: v0.8.2
      probePort: 20000
' > /tmp/lazyload-slimeboot.yaml

  kubectl apply -f /tmp/lazyload-slimeboot.yaml
```

一些字段说明，可参考 [lazyload安装样例](../slime-boot/tutorial.md)



确认所有组件已正常运行

```sh
$ kubectl get slimeboot -n mesh-operator
NAME       AGE
lazyload   12s
$ kubectl get pod -n mesh-operator
NAME                              READY   STATUS    RESTARTS   AGE
global-sidecar-7dd48b65c8-gc7g4   2/2     Running   0          18s
lazyload-85987bbd4b-djshs         1/1     Running   0          18s
slime-boot-6f778b75cd-4v675       1/1     Running   0          126s
```



### 安装bookinfo

创建前请将current-context中namespace切换到你想部署bookinfo的namespace，使bookinfo创建在其中。此处以default为例。

```sh
$ kubectl label namespace default istio-injection=enabled
$ kubectl apply -f "https://raw.githubusercontent.com/slime-io/slime/v0.5.0/install/config/bookinfo.yaml"
```

创建完后，状态如下

```sh
$ kubectl get po -n default
NAME                              READY   STATUS    RESTARTS   AGE
details-v1-79f774bdb9-6vzj6       2/2     Running   0          60s
productpage-v1-6b746f74dc-vkfr7   2/2     Running   0          59s
ratings-v1-b6994bb9-klg48         2/2     Running   0          59s
reviews-v1-545db77b95-z5ql9       2/2     Running   0          59s
reviews-v2-7bf8c9648f-xcvd6       2/2     Running   0          60s
reviews-v3-84779c7bbc-gb52x       2/2     Running   0          60s
```





### 开启懒加载

由于我们在下发的slimeboot中已经指定了以下配置，所以默认安装的lazyload自动开启了懒加载

```yaml
  autoPort: true
  autoFence: true
  defaultFence: true
```


确认生成servicefence和sidecar对象。

```sh
$ kubectl get servicefence -n default
NAME          AGE
productpage   12s
$ kubectl get sidecar -n default
NAME          AGE
productpage   22s
$ kubectl get servicefence productpage -n default -oyaml
apiVersion: microservice.slime.io/v1alpha1
kind: ServiceFence
metadata:
  labels:
    app.kubernetes.io/created-by: fence-controller
  name: productpage
  namespace: default
  resourceVersion: "10662886"
  uid: f21e7d2b-4ab3-4de0-9b3d-131b6143d9db
spec:
  enable: true
status: {}
$ kubectl get sidecar productpage -n default -oyaml
apiVersion: networking.istio.io/v1beta1
kind: Sidecar
metadata:
  name: productpage
  namespace: default
  ownerReferences:
  - apiVersion: microservice.slime.io/v1alpha1
    blockOwnerDeletion: true
    controller: true
    kind: ServiceFence
    name: productpage
    uid: f21e7d2b-4ab3-4de0-9b3d-131b6143d9db
  resourceVersion: "10662887"
  uid: 85f9dc11-6d83-4b84-8d1b-14bd031cc57b
spec:
  egress:
  - hosts:
    - istio-system/*
    - mesh-operator/*
  workloadSelector:
    labels:
      app: productpage
```

补充说明：

如果用户想关闭某个服务的的懒加载，需要手动在svc上加上label，lazyload会根据label动态删除对应的servicefence和sidecar对象。

```sh
$ kubectl label service productpage -n default slime.io/serviceFenced=false
```


### 首次访问观察

此样例中可以在pod/ratings中发起对productpage的访问，`curl productpage:9080/productpage`。

另外也可参考 [对外开放应用程序](https://istio.io/latest/zh/docs/setup/getting-started/#ip) 给应用暴露外访接口。


第一次访问productpage，使用`kubectl logs -f productpage-xxx -c istio-proxy -n default`观察envoy访问日志。

```
[2021-12-23T06:24:55.527Z] "GET /details/0 HTTP/1.1" 200 - via_upstream - "-" 0 178 12 12 "-" "curl/7.52.1" "7ec25152-ca8e-923b-a736-49838ce316f4" "details:9080" "172.17.0.10:80" outbound|9080||global-sidecar.mesh-operator.svc.cluster.local 172.17.0.11:45194 10.102.66.227:9080 172.17.0.11:40210 - -

[2021-12-23T06:24:55.552Z] "GET /reviews/0 HTTP/1.1" 200 - via_upstream - "-" 0 295 30 29 "-" "curl/7.52.1" "7ec25152-ca8e-923b-a736-49838ce316f4" "reviews:9080" "172.17.0.10:80" outbound|9080||global-sidecar.mesh-operator.svc.cluster.local 172.17.0.11:45202 10.104.97.115:9080 172.17.0.11:40880 - -

[2021-12-23T06:24:55.490Z] "GET /productpage HTTP/1.1" 200 - via_upstream - "-" 0 4183 93 93 "-" "curl/7.52.1" "7ec25152-ca8e-923b-a736-49838ce316f4" "productpage:9080" "172.17.0.11:9080" inbound|9080|| 127.0.0.6:48621 172.17.0.11:9080 172.17.0.7:41458 outbound_.9080_._.productpage.default.svc.cluster.local default
```

可以看出，此次outbound后端访问global-sidecar.mesh-operator.svc.cluster.local。

观察servicefence的stataus.metricStatus字段，可以观察到服务间调用关系已经生成

```sh
$ kubectl get servicefence productpage -n default -oyaml
apiVersion: microservice.slime.io/v1alpha1
kind: ServiceFence
metadata:
  creationTimestamp: "2021-12-23T06:21:14Z"
  generation: 1
  labels:
    app.kubernetes.io/created-by: fence-controller
  name: productpage
  namespace: default
  resourceVersion: "10663136"
  uid: f21e7d2b-4ab3-4de0-9b3d-131b6143d9db
spec:
  enable: true
status:
  domains:
    details.default.svc.cluster.local:
      hosts:
      - details.default.svc.cluster.local
    reviews.default.svc.cluster.local:
      hosts:
      - reviews.default.svc.cluster.local
  metricStatus:
    '{destination_service="details.default.svc.cluster.local"}': "1"
    '{destination_service="reviews.default.svc.cluster.local"}': "1"
```



观察sidecar，可以看到spec.egress.host字段中添加了detail和review服务信息

```sh
$ kubectl get sidecar productpage -n default -oyaml
apiVersion: networking.istio.io/v1beta1
kind: Sidecar
metadata:
  creationTimestamp: "2021-12-23T06:21:14Z"
  generation: 2
  name: productpage
  namespace: default
  ownerReferences:
  - apiVersion: microservice.slime.io/v1alpha1
    blockOwnerDeletion: true
    controller: true
    kind: ServiceFence
    name: productpage
    uid: f21e7d2b-4ab3-4de0-9b3d-131b6143d9db
  resourceVersion: "10663141"
  uid: 85f9dc11-6d83-4b84-8d1b-14bd031cc57b
spec:
  egress:
  - hosts:
    - '*/details.default.svc.cluster.local'
    - '*/reviews.default.svc.cluster.local'
    - istio-system/*
    - mesh-operator/*
  workloadSelector:
    labels:
      app: productpage
```

reviews 和 details 被自动加入！



### 再次访问观察

第二次访问productpage，观察productpage应用日志

```
[2021-12-23T06:26:47.700Z] "GET /details/0 HTTP/1.1" 200 - via_upstream - "-" 0 178 13 12 "-" "curl/7.52.1" "899e918c-e44c-9dc2-9629-d8db191af972" "details:9080" "172.17.0.13:9080" outbound|9080||details.default.svc.cluster.local 172.17.0.11:50020 10.102.66.227:9080 172.17.0.11:42180 - default

[2021-12-23T06:26:47.718Z] "GET /reviews/0 HTTP/1.1" 200 - via_upstream - "-" 0 375 78 77 "-" "curl/7.52.1" "899e918c-e44c-9dc2-9629-d8db191af972" "reviews:9080" "172.17.0.16:9080" outbound|9080||reviews.default.svc.cluster.local 172.17.0.11:58986 10.104.97.115:9080 172.17.0.11:42846 - default

[2021-12-23T06:26:47.690Z] "GET /productpage HTTP/1.1" 200 - via_upstream - "-" 0 5179 122 121 "-" "curl/7.52.1" "899e918c-e44c-9dc2-9629-d8db191af972" "productpage:9080" "172.17.0.11:9080" inbound|9080|| 127.0.0.6:51799 172.17.0.11:9080 172.17.0.7:41458 outbound_.9080_._.productpage.default.svc.cluster.local default
```

可以看到，outbound日志的后端访问信息变为details.default.svc.cluster.local和reviews.default.svc.cluster.local，直接访问成功。



### 卸载

卸载 bookinfo

```sh
$ kubectl delete -f "https://raw.githubusercontent.com/slime-io/slime/v0.5.0/install/config/bookinfo.yaml"
```

卸载 lazyload

```sh
kubectl delete -f /tmp/lazyload-slimeboot.yaml
kubectl get envoyfilter -n istio-system | grep to-global-sidecar && kubectl delete envoyfilter to-global-sidecar -n istio-system
```

卸载 servicefence

```
kubectl get svf -A |awk '{print "kubectl delete svf "$2" -n "$1" "}' |bash
```

确认集群中 svf已经清理完毕 

```
kubectl get svf -A
```

卸载 slime-boot

```sh
export tag_or_commit=$(curl -s https://api.github.com/repos/slime-io/slime/tags | grep 'name' | cut -d\" -f4 | head -1)
kubectl delete -f "https://raw.githubusercontent.com/slime-io/slime/$tag_or_commit/install/init/deployment_slime-boot.yaml"
kubectl delete -f "https://raw.githubusercontent.com/slime-io/slime/$tag_or_commit/install/init/crds-v1.yaml"
kubectl delete ns mesh-operator
```

**note** 需要注意的是，如果手动修改过mesh-operator下存在一些envoyfilter和cm，可手动删除

