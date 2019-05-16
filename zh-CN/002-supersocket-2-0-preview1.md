# SuperSocket 2.0 Preview1 发布

今天，SuperSocket的作者发布了2.0版本的第一个预览版。SuperSocket 2.0 是一个经过全新设计的，第一个完全基于.NET Core的版本。作者正在积极尝试提供更简单易用的API的同时，尽量保证与老版本相似的原汁原味的开发体验。新的版本中亦删除了一些不太重要并且已有更好的替代实现的功能，例如服务器宿主。

时隔三年之后再次发布新的版本，意义重大。首先让我们来回顾一下SuperSocket的发展历程：

* 2008-2009: 起源于对公司Email服务器和FTP服务器开发的现状不满。低质量，冗余的代码和不统一的开发方式让作者产生了自己写一套Socket服务器框架的想法；后又开始尝试写了一些代码并形成了SuperSocket的雏形；
* 2010: SuperSocket正式开源并发布于codeplex.com; https://supersocket.codeplex.com
* 2010-10: SuperSocket 1.0 发布，仅支持命令行协议（Telnet）；https://www.cnblogs.com/jzywh/archive/2010/10/19/SuperSocket1stable.html
* 2011-01: SuperSocket 1.3 发布，首个支持自定义协议的版本；https://www.cnblogs.com/jzywh/archive/2011/01/17/supersocket13stable.html
* 2011-07: SuperSocket 1.4 发布，新增命令过滤器和连接过滤器, 并通过Mono跨平台；http://www.cnblogs.com/jzywh/archive/2011/07/06/2099097.html
* 2013-01: SuperSocket 1.5 发布，新增动态语言的支持和多服务器实例的隔离；http://www.cnblogs.com/jzywh/archive/2013/01/07/supersocket150.html
* 2013-10: SuperSocket 1.6 发布，新增进程级别隔离，服务器主动连接和客户端证书验证；https://www.oschina.net/news/45454/supersocket-1-6-stable
* 2014-2016: 发布SuperSocket 1.6.1 - 1.6.6 以修复一些缺陷并提高稳定性；
* 2016-4: 在Nuget上发布SuperSocket 1.6.6.1，该版本暂时为为SuperSocket公开发布的最新的稳定版；https://www.nuget.org/packages/SuperSocket/1.6.6.1

SuperSocket 2.0 基于 .NET Core (3.0) 重新设计，充分利用System.IO.Pipelines带来的高效的流式数据处理能力，将会给大家带来更好的开发运行体验：
* 更高效（zero copy），更好用的协议解析API；
* 云原生的支持（Cloud Native），轻松运行于Docker和Kubernetes；
* Middleware的设计简化核心代码的同时支持更好的扩展能力；
* 其它由.NET Core带来的优点，如更灵活的日志抽象和更多样化的配置支持等等；

SuperSocket 2.0 还在积极开发中，最终版本预计在下半年紧随着.NET Core 3.0正式版之后发布。
该项目作者鼓励用户多多反馈意见。2.0版本最终会包含哪些功能，很大部分可能会取决于用户的反馈。

SuperSocket 2.0 Preview1 已可在NuGet上获取：
https://www.nuget.org/packages/SuperSocket/2.0.0-preview1

同时SuperSocket 2.0文档也在同步准备之中：
http://docs.supersocket.net/v2-0/