# 掌握Spring之RestTemplate

## 认识 `RestTemplate`

首先在我们学习使用 `RestTemplate` 之前，先认识下这个类，来看 Spring 官方怎么描述的。 从官方 API 文档 [RestTemplate javadoc](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html) 可以找该类的描述如下：

> Synchronous client to perform HTTP requests, exposing a simple, template method API over underlying HTTP client libraries such as the JDK HttpURLConnection, Apache HttpComponents, and others. The RestTemplate offers templates for common scenarios by HTTP method, in addition to the generalized exchange and execute methods that support of less frequent cases.

从这里可以清楚地了解到 `RestTemplate` 采用同步方式执行 HTTP 请求的类，底层使用 JDK 原生 `HttpURLConnection` API ，或者 `HttpComponents`等其他 HTTP 客户端请求类库。还有一处强调的就是 `RestTemplate` 提供模板化的方法让开发者能更简单地发送 HTTP 请求。



作者：闻人的技术博客链接：https://juejin.im/post/5cd680eff265da037b612e28来源：掘金著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。