[TOC]

# 线程

## ThreadLocal就是这么简单

总结：让当前线程中有属于自己的变量，并不是解决并发或者共享变量的问题

### **一、什么是ThreadLocal**

提供线程的局部变量，每个线程可以通过set()和get()来对这个变量进行操作，但不会和其他线程变量起冲突，实现了线程之间的数据隔离。

### **二、为什么要学习ThreadLocal？**

#### 2.1管理Connection

**最典型的是管理数据库的Connection：**当时在学JDBC的时候，为了方便操作写了一个简单数据库连接池，需要数据库连接池的理由也很简单，频繁创建和关闭Connection是一件非常耗费资源的操作，因此需要创建数据库连接池～

那么，数据库连接池的连接怎么管理呢？？我们交由ThreadLocal来进行管理。为什么交给它来管理呢？？ThreadLocal能够实现**当前线程的操作都是用同一个Connection，保证了事务！**

当时候写的代码：

```java
public class DBUtil {

    //数据库连接池
    private static BasicDataSource source;

    //为不同的线程管理
    private static ThreadLocal<Connection> local;

    static {
        try {

            //加载配置文件
            Properties properties = new Properties();

            //获取读取流
            InputStream stream = DBUtil.class.getClassLoader().getResourceAsStream("db_config.properties");

            //从配置文件中加载数据
            properties.load(stream);

            //关闭流
            stream.close();

            //初始化连接池
            source=new BasicDataSource();

            //设置驱动
            source.setDriverClassName(properties.getProperty("driverClassName"));
            //设置url
            source.setUrl(properties.getProperty("url"));
            //设置用户名
            source.setUsername(properties.getProperty("username"));
            //设置密码
            source.setPassword(properties.getProperty("password"));
            //
            source.setInitialSize(Integer.valueOf(properties.getProperty("initialSize")));
            source.setMaxActive(Integer.valueOf(properties.getProperty("maxActive")));
            source.setMaxWait(Integer.valueOf(properties.getProperty("maxWait")));
            source.setMinIdle(Integer.valueOf(properties.getProperty("minidle")));
            local=new ThreadLocal<>();

        }catch (IOException e){
            e.printStackTrace();
        }
    }

    public static Connection getConnection() throws SQLException{
        //获取Connection对象
        Connection connection = source.getConnection();

        //把Connection放进ThreadLocal里面
        local.set(connection);

        //返回Connection对象
        return connection;

    }

    public static void closeConnection(){
        Connection connection = local.get();
        try {
            if (connection != null){
                connection.setAutoCommit(true);
                connection.close();
                local.remove();
            }
        }catch (SQLException e){
            e.printStackTrace();
        }
    }
}
```

## 创建线程的方式

1.继承Thread类（本质实现Runable接口）

2.实现Runable接口

3.实现cabb

## Thread线程类

### 1.设置线程名字

调用`Thread.currentThread().getName()`即可。

如果没有做什么的设置，我们会发现线程的名字是这样子的：**主线程叫做main，其他线程是Thread-x**

如果我们想要为线程起个名字，那也是很简单的。**Thread给我们提供了构造方法**！

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/D8F055D98499440FAFC2443A93C3172D/9460)

### 守护线程

守护线程是**为其他线程服务的**

- **垃圾回收线程就是守护线程**~

守护线程有一个**特点**：

- 当别的用户线程执行完了，虚拟机就会退出，守护线程也就会被停止掉了。
- 也就是说：守护线程作为一个**服务线程，没有服务对象就没有必要继续运行**了

### 设置优先级

线程优先级高仅仅表示线程**获取的CPU时间片的几率高**，但这不是一个**确定的因素**！

可以看到的是，Java提供的优先级默认是5，最低是1，最高是10：

### 1.4线程生命周期

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/8AFB3DBFCA364C3F927AC688554B6403/9464)

### interrupt方法

线程中断在之前的版本有stop方法，但是被设置过时了。现在已经**没有强制线程终止**的方法了！

由于stop方法可以让**一个线程A终止掉另一个线程B**

- 被终止的线程B会立即释放锁，这可能会让**对象处于不一致的状态**。
- **线程A也不知道线程B什么时候能够被终止掉**，万一线程B还处理运行计算阶段，线程A调用stop方法将线程B终止，那就很无辜了~

总而言之，Stop方法太暴力了，不安全，所以被设置过时了。

我们一般使用的是interrupt来**请求终止线程**~

## 多线程常见的问题

### 1线程安全问题

共享变量同时被多个线程读取

**下面的程序在单线程中跑起来，是没有问题的**。

```java
ublic class UnsafeCountingServlet extends GenericServlet implements Servlet {
    private long count = 0;

    public long getCount() {
        return count;
    }

    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {

        ++count;
        // To something else...
    }
}
```

但是在多线程环境下跑起来，它的**count值计算**就不对了！

首先，它**共享**了count这个变量，其次来说`++count;`这是一个**组合的操作**(注意，**它并非是原子性**）

- `++count`实际上的操作是这样子的：

- - 读取count值
  - 将值+1
  - 将计算结果写入count

于是多线程执行的时候**很可能**就会有这样的情况：

- 当线程A读取到count的值是8的时候，**同时**线程B也进去这个方法上了，也是读取到count的值为8
- 它俩都对值进行加1
- 将计算结果写入到count上。但是，**写入到count上的结果是9**
- 也就是说：两个线程进来了，但是**正确的结果是应该返回10**，而它返回了9，这是不正常的！

如果说：当多个线程访问某个类的时候，这个类**始终**能表现出**正确的行为**，那么这个类就是线程安全的！

有个原则：**能使用JDK提供的线程安全机制，就使用JDK的**。

### 3性能问题

使用多线程我们的目的就是为了提高应用程序的使用率，但是如果多线程的代码**没有好好设计**的话，那未必会提高效率。**反而降低了效率，甚至会造成死锁**！

就比如说我们的Servlet，一个Servlet对象可以处理多个请求的，**Servlet显然是一个天然支持多线程的**。

又以下面的例子来说吧：

```java
public class UnsafeCountingServlet extends GenericServlet implements Servlet {
    private long count = 0;

    public long getCount() {
        return count;
    }

    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {

        ++count;
        // To something else...
    }
}
```

从上面我们已经说了，上面**这个类是线程不安全的**。最简单的方式：如果我们在service方法上加上JDK为我们提供的内置锁synchronized，那么我们就可以实现线程安全了。

```java
public class UnsafeCountingServlet extends GenericServlet implements Servlet {
    private long count = 0;

    public long getCount() {
        return count;
    }

    public void synchronized service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {

        ++count;
        // To something else...
    }
}
```



虽然实现了线程安全了，但是这会带来**很严重的性能问题**：

- 每个请求都得**等待**上一个请求的service方法**处理了以后**才可以完成对应的操作

这就导致了：我们完成一个小小的功能，使用了多线程的目的是想要提高效率，但现在**没有把握得当，却带来严重的性能问题**！

在使用多线程的时候：更严重的时候还有**死锁**(程序就卡住不动了)。

这些都是我们接下来要学习的地方：**学习使用哪种同步机制来实现线程安全，并且性能是提高了而不是降低了**~

### 3.1简述解决线程安全性的办法

使用多线程就一定要**保证我们的线程是安全的**，这是最重要的地方！

在Java中，我们一般会有下面这么几种办法来实现线程安全问题：

- 无状态(**没有共享变量**)

- 使用final使该引用变量不可变(如果该对象引用也引用了其他的对象，那么无论是发布或者使用时都需要加锁)

- 加锁(内置锁，显示Lock锁)

- 使用JDK为我们提供的类来实现线程安全(此部分的类就很多了)

- - 原子性（就比如上面的`count++`操作，可以使用AtomicLong来实现原子性，那么在增加的时候就不会出差错了！)
  - 容器(ConcurrentHashMap等等…)
  - ……

- …等等

## 线程池

### 线程池的作用

1. 减少资源开销

减少每次创建线程、销毁线程的开销。

2. 提高响应速度 
   每次请求到来时，由于线程的创建已经完成，故可以直接执行任务，因此提高了响应速度。
3. 提高线程的可管理性 
   线程是一种稀缺资源，若不加以限制，不仅会占用大量资源，而且会影响系统的稳定性。 
   因此，线程池可以对线程的创建与停止、线程数量等等因素加以控制，使得线程在一种可控的范围内运行，不仅能保证系统稳定运行，而且方便性能调优。

### 线程池的实现原理

线程池一般由两种角色构成：多个工作线程 和 一个阻塞队列。

- 工作线程 
  工作线程是一组已经处在运行中的线程，它们不断地向阻塞队列中领取任务执行。
- 阻塞队列 
  阻塞队列用于存储工作线程来不及处理的任务。当工作线程都在执行任务时，到来的新任务就只能暂时在阻塞队列中存储。

## ThreadPoolExecutor的使用

## 创建线程池

通过如下代码即可创建一个线程池：

```java
new ThreadPoolExecutor(corePoolSize, maximumPoolSize, keepAliveTime, timeUnit, runnableTaskQueue, handler);
```

- corePoolSize：基本线程数量 
  它表示你希望线程池达到的一个值。线程池会尽量把实际线程数量保持在这个值上下。
- maximumPoolSize：最大线程数量 
  这是线程数量的上界。 
  如果实际线程数量达到这个值：

1. 阻塞队列未满：任务存入阻塞队列等待执行
2. 阻塞队列已满：调用饱和策略

keepAliveTime：空闲线程的存活时间 
当实际线程数量超过corePoolSize时，若线程空闲的时间超过该值，就会被停止。 
PS：当任务很多，且任务执行时间很短的情况下，可以将该值调大，提高线程利用率。

timeUnit：keepAliveTime的单位

runnableTaskQueue：任务队列 
这是一个存放任务的阻塞队列，可以有如下几种选择：

1. ArrayBlockingQueue 
   它是一个由数组实现的阻塞队列，FIFO。
2. LinkedBlockingQueue 
   它是一个由链表实现的阻塞队列，FIFO。 
   吞吐量通常要高于ArrayBlockingQueue。 
   fixedThreadPool使用的阻塞队列就是它。 
   它是一个无界队列。
3. SynchronousQueue 
   它是一个没有存储空间的阻塞队列，任务提交给它之后必须要交给一条工作线程处理；如果当前没有空闲的工作线程，则立即创建一条新的工作线程。 
   cachedThreadPool用的阻塞队列就是它。 
   它是一个无界队列。
4. PriorityBlockingQueue 
   它是一个优先权阻塞队列。

handler：饱和策略 
当实际线程数达到maximumPoolSize，并且阻塞队列已满时，就会调用饱和策略。 
JDK1.5由四种饱和策略：

1. AbortPolicy 
   默认。直接抛异常。
2. CallerRunsPolicy 
   只用调用者所在的线程执行任务。
3. DiscardOldestPolicy 
   丢弃任务队列中最久的任务。
4. DiscardPolicy 
   丢弃当前任务。



## 提交任务

可以向ThreadPoolExecutor提交两种任务：Callable和Runnable。

1. Callable 
   该类任务有返回结果，可以抛出异常。 
   通过submit函数提交，返回Future对象。 
   可通过get获取执行结果。
2. Runnable 
   该类任务只执行，无法获取返回结果，并在执行过程中无法抛异常。 
   通过execute提交。



## 关闭线程池

关闭线程池有两种方式：shutdown和shutdownNow，关闭时，会遍历所有的线程，调用它们的interrupt函数中断线程。但这两种方式对于正在执行的线程处理方式不同。

1. shutdown() 
   仅停止阻塞队列中等待的线程，那些正在执行的线程就会让他们执行结束。
2. shutdownNow() 
   不仅会停止阻塞队列中的线程，而且会停止正在执行的线程。



## ThreadPoolExecutor运行机制

当有请求到来时：

1. 若当前实际线程数量 **少于** corePoolSize，即使有空闲线程，也会创建一个新的工作线程；
2. 若当前实际线程数量处于corePoolSize和maximumPoolSize之间，并且阻塞队列没满，则任务将被放入阻塞队列中等待执行；
3. 若当前实际线程数量 **小于** maximumPoolSize，但阻塞队列已满，则直接创建新线程处理任务；
4. 若当前实际线程数量已经达到maximumPoolSize，并且阻塞队列已满，则使用饱和策略。



## 设置合理的线程池大小

任务一般可分为：CPU密集型、IO密集型、混合型，对于不同类型的任务需要分配不同大小的线程池。

- CPU密集型任务 
  尽量使用较小的线程池，一般为CPU核心数+1。 
  因为CPU密集型任务使得CPU使用率很高，若开过多的线程数，只能增加上下文切换的次数，因此会带来额外的开销。
- IO密集型任务 
  可以使用稍大的线程池，一般为2*CPU核心数。 
  IO密集型任务CPU使用率并不高，因此可以让CPU在等待IO的时候去处理别的任务，充分利用CPU时间。
- 混合型任务 
  可以将任务分成IO密集型和CPU密集型任务，然后分别用不同的线程池去处理。 
  只要分完之后两个任务的执行时间相差不大，那么就会比串行执行来的高效。 
  因为如果划分之后两个任务执行时间相差甚远，那么先执行完的任务就要等后执行完的任务，最终的时间仍然取决于后执行完的任务，而且还要加上任务拆分与合并的开销，得不偿失。