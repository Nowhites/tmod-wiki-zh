# 简介
tModLoader将有用信息记录到日志文件中。用户可以使用日志来识别有问题的模组以便禁用。用户可以查看[用户常见问题中的阅读client.log部分](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-FAQ#reading-clientlog)来了解如何阅读自己的日志以识别有问题的模组。

tModLoader使用[log4net库](https://logging.apache.org/log4net/)来为模组制作者提供日志功能。模组制作者可以使用用户的日志来诊断和识别他们开发的模组中的bug和不兼容性问题。模组制作者可以阅读下文了解如何正确使用日志。

# 功能
## 详细日志
日志被分割到不同文件中，例如：`client.log`和`server.log`。如果多人游戏中出现问题，问题可能会被记录在服务器日志或客户端日志中，取决于代码运行在哪里。如果您看到`client2.log`或`server2.log`，那些是同时运行的客户端或服务器的第二个实例的日志。
每条日志输出至少提供通用日志信息：时间、日志级别和来源。
![image](https://user-images.githubusercontent.com/4522492/192859549-6cb6fce2-f0c2-4d9e-96f1-8540eea44483.png)    

除了常规日志外，还有一些特殊日志可用于诊断更独特的问题：
* `environment.log`是环境变量的日志
* `Launch.log`是游戏实际启动之前发生的事件的日志
* `Natives.log`是本机dll问题和消息的日志
* `terrariasteamclient.log`是处理Terraria游戏时间跟踪功能的进程的日志

## 归档
旧日志以压缩格式存档，以便日后可以重新查看。这些包含在`Old`子文件夹中。这些旧日志可以用于查阅之前游戏会话中发生的问题。    
![image](https://user-images.githubusercontent.com/4522492/192860671-fb01a278-2d68-43c0-afac-dcdb836001c6.png)

# 用法
您的`Mod`类中提供了您自己的`Logger`对象。您可以使用它来代表您的模组记录信息。在日志文件中，您的模组将被标记为该日志的来源。

日志有几个不同的级别：

**INFO**：用于通用信息消息 <br/>
**WARN**：用于警告，例如当某些事情出了问题但没有导致关键问题时 <br/>
**ERROR**：用于关键问题，例如模组损坏、可能崩溃或系统故障 <br/>
**FATAL**：用于由于关键问题导致应用程序无法继续做有用工作时 <br/>
**DEBUG**：用于额外的调试信息（详细） <br/>

知道错误和致命日志之间的区别可能很困难，但通常您可以遵循这个经验法则：如果导致错误的问题使应用程序根本无法继续，则错误是致命的

示例：
```cs
Mod.Logger.Info("这是一条信息日志"); // 日志级别INFO
Mod.Logger.InfoFormat("这是一条来自{0}的格式化信息日志", Mod.Name);

Mod.Logger.Warn("这是一条警告"); // 日志级别WARN
Mod.Logger.WarnFormat("这是来自{0}的警告", Mod.Name);

Mod.Logger.Error("发生了一个错误"); // 日志级别ERROR
Mod.Logger.ErrorFormat("发生错误：{0}", e.StackTrace);

Mod.Logger.Fatal("发生了致命问题"); // 日志级别FATAL
Mod.Logger.FatalFormat("发生致命问题：{0}", e.StackTrace);

Mod.Logger.Debug("一些调试信息"); // 日志级别DEBUG
Mod.Logger.DebugFormat("调试信息：({0}, {1})", x, y);
```

上述示例都假设日志记录发生在任何继承自`ModX`的类中，例如`ModItem`、`ModSystem`等。如果直接从`Mod`类记录日志，您可以只写`Logger.Info("示例文本")`。如果您从其他类记录日志，您可以使用`ModContent.GetInstance<ModNameHere>().Logger.Info("示例文本")`来访问您的`Mod`类的`Logger`。

# 我应该记录什么？
确定应该记录什么可能很困难。一般来说，您应该尝试避免用无用的内容使日志文件膨胀。问问自己：如果记录了这个，它有什么用途？您希望日志有用，所以它们需要一个_目的_。日志的目的通常是识别问题（或确保没有问题！）理想情况下，您只记录对实现此目的所需的内容。例如，如果模组中的某个系统成功完成了特定任务（例如设置），您应该推送一条信息日志。如果失败，则会推送警告或错误。这样，在检查日志时，如果此系统初始化出现问题，您可以识别出来。

## 可选日志记录
您可以使用`ModConfig`选项来允许用户决定是否应该记录某些内容。这对于会膨胀大多数用户日志文件的罕见情况很有用。可以在[BossChecklist](https://github.com/JavidPack/BossChecklist/search?q=ModCallLogVerbose)中看到这个方法的示例。在这个示例中，仅对模组制作者有用的日志记录被提供为一个可切换的选项，保持普通用户日志文件不膨胀。
