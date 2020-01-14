[TOC]

# springboot 模板引擎

## freemarker

配置SpringBoot集成支持，文件：“application.properties”



> *#### freemarker配置 #####*
>
> *# 开启freemarker支持*
>
> **spring.freemarker.enabled**=**true**
>
> *# 文件后缀名*
>
> **spring.freemarker.suffix**=**.ftl**
>
> *# 开启缓存*
>
> **spring.freemarker.cache**=**true**
>
> *# 编码*
>
> **spring.freemarker.charset**=**UTF-8**
>
> *# freemarker存放路径*
>
> **spring.freemarker.template-loader-path**=**classpath:/templates/**
>
> **spring.freemarker.expose-request-attributes**=**true**
>
> **spring.freemarker.expose-session-attributes**=**true**

**templates目录下存放的文件后缀名为ftl**

## thymeleaf

