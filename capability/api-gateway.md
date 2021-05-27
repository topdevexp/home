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

* Netflix API 网关：Zuul
* 亚马逊 API Gateway
* Kong API Gateway