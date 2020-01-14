# response、request对象

> Tomcat收到客户端的http请求，会针对每一次请求，分别创建一个**代表请求的request对象**、和**代表响应的response对象**

既然request对象代表http请求，那么我们**获取浏览器提交过来的数据，找request对象**即可。response对象代表http响应，那么我们**向浏览器输出数据，找response对象**即可。

# 什么是HttpServletResponse对象？

http响应由状态行、实体内容、消息头、一个空行组成。**HttpServletResponse对象就封装了http响应的信息。**

# HttpServletResponse的应用

## 调用getOutputStream()方法向浏览器输出数据

调用getOutputStream()方法向浏览器输出数据,**getOutputStream()方法可以使用print()也可以使用write()**，它们有什么区别呢？我们试验一下。代码如下

```java
 		//获取到OutputStream流
        ServletOutputStream servletOutputStream = response.getOutputStream();

        //向浏览器输出数据
        servletOutputStream.print("aaaa");
```

