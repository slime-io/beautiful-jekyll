# 使用



## 准备

安装前需要准备 Metric Source。

支持的指标来源有Prometheus和Accesslog。Slime的懒加载(Lazyload)和自适应限流(Limiter)等模块运行时需要监控指标。具体来说，懒加载支持Promehteus或Accesslog，自适应限流支持Prometheus。

如果需要从Prometheus获取指标，这里提供一份istio官网的Prometheus简化部署文件，可以一键部署出Prometheus。

```shell
$ kubectl apply -f "https://raw.githubusercontent.com/slime-io/slime/$tag_or_commit/install/config/prometheus.yaml"
```



## 安装

在使用slime module之前，需要安装deployment/slime-boot，实际是一个封装的helm operator。它会监听slimeboot cr资源的创建，可以方便的安装和卸载slime模块。 

此处$tag_or_commit使用最新tag。如有需要，也可以自行替换为老版本tag或commit_id。执行如下命令：

```shell
$ export tag_or_commit=$(curl -s https://api.github.com/repos/slime-io/slime/tags | grep 'name' | cut -d\" -f4 | head -1)
$ kubectl create ns mesh-operator
$ kubectl apply -f "https://raw.githubusercontent.com/slime-io/slime/$tag_or_commit/install/init/crds.yaml"
$ kubectl apply -f "https://raw.githubusercontent.com/slime-io/slime/$tag_or_commit/install/init/deployment_slime-boot.yaml"
```



## 验证

安装完毕后，检查mesh-operator中创建的slime-boot与istio-system中的prometheus。如果是Accesslog，则没有额外组件。

```sh
$ kubectl get po -n mesh-operator
NAME                         READY   STATUS    RESTARTS   AGE
slime-boot-fd9d7ff6d-4qb5f   1/1     Running   0          3h25m
$ kubectl get po -n istio-system
NAME                                    READY   STATUS    RESTARTS   AGE
istio-egressgateway-78cb6c4799-6w2cn    1/1     Running   5          14d
istio-ingressgateway-59644976b5-kmw9s   1/1     Running   5          14d
istiod-664799f4bc-wvdhv                 1/1     Running   5          14d
prometheus-69f7f4d689-hrtg5             2/2     Running   2          4d4h
```

