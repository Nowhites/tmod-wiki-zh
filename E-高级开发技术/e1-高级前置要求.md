# 高级先决条件
基本上是所有之前的先决条件。首先请参阅初级和中级先决条件指南。

## 高级IDE功能
如果您还没有，现在是时候探索IDE的功能了。请参阅[为什么使用IDE](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE)指南，熟悉IDE可以做的提高模组制作效率的一切。特别值得注意的是，确保您充分利用智能感知，并且知道如何使用调试器。

## tModLoader源代码
您可能需要反编译tModLoader来理解正确的方法，或者弄清楚您正在做的事情的建议方法。任何尝试做高级指南的人可能都需要自己的反编译`tModLoader.dll`副本。

### 但是等等，我在Google上找到了代码！
不。那代码不好，不会起作用。它有数万个反编译错误。就自己反编译，很简单。继续阅读。

### 简单方法
更简单的方法是下载最近版本的[ILspy](https://github.com/icsharpcode/ILSpy/releases)（这些说明最后用ILSpy 9.0测试），向下滚动到"Assets"，根据需要展开，然后下载名称中包含"binaries"的zip文件以匹配您的CPU架构（很可能是x64），将其解压到一个文件夹，然后启动`ILSpy.exe`。（如果您在Linux或Mac上，您需要使用[AvaloniaILSpy](https://github.com/icsharpcode/AvaloniaILSpy/releases)而不是ILSpy。以下说明应该基本相同。）

然后，为了使适配更容易，转到`View`->`Options`->`Decompiler`选项卡，向下滚动到`Other`部分，启用`Always qualify member references`，然后按`OK`。这将使代码变为`base.width = 20;`而不是`width = 20;`，使调整代码更容易。

然后使用`File`->`Open`从[安装目录](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#install)打开`tModLoader.dll`。

此外，您还应该使用`File`->`Open`打开`FNA.dll`，位于`[安装目录]\Libraries\FNA\1.0.0\FNA.dll`。如果您愿意，可以拖动`.dll`文件而不是`File`->`Open`。对`ReLogic.dll`（`[安装目录]\Libraries\ReLogic\1.0.0\ReLogic.dll`）做同样的操作。

最后，确保在导航窗格中选择tModLoader，然后点击`File`->`Save...`并导航到一个方便的**空**文件夹，然后按`Save`（您会希望选择一个以后可以轻松找到的文件夹，我建议在[保存目录](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#saves)中创建一个文件夹：`C:\Documents\My Games\Terraria\tModLoader\DecompiledTModLoader`）。

![image](https://github.com/user-attachments/assets/d092ec75-c980-41b0-906d-29afdd461faf)

这将需要一分钟左右的时间，取决于您的计算机。一旦完成，您保存代码的文件夹现在有了一个`tModLoader.csproj`文件，如果您安装了Visual Studio（强烈推荐），打开这个文件应该会打开Visual Studio。如果您没有安装Visual Studio，您将不得不通过文件资源管理器单独导航文件。

如果您在任何文件中看到"Unknown result type (might be due to invalid IL or missing references)"，您很可能忘记了打开`FNA.dll`和`ReLogic.dll`。

简单方法有一些缺点。最大的缺点是您不能调试tModLoader本身。这是一个高级技术，但如果您以后想这样做，请回到这些说明并阅读下面的"困难方法"。另一个缺点是您可能会发现代码有反编译错误。使用最新版本的ILspy，这些非常有限且易于修复。

> ![](http://i.imgur.com/ZeXH2p5.png)

### 简单方法，适配指南额外步骤
如果您正在遵循[高级原版代码适配](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption)指南，您可能希望在导出代码之前执行一个额外的步骤。ILSpy的默认行为是省略限定成员引用。这意味着您在`Projectile.cs`中找到的代码，例如，会读作`if (type == 601)`而不是`if (this.type == 601)`。第二种方法大大简化了原版代码的适配，因为它允许使用简单的查找/替换操作将代码转换为适合粘贴到我们的`ModProjectile`类中的代码。没有这种方法，代码很难修复。要启用第二种方法，请转到`View`->`Options`->`Decompiler`并向下滚动到`Other`部分。找到`Always qualify member reference values`，确保它被选中，然后点击`OK`。从现在起，ILSpy将输出带有额外文本的代码，以大大简化适配。
![](https://i.imgur.com/8hByh7p.png)

### 困难方法
获取源代码的最佳方式是下载tModLoader仓库并按照[设置说明](https://github.com/tModLoader/tModLoader/wiki/tModLoader-guide-for-contributors#getting-the-tmodloader-code-for-the-first-time)操作。这将使您获得一个完全可用的解决方案，您可以在Visual Studio中打开。这种方法需要更多时间，通常不需要，除非您需要调试Terraria代码本身或直接为tModLoader做出贡献。