# Slime 架构


![slime架构图](../assets/slime-arch.png)


Slime 架构主要分为三大块：

1. slimeboot，部署 Slime（ slime-modules 和 slime-framework ）的 Operator 组件。
2. slime-submodules ，Slime 的核心线程，将 SlimeCRD 转换为 IstioCRD ，并触发其他业务逻辑。
3. slime-framework ，模块底座，提供通用的基础能力。
