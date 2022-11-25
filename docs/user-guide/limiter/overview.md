服务限流的目的是为了保护服务不被大量请求击垮，通过限制请求速率保护服务正常运行，在服务网格体系中，需要下发复杂的的EnvoyFilter才能完成限流。为了减轻用户使用难度，我们们推出了限流组件slime/limiter，用户只需提交贴近用户语义的SmartLimiter资源，即可完成限流规则的下发。

## 样例

`SmartLimiter`的CRD定义的比较接近自然语义, 例如，以下样例中，对review服务的9080端口设置限流规则 10次/s

~~~yaml
apiVersion: microservice.slime.io/v1alpha2
kind: SmartLimiter
metadata:
  name: review
  namespace: default
spec:
  sets:
    v1:
      descriptor:
      - action:
          fill_interval:
            seconds: 1
          quota: "10"
          strategy: "single"
        condition: "true"
        target:
          port: 9080
~~~