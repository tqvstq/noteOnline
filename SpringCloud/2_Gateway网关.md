# Gateway网关
官方文档地址[传送](https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.2.0.RC2/reference/html/#gateway-starter)

Gateway是新一代,高性能的微服务网关,微服务是整个微服务API请求的入口，可以实现日志拦截、权限控制、解决跨域问题、限流、熔断、负载均衡、黑名单与白名单拦截、授权等。

注意:Gateway网关基于webflux实现的,同时存在springboot-web将会报错


## Gateway 环境搭建
### 项目中增加依赖
```xml
 <!--gateway-->
 <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
    <version>2.0.0.RELEASE</version>
</dependency>
```

### 项目增加配置
```yaml
spring:
  application:
    # 服务名称
    name: tq-gateway
  cloud:
    gateway:
      discovery:
        locator:
          # 开启后会去 nacos 上获取调用地址 uri地址可以用服务名去nacos上获取 需要配置 filters 过滤器
          enabled: true
        # 路由策略
      routes:
        # 路由策略 id 不重复可配置多个
        - id: tqvstq
          # 转发地址  加斜杠后不会追加 predicates里配置的地址
          uri: http://www.baidu.com/
          # 匹配拦截规则
          predicates:
            - Path=/tq/**
```


## Gateway 拦截器
Gateway 有很多自定义的拦截器,具体可以查看官网

### 编写自定义过滤器简单实现
1. 实现 GlobalFilter 接口 重写filter方法
2. ServerWebExchange 中封装了请求和响应的方法
3. GatewayFilterChain 网关中的操作

```java
@Component
public class TokenFilter implements GlobalFilter {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        // 获取请求中的token参数验证
        String token = exchange.getRequest().getQueryParams().getFirst("token");
        if (token == null || token.isEmpty()) {
            ServerHttpResponse response = exchange.getResponse();
            response.setStatusCode(HttpStatus.BAD_REQUEST);
            String msg = "token not is null ";
            DataBuffer buffer = response.bufferFactory().wrap(msg.getBytes());
            // 写入响应内容
            return response.writeWith(Mono.just(buffer));
        }
        // 返回过滤器 进行过滤
        return chain.filter(exchange);
    }
}
```

## Gateway 高可用
配合 Nginx 进行多节点部署,配置文件一模一样,Nginx上配置负载均衡


## Gateway 动态路由
网关提供了api支持新增和修改
实现方式
1. 使用分布式配置中心,存json格式的字符串
2. 基于数据库表



## Gateway 分析




## Gateway 和 Nginx 的区别?
> 都可以实现负载均衡,Nginx负载均衡基于服务器端负载均衡,Gateway 实现负载均衡为本地负载均衡
> Nginx C语言写的,实现权限过滤,拦截等扩展需要结合Lua语言,Gateway 属于Java语言编写的,比较方便实现功能扩展

