在服务网格中，通过下发envoyfilter的方式可以扩展网格功能，但是envoyfilter对用户不够友好，slime/plugin 提供了一套友好的api, 将用户配置转化成envoyfilter。


## 架构

plugin模块的架构如下，主体功能：监听EnvoyPlugin和PluginManager资源，并将其转化成Envoyfilter下发
