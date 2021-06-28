## 云原生架构

### 什么是云原生？

CNCF对云原生的定义：
>Cloud native technologies empower organizations to build and run scalable applications in modern, dynamic environments such as public, private, and hybrid clouds. Containers, service meshes, microservices, immutable infrastructure, and declarative APIs exemplify this approach.
> These techniques enable loosely coupled systems that are resilient, manageable, and observable. Combined with robust automation, they allow engineers to make high-impact changes frequently and predictably with minimal toil.
> The Cloud Native Computing Foundation seeks to drive adoption of this paradigm by fostering and sustaining an ecosystem of open source, vendor-neutral projects. We democratize state-of-the-art patterns to make these innovations accessible for everyone.

> 云原生技术有利于各组织在公有云、私有云和混合云等新型动态环境中，构建和运行可弹性扩展的应用。云原生的代表技术包括容器、服务网格、微服务、不可变基础设施和声明式 API。
> 这些技术能够构建容错性好、易于管理和便于观察的松耦合系统。结合可靠的自动化手段，云原生技术使工程师能够轻松地对系统作出频繁和可预测的重大变更。
> 云原生计算基金会（CNCF）致力于培育和维护一个厂商中立的开源生态系统，来推广云原生技术。我们通过将最前沿的模式民主化，让这些创新为大众所用。

**凡是能够提高云上资源利用率和应用交付效率的行为或方式都是云原生的。**

### [云原生12要素][云原生12要素]
* **基准代码 - 一份基准代码，多份部署** 
  
在类似SVN这样的集中式版本控制系统中，基准代码就是指控制系统中的这一份代码库；而在 Git 那样的分布式版本控制系统中，基准代码则是指最上游的那份代码库。
基准代码和应用之间总是保持一一对应的关系。尽管每个应用只对应一份基准代码，但可以同时存在多份部署。每份部署相当于运行了一个应用的实例。

* **依赖 - 显式声明依赖关系**

12-Factor规则下的应用程序不会隐式依赖系统级的类库。 它一定通过依赖清单，确切地声明所有依赖项。此外，在运行过程中通过依赖隔离工具来确保程序不会调用系统中存在但清单中未声明的依赖项。这一做法会统一应用到生产和开发环境。

* **配置 - 在环境中存储配置**

通常，应用的配置在不同部署(预发布、生产环境、开发环境等等)间会有很大差异。有些应用在代码中使用常量保存配置属于反模式。环境变量可以非常方便地在不同的部署间做修改，却不动一行代码；与配置文件不同，不小心把它们签入代码库的概率微乎其微；与一些传统的解决配置问题的机制（比如 Java 的属性配置文件）相比，环境变量与语言和系统无关。12-Factor 应用中，环境变量的粒度要足够小，且相对独立。它们永远也不会组合成一个所谓的“环境”，而是独立存在于每个部署之中。当应用程序不断扩展，需要更多种类的部署时，这种配置管理方式能够做到平滑过渡。

* **构建，发布，运行 - 严格分离构建和运行**

12-factor应用严格区分构建，发布，运行这三个步骤。 举例来说，直接修改处于运行状态的代码是非常不可取的做法，因为这些修改很难再同步回构建步骤。

* **进程 - 以一个或多个无状态进程运行应用**

12-Factor应用的进程必须无状态且无共享。任何需要持久化的数据都要存储在后端服务内，比如数据库。源文件打包工具（Jammit, django-compressor） 使用文件系统来缓存编译过的源文件。12-Factor应用更倾向于在构建步骤 做此动作——正如Rails资源管道 ，而不是在运行阶段。

* **端口绑定 - 通过端口绑定(Port binding)来提供服务**

12-Factor应用完全自我加载而不依赖于任何网络服务器就可以创建一个面向网络的服务。互联网应用通过端口绑定来提供服务，并监听发送至该端口的请求。

* **并发 - 通过进程模型进行扩展**

在12-factor应用中，进程是一等公民。12-Factor应用的进程主要借鉴于unix守护进程模型。开发人员可以运用这个模型去设计应用架构，将不同的工作分配给不同的进程类型。

* **易处理 - 快速启动和优雅终止可最大化健壮性**

12-Factor应用的进程是易处理（disposable）的，意思是说它们可以瞬间开启或停止。这有利于快速、弹性的伸缩应用，迅速部署变化的代码或配置，稳健的部署应用。

* **开发环境与线上环境等价 - 尽可能的保持开发，预发布，线上环境相同**


  |传统应用|	12-Factor| 应用|
  |---|---|---|
  |每次部署间隔|	数周|	几小时
  |开发人员 vs 运维人员|	不同的人|	相同的人
  |开发环境 vs 线上环境|	不同|	尽量接近

12-Factor应用的开发人员应该反对在不同环境间使用不同的后端服务，即使适配器已经可以几乎消除使用上的差异。这是因为，不同的后端服务意味着会突然出现的不兼容，从而导致测试、预发布都正常的代码在线上出现问题。这些错误会给持续部署带来阻力。从应用程序的生命周期来看，消除这种阻力需要花费很大的代价。

* **日志 - 把日志当作事件流**

12-factor应用本身从不考虑存储自己的输出流。不应该试图去写或者管理日志文件。相反，每一个运行的进程都会直接的标准输出（stdout）事件流。开发环境中，开发人员可以通过这些数据流，实时在终端看到应用的活动。

* **管理进程 - 后台管理任务当作一次性进程运行**

12-factor尤其青睐那些提供了REPL shell的语言，因为那会让运行一次性脚本变得简单。在本地部署中，开发人员直接在命令行使用shell命令调用一次性管理进程。在线上部署中，开发人员依旧可以使用ssh或是运行环境提供的其他机制来运行这样的进程。

[云原生12要素]: https://12factor.net/zh_cn/