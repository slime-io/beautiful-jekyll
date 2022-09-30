## 镜像使用说明



部署时，组件的完整镜像信息=Image Registry:Image Tag，建议选择对应组件最新的tag（根据每行的Update Date）。

**样例**（此处以2021/12/30最新镜像为例）

假如需要部署slime-boot，查表可知，slime-boot最新的镜像是 **docker.io/slimeio/slime-boot:v0.3.8-c8a453d**。 在对应的yaml文件中替换镜像信息即可。考虑到国内镜像加速的需要，我们将镜像也同步到了阿里云（**registry.cn-hangzhou.aliyuncs.com**）。



| Slime-boot Image Registry                            | Lazyload Image Registry                                  | Limiter Image Registry                                  | Plugin Image Registry                                  | Global-Sidecar Image Registry(for lazyload v0.2+)            | Global-Sidecar Image Registry(for lazyload v0.1)  | Global-Pilot Image Registry(for lazyload v0.1)  |
| ---------------------------------------------------- | -------------------------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------- | ----------------------------------------------- |
| docker.io/slimeio/slime-boot                         | docker.io/slimeio/slime-lazyload                         | docker.io/slimeio/slime-limiter                         | docker.io/slimeio/slime-plugin                         | docker.io/slimeio/slime-global-sidecar                       | istio/proxyv2                                     | docker.io/slimeio/pilot                         |
| registry.cn-hangzhou.aliyuncs.com/slimeio/slime-boot | registry.cn-hangzhou.aliyuncs.com/slimeio/slime-lazyload | registry.cn-hangzhou.aliyuncs.com/slimeio/slime-limiter | registry.cn-hangzhou.aliyuncs.com/slimeio/slime-plugin | registry.cn-hangzhou.aliyuncs.com/slimeio/slime-global-sidecar | registry.cn-hangzhou.aliyuncs.com/slimeio/proxyv2 | registry.cn-hangzhou.aliyuncs.com/slimeio/pilot |

| Slime-boot Image Tag | Lazyload Image Tag | Limiter Image Tag  | Plugin Image Tag   | Global-Sidecar Image Tag(for lazyload v0.2+) | Update Date |
| -------------------- | ------------------ | ------------------ | ------------------ | -------------------------------------------- | ----------- |
| v0.2.3-5bf313f       |                    |                    |                    |                                              | 2021-10-18  |
| v0.3.7-1941e85       | v0.1.1-6a9f66c     | v0.1.1-4df8aa0     | v0.1.0-fc92dd9     |                                              | 2021-12-01  |
| v0.3.8-c8a453d       | v0.2.0-1b93bf7     |                    |                    | v0.2.0-1b93bf7                               | 2021-12-30  |
| v0.3.12_linux_amd64  | v0.2.2_linux_amd64 |                    |                    |                                              | 2022-03-22  |
| v0.4.0_linux_amd64   | v0.3.0_linux_amd64 | v0.2.0_linux_amd64 | v0.2.0_linux_amd64 |                                              | 2022-03-23  |
|                      |                    |                    |                    |                                              |             |