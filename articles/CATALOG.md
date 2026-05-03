# 知识点目录

## iOS基础

| 文章                                                                 | 描述                      |
| ------------------------------------------------------------------ | ----------------------- |
| [App启动流程](ios-basics/App启动流程.md)                                   | 详解iOS应用的启动过程            |
| [Objective-C中import详解](ios-basics/Objective-C中import详解.md)         | Objective-C中import的源码解析 |
| [Swift中import详解](ios-basics/Swift中import详解.md)                     | Swift中import的源码详解       |
| [iOS中的内存管理](ios-basics/iOS中的内存管理.md)                               | iOS内存管理机制详解             |
| [+load与+initialize的区别](ios-basics/load与initialize的区别.md)           | +load和+initialize方法对比   |
| [Mach-O的链接、装载与库](ios-basics/Mach-O的链接、装载与库.md)                     | Mach-O文件格式及相关概念         |
| [mmap详解](ios-basics/mmap详解.md)                                     | 内存映射机制详解                |
| [Objective-C与Swift区别](ios-basics/Objective-C与Swift区别.md)           | OC与Swift语言特性对比          |
| [值类型和引用类型的区别](ios-basics/值类型和引用类型的区别.md)                           | 值类型与引用类型对比分析            |
| [Objective-C底层原理-NSObject](ios-basics/Objective-C底层原理-NSObject.md) | NSObject底层实现原理          |
| [Swift底层原理-结构体、类和协议](ios-basics/Swift底层原理-结构体、类和协议.md)             | Swift结构体、类和协议的底层实现      |
| [runloop](ios-basics/runloop.md)                                   | RunLoop运行循环机制           |
| [runtime](ios-basics/runtime.md)                                   | Objective-C运行时机制        |
| [SIL](ios-basics/SIL.md)                                           | Swift中间语言详解             |
| [iOS编译原理](ios-basics/iOS编译原理.md)                                   | LLVM/Clang架构与OC/Swift编译流程详解 |
| [Swift二进制兼容性](ios-basics/Swift二进制兼容性.md)                           | Swift ABI稳定性详解          |
| [Timer的注意事项](ios-basics/Timer的注意事项.md)                             | Timer使用中的常见问题           |
| [响应者链](ios-basics/响应者链.md)                                         | iOS事件响应链机制              |
| [多线程](ios-basics/多线程.md)                                           | iOS多线程编程详解              |
| [布局方法详解](ios-basics/布局方法详解.md)                                     | iOS布局系统详解               |
| [生命周期](ios-basics/生命周期.md)                                         | App和ViewController生命周期  |
| [类型擦除](ios-basics/类型擦除.md)                                         | Swift类型擦除技术详解           |
| [weak详解](ios-basics/weak详解.md)                                     | weak引用的实现原理与使用          |
| [KVC底层原理](ios-basics/KVC底层原理.md) | KVC键值编码的底层实现原理 |
| [KVO底层原理](ios-basics/KVO底层原理.md) | KVO键值观察的底层实现原理 |
| [Block底层原理](ios-basics/Block底层原理.md) | Block的底层结构、三种类型、__block原理与循环引用 |
| [iOS反射](ios-basics/iOS反射.md) | OC Runtime反射与Swift Mirror内省机制 |
| [iOS中的数据库](ios-basics/iOS中的数据库.md) | iOS数据持久化与数据库方案全景解析 |
| [static关键字详解](ios-basics/static关键字详解.md) | OC/Swift中static的用法与底层原理 |
| [计算机网络](ios-basics/计算机网络.md) | TCP/IP、HTTP/HTTPS、DNS与iOS网络编程 |
| [Swift宏](ios-basics/Swift宏.md) | Swift 5.9+宏系统原理、实现、系统与社区实践 |
| [Codable底层原理](ios-basics/Codable底层原理.md) | 基于Swift源码剖析Codable协议、编译器合成与JSONEncoder实现 |

---

## iOS进阶

### 启动优化

| 文章 | 描述 |
|------|------|
| [启动优化](ios-advanced/启动优化/启动优化.md) | 启动优化综合指南 |
| [启动优化-观测](ios-advanced/启动优化/启动优化-观测.md) | 启动时间测量与监控 |
| [启动优化-二进制重排](ios-advanced/启动优化/启动优化-二进制重排.md) | 二进制重排优化方案 |
| [启动优化-减少动态库](ios-advanced/启动优化/启动优化-减少动态库.md) | 动态库优化策略 |
| [启动优化-Rebase与Bind](ios-advanced/启动优化/启动优化-Rebase与Bind.md) | Rebase与Bind优化 |
| [启动优化-Initializers](ios-advanced/启动优化/启动优化-Initializers.md) | 初始化器优化 |
| [启动优化-load方法](ios-advanced/启动优化/启动优化-load方法.md) | +load方法优化 |
| [启动优化-didFinishLaunching](ios-advanced/启动优化/启动优化-didFinishLaunching.md) | didFinishLaunching优化 |

### 卡顿优化

| 文章 | 描述 |
|------|------|
| [卡顿](ios-advanced/卡顿/卡顿.md) | 卡顿问题综合指南 |
| [卡顿-原理](ios-advanced/卡顿/卡顿-原理.md) | 卡顿产生的原理 |
| [卡顿-检测](ios-advanced/卡顿/卡顿-检测.md) | 卡顿检测方案 |
| [卡顿-主线程优化](ios-advanced/卡顿/卡顿-主线程优化.md) | 主线程优化策略 |
| [卡顿-离屏渲染](ios-advanced/卡顿/卡顿-离屏渲染.md) | 离屏渲染问题与优化 |
| [卡顿-图片优化](ios-advanced/卡顿/卡顿-图片优化.md) | 图片加载与渲染优化 |
| [卡顿-TableView优化](ios-advanced/卡顿/卡顿-TableView优化.md) | TableView性能优化 |

### 包瘦身

| 文章 | 描述 |
|------|------|
| [包瘦身](ios-advanced/包瘦身/包瘦身.md) | 包瘦身综合指南 |
| [包瘦身-分析工具](ios-advanced/包瘦身/包瘦身-分析工具.md) | 包体积分析工具使用 |
| [包瘦身-资源优化](ios-advanced/包瘦身/包瘦身-资源优化.md) | 资源文件优化策略 |
| [包瘦身-可执行文件优化](ios-advanced/包瘦身/包瘦身-可执行文件优化.md) | 可执行文件优化方案 |
| [包瘦身-AppThinning](ios-advanced/包瘦身/包瘦身-AppThinning.md) | App Thinning技术详解 |

### 崩溃治理

| 文章 | 描述 |
|------|------|
| [崩溃](ios-advanced/崩溃/崩溃.md) | 崩溃问题综合指南 |
| [崩溃-原理](ios-advanced/崩溃/崩溃-原理.md) | 崩溃产生的原理 |
| [崩溃-采集](ios-advanced/崩溃/崩溃-采集.md) | 崩溃信息采集方案 |
| [崩溃-Mach异常](ios-advanced/崩溃/崩溃-Mach异常.md) | Mach异常处理 |
| [崩溃-信号处理](ios-advanced/崩溃/崩溃-信号处理.md) | Unix信号处理机制 |
| [崩溃-治理](ios-advanced/崩溃/崩溃-治理.md) | 崩溃治理策略与实践 |
| [崩溃日志解读](ios-advanced/崩溃/崩溃日志解读.md) | `.ips` 双段 JSON、Exception Type 与 Termination Namespace、寄存器与符号化实战 |
| [JetsamEvent日志解读](ios-advanced/崩溃/JetsamEvent日志解读.md) | JetsamEvent `.ips` 字段、`reason` 类型、机型阈值矩阵与解析脚本 |
| [面试题](ios-advanced/崩溃/面试题.md) | 25道崩溃高频面试题，涵盖原理、符号化、采集、治理、OOM、日志解读六大方向 |

### 死锁治理

| 文章 | 描述 |
|------|------|
| [死锁](ios-advanced/死锁/死锁.md) | Coffman四条件、12类iOS常见死锁场景、TSan/MetricKit/Sentry/字节Heimdallr检测方案与治理实践 |

### 编译优化

| 文章 | 描述 |
|------|------|
| [编译优化](ios-advanced/编译优化/编译优化.md) | 编译优化综合指南，覆盖原理、流程与优化矩阵 |
| [编译优化-观测](ios-advanced/编译优化/编译优化-观测.md) | Build Timing Summary、-debug-time-compilation、XCLogParser 等观测方案 |
| [编译优化-Xcode构建系统](ios-advanced/编译优化/编译优化-Xcode构建系统.md) | llbuild、XCBBuildService、Swift Driver 架构与并行/增量原理 |
| [编译优化-Explicit Modules](ios-advanced/编译优化/编译优化-Explicit%20Modules.md) | Xcode 16/17 显式模块构建原理、Scan/Build/Compile 三阶段与变体治理 |
| [编译优化-头文件与HMap](ios-advanced/编译优化/编译优化-头文件与HMap.md) | Clang HMap 二进制格式、美团 cocoapods-hmap-prebuilt 原理 |
| [编译优化-Swift编译优化](ios-advanced/编译优化/编译优化-Swift编译优化.md) | 类型推导、WMO/CMO、访问控制、泛型特化与混编优化 |
| [编译优化-链接优化](ios-advanced/编译优化/编译优化-链接优化.md) | ld-prime/ThinLTO、Mergeable Libraries、filelist 与 Dead Strip |
| [编译优化-二进制化](ios-advanced/编译优化/编译优化-二进制化.md) | XCFramework、cocoapods-bin、Rugby、美团 zsource 源码回切 |
| [编译优化-二进制化实现原理](ios-advanced/编译优化/编译优化-二进制化实现原理.md) | cocoapods-bin 与 Medusa 源码拆解：Hook 集成、壳工程打包、DWARF 软链调试 |
| [编译优化-CocoaPods优化](ios-advanced/编译优化/编译优化-CocoaPods优化.md) | Pod Install 六阶段优化：抖音 seer-optimize 实践剖析 |
| [编译优化-编译缓存](ios-advanced/编译优化/编译优化-编译缓存.md) | Module Cache、ccache、sccache、XCRemoteCache、Bazel Remote Cache |
| [编译优化-Bazel方案](ios-advanced/编译优化/编译优化-Bazel方案.md) | rules_apple/rules_swift/rules_xcodeproj、头条与大厂 Monorepo 实践 |

### 耗电治理

| 文章 | 描述 |
|------|------|
| [耗电](ios-advanced/耗电/耗电.md) | 耗电问题综合指南 |
| [耗电-原理](ios-advanced/耗电/耗电-原理.md) | iOS功耗模型与能效调度原理 |
| [耗电-检测](ios-advanced/耗电/耗电-检测.md) | Energy Gauge、MetricKit与线上采集方案 |
| [耗电-CPU与后台优化](ios-advanced/耗电/耗电-CPU与后台优化.md) | CPU、Timer、QoS与BackgroundTasks优化 |
| [耗电-网络优化](ios-advanced/耗电/耗电-网络优化.md) | 无线电能效、请求合并与后台下载 |
| [耗电-定位与传感器优化](ios-advanced/耗电/耗电-定位与传感器优化.md) | 定位、传感器、屏幕与Low Power Mode适配 |
| [耗电-治理](ios-advanced/耗电/耗电-治理.md) | 研发规范、监控体系与排查手册 |

### APM 性能监控

| 文章 | 描述 |
|------|------|
| [APM](ios-advanced/APM/APM.md) | APM 体系总览：目标、架构、选型、演进路线 |
| [APM-指标体系](ios-advanced/APM/APM-指标体系.md) | 稳定性/流畅性/启动/资源/网络/业务/H5 指标族与目标值 |
| [APM-数据采集](ios-advanced/APM/APM-数据采集.md) | Hook/MetricKit/Signal/Backtrace 等采集技术实现 |
| [APM-数据上报](ios-advanced/APM/APM-数据上报.md) | 采样、聚合、批量、重试、合规与鲁棒性 |
| [APM-业界方案](ios-advanced/APM/APM-业界方案.md) | MetricKit/Sentry/Firebase/Bugly/Matrix/Slardar/Hertz/mPaaS 深度剖析 |
| [APM-可视化与告警](ios-advanced/APM/APM-可视化与告警.md) | 大盘、归因、告警、分发与防劣化闭环 |

### 架构设计

| 文章 | 描述 |
|------|------|
| [iOS架构概述](ios-advanced/architecture/iOS架构概述.md) | iOS常见架构模式概览 |
| [MVC](ios-advanced/architecture/MVC.md) | MVC架构详解 |
| [MVP](ios-advanced/architecture/MVP.md) | MVP架构详解 |
| [MVVM](ios-advanced/architecture/MVVM.md) | MVVM架构详解 |
| [MVI](ios-advanced/architecture/MVI.md) | MVI架构详解 |
| [VIPER](ios-advanced/architecture/VIPER.md) | VIPER架构详解 |
| [TCA](ios-advanced/architecture/TCA.md) | The Composable Architecture详解 |
| [组件化](ios-advanced/architecture/组件化.md) | iOS组件化架构实践 |
| [微服务化](ios-advanced/architecture/微服务化.md) | iOS微服务化架构探索 |
| [插件化](ios-advanced/architecture/插件化.md) | iOS插件化方案 |

### 设计模式

| 文章 | 描述 |
|------|------|
| [设计模式概述](ios-advanced/design-patterns/设计模式概述.md) | 设计模式基础介绍 |
| [单例模式](ios-advanced/design-patterns/单例模式.md) | 单例模式详解与iOS实现 |
| [工厂模式](ios-advanced/design-patterns/工厂模式.md) | 工厂模式详解与iOS实现 |
| [建造者模式](ios-advanced/design-patterns/建造者模式.md) | 建造者模式详解与iOS实现 |
| [代理模式](ios-advanced/design-patterns/代理模式.md) | 代理模式详解与iOS实现 |
| [观察者模式](ios-advanced/design-patterns/观察者模式.md) | 观察者模式详解与iOS实现 |
| [策略模式](ios-advanced/design-patterns/策略模式.md) | 策略模式详解与iOS实现 |
| [命令模式](ios-advanced/design-patterns/命令模式.md) | 命令模式详解与iOS实现 |
| [适配器模式](ios-advanced/design-patterns/适配器模式.md) | 适配器模式详解与iOS实现 |
| [装饰器模式](ios-advanced/design-patterns/装饰器模式.md) | 装饰器模式详解与iOS实现 |
| [外观模式](ios-advanced/design-patterns/外观模式.md) | 外观模式详解与iOS实现 |
| [责任链模式](ios-advanced/design-patterns/责任链模式.md) | 责任链模式详解与iOS实现 |
| [生产者消费者模式](ios-advanced/design-patterns/生产者消费者模式.md) | 生产者消费者模式详解与iOS实现 |

### 设计原则

| 文章 | 描述 |
|------|------|
| [SOLID原则](ios-advanced/design-principles/SOLID原则.md) | 面向对象设计五大原则 |
| [DRY原则](ios-advanced/design-principles/DRY原则.md) | Don't Repeat Yourself原则 |
| [KISS原则](ios-advanced/design-principles/KISS原则.md) | Keep It Simple, Stupid原则 |
| [迪米特法则](ios-advanced/design-principles/迪米特法则.md) | 最少知识原则详解 |

### 编程范式

| 文章 | 描述 |
|------|------|
| [OOP-POP-AOP](ios-advanced/OOP-POP-AOP.md) | 面向对象、面向协议、面向切面编程 |

### 跨平台

| 文章 | 描述 |
|------|------|
| [KMP（Kotlin Multiplatform）详解](ios-advanced/cross-platform/KMP.md) | KMP 架构、Kotlin/Native 编译流程、Android/iOS/鸿蒙/Web 四端互操作原理、内存模型与工程最佳实践 |
| [Flutter详解](ios-advanced/cross-platform/Flutter.md) | 三棵树架构、Dart AOT/JIT编译、Impeller渲染、iOS嵌入、AI时代优劣势分析 |
| [React Native详解](ios-advanced/cross-platform/ReactNative.md) | Bridge→New Architecture演化、JSI/Fabric/TurboModules原理、Hermes、Expo生态与AI时代决策矩阵 |
| [动态化DSL详解](ios-advanced/cross-platform/动态化DSL.md) | DSL分类、核心渲染管线、DinamicX/Tangram/LuaView/Lynx/Hummer/Recce剖析、AI Coding时代优劣势 |

### 三方库源码

| 文章 | 描述 |
|------|------|
| [SDWebImage源码导读](ios-advanced/三方库源码/SDWebImage源码导读.md) | SDWebImage 5.21源码分析：九阶段状态机、二级缓存、LIFO下载、渐进式解码、HDR支持 |
| [IGListKit源码导读](ios-advanced/三方库源码/IGListKit源码导读.md) | IGListKit源码分析 |
| [Texture源码导读](ios-advanced/三方库源码/Texture源码导读.md) | Texture(AsyncDisplayKit)源码分析 |
| [Kingfisher源码导读](ios-advanced/三方库源码/Kingfisher源码导读.md) | Kingfisher图片下载与缓存库源码分析 |
| [RxSwift源码导读](ios-advanced/三方库源码/RxSwift源码导读.md) | RxSwift响应式编程框架源码分析 |
| [Alamofire源码导读](ios-advanced/三方库源码/Alamofire源码导读.md) | 基于v5.11.2分析Session调度、Request状态机、拦截器与重试体系 |
| [AFNetworking源码导读](ios-advanced/三方库源码/AFNetworking源码导读.md) | AFNetworking网络库源码分析：NSURLSession封装、SSL Pinning、流式Multipart、Class Cluster Swizzling |
| [fishhook源码导读](ios-advanced/三方库源码/fishhook源码导读.md) | 基于master最新源码分析fishhook：Mach-O间接符号绑定、`__la_symbol_ptr`重写、`__DATA_CONST`与VM_PROT_COPY适配 |
| [SnapKit源码导读](ios-advanced/三方库源码/SnapKit源码导读.md) | 基于v5.7.1分析阶段化Builder、Target协议族、OptionSet属性位图与Constraint激活流程 |

### CocoaPods源码导读

| 文章 | 描述 |
|------|------|
| [CocoaPods源码导读-架构总览](ios-advanced/CocoaPods/CocoaPods源码导读-架构总览.md) | 15个Gem组成的生态、`Installer#install!`九阶段、`--repo-update`全景图 |
| [CocoaPods源码导读-从命令到依赖求解](ios-advanced/CocoaPods/CocoaPods源码导读-从命令到依赖求解.md) | CLAide命令分发、Podfile DSL、Analyzer七步走、Molinillo回溯求解 |
| [CocoaPods源码导读-从下载到工程集成](ios-advanced/CocoaPods/CocoaPods源码导读-从下载到工程集成.md) | Downloader缓存、Pods.xcodeproj生成、UserProjectIntegrator、Lockfile写入 |

### 音视频

| 文章 | 描述 |
|------|------|
| [音视频原理](ios-advanced/音视频原理.md) | iOS音视频采集、编解码、播放与渲染全链路原理 |

### 其他

| 文章 | 描述 |
|------|------|
| [线上故障预防](ios-advanced/线上故障预防.md) | 线上故障预防策略 |
| [WebView离线包](ios-advanced/WebView离线包.md) | WebView离线包方案、原理与避坑指南 |
| [WebView底层原理](ios-advanced/WebView底层原理.md) | 基于WebKit源码剖析WKWebView多进程架构与渲染原理 |
| [iOS国际化](ios-advanced/iOS国际化.md) | RTL、多时区、多币种、复数规则与工程化实践 |
| [Instruments详解](ios-advanced/Instruments详解.md) | 基于Xcode 26讲解Instruments各工具的使用场景与实践 |
| [iOS无障碍树](ios-advanced/iOS无障碍树.md) | AX Tree架构、UIAccessibility协议、VoiceOver原理与SwiftUI无障碍适配 |

---

## AI系列

| 文章 | 描述 |
|------|------|
| [神经网络原理](ai/神经网络原理.md) | 神经网络的结构、前向传播与学习原理 |
| [反向传播算法](ai/反向传播算法.md) | 链式法则、梯度下降与反向传播推导 |
| [Transformer架构原理](ai/Transformer架构原理.md) | 注意力机制、多头注意力与GPT原理 |
| [RAG](ai/RAG.md) | 检索增强生成的原理与工程实践 |
| [Prompt Engineering](ai/Prompt%20Engineering.md) | 提示词工程的核心技术与实践 |
| [AI Agent](ai/AI%20Agent.md) | 智能体的架构、推理模式与应用 |
| [Function Calling与MCP](ai/Function%20Calling与MCP.md) | LLM工具调用与MCP协议对比 |
| [AI编程工具](ai/AI编程工具.md) | AI编程工具生态、Rule/Skill/MCP概念解析 |
| [iOS中AI Chat实战](ai/iOS%E4%B8%ADAI%20Chat%E5%AE%9E%E6%88%98.md) | iOS实现AI聊天：流式输出、A2UI协议、Function Calling等高级能力 |
| [FoundationModels](ai/FoundationModels.md) | Apple端侧LLM框架：Guided Generation、Tool Calling、Streaming与Adapter详解 |
| [Harness Engineering](ai/Harness%20Engineering.md) | Anthropic/OpenAI/LangChain/DeepMind九篇文章精读：Agent=Model+Harness的工程化实践 |
| [LLM Wiki](ai/LLM%20Wiki.md) | Karpathy提出的"后RAG时代"个人知识库范式：Raw/Wiki/Schema三层架构与Ingest/Query/Lint三操作 |

### OpenClaw 源码导读

| 文章 | 描述 |
|------|------|
| [OpenClaw源码导读-架构总览](ai/openclaw/OpenClaw源码导读-架构总览.md) | 项目定位、Gateway隐喻、100+子目录分类、模块全景与CLI启动流程 |
| [OpenClaw源码导读-Gateway控制平面](ai/openclaw/OpenClaw源码导读-Gateway控制平面.md) | CLI→Gateway六步启动、WebSocket协议帧、六种Auth、Pairing、Session模型 |
| [OpenClaw源码导读-Agent Harness](ai/openclaw/OpenClaw源码导读-Agent%20Harness.md) | AgentHarness抽象、Harness注册表与选择策略、2160行主循环、40+状态位的失败分类与Compaction |
| [OpenClaw源码导读-Channels与扩展生态](ai/openclaw/OpenClaw源码导读-Channels与扩展生态.md) | 118个Channel Plugin、Node RPC、A2UI Canvas协议、53个Skill、Docker Sandbox隔离 |

---

## LeetCode算法

| 文章 | 描述 |
|------|------|
| [经典算法](leetcode/经典算法.md) | iOS面试常见算法题 |

