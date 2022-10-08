# Slime Website


## 本地启动

1.当前目录切换至仓库根目录下
2.通过docker run启动页面服务

```shell
## 若本地8000端口被占用，修改-p选项的端口映射规则
docker run --rm -it -p 8000:8000 -v ${PWD}:/docs squidfunk/mkdocs-material:7.2.5
```



## 访问调试

默认以8000端口启动，访问url如下

```
http://localhost:8000/
```
