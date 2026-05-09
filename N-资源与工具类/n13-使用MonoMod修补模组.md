___
[不熟悉IL编辑？先阅读我们的其他IL编辑指南，点击这里！](Expert-IL-Editing)
___

# 简介
本指南将探讨使用MonoMod修补其他mod代码的方法——这是实现兼容性以及允许您的mod更好地与其他mod交互的非常有用的工具。使用它，您基本上可以完全自由地选择您想要进行的更改。它涉及使用有经验的modder熟悉的技术——`On`和`IL`，我建议在继续使用其他mod之前先用原版尝试，这样您就能掌握它的工作原理。专家IL指南可以在[这里](https://github.com/tModLoader/tModLoader/wiki/Expert-IL-Editing)找到。

# 警告
请注意，tModLoader指南之一规定：**您的mod不会做任何其他被视为非法、不当或有害的事情。
（是的，我们时不时会检查mod的反编译源代码。如果您的代码被混淆以防止轻松检查，如果我们决定这样做，我们可能会禁止您。）这还包括不负责任地使用反射、Cecil或其他阻止和谐mod共存的代码。**这意味着禁止使用本指南中提到的任何技术来故意阻止另一个mod工作，或以其他方式损害它。如果我们发现您这样做，您将被禁止使用创意工坊。也有一些例外，例如故意禁用/更改可能会导致游戏崩溃的机制等。但不要乱来！

# 先决条件

## Mod提取
如果您要进行IL编辑，其先决条件与IL指南（上面链接）中提到的完全相同。但是，由于我们正在编辑其他mod，还需要做更多的事情。

为了对另一个mod进行更改，我们首先需要获取其`.dll`文件，因为如果我们不知道要针对什么，就无法进行更改。最简单的方法是在游戏中提取mod：

<p align="center">
  <img width="50%" height="50%" src="https://i.imgur.com/VKuDY6a.png">
  <img width="50%" height="50%" src="https://i.imgur.com/TVwOPO6.png">
</p>

但是，如果这没有产生任何结果，使用这个[ILSpy](https://github.com/steviegt6/ILSpy)的分支可以打开和提取`.tmod`文件。在ILSpy中反编译`.dll`后，其C#代码可以保存（`文件 > 保存代码`）以供以后查看，IL可以在ILSpy中查看。无论如何，您都需要使用mod的`.dll`文件来执行此操作。

> [!NOTE]
> 在大多数情况下，ILSpy必须严格用于查看目的，因为它所用于的mod的许可证必须得到尊重。如果mod没有可见的许可证，则保留所有权利。


最后，如果您想访问另一个mod中包含的类（主要用于编写`On`钩子），则需要将该mod的`.dll`作为依赖项添加到您的项目中。请注意，这**不等同于**对另一个mod有强引用；它只是允许编译器解析对该mod类的任何引用。

在VS中，您可以通过右键单击解决方案资源管理器中mod的`.csproj`下的`依赖项`，单击`添加项目引用`、`浏览`，然后在文件资源管理器中选择mod的`.dll`来轻松添加依赖项。本指南中的mod将`CalamityMod.dll`添加为引用，以允许解析该mod的类型。

如果您将mod的`.dll`添加为引用，您需要将其添加为强引用或弱引用（请在此处阅读两者：[这里](https://github.com/tModLoader/tModLoader/wiki/Expert-Cross-Mod-Content)）。本指南将添加Calamity（我将用作示例的mod）作为弱引用（`build.txt`中为`weakReferences = CalamityMod`），因为使用`On`应用补丁时需要弱引用\*，对于`IL`，建议这样做以减少所需的反射。

<sup>\* 在任何需要引用目标mod类型的情况下都是必需的。从技术上讲，这并不总是必要的，因为静态方法的参数仅来自非目标的类型，可以被修补而无需引用mod，但这不适用于99%的用例。</sup>

## 防御性设计

您不能总是假设您所针对的mod会被加载，除非您的mod向它提供强引用（即，它在`build.txt`的`modReferences`列表中）。因此，在使用弱引用或根本没有引用时，您的代码必须这样编写：如果目标mod未启用，它不会崩溃。

首先，让我们创建一个`ModSystem`类，负责处理对Calamity的跨mod更改，利用防御性功能：

```cs
using Terraria.ModLoader;

namespace CrossmodTest;

[ExtendsFromMod("CalamityMod")]
public class CalamityCrossmodSystem : ModSystem
{
    public override void Load()
    {
        ApplyOnEdits();
        ApplyILEdits();
    }

    private void ApplyOnEdits()
    {
    }

    private void ApplyILEdits()
    {
    }
}
```
`[ExtendsFromMod("CalamityMod")]`属性用于类上。这可以防止tModLoader自动加载并尝试解析此类中的引用，除非Calamity已启用，这样可以防止如果Calamity未加载时发生[JIT异常](https://github.com/tModLoader/tModLoader/wiki/JIT-Exception)。

如果由于某种原因在类上使用`ExtendsFromMod`不可取，另一个选择是使用`ModLoader.HasMod`有条件地调用应用补丁的方法。使用此替代方法时，这些方法中的每一个都需要用`JITWhenModsEnabled`属性注解。

# 使用`On`
首先，让我们决定进行什么更改。（此处显示的所有Calamity源代码由Calamity Mod团队提供，可在其[公共GitHub](https://github.com/CalamityTeam/CalamityModPublic)上找到）。

在Calamity中，`CalamityUtils.SpawnBossBetter`方法在其代码库的多个地方使用，用于以额外参数生成一些boss：

```cs
	public static NPC SpawnBossBetter(Vector2 relativeSpawnPosition, int bossType, BaseBossSpawnContext spawnContext = null, float ai0 = 0f, float ai1 = 0f, float ai2 = 0f, float ai3 = 0f)
	{
		if (Main.netMode == 1)
		{
			return null;
		}
		if (spawnContext == null)
		{
			spawnContext = new ExactPositionBossSpawnContext();
		}
		Vector2 spawnPosition = spawnContext.DetermineSpawnPosition(relativeSpawnPosition);
		int bossIndex = NPC.NewNPC(NPC.GetBossSpawnSource(Player.FindClosest(spawnPosition, 1, 1)), (int)spawnPosition.X, (int)spawnPosition.Y, bossType, 0, ai0, ai1, ai2, ai3);
		if (Main.npc.IndexInRange(bossIndex))
		{
			BossAwakenMessage(bossIndex);
			return Main.npc[bossIndex];
		}
		return null;
	}
```
调用此方法的情况之一是在生成Supreme Calamitas（SCal）时：
```cs
...
NPC scal = CalamityUtils.SpawnBossBetter(spawnPosition, ModContent.NPCType<SupremeCalamitas>());
...
```

我们的目标将是在生成时用另一个NPC替换SCal；这个例子将使用一个地精。因此，这是`On`钩子的一个好用例，因为我们想要更改方法的返回值并用自己的功能替换它。

在尝试修补另一个mod时，您可能首先注意到的一件事是，与针对原版的补丁不同，没有提供应用补丁的事件的`On_X`或`IL_X`类。这是因为这些是由依赖项`MMHook_Terraria.dll`预先生成和提供的。此外，如果您熟悉`On`的典型工作方式，这可能看起来是一个障碍，因为没有`orig_X`委托可以用作钩子中`orig`参数。

为了解决这个问题，您首先需要声明一个与目标方法具有相同签名的委托。这将用作您的`orig`委托，在钩子中调用时调用原始方法：
```cs
private delegate NPC orig_SpawnBossBetter(Vector2 relativeSpawnPosition, int bossType, BaseBossSpawnContext spawnContext = null, float ai0 = 0f, float ai1 = 0f, float ai2 = 0f, float ai3 = 0f);
```
接下来，我们实际上可以应用钩子。由于没有预先生成的`On_X`方法可用，必须使用`MonoModHooks.Add`手动应用。这个tModLoader方法包装了MonoMod的`new Hook()`功能以应用`On`钩子。如果需要，可以使用`MonoModHooks.Remove`禁用钩子，但这通常是不必要的，因为tModLoader在卸载时自动删除所有钩子。

要应用它，我们需要获取目标方法的`MethodInfo`，并声明另一个方法作为钩子。这个方法应该具有与目标相同的签名，但将`orig_SpawnBossBetter`委托作为额外的第一个参数：
```cs
    private void ApplyOnEdits()
    {
        // 首先，获取要应用On补丁的方法的MethodInfo。
        MethodInfo targetMethod = typeof(CalamityUtils).GetMethod("SpawnBossBetter", BindingFlags.Static | BindingFlags.Public);

        // 使用目标方法和补丁方法调用MonoModHooks.Add。
        MonoModHooks.Add(targetMethod, OnSpawnBossBetter);
    }

    private NPC OnSpawnBossBetter(orig_SpawnBossBetter orig, Vector2 relativeSpawnPosition, int bossType, BaseBossSpawnContext spawnContext = null, float ai0 = 0f, float ai1 = 0f, float ai2 = 0f, float ai3 = 0f)
    {
    }
```
目前，这不会编译，因为`OnSpawnBossBetter`需要返回一个NPC实例（根据之前的源代码，这是该方法生成的boss实例）。这意味着我们应该填充该方法。由于我们只想针对SCal，如果`bossType`参数不是SCal的NPC ID，我们需要恢复到方法的默认行为：
```cs
    private NPC OnSpawnBossBetter(orig_SpawnBossBetter orig, Vector2 relativeSpawnPosition, int bossType, BaseBossSpawnContext spawnContext = null, float ai0 = 0f, float ai1 = 0f, float ai2 = 0f, float ai3 = 0f)
    {
        // 如果生成的boss不是Supreme Calamitas，则运行原始行为。
        if (bossType != ModContent.NPCType<SupremeCalamitas>())
        {
            return orig(relativeSpawnPosition, bossType, spawnContext, ai0, ai1, ai2, ai3);
        }
    }
```
此块之后的任何代码将仅适用于SCal。为了模仿原始方法的功能，我们可以调整原始源代码中的一些位置计算和`Netmode`检查代码：
```cs
    private NPC OnSpawnBossBetter(orig_SpawnBossBetter orig, Vector2 relativeSpawnPosition, int bossType, BaseBossSpawnContext spawnContext = null, float ai0 = 0f, float ai1 = 0f, float ai2 = 0f, float ai3 = 0f)
    {
        // 如果生成的boss不是Supreme Calamitas，则运行原始行为。
        if (bossType != ModContent.NPCType<SupremeCalamitas>())
        {
            return orig(relativeSpawnPosition, bossType, spawnContext, ai0, ai1, ai2, ai3);
        }

        // 此代码改编自原始源代码，保持功能并实例化正确的实体源。
        if (Main.netMode == NetmodeID.MultiplayerClient)
        {
            return null;
        }

        spawnContext ??= new ExactPositionBossSpawnContext();

        Vector2 spawnPosition = spawnContext.DetermineSpawnPosition(relativeSpawnPosition);
        IEntitySource source = NPC.GetBossSpawnSource(Player.FindClosest(spawnPosition, 1, 1));
    }
```
最后，我们可以添加一个生成新NPC并返回其实例的方法。`NPC.NewNPC`的简写版本是`NewNPCDirect`：
```cs
    private NPC OnSpawnBossBetter(orig_SpawnBossBetter orig, Vector2 relativeSpawnPosition, int bossType, BaseBossSpawnContext spawnContext = null, float ai0 = 0f, float ai1 = 0f, float ai2 = 0f, float ai3 = 0f)
    {
        // 如果生成的boss不是Supreme Calamitas，则运行原始行为。
        if (bossType != ModContent.NPCType<SupremeCalamitas>())
        {
            return orig(relativeSpawnPosition, bossType, spawnContext, ai0, ai1, ai2, ai3);
        }

        // 此代码改编自原始源代码，保持功能并实例化正确的实体源。
        if (Main.netMode == NetmodeID.MultiplayerClient)
        {
            return null;
        }

        spawnContext ??= new ExactPositionBossSpawnContext();

        Vector2 spawnPosition = spawnContext.DetermineSpawnPosition(relativeSpawnPosition);
        IEntitySource source = NPC.GetBossSpawnSource(Player.FindClosest(spawnPosition, 1, 1));

        // 在Supreme Calamitas会生成的地方，生成一个地精。
        NPC npc = NPC.NewNPCDirect(source, spawnPosition, NPCID.Gnome);

        return npc;
    }
```
此代码现在将编译，当我们通过mod的生成方法召唤SCal时：

<p align="center">
  <img width="50%" height="50%" src="https://media3.giphy.com/media/i5loB6UGovmm7vqKIN/giphy.gif" alt="Description">
</p>

> [!NOTE]
> 使用`On`最常见的陷阱是忘记调用`orig`委托，这会导致在无意做此事时丢失功能。除非您想阻止基方法运行，否则请务必调用`orig`！

# 使用`IL`

本指南假设您已经知道`IL`编辑如何工作，如果您不知道，建议先阅读主要[IL指南](https://github.com/tModLoader/tModLoader/wiki/Expert-IL-Editing)。在代码方面，`IL`编辑比`On`需要更少的样板，因为您不需要委托，甚至不需要弱引用——只需要一个表示目标方法的`MethodInfo`。

再一次，让我们选择一个我们想要进行IL编辑的方法。再看SCal，我们将更改她在生成时在聊天中显示的文本。这发生在她的`ModNPC`类的`AI`方法中，它有超过一千行长。这使得使用`On`变得不可行，因为替换整个方法的功能是不合理的，所以我们需要使用`IL`进行更细粒度的编辑。

将IL和C#代码关联的最简单方法是使用在IL和C#上叠加的ILSpy模式。让我们在ILSpy中导航到AI代码中此消息被发送的区域，位于`CalamityMod.NPCs.SupremeCalamitas.SupremeCalamitas.AI`：

<p align="center">
  <img width="50%" height="50%" src="https://i.imgur.com/d7fzRrw.png">
</p>
相应的源代码片段如下所示：

```cs
		if (!startText)
		{
			if (!bossRush)
			{
				string key = "Mods.CalamityMod.Status.Boss.SCalSummonText";
				if (cirrus)
				{
					key = "Mods.CalamityMod.Status.Boss.CirrusSummonText";
				}
				else if (DownedBossSystem.downedCalamitas)
				{
					key += "Rematch";
				}
				CalamityUtils.DisplayLocalizedText(key, cirrus ? cirrusTextColor : textColor);
			}
			startText = true;
		}
```
现在我们有了目标方法，需要设置一对方法，使用`Add`的IL对应物（称为`MonoModHooks.Modify`）来应用该编辑。编辑本身的签名与任何其他IL编辑相同：
```cs
    private void ApplyILEdits()
    {
        // 首先，获取要应用IL补丁的方法的MethodInfo。
        MethodInfo targetMethod = typeof(SupremeCalamitas).GetMethod("AI", BindingFlags.Instance | BindingFlags.Public);

        // 使用目标方法和补丁方法调用MonoModHooks.Modify。
        MonoModHooks.Modify(targetMethod, ILSupremeCalamitasAI);
    }

    private void ILSupremeCalamitasAI(ILContext il)
    {
        ILCursor c = new(il);
    }
```
在这种情况下编辑文本最简单的方法是更改之前`key`的值，我们将在其首次被推送到堆栈后替换其值。第一步是通过将其值匹配到`Ldstr`来导航到该字符串被加载到堆栈上的指令：
```cs
    private void ILSupremeCalamitasAI(ILContext il)
    {
        ILCursor c = new(il);

        if (!c.TryGotoNext(MoveType.After, i => i.MatchLdstr("Mods.CalamityMod.Status.Boss.SCalSummonText")))
            throw new Exception("IL编辑失败！");
    }
```
如果您之前没有见过此模式，如果找不到匹配操作数的指令，则抛出异常。或者，您可以在这里`return`而不是抛出，这将防止编辑安全地应用，而不是阻止mod加载。`MoveType.After`将光标放在目标指令之后，这样我们就可以插入我们自己的指令，避免需要删除指令（这是不鼓励的，因为它可能扰乱其他编辑）。

下一步是将新值插入堆栈。由于我们不能在堆栈上留下未使用的值，我们首先必须`Pop`现有值：
```cs
    private void ILSupremeCalamitasAI(ILContext il)
    {
        ILCursor c = new(il);

        if (!c.TryGotoNext(MoveType.After, i => i.MatchLdstr("Mods.CalamityMod.Status.Boss.SCalSummonText")))
            throw new Exception("IL编辑失败！");

        c.Emit(OpCodes.Pop);
    }
```
这将允许我们使用`Ldstr`将新值推送到堆栈上。由于字符串本身是一个本地化键——`"Mods.CalamityMod.Status.Boss.SCalSummonText"`——新字符串在这种情况下也必须是本地化键。为简单起见，而不是向mod添加新的，我将使用Calamity中的另一个本地化键——`"Mods.CalamityMod.Status.Boss.CryogenBossText"`。这可以在公共GitHub的本地化文件中找到。让我们把它推到堆栈上：
```cs
    private void ILSupremeCalamitasAI(ILContext il)
    {
        ILCursor c = new(il);

        if (!c.TryGotoNext(MoveType.After, i => i.MatchLdstr("")))
            throw new Exception("IL编辑失败！");

        c.Emit(OpCodes.Pop);
        c.Emit(OpCodes.Ldstr, "Mods.CalamityMod.Status.Boss.CryogenBossText");
    }
```
最后，当在游戏中召唤SCal时（禁用地精编辑），消息按预期更改（之前与之后）：
<p align="center">
  <img width="50%" height="50%" src="https://i.imgur.com/3a1O0Ax.png">
  <img width="50%" height="50%" src="https://i.imgur.com/7qnTW1X.png">
</p>

两个测试编辑的完整源代码如下：

```cs
using CalamityMod;
using CalamityMod.DataStructures;
using Microsoft.Xna.Framework;
using System.Reflection;
using Terraria;
using Terraria.DataStructures;
using Terraria.ModLoader;
using Terraria.ID;
using CalamityMod.NPCs.SupremeCalamitas;
using MonoMod.Cil;
using System;
using Mono.Cecil.Cil;

namespace CrossmodTest;

[ExtendsFromMod("CalamityMod")]
public class CalamityCrossmodSystem : ModSystem
{
    public override void Load()
    {
        ApplyOnEdits();
        ApplyILEdits();
    }

    private delegate NPC orig_SpawnBossBetter(Vector2 relativeSpawnPosition, int bossType, BaseBossSpawnContext spawnContext = null, float ai0 = 0f, float ai1 = 0f, float ai2 = 0f, float ai3 = 0f);

    private void ApplyOnEdits()
    {
        // 首先，获取要应用On补丁的方法的MethodInfo。
        MethodInfo targetMethod = typeof(CalamityUtils).GetMethod("SpawnBossBetter", BindingFlags.Static | BindingFlags.Public);

        // 使用目标方法和补丁方法调用MonoModHooks.Add。
        MonoModHooks.Add(targetMethod, OnSpawnBossBetter);
    }

    private NPC OnSpawnBossBetter(orig_SpawnBossBetter orig, Vector2 relativeSpawnPosition, int bossType, BaseBossSpawnContext spawnContext = null, float ai0 = 0f, float ai1 = 0f, float ai2 = 0f, float ai3 = 0f)
    {
        // 如果生成的boss不是Supreme Calamitas，则运行原始行为。
        if (bossType != ModContent.NPCType<SupremeCalamitas>())
        {
            return orig(relativeSpawnPosition, bossType, spawnContext, ai0, ai1, ai2, ai3);
        }

        // 此代码改编自原始源代码，保持功能并实例化正确的实体源。
        if (Main.netMode == NetmodeID.MultiplayerClient)
        {
            return null;
        }

        spawnContext ??= new ExactPositionBossSpawnContext();

        Vector2 spawnPosition = spawnContext.DetermineSpawnPosition(relativeSpawnPosition);
        IEntitySource source = NPC.GetBossSpawnSource(Player.FindClosest(spawnPosition, 1, 1));

        // 在Supreme Calamitas会生成的地方，生成一个地精。
        NPC npc = NPC.NewNPCDirect(source, spawnPosition, NPCID.Gnome);

        return npc;
    }

    private void ApplyILEdits()
    {
        // 首先，获取要应用IL补丁的方法的MethodInfo。
        MethodInfo targetMethod = typeof(SupremeCalamitas).GetMethod("AI", BindingFlags.Instance | BindingFlags.Public);

        // 使用目标方法和补丁方法调用MonoModHooks.Modify。
        MonoModHooks.Modify(targetMethod, ILSupremeCalamitasAI);
    }

    private void ILSupremeCalamitasAI(ILContext il)
    {
        ILCursor c = new(il);

        if (!c.TryGotoNext(MoveType.After, i => i.MatchLdstr("Mods.CalamityMod.Status.Boss.SCalSummonText")))
            throw new Exception("IL编辑失败！");

        c.Emit(OpCodes.Pop);
        c.Emit(OpCodes.Ldstr, "Mods.CalamityMod.Status.Boss.CryogenBossText");
    }
}
```
