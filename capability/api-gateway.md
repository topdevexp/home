### API和服务设计

#### 微服务与API服务

传统的软件架构多为单体结构，在一个单体服务中，包含所有的业务逻辑。
随着计算机的发展，用户对新需求实现的速度要求越来越高。
然而单体架构随着项目的迭代，变得逐渐臃肿，传统的单体架构已经很难快速对用户的需求做出响应。
用户期待迭代速度的提高和单体架构对迭代速度的降低之间的矛盾日益加深。
这个时候，聪明的软件开发者们，决定将服务进行横向拆分，将一个单体服务分为N个微服务。
将模块高度耦合的单体服务拆分为松散耦合的许多微服务或者模块，这样做对下面几点有很大帮助：
* 每个微服务都可以独立于应用程序中的同级服务进行部署、升级、扩展、维护和重新启动。
* 通过自治的跨职能团队进行敏捷开发和敏捷部署。
* 运用技术时具备灵活性和可扩展性。

在微服务架构中，我们根据各自的特定需求部署不同的松耦合服务，其中每个服务都有其更细粒度的API模型，用以服务于不同的客户端（Web，移动和第三方 API）。
然而在实际通信中，还是存在着诸多挑战：
* 微服务公开的细粒度API多为基于Resource的REST API。在典型的应用中，可能要进行多次API调用才能满足要求。
  网络成本开销比较大，一旦网络波动，用户的体验并不好。
* 微服务中存在多种通信协议，比如REST API，GRPC，AMQP等。客户端开发成本比较大。
* 必须在各个微服务中提供基础的通用网关功能，比如身份验证，授权。
* 很难对线上服务进行升级或者修改，可能客户端正在请求相关资源。


#### API网关

为了解决上述的问题，软件开发者们在用户和服务之间加入了一层服务，充当从客户端到服务器的反向代理路由请求。
与面向对象设计的模式相似，它为封装底层系统架构的API提供了一个单一的入口，称为API网关。

API网关可以包含以下功能：
* 认证和授权
* 服务发现集成
* 缓存响应结果
* 重试策略、熔断器、QoS
* 限速和节流
* 负载均衡
* log 日志、链路追踪、关联
* Header、query 字符串 以及 claims 转义
* IP 白名单
* IAM
* 集中式日志管理（服务之间的 transaction ID、错误日志等）
* 身份的提供方，验证与授权


#### 著名的API网关

* **Netflix API 网关：Zuul**

Netflix流媒体应用程序是由多个相互交织的系统组成的复杂阵列，这些系统协同工作以无缝地为客户提供出色的体验。
2013年Netflix推出Zuul，将API Gateway作为Netflix后台服务的门户，可支持1,000多种不同的设备类型，并在高峰时段每秒处理50,000个请求。

下面是Netflix的云架构：

![Zuul in Netflix’s Cloud Architecture](https://miro.medium.com/max/2348/1*Cv4CCYNTlGnIkQ4VkP4pHg.png)
我们可以看到在WebService，API Service和Streaming Service上层，是由Zuul进行管理的。

##### **Zuul如何工作**

Zuul的中心是一系列过滤器，它们能够在路由HTTP请求和响应期间执行一系列操作。以下是Zuul过滤器的主要特征：
* Type：通常定义路由流程中应用过滤器的阶段（尽管它可以是任何自定义字符串）
* Execution Order：在类型中应用，定义跨多个过滤器的执行顺序
* Criteria：执行过滤器所需的条件
* Action：如果符合条件，则要执行的动作

下面是一个例子来演示如何延迟来自故障系统的请求：
```
  class DeviceDelayFilter extends ZuulFilter {
  
      def static Random rand = new Random()
      
      @Override
      String filterType() {
         return 'pre'
      }
      
      @Override
      int filterOrder() {
         return 5
      }

      @Override
      boolean shouldFilter() {
         return  RequestContext.getRequest().
         getParameter("deviceType")?equals("BrokenDevice"):false
      }
  
      @Override
      Object run() {
         sleep(rand.nextInt(20000)) // Sleep for a random number of
                                    // seconds between [0-20]
      }
  }
```
Zuul提供了一个可以动态加载，编译并且运行拦截器的框架。
各个Filter之间是不会互相通信的，但是它们通过唯一的Request Context来共享状态。

目前Filter是由Groovy语言编写的，但是它可以支持任意的JVM语言。
Filter的源码会存放在Zuul服务器的某个特定目录中，会被定期轮询查看是否更改。
更改过后的Filter会动态加载并编译，由Zuul为每个后续请求调用。

下面是Zuul的核心架构：

![Zuul Core Architecture](https://miro.medium.com/max/2026/1*j9iGkeQ7bPK2nC1a7BgFOw.png)

有几种标准Filter类型对应于请求的生命周期：
* **PRE filter** 会在路由到Origin之前执行。比如请求的验证，选取源服务器，或者打印日志信息
* **ROUTING filter** 将请求路由到源服务器。
* **POST filter** 会在路由之后执行，比如加一些标准的header信息，采集一些元数据，或者将数据流式传输。
* **ERROR filter**在其他阶段发生错误时执行。

![Request Lifecycle](https://miro.medium.com/max/1920/1*9IeEGHSRMGfAnhqM49TLpQ.png)

除了默认的Filter流程，Zuul还允许我们创建自定义Filter类型并显式执行它们。
例如，Zuul有一个STATIC类型，它在Zuul内生成响应，而不是将请求转发到源。


##### **Zuul 2**

随着NIO的提出和Netty的流行，2018年Netflix推出了Zuul的第二个版本：Zuul 2。
Zuul 2显着改进了架构和功能，使Netflix的网关能够处理、路由和保护Netflix的云系统，并帮助1.25亿会员提供尽可能最佳的体验。

下面是Zuul 2的核心架构：

![Zuul 2 Core Architecture](https://miro.medium.com/max/4800/0*ycjEWsSKCaPemEg3.)

我们可以看到，相比于老版本的架构，Zuul 2主要采用了Netty来处理网络数据之间的传输。
Filter前后的Netty handlers主要负责处理网络协议、Web 服务器、连接管理和代理工作。
Inbound Filters对应着老版本的Pre Filters，Endpoint Filter对应Routing Filter，Outbound Filters对应Post Filters。
更加表意，随着这些内部工作的抽象化，Filter完成了所有繁重的工作。
而且Netty是基于NIO思想编写的架构，响应更加迅速。
Netflix的Cloud Gateway团队运行并运营着80多个Zuul 2集群，将流量发送到大约100个（并且还在不断增长）后端服务集群，每秒请求数超过100万。



* 亚马逊 API Gateway
* Kong API Gateway