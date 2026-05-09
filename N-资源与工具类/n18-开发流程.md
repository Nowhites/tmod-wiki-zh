- **本文大致解释了tModLoader是如何建立的、它是如何工作的以及如何维护的**
- **将本文作为tML的入门资料，并获取有关该API的一些有价值的背景信息**

# tModLoader是如何开发的？
首先，tModLoader是对原版源代码的修改，既促进mod开发也支持mod加载（游戏）。它使用自定义构建的补丁程序（由chicken-bones编写），可以通过补丁管理源代码。补丁程序在内部使用[ILSpy](https://github.com/icsharpcode/ILSpy)来反编译原版客户端，从而允许使用[Visual Studio](https://visualstudio.microsoft.com/)等IDE进行修改。

补丁系统使tML能够成为开源的，因为它不会泄露原版信息，也不会托管反编译的源代码。开发人员将需要手动反编译和补丁其本地源代码以在tML代码库上工作。补丁程序可以通过首先反编译原版客户端来执行此操作，然后在一个将应用补丁的单独托管项目中进行。这将使用户能够在本地访问tML修改的代码库。

# tModLoader如何更新？
每次原版更新时，tModLoader的补丁都会损坏。这是因为原版代码库发生了变化。对于每次更新，开发人员将需要手动遍历补丁以找出发生了什么变化以及如何修复补丁。这一切都是手动完成的。

# tModLoader如何促进mod制作需求？
tModLoader公开了各种类和 方法，mod制作者可以使用它们来创建自己的内容。然后它可以"构建"mod制作者的mod并在原版代码中注册其更改。mod制作者通过在IDE中引用modded程序集（tModLoader）来访问此功能。每个mod都有一个入口点，即"Mod"类，然后可以从中添加任何所需的内容。

tModLoader为mod制作者提供了面向对象的设计方法来构建内容，这由[ModItem](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/ModLoader/ModItem.cs)、[ModProjectile](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/ModLoader/ModProjectile.cs)、[ModPlayer](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/ModLoader/ModPlayer.cs)等类来促进。mod制作者可以使用这些类来向mod添加各自的主题。例如，使用ModItem类，mod制作者可以向游戏添加自己的物品。

我们设计这些类是为了易于使用和[多态](https://en.wikipedia.org/wiki/Polymorphism_(computer_science))。我们通过[抽象](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/abstract)方法或[虚](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/virtual)方法公开API，这些方法可以由[派生类](https://en.cppreference.com/w/cpp/language/derived_class)重写。抽象定义不包含任何逻辑，必须由派生方实现。虚定义_可以_包含基本逻辑，但可以由派生方重写。

一旦mod代码库创建完成，就可以使用游戏内的mod源菜单来构建它，这将把mod打包成.tmod文件。然后可以将此文件分发给其他玩家，以便他们可以玩该mod。

# tModLoader如何促进使用mod玩游戏？
tModLoader不仅是构建mod的工具，还允许玩游戏。它通过提供Mod浏览器（查找和下载mod）和游戏内功能来加载和应用mod到游戏（以及卸载它们）。玩家只需要一个".tmod"文件即可开始，这是tModLoader特定的mod包格式。

# tModLoader如何理解mod想要添加的内容？
在加载mod时，tModLoader将加载该[mod的程序集](https://www.google.com/search?client=firefox-b-d&q=c%23+code+assembly)（其代码）并查找tModLoader提供的类。然后它可以将此内容加载到游戏中。这是通过所谓的"Loader"类完成的。这些类是包含将特定内容加载到游戏中的特定代码的类。这些类执行诸如准备要被编辑的原版源代码之类的事情，这可以包括调整容纳特定内容的数组大小等操作。

加载器包含从原版代码中的特定点调用的方法，当mod制作者期望其代码执行时会调用这些方法。这些调用由tModLoader开发人员添加，这就是mod内容工作的原因。tModLoader有点像客户端和mod之间的桥梁；它将知道加载了哪些mod，然后继续在它们中调用必要的方法。这就是为什么mod制作者不必担心他们的代码将如何或何时执行，他们可以只专注于创建内容。

# 规避性补丁
由于补丁系统的存在，我们尽量少编辑反编译的源代码。这样做可以最小化补丁差异，从而使在原版更新时更容易修复API。如果您希望为API做贡献，请记住这一点，并尽量减少补丁差异。

## 提示
以下也是我们自己记住以最小化补丁差异的事情：

1. 如果可以的话，从原版类中调用tModLoader类。当代码位于我们自己的类中时，这不是维护麻烦。
2. 不要重构任何原版代码。虽然很诱人，但它会增加补丁差异并使更新更加困难。
3. 记录您的更改，尤其是在补丁中进行的更改。标记行号和里程碑。
