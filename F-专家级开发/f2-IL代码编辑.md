___
[想要学习如何IL编辑其他模组？点击这里！](Patching-Other-Mods-Using-MonoMod)

**IL编辑新手？那么首先阅读这个指南。**
___

# 简介
本指南探讨IL编辑。IL编辑是一项专家级技术，可以非常强大。通过IL编辑，模组基本上可以在泰拉瑞亚代码库的任何地方编辑代码，而不依赖于tModLoader钩子。IL编辑代表中间语言编辑，本质上是我们按需编辑编译后的代码。这对于非常偏僻的方法很有用，这些方法不值得使用tModLoader方法。通过IL编辑，至关重要的是您的代码采用防御性编程技术，以正确预见到其他模组尝试修改代码相同区域的可能性。IL编辑很强大，但如果可能的话，尽量利用tModLoader钩子，因为它们可以比IL编辑更好地促进多个模组。如果您还不害怕，请继续阅读。

### 限制
请注意，公共语言运行时(CLR)会在运行时内联短方法，这些方法无法使用这些技术进行编辑。什么算"短"没有明确定义，但像属性这样的可能是候选者。

## 前置条件
* [dnSpy](https://github.com/0xd4d/dnSpy) - 我们将使用编译功能来帮助设计我们的补丁。
* [专家前置条件](https://github.com/tModLoader/tModLoader/wiki/Expert-Prerequisites) - 如果您不知道如何调试，您将失败。
* [高级原版代码适配](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption) - 熟悉在泰拉瑞亚源代码中查找内容非常有帮助。
* 需要Visual Studio或类似的IDE。

## 代码布局
您可以将补丁放在任何您想要的地方，但`Mod.Load`或任何`ModType.Load`方法都是很好的候选者。

# 示例 - Hive Pack升级
## 目标
本指南的目标是在佩戴[Hive Pack](https://terraria.wiki.gg/wiki/Hive_Pack)的升级时，让各种蜜蜂相关物品更强。各种物品将生成蜜蜂作为武器。如果玩家佩戴着Hive Pack，`player.strongBees`将为true，生成的蜜蜂将有机会作为GiantBee生成。为了实现我们的Hive Pack升级，我们需要修改引用`player.strongBees`的代码，使其也有机会生成Beenade。

这是装备Hive Pack时蜜蜂武器目前的工作方式：

https://github.com/tModLoader/tModLoader/assets/4522492/6ca23d26-ce33-4089-8f92-fd09b1d4487f

注意大约一半的蜜蜂生成作为GiantBees。

## 原始方法
让我们首先查看原始方法，看看我们想要对IL代码做什么更改。启动dnSpy并通过File->Open添加dll，然后浏览到tModLoader.dll。展开TModLoader、tModLoader.dll、Terraria和Player的选项卡。向下滚动并点击`beeType()`方法。我怎么知道这是我需要查看的地方？经验。我按照反编译代码中蜜蜂武器的逻辑，发现`Player.beeType`是做出我想修改的决定的方法。如果您正在阅读本文，您可能已经识别出一个您希望更改的方法，因为您正在使用IL编辑。到目前为止您应该看到这个：
![](https://i.imgur.com/9fOlM4n.png)
```cs
public int beeType()
{
	if (this.strongBees && Main.rand.Next(2) == 0)
	{
		this.makeStrongBee = true;
		return 566;
	}
	this.makeStrongBee = false;
	return 181;
}
```
这是该方法的C#代码。如果您在`Terraria.ID.ProjectileID`中查找，您会看到566是`GiantBee`，181是`Bee`。我们的计划是将以随机几率将选定的弹射物更改为`Beenade`（183）。这是我们想要的代码的样子：
```cs
public int beeType()
{
	if (this.strongBees && Main.rand.Next(2) == 0)
	{
		this.makeStrongBee = true;
		if(this.GetModPlayer<ExamplePlayer>().strongBeesUpgrade && Main.rand.NextBool(10))
			return ProjectileID.Beenade;
		return 566;
	}
	this.makeStrongBee = false;
	return 181;
}
```
这很棒，但现在怎么办？首先，让我们使用dnSpy查看此方法的IL代码。在菜单栏中，点击下拉组合框并从C#切换到IL。这就是我们看到的：
```cs
// Token: 0x0600050A RID: 1290 RVA: 0x0023D2D0 File Offset: 0x0023B4D0
.method public hidebysig 
	instance int32 beeType () cil managed 
{
	// Header Size: 1 byte
	// Code Size: 47 (0x2F) bytes
	.maxstack 8

	/* 0x0023B4D1 02           */ IL_0000: ldarg.0
	/* 0x0023B4D2 7B280A0004   */ IL_0001: ldfld     bool Terraria.Player::strongBees
	/* 0x0023B4D7 2C1A         */ IL_0006: brfalse.s IL_0022

	/* 0x0023B4D9 7E19040004   */ IL_0008: ldsfld    class Terraria.Utilities.UnifiedRandom Terraria.Main::rand
	/* 0x0023B4DE 18           */ IL_000D: ldc.i4.2
	/* 0x0023B4DF 6F53090006   */ IL_000E: callvirt  instance int32 Terraria.Utilities.UnifiedRandom::Next(int32)
	/* 0x0023B4E4 2D0D         */ IL_0013: brtrue.s  IL_0022

	/* 0x0023B4E6 02           */ IL_0015: ldarg.0
	/* 0x0023B4E7 17           */ IL_0016: ldc.i4.1
	/* 0x0023B4E8 7D690B0004   */ IL_0017: stfld     bool Terraria.Player::makeStrongBee
	/* 0x0023B4ED 2036020000   */ IL_001C: ldc.i4    566
	/* 0x0023B4F2 2A           */ IL_0021: ret

	/* 0x0023B4F3 02           */ IL_0022: ldarg.0
	/* 0x0023B4F4 16           */ IL_0023: ldc.i4.0
	/* 0x0023B4F5 7D690B0004   */ IL_0024: stfld     bool Terraria.Player::makeStrongBee
	/* 0x0023B4FA 20B5000000   */ IL_0029: ldc.i4    181
	/* 0x0023B4FF 2A           */ IL_002E: ret
} // end of method Player::beeType
```
让我们通过跟随来理解这个。请务必将鼠标悬停在各个指令上或点击它们以直接从dnSpy打开[OpCode文档](https://msdn.microsoft.com/en-us/library/system.reflection.emit.opcodes_fields(v=vs.110).aspx)：
```cs
// ldarg.0将第一个参数（参数0）推送（加载）到堆栈上。
// 这个方法没有参数，怎么回事？
// 实际上，非静态方法将当前实例（this）作为第一个参数，
// 即使它不在方法参数中，它也在那里。
IL_0000: ldarg.0
// 接下来，将strongBees的值推送到堆栈
IL_0001: ldfld     bool Terraria.Player::strongBees
// 如果该值为false，则跳转（分支）到IL_0022（方法中0x0022处的指令）
IL_0006: brfalse.s IL_0022

// 将静态字段Main.rand推送到堆栈
IL_0008: ldsfld    class Terraria.Utilities.UnifiedRandom Terraria.Main::rand
// 然后将2推送到堆栈
IL_000D: ldc.i4.2
// 然后调用Main.rand的"Next"方法，将结果推送到堆栈。
IL_000E: callvirt  instance int32 Terraria.Utilities.UnifiedRandom::Next(int32)
// 如果堆栈顶部（"Next"的结果）为非零，跳转到IL_0022。
IL_0013: brtrue.s  IL_0022

// 将Player实例推送到堆栈
IL_0015: ldarg.0
// 将1推送到堆栈（记住，1是true）
IL_0016: ldc.i4.1
// 将Player.makeStrongBee设置为true。
IL_0017: stfld     bool Terraria.Player::makeStrongBee
// 将566推送到堆栈。
IL_001C: ldc.i4    566
// 返回堆栈顶部，566。
IL_0021: ret

// 将Player实例推送到堆栈
IL_0022: ldarg.0
// 将0推送到堆栈（记住，0是false）
IL_0023: ldc.i4.0
// 将Player.makeStrongBee设置为false
IL_0024: stfld     bool Terraria.Player::makeStrongBee
// 将181推送到堆栈
IL_0029: ldc.i4    181
// 返回堆栈顶部，181。
IL_002E: ret
```
希望这个带注释的IL代码可以帮助您理解事物。如果您感到困惑，您可能值得学习关于堆栈和其他与计算机运行指令相关的东西。
既然我们已经了解了原始方法，让我们使用dnSpy查看我们的更改将如何看起来。在dnSpy中，右键单击该方法并点击`Edit Method (C#)...`。
![](https://i.imgur.com/vKEjicD.png)
在弹出的窗口中，进行我们之前决定的更改，然后点击Compile。
![](https://i.imgur.com/ojUkfez.png)
如果缺少程序集引用或代码错误，您会看到一些错误。首先，我们需要添加`using Terraria.ID;`和`using ExampleMod;`。然后，我们需要为ReLogic和ExamplePlayer添加缺失的引用。您可以在`\Documents\My Games\Terraria\ModLoader\references`中找到ReLogic.dll。如果您在游戏中使用了Extract Mod工具，您可以在`\Documents\My Games\Terraria\ModLoader\references\mods\`中找到ExampleMod.dll；或者如果您在Visual Studio中构建了模组，则在`\Documents\My Games\Terraria\Modding\ModLoader\ExampleMod\bin\Debug\net45\`中找到。通过"添加程序集引用"将这两个dll添加到dnSpy：
![](https://i.imgur.com/U6f23SK.png)
现在我们已经修复了错误，点击"Compile"，窗口将关闭，您将返回IL代码。切换回C#视图，您可能会惊讶地发现您的更改并不完全保持在C#视图中。逻辑都是相同的，只是我们代码的布局移动了各个部分。
```cs
public int beeType()
{
	if (!this.strongBees || Main.rand.Next(2) != 0)
	{
		this.makeStrongBee = false;
		return 181;
	}
	this.makeStrongBee = true;
	if (this.GetModPlayer<ExamplePlayer>().strongBeesUpgrade && Main.rand.NextBool(10))
	{
		return 183;
	}
	return 566;
}
```
但是不要担心，因为我们的补丁方法是找到`return 566`并在它之前插入我们的指令。即使指令在临时修改的副本中移动了一点，这仍然可以完成。切换回IL视图，让我们找到`this.makeStrongBee = true;`和`return 566`之间的代码。这段代码现在包含我们if语句和返回183的指令。让我们现在注释这些指令：
```cs
// 将Player实例推送到堆栈
IL_0015: ldarg.0
// 将1或true推送到堆栈
IL_0016: ldc.i4.1
// 设置Player.makeStrongBee
IL_0017: stfld     bool Terraria.Player::makeStrongBee

// 新增：将Player实例推送到堆栈
IL_001C: ldarg.0
// 新增：调用GetModPlayer
IL_001D: call      instance !!0 Terraria.Player::GetModPlayer<class [ExampleMod]ExampleMod.ExamplePlayer>()
// 新增：将strongBeesUpgrade的值放到堆栈上
IL_0022: ldfld     bool [ExampleMod]ExampleMod.ExamplePlayer::strongBeesUpgrade
// 新增：如果结果为false，跳转到指令3D。（短路）
IL_0027: brfalse.s IL_003D

// 新增：将Main.rand推送到堆栈
IL_0029: ldsfld    class Terraria.Utilities.UnifiedRandom Terraria.Main::rand
// 新增：将10推送到堆栈
IL_002E: ldc.i4.s  10
// 新增：调用Utils.NextBool。为什么是Utils？Utils是定义NextBool扩展方法的类！
IL_0030: call      bool Terraria.Utils::NextBool(class Terraria.Utilities.UnifiedRandom, int32)
// 新增：如果结果为false，跳转到指令3D。
IL_0035: brfalse.s IL_003D

// 新增：将183（又名Beenade）推送到堆栈
IL_0037: ldc.i4    183
// 新增：返回用它装载Beenade
IL_003C: ret

// 将566（又名GiantBee）推送到堆栈
IL_003D: ldc.i4    566
// 返回用它装载GiantBee
IL_0042: ret
```
通过注释新的IL代码，我们可以看到我们的逻辑被整齐地包含在原始`return 566`代码之前。现在让我们开发补丁代码。终于！

## 补丁代码
由于这个IL编辑相当直接，我们将详细说明这个补丁的3种不同方法。希望重复可以帮助深入了解IL编辑的不同方法。完整代码可以在[WaspNest.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Accessories/WaspNest.cs)上探索。

### 常见思路
第一个要探索的概念是加载我们的补丁。由于这个补丁关于我们模组中的新ModItem，让我们将补丁添加到`ModItem.Autoload`。只需重写`Autoload`并输入`IL_Player.beeType += HookBeeType;`，然后让Visual Studio为我们生成HookBeeType方法。如果Visual Studio不理解`IL_Player`类，请确保添加对MonoMod和TerrariaHooks dll的dll引用，这些可以在`Documents\My Games\Terraria\ModLoader\references`中找到。

接下来，我们开始编写代码。首先，我们将整个补丁方法包装在try-catch中，以防出现错误。在catch部分，写`MonoModHooks.DumpIL(ModContent.GetInstance<ExampleMod>(), il, e);`。这将IL转储到一个文件，以帮助我们在补丁失败时进行调试。接下来，我们通过写`var c = new ILCursor(il);`创建一个Cursor。Cursor允许我们以组织良好的方式导航IL代码。我们需要使用Cursor方法如`GotoNext`和`GotoLabel`来导航到IL指令列表中的正确索引。我们不能依赖硬编码的索引，因为我们需要我们的补丁在多个补丁编辑相同方法时正常工作，或者当tModLoader的不同构建略微更改IL指令时。设计健壮的补丁代码是预期的，因为这是一项专家级技术。

创建cursor后，我们需要将cursor前进到我们想要编辑的代码区域。正如我们通过在dnSpy中编译所发现的，我们想要在我们设置`makeStrongBee`为true的代码和返回566的代码之间插入我们的代码。我们可以写`v.GotoNext(i => i.MatchLdcI4(566))`来将cursor前进到下一个匹配`ldc.i4`的OpCode与操作数566的IL指令。如果找不到这样的指令，将抛出错误，该错误将被我们之前写的try-catch捕获，这将转储IL。

（您可能会发现[OpCodes类文档](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes?view=net-10.0)对以下三种方法有用，因为它包含一个包含各种操作码含义的表。）

### 方法1 - 修改评估堆栈
第一种方法是最简单的。在这种 方法中，我们可以利用这样一个事实：虽然`return 566;`在C#中是一行，但在IL指令中，它由2条指令组成，第一条将566推送到堆栈，第二条从方法返回。通过利用这一点，我们可以在这两条指令之间插入指令以实现我们想要的行为。实际上，我们将`return 566;`改为`return (this.GetModPlayer<ExamplePlayer>().strongBeesUpgrade && Main.rand.NextBool(10)) ? 183 : 566;`。这里的第一行代码向下移动cursor：`c.Index++;`。Cursor之前指向将566推送到堆栈的指令，所以增加索引使cursor正好在ret OpCode上。之后，我们对cursor调用`.Emit`并传入一个OpCode，这将在当前cursor索引处放置指定的OpCode并将所有其他指令向下推，类似于List.Insert。我们提供的指令是Ldarg_0，这将把当前Player实例推送到堆栈，因为这是一个非静态方法。此时，堆栈由顶部的Player和下面的原始返回值的一个int组成。
有了堆栈上的int和Player，我们现在可以使用`.EmitDelegate`来编写C#代码来完成我们补丁的其余部分，大大简化了事情。提供给Delegate的泛型类型需要匹配当前堆栈，从下到上（从最旧到最近推送）。在这种情况下，我们将使用一个`Func`，它接受2个参数并返回1个参数。2个输入参数必须是`int`和`Player`，因为它们匹配当前堆栈。输出类型将是`int`，因为它在int和Player被弹出后将放到堆栈上。当我们的补丁开始时，堆栈上有一个int，所以我们需要确保当我们的补丁完成时堆栈仍然是相同的，这样我们就不会崩溃游戏。在我们的delegate中，我们只是放入我们的条件并使用提供的原始返回值和Player实例来驱动我们的逻辑。这是完整代码：
```cs
// 代码包装在try catch中，以防IL编辑出错
try {
	// 将Cursor启动在开始处
	var c = new ILCursor(il);
	// 尝试找到566被放置在堆栈上的地方
	c.GotoNext(i => i.MatchLdcI4(566));

	// 将cursor移动到566之后并进入ret op。
	c.Index++; 
	// 将Player实例推送到堆栈
	c.Emit(Mono.Cecil.Cil.OpCodes.Ldarg_0);
	// 使用堆栈中的int和Player调用一个delegate。
	c.EmitDelegate<Func<int, Player, int>>((returnValue, player) =>
	{
		// 常规C#代码
		if (player.GetModPlayer<ExamplePlayer>().strongBeesUpgrade && Main.rand.NextBool(10) && Main.ProjectileUpdateLoopIndex == -1)
			return ProjectileID.Beenade;
		return returnValue;
	});
	// 在delegate之后，堆栈将再次有一个int，ret指令将从这个方法返回
}
catch (Exception e) {
	// 如果IL编辑有任何失败，这个方法将IL转储到Logs/ILDumps/{Mod Name}/{Method Name}.txt
	MonoModHooks.DumpIL(ModContent.GetInstance<ExampleMod>(), il);

	// 如果模组没有IL钩子就无法运行，则抛出异常而不是。异常将在内部调用DumpIL
	// throw new ILPatchFailureException(ModContent.GetInstance<ExampleMod>(), il, e);
}
```

### 方法2 - 标签和分支
接下来这种方法与方法1非常相似，但旨在展示分支如何通过标签工作。如果您记得从我们对IL代码的探索中，IL指令经常使用`brfalse` OpCodes有条件地跳转到不同的指令。当我们在C#中制作if语句时，编译器将那些实现为跳转到不同区域的代码。通过IL编辑，我们可以定义我们的分支代码可以跳转到的标签。代码如下。我们可以看到，实际上这种方法通过更紧密地模仿插入到原始代码中`return 566;`之前的if语句的行为，更类似于典型的C#代码方法。虽然这是一个简单的例子，但使用标签和分支可能是一个有用的技能。
```cs
// 创建一个标签供以后使用
var label = il.DefineLabel();
// 将Player实例推送到堆栈
c.Emit(Mono.Cecil.Cil.OpCodes.Ldarg_0);
// 调用一个delegate，从堆栈弹出Player并推送一个bool
c.EmitDelegate<Func<Player, bool>>(player => player.GetModPlayer<ExamplePlayer>().strongBeesUpgrade && Main.rand.NextBool(10) && Main.ProjectileUpdateLoopIndex == -1);
// 如果堆栈上的bool为false，跳转到标签
c.Emit(Mono.Cecil.Cil.OpCodes.Brfalse, label);
// 否则，推送ProjectileID.Beenade并返回
c.Emit(Mono.Cecil.Cil.OpCodes.Ldc_I4, ProjectileID.Beenade);
c.Emit(Mono.Cecil.Cil.OpCodes.Ret);
// 将标签设置到当前cursor，它仍然指向将566推送的指令（后面跟着Ret）
c.MarkLabel(label);
```

### 方法3 - 直接OpCode
最后这种方法最终使用从dnSpy生成的直接输出。看到我们对方法C#代码的更改被整齐地包含在设置makeStrongBee为true和返回566之间的IL指令块中，我们可以直接在这些指令之间插入那些新指令。为此，我们获取dnSpy的输出，逐行地用等效的补丁代码替换每条指令。例如，对于`IL_001C: ldarg.0`，我们可以写`c.Emit(Mono.Cecil.Cil.OpCodes.Ldarg_0);`。我们可以使用`using static Mono.Cecil.Cil.OpCodes;`来精简代码到`c.Emit(Ldarg_0);`。我们仍然需要为分支指令生成新标签，并且需要知道预期值并正确转换。例如，要将`IL_002E: ldc.i4.s  10`转换为代码，我们需要阅读文档并看到`ldc.i4.s` OpCode期望一个int8，即sbyte。代码将是`c.Emit(Ldc_I4_S, (sbyte)10);`，不正确转换将崩溃游戏。
需要注意的另一件事是，您需要传入MethodInfo和FieldInfo类，而不是直接调用方法或字段。例如，要适应`IL_0037: ldsfld    int32 Terraria.Main::ProjectileUpdateLoopIndex`，您可能会犯错误写`c.Emit(Ldsfld, Main.ProjectileUpdateLoopIndex);`，但那不起作用。您需要使用常规反射技术来检索FieldInfo，就像这样：`c.Emit(Ldsfld, typeof(Main).GetField(nameof(Main.ProjectileUpdateLoopIndex)));`。请注意，使用`nameof`有助于避免拼写错误。这个例子还将展示如何检索泛型版本的MethodInfo。
将所有IL指令转换为补丁代码，制作标签，并将这些标签应用到需要的指令上后，我们的补丁就完成了。这种直接方法如果您不太了解堆栈工作原理并且愿意编写更多乏味的代码，则很有用。
```cs
var label = il.DefineLabel(); // 创建一个标签，将指向将566推送到堆栈的指令

c.Emit(Ldarg_0);
c.Emit(Call, typeof(Player).GetMethod("GetModPlayer", new Type[] { }).MakeGenericMethod(typeof(ExamplePlayer)));
c.Emit(Ldfld, typeof(ExamplePlayer).GetField(nameof(ExamplePlayer.strongBeesUpgrade)));
c.Emit(Brfalse_S, label);
c.Emit(Ldsfld, typeof(Main).GetField(nameof(Main.rand)));
c.Emit(Ldc_I4_S, (sbyte)10);
c.Emit(Call, typeof(Utils).GetMethod("NextBool", new Type[] { typeof(Terraria.Utilities.UnifiedRandom), typeof(int) }));
c.Emit(Brfalse_S, label);
c.Emit(Ldsfld, typeof(Main).GetField(nameof(Main.ProjectileUpdateLoopIndex)));
c.Emit(Ldc_I4_M1);
c.Emit(Bne_Un_S, label);
c.Emit(Ldc_I4, ProjectileID.Beenade);
c.Emit(Ret);

c.MarkLabel(label); // cursor仍然指向ldc.i4 566指令，这个标签给分支指令一个目的地
```

## 结果
完整代码可以在[WaspNest.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Accessories/WaspNest.cs)上探索。
让我们在看应用我们的补丁后观察蜜蜂武器的行为：

https://github.com/tModLoader/tModLoader/assets/4522492/44a205f8-5e32-4330-ab1b-bce7671614ff

提醒一下，[这是它过去的行为](https://github.com/tModLoader/tModLoader/assets/4522492/6ca23d26-ce33-4089-8f92-fd09b1d4487f)。

# 示例 - Lava Frog雕像生成
请参阅[ExampleCritter.cs](https://github.com/tModLoader/tModLoader/blob/1.4.4/ExampleMod/Content/NPCs/ExampleCritter.cs)获取另一个IL编辑补丁示例。这个例子更加棘手，因为我们想要补丁的方法非常大。该示例有很好的注释，并显示了一个更复杂的指令定位示例。

# 访问实例数据
许多模组开发者在制作IL编辑或detour时遇到一个陷阱。陷阱是他们尝试通过直接访问当前类字段来访问IL编辑或detour代码中的实例数据。如果您发现IL编辑或detour中的代码似乎总是看到字段的默认值，这就是问题所在。这不能正常工作，因为IL编辑或detour属于内容的模板实例，而不是被操作的实际实例。

要解决此问题，只需确保您的IL编辑或detour方法是`static`，然后修复代码以访问正确的实例。对于玩家，这将通过`player.GetModPlayer<T>()`方法。对于其他内容，有其他合适的方法。

# 进一步学习
[为其他模组制作IL编辑（专家级）](Patching-Other-Mods-Using-MonoMod)
