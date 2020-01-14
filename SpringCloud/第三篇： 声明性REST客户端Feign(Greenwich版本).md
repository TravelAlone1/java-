[TOC]

# 第三篇： 声明性REST客户端Feign(Greenwich版本)

Spring Cloud有两种服务调用方式，一种是Ribbon+RestTemplate，另一种是Feign,上一篇文章，讲述了如何通过Ribbon+RestTemplate去调用服务，这篇文章主要讲述如何通过Feign去调用服务

### 一. Feign简介

Feign是一个**声明式的Web服务客户端**。这使得Web服务客户端的写入更加方便 要使用Feign创建一个界面并对其进行注释。它具有可插入注释支持，包括Feign注释和JAX-RS注释。Feign还支持可插拔编码器和解码器。Spring Cloud增加了对Spring MVC注释的支持，并使用Spring Web中默认使用的HttpMessageConverters。**Spring Cloud集成Ribbon和Eureka以在使用Feign时提供负载均衡的http客户端。**

### 二. Feign能做什么?

Feign能使我**们远程调用服务客户端变得更加容易**,不再使用Ribbon+RestTemplate模板式的调用,在一套系统中, 不止一个接口需要调用,往往遇到这样的情况总合有方法解决那就是Feign,Fegin进行了封装,由他来帮助我们定义和实现依赖服务接口的定义.在Feign的实现下,我们只需要创建一个接口并使用注解的方式来配置它(以前是Dao接口上标注Mapper注解,现在是一个微服务接口上面标注一个Feign注解即可),即可完成对服务提供方的接口绑定,简化了使用Spring cloud Ribbon时,自动封装服务调用客户端的开发量