# Spring Boot 

费曼学习法

- 选择目标
- 教学
- 纠错学习
- 简化


## Spring Boot是什么

spring本就是为了解决传统的企业级框架繁琐的缺点

Spring Boot 在spring 的宗旨上简化企业级开发的宗旨上更进一步更加简化spring的开发。

目的简化Spring的开发。

本质就是在springmvc的基础上在简化项目配置，具体是在Spring Boot Starter（启动器中）

## Spring Boot Starter的工作原理

- Spring Boot 项目启动时，会去依赖的Starter包中寻找resource/META/spring.factories文件，然后根据文件中Jar包去扫描项目所依赖的Jar包。
- 

## application

核心注解：@SpringBootApplication里面有很多注解，核心作用是整个应用采用自动扫描，自动注入，自动配置，文件必须放在根目录下扫描才能被扫描