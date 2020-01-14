[TOC]

## 什么是Tomcat
Tomcat简单的说就是一个运行JAVA的网络服务器，底层是Socket的一个程序，它也是JSP和Serlvet的一个容器。
## 为什么需要用到tomcat（tomcat的作用）
如果你学过html，css，你会知道你写的页面只能自己访问，别人不能远程访问你写的页面，Tomcat就是提供能够让别人访问自己写的页面的一个程序
![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/71B1A9F09605474B92B16A817D284FDD/9477)

## 相关术语介绍

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/E34F811C693948E087BC3A06623F9D1C/9479)

## tomcat结构目录

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/516B825AC9444891BE32695DBCD6D315/9482)

## 目录简单介绍

1. 
   bin：启动和关闭tomcat的bat文件

   

2. 

   conf：配置文件

   

3. - `server.xml`该文件用于配置server相关的信息，比如tomcat启动的端口号，配置主机(Host)
   - `web.xml`文件配置与web应用（web应用相当于一个web站点）
   - `tomcat-user.xml`配置用户名密码和相关权限.

4. 

   lib：该目录放置运行tomcat运行需要的jar包

   

5. 

   logs：存放日志，当我们需要查看日志的时候，可以查询信息

   

6. 

   webapps：放置我们的web应用

   

7. 

   work工作目录：该目录用于存放**jsp被访问后生成对应的server文件和.class文件**

## webapps目录的详细说明

**在webapps中建立了web1目录**，下面放置我们的html文件，jsp文件，图片等等，**则web1就被当做web应用管理起来**【tomcat6.0以后的版本才支持】

例子：**在webapps下创建一个web站点，在web站点下创建一个html文件，访问html文件**

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/015C0E7CDEFD4B7B8AACCBFE14D2D198/9484)

**web站点的目录是有规范的** 

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/6BA4BBF6BE7D41BB9A415934343BA80E/9486)

为什么要这样设置web站点目录呢？

- 需求：我有多个html文件，想把其中的一个html文件作为我web站点的首页。
- 如果**没有WEB-INF目录下的web.xml文件支持，是无法解决我的需求的**
- **这个规范是约定熟成的。**

------

下面将web站点下的helloword2.xml文件作为站点的首页

- 新建一个WEB-INF目录
![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/A3E5411A216D4715ABAB9329A95AB378/9490)
- 在WEB-INF目录下创建一个web.xml

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/36B5C896C38241A888C9CBBAD3F67DA3/9492)

- web.xml我们不可能会写，所以可以**在webapps目录下其他的站点中抄一份过来**【复制ROOT/WEB-INF/web.xml的文件到自己的站点中】
- 在web.xml中添加以下代码

```java
<welcome-file-list>
	<welcome-file>helloword2.html</welcome-file>
</welcome-file-list>
```

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/F4F402F67D664C709927B015BF729C88/9494)

- 访问web站点【**helloword2.html已经是web站点的首页了，所以不需要指定资源访问了**】

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/CD9FF55024AC476A976CA91D0B0AB99C/9488)

# 配置虚拟目录

## 为什么需要配置虚拟目录？(目的)

- 如果把所有web站点的目录都放在webapps下，可能导致**磁盘空间不够用**，也**不利于对web站点目录的管理**【如果存在非常多的web站点目录】
- 把**web站点的目录分散到其他磁盘管理就需要配置虚拟目录【默认情况下，只有webapps下的目录才能被Tomcat自动管理成一个web站点】**
- 把web应用所在目录交给web服务器管理，这个过程称之为虚拟目录的映射

## 配置虚拟目录方法一：

- 在其他盘符下创建一个web站点目录，并创建WEB-INF目录和一个html文件。

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/63EFD7A022304C35A10F3C2513BB1337/9496)

- 找到Tomcat目录下/conf/server.xml文件

  ![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/1C674BFDC45A4F5083EEA9BDD6221995/9498)

- 在server.xml中的节点下添加如下代码。**path表示的是访问时输入的web项目名，docBase表示的是站点目录的绝对路径**

```xml
<Context path="/web1" docBase="D:\web1" />
```

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/71032FB5C3B0410EAA2B95D487520F32/9500)

-  访问配置好的web站点

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/E14A4150CFB741D5A43B9E837300232C/9502)

## 配置虚拟目录方法二：

- 进入到conf\Catalina\localhost文件下，创建一个xml文件，**该文件的名字就是站点的名字。** 

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/02685EF0F29B41EA9D43FB196242E968/9504)

- xml文件的代码如下，**docBase是你web站点的绝对路径**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Context 
          docBase="D:\web1" reloadable="true">
</Context>
```

- 访问web站点下的html资源 

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/6BE0E5BEE50B428FB5C2363AA4B47019/9506)

# 配置临时域名

访问Tomcat服务器有好几种方式

- 使用localhost域名访问【localhost代表本机】
- 使用ip地址127.0.0.1访问【该ip地址也是本机】
- 使用机器名称访问【只限用于本机上或者局域网】
- 使用本机IP地址访问【**在cmd中输入ipconfig可以查询到本机IP地址**】
- 还可以为机器配置临时域名