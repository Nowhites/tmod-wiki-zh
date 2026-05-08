***
本指南已更新至1.4版本。如果您需要查看此wiki页面的1.3旧版本，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Basic-Sounds/0dd271b8a344c26fb1a1b375b1250d4f7069c626)
***

# 简介
我们可以在tModLoader的许多地方使用声音，本指南将教您了解tModLoader如何组织和使用声音。

# 声音基础
需要注意2个概念。第一个是实际的声音资源。这对应于特定的声音文件，例如.wav文件。第二个概念是`SoundStyle`，这是一个表示声音资源和相关播放设置的对象。

## 声音资源

声音资源可以在您的ModSources文件夹和Terraria安装目录中找到。声音资源可以是许多不同的声音文件格式，包括.wav、.ogg、.mp3和.xnb。例如，[ExampleMod](https://github.com/tModLoader/tModLoader/tree/stable/ExampleMod/Assets/Sounds/Items/Guns)在`ExampleMod\Assets\Sounds\Items\Guns\ExampleGun.ogg`有一个`ExampleGun.ogg`文件。在您的Terraria安装文件夹中，您会在`C:\Program Files (x86)\Steam\steamapps\common\Terraria\Content\Sounds`文件夹中找到数百个声音文件。这些文件是.xnb文件，这是一种特殊格式，您无法通过双击直接聆听。要在计算机上聆听这些声音，请参阅下面的[提取](#Extract)部分。

## SoundStyle

现有的`SoundStyle`可以在`Terraria.ID.SoundID`类中找到，新的`SoundStyle`可以在您的模组中创建，用于现有声音资源的变体或播放包含在您模组中的声音资源。

要创建新的`SoundStyle`，您需要声音文件的路径，不带文件扩展名。使用该路径，您可以创建SoundStyle对象并存储以供以后使用。

```cs
// 从此Mod中的声音文件创建SoundStyle，然后播放
SoundStyle ExampleGunSoundStyle = new SoundStyle("ExampleMod/Assets/Sounds/Items/Guns/ExampleGun");
SoundEngine.PlaySound(ExampleGunSoundStyle);

// 从Terraria中的声音文件创建SoundStyle，然后播放
SoundStyle CoinsSoundStyle = new SoundStyle("Terraria/Sounds/Coins");
SoundEngine.PlaySound(CoinsSoundStyle);
```
不是赋值给变量，您可以直接播放声音：
```cs
// 从此Mod中的声音文件创建SoundStyle，然后播放
SoundEngine.PlaySound(new SoundStyle("ExampleMod/Assets/Sounds/Items/Guns/ExampleGun"));

// 从Terraria中的声音文件创建SoundStyle，然后播放
SoundEngine.PlaySound(new SoundStyle("Terraria/Sounds/Coins"));
```
可以进一步自定义`SoundStyle`以调整音量、音调、叠加行为等。请参阅下面的[自定义声音播放](#Customizing-Sound-Playback)部分了解更多信息。

# 播放声音
有些声音自动播放，而其他则可以手动播放。

## 预定事件声音

声音最常见的用法是在某些事件发生时播放。例如，当使用`Item`时，`Item.UseSound`会自动播放。通过为这些现有字段分配`SoundStyle`，声音将在预期时自动播放。

我们可以为`Item.UseSound`、`NPC.HitSound`、`NPC.DeathSound`、`ModWall.HitSound`和`ModTile.HitSound`分配`SoundStyle`。我们将在相应的`SetDefaults`覆盖中执行这些操作。以下是使用现有`SoundStyle`的一些示例以及使用自定义`SoundStyle`的示例：

```cs
// 需要在.cs文件顶部使用using Terraria.ID;

public override void SetDefaults()
{
	// 其他代码
	Item.UseSound = SoundID.Item1;  // 剑挥动声音
}

public override void SetDefaults()
{
	// 其他代码
	NPC.HitSound = SoundID.NPCHit24; // 巨型陆龟被击中声音
	NPC.DeathSound = SoundID.NPCDeath4; // 蝙蝠死亡声音
}

// 除了上述内容外，还需要在此示例的.cs文件顶部使用using Terraria.Audio;

public override void SetDefaults()
{
	// 其他代码
	Item.UseSound = new SoundStyle($"ExampleMod/Assets/Sounds/Items/Guns/ExampleGun"); // 来自此模组的声音文件
}
```
可以通过[Intellisense](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#autocomplete--intellisense)找到其他可用的现有声音，但请注意大多数声音具有相当通用的名称。如果您正在尝试查找特定声音，您需要参阅下面的[查找声音](#Finding-Sounds)部分。
![](https://i.imgur.com/LIX0kff.png)

使用现有`SoundStyle`时，您会继承分配给该`SoundStyle`的播放设置。请参阅下面的[自定义声音播放](#Customizing-Sound-Playback)部分了解更多信息。

## 手动播放声音

要手动播放声音，请使用`SoundEngine.PlaySound`方法。此方法在`Terraria.Audio;`命名空间中，因此请确保在.cs文件顶部有`using Terraria.Audio;`。

`SoundEngine.PlaySound`方法有2个参数。第一个是`SoundStyle`，这是必需的。第二个是可选的`Vector2`，表示声音在世界坐标中的位置。如果省略位置，声音将像发生在屏幕中心一样播放，没有任何平移效果。

要播放现有声音，只需调用方法：
```cs
SoundEngine.PlaySound(SoundID.Item59); // 储钱罐哼声
```
要在特定位置播放该声音，将位置作为第二个参数传入。位置是世界坐标。此示例假设代码在`ModProjectile`中：
```cs
SoundEngine.PlaySound(SoundID.Item59, Projectile.Center); // 储钱罐哼声
```

### 我在哪里放置这段代码？
只要有意义就行，但通常`ModNPC.AI`或`ModProjectile.AI`是手动播放声音的最常见位置。使用随机性或计时器可以帮助您的内容看起来自然。

# 自定义声音播放

许多在`SoundID`类中找到的现有`SoundStyle`预配置了各种播放自定义。例如，如果您播放`SoundID.NPCHit24`，您会注意到它比`new SoundStyle("Terraria/Sounds/NPC_Hit_24")`的音量低一半。每个`SoundStyle`都有附加数据来配置这些自定义行为。

要自定义`SoundStyle`，我们将使用[`with`语法](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/with-expression)。`with`语法基本上允许我们创建现有对象的副本，只是进行一些指定的更改。例如，如果我们在`ModNPC`中使用`NPC.HitSound = SoundID.NPCHit4;`但发现音量太高不适合同敌人，我们可以创建具有自定义音量的`SoundStyle`副本：`NPC.HitSound = SoundID.NPCHit4 with { Volume = 0.7f };`

`with`语法适用于模组和原版声音，也可以进行多个调整。以下是最常见的调整。以下是可以在单个`with`语句中使用多个调整的方法：

```cs
NPC.HitSound = SoundID.NPCHit4 with { 
    Volume = 0.5f, 
    Pitch = 0.5f, 
    PitchVariance = 0.1f, 
    MaxInstances = 3, 
    SoundLimitBehavior = SoundLimitBehavior.IgnoreNew 
};
```

## Volume
音量默认为`1f`（100%），范围可以从`0f`到`1f`。

## Pitch
可以向上或向下调整音调，默认为`0f`。下限`-1f`是下一个八度，下限`1f`是上一个八度。

## Pitch Variance
每次播放声音时添加的音调随机性。这可以为声音添加一些变化，使其不那么重复。

## MaxInstances
这决定了可以同时播放多少个声音实例。默认值为`1`。调整此值以允许重叠声音。

## SoundLimitBehavior
当达到`MaxInstances`限制时，此调整会调整会发生什么。默认值是`ReplaceOldest`，它将重新启动声音。另一个选项是`IgnoreNew`，它将忽略最新播放声音的尝试。

## IsLooped
这决定声音是否应该循环。默认为false。使用此功能时，极其重要的是遵循本指南[循环声音](#looping-sounds)部分中的指南。

## Variation
可以为声音样式分配多个声音资源并随机播放。为此，声音资源必须命名相同但在末尾带有最终数字后缀。例如，`SoundStyle ExampleGunsSoundStyle = new SoundStyle("ExampleMod/Assets/Sounds/Items/Guns/ExampleGun_", 3);`将随机播放`ExampleGun_1`、`ExampleGun_2`或`ExampleGun_3`，概率相等。

### 加权概率和特定变体后缀
可以通过其他`SoundStyle`构造函数重载实现加权概率和更多对特定后缀的控制。
![image](https://user-images.githubusercontent.com/4522492/176332504-3d5f41a1-0420-48bf-ae38-ed3adb62334e.png)

原版鸭子声音是一个很好的例子。有2个主要变体，`Zombie_10`和`Zombie_11`。还有一个极罕见的`Zombie_12`，这是一个人说"quack"。相当令人不安。此代码使用权重来强烈支持2个正常嘎嘎声。`Zombie_10`和`Zombie_11`声音的权重为300f，`Zombie_12`声音的权重为1f。这意味着`Zombie_12`声音将大约每601次嘎嘎声中播放1次：

```cs
SoundStyle quack = new SoundStyle("Terraria/Sounds/Zombie_", stackalloc (int, float)[] { (10, 300f), (11, 300f), (12, 1f) });
```

## Position
声音的位置决定了相对于屏幕位置声音将播放的响度。它还控制声音在一侧播放得更大声，以暗示声音的方向。Position不是`SoundStyle`的属性，而是`SoundEngine.PlaySound`的第二个参数。它是可选的，当未提供时，声音将正常播放，没有平移或音量阻尼。

# 活动声音
在电子游戏的声音中，大多数时候使用"即发即忘"方法就完全足够了。"即发即忘"是一个视频游戏编程术语，指的是启动某些动作并让它运行过程，而没有办法在启动后调整它。对于大多数音效，不需要提前停止声音或调整音量或音调，因为它们通常很短，不需要努力。

然而，有些情况需要跟踪正在播放的声音并动态调整。这种功能最常见的用途是长声音和循环声音。

tModLoader将这些声音称为"活动声音"。

[ActiveSoundShowcaseProjectile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ActiveSoundShowcaseProjectile.cs)文件可用作活动声音的实践指南和参考。强烈建议使用`ExampleMod`在游戏中实验`ActiveSoundShowcase`。`ActiveSoundShowcaseProjectile.cs`可以视为本wiki部分的配套指南。

要正确使用活动声音，首先我们需要熟悉以下类：

### SlotId
本指南之前的示例展示了使用`SoundEngine.PlaySound`方法播放声音，但没有展示对方法返回值做任何事情。`SoundEngine.PlaySound`方法实际上有返回值。`SoundEngine.PlaySound`的返回值是一个`SlotId`实例。返回的`SlotId`实例可以存储为类变量，以便稍后检索该声音的实例。

使用`SoundEngine.TryGetActiveSound`方法，`SlotId`可以检索相应的`ActiveSound`来对其执行操作。

### ActiveSound
`ActiveSound`类对应于当前正在播放的单个`SoundStyle`实例。模组代码通常将使用`SlotId`或`SoundStyle`来检索`ActiveSound`来对其执行操作。

使用`SoundEngine.FindActiveSound`，`SoundStyle`可用于从`SoundStyle`检索`ActiveSound`。然而，更常见的是使用`SoundEngine.TryGetActiveSound`方法，因为如果实体播放相同`SoundStyle`的多个实例，将导致检索到错误的`ActiveSound`。

### SoundUpdateCallback
`SoundEngine.PlaySound`的第三个参数是一个可选参数，是一个`SoundUpdateCallback`委托。模组制作者可以传入一个方法，该方法返回一个bool并接受单个`ActiveSound`参数，该参数将在声音每tick更新时被调用。此回调是动态修改声音的主要机制，但声音可以在适合代码设计的其他方法中修改。

## 平移声音或更改声音位置
现在我们可以使用到目前为止学到的东西来学习"活动声音"最常见的用法：动态调整声音的位置。当NPC或弹幕发出声音时，通常声音足够快，玩家不会注意到声音不会随着NPC或弹幕移动，但如果声音很长，如果不正确平移则更明显。要调整声音的位置，我们可以检索`ActiveSound`并修改其属性。

### 平移长声音示例
```cs
public class MyNPC : ModNPC
{
	SlotId wooshSoundSlot;
	SoundStyle wooshSoundStyle = new SoundStyle("ModMod/Sounds/whoosh");

	public override void AI() {
		// 其他AI代码...

		// 检查声音是否已经在播放...
		if (!SoundEngine.TryGetActiveSound(wooshSoundSlot, out var activeSound)) {
			// 如果没有，播放声音并记住SlotId
			wooshSoundSlot = SoundEngine.PlaySound(wooshSoundStyle, NPC.Center);
		}
		else {
			// 如果正在播放，更新声音的位置以匹配NPC的位置
			activeSound.Position = NPC.Center;
		}
	}
}
```

## 停止声音
如果某些东西中断了声音的来源，可以提前停止声音。要做到这一点，遵循与之前相同的逻辑，但在`ActiveSound`上调用`Stop`方法。这可以在代码中任何相关的地方完成。

## 更改声音音量
以同样的方式，可以更改`ActiveSound`实例上的`Volume`字段以动态调整声音音量。

## 更改声音音调
以同样的方式，可以更改`ActiveSound`实例上的`Pitch`字段以动态调整声音音量。

## SoundUpdateCallback方法
循环声音部分解释的SoundUpdateCallback方法也是一种很好的方法。它允许所有声音逻辑包含在一个地方。

## 循环声音
到目前为止讨论的技术对于长声音已经足够了，但对于循环声音，它们可能会导致声音继续播放直到游戏关闭。例如，设置为循环的声音可能会被启动，然后异常或其他错误代码导致跟踪"活动声音"的实体变得无效。声音，没有东西告诉它停止，会乖乖地继续播放。

正是由于这个原因，在处理循环声音时，模组制作者应该**始终**使用`SoundEngine.PlaySound`的`SoundUpdateCallback`参数。实际上，许多人可能会发现`SoundUpdateCallback`方法比使用之前为非循环声音解释的`SlotId`方法更方便。

要制作循环声音，只需将`IsLooped = true`添加到将播放的`SoundStyle`中。

### SoundUpdateCallback示例
使用`SoundEngine.PlaySound`的参数`SoundUpdateCallback`委托，提供的方法将在每次游戏更新时被调用，允许声音更新位置和其他属性。委托的返回值控制声音是否应该停止。以下示例显示了在每次更新时更新其位置的弹幕播放声音。声音将在弹幕不再活动时停止，但也可以使用其他逻辑。

```cs
public class MyProjectile : ModProjectile
{
	SlotId loopingSoundSlot;
	SoundStyle loopingSoundStyle = new SoundStyle("ModMod/Sounds/loopSound") {
		IsLooped = true,
	};

	public override void AI() {
		// 其他AI代码...

		// 检查声音是否已经在播放...
		if (!SoundEngine.TryGetActiveSound(loopingSoundSlot, out var activeSound)) {
			// 如果没有，播放声音并记住SlotId
			var tracker = new ProjectileAudioTracker(Projectile);
			loopingSoundSlot = SoundEngine.PlaySound(loopingSoundStyle, Projectile.position, soundInstance => {
				// 此示例是内联的，参见ActiveSoundShowcaseProjectile.cs了解其他方法
				soundInstance.Position = Projectile.position;
				return tracker.IsActiveAndInGame();
			});
		}
	}
}

```

**注意：**当处理`Projectile`跟踪声音时，需要`ProjectileAudioTracker`实例以避免罕见的边缘情况。[ActiveSoundShowcaseProjectile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ActiveSoundShowcaseProjectile.cs)中的示例展示了正确使用它们的方法。对于`NPC`，检查`.active`应该就足够了。对于其他情况，请确保逻辑允许声音在某些时候正确停止。

# 适配原版代码或过去tModLoader版本的代码
过去版本的tModLoader和从反编译的Terraria获取的代码不使用相同的`SoundStyle`方法来播放声音。要使用旧方法的代码，您需要修复代码。例如，您可能会发现类似`SoundEngine.PlaySound(12);`或`SoundEngine.PlaySound(4, (int)base.position.X, (int)base.position.Y, 7);`的代码，但尝试在模组中使用此代码将导致错误，例如`No overload for method 'PlaySound' takes 4 arguments`。

要修复这些，您需要打开[官方Terraria Wiki的声音ID页面](https://terraria.wiki.gg/wiki/Sound_IDs)，找到与您拥有的参数对应的行，然后将其更改为使用`SoundID`条目。可能会有一些轻微的差异，例如
"NPC Hit 50"对应`SoundID.NPCHit50"，"NPC Killed 18"对应`SoundID.NPCDeath18`。如果您仍然遇到错误，请依靠良好的判断力和[Intellisense](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#autocomplete--intellisense)。

这是一个简单的示例：
```cs
// 旧代码
SoundEngine.PlaySound(12);
```
现在，我们打开wiki并找到ID为12的条目：
![image](https://user-images.githubusercontent.com/4522492/171317783-e1dc3817-13e5-4444-abd9-4451f23a04aa.png)
利用此信息，我们可以更新代码：
```cs
// 修复后的代码
SoundEngine.PlaySound(SoundID.MenuTick);
```

有时原版代码对第2个和第3个参数使用`-1`。这些参数表示空位置，因此您可以安全地忽略它们。有时您还会看到`1`作为声音的"Style"参数，该参数在wiki上只有1个条目。您也可以忽略这些：
```cs
// 旧
SoundEngine.PlaySound(12, -1, -1, 1);
// 修复
SoundEngine.PlaySound(SoundID.MenuTick);
```

有时有2个数字（"Type"和"Style"），这些将在wiki上显示为一个数字后跟括号中的另一个数字。
```cs
// 旧代码
SoundEngine.PlaySound(4, (int)base.position.X, (int)base.position.Y, 7);
```
现在，我们打开wiki并找到ID为4和样式为7的条目。这将在wiki上显示为`4 (7)`：
![image](https://user-images.githubusercontent.com/4522492/171318331-62a11319-12a1-48d3-b905-87df52a199b7.png)
利用此信息，我们可以更新代码。另外，由于`SoundEngine.PlaySound`的第二个参数现在是`Vector2`，我们可以直接传入位置，而不是分别传入X和Y坐标：
```cs
// 修复后的代码
SoundEngine.PlaySound(SoundID.NPCDeath7, base.position);
```

很少，旧代码设置音量或音调偏移。要修复这些，请使用上面[自定义声音播放](#Customizing-Sound-Playback)部分所示的`with`语法。
```cs
// 旧
SoundEngine.PlaySound(12, -1, -1, 1, 0.75f, 0.3f);
// 修复后的代码A：考虑现有SoundStyle的音量和音调设置（如果有的话）。这是旧代码的直接改编
SoundStyle adjusted = SoundID.MenuTick with {
	Volume = SoundID.MenuTick.Volume * 0.75f,
	Pitch = SoundID.MenuTick.Pitch + 0.3f,
};
SoundEngine.PlaySound(adjusted);
// 修复后的代码B：忽略现有SoundStyle继承的现有音量和音调设置。这在技术上是不同的，但可能需要了解。注意，音调加了1f。
SoundEngine.PlaySound(SoundID.MenuTick with { Volume= 0.75f, Pitch = 1.3f });
```

要适配模组中的旧代码，您需要按照上面[SoundStyle](#SoundStyle)部分教的那样制作一个`SoundStyle`。

# 查找声音
对于许多模组来说，重用Terraria自带的声音是一个很棒的主意。Terraria有超过700种声音，但可能很难记住或找到特定的声音。有几种方法可以找到要使用的Terraria声音。
1. 提取所有声音并在媒体播放器中播放
2. 从您记得的物品或npc找到声音
3. 查阅源代码找到播放特定声音的代码

## 提取
Terraria声音文件是.xnb文件，您无法直接在计算机上播放这些文件。[TConvert](https://forums.terraria.org/index.php?threads/tconvert-extract-content-files-and-convert-them-back.61706/)可以提取Terraria声音文件并将其保存为.wav文件，您可以轻松加载到VLC或计算机上的任何媒体播放器中。一个接一个地播放它们可以是快速找到要使用的独特声音的方法。

## 在Terraria Wiki上查找声音
如果您查阅[官方Terraria Wiki的声音ID页面](https://terraria.wiki.gg/wiki/Sound_IDs)，您可以找到并播放任何声音。内部名称列对应于您将使用的`SoundID`字段。如果您知道特定敌人或物品发出声音并想要找到该声音，请使用此方法。

## 模组测试模组
一些模组可以帮助查找和播放现有声音：

[Modders Toolkit](https://forums.terraria.org/index.php?threads/modders-toolkit-a-mod-for-modders-doing-modding.55738/)有一个记录声音的选项，允许您查看导致声音播放的代码，以及查看模组声音路径以及声音类型和声音样式。它还有测试音量和音调自定义的工具，并生成您可以粘贴到模组中的声音播放代码。

## 源代码（需要专家先决条件）
通过使用`ItemID`或`NPCID`编号搜索`NPC.SetDefaults`或`Item.SetDefaults`，您可以轻松找到与您想要的声音对应的声音。

并非所有在Terraria中播放的声音都只是`UseSound`、`HitSound`或`DeathSound`数据，因此如果您希望找到在特殊情况下播放的声音，必须使用此方法。例如，您可能想知道鸭子在偶尔发出的嘎嘎声。搜索Duck的NPCID 362，您会发现结果之一显示`SoundEngine.PlaySound(30, (int)position.X, (int)position.Y);`嵌套在白天和1/200几率的条件下。我们现在可以使用此信息正确实现新鸭子。

我们将不得不更改此代码以适应tModLoader方法，因此将其更改为`SoundEngine.PlaySound(SoundID.Duck, position);`

## 电子表格
在这里查看原版物品和NPC的UseSound、HitSound和DeathSound：
[原版物品字段值](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Item-Field-Values)
[原版NPC字段值](https://github.com/tModLoader/tModLoader/wiki/Vanilla-NPC-Field-Values)
注意：电子表格目前已过时，因此您必须将所说的数字转换为`SoundID`字段。例如，如果物品的`UseSound`说"20"，您必须将其更改为`SoundID.Item20`。

## 从其他来源查找声音
如果您想在互联网上查找声音，请尝试查找可合法免费使用的声音。

# 常见错误
### 名称SoundID在当前上下文中不存在
在源文件顶部添加`using Terraria.ID;`。

### 名称SoundEngine在当前上下文中不存在
在源文件顶部添加`using Terraria.Audio;`。

### 找不到类型或命名空间名称'SoundStyle'（是否缺少using指令或程序集引用？）
在源文件顶部添加`using Terraria.Audio;`。

### "确保指定流包含有效的PCM单声道或立体声波形数据。"
使用[Audacity](http://www.audacityteam.org/download/)运行您的.wav文件，使用`File->Export Audio->Wav (Microsoft) signed 16-bit PCM`。作为参考，以下是Wav文件允许的参数：
1. 必须是PCM波形文件
2. 只能是单声道或立体声
3. 必须是8或16位
4. 采样率必须在8,000 Hz到48,000 Hz之间

## 相关参考
* [原版声音ID](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Content-IDs#sound-ids)

## 基础级别未涵盖的内容
音乐 -- 音乐的处理方式不同。