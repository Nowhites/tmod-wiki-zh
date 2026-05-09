# 简介
tModPorter允许mod开发者轻松地将现有代码移植到最新的tModLoader API。tModPorter与tModLoader一起更新。每当对tModLoader API进行会破坏现有mod源代码的更改时，我们都会尝试编写tModPorter代码来为mod开发者修复代码。在极少数情况下无法自动修复时，我们会添加注释来指导mod开发者如何修复他们的代码以适应更改。

# 拉取请求
编写tModPorter测试和代码重构与更改tModLoader源代码本身是不同的技能组合。当贡献者提出拉取请求时，**不期望**他们编写tModPorter代码。但是，**鼓励**频繁贡献者尝试为其拉取请求编写tModPorter代码。

本指南将帮助有兴趣的贡献者编写tModPorter代码。我们鼓励贡献者尝试一下，因为这将加快tModLoader开发。请记住，对于拉取请求，tModPorter代码不是必需的。如果需要，tModLoader维护者能够在审查拉取请求时编写tModPorter代码。

# 何时需要tModPorter
并非所有tModLoader的更改都需要tModPorter更改。新钩子、新功能和新的公共变量都是不需要任何tModPorter考虑的拉取请求示例。

当您拉取请求中的更改发布后，如果现有modder的代码会出现编译错误，则需要tModPorter。例如，更改钩子的参数是需要tModPorter代码的典型更改。

# 基础
tModPorter有2个主要部分："测试"和"重构"。我们将首先创建一个失败的测试，然后添加重构来修复那些失败的测试。

> 请注意，本指南中的说明将使用一个添加了`bool newParameter`到`ModBuff.RightClick`方法的人为示例进行演示。

## 运行测试
首先，让我们确保测试在任何更改之前正常运行。这是一个完整性检查。按照以下步骤操作：

* 打开`tModLoader.sln`
* 构建`Terraria`项目
* 在解决方案资源管理器中，右键单击`tModPorter.Tests`项目并选择"运行测试"

![image](https://github.com/tModLoader/tModLoader/assets/4522492/d5b7a4c9-840e-4491-b1a7-580088c525de)

* 这将构建`tModPorter`和`tModPorter.Tests`项目，并将显示"测试资源管理器"面板。片刻后，测试应该运行并且应该全部通过。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/06e77f8d-29f1-4bbf-ad3f-9edd178f2d15)

如果有什么没有通过，要么您忘记了构建Terraria项目，要么其他什么地方出错了。向tModLoader维护者寻求帮助。

如果一切通过，您就可以进行下一步了。

## 编写失败的测试
在解决方案资源管理器中，打开`tModPorter.Tests`项目和`TestData`文件夹，您应该会看到一组`.cs`文件。这些文件成对出现，既有常规文件也有".Expected."文件。常规文件是给tModPorter的输入，另一个是tModPorter的预期输出：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/4d8a8f65-056e-4237-b623-d24815e64cb4)

让我们看一个例子，打开`ModBuffTest.cs`和`ModBuffTest.Expected.cs`。在`ModBuffTest.cs`（输入文件）中，您将看到来自各种先前版本的tModLoader的代码。例如，`ModifyBuffTip`的参数曾经是`ref string tip, ref int rare`。在`ModBuffTest.Expected.cs`（预期输出文件）中，您将看到运行tModPorter后的预期结果。这里我们看到`ModifyBuffTip`现在有当前参数`ref string buffName, ref string tip, ref int rare`。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/368665d9-6ea2-4579-a331-b7c3804f67ae)

现在我们了解了输入和预期文件，是时候编写测试了。找到最适合您的PR更改的文件。如果没有合适的文件，可以创建，但大多数tModLoader API类应该存在现有测试。将代码添加到输入文件，显示modder当前可能使用的内容。保持代码简单，只需更改的方法或字段，无需编写实际mod内容。接下来，将新代码添加到预期文件中。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/bb43636e-a542-439e-91dc-1491067c81d2)

再次运行测试。您应该会看到一到两个失败。

如果`ExpectedModCompiles`测试失败，这意味着预期文件没有正确构建。点击测试条目查看错误消息。这可能只是缺少using语句，将using语句添加到**输入和预期**两个文件中，然后再次尝试运行测试。如果您看到"找不到合适的重写方法"，您可能忘记了构建`Terraria`项目或输入有误。（在极少数情况下，tModPorter将无法完全将代码移植到新方法，请参阅下面的[#if COMPILE_ERROR](https://github.com/tModLoader/tModLoader/wiki/Contributing-to-tModPorter#if-compile_error)部分，了解如何告诉测试忽略预期输出中的编译错误。）

您应该在`RewriteCode`部分看到输入文件的错误。如果没有，那么有什么地方出错了。错误详情将显示预期文件与输出文件之间的差异。此时，我们希望此测试失败，我们将在下一节中修复它。确保差异看起来正确。应该只显示tModPorter有望修复的更改的差异。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/80ca328e-0a78-46a9-b73c-01a429411851)

## 编写重构
现在我们准备好编写实际的tModPorter代码了。打开`tModPorter`项目中的`Config.ModLoader.cs`文件。这是实现大多数重构的地方。该文件中的每个语句代表tModPorter将应用于mod源代码的自动重构。

例如，第一行，`RenameInstanceField("Terraria.ModLoader.ModType", from: "mod", to: "Mod");`，将把`ModType`类中实例字段`mod`的所有用法更改为`Mod`。如果您还记得，这个更改发生在1.4.3版本中。这段代码仍然存在，以便tModPorter可以帮助将1.3 mod移植到当前tModLoader。

找一个与您正在实现的更改类似的现有重构，应该有所有tModPorter功能的示例。使用现有重构作为指南。现在为您的心重构找到合适的位置。尽量遵循现有文件组织。按照示例中所示的模式实现重构：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/2a7301cb-ddc5-48c4-9478-f232d2362e6b)

## 再次运行测试
现在再次运行测试（右键单击`tModPorter.Tests`项目并选择"运行测试"）。如果一切顺利，您应该看到测试通过了：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/e2089eef-caf2-4e29-ba5b-0ef0ae3a01b3)

如果测试因代码格式或空白而失败，请在预期或输入文件中修复问题，然后再次运行。如果重构似乎根本没有应用，请仔细检查您的代码。如果仍然失败，请向tModLoader维护者寻求帮助。

一切正常后，将您的代码更改提交到GitHub。

## 更新拉取请求
请使用`Porting Notes`部分更新您的拉取请求。该部分应详细说明modder需要采取的手动步骤以复制tModPorter执行的自动重构。它还应该提到modder需要适应的其他更改。我们将使用此信息在[tModLoader Discord](https://discord.gg/tmodloader)的`#preview-update-log`频道、[Steam上的发布公告](https://steamcommunity.com/app/1281930/allnews/)以及我们wiki上的[更新迁移指南](https://github.com/tModLoader/tModLoader/wiki/Update-Migration-Guide)中显示。请不要更新迁移指南wiki页面，tModLoader维护者会这样做。

# 工作流程
综上所述，这是基本工作流程：

* 打开`tModLoader.sln`
* 构建`Terraria`项目
* 制作失败的测试
* 编写重构代码
* 确保测试成功
* 提交更改并更新拉取请求描述

# 高级
## #if COMPILE_ERROR
有时tModPorter的功能不足以完成modder的所有更改。例如，`ModBuffTest.Expected.cs`示例有一个使用`#if COMPIILE_ERROR...#endif`"注释掉"的部分。这部分代码将无法编译。留下编译器错误在更高级的情况下是不可避免的。在这种情况下，注释会指导modder如何修复问题。我们使用`#if COMPILE_ERROR...#endif`模式允许测试跳过尝试构建这些代码行，从而使`ExpectedModCompiles`测试能够继续通过。除非实际结果应该导致编译错误，否则不要使用`#if COMPILE_ERROR...#endif`。
