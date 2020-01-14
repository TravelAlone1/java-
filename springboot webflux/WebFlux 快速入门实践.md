[TOC]

# 第01课：WebFlux 快速入门实践spring.io 官网有句醒目的话是：

```
BUILD ANYTHING WITH SPRING BOOT
```

Spring Boot （Boot 顾名思义，是引导的意思）框架是用于简化 Spring 应用从搭建到开发的过程。应用开箱即用，只要通过一个指令，包括命令行 `java -jar` 、`SpringApplication` 应用启动类 、 Spring Boot Maven 插件等，就可以启动应用了。另外，Spring Boot 强调只需要很少的配置文件，所以在开发生产级 Spring 应用中，让开发变得更加高效和简易。目前，Spring Boot 版本是 2.x 版本。Spring Boot 包括 WebFlux。

### Spring Boot 2.0 WebFlux

了解 WebFlux，首先了解下什么是 Reactive Streams。Reactive Streams 是 JVM 中面向流的库标准和规范：

- 处理可能无限数量的元素
- 按顺序处理
- 组件之间异步传递
- 强制性非阻塞背压（Backpressure）

**Backpressure（背压）**

背压是一种常用策略，使得发布者拥有无限制的缓冲区存储元素，用于确保发布者发布元素太快时，不会去压制订阅者。

**Reactive Streams（响应式流）**

一般由以下组成：

- 发布者：发布元素到订阅者
- 订阅者：消费元素
- 订阅：在发布者中，订阅被创建时，将与订阅者共享
- 处理器：发布者与订阅者之间处理数据

**响应式编程**

有了 Reactive Streams 这种标准和规范，利用规范可以进行响应式编程。那再了解下什么是 Reactive programming 响应式编程。响应式编程是基于异步和事件驱动的非阻塞程序，只是垂直通过在 JVM 内启动少量线程扩展，而不是水平通过集群扩展。这就是一个编程范例，具体项目中如何体现呢？

响应式项目编程实战中，通过基于 Reactive Streams 规范实现的框架 Reactor 去实战。Reactor 一般提供两种响应式 API ：

- Mono：实现发布者，并返回 0 或 1 个元素
- Flux：实现发布者，并返回 N 个元素

**Spring Webflux**

Spring Boot Webflux 就是基于 Reactor 实现的。Spring Boot 2.0 包括一个新的 spring-webflux 模块。该模块包含对响应式 HTTP 和 WebSocket 客户端的支持，以及对 REST，HTML 和 WebSocket 交互等程序的支持。一般来说，Spring MVC 用于同步处理，Spring Webflux 用于异步处理。

Spring Boot Webflux 有两种编程模型实现，一种类似 Spring MVC 注解方式，另一种是使用其功能性端点方式。注解的会在第二篇文章讲到，下面快速入门用 Spring Webflux实用性功能性方式实现。

Spring Boot 2.0 WebFlux 特性

常用的Spring Boot 2.0 WebFlux生产特性如下：

- 响应式编程API
- 编程模型
- 适用性
- 内嵌容器
- Starter组件

### 响应式 API

实现原理：Reactor 框架是Spring Boot  Webflux 是响应库依赖，通过Reactive Streams 并与其他响应库交互。

方案：提供两种响应式 API：Mono和 Flux。

大概流程是：一般是将 Publisher 作为输入，在框架内部转换成Reactor类型并处理逻辑，然后返回Flux或Mono作为输出。

### 编程模型

spring 5 web 模块包含了 Spring WebFlux 的HTTP抽象。类似于 Servlet API，提供了WebHandler API 去定义非阻塞式API抽象接口。通过两种编程模型实现

- 注解控制层
- 功能性端点。基于lambda 轻量级编程模型，用来路由和处理请求的小工具。赢上面最大的区别：全程控制请求-响应的生命流程