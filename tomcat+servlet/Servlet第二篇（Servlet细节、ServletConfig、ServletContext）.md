[TOC]

## Servlet第二篇（Servlet细节、ServletConfig、ServletContext）

# Servlet的细节

## 一个已经注册的Servlet可以被多次映射

**同一个Servlet可以被映射到多个URL上。**

```xml
    <servlet>
        <servlet-name>Demo1</servlet-name>
        <servlet-class>zhongfucheng.web.Demo1</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>Demo1</servlet-name>
        <url-pattern>/Demo1</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>Demo1</servlet-name>
        <url-pattern>/ouzicheng</url-pattern>
    </servlet-mapping>
```

无论我访问的是http://localhost:8080/Demo1还是http://localhost:8080/ouzicheng。我访问的都是Demo1。

![](https://note.youdao.com/yws/public/resource/01cfaf3dcf261d15e042a4c84309155d/xmlnote/0000298EF3E7459D8A10707786E92E0A/9550)

![](https://note.youdao.com/yws/public/resource/01cfaf3dcf261d15e042a4c84309155d/xmlnote/259FED33E7E94FD1B4C51E44CD38E511/9552)

## Servlet映射的URL可以使用通配符

通配符有两种格式：

1. *.扩展名
2. 正斜杠（/）开头并以“/*”结尾。

**匹配所有**

![](https://note.youdao.com/yws/public/resource/01cfaf3dcf261d15e042a4c84309155d/xmlnote/016BD0C8016242DC9CF6A9D86EB974DF/9554)

**匹配扩展名为.jsp的**

![](https://note.youdao.com/yws/public/resource/01cfaf3dcf261d15e042a4c84309155d/xmlnote/54E8B4176DA8426996415BEE3C6780D9/9556)

如果.扩展名和正斜杠（/）开头并以“/”结尾两种通配符同时出现，匹配的是哪一个呢？

1. **看谁的匹配度高，谁就被选择**
2. `*.`扩展名的优先级最低

Servlet映射的URL可以使用通配符和Servlet可以被映射到多个URL上的作用：

1. **隐藏网站是用什么编程语言写的**【.php,.net,.asp实际上访问的都是同一个资源】
2. 用特定的**后缀声明版权**【公司缩写】

## Servlet是单例的

### 为什么Servlet是单例的

**浏览器多次对Servlet的请求**，一般情况下，**服务器只创建一个Servlet对象**，也就是说，Servlet对象**一旦创建了**，就会**驻留在内存中，为后续的请求做服务，直到服务器关闭**。

### 每次访问请求对象和响应对象都是新的

对于**每次访问请求**，Servlet引擎都会**创建一个新的HttpServletRequest请求对象和一个新的HttpServletResponse响应对象**，然后将这两**个对象作为参数传递给它调用的Servlet的service()方法**，**service方法再根据请求方式分别调用doXXX方法**。

### 线程安全问题

当多个用户访问Servlet的时候，**服务器会为每个用户创建一个线程**。**当多个用户并发访问Servlet共享资源的时候就会出现线程安全问题**。

原则：

1. 如果一个**变量需要多个用户共享**，则应当在访问该变量的时候，**加同步机制synchronized (对象){}**
2. 如果一个变量**不需要共享**，则**直接在 doGet() 或者 doPost()定义**.这样不会存在线程安全问题

------

## load-on-startup

如果在元素中配置了一个元素，那么**WEB应用程序在启动时**，就会**装载并创建Servlet的实例对象**、以及**调用Servlet实例对象的init()方法**。

![](https://note.youdao.com/yws/public/resource/01cfaf3dcf261d15e042a4c84309155d/xmlnote/BB423F8CABB24B67B70B270B5D9A357E/9558)

![](https://note.youdao.com/yws/public/resource/01cfaf3dcf261d15e042a4c84309155d/xmlnote/FC175DD14D104351BFB22F229AB77256/9560)

作用：

1. 为web应用写一个InitServlet，这个**servlet配置为启动时装载**，为整个web应用**创建必要的数据库表和数据**
2. 完成一些定时的任务【定时写日志，定时备份数据】

## 在web访问任何资源都是在访问Servlet

当你启动Tomcat，你在网址上输入http://localhost:8080。为什么会出现Tomcat小猫的页面？

这是由**缺省Servlet为你服务的**！

- 我们先看一下web.xml文件中的配置,**web.xml文件配置了一个缺省Servlet**



- 什么叫做缺省Servlet？凡是在web.xml文件中**找不到匹配的元素的URL**，它们的访问**请求都将交给缺省Servlet处理**，也就是说，**缺省Servlet用于处理所有其他Servlet都不处理的访问请求**
- 既然我说了在web访问任何资源都是在访问Servlet，那么**我访问静态资源【本地图片，本地HTML文件】也是在访问这个缺省Servlet【DefaultServlet】**
- 证实一下：当我没有手工配置缺省Servlet的时候，**访问本地图片是可以访问得到的**

- 总结：**无论在web中访问什么资源【包括JSP】，都是在访问Servlet。**没有手工配置缺省Servlet的时候，**你访问静态图片，静态网页，缺省Servlet会在你web站点中寻找该图片或网页**，如果有就返回给浏览器，没有就报404错误

# ServletConfig对象

## ServletConfig对象有什么用？

> 通过此对象可以读取web.xml中配置的初始化参数。

现在问题来了，**为什么我们要把参数信息放到web.xml文件中呢**？我们可以直接在程序中都可以定义参数信息，**搞到web.xml文件中又有什么好处呢**？

好处就是：**能够让你的程序更加灵活**【更换需求，更改配置文件web.xml即可，程序代码不用改】

# ServletContext对象

## 什么是ServletContext对象？

当Tomcat启动的时候，就会创建一个ServletContext对象。它**代表着当前web站点**

## ServletContext有什么用？

1. ServletContext既然代表着当前web站点，那么**所有Servlet都共享着一个ServletContext对象**，所以**Servlet之间可以通过ServletContext实现通讯**。
2. ServletConfig获取的是配置的是单个Servlet的参数信息，**ServletContext可以获取的是配置整个web站点的参数信息**
3. **利用ServletContext读取web站点的资源文件**
4. 实现Servlet的转发【用ServletContext转发不多，主要用request转发】

### Servlet之间实现通讯

ServletContext对象可以被称之为**域对象**

到这里可能有一个疑问，域对象是什么呢？其实域对象可以简单理解成**一个容器【类似于Map集合】**

实现Servlet之间通讯就要**用到ServletContext的setAttribute(String name,Object obj)方法**， 第一个参数是关键字，第二个参数是你要存储的对象