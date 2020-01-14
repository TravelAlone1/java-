# API 网关

### 服务网关简介（zuul）

 服务网关是微服务架构中一个不可或缺的部分。通过服务网关统一向外系统提供REST API的过程中，除了具备服务路由、均衡负载功能之外，它还具备了权限控制等功能。Spring Cloud Netflix中的Zuul就担任了这样的一个角色，为微服务架构提供了前门保护的作用，同时将权限控制这些较重的非业务逻辑内容迁移到服务路由层面，使得服务集群主体能够具备更高的可复用性和可测试性。 

### 常见配置

```properties
#创建路由转发
#zuul.routes是固定写法，具体路由规则
zuul.routes.feign.server-id: feign-server
zuul.routes.feign.path: /feign/**
```

