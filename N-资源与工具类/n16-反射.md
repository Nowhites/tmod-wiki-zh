# 介绍
Terraria使用C#编写。在C#中，[访问修饰符](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers)（如`public`、`internal`和`private`）用于限制如何访问类、方法、属性和字段。例如，具有`private`可访问性的内容在类外部是不可访问的。正确使用访问修饰符是代码结构良好的标志，通常没有理由绕过这些访问修饰符。

然而，作为模组制作者，我们有时需要访问由于模组制作游戏的性质而无法正常访问的内容。既然我们无法直接在泰拉瑞亚源代码中实现代码，我们就需要使用一种叫做[`反射`](https://learn.microsoft.com/en-us/dotnet/framework/reflection-and-codedom/reflection)的方法来绕过访问限制。

使用`反射`时，我们必须意识到访问不可访问成员的含义。例如，某些被标记为`private`的内容可能是这样的，因为`类`需要与其他字段一起调整该字段以保持代码正常工作。确保研究包含您使用反射访问的成员的代码，以确保行为被维护。

请注意，tModLoader本身会根据对模组制作者的有用程度更改各种成员的访问修饰符。看起来tModLoader可以简单地将泰拉瑞亚的所有内容更改为`public`，但我们不这样做，因为访问修饰符是有用的，可以帮助模组制作者编写正确的代码。如果有有益的更改，请随时提出建议。

最后，请注意，通过使用反射，您的模组可能在tModLoader更新时停止工作。tModLoader开发人员尝试不通过不更改公共方法和字段来破坏模组，但私有方法和字段没有这种保证。

# 基本概念
网上有很多关于"c#反射"的写得很好的教程。本指南不会重复反射的基本概念和信息，而是指导读者查阅这些教程以建立对概念的 基本理解。谷歌"c#反射"并跟随您找到的任何教程，直到您感到有些舒适，然后返回本页查看反射如何在tModLoader中使用。

请注意，本指南尚未更新到.NET 8中添加的新[UnsafeAccessorAttribute](https://learn.microsoft.com/en-us/dotnet/api/system.runtime.compilerservices.unsafeaccessorattribute?view=net-8.0)反射方法，本指南将在稍后更新该信息。

## 访问私有字段
在`NPC`中有一个名为`defaultSpawnRate`的静态字段是`private`。我们想要读取该值。首先，我们使用反射来获取`FieldInfo`。`FieldInfo`是一个特殊的类，便于访问字段。
```cs
FieldInfo NPCDefaultSpawnRateFieldInfo = typeof(NPC).GetField("defaultSpawnRate", BindingFlags.NonPublic | BindingFlags.Static);
```
接下来，我们使用`FieldInfo`访问值。由于字段是`static`，我们传入`null`作为`object`参数。
```cs
Main.NewText("defaultSpawnRate is " + NPCDefaultSpawnRateFieldInfo.GetValue(null));
```
要设置值，我们使用`SetValue`方法。我们再次传入`null`作为`object`参数。确保您传入的值与字段的`类型`匹配，使用反射时由程序员负责这一点。
```cs
NPCDefaultSpawnRateFieldInfo.SetValue(null, 300);
```

## 访问私有类的私有字段
泰拉瑞亚中的一些类是私有的。在这种情况下，我们必须使用反射来访问类的`类型`，而不是使用`typeof`运算符。要使用`GetType`方法，我们需要一个`Assembly`实例。对于泰拉瑞亚类，我们可以使用`typeof(Mod).Assembly`来检索它。使用 assembly 检索`类型`，然后像往常一样使用反射访问私有字段。
```
var UIGridType = typeof(Mod).Assembly.GetType("Terraria.ModLoader.UI.Elements.UIGrid");
FieldInfo _innerListFieldInfo = UIGridType.GetField("_innerList", BindingFlags.Instance | BindingFlags.NonPublic);
```

## 对其他模组成员的反射使用
在处理其他模组时，反射以2种方式使用。

第一种方式是我们之前所做的访问`private`成员。这对于具有对目标模组的[强引用或弱引用](https://github.com/tModLoader/tModLoader/wiki/Expert-Cross-Mod-Content#strong-references-aka-modreferences-expert)的模组很有用。

第二种方式是在完全没有任何引用的情况下访问其他模组成员的[无引用方法](https://github.com/tModLoader/tModLoader/wiki/Expert-Cross-Mod-Content#no-references-aka-reflection-expert)。在这种情况下，您的代码无法访问字段不是因为它们是私有的，而是因为它们可能存在也可能不存在。这可用于松散的跨模组兼容性，例如某个模组在类中寻找特定命名的字段来驱动某些效果。

其他模组的反射与上面相同，但我们需要访问包含`类型`的`模组`的`程序集`。在用`ModLoader.GetMod`或`ModLoader.TryGetMod`检索`模组`实例后，`模组`类的`代码`字段就是我们需要的`程序集`对象。

```cs
Mod ExampleMod = ModLoader.GetMod("ExampleMod");
FieldInfo ExampleMagicMirrorItemNameCycleColorsFieldInfo = ExampleMod.Code.GetType("ExampleMod.Content.Items.Tools.ExampleMagicMirror").GetField("itemNameCycleColors");

Color[] MyItemNameCycleColors = [
	Color.Red,
	Color.Orange,
	Color.Yellow,
];
ExampleMagicMirrorItemNameCycleColorsFieldInfo.SetValue(null, MyItemNameCycleColors);
```

# 清晰的代码
使用反射时，有时代码可能会变得有点冗长。本指南中的代码示例是为了简单起见而一次性访问或设置反射成员而编写的。如果要多次访问成员，使用其他编程方法会很有用。

## 存储MemberInfo实例
不要每次代码应该访问私有成员时都使用`GetField`/`GetMethod`/`GetProperty`，您可以将`MemberInfo`存储为类中的字段并重用它。这应该为了效率和方便而这样做。反射很慢，所以只检索一次`MemberInfo`并重用它将加快需要使用反射的代码。

`TODO：示例`
