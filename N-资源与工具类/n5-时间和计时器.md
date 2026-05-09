***
本指南已更新至1.4版本。如果您需要查看此wiki页面的1.3旧版本，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Time-and-Timers/ecf72a76ff8f2643414d2dd2095a7c0f7b01024e)
***

# 时间和计时器
在处理Terraria模组制作的各个方面时，您会发现自己想要延迟操作或减少事情发生的频率。简而言之，您需要利用时间和计时器来实现您的模组制作目标。本指南将介绍许多与时间相关的概念，这样您就不会在代码中不知不觉地犯错误。

## 冷却
冷却不过是一个计时器，通常伴随条件或触发器而不是无限重复。本指南中的相同概念可以应用于实现冷却，例如在按下自定义键绑定、冲刺以及许多其他事情时。

## 不要使用循环
在处理计时器时，最重要记住的是游戏循环是调用所有钩子和方法的东西。代码中的循环不会充当计时器，它们会在纳秒内循环指定的次数，然后继续执行下面的代码。计时器必须编程，考虑到游戏循环将每刻调用一次方法。以下显示模组制作者常犯的常见错误。

### 错误示例
这个示例错误地假设while循环将导致此代码等待60刻然后运行下面的代码。情况并非如此，while循环将运行60次然后立即运行下面的代码。正确的方法是将计时器存储在非局部变量中并移除循环，如其他示例所示。
```cs
int timer = 60;
while (timer > 0)
{
    timer--;
}
// 其他打算在计时器到期后运行的代码
```

***

这个示例也会失败，`timer`永远不会达到60以上，因为它在相同作用域中定义，这意味着它将从0开始，增量到1，然后下一次刻将再次从0开始。为避免此问题，请将您的计时器定义（`int timer = 0;`）放在方法之外（通常在类中）。有关详细信息请参阅"使用新字段的计时器"部分。
```cs
int timer = 0;
timer++;
if (timer > 60)
{
    // 其他打算在计时器到期后运行的代码
    timer = 0;
}
```

# 墙时间 vs 游戏时间 vs 世界时间
首先，区分各种类型的时间很重要。

墙时间是现实世界的实际时间。我们几乎不想在模组制作中使用这个，因为通常我们的效果以与游戏相同的速度运行，或者以游戏世界的速度运行。以墙时间运行意味着当游戏暂停或以较低的每秒帧数运行时，我们编码的事件仍然会发生。这将导致许多bug。

世界时间是世界的报告时间。不正确使用世界时间可能产生的问题是当附魔日晷激活时事件发生得非常快。

游戏时间是我们最常用于游戏效果的时间。Terraria尝试以每秒60帧的速度运行，但如果弱电脑无法保持60 FPS，他们可能会以慢动作运行游戏。通过在我们的逻辑中使用游戏时间，我们可以使游戏效果以与游戏本身相同的相对速度运行。例如，如果敌人应该每秒攻击一次，使用墙时间意味着以30 FPS运行Terraria的用户需要躲避两倍的攻击次数。

总之，对几乎所有事情使用游戏时间。永远不要使用墙时间。对与世界本身的日夜循环相关的事情使用世界时间。

# 游戏时间
游戏时间是我们用于大多数游戏计时器元素的时间。游戏时间与帧率一致，因此游戏效果发生的频率与游戏本身相同。对于希望在模组中实现"计时器"的模组制作者来说，这几乎始终是预期的行为。

游戏时间以刻为单位测量。刻是一个术语，指每次游戏逻辑更新时。例如，每刻每个弹幕都会按其速度前进，其他一切也会移动。如果您的电脑很好，您每秒将获得60刻。因此，如果您希望效果持续2秒，则需要计数到120刻。作为提醒，如果用户未达到60 FPS并禁用了帧跳过，它不会正好是2秒，但这没关系，因为游戏中的其他一切都会以相同程度减慢。

实现一个以刻为单位的计时器很容易，您只需在每次调用Update/AI等时增加类的字段。增量后，您可以使用`if`语句检查是否已达到预期值，如果是，运行您想要的代码，例如生成`Projectile`。在此if语句中，如果打算重复操作，您可能还需要将计时器重置为0。（另一种方法是将计时器初始化为一个数字并递减直到达到0。效果是相同的，无论是向上计数还是向下计数。这取决于您的偏好。）

## 重要字段
* `Main.GameUpdateCount` - 每次加载世界时，这将重置为0。每刻增量1，即使游戏暂停时也会增量。
* `Player.miscCounter` - 每刻增量1，周期从0到299。使用此驱动玩家视觉。
* `NPC.ai[]`和`Projectile.ai[]` - 原版代码中的常见方法是使用这些数组作为计时器。
* [`Projectile.timeLeft`](https://github.com/tModLoader/tModLoader/wiki/Projectile-Class-Documentation#timeleft) - 倒数到0，一旦达到0，弹幕将自动死亡。默认情况下，将是3600（60秒），但可以在`SetDefaults`中更改。可用于简单计时器，通常用于计算生成后或消失前的刻。请参阅下面的示例。
* [`Projectile.extraUpdates`](https://github.com/tModLoader/tModLoader/wiki/Projectile-Class-Documentation#extraupdates)/`Projectile.MaxUpdates` - 这将导致Projectile.Update为特殊效果运行多次。如果您在实现计时器时似乎太快，请注意这一点。您需要缩放计时器逻辑以考虑额外的更新。

## 示例
在这些示例中，我们将使用`ModNPC.AI`或`ModProjectile.AI`来推进我们的计时器。
### 使用Projectile.ai[]（或NPC.ai[]）的计时器
（此示例使用`Projectile.ai[]`作为示例，但`NPC.ai[]`以相同方式工作）
如果您使用的是原始AI，或者知道您使用的aiStyle不使用2个ai槽位之一（NPC为4个），您可以使用projectile.ai[]作为计时器。由于projectile.ai[]会自动同步，这可能很方便，但确实需要一些调查以确保ai槽位可以自由使用：
```cs
Projectile.ai[0]++;
if(Projectile.ai[0] > 120) {
    // 我们的计时器已完成，在这里做点什么：
    // SoundEngine.PlaySound, Dust.NewDust, Projectile.NewProjectile等。随你便。
    Projectile.ai[0] = 0;
}
```
[ExampleAdvancedAnimatedProjectile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleAdvancedAnimatedProjectile.cs#L79)展示了使用计时器在50刻内淡入弹幕然后再淡出的用法。

我们可以使用属性使代码更易于阅读：
```cs
public float Timer {
	get => Projectile.ai[0];
	set => Projectile.ai[0] = value;
}

public override void AI() {
	// 其他代码...
	Timer++;
	if (Timer > 120) {
		// 我们的计时器已完成，在这里做点什么：
		// SoundEngine.PlaySound, Dust.NewDust, Projectile.NewProjectile等。随你便。
		Timer = 0;
	}
	// 其他代码...
}
```
[ExampleCustomAISlimeNPC.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/NPCs/ExampleCustomAISlimeNPC.cs#L178)展示了这种方法在实践中的应用。

### 使用新字段的计时器
`NPC.ai[]`和`Projectile.ai[]`通过网络自动同步，但您可以在`ModNPC`或`ModProjectile`类中制作新字段。这使代码更易于阅读，但您可能需要同步这些额外数据。如果您想了解更多，请阅读[多人游戏兼容性](https://github.com/tModLoader/tModLoader/wiki/Multiplayer-Compatibility#npc--modnpc)。某些数据不需要同步，因此请尝试熟悉数据何时需要同步。

制作充当计时器的新字段很简单：
```cs
public class ExampleBullet : ModProjectile
{
	public int Timer;

	public override void SetDefaults() {
		// 代码在这里
	}

	public override void AI() {
		Timer++;
		if (Timer > 120) {
			// 我们的计时器已完成，在这里做点什么：
			// SoundEngine.PlaySound, Dust.NewDust, Projectile.NewProjectile等。随你便。
			Timer = 0;
		}
	}
}
```
ExampleMod有许多示例：[Abomination Send/ReceiveExtraAI](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Old/NPCs/Abomination/Abomination.cs#L231)。[更多示例](https://github.com/tModLoader/tModLoader/search?utf8=%E2%9C%93&q=SendExtraAI+path:ExampleMod&type=Code)

### 使用Projectile.timeLeft的弹幕一次性计时器
如果您需要某事在生成后X刻发生一次，您可以利用`Projectile.timeLeft`（您可能想在`ModProjectile.SetDefaults`中将`Projectile.timeLeft`更改为更小的值）：
```cs
// 当弹幕还有1秒生命时
if(Projectile.timeLeft == 60) {
    // 在这里做点什么。
}
```
### 取模计时器
对于视觉效果，您可以使用简单地使用`Main.GameUpdateCount`的计时器。对于游戏元素不要使用这种方法，因为不准确会导致问题。
```cs
if(Main.GameUpdateCount % 60 == 0) {
   // Dust.NewDust或其他视觉效果。
}
```
这是另一个示例，显示在1秒之间循环4种颜色。将`GameUpdateCount`除以60将值转换为计数秒，使用取模4让它循环。这段代码来自`ModifyTooltips`方法，但这个想法可以在其他情况下使用。
```cs
switch (Main.GameUpdateCount / 60 % 4)
{
	case 0:
		line.OverrideColor = new Color(254, 105, 47);
		break;
	case 1:
		line.OverrideColor = new Color(34, 221, 151);
		break;
	case 2:
		line.OverrideColor = new Color(190, 30, 209);
		break;
	case 3:
		line.OverrideColor = new Color(0, 106, 185);
		break;
}
```

### 循环、插值效果

我们可以使用计时器来控制应该随时间变化的视觉效果。例如，如果我们想要在2种颜色之间过渡工具提示，我们可以使用计时器来控制我们离第二种颜色有多近。这称为插值。要做到这一点，使用取模计时器方法并将结果除以周期。`(Main.GameUpdateCount % 60) / 60f`，例如，将从0开始，在每次游戏更新时向1移动，直到60次游戏更新或1秒后循环回0。我们使用这个值调用`Color.Lerp`方法，这将返回输入值之间适当的颜色值。

**循环**
```cs
float lerpAmount = (Main.GameUpdateCount % 60) / 60f;
Color color = Color.Lerp(Color.Red, Color.Green, fade);
```
当游戏每次游戏更新运行此代码时，颜色将在红色和绿色之间转换，然后在1秒内循环回红色。更改60以调整速度。还有其他"Lerp"方法，如`MathHelper.Lerp`和`Utils.MultiLerp`可以尝试。

**循环**
另一种方法是循环。循环效果是通过从0到1再回到0的插值来实现的。这种方法将避免循环导致的跳跃。`Utils.PingPongFrom01To010`获取0到1之间的值，并从0插值到1再回到0，没有任何跳跃。这个示例从红色转换到绿色再回到红色，然后重复。

```cs
float lerpAmount = Utils.PingPongFrom01To010((Main.GameUpdateCount % 60) / 60f);
Color color = Color.Lerp(Color.Red, Color.Green, fade);
```
`Utils.Turn01ToCyclic010`以相同方式操作，但以余弦波的方式平滑进行。对于更平滑的效果可以使用此方法，避免过渡突然反转方向。

# 世界时间
世界时间通常以与游戏时间相同的速度前进，但在几种情况下差异至关重要。当游戏暂停时，世界时间不会前进。当附魔日晷正在使用时，时间以比平时快60倍的速度前进。旅程模式还允许调整世界时间。请注意，模组也可以更改时间前进的速度。例如，时间可以在HerosMod中暂停，因此依赖世界时间进行游戏效果会失败。

## 重要字段
以下是与世界时间相关的重要字段：
* `Main.dayTime` - 白天为true，夜晚为false
* `Main.time` - 白天时为0（凌晨4:30）到54000（下午7:30）之间的值，夜晚时为0（下午7:30）到32400（凌晨4:30）之间的值。与Main.dayTime一起使用。Main.time通常每刻增量1（参见`Main.dayRate`）。每个游戏内小时是3600刻。
  * 示例：`Main.dayTime && Main.time < 18000.0` - 凌晨4:30到上午9:30之间的早晨（因为18000/3600 == 5）
* `Main.dayRate` - 通常为1。附魔日晷发生时为60。该值在`Main.UpdateTime`方法期间添加到`Main.time`。

## 预期用途
* [NPC生成](https://github.com/tModLoader/tModLoader/wiki/Basic-NPC-Spawning)
* 世界更新事件，如TODO：[ExampleWorld中的火山事件](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Old/ExampleWorld.cs#L336)

# 墙时间
当您是一个初学者模组制作者时，您可能会发现自己正在搜索"c#计时器"试图为模组编写一些东西。如果您这样做了，您找到了使用`System.Timers`的示例。这些概念根本不适用于视频游戏或tModLoader模组制作，因为它们是墙时间的示例。您需要查阅上面的[世界时间](#world-time)和[游戏时间](#game-time)来确定正确的方法。同样，使用`System.Timers`几乎肯定是错误的方法。当用户暂停游戏或使用附魔日晷时，它会有bug且不正确。

同样，使用`Thread.Sleep`等待一些时间再运行代码也是如此。这会冻结游戏。您需要编写自己的逻辑，通过利用游戏循环来稍后运行代码。

模组制作者对墙时间的一个适当用途是测量一段代码的性能，例如世界生成方法。随意使用墙时间来测量性能：
```cs
var stopWatch = Stopwatch.StartNew();
// 要测量的代码
ModContent.GetInstance<ExampleMod>().Logger.Info($"{stopWatch.ElapsedMilliseconds}毫秒已过去。");
```
