### "Terraria.ModLoader.Mod.GetTexture(String name)" 错误
___
这是下面 `MissingResourceException` 的1.3版本错误。<!--为链接到它的内容保留-->

### "Terraria.ModLoader.Exceptions.MissingResourceException: Expected resource not found" 错误
___
此错误意味着tModLoader找不到您指定的纹理。您可能会想，"我在哪里指定了要使用的纹理？"，要回答这个问题，您应该阅读[自动加载](https://github.com/tModLoader/tModLoader/wiki/Basic-Autoload)页面。如果您正在制作装备物品，如护甲、虚荣物品或配饰，请特别注意"额外纹理"部分，因为装备物品需要2个单独的纹理。

现在您了解了自动加载，您知道纹理是从类的命名空间和类名派生的。如果您不知道什么是命名空间和类名：[谷歌一下](http://www.google.com)

假设有一个名为 `CoolGun` 的类，位于 `ExampleMod.Items` 命名空间中，文件名为 `MyGun.cs`，位于 `\Mod Sources\CoolMod\Items\Weapons\` 文件夹中，纹理文件为 `MyGun.png`。

模组制作者假设使用的纹理：`\Mod Sources\CoolMod\Items\Weapons\MyGun.png`
自动加载实际会查找的纹理：`\Mod Sources\ExampleMod\Items\CoolGun.png`

让我们来看看新手模组制作者在这里会遇到的问题：
- 期望.cs文件名和.png文件名必须匹配。
  - 实际上不是这样，类名需要与纹理文件名匹配。但是，让.cs文件名和类名保持一致是有帮助的。
- 期望.cs文件的文件夹路径很重要
  - 实际上不是这样，.cs文件内的类的命名空间对于自动加载纹理至关重要。但是，让命名空间结构与文件夹结构相匹配是有帮助的。

看到错误后，新手模组制作者会采取以下步骤：
- 保持.cs文件名、类名和.png文件名一致。
- 确保您的基础命名空间和Mod Sources文件夹相等
- 确保您的命名空间和文件夹结构匹配：ExampleMod.Items.Weapons 变为 `\Mod Sources\ExampleMod\Items\Weapons\`

图解说明：
![](https://i.imgur.com/XLeNQCs.png)

### 'ClassName.MethodName(参数)': 找不到合适的可重写方法
这意味着您为方法提供的参数与您尝试重写的虚方法的参数不匹配。这最常见于尝试重写hook并从旧教程或AI中查找过时代码示例的模组制作者。例如，您可能从过时的示例中复制了
```cs
public override void OnHitNPCWithProj(Projectile proj, NPC target, int damage, float knockback, bool crit)
```
但是
```cs
public override void OnHitNPCWithProj(Projectile proj, NPC target, NPC.HitInfo hit, int damageDone)
```
是当前版本tModLoader的正确更新方法参数。另一个常见错误是将代码从一个类（如 `ModItem` 类）直接粘贴到另一个类（如 `ModProjectile` 类）中。虽然许多方法都存在于两个基类中，但它们的参数通常不同，需要调整方法参数和代码内容以适应。虚方法或"hook"要求参数与tModLoader提供的现有方法参数匹配，这是一个必须遵循的模板，额外或更改的参数将不起作用。

修复方法很简单，要么在[文档](http://tmodloader.github.io/tModLoader/)中查找正确的参数并使用它们，要么删除您拥有的内容，使用您的IDE[自动生成正确的参数](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#override)。

### "Sequence contains no matching element" 错误
___
这意味着您的Mod不包含任何扩展自Mod的类。如果您完成了[基础模组制作指南](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Modding-Guide)，您应该注意到其中一个文件有 `class MyMod : Mod`。确保您在模组中只有1个且仅有1个这样的类。

### "error CS0234: The type or namespace name 'X' does not exist in the namespace 'Y' (are you missing an assembly reference?)"
___
当您的代码中的命名空间或类与原版Terraria类或命名空间冲突时，通常会发生这种情况。例如，如果您恰好有一个名为Projectile的命名空间，并尝试使用Projectile.NewProjectile，计算机会混淆您是指Terraria.Projectile类还是Projectile命名空间中的某个内容。最简单的解决方案是不要将命名空间命名为与原版类相同的名称。例如，ExampleMod的命名空间名为Dusts、Projectiles和Items，以避免与原版类Dust、Projectile和Item冲突。

### Error CS0103: The name 'X' does not exist in the current context
___
这意味着您有一个计算机不理解的变量名。基本上您自己发明了一个东西而计算机完全不知道那是什么。想象您的朋友说"把Flobulizer递给我"，但您不知道那是什么，也从未听过那个词。计算机的情况也是如此。您可能告诉计算机"给玩家施加冰冻debuff"，但计算机不知道"player"是什么。修复方法：谷歌一些关于C#变量的教程，学习变量如何工作，然后确保在使用变量之前先声明它。

### Error CS0246: The type or namespace name 'Player' (or Item, Projectile, etc) could not be found (are you missing a using directive or an assembly reference?)
___
这是因为计算机不知道Player类是什么。您需要告诉计算机在哪里找到Player。通过在文件顶部添加"using Terraria;"来做到这一点，这样计算机就知道如何找到Player类。（另一个常见的是Vector2和Color，需要"using Microsoft.Xna.Framework;"）

### Error CS0103: The name 'ItemID' (or ProjectileID, NPCId, etc) does not exist in the current context
___
这是因为计算机不知道ItemID类是什么。您需要告诉计算机在哪里找到ItemID。通过在文件顶部添加"using Terraria.ID;"来做到这一点，这样计算机就知道如何找到ItemID类。

### 'LocalizedText' does not contain a definition for 'SetDefault'
___
*如果您正在移植旧模组，请参阅[迁移指南](https://github.com/tModLoader/tModLoader/wiki/Update-Migration-Guide#localization-changes)了解如何解决整个模组中的这些错误。*

此错误意味着您使用的是过时代码（1.4.4之前）。删除或注释这些行，然后在编译模组时，编辑生成的本地化。您可以在[这里](https://github.com/tModLoader/tModLoader/wiki/Localization)了解更多关于本地化文件和工作流程的信息。

### You must add a reference to assembly 'Relogic, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
___
如果您使用的是Visual Studio，可能需要添加[对Relogic.dll的引用](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio#error-cs0246-the-type-or-namespace-name-relogic-could-not-be-found)

### Error CS0161: 'ExampleItem.UseItem(Player)' (or some other hook): not all code paths return a value
___
某些方法有返回值。在使用tModLoader hook时，您需要注意方法有返回值，不返回值是一个错误。例如，UseItem的方法签名是：`bool UseItem(Player player)`，那个`bool`告诉我们必须返回一个bool值。查阅[文档](http://tmodloader.github.io/tModLoader/)了解需要返回什么值。

### System.Threading.ThreadStateException: FNA3D graphics functions must be called on the main thread
___
[仅限1.4]如果您遇到此问题，这意味着您在非主线程上执行图形任务（如释放或预乘纹理）（如 `Load` 和 `Unload`）。要解决这个问题，请将您的代码包装在以下内容中：
```cs
Main.QueueMainThreadAction(() => {
	// 在这里编写您的代码
});
```

### C# 6问题："An attempt was made to load an assembly from a network location..."
这与Windows阻止下载的文件有关。发生的事情是下载的tModLoader zip文件被标记为"已阻止"或"从互联网下载"，当您使用默认的Windows解压过程时，所有解压的文件也会被标记。那些文件需要被取消阻止才能正常工作。最简单的方法是首先取消阻止tModLoader下载，然后重新安装tModLoader。方法是：在文件资源管理器中选择zip文件，按快捷键"alt-enter"或右键单击文件并选择属性。然后只需点击取消阻止，然后按确定。Windows 7和8看起来可能不同：

![](https://i.imgur.com/5IaE5U8.png)

由于在以前的安装基础上安装非常简单，这是推荐的过程。如果您宁愿手动操作，请打开您的Terraria安装文件夹，查看tModLoader安装的所有文件的属性，并取消阻止每个文件。这包括Terraria.exe和Mod Compile文件夹中的所有文件。

### TileObjectData.AddTile 问题
___
确保在所有关于 `TileObjectData.newTile` 的语句之后调用 `TileObjectData.AddTile`。涉及这些语句的乱序代码会破坏Terraria。