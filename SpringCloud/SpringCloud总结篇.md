[TOC]

# SpringCloud总结篇

### 集群

同一个业务，部署在多个服务器上（不同的服务器运行同样的代码，干同一件事）

### 分布式

一个业务分拆多个子业务，部署在不同的服务器上（不同的服务器，运行不同的代码，为了同一个目的）

### 微服务

### 三、CAP理论

从上面所讲的分布式概念我们已经知道，分布式简单理解就是：**一个业务分拆多个子业务，部署在不同的服务器上**

- 一般来说，一个子业务我们称为**节点**。

如果你接触过一些分布式的基础概念，那肯定会听过CAP这个理论。就比如说：你学了MySQL的InnoDB存储引擎相关知识，你肯定听过ACID！

首先，我们来看一下CAP分别代表的是什么意思：

- C：数据一致性(consistency)

- - **所有**节点拥有数据的最新版本

- A：可用性(availability)

- - 数据具备高可用性

- P：分区容错性(partition-tolerance)

- - **容忍网络出现分区**，分区之间网络不可达。

## 3.1再次梳理一下CAP理论

一般我们说的分布式系统，P：分区容错性(partition-tolerance)这个是**必需**的，这是客观存在的。

CAP是无法完全兼顾的，从上面的例子也可以看出，我们可以选AP，也可以选CP。但是，要**注意的是**：不是说选了AP，C就完全抛弃了。不是说选了CP，A就完全抛弃了！

在CAP理论中，**C所表示的一致性是强一致性**(每个节点的数据都是最新版本)，其实一致性还有其他级别的：

- 弱一致性：弱一致性是相对于强一致性而言，它不保证总能得到最新的值；
- 最终一致性(eventual consistency)：放宽对时间的要求，在被调完成操作响应后的某个时间点，被调多个节点的数据最终达成一致

可用性的值域可以定义成**0到100%的连续区间**。

所以，**CAP理论定义的其实是在容忍网络分区的条件下，“强一致性”和“极致可用性”无法同时达到**。

### 为什么需要SpringCloud

SpringCloud的**基础功能**：

- 服务治理： Spring  Cloud Eureka
- 客户端负载均衡： Spring Cloud Ribbon
- 服务容错保护： Spring  Cloud Hystrix  
- 声明式服务调用： Spring  Cloud Feign
- API网关服务：Spring Cloud Zuul
- 分布式配置中心： Spring Cloud Config

# 五、引出Eureka

解决模块之间的通信问题

**问题：**那会出现什么问题呢？？首当其冲的就是子系统之间的**通讯**问题。子系统与子系统之间不是在同一个环境下，那就需要**远程调用**。远程调用可能就会想到httpClient，WebService等等这些技术来实现。

既然是远程调用，就必须知道ip地址，我们可能有以下的场景。

- 功能实现一：A服务需要调用B服务

- - 在A服务的代码里面调用B服务，**显式通过IP地址调用**：`http://123.123.123.123:8888/java3y/3`

- 功能实现二：A服务调用B服务，B服务调用C服务，C服务调用D服务

- - 在A服务的代码里面调用B服务，显式通过IP地址调用：`http://123.123.123.123:8888/java3y/3`，(同样地)B->C，C->D

- 功能实现三：D服务调用B服务，B服务调用C服务

- - 在D服务的代码里面调用B服务，显式通过IP地址调用：`http://123.123.123.123:8888/java3y/3`，(同样地)B->C

- …..等等等等

万一，我们**B服务的IP地址变了**，想想会出现什么问题：A服务,D服务(等等)需要**手动更新**B服务的地址

- 在服务多的情况下，手动来维护这些静态配置就是噩梦！

> 为了解决微服务架构中的**服务实例维护问题(ip地址)**， 产生了大量的**服务治理**框架和产品。 这些框架和产品的实现都围绕着服务注册与服务发现机制来完成对微服务应用实例的**自动化管理**。

在SpringCloud中我们的服务治理框架一般使用的就是Eureka。

我们的问题：

- 现在有A、B、C、D四个服务，它们之间会互相调用(而且IP地址很可能会发生变化)，一旦某个服务的IP地址变了，那服务中的代码要跟着变，手动维护这些静态配置(IP)非常麻烦！

Eureka是这样解决上面所说的情况的：

- 创建一个E服务，将A、B、C、D四个服务的信息都**注册**到E服务上，E服务维护这些已经注册进来的信息

![](https://note.youdao.com/yws/public/resource/ab24ea43c3a2d551e54ab38113b55ecb/xmlnote/F7DDB6A1546843BC9917ACAFE71B5D74/9708)

A、B、C、D四个服务都可以**拿到**Eureka(服务E)那份**注册清单**。A、B、C、D四个服务互相调用不再通过具体的IP地址，而是**通过服务名来调用**！

- 拿到注册清单--->注册清单上有服务名--->自然就能够拿到服务具体的位置了(IP)。
- 其实简单来说就是：代码中通过**服务名找到对应的IP地址**(IP地址会变，但服务名一般不会变)

![](https://note.youdao.com/yws/public/resource/ab24ea43c3a2d551e54ab38113b55ecb/xmlnote/3A9FA895BB434BEDB8F7B1EAAB18C3E9/9712)

## 5.1Eureka细节

Eureka专门用于给其他服务注册的称为Eureka Server(服务注册中心)，其余注册到Eureka Server的服务称为Eureka Client。

![](https://note.youdao.com/yws/public/resource/ab24ea43c3a2d551e54ab38113b55ecb/xmlnote/20398CFDE3064BCCA3757048DAE9FB0B/9715)

在Eureka Server一般我们会这样配置：

```properties
server.port=8761
# 注册注册中心实例主机名
eureka.instance.hostname=localhost
#是否向服务注册中心注册自己
eureka.client.register-with-eureka=false
#是否检索服务
eureka.client.fetch-registry=false
#服务注册中心的配置内容，指定服务注册中心的位置
eureka.client.service-url.defaultZone=http://${eureka.instance.hostname}:${server.port}/eureka/
```

Eureka Client**分为服务提供者和服务消费者**。

- 但很可能，某服务**既是服务提供者又是服务消费者**。

如果在网上看到SpringCloud的**某个服务配置没有"注册"到Eureka-Server也不用过于惊讶**(但是它是可以获取Eureka服务清单的)

- 很可能只是作者把该服务认作为**单纯的服务消费者**，单纯的服务消费者无需对外提供服务，也就无须注册到Eureka中了

```yml
eureka:  
	client: 
		register-with-eureka: false  # 当前微服务不注册到eureka中(消费端)    
		service-url:  
			defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/  
```

下面是Eureka的治理机制：

- 服务提供者

- - **服务注册：**启动的时候会通过发送REST请求的方式将**自己注册到Eureka Server上**，同时带上了自身服务的一些元数据信息。
  - **服务续约：**在注册完服务之后，**服务提供者会维护一个心跳**用来持续告诉Eureka Server:  "我还活着 ” 、
  - **服务下线：**当服务实例进行正常的关闭操作时，它会**触发一个服务下线的REST请求**给Eureka Server, 告诉服务注册中心：“我要下线了 ”。

- 服务消费者

- - **获取服务：**当我们**启动服务消费者**的时候，它会发送一个REST请求给服务注册中心，来获取上面注册的服务清单
  - **服务调用：**服务消费者在获取服务清单后，通过**服务名**可以获得具体提供服务的实例名和该实例的元数据信息。在进行服务调用的时候，**优先访问同处一个Zone中的服务提供方**。

- Eureka Server(服务注册中心)：

- - **失效剔除：**默认每隔一段时间（默认为60秒） 将当前清单中超时（默认为90秒）**没有续约的服务剔除出去**。
  - **自我保护：**。EurekaServer 在运行期间，会统计心跳失败的比例在15分钟之内是否低于85%(通常由于网络不稳定导致)。 Eureka Server会将当前的**实例注册信息保护起来**， 让这些实例不会过期，尽可能**保护这些注册信息**。

# 六、引出RestTemplate和Ribbon

通过Eureka服务治理框架，我们可以通过服务名来获取具体的服务实例的位置了(IP)。一般在使用SpringCloud的时候**不需要自己手动创建**HttpClient来进行远程调用。

可以使用Spring封装好的**RestTemplate**工具类，使用起来很简单：

```java
// 传统的方式，直接显示写死IP是不好的！    
//private static final String REST_URL_PREFIX = "http://localhost:8001";    
// 服务实例名    
private static final String REST_URL_PREFIX = "http://MICROSERVICECLOUD-DEPT";    /**     
	* 使用 使用restTemplate访问restful接口非常的简单粗暴无脑。 (url, requestMap,     
	* ResponseBean.class)这三个参数分别代表 REST请求地址、请求参数、HTTP响应转换被转换成的对象类型。 
	*/    
@Autowired    
private RestTemplate restTemplate;    
@RequestMapping(value = "/consumer/dept/add")    
public boolean add(Dept dept) {        
    return restTemplate.postForObject(REST_URL_PREFIX + "/dept/add", dept, Boolean.class);    
}
```

为了实现服务的**高可用**，我们可以将**服务提供者集群**。比如说，现在一个秒杀系统设计出来了，准备上线了。在11月11号时为了能够支持高并发，我们开多台机器来支持并发量。

![](https://note.youdao.com/yws/public/resource/ab24ea43c3a2d551e54ab38113b55ecb/xmlnote/756837A650484791885DDD41CBCD9875/9718)

现在想要这三个秒杀系统**合理摊分**用户的请求(专业来说就是负载均衡)，可能你会想到nginx。

其实SpringCloud也支持的负载均衡功能，只不过它是**客户端的负载均衡**，这个功能实现就是Ribbon！

负载均衡又区分了两种类型：

- 客户端负载均衡(Ribbon)

- - 服务实例的**清单在客户端**，客户端进行负载均衡算法分配。
  - (从上面的知识我们已经知道了：客户端可以从Eureka Server中得到一份服务清单，在发送请求时通过负载均衡算法，**在多个服务器之间选择一个进行访问**)

- 服务端负载均衡(Nginx)

- - 服务实例的**清单在服务端**，服务器进行负载均衡算法分配

所以，我们的图可以画成这样：

![](https://note.youdao.com/yws/public/resource/ab24ea43c3a2d551e54ab38113b55ecb/xmlnote/362D29C4259B4685AD1FF70037B1E1DF/9723)

## 6.1Ribbon细节

Ribbon是支持负载均衡，默认的负载均衡策略是轮询，我们也是可以根据自己实际的需求自定义负载均衡策略的。

```java
@Configuration
public class MySelfRule{    
    @Bean    
    public IRule myRule(){        
        //return new RandomRule();
        // Ribbon默认是轮询，我自定义为随机        
        //return new RoundRobinRule();
        // Ribbon默认是轮询，我自定义为随机        return new RandomRule_ZY();
        // 我自定义为每台机器5次    }}
```

实现起来也很简单：继承AbstractLoadBalancerRule类，重写`public Server choose(ILoadBalancer lb, Object key)`即可。

SpringCloud 在CAP理论是选择了AP的，在Ribbon中还可以配置**重试机制**的(有兴趣的同学可以去搜搜)~

# 七、引出Hystrix

到目前为止，我们的服务看起来好像挺好的了：能够根据服务名来远程调用其他的服务，可以实现客户端的负载均衡。

![](https://note.youdao.com/yws/public/resource/ab24ea43c3a2d551e54ab38113b55ecb/xmlnote/F89811AD0F264EEDBCEF90845EFDD0EB/9727)

但是，如果我们在**调用多个远程服务时，某个服务出现延迟**，会怎么样？？

![](https://note.youdao.com/yws/public/resource/ab24ea43c3a2d551e54ab38113b55ecb/xmlnote/FAFB9F3F5F07452DA55DBF922F36BB25/9725)

#### 2.什么是Hystrix?

在一个分布式系统里，许多依赖不可避免的会调用失败，比如超时、异常等，如何能够保证在一个依赖出问题的情况下，不会导致整体服务失败，这个就是Hystrix需要做的事情。Hystrix提供了熔断、隔离、Fallback、cache、监控等功能，能够在一个、或多个依赖同时出现问题时保证系统依然可用。

**实际生产中问题**

在**高并发**的情况下，由于单个服务的延迟，可能导致**所有的请求都处于延迟状态**，甚至在几秒钟就使服务处于负载饱和的状态，资源耗尽，直到不可用，最终导致这个分布式系统都不可用，这就是“雪崩”。



**针对上述问题**， Spring Cloud Hystrix实现了**断路器、线程隔离**等一系列服务保护功能。

- Fallback(失败快速返回)：当某个服务单元发生故障（类似用电器发生短路）之后，通过断路器的故障监控（类似熔断保险丝）， **向调用方返回一个错误响应， 而不是长时间的等待**。这样就不会使得线程因调用故障服务被长时间占用不释放，**避免**了故障在分布式系统中的**蔓延**。
- 资源/依赖隔离(线程池隔离)：它会为**每一个依赖服务创建一个独立的线程池**，这样就算某个依赖服务出现延迟过高的情况，也只是对该依赖服务的调用产生影响， 而**不会拖慢其他的依赖服务**。

Hystrix提供几个熔断关键参数：`滑动窗口大小（20）、 熔断器开关间隔（5s）、错误率（50%）`

- 每当20个请求中，有50%失败时，熔断器就会打开，此时再调用此服务，将会**直接返回失败**，不再调远程服务。
- 直到5s钟之后，重新检测该触发条件，**判断是否把熔断器关闭，或者继续打开**。

Hystrix还有请求合并、请求缓存这样强大的功能，在此我就不具体说明了，有兴趣的同学可继续深入学习~

## 7.1Hystrix仪表盘

Hystrix仪表盘：它主要用来**实时监控Hystrix的各项指标信息**。通过Hystrix Dashboard反馈的实时信息，可以帮助我们快速发现系统中存在的问题，从而及时地采取应对措施。

启动时的页面：

![](https://note.youdao.com/yws/public/resource/ab24ea43c3a2d551e54ab38113b55ecb/xmlnote/4EF318D177BE4B5F9A65DB47E02E2F98/9729)

**监控单服务**的页面：

![](https://note.youdao.com/yws/public/resource/ab24ea43c3a2d551e54ab38113b55ecb/xmlnote/9A5E77EDBD3343E39422F3D85AEB4997/9736)

我们现在的服务是这样的：

![](https://note.youdao.com/yws/public/resource/ab24ea43c3a2d551e54ab38113b55ecb/xmlnote/5D707FBDAC3F4403B56FF3C912064A08/9740)

除了可以开启单个实例的监控页面之外，还有一个监控端点 `/turbine.stream`是对**集群**使用的。 从端点的命名中，可以引入Turbine, 通过它来**汇集监控信息**，并将聚合后的信息提供给 HystrixDashboard 来**集中展示和监控**。

![](https://note.youdao.com/yws/public/resource/ab24ea43c3a2d551e54ab38113b55ecb/xmlnote/D44CD991DE7C404AABF8BDCBC90FF1D7/9742)

# 八、引出Feign

# 九、引出Zuul

基于上面的学习，我们现在的架构很可能会设计成这样：

![](https://note.youdao.com/yws/public/resource/ab24ea43c3a2d551e54ab38113b55ecb/xmlnote/23C29DF5B1DD4D4DBF8D03953C36E1E2/9744)

这样的架构会有两个比较麻烦的问题：

1. **路由规则与服务实例的维护间题**：外层的负载均衡(nginx)需要**维护**所有的服务实例清单(图上的OpenService)
2. **签名校验、 登录校验冗余问题**：为了保证对外服务的安全性， 我们在服务端实现的微服务接口，往往都会有一定的**权限校验机制**，但我们的服务是独立的，我们**不得不在这些应用中都实现这样一套校验逻辑**，这就会造成校验逻辑的冗余。

还是画个图来理解一下吧：