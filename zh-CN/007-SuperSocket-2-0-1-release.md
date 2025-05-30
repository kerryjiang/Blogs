# SuperSocket 2.0.1 发布：为 .NET 套接字应用程序提供增强的稳定性和性能

**开源 .NET 套接字服务器框架提供更高的可靠性和开发者体验**

SuperSocket 团队今天宣布发布 [SuperSocket 2.0.1](https://github.com/kerryjiang/SuperSocket/releases/tag/v2.0.1)，这是对流行的高性能、可扩展 .NET 套接字服务器应用程序框架的维护更新。此版本专注于稳定性改进、性能优化和增强的开发者体验。

### 主要亮点

**增强的稳定性和可靠性**

[SuperSocket 2.0.1](https://github.com/kerryjiang/SuperSocket/releases/tag/v2.0.1) 解决了几个关键的稳定性问题，提高了基于套接字应用程序的整体可靠性。该版本包括中间件会话处理的修复、SocketSender 异常的解决，以及配置方法的顺序无关性改进。

**性能改进**

新版本引入了性能优化，包括通过 ConfigureAwait 为 SendAsync 操作提供更好的异步操作处理，以及各种减少上下文切换开销的套接字处理优化。

**改进的开发者体验**

开发者将受益于整个代码库中添加的全面 XML 文档、通过 .editorconfig 支持的一致编码标准，以及通过暴露受保护方法提供的更好可扩展性。该版本还包括对管道过滤器的增强依赖注入支持。

**更好的项目结构**

框架的架构已经得到优化，实现了更清晰的关注点分离，将 ProtoBuf 等专门组件移至单独的存储库，并改善了整体可维护性。

### 兼容性和升级路径

SuperSocket 2.0.1 与 2.0.0 版本保持完全兼容，使其成为现有应用程序的直接替换。鼓励所有用户升级以从稳定性改进和错误修复中受益。

### 关于 SuperSocket

[SuperSocket](https://github.com/kerryjiang/SuperSocket/) 是一个高性能、可扩展的 .NET 套接字服务器应用程序框架，支持包括 TCP、UDP 和 WebSocket 在内的多种协议。它为构建自定义网络通信应用程序提供了强大的架构，具有灵活的管道架构、协议抽象、中间件支持和全面的会话管理等功能。

该框架广泛用于实时通信系统、物联网设备连接、游戏服务器、聊天应用程序和自定义网络协议。

### 可用性

SuperSocket 2.0.1 现已通过 NuGet 包提供。有关更多信息、文档和下载，请访问：

- **NuGet 包**: [https://www.nuget.org/packages?q=SuperSocket](https://www.nuget.org/packages?q=SuperSocket)
- **文档**: [https://docs.supersocket.net/](https://docs.supersocket.net/)
- **GitHub 仓库**: [https://github.com/kerryjiang/SuperSocket](https://github.com/kerryjiang/SuperSocket)
