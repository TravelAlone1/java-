[TOC]

# 第二篇： 客户端负载平衡器Ribbon(Greenwich版本)

前面说过在微服务架构中, 各个服务之间通常是基于HTTP的Restful API进行通信, Spring Cloud有两种服务调用方式，一种是Ribbon+RestTemplate，另一种是Feign,本文先将讲解前者

### 一. Ribbon简介

> > 可以参考官方文档: http://cloud.spring.io/spring-cloud-netflix/single/spring-cloud-netflix.html#spring-cloud-ribbon

- Ribbon是一个客户端负载均衡器，它可以很好地控制HTTP和TCP客户端的行为

Spring Cloud Netflix默认情况下为Ribbon（BeanType beanName：ClassName）提供以下bean：

- IClientConfig ribbonClientConfig：DefaultClientConfigImpl
- IRule ribbonRule：ZoneAvoidanceRule
- IPing ribbonPing：NoOpPing
- ServerList ribbonServerList：ConfigurationBasedServerList
- ServerListFilter ribbonServerListFilter：ZonePreferenceServerListFilter
- ILoadBalancer ribbonLoadBalancer：ZoneAwareLoadBalancer
- ServerListUpdater ribbonServerListUpdater：PollingServerListUpdater

1. IClientConfig：Ribbon的客户端配置，主要的作用就是装载配置信息，用于初始化客户端和负载均衡器。默认的实现方式是DefaultClientConfigImpl
2. IRule：默认采用ZoneAvoidanceRule实现，该策略能够在多区域环境下选出最佳区域的实例。
3. IPing：默认采用DummyPing实现，该检查策略是一个特殊的实现，实际上并不会检查实例是否可用，而是始终返回true,默认认为所有实例都可用。
4. ServerList: 服务实例清单的维护机制，默认采用ConfigurationBasedServerList实现。
5. ServerlistFilter: 服务实例清单过滤机制，默认采用ZonePreferenceServerListFilter，该策略能够优先过滤出于请求方处于同区域的服务实例。
6. ILoadBalancer:负载均衡器，默认采用ZoneAwareLoadBalancer，它具备区域感知的能力

**Ribbon可以在通过客户端中配置的ribbonServerList服务端列表去轮询访问以达到均衡负载的作用 当Ribbon与Eureka联合使用时，ribbonServerList会被DiscoveryEnabledNIWSServerList重写，扩展成从Eureka注册中心中获取服 务端列表。同时它也会用NIWSDiscoveryPing来取代IPing，它将职责委托给Eureka来确定服务端是否已经启动**