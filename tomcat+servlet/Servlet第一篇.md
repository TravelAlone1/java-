[TOC]

# 什么是Serlvet？

Servlet其实就是一个**遵循Servlet开发的java类**。Serlvet是**由服务器调用的**，**运行在服务器端**。

# 为什么要用到Serlvet？

我们编写java程序想要在网上实现 聊天、发帖、这样一些的交互功能，**普通的java技术是非常难完成的**。sun公司就提供了Serlvet这种技术供我们使用。

# HTTP协议

## 什么是HTPP协议

```
超文本传输协议（HTTP，HyperText Transfer Protocol)是互联网上应用最为广泛的一种网络协议。所有的WWW文件都必须遵守这个标准。它是TCP/IP协议的一个应用层协议
```

简单来说，**HTTP协议就是客户端和服务器交互的一种通迅的格式**。

例子:在浏览器点击一个链接，浏览器就为我打开这个链接的网页。

原理：当在浏览器中点击这个链接的时候，**浏览器会向服务器发送一段文本**，**告诉服务器请求打开的是哪一个网页。服务器收到请求后，就返回一段文本给浏览器，浏览器会将该文本解析，然后显示出来。**这段文本就是遵循HTTP协议规范的。

## HTTP1.0和HTTP1.1的区别

HTTP1.0协议中，客户端与web服务器建立连接后，只能获得一个web资源【短连接，获取资源后就断开连接】

HTTP1.1协议，允许客户端与web服务器建立连接后，在一个连接上获取多个web资源【保持连接】

## HTTP请求

**浏览器向服务器请求某个web资源时，称之为浏览器向服务器发送一个http请求。**

一个完整的http请求应该包含三部分：

1. 请求行【描述客户端的**请求方式**、**请求的资源名称**，以及使用的**HTTP协议版本号**】
2. 多个消息头【描述客户端请求哪台主机，以及**客户端的一些环境信息**等】
3. 一个空行

### 请求行

请求行：GET /java.html HTTP/1.1

**请求行中的GET称之为请求方式**，请求方式有：

POST,GET,HEAD,OPTIONS,DELETE,TRACE,PUT。

**常用的有：POST,GET**

一般来说，当我们**点击超链接，通过地址栏访问都是get请求方式**。通过**表单提交的数据一般是post方式**。

可以简单理解**GET方式用来查询数据**,**POST方式用来提交数据**，**get的提交速度比post快**

GET方式：在URL地址后**附带的参数是有限制的**，其**数据容量通常不能超过1K**。

POST方式：可以在**请求的实体内容中向服务器发送数据**，**传送的数据量无限制**。

### 请求头

- Accept: text/html,image/* 【浏览器告诉服务器，它支持的数据类型】
- Accept-Charset: ISO-8859-1 【浏览器告诉服务器，它支持哪种**字符集**】
- Accept-Encoding: gzip,compress 【浏览器告诉服务器，它支持的**压缩格式**】
- Accept-Language: en-us,zh-cn 【浏览器告诉服务器，它的语言环境】
- Host: www.it315.org:80【浏览器告诉服务器，它的想访问哪台主机】
- If-Modified-Since: Tue, 11 Jul 2000 18:23:51 GMT【浏览器告诉服务器，缓存数据的时间】
- Referer: http://www.it315.org/index.jsp【浏览器告诉服务器，客户机是从那个页面来的---**反盗链**】
- 8.User-Agent: Mozilla/4.0 (compatible; MSIE 5.5; Windows NT 5.0)【浏览器告诉服务器，浏览器的内核是什么】
- Cookie【浏览器告诉服务器，**带来的Cookie是什么**】
- Connection: close/Keep-Alive 【浏览器告诉服务器，请求完后是断开链接还是保持链接】
- Date: Tue, 11 Jul 2000 18:23:51 GMT【浏览器告诉服务器，请求的时间】

## HTTP响应

一个HTTP响应代表着**服务器向浏览器回送数据**

一个完整的HTTP响应应该包含四个部分:

1. 一个状态行【用于描述**服务器对请求的处理结果。**】
2. 多个消息头【用于描述**服务器的基本信息**，以及**数据的描述**，**服务器通过这些数据的描述信息，可以通知客户端如何处理等一会儿它回送的数据**】
3. 一个空行
4. 实体内容【**服务器向客户端回送的数据**】

### 状态行

格式： HTTP版本号　状态码　原因叙述

状态行：HTTP/1.1 200 OK

状态码用于表示**服务器对请求的处理结果**，它是一个**三位的十进制数**。响应状态码分为5类

![](https://note.youdao.com/yws/public/resource/01cfaf3dcf261d15e042a4c84309155d/xmlnote/B1942A2BB29D42C6B1276B462C3E10EF/9525)

### 响应头

- Location: http://www.it315.org/index.jsp 【服务器告诉浏览器**要跳转到哪个页面**】
- Server:apache tomcat【服务器告诉浏览器，服务器的型号是什么】
- Content-Encoding: gzip 【服务器告诉浏览器**数据压缩的格式**】
- Content-Length: 80 【服务器告诉浏览器回送数据的长度】
- Content-Language: zh-cn 【服务器告诉浏览器，服务器的语言环境】
- Content-Type: text/html; charset=GB2312 【服务器告诉浏览器，**回送数据的类型**】
- Last-Modified: Tue, 11 Jul 2000 18:23:51 GMT【服务器告诉浏览器该资源上次更新时间】
- Refresh: 1;url=http://www.it315.org【服务器告诉浏览器要**定时刷新**】
- Content-Disposition: attachment; filename=aaa.zip【服务器告诉浏览器**以下载方式打开数据**】
- Transfer-Encoding: chunked 【服务器告诉浏览器数据以分块方式回送】
- Set-Cookie:SS=Q0=5Lb_nQ; path=/search【服务器告诉浏览器要**保存Cookie**】
- Expires: -1【服务器告诉浏览器**不要设置缓存**】
- Cache-Control: no-cache 【服务器告诉浏览器**不要设置缓存**】
- Pragma: no-cache 【服务器告诉浏览器**不要设置缓存**】
- Connection: close/Keep-Alive 【服务器告诉浏览器连接方式】
- Date: Tue, 11 Jul 2000 18:23:51 GMT【服务器告诉浏览器回送数据的时间】

# Servlet的作用

Servlet带给我们最大的作用就是能够**处理浏览器带来HTTP请求，并返回一个响应给浏览器，从而实现浏览器和服务器的交互**。

# JAVAWEB目录结构

![](https://note.youdao.com/yws/public/resource/01cfaf3dcf261d15e042a4c84309155d/xmlnote/9D8FBE80D3F445268D32169E639FB8A1/9529)

以上图说明：

- bbs目录代表一个web应用
- bbs目录下的html,jsp文件可以直接被浏览器访问
- **WEB-INF目录下的资源是不能直接被浏览器访问的**
- web.xml文件是web程序的主要配置文件
- 所有的classes文件都放在classes目录下
- jar文件放在lib目录下

# 实现Servlet接口编写Servlet程序

程序我都是由idea下写，首先要在idea上配置Tomcat，在我别的博文中有教程！

## 编写Servlet程序的步骤

- 创建一个自定义类，**实现Serlvet接口**

![](https://note.youdao.com/yws/public/resource/01cfaf3dcf261d15e042a4c84309155d/xmlnote/AAAC5E0E99F84572A83E8BC4D14AB67B/9532)

- 我们发现有5个方法需要重写，有init【初始化】，destroy【销毁】,service【服务】,ServletConfig【Servlet配置】,getServletInfo【Serlvet信息】。
- 在此一看，发现service()方法是最有可能是写逻辑代码的地方
- 首先写一个hellword入门先
- 调用ServletResponse对象的方法向浏览器输出HelloWorld
- 配置xml文件，光写了Servlet是不行的，Tomcat还要知道浏览器怎么访问这个Servlet。
- 访问自己写的Serlvet程序

## Servlet生命周期可分为5个步骤

1. **加载Servlet**。当Tomcat第一次访问Servlet的时候，**Tomcat会负责创建Servlet的实例**
2. **初始化**。当Servlet被实例化后，Tomcat会**调用init()方法初始化这个对象**
3. **处理服务**。当浏览器**访问Servlet**的时候，Servlet **会调用service()方法处理请求**
4. **销毁**。当Tomcat关闭时或者检测到Servlet要从Tomcat删除的时候会自动调用destroy()方法，**让该实例释放掉所占的资源**。一个Servlet如果长时间不被使用的话，也会被Tomcat自动销毁
5. **卸载**。当Servlet调用完destroy()方法后，等待垃圾回收。如果**有需要再次使用这个Servlet，会重新调用init()方法进行初始化操作**。

- 简单总结：**只要访问Servlet，service()就会被调用。init()只有第一次访问Servlet的时候才会被调用。 destroy()只有在Tomcat关闭的时候才会被调用。**

# 继承HttpServlet编写Servlet程序

在上面我们实现Servlet接口，要实现5个方法。这样太麻烦了！而**HttpServlet类已经实现了Servlet接口的所有方法**，编写Servlet时，只需要**继承HttpServlet，重写你需要的方法即可**，并且它在原有Servlet接口上**添加了一些与HTTP协议处理方法**，它**比Servlet接口的功能更为强大**。

- 

  一般我们开发的时候，都是**重写doGet()和doPost()方法的**。对于idea而言，创建Servlet的时候已经帮你重写好了