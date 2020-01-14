# 社招，一个HTTP刷掉80%的面试者



### 实际笔试问题

- http所有请求方法以及描述
- http报文格式

### http协议简介

http（超文本传输协议）是应用层上的一种客户端/服务端模型的通信协议，由请求和响应构成，且是无状态的。（暂不介绍HTTP2）

- 协议

  协议规定了通信双方必须遵守的数据传输格式，这样通信双方按照约定的格式才能准确的通信

- 无状态

  两次连接通信之间没有任何关系的，每次都是一次连接，服务端不会记录请求前后的请求信息。

- 客户端/服务端模型

  ![](https://note.youdao.com/yws/public/resource/8a5296cfc5e3ca295e8fa042b7de7484/xmlnote/2A6FF69280EB4921A21AF37A8A20F52E/9785)

五层网络模型

![](https://note.youdao.com/yws/public/resource/8a5296cfc5e3ca295e8fa042b7de7484/xmlnote/ACC2AE3D6B7A4EA3ACA35970226A6F19/9788)

### URL构成

 用了这么久，你注意过吗？ 

![](https://note.youdao.com/yws/public/resource/8a5296cfc5e3ca295e8fa042b7de7484/xmlnote/5B72D8217AF04FCAB88C722FA024AD2F/9790)

## 协议内容

### 请求（Request）

客户端发送一个HTTP请求到服务端的格式：

- 请求行
- 请求头
- 请求体

![](https://note.youdao.com/yws/public/resource/8a5296cfc5e3ca295e8fa042b7de7484/xmlnote/3377F29CEA4B403DB0FB5881FB511963/9792)

### 响应（Response）

服务端响应客户端格式：

- 状态行
- 响应头
- 响应体

![](https://note.youdao.com/yws/public/resource/8a5296cfc5e3ca295e8fa042b7de7484/xmlnote/4D99FA38EE00429BA181C9C6558F7F47/9794)

### 状态码

HTTP状态码由三个十进制数字组成，第一个十进制数字定义了状态码的类型，后两个数字没有分类的作用。HTTP状态码共分为5种类型

![](https://note.youdao.com/yws/public/resource/8a5296cfc5e3ca295e8fa042b7de7484/xmlnote/58BB995283594F81B5DD76FD41E6B027/9796)

更详细的状态码可查看 HTTP状态码

但一般我们只需要知道几个常见的就行，比如 200，400，401，403，404，500，502.

### 请求方法

截止到HTTP1.1共有下面几种方法：

![](https://note.youdao.com/yws/public/resource/8a5296cfc5e3ca295e8fa042b7de7484/xmlnote/3CAE5A9118BB45A78AE985EE5550ABEF/9798)

 **请求和响应常见通用头** 

![](https://note.youdao.com/yws/public/resource/8a5296cfc5e3ca295e8fa042b7de7484/xmlnote/16CF625329904333A394EA3C60D67485/9800)

### 注意

Content-Type，内容类型，一般是指网页中存在的Content-Type，用于定义网络文件的类型和网页的编码，决定浏览器将以什么形式、什么编码读取这个文件。

常见的媒体格式类型如下：

![](https://note.youdao.com/yws/public/resource/8a5296cfc5e3ca295e8fa042b7de7484/xmlnote/8994B1FFFA0646F680D39A333573DC4B/9802)

 以application开头的媒体格式类型： 

![](https://note.youdao.com/yws/public/resource/8a5296cfc5e3ca295e8fa042b7de7484/xmlnote/D30CD8E807CE4B389CEBA76A91CDCE1B/9804)

 常见请求头 

![](https://note.youdao.com/yws/public/resource/8a5296cfc5e3ca295e8fa042b7de7484/xmlnote/CB82DA84B8AB4B0D936E5F2C1A6869BB/9806)

 **常见响应头** 

![](https://note.youdao.com/yws/public/resource/8a5296cfc5e3ca295e8fa042b7de7484/xmlnote/6B6549F340924E919061875BFAC8F728/9808)

