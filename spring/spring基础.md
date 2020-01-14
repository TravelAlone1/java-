[TOC]

# spring 

## 目的：

简化开发

## 具体简化开发：

- 创建Spring的目的就是用来**替代更加重量级的的企业级Java技术**

- **简化Java的开发**

- - 基于POJO轻量级和**最小侵入式开发**
  - 通过依赖注入和面向接口实现**松耦合**
  - **基于切面**和惯例进行声明式编程
  - 通过切面和模板**减少样板式代码 **

## 核心思想之一 IOC控制反转

**概念：对象的创建交给外部容器完成**

### 注意事项：

- spring使用控制反转来实现对象不用再程序中写死
- 控制反转解决对象处理问题【把对象交给别人创建】

### 依赖注入

目的：解决对象之间的依赖关系

#### 注意点：

- spring使用依赖注入来实现对象之间的依赖
- 在创建完对象之后，对象的关系处理就是依赖注入

总结：控制反转是通过外部容器完成的，**而spring为我们提供了这个一个容器，我们一般把这个容器叫做：IOC容器。**

#### IOC控制反转这一思想的作用

- **资源集中管理，实现资源的可配置和易管理**
- **降低了使用资源双方的依赖程度，也就是我们说的耦合度**。

## 核心思想之二 AOP

概念：面向切面编程

原理（）：通过预编译方式和运行期动态代理实现陈旭功能的统一维护的一种技术。

使用：通过对既有程序定义一个切入点，然后在其前后切入不同的执行内容

常见使用场景：打开数据库的连接/关闭数据库的连接、打开事务/关闭事务、记录日志等。

好处：基于AOP不会破坏原来的程序逻辑，因此他可以很好的对业务逻辑的各个部分进行隔离，从而使得业务逻辑各个部分之间的耦合度降低，提高程序的可用性，同时提高了开发效率。

#### Spring容器不单单只有一个，可以归为两种类型

- **Bean工厂，BeanFactory【功能简单】 **
- **应用上下文，ApplicationContext【功能强大，一般我们使用这个】**

## spring 配置bean对象

- 使用xml文件配置
- 使用注解来配置
- 使用JavaConfig来配置

### **类路径下XML获取ApplicationContext**

- **直接通过ClassPathXmlApplicationContext对象来获取**

```java
 //得到IOC容器
ApplicationContext ac = new ClassPathResource("applicationContext.xml");	 
```



### 1.xml配置方式

在已经获得IOC容器的对象情况下。接下来就是在applicationContext.xml文件配置信息【让IOC容器根据application.xml文件来创建对象】

- 首先有个javaBean类

```java
/**
 * Created by ozc on 2017/5/10.
 */
public class User {

    private String id;
    private String username;


    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }
}
```

- 以前我们是通过new User的方法创建对象的….

```java
    User user = new User();
```

- 现在我们有了IOC容器，可以让IOC容器帮我们创建对象了。在applicationContext.xml文件中配置对应的信息就行了

```java
       <!--
        使用bean节点来创建对象
            id属性标识着对象
            name属性代表着要创建对象的类全名
        -->
    <bean id="user" class="User"/>
```

**通过IOC容器对象获取对象:**

- **在外界通过IOC容器对象得到User对象**

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/5BC97130217944DA8D56C8B5E283C13E/9467)

上面我们使用的是IOC通过无参构造函数来创建对象，我们来回顾一下一般有几种创建对象的方式：

- 无参构造函数创建对象

- 带参数的构造函数创建对象

- 工厂创建对象

- - 静态方法创建对象
  - 非静态方法创建对象

使用无参的构造函数创建对象我们已经会了，接下来我们看看使用剩下的IOC容器是怎么创建对象的

### 带参数的构造函数创建对象

首先，**JavaBean就要提供带参数的构造函数：**

```java
    public User(String id, String username) {
        this.id = id;
        this.username = username;
    }
```

接下来，关键是怎么配置applicationContext.xml文件了。

```java
    <bean id="user" class="User">

        <!--通过constructor这个节点来指定构造函数的参数类型、名称、第几个-->
        <constructor-arg index="0" name="id" type="java.lang.String" value="1"></constructor-arg>
        <constructor-arg index="1" name="username" type="java.lang.String" value="zhongfucheng"></constructor-arg>
    </bean>
```

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/70ECD221ACDE4B04BA5CA0DE08EBDD74/9469)

在constructor上**如果构造函数的值是一个对象，而不是一个普通类型的值，我们就需要用到ref属性了，而不是value属性**

比如说：**我在User对象上维护了Person对象的值，想要在构造函数中初始化它**。因此，就需要用到ref属性了

```java
<bean id="person" class="Person"></bean> 

    <bean id="user" class="User" >

        <!--通过constructor这个节点来指定构造函数的参数类型、名称、第几个-->
        <constructor-arg index="0" name="id" type="java.lang.String" value="1"></constructor-arg>
        <constructor-arg index="1" name="username" type="java.lang.String" ref="person"></constructor-arg>
    </bean>
```

