[TOC]

# spring boot swagger

## Swagger 简介

Swagger 是一套基于 OpenAPI 规范构建的开源工具，可以帮助我们设计、构建、记录以及使用 Rest API。Swagger 主要包含了以下三个部分：

1. Swagger Editor：基于浏览器的编辑器，我们可以使用它编写我们 OpenAPI 规范。
2. Swagger UI：它会将我们编写的 OpenAPI 规范呈现为交互式的 API 文档，后文我将使用浏览器来查看并且操作我们的 Rest API。
3. Swagger Codegen：它可以通过为 OpenAPI（以前称为 Swagger）规范定义的任何 API 生成服务器存根和客户端 SDK 来简化构建过程。

### 为什么要使用 Swagger

当下很多公司都采取前后端分离的开发模式，前端和后端的工作由不同的工程师完成。在这种开发模式下，维持一份及时更新且完整的 Rest API 文档将会极大的提高我们的工作效率。传统意义上的文档都是后端开发人员手动编写的，相信大家也都知道这种方式很难保证文档的及时性，这种文档久而久之也就会失去其参考意义，反而还会加大我们的沟通成本。而 Swagger 给我们提供了一个全新的维护 API 文档的方式，下面我们就来了解一下它的优点：

1. 代码变，文档变。只需要少量的注解，Swagger 就可以根据代码自动生成 API 文档，很好的保证了文档的时效性。
2. 跨语言性，支持 40 多种语言。
3. Swagger UI 呈现出来的是一份可交互式的 API 文档，我们可以直接在文档页面尝试 API 的调用，省去了准备复杂的调用参数的过程。
4. 还可以将文档规范导入相关的工具（例如 SoapUI）, 这些工具将会为我们自动地创建自动化测试。

以上这些优点足以说明我们为什么要使用 Swagger 了，您是否已经对 Swagger 产生了浓厚的兴趣了呢？下面我们就将一步一步地在 Spring Boot 项目中集成和使用 Swagger，让我们从准备一个 Spring Boot 的 Web 项目开始吧。

### 添加依赖

首先要做的自然是添加 Swagger2 所需要的依赖包：

##### 清单 3. 添加 Swagger 依赖

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>    
```

### Java 配置

Springfox 提供了一个 Docket 对象，让我们可以灵活的配置 Swagger 的各项属性。下面我们新建一个 cn.itweknow.sbswagger.conf.SwaggerConfig.java 类，并增加如下内容:

##### 清单 4. Swagger Java 配置

```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2)
                .select()
                .apis(RequestHandlerSelectors.any())
                .paths(PathSelectors.any())
                .build();
    }
}
```

注意: `@Configuration` 是告诉 Spring Boot 需要加载这个配置类，`@EnableSwagger2` 是启用 Swagger2，如果没加的话自然而然也就看不到后面的验证效果了。

### 验证

至此，我们已经成功的在 Spring Boot 项目中集成了 Swagger2，启动项目后，我们可以通过在浏览器中访问 http://localhost:8080/ v2/api-docs 来验证，您会发现返回的结果是一段 JSON 串，可读性非常差。幸运的是 Swagger2 为我们提供了可视化的交互界面 SwaggerUI，下面我们就一起来试试吧。

## 成 Swagger UI

### 添加依赖

和之前一样，集成的第一步就是添加相关依赖，在 pom.xml 中添加如下内容即可：

##### 清单 5. 添加 Swagger UI 依赖

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
```

### 访问验证

其实就只需要添加一下依赖就可以了，我们重新启动一下项目，然后在浏览器中访问 http://localhost:8080/swagger-ui.html 就可以看到如下的效果了:

### 文档相关描述配置

1. 通过在控制器类上增加注解，可以给控制器增加描述和标签信息。

   ##### 清单 6. 给 Controller 添加描述信息

```java
@Api(tags = "用户相关接口", description = "提供用户相关的 Rest API")
public class UserController
```

通过在接口方法上增加 `@ApiOperation`

##### 清单 7. 给接口添加描述信息

```java
@ApiOperation("新增用户接口")
@PostMapping("/add")
public boolean addUser(@RequestBody User user) {
    return false;
}
```

实体描述，我们可以通过` @ApiModel``@ApiModelProperty`

##### 清单 8. 给实体类添加描述信息

```java
@ApiModel("用户实体")
public class User {
    @ApiModelProperty("用户 id")
    private int id;
}
```

1. 文档信息配置，Swagger 还支持设置一些文档的版本号、联系人邮箱、网站、版权、开源协议等等信息，但与上面几条不同的是这些信息不是通过注解配置，而是通过创建一个 ApiInfo 对象，并且使用 `Docket.appInfo()` 方法来设置，我们在 SwaggerConfig.java 类中新增如下内容即可。

##### 清单 9. 配置文档信息

```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2)
                .select()
                .apis(RequestHandlerSelectors.any())
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfo(
                "Spring Boot 项目集成 Swagger 实例文档",
                "我的博客网站：https://itweknow.cn，欢迎大家访问。",
                "API V1.0",
                "Terms of service",
                new Contact("名字想好没", "https://itweknow.cn", "gancy.programmer@gmail.com"),
                "Apache", "http://www.apache.org/", Collections.emptyList());
    }
}
```

经过上面的步骤，我们的文档将会变成下图的样子，现在看起来就清楚很多了。

### 接口过滤

有些时候我们并不是希望所有的 Rest API 都呈现在文档上，这种情况下 Swagger2 提供给我们了两种方式配置，一种是基于 `@ApiIgnore` 注解，另一种是在 Docket 上增加筛选。

`@ApiIgnore` 注解。

##### 清单 10. @ApiIgnore 使用实例

```java
@ApiIgnore
public boolean delete(@PathVariable("id") int id)
```

   