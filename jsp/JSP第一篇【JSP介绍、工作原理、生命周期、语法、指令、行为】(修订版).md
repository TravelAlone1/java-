[TOC]

# jsp是什么

JSP全名为Java Server Pages，**java服务器页面**。JSP是一种基于文本的程序，**其特点就是HTML和Java代码共同存在**！

# 为什么需要JSP

**JSP是为了简化Servlet的工作出现的替代品，Servlet输出HTML非常困难，JSP就是替代Servlet输出HTML的。**

# 简单使用一下JSP

- 在idea下生成一个JSP，我们来看一下JSP长什么样子

```jsp
<%--
  Created by IntelliJ IDEA.
  User: Administrator
  Date: 2019/9/16
  Time: 14:08
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    
</body>
</html>
```

* **看起来就像一个HTML页面**，前面也说了：JSP的特点就是HTML和Java代码共同存在

# JSP的工作原理

在Tomcat博客中我提到过：**Tomcat访问任何的资源都是在访问Servlet**！，当然了，JSP也不例外！**JSP本身就是一种Servlet**。为什么我说JSP本身就是一种Servlet呢？**其实JSP在第一次被访问的时候会被编译为HttpJspPage类（该类是HttpServlet的一个子类）**

刚才我简单使用了一下JSP，**它被编译成了这么一个Servlet**:

# JSP的语法

JSP代码可以分为两部分：

1. **模板数据：就是HTML代码**
2. **元素：JSP页面中的java代码、JSP指令、JSP标签**

## JSP脚本

- **JSP的脚本就是JSP页面中的java代码**，也叫做scriptlet。**JSP的脚本必须使用<%%>括起来，不然会被当成是模板数据的！**

- JSP脚本有三种方式：

- - <%%>【定义局部变量，编写语句】
  - <%!%>【定义类或方法，**但是没人这样用！**】
  - <%=%>（也称之为**表达式输出**）【输出各种类型的变量，int、double、String、Object等】

如果过多地使用<%%>会导致代码混乱，JSP还提供了一种scriptlet标签，**使用此标签和<%%>有相同的功能，只不过它更美观了一些**

```jsp
<jsp:scriptlet>

    String s = "HelloWorld";
    out.println(s);

</jsp:scriptlet>
```

## JSP注释

```jsp
<%--这是JSP注释--%>
<%--%>

//这是java的当行注释
//


/*这是java的多行注释*/
/**/
```

## JSP指令

**JSP指令用来声明JSP页面的相关属性，例如编码方式、文档类型等等**

JSP指令的语法：

```jsp
<%@指令  属性名="值"  %>
```

## page指令

- 我在idea生成的JSP页面就有page指令了。

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
```

- page指令常见属性：
- language="java"
- extends="package.class"
- **import**="{package.class | package.*}, …"
- **session="true | false"**
- buffer="none | 8kb | sizekb"
- autoFlush="true | false"
- isThreadSafe="true | false"
- info="text"
- **errorPage="relative_url"**
- **isErrorPage="true | false"**
- **contentType="mimeType  ;charset=characterSet " | "text/html ; charset=ISO-8859-1"**
- **pageEncoding="characterSet | ISO-8859-1"**
- **isELIgnored="true | false"**
- 一般地，在eclipse或idea这些高级开发工具上开发，**我们只需要在page指令中指定contentType="text/html;charset=UTF-8"，就不会出现中文乱码问题！**
- 当然了contentType **不仅仅可以指定以text/html的方式显示，还可以使用其他的形式显示出来。在conf/web.xml文件中可以查询出来**

- 比如，**我以doc形式显示jsp的数据**

```jsp
<%@ page contentType="application/msword;charset=UTF-8" language="java" %>
<html>
<head>
    <title>简单使用JSP</title>
</head>
<body>

    1111
</body>
</html>
```

- 我们上网的时候，**如果我们操作不当，或者服务器出错了，页面都是会出现友好提示的**！这个也能通过page指令来实现**跳转到友好提示页面上**！
- page指令**errorPage=和isErrorPage**这两个属性，下面我们来看一下怎么使用！
- 1.jsp出现了错误，**通过page指令的errorPage属性跳转到error.jsp页面**上

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" errorPage="error.jsp" %>
<html>
<head>
    <title>该页面出错了！</title>
</head>
<body>
    <%--模拟页面出错了！！！--%>
    <%
        int result = 2 / 0;
    %>
    你好呀
</body>
</html>
```

- **error.jsp页面要通过page指令的isErrorPage属性设置页面就是错误页面**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" isErrorPage="true"   %>
<html>
    <head>
        <title>友好提示页面</title>
    </head>
    <body>
        服务器正忙着呢！
    </body>
</html>

```

- 我们可以在web.xml文件中全局设置错误页，**只要发生了404错误或者空指针异常的错误都会跳转到error.jsp页面上**

```xml
<error-page>
    <error-code>404</error-code>
    <location>/error.jsp</location>
</error-page>

<error-page>
    <exception-type>java.lang.NullPointerException</exception-type>
    <location>/error.jsp</location>
</error-page>
```

### include指令

- **在讲解request对象的时候，我们曾经使用过request.getRequestDispatcher(String url).include(request,response)来对页头和页尾面进行包含**
- inclue指令也是做这样的事情，我们来试验一下吧！
- 这是页头

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java"   %>
<html>
    <head>
        <title>页头</title>
    </head>
    <body>
    我是页头
    <br>
    <br>
    <br>
    </body>
</html>
```

- 这是页尾

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>页尾</title>
</head>
<body>

我是页尾

</body>
</html>
```

**在1.jsp中把页头和页尾包含进来**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>包含页头和页尾进来</title>
</head>
<body>


<%@include file="head.jsp" %>
<%@include file="foot.jsp" %>
</body>
</html>
```

- **include指令是静态包含**。静态包含的意思就是：**把文件的代码内容都包含进来，再编译！**，看一下jsp的源代码就知道了！

## JSP行为

> **JSP行为（JSP Actions）是一组JSP内置的标签**，只书写少量的标记代码就能够使用JSP提供丰富的功能，**JSP行为是对常用的JSP功能的抽象和封装**。

为什么我不把它直接称为JSP标签呢？**我把这些JSP内置的标签称之为JSP行为，能够和JSTL标签区分开来**。当然了，你也可以把它称之为JSP标签，你不要搞混就行了。我个人喜欢把这些JSP内置标签称之为JSP行为。

### include行为

- 上面已经提及到了，**include指令是静态包含，include行为是动态包含**。**其实include行为就是封装了request.getRequestDispatcher(String url).include(request,response)**
- include行为语法是这个样子的

```jsp
    <jsp:include page=""/>
```

- 我们先来使用一下把，**在1.jsp页面中也将页头和页尾包含进来**。

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>包含页头和页尾进来</title>
</head>
<body>
    <jsp:include page="head.jsp"/>
    <jsp:include page="foot.jsp"/>
</body>
</html>
```

- 当然了，现在有静态包含和动态包含，使用哪一个更好呢？**答案是：动态包含**。
- 动态包含可以**向被包含的页面传递参数（用处不大）**，并且是**分别处理包含页面的（将被包含页面编译后得出的结果再写进包含页面）【如果有相同名称的参数，使用静态包含就会报错！】**！
- 模拟一下场景吧，现在我的头页面有个名为s的字符串变量

### param行为

- 在讲解request对象的时候，我们使用request.getRequestDispatcher(String url).forward(request,response)进行跳转。**其实forward行为就是对其封装**！
- 我们来看一下forward的语法：

```jsp
<jsp:forward page=""/>
```

- 好的，我们来使用一下吧。**访问1.jsp页面就跳转到head.jsp页面中**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>访问1.jsp就跳转到head.jsp</title>
</head>
<body>

<jsp:forward page="head.jsp"/>


</body>
</html>
```

- **如果我要传递参数，就要在forward行为嵌套param行为**
- 在跳转到head.jsp时传入参数username值为zhongfucheng

```jsp
<jsp:forward page="head.jsp">
    <jsp:param name="username" value="zhongfucheng"/>
</jsp:forward>
```

- 在head.jsp页面中获取到传递过来的参数

```jsp
<%
    String ss = request.getParameter("username");
%>

获取到的参数是：
<%=ss%>
```

### directive行为

- **directive的中文意思就是指令**。**该行为就是替代指令<%@%>的语法的**

- - 相当于<%@include file="" %>
  - 相当于<%@page %>
  - 相当于<%@taglib %>

- 我们来试一下能不能用的

```jsp
<jsp:directive.include file="head.jsp"></jsp:directive.include>
<jsp:directive.include file="foot.jsp"></jsp:directive.include>
```

- **使用该指令可以让JSP页面更加美观**！
- 使用scriptlet行为``替代<%%>是同样一个道理

### javaBean行为

- **JSP还提供了操作javaBean对象的行为**，**在这里就不详细说明了，后面会讲到的！现在记住JSP提供了javaBean行为来操作简单类即可！**

```jsp
<jsp:useBean id=""/>

<jsp:setProperty name="" property=""/>

<jsp:getProperty name="" property=""/>
```

