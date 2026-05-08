# 什么是调试？
调试是模组制作者调查其代码中问题以尝试修复它们的方式。模组构建良好，但模组的行为不符合预期。不可避免地，您的模组中会有一些不符合预期的事情。当这种情况发生时，您需要"调试"模组以确定问题发生在哪里。

调试可以有多种形式，本指南将重点介绍3种方法：[隔离调试](#隔离调试)、[文本调试](#文本调试)和[调试器调试](#调试器调试)。

本指南专注于tModLoader模组制作。官方的[面向绝对初学者的调试指南](https://learn.microsoft.com/en-us/visualstudio/debugger/debugging-absolute-beginners?view=vs-2022&tabs=csharp)是一个很好的通用C#调试资源，也涵盖了更多概念主题以及通用C#调试。强烈建议模组制作者也阅读该指南。

# 隔离调试
遇到bug时的第一步是尽可能隔离它。使用新世界和新玩家进行测试可以排除许多问题来源。此外，其他模组是不当行为的大来源，您看到的bug可能是其中一个模组的功能或冲突。

禁用所有其他模组并确保bug仍然存在，如果是，这意味着问题在您的代码中，这意味着您可以修复它。如果bug不存在，您需要通过单独加载各个模组与您的模组一起来找出哪个模组导致了问题。如果bug仅在另一个模组存在时发生，那可能是该模组的bug。可能需要与该模组制作者合作来修复问题。

一旦您知道bug是由您的模组引起的，您将需要确定问题来自哪里。以下部分将帮助您发现问题。

# 文本调试
如果您不熟悉使用Visual Studio等调试器，或者只是需要快速可视化某些数据，那么通过文本进行调试可能很有用。请注意，最终您会想要学习[调试器调试](#调试器调试)，因为它更快、更强大。

文本调试，简单来说，就是打印您可以阅读的文本以尝试识别问题。例如，如果某些数学运算没有给您预期的结果，打印这些值可以帮助识别问题。首先，让我们探索可用的文本调试方法：

## 文本调试方法
以下是tModLoader中文本调试的典型方法。对于所有这些方法，从您想要输出的数据创建一个字符串。例如，如果您有一个名为`total`的变量，您可能想要创建一个字符串`$"总数是：{total}."`，然后将其传递到这些方法中的任何一个。要了解如何从文本和数据创建字符串，请参阅此[C#字符串插值](https://www.w3schools.com/cs/cs_strings_interpol.php)教程。

### Main.NewText
此方法将文本输出到游戏内聊天窗口。这简单明了。文本将很容易看到，一段时间后会消失。

`Main.NewText($"使用Main.NewText：当前生命值：{player.statLife}.");`

![image](https://github.com/tModLoader/tModLoader/assets/4522492/6eacdbe3-9b39-4e0e-8c54-1fc7b0aefdc8)

### Mod.Logger.Info
此方法将文本输出到[日志文件](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#logs)。客户端输出到`client.log`，服务器进程输出到`server.log`。这种方法将持久化数据，因此如果您需要随时间跟踪某些内容，它可能更有用。

`Mod.Logger.Info($"使用Mod.Logger.Info：当前生命值：{player.statLife}.");`：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/6e2daa44-0442-4500-9dcf-7814b73c1cb9)

### Console.WriteLine
如果您熟悉编程，使用`Console.WriteLine`进行文本调试您应该很熟悉。但是，这种方法对调试模组不太有用。控制台是隐藏的，所以没有办法看到文本。使用其他方法之一。

### CombatText.NewText
这种方法产生浮动文本。文本很快消失，但因为文本在指定位置产生，所以在这种情况下很有用。该方法需要传入一个Rectangle作为位置和一个Color：

`CombatText.NewText(player.getRect(), Color.Purple, $"使用CombatText.NewText：当前生命值：{player.statLife}.");`：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/a725aab3-7450-4343-9025-2058088fdd39)

### Dust.QuickDust、Dust.QuickBox、Dust.QuickDustLine
不是文本，但在特定世界坐标处产生灰尘可能对处理世界位置的代码有用。使用此方法来验证Projectile和NPC AI代码中的[几何计算](https://github.com/tModLoader/tModLoader/wiki/Geometry)。

```cs
Dust.QuickDust(player.TopLeft, Color.Red);
Dust.QuickDust(player.BottomRight, Color.Blue);
```

![image](https://github.com/tModLoader/tModLoader/assets/4522492/97ef7aa3-7d1b-4d71-a481-9afde3de11c6)

## 如何使用文本调试
现在我们知道如何输出文本，让我们通过一个例子来走一遍。这个练习将帮助学习如何使用文本调试。考虑以下治疗药水物品中的代码，类似于[ExampleHealingPotion](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Consumables/ExampleHealingPotion.cs)：
```cs
public override void GetHealLife(Player player, bool quickHeal, ref int healValue) {
	int numberOfDiamonds = player.CountItem(ItemID.DiamondRing);
	float healMultiplier = numberOfDiamonds / 10;
	healMultiplier = Utils.Clamp(healMultiplier, 0f, 1f);
	healValue = 100 + (int)(10 * healMultiplier);
}
```
这段代码的目的是治疗玩家100点生命值，加上玩家物品栏中每颗钻石戒指额外10点生命值，最高200点生命值。乍一看，这段代码看起来正确，但实际上因为bug总是治疗100点生命值。让我们添加一些文本调试来识别问题：
```cs
public override void GetHealLife(Player player, bool quickHeal, ref int healValue) {
	int numberOfDiamonds = player.CountItem(ItemID.DiamondRing);
	Main.NewText($"numberOfDiamonds: {numberOfDiamonds}.");
	float healMultiplier = numberOfDiamonds / 10;
	Main.NewText($"healMultiplier初始值：{healMultiplier}.");
	healMultiplier = Utils.Clamp(healMultiplier, 0f, 1f);
	Main.NewText($"healMultiplier Clamp后：{healMultiplier}.");
	healValue = 100 + (int)(100 * healMultiplier);
	Main.NewText($"healValue: {healValue}.");
}
```
当玩家持有5颗钻石戒指时，这段代码产生以下输出：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/9c403f63-7b85-43f5-afd9-aeb8cebc4094)

出于某种原因，`CountItem`方法只计数到1。让我们查看该方法的文档：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/4b2dcb64-1308-43ac-b97f-5e08df04f405)

看起来有一个名为`stopCountingAt`的第2个参数。我们想要计数到10，所以让我们通过将该行更改为`int numberOfDiamonds = player.CountItem(ItemID.DiamondRing, 10);`来修复这个问题，然后重试：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/48eb0b0b-f6e6-492e-af6a-ba3e6236c7cd)

`numberOfDiamonds`的值现在正确了，但`healMultiplier`仍然不正确。看起来`float healMultiplier = numberOfDiamonds / 10;`在`numberOfDiamonds`为`1`时产生值`0`！这不是我们想要的。这是[整数除法](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#integer-division)导致的，我们可以通过将`numberOfDiamonds / 10;`更改为`numberOfDiamonds / 10f;`来修复这个问题。让我们进行修复并再次尝试：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/0ec58e15-7cdc-43bb-9788-29b71281935f)

现在一切正常了。

在这个例子中，我们使用文本调试来确定bug发生在哪里。使用类似的方法，您应该能够定位模组代码中的逻辑错误。文本调试是一个有用的工具，但可能相当乏味。输入代码将文本输出到聊天不是很高效。一旦您熟悉了调试的基本概念，您应该尽快进入[调试器调试](#调试器调试)。

## 清理
一旦文本调试达到了它的目的，请记住从模组中删除文本调试代码。没有什么比用户在看似随机的环境中看到聊天中出现奇怪文本更烦人的了。

有些情况下文本调试代码以后可能有用。一种方法是使用[预处理器指令](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/preprocessor-directives)在模组的发布版本中隐藏代码。使用这种方法，代码将在从Visual Studio调试时存在，但在游戏中构建的模组中将不存在：

```cs
#if DEBUG
	Main.NewText("此文本仅对调试构建显示");
#endif
```
有时调试文本在远程帮助模组用户诊断问题时很有用。向他们发送带有调试文本的模组特殊构建可能有用，但另一种方法是提供一个配置来切换额外的调试日志。例如，`BossChecklist`模组使用[一个配置设置](https://github.com/search?q=repo%3AJavidPack%2FBossChecklist%20ModCallLogVerbose%20&type=code)来控制是否记录文本。这种切换允许与该模组集成的模组有一种方式来诊断他们的`Mod.Call`代码。

# 调试器调试
在文本调试部分，我们学习了输出特定值到聊天以识别和修复一些bug。那个过程很乏味，因为模组制作者每次进行更改时都需要重新构建模组。在本节中，我们将学习利用调试器（Visual Studio）的强大功能来调试我们的模组。本节假设模组制作者正在使用[Visual Studio](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio)并已正确设置它，请查阅该指南以遵循这些先决条件。

本节将介绍调试的基础知识，强烈建议熟悉这些基础知识的模组制作者进一步查阅官方的[Visual Studio调试器初探](https://learn.microsoft.com/en-us/visualstudio/debugger/debugger-feature-tour?view=vs-2022)指南以获取更多提示。那里讨论了各种其他技术来大大增强调试的实用性。

## 启动调试器
首先，确保在Visual Studio中打开模组的项目。接下来，确保tModLoader已关闭。还要确保在解决方案配置下拉菜单中选择了"调试"。现在点击标记为"Terraria"的按钮或按F5：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/01413b2a-7525-418b-9d72-951c1e391938)

如果您有错误，错误列表将出现，否则，模组将构建，自动启用，然后Terraria将启动。（您可能会发现全屏模式使模组制作变得困难，我建议在制作模组时使用窗口模式。）如果Visual Studio告诉您存在构建错误并询问是否仍要启动，请说不！修复错误并重试。如果您看到此内容，您就知道它正在工作：

![68747470733a2f2f692e696d6775722e636f6d2f39764b317276462e706e67](https://github.com/tModLoader/tModLoader/assets/4522492/93790fec-63a1-48a6-bae4-51af4fd27ec0)

进入游戏并准备触发您的bug。

### 调试服务器
您可以通过在dropdown中选择"Terraria Server"启动目标而不是"Terraria"来直接调试服务器进程。您可以正常启动客户端，一旦服务器加载世界，通过`localhost`作为IP地址连接到服务器。

## 断点
我们需要暂停程序以"检查"数据。这称为"中断模式"。为此，请在怀疑bug发生的代码行上点击"行号边距"。这将放置一个红点。这个红点是一个"断点"。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/6409eb14-f43f-420f-957b-50367217e94a)

下次tModLoader运行该行代码时，游戏将进入中断模式，您将在Visual Studio中看到该断点上的黄色箭头。您将无法在中断模式下与游戏交互。黄色箭头表示接下来将运行的代码行。

在tModLoader中，执行任何需要触发射中有断点的代码行的操作。（这可能是射武器、击杀敌人、装备配饰等。）

![image](https://github.com/tModLoader/tModLoader/assets/4522492/2a01125c-6f3e-4c31-aebd-fbacc836005e)

通过按`F10`键，您可以通过1行推进游戏状态。这称为"单步执行"代码。按`F5`或绿色继续按钮将退出中断模式，游戏将恢复。游戏将运行直到遇到另一个断点。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/e3e9b62e-f371-44a5-9d17-3e04845ab692)

## 检查变量
如果游戏不在中断模式下，在有bug的代码附近重复进入中断模式的步骤。我们现在将"检查"变量。为此，只需将光标悬停在变量上。[使用数据提示检查变量](https://learn.microsoft.com/en-us/visualstudio/debugger/debugger-feature-tour?view=vs-2022#inspect-variables-with-data-tips)指南更多地谈论此功能：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/327eb0c3-1104-4e24-a0d8-3f768b054bd9)

重要的是要记住黄色箭头表示接下来要执行的行。在上图中，`numberOfDiamonds`的值仍然是`0`，因为该行代码还没有运行。按F10前进一行后，值现在变为`5`，这是正确的。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/d620f053-44e4-4c7f-b3b5-4c698e8a2987)

通过单步执行代码和检查变量，我们可以遵循逻辑并识别错误发生在哪里。[自动和本地窗口](https://learn.microsoft.com/en-us/visualstudio/debugger/debugger-feature-tour?view=vs-2022#inspect-variables-with-the-autos-and-locals-windows)将快速显示当前方法相关的所有变量。固定变量是另一个有用的技术。另一个有用的功能是固定变量以及本地或自动窗口中的变量值在单步执行代码后更改时会变为红色。

## 编辑代码
一旦识别出bug，您可以在调试时编辑代码。当文件被保存时，它们应该立即生效。要启用此功能，请仔细检查"文件保存时热重载"选项是否已选中：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/34e90454-6570-4884-8dfb-a9af6903c1ed)

请注意，添加新方法和类可能需要重新启动游戏：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/6a1c8fad-708d-4f6a-81da-1e4f3f792da8)

使用"调试器调试"，您应该能够比"文本调试"中的示例更快地修复相同的问题。更快地修复bug可以让您有更多时间制作有趣的模组。

## 异常调试
调试异常是仅在使用调试器时才能做到的事情。当调试时发生异常，游戏将立即进入中断模式。在Visual Studio中，将显示导致异常的代码行。从那里，您可以检查变量并尝试识别bug。

如果异常发生在tModLoader代码中，您可能会看到空白屏幕，因为您没有可用的源代码。在这种情况下，查看"堆栈跟踪"窗口并导航到导致tModLoader代码抛出异常的模组中的行。

官方的[检查调用堆栈](https://learn.microsoft.com/en-us/visualstudio/debugger/debugger-feature-tour?view=vs-2022#examine-the-call-stack)和[检查异常](https://learn.microsoft.com/en-us/visualstudio/debugger/debugger-feature-tour?view=vs-2022#exception)部分在该指南中更深入地解释了这个概念。

### 异常调试示例
发现`Object reference not set to an instance of an object`错误的确切原因：在[此视频](https://github.com/tModLoader/tModLoader/assets/4522492/0d6401fe-3c57-46c7-9612-3097d207b7e5)中，我们可以轻松诊断错误发生在哪里。当错误在游戏中发生时，Visual Studio进入前台并高亮显示发生错误的行。然后我们可以将鼠标悬停在事物上并检查它们的值。这里我们注意到`instance`为null。

现在我们可以修复错误，我们可以使用"查找所有引用"来确认是的，我们忘记将`instance`设置为一个值。您可能注意到`instance`下的绿色下划线警告我们`instance`从未被赋值。我们应该注意到的！现在我们可以轻松修复错误：[视频](https://github.com/tModLoader/tModLoader/assets/4522492/7cc8f838-6096-4a5c-8c4d-83c9647ca262)

## 查看反编译的tModLoader代码
在调试时，我们可以使用F10单步执行行。有时我们需要查看一个方法以找出问题。F11允许我们进入一个方法。通过进入一个方法，我们可以看到对象可能被更改的方式和原因。如果该方法来自tModLoader，默认情况下我们无法进入它。我们可以启用一个功能来允许进入反编译代码。要在Visual Studio中执行此操作，请使用搜索（Ctrl-Q）搜索"decompile source"。点击"在需要时自动反编译为源（仅托管）"，向下滚动以选中复选框，最后点击"OK"：
![image](https://github.com/tModLoader/tModLoader/assets/4522492/45b1ccc1-bcdd-444f-b507-18d4d5c227e6)
![image](https://github.com/tModLoader/tModLoader/assets/4522492/034de91f-4636-4fd5-9ecd-36d6549809b3)
从现在开始，您将能够在调试时进入tModLoader方法。可能需要一些时间来完成反编译。