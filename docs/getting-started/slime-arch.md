# Slime 架构





![slime架构图](../assets/blog-slime-overview-2021/slime-arch-v2.png)



Slime 架构主要分为三大块：

1. slime-boot，部署 Slime（ slime-modules 和 slime-framework ）的 Operator 组件。
2. slime-modules ，Slime 的核心线程，将 SlimeCRD 转换为 IstioCRD ，并触发其他业务逻辑。
3. slime-framework ，模块底座，提供通用的基础能力。



Slime 支持聚合打包，可以将任意模块聚合成一个镜像。所以， Slime 可以以一个 Deployment 形式部署，避免组件过多。