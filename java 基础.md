[TOC]

# java 基础

#### **一个".java"源文件中是否可以包括多个类（不是内部类）？有什么限制？**

```
 可以有多个类，但只能有一个public的类，并且public的类名必须与文件名相一致。
```



#### **Java有没有goto?**

 java中的保留字，现在没有在java中使用。

#### **switch语句能否作用在byte上，能否作用在long上，能否作用在String上?**



####  **short s1= 1; s1 = （s1+1是int类型，而等号左边的是short类型，所以需要强转）1 + 1;有什么错? short s1 = 1; s1 += 1;有什么错?(没有错)**



对于short s1= 1; s1 = s1 + 1;由于s1+1运算时会自动提升表达式的类型，所以结果是int型，再赋值给short类型s1时，编译器将报告需要强制转换类型的错误。

​        对于short s1= 1; s1 += 1;由于 +=是java语言规定的运算符，java编译器会对它进行特殊处理，因此可以正确编译。

#### **char型变量中能不能存贮一个中文汉字?为什么?**

char型变量是用来存储Unicode编码的字符的，unicode编码字符集中包含了汉字，所以，char型变量中当然可以存储汉字啦。不过，如果某个特殊的汉字没有被包含在unicode编码字符集中，那么，这个char型变量中就不能存储这个特殊汉字。补充说明：unicode编码占用两个字节，所以，char类型的变量也是占用两个字节。

#### **用最有效率的方法算出2乘以8等於几?**

​    2<< 3，(左移三位)因为将一个数左移n位，就相当于乘以了2的n次方，那么，一个数乘以8只要将其左移3位即可，而位运算cpu直接支持的，效率最高，所以，2乘以8等於几的最效率的方法是2<< 3。

#### **使用final关键字修饰一个变量时，是引用不能变，还是引用的对象不能变？**

 使用final关键字修饰一个变量时，是指引用变量不能变，引用变量所指向的对象中的内容还是可以改变的。例如，对于如下语句：

 *finalStringBuffer a=new StringBuffer("immutable");*

```java
 finalStringBuffer a=new StringBuffer("immutable");
```



执行如下语句将报告编译期错误：

```java
a=new StringBuffer("");
```

但是，执行如下语句则可以通过编译：

```java
a.append(" broken!");
```

有人在定义方法的参数时，可能想采用如下形式来阻止方法内部修改传进来的参数对象：

```java
public void method(final  StringBuffer param){

}
```

实际上，这是办不到的，在该方法内部仍然可以增加如下代码来修改参数对象：

```java
param.append("a");
```

#### **静态变量和实例变量的区别？**

在语法定义上的区别：静态变量前要加static关键字，而实例变量前则不加。

​        在程序运行时的区别：实例变量属于某个对象的属性，必须创建了实例对象，其中的实例变量才会被分配空间，才能使用这个实例变量。静态变量不属于某个实例对象，而是属于类，所以也称为类变量，只要程序加载了类的字节码，不用创建任何实例对象，静态变量就会被分配空间，静态变量就可以被使用了。总之，实例变量必须创建对象后才可以通过这个对象来使用，静态变量则可以直接使用类名来引用。

​        例如，对于下面的程序，无论创建多少个实例对象，永远都只分配了一个staticVar变量，并且每创建一个实例对象，这个staticVar就会加1；但是，每创建一个实例对象，就会分配一个instanceVar，即可能分配多个instanceVar，并且每个instanceVar的值都只自加了1次。

```java
public class VariantTest{
        publicstatic int staticVar = 0;
        publicint instanceVar = 0;
        publicVariantTest(){
              staticVar++;
              instanceVar++;
              System.out.println(staticVar +instanceVar);
        }
}
```

#### **接口是否可继承接口?抽象类是否可实现(implements)接口?抽象类是否可继承具体类(concreteclass)?抽象类中是否可以有静态的main方法？**

  接口可以继承接口。抽象类可以实现(implements)接口，抽象类可以继承具体类。抽象类中可以有静态的main方法。

​        备注：只要明白了接口和抽象类的本质和作用，这些问题都很好回答，你想想，如果你是java语言的设计者，你是否会提供这样的支持，如果不提供的话，有什么理由吗？如果你没有道理不提供，那答案就是肯定的了。

​        只要记住抽象类与普通类的唯一区别就是不能创建实例对象和允许有abstract方法。

#### **Java中实现多态的机制是什么？**

  靠的是父类或接口定义的引用变量可以指向子类或具体实现类的实例对象，而程序调用的方法在运行期才动态绑定，就是引用变量所指向的具体实例对象的方法，也就是内存里正在运行的那个对象的方法，而不是引用变量的类型中定义的方法。

#### **abstractclass和interface语法上有什么区别?**

1.抽象类可以有构造方法，接口中不能有构造方法。

2.抽象类中可以有普通成员变量，接口中没有普通成员变量

3.抽象类中可以包含非抽象的普通方法，接口中的所有方法必须都是抽象的，不能有非抽象的普通方法。

\4. 抽象类中的抽象方法的访问类型可以是public，protected和（默认类型,虽然

eclipse下不报错，但应该也不行），但接口中的抽象方法只能是public类型的，并且默认即为public abstract类型。

\5. 抽象类中可以包含静态方法，接口中不能包含静态方法

\6. 抽象类和接口中都可以包含静态成员变量，抽象类中的静态成员变量的访问类型可以任意，但接口中定义的变量只能是publicstatic final类型，并且默认即为publicstatic final类型。

\7. 一个类可以实现多个接口，但只能继承一个抽象类。

####  **Java 中，throw 和 throws 有什么区别**

​        throw 用于抛出 java.lang.Throwable 类的一个实例化对象，意思是说你可以通过关键字 throw 抛出一个Exception，如： 
*throw new IllegalArgumentException(“XXXXXXXXX″)*

​        而throws 的作用是作为方法声明和签名的一部分，方法被抛出相应的异常以便调用者能处理。Java 中，任何未处理的受检查异常强制在 throws 子句中声明。





