# 前置条件

您应该知道[如何查看原版代码](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption)。

您应该知道[什么是事件](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/)。

# 什么以及为什么

有时候在尝试实现功能时，我们无法直接override tModLoader提供的钩子。但是，如果我们能以某种方式"override"现有的原版方法，我们就能够产生我们想要的效果。这基本上就是detours的作用。

tModLoader提供了一个用于detour原版方法的API，为我们提供了许多可以订阅的事件。它们遵循以下模式：

`namespace.On_Class.MethodName_MethodSignature`

例子：
```
Terraria.On_Player.GetItemGrabRange
Terraria.GameContent.ItemDropRules.On_CommonDrop
Terraria.On_Projectile.NewProjectile_IEntitySource_Vector2_Vector2_int_int_float_int_float_float_float
Terraria.On_NPC.HitModifiers.SetInstantKill
```

这里有几件需要注意的事：
- `On_`只前缀类，永远不是命名空间或方法
- 具有重载的方法会附加方法签名来区分它们，只有在方法实际有重载时才这样做
- 嵌套类型，如`NPC.HitModifiers`，它们的detour事件在父类型上

所有这些`On`事件就是我们detour原版方法的方式。

# 如何做

要detour其中之一，我们在某个地方使用`Load`钩子，我们将使用`ModSystem`作为这个例子，但如果您喜欢，您可以把它放在其他地方，比如`ModPlayer`或`ModItem`（如果它与那里的其他代码相关的话）。

所以，我们`override``Load`

```cs
public class DetourExample : ModSystem 
{
    public override void Load() {
    
    }
}
```

现在，我们只选择一个要detour的东西。您通常会通过查看原版代码来了解需要什么。对于这个例子，我们将编写一个detour，将任何buff的buff时间增加三倍，不包括flask buff和debuff。如果我们在原版代码中搜索一点，我们会发现`Player.AddBuff_DetermineBuffTimeToAdd`方法就是这样做的。

我们将使用Visual Studio的一个功能，但任何适当的IDE也应该有这个功能。如果我们输入事件后跟`+=`来订阅一个方法，我们会看到这个：

![image](https://user-images.githubusercontent.com/82264356/263371842-20b68694-d427-4fc7-8532-dd7d82432136.png)

这是（大多数）IDE的一个方便的功能，它允许您快速生成具有正确参数的方法。如果我们继续按tab，我们会看到这个：

![image](https://user-images.githubusercontent.com/82264356/263371901-76a8e96c-a36c-40c9-9785-ccbb3dd6a2b4.png)

这允许我们将订阅的方法重命名为我们想要的任何名称，如果您愿意，可以保留它。我喜欢根据方法的作用而不是它们正在detour什么来命名我的方法，所以我们将使用这个作为例子：

```cs
public class DetourExample : ModSystem 
{
    public override void Load() {
        Terraria.On_Player.AddBuff_DetermineBuffTimeToAdd += TripleBuffTime;
    }

    private int TripleBuffTime(On_Player.orig_AddBuff_DetermineBuffTimeToAdd orig, Player self, int type, int time1) {
        throw new System.NotImplementedException();
    }
}
```

首先，让我们把它做成`static`以避免从我们的类中捕获任何实例东西（[见这里获取更多信息](#常见错误)）。现在，我们比原版方法多了几个参数。这个`On_Player.orig_AddBuff_DetermineBuffTimeToAdd orig`和`Player self`参数。这是我们在detour中看到的常见模式，第一个`orig`参数是我们调用原版方法的方式，第二个`self`参数是在其上调用方法的对象（注意：当detour静态方法时，这里不会有`self`，因为没有要传递的实例）。正如您可能已经可以告诉的，这些遵循模式：

`On_Class.orig_MethodName orig`和`Class self`

此外，传递给现有原版方法的任何参数都在这两个之后，在这个例子中我们有`int type`和`int time1`，但您detour的其他方法可能有更多或更少。

所以，让我们实际实现一些东西

```cs
public class DetourExample : ModSystem 
{
    public override void Load() {
        Terraria.On_Player.AddBuff_DetermineBuffTimeToAdd += TripleBuffTime;
    }

    private static int TripleBuffTime(On_Player.orig_AddBuff_DetermineBuffTimeToAdd orig, Player self, int type, int time1) {
        int buffTime = orig(self, type, time1);

        return buffTime;
    }
}
```

所有这些做的就是调用`orig`，它将为我们调用原版方法，然后返回结果。这与根本没有detour的情况完全相同。让我们*实际*做点什么

```cs
public class DetourExample : ModSystem 
{
    public override void Load() {
        Terraria.On_Player.AddBuff_DetermineBuffTimeToAdd += TripleBuffTime;
    }

    private static int TripleBuffTime(On_Player.orig_AddBuff_DetermineBuffTimeToAdd orig, Player self, int type, int time1) {
        int buffTime = orig(self, type, time1);

        if (!Main.debuff[type] && !BuffID.Sets.IsAFlaskBuff[type] && !Main.buffNoTimeDisplay[type] && time1 != 2) {
            return buffTime * 3;
        }

        return buffTime;
    }
}
```

我们在这里添加了一点逻辑——如果我们的buff不是debuff、flask buff或不应该增加时间的buff，返回`orig`结果的三倍。

就是这样……我们的detour完成了！如果我们继续测试这个，我们会看到10分钟的采矿药水提供30分钟的buff。

![image](https://user-images.githubusercontent.com/82264356/263373060-bcc099d3-8378-4716-b8a2-676c04be0366.png)
![image](https://user-images.githubusercontent.com/82264356/263373119-155081c1-922d-4e93-affb-16e000f2f2dd.png)

这只是您可以使用detours的一个例子，而且是一个非常基础的例子。

# 关于...

## 取消订阅

如果您理解事件，您会知道我们通常需要取消订阅它们，否则我们的方法会在不应该的时候保持订阅。tModLoader在幕后为我们处理取消订阅，所以我们不需要做这个。

## 多个模组订阅同一个事件

您实际上可以自己测试这个——多次detour同一个方法，在那里放置一些日志通过调用`Main.NewText("Hello, from the first detour!")`，然后看看当您切换detour的顺序时会发生什么。您应该看到最后订阅的方法被首先调用，如果您移除`orig`调用，*只有*最后一个方法被调用。基本上，当您调用`orig`时，它实际上调用下一个订阅的方法，最后是原版方法。所以，如果您的模组在另一个detour相同方法的模组之前加载，他们的`orig`将调用您的detour，您的`orig`将调用原版逻辑。如果顺序相反，您的`orig`调用他们的detour，他们的`orig`调用原版逻辑。

## 构造函数

这些也是可以detour的。`ctor`是实例构造函数，`cctor`是静态构造函数。

## Detour没有提供事件的方法

如果您想detour其他模组的方法，这是很常见的。这超出了本指南的范围，但[这个指南](https://github.com/tModLoader/tModLoader/wiki/Detouring-and-IL-Editing-using-HookEndpointManager)详细解释了它。

# 常见错误

## Detour内联方法

编译后的C#程序由公共语言运行时(CLR)运行。简单地说，CLR在运行程序时会寻找它可以做的优化，使程序运行得更快。它会做的优化之一是内联方法调用——完全移除方法调用，用方法实际体替换它。如果方法被内联，我们就无法detour它们，因为方法从未被调用以便我们的detour被调用。无法判断方法是否会被内联，但是小方法（<10行代码）很可能被内联。在detour小方法时，先快速测试一下方法是否被内联，然后再编写完整的逻辑。

## 捕获对象

如果我们在detour中使用实例属性/字段，我们就打开了意外捕获那些对象的大门。这可能导致问题，因为我们从tModLoader创建的dummy实例中捕获这些对象，然后每次运行detour时继续使用它们。如果我们捕获`ModPlayer``Player`属性，它不会与我们可能在detour中传递的`self`相同。解决这个问题的方法是使我们的detour方法成为static，并使用提供的`self`参数访问实例数据。例如：

```cs
    public class DetourExample : ModPlayer
    {
        public bool EpicAccessory { get; set; } // 假设在ResetEffects中正确重置，并在配饰物品中正确分配

        public override void Load() {
            Terraria.On_Player.AddBuff_DetermineBuffTimeToAdd += TripleBuffTime;
        }

        private int TripleBuffTime(On_Player.orig_AddBuff_DetermineBuffTimeToAdd orig, Player self, int type, int time1) {
            int buffTime = orig(self, type, time1);

            if (!Main.debuff[type] && !BuffID.Sets.IsAFlaskBuff[type] && !Main.buffNoTimeDisplay[type] && time1 != 2) {
                int buffTimeMult = EpicAccessory ? 5 : 3;
                return buffTime * buffTimeMult;
            }

            return buffTime;
        }
    }
```

在这里，我们意外捕获了那个`EpicAccessory` bool，这是由于我们的detour方法是实例的。如果我们扔一个`static`进去，我们会看到这个错误：

![image](https://user-images.githubusercontent.com/82264356/264714299-9c74c284-3b1f-4802-ba5e-a01b70a24632.png)

所以，让我们修复它以使用传递的实例。

```cs
    public class DetourExample : ModPlayer
    {
        public bool EpicAccessory { get; set; } // 假设在ResetEffects中正确重置，并在配饰物品中正确分配

        public override void Load() {
            Terraria.On_Player.AddBuff_DetermineBuffTimeToAdd += TripleBuffTime;
        }

        private static int TripleBuffTime(On_Player.orig_AddBuff_DetermineBuffTimeToAdd orig, Player self, int type, int time1) {
            int buffTime = orig(self, type, time1);

            if (!Main.debuff[type] && !BuffID.Sets.IsAFlaskBuff[type] && !Main.buffNoTimeDisplay[type] && time1 != 2) {
                int buffTimeMult = self.GetModPlayer<DetourExample>().EpicAccessory ? 5 : 3;
                return buffTime * buffTimeMult;
            }

            return buffTime;
        }
    }
```

注意：如果您订阅一个通过lambda语法创建的方法（`evt += (orig, self, params) => { }`），您仍然可以在参数声明之前添加`static`关键字（`evt += static (orig, self, params) => { }`）。但是，编译器生成的实际方法***不能保证是static***。有关更多信息，请参阅[Microsoft关于静态匿名函数的解释](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/csharp-9.0/static-anonymous-functions)
