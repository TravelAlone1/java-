# Spring Cloud构建微服务架构（四）分布式配置中心

### 简介

 Spring Cloud Config为分布式系统中的外部化配置提供服务器和客户端支持。使用Config Server，您可以在所有环境中管理应用程序的外部属性。客户端和服务器上的概念映射与Spring Environment和PropertySource抽象，因此它们非常适合Spring应用程序，但可以与任何语言运行的任何应用程序一起使用。当应用程序通过部署管道从开发到测试并进入生产时，您可以管理这些环境之间的配置，并确保应用程序具有迁移时需要运行的所有内容。服务器存储后端的默认实现使用git，因此它可以轻松支持配置环境的标签版本，以及可用于管理内容的各种工具。添加替代实现并使用Spring配置插入它们很容易。 

## 构建Config Server

通过Spring Cloud构建一个Config Server，非常简单，只需要三步：

- pom.xml中引入`spring-cloud-config-server`依赖，完整依赖配置如下：

```XML
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.lx</groupId>
        <artifactId>sojson-weather</artifactId>
        <version>1.0-SNAPSHOT</version>
        <relativePath>../</relativePath> <!-- lookup parent from repository -->
    </parent>
    <artifactId>sojson-weather-config-server</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>sojson-weather-config-server</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Greenwich.SR3</spring-cloud.version>
    </properties>
    
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

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

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
        </repository>
    </repositories>

</project>

```

- 创建Spring Boot的程序主类，并添加`@EnableConfigServer`注解，开启Config Server

```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableConfigServer
public class SojsonWeatherConfigServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(SojsonWeatherConfigServerApplication.class, args);
    }

}
```

- `application.properties`中配置服务信息以及git信息，例如：

```properties
spring.application.name=sojson-weather-config-server

server.port=8888

eureka.port=8080
eureka.instance.hostname=localhost
eureka.client.service-url.defaultZone=http://${eureka.instance.hostname}:${eureka.port}/eureka/

# git方式
# github地址
spring.cloud.config.server.git.uri=https://github.com/TravelAlone1/sojson-weather-config-server
# 仓库路径 spring cloud会先在searchPaths中寻找配置文件
spring.cloud.config.server.git.search-paths=config-repo
# 仓库的分支
spring.cloud.config.label=master
```

如果Git仓库为公开仓库，可以不填写用户名和密码，如果是私有仓库需要填写，我这边是公开仓库

config-repo文件夹下有config-dev.properties配置文件内容属性为

```properties
name=lx
```

HTTP服务具有以下形式的资源：

```
/{application}/{profile}[/{label}]/{application}-{profile}.yml/{label}/{application}-{profile}.yml/{application}-{profile}.properties/{label}/{application}-{profile}.properties
```

启动程序 打开浏览器访问 http://localhost:8888/config/dev/master

config是我的配置文件前缀

dev表示开发环境 master表示分支 

到这里配置文件服务端就搭建好了 

下面搭建客户端 

### 三. 构建config client 客户端

![img](https://mmbiz.qpic.cn/mmbiz_png/znPPvMIlDMV41OBDlsso8icY1ua9tvH0wlsQTO4yWWASXHykxaYZNs4vibyNgTFk4ZFq1XwgOaG1EBFrsBxnn5Tw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.lx</groupId>
        <artifactId>sojson-weather</artifactId>
        <version>1.0-SNAPSHOT</version>
        <relativePath>../</relativePath> <!-- lookup parent from repository -->
    </parent>

    <artifactId>sojson-weather-config-client</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>sojson-weather-config-client</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Greenwich.SR3</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

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

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
        </repository>
    </repositories>

</project>
```

 bootstrap.properties配置文件 

```properties
spring.application.name=sojson-weather-config-client

server.port=8889

eureka.port=8080
eureka.instance.hostname=localhost
eureka.client.service-url.defaultZone=http://${eureka.instance.hostname}:${eureka.port}/eureka

# 配置文件前缀 config-dev.properties
#{application}-{profile}.properties，application=name
spring.cloud.config.name=config
# 指明远程仓库的分支
spring.cloud.config.label=master
# dev开发环境配置文件 test测试环境 pro正式环境
spring.cloud.config.profile=dev

# 配置服务中心的网址（config-server位置）
spring.cloud.config.uri=http://localhost:8888/
spring.cloud.config.discovery.enabled=true

# 配置中心服务端服务名
spring.cloud.config.discovery.serviceId=sojson-weather-config-server
```

重点说下

> 当你的配置中心的 server.port 不是 8888 的时候，服务就起不来了，从日志中可以发现，服务启动的时候，Fetching config from server at: http://localhost:8888，说明其他服务还是去 8888 这个默认端口取配置中心的文件，而不去你在 application.properties 文件中配置的配置中心读取配置文件 当你的服务配置文件使用 application.properties 文件时，服务启动还没到加载 application.properties 文件那一步，所以并不会去你配置的注册中心里的配置中心读取所需要的配置信息，因为application.properties 的优先级不高，而此时又需要一些配置信息（例如数据库的信息），系统才能继续往下执行启动，这个时候就需要使用 bootstrap.properties 引导配置文件，使用这个配置文件时，服务在启动的时候就会先加载 bootstrap.properties 配置文件，这样就会去你配置的注册中心里的配置中心读取配置文件信息，然后加载信息进行启动。 优先级bootstrap>application

 在程序的入口类，写一个接口，返回从配置中心读取的my.name变量的值，代码如下： 

```java
package com.lx.weather;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@EnableDiscoveryClient
@RestController
public class SojsonWeatherConfigClientApplication {

    public static void main(String[] args) {
        SpringApplication.run(SojsonWeatherConfigClientApplication.class, args);
    }


    @Value("${name}")
    private String name;

    @GetMapping("/hello")
    public String hello(){
        return name;
    }
}

```

 启动程序 打开浏览器访问 http://localhost:8889/hello 

返回 **lx**  说明配置中心成功 