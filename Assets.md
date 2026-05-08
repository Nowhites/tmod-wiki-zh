# 资产系统指南

## 概述

在tModLoader中，我们使用`Asset`类来处理资产，如纹理（`Texture2D`）、着色器（`Effect`）、字体（`DynamicSpriteFont`）和声音（`SoundEffect`）。`Asset`和`AssetRepository`类提供按需（异步）加载和资源包支持。

本指南介绍如何最佳地使用资产系统以获得最佳性能和兼容性。

## 一般指南

本节是各种最佳实践的快速概述。请参阅后面的部分以获取每个主题的更多详细信息。

### 存储Asset引用，而不是包含的数据类

您应该始终直接使用`Asset`类，直到需要使用包含的数据。例如，不要存储`Texture2D sprite`并调用`spriteBatch.Draw(sprite)`，而是存储`Asset<Texture2D> sprite`并调用`spriteBatch.Draw(sprite.Value)`。

### 预加载纹理

tModLoader会在mod加载期间请求并加载大多数mod内容的纹理。这种预加载以加载时间和RAM使用为代价，减少了运行时按需请求纹理的需要。

- 这些资产通常可以在`Terraria.GameContent`静态类中找到的数组中访问，例如`TextureAssets`。
- 如果您有额外的纹理，例如NPC或弹幕的特殊效果，您可以在`SetStaticDefaults`或其他加载钩子中请求它们。
- Vanilla不会预加载所有纹理。对于物品、NPC、弹幕、背景和其他一些类型的内容，加载的Asset会填充到`TextureAssets`类的字段中。使用这些纹理需要首先调用相应的`Main.instance.LoadX`方法来确保它们已加载。没有相应`Main.instance.LoadX`的Vanilla纹理很可能是预加载的。

在`PostSetupContent`之后，tModLoader将确保 mod的所有请求资产完全加载，因此在游戏过程中不需要`.Wait()`等待它们。

- 这也意味着在加载期间请求纹理时不需要使用`AssetRequestMode.ImmediateLoad`。这样做只会显著减慢加载过程。

如果您不想预加载额外资产，无论是因为不方便还是想节省RAM或加载时间，您可以在游戏中的任何时候按需`Request`它们。

- 在游戏中请求资产时，仍建议使用`AssetRequestMode.AsyncLoad`以避免降低帧率。

### 避免每帧请求资产

您应该努力只请求一次资产，然后将引用存储在字段中供以后使用。访问字段比调用`Request`方法更高效。这可以在mod加载期间或通过空检查按需完成。对于单例类，将资产存储在实例字段中是足够的。对于具有多个实例的类（如`ModItem`、`ModProjectile`和`ModNPC`），资产需要存储在静态字段中并在`Load`期间请求。不需要在`Unload`中将引用设置为`null`或手动`Dispose` `Asset`。

### 避免使用`AssetRequestMode.ImmediateLoad`

使用`AssetRequestMode.ImmediateLoad`（或调用`.Wait()`）加载资产效率低下，在大多数情况下是不必要的。阅读下面的部分以了解何时可能需要它。

## 获取/请求资产

`ModContent.Request`方法可用于从任何mod或Terraria请求任何资产，但还有其他可以略微减少代码重复的选项。使用`Request`方法时，资产的类型作为泛型参数传入。例如，`ModContent.Request<Texture2D>(pathHere)`请求指定路径的纹理（`Texture2D`）。`ModContent.Request<Effect>(pathHere)`将对着色器（`Effect`）资产执行相同的操作。

## Mod中包含的资产

要加载当前mod中包含的资产，只需调用`Mod`类上的`Assets.Request`方法。任何`ModType`类都可以通过`Mod`属性访问`Mod`实例，因此可以编写`Mod.Assets.Request`。不继承自`ModType`的其他类可以使用`ModContent.GetInstance<ModClass>()`访问`Mod`实例。或者，如果mod作者将`ModName`作为资产路径的一部分，则可以使用`ModContent.Request`方法。

在mod加载期间请求资产，例如在`ModType.Load`或`SetStaticDefaults`方法中，是一个好主意，因为它会在加载时立即抛出错误，而不是在游戏过程中。这样可以让mod制作者更快地注意到拼写错误和其他资产相关错误。

```csharp
public class ExampleHookProjectile : ModProjectile {
	private static Asset<Texture2D> chainTexture;

	public override void Load() {
		// 选项1：直接从Mod类加载资产。Mod名称不是资产路径的一部分。
		chainTexture = Mod.Assets.Request<Texture2D>("Content/Items/Tools/ExampleHookChain");
		// 选项2：直接从通过ModContent.GetInstance检索的Mod类加载资产。Mod名称不是资产路径的一部分。
		chainTexture = ModContent.GetInstance<ExampleMod>().Assets.Request<Texture2D>("Content/Items/Tools/ExampleHookChain");
		// 选项3：使用ModContent.Request加载资产。Mod名称必须在资产路径的开头。
		chainTexture = ModContent.Request<Texture2D>("ExampleMod/Content/Items/Tools/ExampleHookChain");
	}
}
```

## 来自Terraria或其他mod的资产

从其他mod或Terraria加载资产与从您自己的mod加载过程相同，只是需要检索Mod实例或在`ModContent.Request`调用中添加mod名称。对于`ModContent.Request`来说，通常最简单的是直接写出完整路径，而不是处理访问其他mod的`Mod`类。对于Terraria资产，`Terraria/`将被添加到路径中。请注意，Terraria资产遵循安装目录中Contents文件夹的路径。

另请注意，大多数Terraria纹理已经加载到`Terraria.GameContent.TextureAssets`类中，可以直接使用，而不是加载单独的`Asset`。其中许多需要调用特殊方法如`Main.LoadItem`或`Main.LoadNPC`来完全加载，因为它们可能尚未加载。

```csharp
public class AssetTest : ILoadable
{
	private static Asset<Texture2D> moddedTexture;
	private static Asset<Texture2D> terrariaTexture;

	void ILoadable.Load(Mod mod) {
		moddedTexture = ModContent.Request<Texture2D>("ExampleMod/Content/SomeTexture");
		moddedTexture = ModContent.GetInstance<ExampleMod>().Assets.Request<Texture2D>("Content/SomeTexture");

		terrariaTexture = Main.Assets.Request<Texture2D>("Images/UI/Settings_Toggle");
		terrariaTexture = ModContent.Request<Texture2D>("Images/UI/Settings_Toggle");
	}
}
```

## 资产加载时机

Mod可以通过两种方式加载额外资产：mod加载期间或按需。推荐在mod加载期间加载额外资产。

在mod加载期间加载的资产将在mod加载过程结束时完全加载，确保在游戏中需要时可以立即使用。资产加载过程还会在任何资产加载失败时导致mod加载失败。这允许mod制作者快速注意到拼写错误或格式问题，否则这些问题只会显示为游戏内聊天中的错误消息。

在游戏中按需加载的资产的好处是除非需要否则不会在RAM中。它们在游戏会话期间永远不会卸载，但对于某些内容，可能只会在需要时加载资产。按需加载的资产可能需要mod制作者付出更多努力来处理资产加载状态。

## 预加载资产

最常见的方法是在`ModType`或`ILoadable`中拥有`Asset<T>`字段，并在`Load`或`SetStaticDefaults`中请求资产。存储`Asset`的字段对于创建同一类的多个实例的内容（如`ModItem`、`ModProjectile`和`ModNPC`）需要是`static`。在这两种情况下，都不需要在`Unload`中将Asset引用设置为`null`或手动`Dispose` `Asset`。

```csharp
public class ExampleHookProjectile : ModProjectile
{
	private static Asset<Texture2D> chainTexture;

	public override void Load() {
		chainTexture = Mod.Assets.Request<Texture2D>("Content/Items/Tools/ExampleHookChain");
	}
}
```

## 按需资产加载

当按需加载资产时，mod制作者应该尝试调用一次`Request`并将生成的`Asset`存储在字段中。这可以通过空检查完成。

```csharp
public class ExamplePlayerDrawLayer : PlayerDrawLayer
{
	private Asset<Texture2D> exampleItemTexture;

	protected override void Draw(ref PlayerDrawSet drawInfo) {
		// 选项A：常规空检查方法
		if (exampleItemTexture == null) {
			exampleItemTexture = Mod.Assets.Request<Texture2D>("Content/Items/ExampleItem");
		}
		// 选项B：空合并赋值运算符方法
		exampleItemTexture ??= Mod.Assets.Request<Texture2D>("Content/Items/ExampleItem");
		
		// 其他代码
		drawInfo.DrawDataCache.Add(new DrawData(
			exampleItemTexture.Value,
			// 其他代码
		));
	}
}
```

作为`if (texture == null)`检查的替代方法，如果mod制作者熟悉[空合并赋值运算符](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/null-coalescing-operator)语法，可以使用`exampleItemTexture ??= Mod.Assets.Request<Texture2D>("Content/Items/ExampleItem");`。

## 服务器注意事项

mod制作者应该记住，服务器不会加载任何资产。处理资产的大多数钩子不会在服务器上调用。在服务器上请求资产将返回带有`null` `Value`的虚拟`Asset`。不需要用`!Main.dedServ`检查来包装尝试加载资产的代码。

## AssetRequestMode

默认请求模式是`AsyncLoad`。在mod加载期间请求的任何资产将在mod加载完成时完全加载。异步加载允许多个资产在后台并行加载，而游戏继续加载mods或更新/绘制帧。

在游戏过程中异步请求的资产至少需要一帧来加载，但如果资产较大或同时请求多个资产，则可能需要多帧。`Asset<Texture2D>`包含一个`DefaultValue`，其中包含透明的1x1纹理，当资产仍在加载时将从`Asset.Value`返回。这允许mods在渲染中使用`Value`而无需检查资产是否已完成加载。

> \[!NOTE]
> 作为演示，这里是一个在游戏过程中加载数百个物品纹理的mod。当使用`AsyncLoad`时，滚动是平滑的，物品纹理在加载时出现。当使用`ImmediateLoad`时，滚动会中断，游戏的帧率会受到影响，因为游戏花费时间等待纹理加载。这是一个极端示例，因为它一次加载数百个物品纹理，对于正常的mod内容，加载的纹理会不那么明显。

某些情况下可能需要立即加载资产。最常见的情况是需要纹理尺寸进行UI布局时（如在`UIElement`/`UIState`类中）。如果元素使用尚未加载的资产初始化，则会使用临时透明1x1纹理的尺寸，结果用户界面将布局错误。建议在mod加载期间异步加载纹理，并稍后初始化`UIState`，但可以将`AssetRequestMode.ImmediateLoad`传递给`Request`，或者可以调用`Asset.Wait()`来确保资产已加载。

```csharp
Asset<Texture2D> buttonTexture = ModContent.Request<Texture2D>("ExampleMod/UI/Button", AssetRequestMode.ImmediateLoad);
UIImageButton button = new UIImageButton(buttonTexture);
```

mod制作者不应使用`AssetRequestMode.DoNotLoad`，而应遵循上面的按需加载建议。Terraria使用`DoNotLoad`用`Asset<Texture2D>`条目填充`Terraria.GameContent.TextureAssets`字段，这些条目仅在需要时加载。这需要辅助方法检查资产是否已加载并在需要时重新请求，如`Main.LoadItem/LoadNPC/LoadProjectile`。

## 其他资产方法

### Asset.IsLoaded

使用`Asset.IsLoaded`检查资产是否已完成加载。

### Asset<Texture2D>.Size()

此辅助方法比调用`Asset.Value.Size()`更简短，对于尚未完全加载的资产将返回`Vector2.Zero`。

### Asset.Wait()

`Asset.Wait()`方法可用于强制立即加载先前请求的纹理。

### 从字节创建Asset（AssetRepository.CreateUntracked）

某些mod需要从其他来源生成资产，例如从网络服务加载图标。mod制作者可以使用`AssetRepository.CreateUntracked`方法从包含`png/xnb/fxc/ogg/wav`等的`Stream`创建`Asset`。生成的`Asset`是"未跟踪"的，因为无法通过名称通过`Request`检索它。

### Vanilla代码适配注意事项

如果您复制了使用`AssetRepository.Request`的Terraria代码，您可能会注意到vanilla代码的行为就像资产是立即加载的一样，即使默认请求模式是`AsyncLoad`。这是因为有一个特殊的`internal`重载`AssetRepository.Request`，默认使用`ImmediateLoad`，以保持vanilla代码正常工作，而我们为mod制作者和`ModContent.Request`更改了默认值。
