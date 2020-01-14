# 烂大街的Spring AOP的工作原理以及常用实现你真的了解吗？

# 前言

大家应该知道AOP编程首先要选择它感兴趣的连接点----即**切入点(Point cut)**，那么，AOP能对切入点做什么样的编程呢？ 我们先将代理模式下的某个连接点细化，你会看到如下这个示意图所表示的过程：

 ![img](https://note.youdao.com/yws/public/resource/f9eb887bbd73b0c3c0cb0f1efd964b2f/xmlnote/9EFE3CDB2B5946E8A3EA1DAF0A014AEA/9828) 

为了降低我们对Spring的AOP的理解难度，我在这里将代理角色的职能进行了简化，方便大家理解。（注意：真实的Spring AOP的proxy角色扮演的只能比这复杂的多，这里只是简化）代理模式的代理角色最起码要考虑三个阶段：

> 1. 在调用真正对象的方法之前，应该需要做什么？
> 2. 在调用真正对象的方法过程中，如果抛出了异常，需要做什么？
> 3. 在调用真正对象的方法后，返回了结果了，需要做什么？

AOP对这个方法调用的编程，就是针对这三个阶段插入自己的业务代码。

现在我们假设当前RealSubject这个角色的类是 `org.luanlouis.springlearning.aop.FooService` ，当前这个连接点对应的方法签名是：`public void foo()`。那么上述的代理对象的三个阶段将会有以下的处理逻辑：

1. **在调用真正对象的方法之前**，
    proxy会告诉Spring AOP:  "我将要调用类`org.luanlouis.springlearning.aop.FooService`  的`public void foo()`，在调用之前，你有什么处理建议吗？";

   Spring AOP这时根据proxy提供的类名和方法签名,然后拿这些信息尝试匹配是否在其感兴趣的切入点内，如果在感兴趣的切入点内，Spring AOP会返回 MethodBeforeAdvice处理建议，告诉proxy应该执行的操作；

2. **在调用真正对象的方法过程中，如果抛出了异常**，需要做什么？
    proxy告诉Spring AOP: “我调用类`org.luanlouis.springlearning.aop.FooService`  的`public void foo()`过程中抛出了异常，你有什么处理建议？”

   Spring AOP根据proxy提供的类型和方法签名，确定了在其感兴趣的切入点内，则返回相应的处理建议ThrowsAdvice，告诉proxy这个时期应该采取的操作。

3. **在调用真正对象的方法后，返回了结果了**，需要做什么？
    proxy告诉Spring AOP:"我调用类`org.luanlouis.springlearning.aop.FooService`  的`public void foo()`结束了，并返回了结果你现在有什么处理建议？"；

   Spring AOP 根据proxy提供的类型名和方法签名，确定了在其感兴趣的切入点内，则返回AfterReturingAdivce处理建议，proxy得到这个处理建议，然后执行建议；

 ![img](https://note.youdao.com/yws/public/resource/f9eb887bbd73b0c3c0cb0f1efd964b2f/xmlnote/272E1BC96FE94A89922179C52619CC3E/9833) 

上述的示意图中已经明确表明了Spring AOP应该做什么样的工作：根据proxy提供的特定类的特定方法执行的特定时期阶段给出相应的处理建议。要完成该工作，Spring AOP应该实现：

> 1. 确定自己对什么类的什么方法感兴趣？ -----即确定 AOP的切入点（Point Cut），这个可以通过切入点(Point Cut)表达式来完成；
> 2. 对应的的类的方法的执行特定时期给出什么处理建议？------这个需要Spring AOP提供相应的建议 ，**即我们常说的Advice**。 ![img](https://note.youdao.com/yws/public/resource/f9eb887bbd73b0c3c0cb0f1efd964b2f/xmlnote/2C61E5BFA63D40348CFC0FCF3DF5390D/9835) 

# AOP实现防止接口重复提交

个人设计的思路：

1. 自定义一个注解，添加了该注解的接口可以防止重复提交。

2. 定义切面类，对使用了自定义注解的接口做代理。

3. 使用登录的userId+文章标题作为key存入redis，设置有效期，当有请求调用接口时，到redis中查找相应的key，如果能找到，则说明重复提交，如果找不到，则执行文章插入操作。
    具体代码如下：

   ## 一. 导入aop依赖

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-aop</artifactId>
   </dependency>
   ```

   ## 二. 自定义注解

   ```kotlin
   @Target(ElementType.METHOD)
   @Retention(RetentionPolicy.RUNTIME)
   @Documented
   public @interface ArticleReSubmit {
   }
   ```

   ## 三. 定义切面类

   切面类需要使用@Aspect和@Component这两个注解做标注。

   ```kotlin
   @Aspect
   @Component
   @Slf4j
   public class ArticleAspect {
   
       @Resource
       private RedisUtil redisUtil;
   
       @Value("${user.session.key}")
       private String userSessionKey;
   
       @Pointcut(value = "@annotation(com.mypage.annotation.ArticleReSubmit)")
       public void annotationPointCut() {
   
       }
   
       @Around("annotationPointCut()")
       public Object NoReSubmit(ProceedingJoinPoint joinPoint) {
           ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
           //获取request
           HttpServletRequest request = attributes.getRequest();
           HttpSession session = request.getSession();
           //从session中获取登录的user对象，如果为null,则要求重新登录
           Object sessionUser = session.getAttribute(userSessionKey);
           if (sessionUser == null) {
               return Response.FAIL("页面超时，请重新登录");
           }
           User user = (User) sessionUser;
           Integer userId = user.getId();
           //获取接口的请求参数，如果时Article类型，则保存为Article对象，使用Article对象里的title属性
           Object[] args = joinPoint.getArgs();
           Article article = null;
           for (Object object : args) {
               if (object instanceof Article) {
                   article = (Article) object;
               }
           }
           if (args == null) {
               return Response.FAIL("请求参数错误");
           }
   
           //组装redis key 从redis中获取对应的值
           String key = userId + "_" + article.getTitle();
           Object flag = redisUtil.getStr(key);
   
           //如果redis中不存在对应的值，则执行原有的代码逻辑（插入文章操作）
           if (flag == null) {
               //redis设置key,value值为1
               redisUtil.setStr(key, "1");
               //设置有效期为5分钟
               redisUtil.strSetExpireSeconds(key, 5*60L);
               try {
                   return joinPoint.proceed();
               } catch (Throwable throwable) {
                   redisUtil.delStr(key);
                   return Response.FAIL("系统错误，请联系管理员！");
               }
           } else {
               //如果redis中存在对应的值，则证明重复提交，返回对应的信息
               log.info("{}:重复提交", key);
               return Response.FAIL("重复提交");
           }
       }
   }
   ```

   ## 四. 在想要防止重复提交的接口上添加注解

   ```java
   @RequestMapping("/addArticle")
   @ResponseBody
   @ArticleReSubmit
   public Response addArticle(Article article, HttpServletRequest request) {
       User user = (User) request.getSession().getAttribute(userSessionKey);
       return articleService.addArticle(article, user);
   }
   ```

   这样我们的功能就实现了。

   谢谢！