# 简介

本指南介绍了在Visual Studio中调试mod，以确定为什么mod在mod重新加载期间没有完全卸载。正确的卸载代码确保mods正确重置其值并且不会使用过多的RAM。本指南假设您知道如何在Visual Studio中调试mod，这是必要的。

![](https://i.imgur.com/EmXS4eb.png)

# 卸载基础

如您所知，C#使用托管内存，这意味着我们通常可以忽略内存问题，因为我们可以相信垃圾收集器将清理不再被引用的对象。对于Mods，有许多常见的陷阱会导致对象在tModLoader尝试卸载Mod后仍被引用，导致mod在禁用后继续消耗RAM/memory。为了解决这些问题，从tModLoader v0.11开始，Mods菜单将通知用户mods未能正确卸载。

[ExampleMod.Unload](https://github.com/tModLoader/tModLoader/blob/master/ExampleMod/ExampleMod.cs#L143)显示了许多modder需要记住的"卸载"（设置为null）的事情。主要罪魁祸首通常是对任何tModLoader类的静态引用。确保将那些设置为null。对`Texture2D`等XNA类的静态引用也会导致问题。

# 调试

在您确保将所有能想到的引用都设置为null后，您可能会发现mod仍然无法卸载。要解决这个问题，我们将使用Visual Studio。但是，首先确保禁用所有其他mods，因为某些mods保留对其他mods的引用。如果禁用所有其他mods解决了问题，则问题在于阻止您的mod卸载的其他mod。告诉他们修复他们的mod。

## 启动调试器

像平常一样通过Visual Studio启动您的mod。如果您忘记了，请参阅[使用Visual Studio开发](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio#debugging)，但本指南假设您知道如何在Visual Studio中调试mod。

## 编辑并继续以及内存快照

成功启动后，禁用mod，然后重新加载，并记下通知mod未正确卸载的图标。现在，在Visual Studio中，点击"诊断工具"窗口中的"拍摄快照"。这将使VS记住内存中所有当前活动的对象。点击对象列中的数字，会弹出一个窗口来搜索快照。

![](https://i.imgur.com/vguLKEZ.png)
![](https://i.imgur.com/IKk1Hba.png)

在右上角的搜索栏中，键入您mod的命名空间。这会将结果过滤到仅您mod中添加的类对象引用。（还要确保选择"Paths to Root"）您应该会看到1个或更多结果：

![](https://i.imgur.com/eyD7yoM.png)

这里我们从列表中选择了`PresentOpener.PresentOpener`（这个例子恰好使用的`Mod`类的名称），用结果填充下面的视图，显示对该类型的1个引用。在树视图下方，我们看到在PresentOpener本身的一个名为`Instance`的静态变量中对该类型的1个引用。看着我们的`Mod.Unload`代码，我们看到它实际上是空的！哎呀！

![](https://i.imgur.com/dW14RXy.png)

让我们在那里设置一个断点，启用mod，重新加载，禁用mod，然后再次重新加载。这将命中断点，允许我们修复第一个问题。

![](https://i.imgur.com/GOv1vSp.png)

现在，我们重复这个过程。这次希望我们刚刚修复的项目不再显示。确保在禁用以前加载的mod后拍摄快照。让我们修复另一个问题：

![](https://i.imgur.com/2oQDRz6.png)

这里我们看到另一个静态引用，这次是对tModLoader API类（ModConfig）的引用。记住对这些类的静态引用将阻止数据被卸载。让我们再次在`Mod.Unload`开头设置一个断点并修复这个问题。然后，一旦我们完成并再次加载和卸载mod，我们可以再拍一张快照。

![](https://i.imgur.com/v9XVb7T.png)

越来越近了，只有几个了。看着最后2个，我注意到PresentProcessUI持有对我们VanillaItemSlotWrapper类的引用。修复未卸载的对PresentProcessUI的引用自动使对VanillaItemSlotWrapper类的引用成为孤儿，使这最后一个修复成为2合1。

![](https://i.imgur.com/F71FFKR.png)

在`Unload`中将`Mod`类的3个静态字段设置为null后，我们的mod终于正确卸载了。并非所有问题都能如此轻松地解决，但我希望本指南能教您足够好地使用Visual Studio诊断工具来调试这些内存问题，这样您就可以自己解决您的问题。

![](https://i.imgur.com/sSDtR8H.png)

## 提示

### ILoadable

实现ILoadable接口可能是明智的：

```cs
internal interface ILoadable {
    void Load();
    void Unload();
}
```

您可以在任何希望遵循加载/卸载行为的类中实现此接口，然后从Mod类中的主要Load()和Unload()例程中，您只需分别对实现ILoadable的任何内容调用Load()和Unload()：

```cs
ILoadable loadable = new MyLoadableImpl();

public void Load() {
    // 假设您在某个地方有一个可加载组件
    loadable.Load();
}

public void Unload() {
    // 假设您在某个地方有一个可加载组件
    loadable.Unload();
}
```

### 什么是`+<>c`

这些可以忽略，一旦您修复了其他问题，它们就会消失。

### 比较快照混淆

如果引用结果似乎没有变小，请确保您不是在比较以前的快照：

![](https://i.imgur.com/NIe3DjD.png)
