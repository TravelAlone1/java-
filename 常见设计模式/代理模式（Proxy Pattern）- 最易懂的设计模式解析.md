# 代理模式（Proxy Pattern

## 目录

![img](https://note.youdao.com/yws/public/resource/0acf8c88b2fe8f87c969b16b58a976dd/xmlnote/32FDCC1124494E4D852E232E3752D384/9947)

# 1. 介绍

### 1.1 定义

给目标对象提供一个代理对象，并由代理对象控制对目标对象的引用

> 1. 代理对象：起到中介作用，连接客户端和目标对象
> 2. 例子：电脑桌面的快捷方式。电脑对某个程序提供一个快捷方式（代理对象），快捷方式连接客户端和程序，客户端通过操作快捷方式就可以操作那个程序

如果从写代码的角度出发，当我们遇到以下场景：

- 无法直接访问某个对象
- 不想直接访问某个对象
- 访问某个对象存在困难

的时候，我们就可以通过一个代理，通过它来间接访问真正的对象。

### 理解

代理就像对象的镜像一样。也正因为如此，代理对象不仅可以覆盖真实对象，还可以扩展其功能。因此，对于只能在屏幕上打印一些文本的对象，我们可以添加另一个对象来过滤显示单词。可以通过代理来定义第二个对象的调用。**代理是封装真实对象的对象**。例如，如果您尝试调用Waiter bean，那么您将调用该Bean的代理，其行为方式完全相同。

### 1.2 主要作用

通过引入**代理对象**的方式来间接访问**目标对象**

### 1.3 解决的问题

防止**直接**访问目标对象给系统带来的不必要复杂性。

# 2. 模式原理

### 2.1 UML类图 & 组成

![img](https://note.youdao.com/yws/public/resource/0acf8c88b2fe8f87c969b16b58a976dd/xmlnote/0472C87BE2684FC59E381F8682E28DAD/9950)

### 2.2 实例讲解

接下来我用一个实例来对代理模式进行更深一步的介绍。
**a. 实例概况**

- 背景：小成希望买一台最新的顶配Mac电脑
- 冲突：国内还没上，只有美国才有
- 解决方案：寻找代购进行购买

> 代购（代理对象） 代替 我（真实对象） 去买Mac（间接访问的操作）

**b. 使用步骤**
**步骤1：** 创建抽象对象接口（Subject）：声明你（真实对象）需要让代购（代理对象）帮忙做的事（买Mac）

```java
public interface Subject {  
              public void buyMac();
}
```

步骤2： 创建真实对象类（RealSubject）,即”我“
```java
  public class RealSubject implement Subject{
    @Override
    public void buyMac() {  
        System.out.println(”买一台Mac“);  
    }  
}
```

步骤3：创建代理对象类（Proxy），即”代购“，并通过代理类创建真实对象实例并访问其方法
```java
public class Proxy  implements Subject{

    @Override
    public void buyMac{
      
      //引用并创建真实对象实例，即”我“
      RealSubject realSubject = new RealSubject()；
    
      //调用真实对象的方法，进行代理购买Mac
      realSubject.buyMac（）；
      //代理对象额外做的操作
      this.WrapMac()；
    }
    
     public void WrapMac(){
      System.out.println(”用盒子包装好Mac“);  
    }
}
```

步骤4：客户端调用

```java
public class ProxyPattern {

    public static void main(String[] args){
    
    Subject proxy = new Proxy（）；
    proxy.buyMac()；
    }

}
```

结果输出

```text
买一台Mac
用盒子包装好Mac
```

## 动态代理

### 1.1 背景
代理模式中的静态代理模式存在一些特点：

- 1个静态代理 只服务1种类型的目标对象
- 若要服务多类型的目标对象，则需要为每种目标对象都实现一个静态代理对象
> 关于静态代理模式可以看文章：代理模式（Proxy Pattern）：静态代理 - 最易懂的设计模式解析

### 1.2 冲突
在目标对象较多的情况下，若采用静态代理，则会出现 静态代理对象量多、代码量大，从而导致代码复杂的问题

### 1.3 解决方案
采用 动态代理模式。

## 动态代理模式介绍
### 2.1 实现原理
- 设计动态代理类（DynamicProxy）时，**不需要显式实现与目标对象类（RealSubject）相同的接口**，而是将这种实现推迟到程序运行时由 JVM来实现

  > 即：在使用时再创建动态代理类 & 实例
  > 静态代理则是在代理类实现时就指定与目标对象类（RealSubject）相同的接口

  

- 通过Java 反射机制的method.invoke（），通过调用动态代理类对象方法，从而自动调用目标对象的方法

### 2.2 优点

- 只需要一个动态代理类就可以解决创建多个静态代理的问题，避免重复、多余的代码
- 更强的灵活性

> 1. 设计动态代理类（`DynamicProxy`）时，**不需要显式实现与目标对象类（RealSubject）相同的接口**，而是将这种实现推迟到程序运行时由 `JVM`来实现
> 2. 在使用时（调用目标对象方法时）才会动态创建动态代理类 & 实例，不需要事先实例化

### 2.3 缺点

- 效率低
  相比静态代理中 **直接调用目标对象方法**，动态代理则需要先通过`Java`反射机制 从而 **间接调用目标对象方法**

- 应用场景局限
  因为 Java 的单继承特性（每个代理类都继承了 Proxy 类），即只能**针对接口** 创建 代理类，**不能针对类** 创建代理类

  > 即只能动态代理 **实现了接口**的类

2.4 应用场景
基于静态代理应用场景下，需要代理对象数量较多的情况下使用动态代理

> AOP 领域
> 定义：即 Aspect Oriented Programming = 面向切面编程，是OOP的延续、函数式编程的一种衍生范型
> 作用：通过预编译方式和运行期动态代理实现程序功能的统一维护。
> 优点：降低业务逻辑各部分之间的耦合度 、 提高程序的可重用性 & 提高了开发的效率
> 具体应用场景：日志记录、性能统计、安全控制、异常处理等

## 具体应用

声明目标对象的抽象接口

```java
package com.lx.design.proxy.dynamicProxy;

public interface Programmer {

    void coding();
}
```

 声明目标对象类

```java
package com.lx.design.proxy.dynamicProxy;

public class Java3y implements Programmer {
    @Override
    public void coding() {
        System.out.println("动态代理模式");
    }
}

```

通过动态代理对象，调用目标对象的方法

```java
package com.lx.design.proxy.dynamicProxy;

import java.lang.reflect.Proxy;

public class Mian {

    public static void main(String[] args) {
        Java3y java3y = new Java3y();
        Programmer programmerWaterArmy = (Programmer)Proxy.newProxyInstance(java3y.getClass().getClassLoader(), java3y.getClass().getInterfaces(), (proxy, method, args1) -> {
            if (method.getName().equals("coding")) {
                method.invoke(java3y, args1);
                System.out.println("我是水军");
            } else {
                return method.invoke(java3y, args1);
            }
            return null;
        });
        programmerWaterArmy.coding();
    }
}
```

## 动态代理调用的过程

我们来看看究竟是怎么请水军的：

**Java提供了一个Proxy类，调用它的newInstance方法可以生成某个对象的代理对象,该方法需要三个参数：**

![img](https://note.youdao.com/yws/public/resource/0acf8c88b2fe8f87c969b16b58a976dd/xmlnote/CDC9094BF0D9475E87756682361FB24B/9953)

- 参数一：生成代理对象使用哪个类装载器【一般我们使用的是被代理类的装载器】
- 参数二：生成哪个对象的代理对象，通过接口指定【指定要被代理类的接口】
- 参数三：生成的代理对象的方法里干什么事【实现handler接口，我们想怎么实现就怎么实现】

在编写动态代理之前，要明确几个概念：

- **代理对象拥有目标对象相同的方法【因为参数二指定了对象的接口，代理对象会实现接口的所有方法】**
- **用户调用代理对象的什么方法，都是在调用处理器的invoke方法。【被拦截】**
- **使用JDK动态代理必须要有接口【参数二需要接口】**

上面也说了：代理对象会实现接口的所有方法，这些实现的方法交由我们的**handler**来处理！

- 所有通过动态代理**实现的方法全部**通过`invoke()`调用

![img](https://mmbiz.qpic.cn/mmbiz_png/2BGWl1qPxib2nymuN7l1FeDlo1YfKkahUbXuRz969eIsB6wtvRJ1ETUEOSQut3U1XSibP4kJRI8ZLeUnia2WbUDYg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

所以**动态代理调用过程**是这样子的：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/2BGWl1qPxib2nymuN7l1FeDlo1YfKkahUb2rib2VQNqXSVyFM2TfyfziaxPFnicKD4ickLghicrDLUibnFXAWuhibBNribA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

# 四、典型应用

我们之前写中文过滤器的时候，需要使用包装设计模式来设计一个request类。如果不是Servlet提供了实现类给我们，我们使用包装设计模式会比较麻烦

现在我们学习了动态代理了，动态代理就是**拦截直接访问对象，可以给对象进行增强的一项技能**

## 4.1中文过滤器

```java
    public void doFilter(final ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
        final HttpServletRequest request = (HttpServletRequest) req;
        HttpServletResponse response = (HttpServletResponse) resp;

        response.setContentType("text/html;charset=UTF-8");
        request.setCharacterEncoding("UTF-8");


        //放出去的是代理对象
        chain.doFilter((ServletRequest) Proxy.newProxyInstance(CharacterEncodingFilter.class.getClassLoader(), request.getClass().getInterfaces(), new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

                //判断是不是getParameter方法
                if (!method.getName().equals("getParameter")) {

                    //不是就使用request调用
                   return method.invoke(request, args);
                }

                //判断是否是get类型的
                if (!request.getMethod().equalsIgnoreCase("get")) {
                   return method.invoke(request, args);
                }

                //执行到这里，只能是get类型的getParameter方法了。
                String value = (String) method.invoke(request, args);
                if (value == null) {
                    return null;
                }
                return new String(value.getBytes("ISO8859-1"), "UTF-8");
            }

        }), response);

    }
```