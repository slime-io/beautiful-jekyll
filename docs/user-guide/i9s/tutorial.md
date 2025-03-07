## 运维场景

常常遇到的场景是，某服务治理规则不生效，需要排查。可能会分以下几个步骤进行排查：

1. 查找服务`pods`, 查看`envoy`的`config_dump`. 一般来说会先查找`pod`, 这里以`view`服务为例

   ```
   kubectl -n skiff-demo-sidecar get pods
   demo-stock-viewer-sidecar-8547788687-lk48m       2/2     Running   0          15d
   ```

2. 执行`envoy debug`命令查看 `config_dump`文件

   ```
   kubectl -n skiff-demo-sidecar exec demo-stock-viewer-sidecar-8547788687-lk48m -- curl 127.0.0.1:15000/config_dump | less
   ```

3. 这时候会遇到类似`config_dump`中没有相关配置文件，控制面是否下发的问题

4. 目标转移到控制面，查找`istio pod`

   ```
   kubectl -n istio-system get pods
   istiod-112-5fb95d6fff-k94pz            1/1     Running     0          46h
   ```

5. 然后首先看`istio`中是否有该配置

   ```
   k exec istiod-112-5fb95d6fff-k94pz -- curl 127.0.0.1:15014/debug/configz | less
   ```

6. 如果发现没有配置，可能是`istio`的上层问题（没下发）。如果是`istio`的问题，可以接着看下发到 view 服务的实际数据是否正常

   ```
   kubectl -n istio-system exec istiod-112-5fb95d6fff-k94pz -- curl 127.0.0.1:15014/debug/config_dump?proxyID=demo-stock-viewer-sidecar-8547788687-lk48m | less
   ```

7. 到这步基本就能判断出问题出在了控制面还是数据面

8. 接下来可能需要更具体的排查


## i9s介绍

`i9s`的宗旨是提高`istio`的运维效率，从运维场景章节可以看到，配置是否下发问题的定位就需要多个`kubectl`交互命令，而`i9s`可以极大提升该过程的查询效率。目前`i9s`提供的功能包括

- `istio` 调试接口，包括`configz`,`adsz`,`config_dump` 等接口信息查看

- `enovy` 调试接口，包括`config_dump`,`cluster`,`endpoints` 等接口信息查看

- `revision`查看，从`revision`视角提供注入规则、deployment资源清单、`mesh`配置文件等信息


## 运行

前提：需要指定`kubeconfig`, 且目前以适配1.12为主, 由于不同版本接口不同，可能导致查询其他版本的接口时出现问题。

- 镜像方式(需要将kubeconfig文件挂载进容器内)

运行最新版本i9s
```
tag=$(curl https://api.github.com/repos/slime-io/i9s/releases/latest -s|grep tag_name|sed 's/.*tag_name": "//g; s/",.*//g')

docker run -it --net=host -v $HOME/.kube/config:/root/.kube/config slimeio/i9s:$tag
```

- 二进制方式, 该安装脚本会检查本地是否有`kubectl`, 如果没有需用户自行安装。之后会检查jq less 等命令是否存在，如果不存在会自动安装, 之后会运行镜像，并将镜像中的 i9s istioctl 可执行文件移动至 /usr/bin 目录下
```
sh ./install.sh
```
如果没有拉取 `install.sh`, 可以执行下面命令在线安装最新版本的i9s
```
tag=$(curl https://api.github.com/repos/slime-io/i9s/releases/latest -s|grep tag_name|sed 's/.*tag_name": "//g; s/",.*//g')

tar_tag=$(curl https://api.github.com/repos/slime-io/i9s/releases/latest -s|grep tag_name|sed 's/.*tag_name": "v//g; s/",.*//g')

source <(curl -fsSL https://github.com/slime-io/i9s/archive/$tag.tar.gz | tar xzO i9s-$tar_tag/install.sh)
```

NOTE: 由于有些`minikube` 权限问题，可能需要将`kubeconifg`中指定的`client-key`的目录一同挂进容器




























































