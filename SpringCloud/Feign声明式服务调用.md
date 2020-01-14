# Feign声明式服务调用

### 一. Feign简介

**Feign是一个声明式的Web服务客户端。**这使得Web服务客户端的写入更加方便 要使用Feign创建一个界面并对其进行注释。它具有可插入注释支持，包括Feign注释和JAX-RS注释。Feign还支持可插拔编码器和解码器。Spring Cloud增加了对Spring MVC注释的支持，并使用Spring Web中默认使用的HttpMessageConverters。Spring Cloud集成Ribbon和Eureka以在使用Feign时提供负载均衡的http客户端。

### 二.作用

一个微服务调用另一个微服务，并且实现客户端负载均衡

![](https://note.youdao.com/yws/public/resource/f2026eb7a0039ddea7ea6d2d14c28e5b/xmlnote/88BE7F9082374C2F90FA3A74FA1359F5/9769)

### 三. Fegin实战

1.添加依赖

```xml

    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Greenwich.SR3</spring-cloud.version>
    </properties>

		<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

 配置application.properties文件 

```properties
# 端口号
server.port=8766
# 需要指明spring.application.name 这个很重要
# 这在以后的服务与服务之间相互调用一般都是根据这个name
spring.application.name=feign-server
#服务注册中心实例的主机名
eureka.instance.hostname=localhost
#服务注册中心端口号
eureka.port=8761
#在此指定服务注册中心地址
eureka.client.service-url.defaultZone=http://${eureka.instance.hostname}:${eureka.port}/eureka/
```

 在程序的启动类加上@EnableEurekaClient向注册中心注册和@EnableFeignClients注解开启Feign的功能 

```java
package com.lx.weather;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;
import org.springframework.cloud.openfeign.FeignClient;

@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients
public class SojsonWeatherReportEurekaFeignApplication {

    public static void main(String[] args) {
        SpringApplication.run(SojsonWeatherReportEurekaFeignApplication.class, args);
    }

}
```

 创建一个服务接口请求服务提供者 **sojson-weather-city-eureka**

```java
@FeignClient("sojson-weather-city-eureka")
public interface CityClient {

     /**
     * 对应服务的url
     * @return
     */
    @GetMapping("/city/list")
    public List<City> getCity();
}
```

