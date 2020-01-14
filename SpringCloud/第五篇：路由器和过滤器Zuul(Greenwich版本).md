[TOC]

# 第五篇：路由器和过滤器Zuul(Greenwich版本)

### 一. 简介

#### 1. Zuul 是什么?

Zuul是Netflix开源的**微服务网关**，他可以和Eureka,Ribbon,Hystrix等组件配合使用

Nginx大家都知道是反向代理服务器,可以做到负载均衡,过滤等功能,其实网关Zuul和它是一样的,比如你的eureka-client服务部署在很多服务器上,前端需要联调,难道都要记住这么多IP和接口,不可能记住的,这个时候就**需要网关进行暴露统一接口进行转发请求不同的服务器上**,一方面是安全另一方面负载均衡

#### 2.Zuul能做什么?

- 认证
- 洞察
- 压力测试
- 金丝雀测试
- 动态路由
- 服务迁移
- 负载脱落
- 安全
- 静态响应处理
- 主动/主动流量管理

Zuul的规则引擎允许通过任何JVM语言来编写规则和过滤器, 支持基于Java和Groovy的构建。 配置属性 zuul.max.host.connections 已经被两个新的配置属性替代, zuul.host.maxTotalConnections （总连接数）和 zuul.host.maxPerRouteConnections,（每个路由连接数） 默认值分别是200和20.