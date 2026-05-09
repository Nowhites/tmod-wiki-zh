# 原版代码适配
本指南教如何导航泰拉瑞亚源代码的丛林，找到或适配代码。为什么？也许你想自定义一个aiStyle，也许你想知道Magma Stone是如何工作的，或者你想模仿一个原版效果。

通过这些示例学习后，你将学到足够多的知识，能够想出如何适配和重新实现大多数其他现有内容。

## tl;dr
* 将`this.X`代码更改为对应的对象，如`NPC.X`或`Player.X`
* 如果你手动复制aiStyles，你可以删除与其他类型相关的所有代码，使代码更易读
  * 例如，弹射物AiStyle 1有3500行代码，但可以精简到大约5行代码，参见[基础弹射物 - AiStyle 1](https://github.com/tModLoader/tModLoader/wiki/Basic-Projectile#aistyle-1)
* 使用F2重命名自动生成的变量名（`num233`、`flag83`等），当你弄清楚每个变量代表什么时
* 由于泰拉瑞亚更新了反编译的工作方式，本指南中显示的`num###`变量可能与你源代码中看到的值不匹配

## 获取原版源代码
发布反编译的源代码是违法的，但任何拥有合格电脑的人都可以轻松反编译他们的个人 Terraria.exe 副本。请注意，你可能希望最终得到反编译的tModLoader而不是反编译的原版泰拉瑞亚，这样你才能知道tModLoader钩子在哪里。参见[高级前置条件](https://github.com/tModLoader/tModLoader/wiki/Advanced-Prerequisites)获取如何反编译tModLoader的更多信息。对于只是适配目的，通过"简单方式"可能更容易，因为它是`this.allCrit += 3;`而不是`allCrit += 3;`，帮助将`this`和`base`批量替换为`Player`（tModLoader设置不会那样做）。

获得源代码副本后，打开Visual Studio中的`Terraria.csproj`文件，这样你就可以轻松搜索整个项目。你可以通过从文件资源管理器打开`Terraria.csproj`文件，或者打开Visual Studio并使用`File->Open->Project/Solution`选择该文件。

## Visual Studio代码说明

<details><summary>展开Visual Studio代码说明</summary><blockquote>

本指南期望用户使用Visual Studio，而不是Visual Studio Code。本节列出与使用Visual Studio Code相关的替代说明。将Visual Studio说明转换为你可以在Visual Studio Code中使用的说明。

### 打开Terraria.csproj
要打开项目，你需要使用`File->Open Folder...`然后选择Terraria文件夹本身。

### 搜索整个项目
搜索整个项目的选项与普通查找工具（`Ctrl-F`）分开。要搜索整个项目，按`Ctrl-Shift-F`调出`Find in Files`工具。"Match whole word"切换是一个按钮在搜索栏上。结果将列在这个侧面板中，而不是底部面板中。

![image](https://github.com/user-attachments/assets/f61ece2a-b280-4eee-87e7-c1b40e936eef)

点击搜索结果时，方法名和类名列在编辑器面板顶部：

![image](https://github.com/user-attachments/assets/bec8d0fa-74f1-48d2-8c84-6deacbe178fc)

### 查找所有引用
`Find All References`选项仍然在右键菜单中找到，但键盘快捷键是`Shift-Alt-F12`而不是。结果将在左侧面板中，而不是下方。

![image](https://github.com/user-attachments/assets/55c2d8b1-5eab-4b84-8e61-cf655029cc19)

### 转到定义
这与Visual Studio中相同。按F12或按住Ctrl并点击以导航到成员的定义。

</blockquote></details>

## 示例：护甲效果：忍者衫
一个调查原版代码的简单例子是弄清楚原版护甲或配饰如何实现它们的特殊效果。在这个例子中，我们想知道忍者衫如何实现"暴击率提高3%"。（跟随你感兴趣的内容。）第一步是找到`ItemID`。搜索`ItemID.cs`，你会找到`NinjaShirt`的定义位置：

![](http://i.imgur.com/fxnOPXA.png)

现在我们必须在整个项目中搜索"257"，使用快速查找工具（`Ctrl-F`，不要将"Search Solution Explorer"误认为是搜索工具，那是不一样的）。确保在这里设置"Match whole word"来限制结果，然后选择"Current Project"搜索所有文件，使用"Find All"按钮找到所有结果。
![image](https://github.com/user-attachments/assets/9630d54d-232a-4f0f-9b19-d8ff2c762ebe)
在结果中，你会看到很多对"257"的引用，你现在必须将结果精简到相关结果。点击搜索结果会导航到该文件中的那一行。当前选中代码行的方法名列在右上角。

![image](https://github.com/user-attachments/assets/555ef432-ea20-4598-b522-a97f9ff1675b)

你可能在`Item.SetDefaults1`中找到一个结果，设置结果默认值，但这段代码不负责投掷伤害奖励，所以我们必须继续搜索。继续查看结果列表，你可以跳过`NPC.cs`中的结果，因为那些可能与`NPCID`（等于257）相关，而不是ItemID的257。最后，你会看到`Player.cs`中的结果，你的屏幕会显示这个：

![image](https://github.com/user-attachments/assets/bfa29eb1-8e71-431b-a55c-91339df7e2f3)

找到了！这看起来是对的。现在我们想把这段代码`this.allCrit += 3;`引入到我们的`ModItem`代码中。首先，在你的`ModItem`类中override `UpdateEquip`钩子。接下来，粘贴代码进去，你会发现你的IDE对你刚刚粘贴的代码有意见。这段代码有"this"，它指的是Player实例。我们的代码在`ModItem`中，而不是`Player`中，所以试图使用"this"并修改`allCrit`字段的值是行不通的，因为`ModItem`没有`allCrit`字段。幸运的是，`UpdateEquip`钩子传递了一个`Player`对象，所以我们把代码改成`player.allCrit += 3;`。然而，仍然有一个错误。错误消息说"`Player.allCrit`由于其保护级别不可访问"。这意味着我们试图使用的代码对模组制作者是隐藏的。当tModLoader被改变以使泰拉瑞亚对模组更灵活和兼容时，这种情况有时会发生。如果你熟悉在tModLoader模组中伤害类通常是如何做的，你会知道要把这行改成`player.GetCritChance<GenericDamageClass>() += 3;`来修复问题。在大多数情况下你不会遇到这样的问题，简单地替换`this`为`player`就会修复问题。

## 示例：配饰效果：岩浆石

另一个例子。搜索`ItemID.cs`找到`MagmaStone = 1322;`。现在搜索"1322"，你会在`Player.UpdateAccessory`中找到`this.magmaStone = true;`的结果。（值得注意的是，这段代码是在Player对象中设置一个bool字段，这和我们模组制作者在`ModPlayer`中做的事情完全一样。）现在我们必须调查这个"magmaStone"变量。我们不是使用查找菜单搜索代码，这次我们将使用Visual Studio的`Find All References`功能。`Shift-F12`是键盘快捷键：

![image](https://github.com/user-attachments/assets/fb5720f4-f6f5-4d57-a347-84f6a28d67fb)

现在我们搜索9个结果，找出我们想知道的内容。2个结果是将bool设置为true，对应于`ModItem.UpdateAccessory`。1个结果是将`magmaStone` bool设置为false，这类似于`ModPlayer.ResetEffects`。`Player.StatusNPC`和`Player.StatusPvP`中的结果似乎是给目标应用一个buff。查看24在`BuffID`中的值，buff是`OnFire`。这些结果对应于ModPlayer.ModifyHitPvp和ModPlayer.ModifyHitNPC。第6个结果在Player.ItemCheck中，看起来是生成一些尘埃。我们知道当武器挥动时会产生火粒子，所以这段代码一定与此相关。我们如何自己模仿这种行为？我们需要搜索附近代码并寻找tModLoader钩子。tModLoader钩子有`PlayerHooks.Something`或`ItemLoader.Something`这样的代码，让我们搜索它。幸运的是，这个结果就在钩子旁边！

![](http://i.imgur.com/UyxK0l6.png)

现在让我们看看[文档](https://docs.tmodloader.net/docs/stable/class_mod_player.html#a94faf1f54d833cf14b17ee9c127cc547)！正如你所看到的，文档也同意，这是要使用的钩子。

最后3个结果在`Projectile.cs`中。它们做与早期结果相同的事情，但针对的是近战弹射物而不是近战物品。（尘埃、击中NPC、击中PvP）我们用来复制这种行为的相应钩子在：`ModPlayer.ModifyHitNPCWithProj`、`ModPlayer.ModifyHitPvpWithProj`和`GlobalProjectile.PostAI`。

记住要将有用的钩子中的代码适配到你所在的上下文。将"this"替换为正确的对象变量，将"Main.npc[i]"替换为"target"，等等。

## 示例：弹射物或NPC AI代码
本指南教如何适配原版AI代码用于新用途。在初级编码中，我们有时对我们ModProjectile或ModNPC使用原版aiStyle。在中级编码中，我们通过设置`aiType`来模仿aiStyle的具体细节，甚至使用`PostAI`或`PreAI`来为我们的特定内容添加额外代码到原版aiStyle。但这并不总是足够的，所以在这个教程中，我们将学习如何调查原版源代码，找到我们需要的信息或代码片段，以完全自定义我们Mod中的内容。

## 示例：物品和弹射物：Shadowbeam Staff克隆
Shadowbeam是一个很酷的武器，发射一个能瞬间弹跳多次的弹射物。让我们制作一个弱化版的Shadowbeam Staff，然后尝试自定义弹射物的外观。

### 弱化版Shadowbeam Staff克隆
我们旅程的第一步是克隆原版物品。这在ExampleMod中出现过几次，但下面是代码。

```cs
using Microsoft.Xna.Framework;
using Terraria.ID;
using Terraria.ModLoader;

namespace ExampleMod.Items
{
	class ShadowbeamStaffClone : ModItem
	{
		public override string Texture => "Terraria/Images/Item_" + ItemID.ShadowbeamStaff;

		public override void SetDefaults()
		{
			Item.CloneDefaults(ItemID.ShadowbeamStaff);
			Item.color = Color.Purple; // 这只是调整库存中的武器精灵图，只是为了知道哪个ShadowBeam Staff是我们的而不是原版的
		}
	}
}
```

![](https://i.imgur.com/ADBy27n.png)
嗯，有什么不对，杖的握持方式不对。让我们在ItemID.cs中找到`ItemID.ShadowbeamStaff`的值。我们发现是"1444"。让我们在源代码中搜索"1444"。
![image](https://github.com/user-attachments/assets/3fffc14c-728b-48fc-b4d3-2a4f6aabc37b)
在这些匹配结果中，我们看到一个NPC战利品掉落、前缀赋值、`SetDefaults`、`ItemID`，还有2个我们以前没见过结果。其中`AchievementInitializer`的那个看起来与成就相关，所以我们看最后一个结果。这是一个switch语句。我们沿着switch语句的case往下看，直到找到代码。看起来我们忘记设置`Item.staff`为true了。如果你研究过ExampleMod的`ExampleStaff`，你就已经知道了。让我们把它添加到代码中在`SetStaticDefaults`中，并借此机会降低武器伤害：

```cs
using Microsoft.Xna.Framework;
using Terraria;
using Terraria.ID;
using Terraria.ModLoader;

namespace ExampleMod.Items
{
	class ShadowbeamStaffClone : ModItem
	{
		public override string Texture => "Terraria/Images/Item_" + ItemID.ShadowbeamStaff;

		public override void SetStaticDefaults()
		{
			Item.staff[Type] = true;
		}

		public override void SetDefaults()
		{
			Item.CloneDefaults(ItemID.ShadowbeamStaff);
			Item.color = Color.Purple; // 这只是调整库存中的武器精灵图，只是为了知道哪个ShadowBeam Staff是我们的而不是原版的。去掉这行一旦你有你自己的精灵图/纹理
			Item.damage = 8; // 从53降低
		}
	}
}
```

现在我们有一个可以正常工作的Shadowbeam Staff克隆，但威力较弱。随意调整`Item.mana`和其他你喜欢的字段。

### Shadowbeam弹射物AI
现在我们让物品工作了，你可能认为修改颜色或弹跳次数会很简单，但并非如此。这一切都硬编码在Shadowbeam弹射物的AI中，所以不幸的是我们必须制作一个新的`ModProjectile`并编辑原版AI代码的副本。首先让我们调查Shadowbeam staff弹射物的紫色来自哪里。首先，让我们看看Shadowbeam Staff物品的`Item.shoot`值。在`Item.SetDefaults`中，我们看到`this.shoot = 294;`。很好，让我们在ProjectileID.cs中找到它：
![](https://i.imgur.com/wZJOeIg.png)
啊，注意也有一个`ShadowBeamHostile`。名字中有Hostile的ProjectileID通常指由敌对NPC射击的弹射物，要注意这一点。让我们在源代码中搜索"294"。大多数45个结果都是指ID为45的物品和瓦片，但Projectile.cs中的4个结果看起来很有希望。第一个结果显示`Projectile.SetDefaults`。值得注意的是`this.extraUpdates = 100;`，那会是什么？剧透一下，这就是弹跳效果的工作原理。它让AI代码每帧执行100次而不是正常的一次。没有它，瞬间弹跳效果就不容易实现。值得注意的是`this.aiStyle = 48`，我们稍后需要找到这个的代码。
![](https://i.imgur.com/kYPxxnI.png)
第二个结果在`Projectile.Damage`方法中。如果你记得，Shadowbeam Staff在穿透敌人时会失去伤害，这一定与那个效果有关。希望你反编译的是tModLoader而不是Terraria，因为你能够找到`ModifyHitNPC`钩子在代码附近被调用。稍后我们需要将这段代码适配到我们制作的替换`ModProjectile`中。
![](https://i.imgur.com/J8cVwsp.png)
第3个结果在`HandleMovement`中。有点难找，但相关的tModLoader钩子是`OnTileCollide`。
第4个结果在`Projectile.AI`中，这是我们需要适配的代码。下面你可以看到Visual Studio的一个特性在运行。将鼠标悬停在guidelines上，你会看到导致这段代码的嵌套条件列表。值得注意的是，我们看到这段代码在一个检查`this.aiStyle == 48`的if语句中。
![](https://i.imgur.com/ml8sPMu.png)
现在我们找到了所有相关代码，现在我们需要调查颜色是如何决定的。这可能是一个好时机检查其他什么弹射物使用弹射物aiStyle 48。搜索`this.aiStyle = 48`发现HeatRay、UFOLaser、MagnetSphereBolt和ShadowBeamHostile也都使用aiStyle 48。我们知道HeatRay是黄色的，但Shadowbeam Staff是紫色的。知道这一点，我们知道颜色的代码一定在AI代码中的一个if语句中。回到第4个结果，AI方法中的结果，我们寻找任何看起来与颜色相关的代码。啊哈！我们找到代码`int num448 = Dust.NewDust(vector33, 1, 1, 173, 0f, 0f, 0, default(Color), 1f);`并在[dust spritesheet](https://github.com/tModLoader/tModLoader/wiki/Basic-Dust)中查找173号尘埃：
![](https://i.imgur.com/FLhIi1G.png)
是的，我们弄清楚了为什么Shadowbeam Staff弹射物是紫色的，因为尘埃173是在AI中生成的。让我们记住将这个改为生成尘埃178，一个青绿色的弹射物，为我们修改后的克隆。如果你查看aiStyle 48的其余代码，你可能会注意到没有限制弹跳次数。通过一些试验和错误，你会发现`Projectile.extraUpates`和`Projectile.timeLeft`负责这件事。`timeLeft`为300通常会停留5秒，但由于`extraUpdates`是100，它实际上只存活3个tick/帧。让我们为我们的弱化克隆改变`timeLeft`为200，这将缩短我们武器的射程。

现在我们已经在源代码中找到了所有相关部分，让我们制作一个原版ShadowBeamFriendly弹射物的`ModProjectile`克隆。让我们首先也记住在`ModItem`中添加`Item.shoot = ModContent.ProjectileType<ShadowbeamStaffCloneProjectile>();`，这样我们的武器就不会射击原版弹射物了。我们还需要在`SetDefaults`中将`Projectile.magic = true`改为`Projectile.DamageType = DamageClass.Magic;`。接下来，我们复制我们在AI方法中找到的AI代码到我们自己的AI方法中，并删除任何我们不感兴趣的其他弹射物类型相关的代码。还要记住删除原来检查我们感兴趣的弹射物类型的if语句，但保留该if语句块内的代码。记住，在适配原版代码时，要确保将`this.`和`base.`东西改成正确的东西，在这种情况下是`projectile.`。还要记住不要复制`this.aiStlye = 48`，因为我们将复制和编辑原版AI代码而不是。此外，使用Visual Studio的F2按钮重命名像num447这样的东西。下面是我们考虑所有原版代码后的完整代码：

原版：
![](https://i.imgur.com/2ZMWHOg.png)
我们的新弹射物：
![](https://i.imgur.com/dyawndQ.png)

```cs
using Microsoft.Xna.Framework;
using Terraria;
using Terraria.ID;
using Terraria.ModLoader;

namespace ExampleMod.Items
{
	class ShadowbeamStaffClone : ModItem
	{
		// 一旦你有了自己的精灵图/纹理，删除这个和SetDefaults中的Item.color行。
		public override string Texture => "Terraria/Images/Item_" + ItemID.ShadowbeamStaff;

		public override void SetStaticDefaults() {
			Item.staff[Type] = true;
		}

		public override void SetDefaults() {
			Item.CloneDefaults(ItemID.ShadowbeamStaff);
			Item.color = Color.Purple; // 这只是调整库存中的武器精灵图，只是为了知道哪个ShadowBeam Staff是我们的而不是原版的。一旦你有了你自己的精灵图/纹理，删除这行
			Item.damage = 8; // 从53降低
							 // 非常重要！
			Item.shoot = ModContent.ProjectileType<ShadowbeamStaffCloneProjectile>();
		}
	}

	class ShadowbeamStaffCloneProjectile : ModProjectile
	{
		public override void SetDefaults() {
			Projectile.width = 4;
			Projectile.height = 4;
			// 不！Projectile.aiStyle = 48;
			Projectile.friendly = true;
			Projectile.DamageType = DamageClass.Magic;
			Projectile.extraUpdates = 100;
			Projectile.timeLeft = 200; // 从300降低
			Projectile.penetrate = -1;
		}

		// 注意，这个纹理实际上只是一个空白纹理，供参考。
		public override string Texture => "Terraria/Images/Projectile_" + ProjectileID.ShadowBeamFriendly;

		public override bool OnTileCollide(Vector2 oldVelocity) {
			if (Projectile.velocity.X != oldVelocity.X) {
				Projectile.position.X = Projectile.position.X + Projectile.velocity.X;
				Projectile.velocity.X = -oldVelocity.X;
			}
			if (Projectile.velocity.Y != oldVelocity.Y) {
				Projectile.position.Y = Projectile.position.Y + Projectile.velocity.Y;
				Projectile.velocity.Y = -oldVelocity.Y;
			}
			return false; // 返回false因为我们正在处理碰撞
		}

		public override void ModifyHitNPC(NPC target, ref NPC.HitModifiers modifiers) {
			Projectile.damage = (int)(Projectile.damage * 0.8);
		}

		public override void AI() {
			Projectile.localAI[0] += 1f;
			if (Projectile.localAI[0] > 9f) {
				for (int i = 0; i < 4; i++) {
					Vector2 projectilePosition = Projectile.position;
					projectilePosition -= Projectile.velocity * ((float)i * 0.25f);
					Projectile.alpha = 255;
					// 重要，已将173改为178！
					int dust = Dust.NewDust(projectilePosition, 1, 1, 178, 0f, 0f, 0, default(Color), 1f);
					Main.dust[dust].noGravity = true;
					Main.dust[dust].position = projectilePosition;
					Main.dust[dust].scale = (float)Main.rand.Next(70, 110) * 0.013f;
					Main.dust[dust].velocity *= 0.2f;
				}
			}
		}
	}
}
```

如果你一直跟随而不仅仅是复制上面的代码，你会注意到尘埃178默认为受重力影响。添加`Main.dust[dust].noGravity = true;`可以轻松解决这个问题。

好吧，我们做到了，我们调查并克隆了一个原版弹射物AI。我们还做一些非常基本的编辑来改变轨迹的颜色。我们可以对其他弹射物做同样的事情，搜索源代码，识别我们希望自定义的代码，然后复制并编辑原版代码到一个新的`ModProjectile`中。

## 示例：NPC：带修改弹射物的NPC克隆（Hoplite）

### 精确克隆
首先，让我们像Party Zombie中看到的那样制作一个NPC的基本克隆。让我们克隆Hoplite。经过一些搜索，我们发现`GreekSkeleton = 481;`在NPCID中，并将其用于我们的基本克隆。为简单起见，我将使用`=>`功能（阅读[关于= >](https://docs.microsoft.com/en-us/magazine/dn802602.aspx)）

以下是这个代码：

```cs
using Terraria;
using Terraria.ID;
using Terraria.ModLoader;

namespace ExampleMod.NPCs
{
	class SuperHoplite : ModNPC
	{
		public override string Texture => "Terraria/Images/NPC_" + NPCID.GreekSkeleton;

		public override void SetStaticDefaults() {
			Main.npcFrameCount[Type] = Main.npcFrameCount[NPCID.GreekSkeleton];
		}

		public override void SetDefaults() {
			NPC.CloneDefaults(NPCID.GreekSkeleton);
			AIType = NPCID.GreekSkeleton;
			AnimationType = NPCID.GreekSkeleton;
		}
	}
}
```

进入游戏，然后使用带有NPC生成器的模组如[Cheat Sheet](https://steamcommunity.com/sharedfiles/filedetails/?id=2563784437)或[HERO's Mod](https://steamcommunity.com/sharedfiles/filedetails/?id=2564645933)生成你的NPC。你会注意到精灵图和行为与原版相同。（稍后随意以正常方式更改精灵图。）在地下，你会注意到我们的Hoplite克隆像往常一样用标枪攻击。教程的其余部分的目标是改变那个弹射物和其他行为。

### 修改的准备工作

不幸的是，没有简单的方法来改变我们的克隆射击的弹射物。我们的克隆，正如我们在`SetDefaults`方法中看到的那样，只是克隆了原版AI代码。原版AI代码中硬编码了弹射物我们的克隆将生成来攻击玩家。第一件事我们需要的是[反编译的泰拉瑞亚源代码](https://github.com/tModLoader/tModLoader/wiki/Advanced-Prerequisites#tmodloader-source-code)。一旦你有了，现在我们必须找到要复制的AI代码。找到`NPCID.GreekSkeleton`并看到它的值是"481"，现在搜索"481"。第一个找到结果我们需要定位在`SetDefaults`方法中的结果。一旦我们找到它，我们就可以找出哪个`aiStyle` GreekSkeleton/Hoplite正在使用：
![](https://i.imgur.com/mcDqzru.png)
很好，我们现在知道搜索源代码中的`aiStyle == 3`：
![](https://i.imgur.com/kGTeWrw.png)
现在按照代码进入`AI_003_Fighters`方法（通过点击`AI_003_Fighters`并按键盘上的`F12`或按住`Ctrl`并点击`AI_003_Fighters`。这是`转到实现`功能）并复制所有代码并粘贴到我们自己`ModNPC`类中的一个AI方法中。（小心不要弄乱`{ }`对）我们还将在`SetDefaults`中设置`NPC.aiStyle = -1;`（因为`CloneDefaults`方法会将其设置为3）并删除`AIType = NPCID.GreekSkeleton;`，因为我们不希望我们的AI代码副本和原版代码都运行。一旦我们将代码复制到我们的AI方法中，我们需要做通常的清理：将`base.`和`this.`改为`NPC.`：

之前：
![](https://i.imgur.com/FGLIGWO.png)

之后：
![](https://i.imgur.com/hd3iXFx.png)

查找和替换：
![](https://i.imgur.com/ekINkM2.png)
![](https://i.imgur.com/0f9wFM3.png)

你可能会注意到代码有很多特定于NPC类型部分的代码。如果你想，你可以进一步清理代码，删除只对其他NPC运行的代码部分，但如果不这样做，就保留它。很多错误会出现。大多数可以通过让Visual Studio添加建议的using语句来解决。如果你没有使用Visual Studio，或者你没有看到建议或错误，你需要修复那个。阅读[常见问题](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio#faq)如果你没有看到建议的话。在这个特定代码中的一个错误是`NPC.gravity`，简单地将那些改为0.3f：

之前：
![](https://i.imgur.com/qdegobF.png)

之后：
![](https://i.imgur.com/3jGCdYb.png)

如果你现在在游戏中测试，你会注意到它们的行为非常奇怪。这是因为所有曾经决定Hoplite特定行为的代码现在都无法访问，因为`NPC.type`不再等于481了。让我们修复这个。使用find找到所有并替换`NPC.type == 481`为`(NPC.type == 481 || true)`。这样做会保留481，这在下一节中更容易找到。
![](https://i.imgur.com/XATbFHQ.png)

保存并重建，然后进入游戏。它工作了！...差不多了。再在复制的AI代码中搜索481，你会发现`NPC.type != 481`。这错误地评估为true，所以让我们把它替换为`false`。现在测试，我们的克隆是完美的。

现在我们奠定了基础，现在我们可以开始修改AI代码了。

### 改变弹射物

现在让我们改变弹射物。首先，制作一个敌对弹射物或找到一些你希望使用的其他弹射物。现在我们需要搜索AI代码，找出代码如何决定为Hoplite生成那个标枪。这段代码近4000行，我们不想盲目搜索。让我们再次搜索`481`并寻找看起来要么生成弹射物要么决定生成哪个弹射物的代码。最可能有我们需要结果的是带有`if (NPC.type == 481)`的行。让我们看第一个结果：
![](https://i.imgur.com/mikBCBp.png)
好的，这是将一些局部int设置为100，但如果你看看后面那个变量被使用的地方，它似乎与弹射物生成无关。另外，100的`ProjectileID`叫做`DeathLaser`，所以这可能是错误的。
![](https://i.imgur.com/FvS9gol.png)
接下来的几个结果赋值浮点值，所以它们可能不相关。
![](https://i.imgur.com/ueQGlEw.png)
最后，我们来到设置几个int变量的结果。在ProjectileID.cs中查找508，我们发现`JavelinHostile = 508;`，这绝对听起来正确！让我们把它改成一些敌对弹射物，然后使用Visual Studio"Find all References"来找出`num151`是什么：
![](https://i.imgur.com/qbD8jcy.png)
![](https://i.imgur.com/sCz0ujk.png)
啊哈，使用VS热键`Ctrl+Shift+Space`，我们看到`num151`作为`NewProjectile`的damage参数传入。随意也改变那行。

让我们在游戏中测试：
![](https://i.imgur.com/kPfkhCs.png)

好吧，它都工作了。结果视频：

https://github.com/tModLoader/tModLoader/assets/4522492/7031a703-89b1-4ceb-bd96-f0be412da07f

### 改变投掷距离、准确性和瞄准
看`Projectile.NewProjectile`代码，我们可以找到`num147`和`num149`指的是`SpeedX`和`SpeedY`：
![](https://i.imgur.com/vvZkdK2.png)
让我们使用`F2`快速重命名源代码中的变量使代码更易读，然后搜索481再次找到特定于原始Hoplite的代码。再次浏览Find结果，你会注意到其中一个结果似乎表示在玩家头部上方瞄准，另一个似乎决定投掷力度，另一个似乎调整`speedX`和`speedY`使弹射物稍微不准确。我将这留给你作为练习。使用`F2`重命名变量，因为你弄清楚它们的目的，开始理解AI代码的小部分。

### 改变动画
本指南将在有人表示有兴趣时稍后完成。

## 自定义连枷
制作一个连枷，你可能已经注意到射程很难自定义。（TODO）

## ExampleLamp瓦片
这些是创建`ExampleLamp`的确切步骤。灯是特殊的，因为它们可以通过电线打开和关闭。到目前为止，ExampleMod中没有这样的例子。

首先，我们需要在[TileID.cs](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Tile-IDs)中找到Lamp TileID。我们发现`Lamps = 93;`。接下来，让我们找到一个我们希望复制的特定灯样式。使用[TConvert](https://forums.terraria.org/index.php?threads/tconvert-extract-content-files-and-convert-them-back.61706/)提取Tiles_93.xnb，然后在图像编辑器中打开Tiles_93.png。我们将修改其中一个灯为ExampleLamp，但你会想要只使用Tiles_93.png作为指南在你的模组中。

![](https://i.imgur.com/SyMfAb7.png)

我喜欢这个，所以我将裁剪图像并编辑它以适应ExampleMod艺术风格。我测量了顶部灯，注意到它高度是54像素（如果我们包括2个填充像素），并确保将所选灯裁剪到相同的高度。填充像素有时会令人困惑，记住大多数瓦片是由16x16像素图像块构成的。

![](https://i.imgur.com/LFGJANI.png)

确保保存文件，然后对物品图像做同样的事情。我只是在[wiki](https://terraria.wiki.gg/wiki/Lamps)上查找ItemID。事实证明我们使用了Rich Mahogany Lamp作为我们的指南，首先通过在源代码中搜索2087（Rich Mahogany Lamp的ItemID）来做`ModItem`。在我们最初的搜索中，我们没有找到结果！看着ItemID.cs，我们看到RichMahoganyLamp在许多其他灯物品旁边。有时源代码使用范围来将代码应用于许多不同的物品类型。`RichMahoganyLamp`上面最远的灯是`CactusLamp = 2082;`，让我们搜索2082。这次我们找到了我们`ModItem.SetDefaults()`所需的代码：

```cs
if (type >= 2082 && type <= 2091)
{
	this.useStyle = 1;
	this.useTurn = true;
	this.useAnimation = 15;
	this.useTime = 10;
	this.autoReuse = true;
	this.maxStack = 99;
	this.consumable = true;
	this.createTile = 93;
	this.placeStyle = type + 1 - 2082;
	this.width = 10;
	this.height = 24;
	this.value = 500;
	return;
}
```

这是我们将这段代码转换为一个`ModItem`的结果。我们添加了一个配方并用正确的TileType替换了`Item.createTile`。我们也删除了`Item.placeStyle`，因为我们的瓦片只有1种样式：

```cs
using Terraria;
using Terraria.ID;
using Terraria.ModLoader;

namespace ExampleMod.Items.Placeable
{
	class ExampleLamp : ModItem
	{
		public override void SetDefaults() {
			Item.useStyle = 1;
			Item.useTurn = true;
			Item.useAnimation = 15;
			Item.useTime = 10;
			Item.autoReuse = true;
			Item.maxStack = 99;
			Item.consumable = true;
			Item.createTile = ModContent.TileType<Tiles.ExampleLamp>();
			Item.width = 10;
			Item.height = 24;
			Item.value = 500;
		}

		public override void AddRecipes() {
			Recipe recipe = CreateRecipe();
			recipe.AddIngredient(ItemID.WoodenChair);
			recipe.AddIngredient(ModContent.ItemType<ExampleBlock>(), 10);
			recipe.AddTile(ModContent.TileType<Tiles.ExampleWorkbench>());
			recipe.Register();
		}
	}
}
```

现在`ModItem`完成了，让我们做`ModTile`。首先我们必须在`ModTile.SetStaticDefaults`中找到我们需要的所有代码行。首先，阅读[基础瓦片](https://github.com/tModLoader/tModLoader/wiki/Basic-Tile)，这样你就知道了关于瓦片的所有各种字段，如`Main.tileSolid`、`Main.tileFrameImportant`等。现在让我们在源代码中搜索TileID（93）。一如既往，会有很多与我们想要的结果无关的结果。幸运的是，我们找到了各种Main.tileX结果，让我们把那些添加到我们的`ModTile`。我们可以忽略Item.cs结果，但Lang.cs显示添加一个地图条目。在我们的`ModTile`中，我们可以利用原版本地化文本这样做：`AddMapEntry(new Color(253, 221, 3), Language.GetText("MapObject.FloorLamp"));`。我们从另一个结果得到了那个颜色：`array[93][0] = color;`然后看`color`。

![](https://i.imgur.com/jWYvbLl.png)

最后，还有其他结果，但我们直接去看`TileObjectData`结果：

```cs
TileObjectData.newTile.CopyFrom(TileObjectData.Style1xX);
TileObjectData.newTile.WaterDeath = true;
TileObjectData.newTile.WaterPlacement = LiquidPlacement.NotAllowed;
TileObjectData.newTile.LavaPlacement = LiquidPlacement.NotAllowed;
TileObjectData.newSubTile.CopyFrom(TileObjectData.newTile);
TileObjectData.newSubTile.LavaDeath = false;
TileObjectData.newSubTile.LavaPlacement = LiquidPlacement.Allowed;
TileObjectData.addSubTile(23);
TileObjectData.addTile(93);
```

我们可以直接复制粘贴到我们的`ModTile`中，在我们将93替换为Type之后。我们也不需要SubTiles，所以让我们删除那些行。这是我们的`ModTile`到目前为止：

```cs
using Microsoft.Xna.Framework;
using Terraria;
using Terraria.Enums;
using Terraria.Localization;
using Terraria.ModLoader;
using Terraria.ObjectData;

namespace ExampleMod.Tiles
{
	class ExampleLamp : ModTile
	{
		public override void SetStaticDefaults()
		{
			Main.tileFlame[Type] = true;
			Main.tileLighted[Type] = true;
			Main.tileFrameImportant[Type] = true;
			Main.tileNoAttach[Type] = true;
			Main.tileWaterDeath[Type] = true;
			Main.tileLavaDeath[Type] = true;

			TileObjectData.newTile.CopyFrom(TileObjectData.Style1xX);
			TileObjectData.newTile.WaterDeath = true;
			TileObjectData.newTile.WaterPlacement = LiquidPlacement.NotAllowed;
			TileObjectData.newTile.LavaPlacement = LiquidPlacement.NotAllowed;
			TileObjectData.addTile(Type);

			AddMapEntry(new Color(253, 221, 3), Language.GetText("MapObject.FloorLamp"));
		}
	}
}
```

让我们在游戏中看看！

https://github.com/tModLoader/tModLoader/assets/4522492/30d7164c-c0bf-4ed1-9039-ab189f219c00

看起来我们还有几个问题：我们无法切换它，我们无法挖掘它来恢复它，火焰纹理完全乱了，它实际上没有照亮任何东西，瓦片没有交替方向，而且我们缺少有时随机生成的小火花。看这6个问题，我们会在剩余的搜索结果中找到其中5个的答案。对于挖掘问题，只需跟随ExampleMod中的其他瓦片。

### 切换
其中一个结果在`Wiring.HitWireSingle`中，这听起来像我们想要的。相应的tModLoader钩子是`ModTile.HitWire`。我们可以复制代码并让Visual Studio建议正确的using语句。随意通过重命名变量来清理代码。

### 照明
另一个结果似乎根据瓦片样式修改3个变量。这可能与照明有关，因为照明使用红色、绿色和蓝色。再一次，我们搜索附近的一个`TileLoader`方法，了解到`ModDust.ModifyLight`是我们需要的地方。记住要清理代码，删除所有与其他瓦片样式相关的代码。

### 火花
其中一个结果接近调用`Dust.NewDust`的代码。这一定是火花代码。在这个代码行附近搜索`TileLoader`告诉我们`ModTile.DrawEffects`是我们想要使用的钩子。也复制这段代码。清理它并适应我们的瓦片。

### 方向
其中一个结果接近与`SpriteEffects`相关的代码。如果你看过ExampleAnimatedTile，你会认这段代码。将此代码迁移到`ModTile.SetSpriteEffects`中。

### 火焰
最后一个相关结果与绘制Flame纹理有关。看这段代码附近的代码，很明显`Main.tileFlame`使用硬编码的TileID来绘制不同的火焰纹理。我们需要自己重新实现它，并删除我们代码中的`Main.tileFlame[Type] = true;`来使其工作。最接近的TileLoader结果告诉我们需要使用`ModTile.PostDraw`。除了适配这段代码，我们还将在我们的模组中制作一个新的Flame纹理。

### 最终代码和结果
这是[最终代码](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleLamp.cs)和结果，示例模组中有精灵图。

https://github.com/tModLoader/tModLoader/assets/4522492/d269da77-3e24-4e6c-9576-dfaa089ecad0
