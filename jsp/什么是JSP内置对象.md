[TOC]

# 什么是JSP内置对象

> JSP引擎在调用JSP对应的jspServlet时，**会传递或创建9个与web开发相关的对象供jspServlet使用**。JSP技术的设计者为便于开发人员在编写JSP页面时获得这些web对象的引用，特意定义了9个相应的变量，**开发人员在JSP页面中通过这些变量就可以快速获得这9大对象的引用**

细心的朋友会发现，**我们没有在JSP页面上定义过out对象，却可以直接使用！其实out对象就是JSP内置对象之一**。

九个内置对象：

- **pageContext**
- page
- config
- **request**
- **response**
- **session**
- **application**
- exception
- out

------

# out对象

out对象的API

- **int getBufferSize()【得到缓存大小】**
- **int getRemaining()【得到未使用缓存的大小】**
- boolean isAutoFlush()
- void println()
- void flush()
- void close()
- void clearBuffer()
- void clear()
- **out对象用于向浏览器输出数据，与之对应的是Servlet的PrintWriter对象**。然而这个out对象的类型并不是PrintWriter，是JspWriter