# 目的

本教程将解释在屏幕上绘制一个简单UI元素（按钮）所需的最基本内容。绘制一个元素有多个步骤，理解这些步骤是构建更大UI组件的必要基础。

# 高层次流程

下图解释了在屏幕上绘制简单按钮所需遵循的一般流程和类。请注意，直到最后一步你才会在屏幕上实际绘制UI元素。
![ui-process](https://user-images.githubusercontent.com/8439537/80857675-c28f0000-8c08-11ea-95df-39712a2159c3.png)

# 制作按钮

制作按钮非常简单。你需要创建一个新类并继承自原版的 UIElement。
```cs
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Terraria.UI;
using Terraria;
using Terraria.ModLoader;

namespace YourMod.UI
{
    class PlayButton : UIElement
    {
        Color color = new Color(50, 255, 153);

        public override void Draw(SpriteBatch spriteBatch)
        {
            spriteBatch.Draw((Texture2D)ModContent.Request<Texture2D>("Terraria/Images/UI/ButtonPlay"), new Vector2(Main.screenWidth + 20, Main.screenHeight -20) / 2f, color);
        }   
    }
}
```

请注意以下几点：
- 我用这个元素将要呈现的内容来命名我的类。这将是一个显示播放符号的按钮
- spriteBatch.Draw 使用的是原版纹理（播放按钮本身的纹理）
- 传递给 spriteBatch.Draw 的 Vector2 参数使用的是玩家可以看到的_屏幕_坐标，而不是_世界_坐标。UI元素被设置在屏幕中央生成，并带有一点偏移，这样它就不会正好在玩家身上

# 将按钮放入 UIState/画布中

正确的术语（和类）是 UIState。然而，我认为更容易理解的方式是将其想象为艺术家作画的画布。如果你真的想的话，你可以在上面放置数百个按钮。不过_你需要_让你的按钮在其构造函数中接受自定义的起始坐标。否则，所有数百个按钮都会绘制在同一个位置。

由于没有更好的想法，我们把这个按钮要放置的状态/画布称为菜单栏，即使目前它只包含一个按钮。
```cs
using Terraria.UI;

namespace YourMod.UI
{
    class MenuBar : UIState
    {
        public PlayButton playButton;

        public override void OnInitialize()
        {
            playButton = new PlayButton();

            Append(playButton);
        }
    }
}
```

请注意以下几点：
- 我们只使用了一个新制作的播放按钮，它使用 PlayButton 类中的任何默认设置（在屏幕中央绘制）
- 我们必须使用 Append() 将 playButton 添加到 UIState/画布中，以便在之后绘制时能够显示出来

# 将 UIState/画布绘制到屏幕上

现在是时候做有趣的部分了。我们目前有一个放置在 MenuBar UIState/画布上的按钮。现在我们将创建一个副本提供给用户界面类，然后告诉游戏绘制它。可以把 MenuBar UIState/画布想象成原件，用户界面想象成副本，或者把 MenuBar 想象成印章，用户界面想象成墨水。

以下代码需要放在继承自 `ModSystem` 的类文件中。

首先，我们需要用 `[Autoload(Side = ModSide.Client)]` 注解 `ModSystem` 类，以允许模组在服务器上加载而不会出错。我们这样做是为了确保UI代码不会在服务器上执行。服务器上的UI代码会导致错误，因为纹理、字体和其他图形资源无法在该上下文中加载。
```cs
namespace YourMod.UI
{
	[Autoload(Side = ModSide.Client)]
	public class MenuBarSystem : ModSystem
	{
```

接下来，我们将声明一个上面制作的 UIState 变量，它包含我们的单个按钮。
```cs
internal MenuBar MenuBar;
```

接下来，我们将声明一个稍后将使用 MenuBar 的 UserInterface 变量。我们不需要创建这个类或继承自它。
```cs
private UserInterface _menuBar;
```

在 Load() 中，让我们将上述两个变量设置为实际实例，然后将 MenuBar 提供给 UserInterface。如果你在模组加载时没有在这里调用 Activate() 来激活UI元素，游戏启动时将会崩溃。它将抛出"对象引用未设置为对象的实例"错误。
```cs
public override void Load()
{
    MenuBar = new MenuBar();
    MenuBar.Activate();
    _menuBar = new UserInterface();
    _menuBar.SetState(MenuBar);
}
```

我对下一部分不太清楚，但假设它是需要的所以这里也包含它。
```cs
public override void UpdateUI(GameTime gameTime)
{
    _menuBar?.Update(gameTime);
}
```

最后，我们以最复杂的部分结束。有人告诉我所有这些代码都是必需的，但我无法向你解释它是如何工作的。从本质上讲，这就是在我们向用户界面提供所需的一切之后实际进行绘制的地方。如果你不使用这段代码，你的整个UI将会消失！
```cs
public override void ModifyInterfaceLayers(List<GameInterfaceLayer> layers)
{
    int mouseTextIndex = layers.FindIndex(layer => layer.Name.Equals("Vanilla: Mouse Text"));
    if (mouseTextIndex != -1)
    {
        layers.Insert(mouseTextIndex, new LegacyGameInterfaceLayer(
            "YourMod: A Description",
            delegate
            {
                _menuBar.Draw(Main.spriteBatch, new GameTime());
                return true;
            },
            InterfaceScaleType.UI)
        );
    }
}
```

# 效果

此时，你的屏幕应该有一个像这样的按钮。请注意，它目前还不是菜单栏……但这只是暂时的。以后你只需要在上面添加和排列更多的 UIElements 就可以了。

![terraria-button](https://user-images.githubusercontent.com/8439537/80858632-6380b980-8c0f-11ea-950e-c4740ef506c1.png)

好吧，它可能不是世界上最令人兴奋的东西，但现在你应该能够在这个想法的基础上进行迭代，制作你想要的任何元素。

为了快速了解你可以用 UIElements 做什么，创建一个新类，继承自它但暂时不添加任何内容。右键单击类括号内的任意位置，点击快速操作，然后选择自动生成重写。这将允许你找出你可以执行的操作。注意 Draw() 出现了！由于 Draw() 看起来是在绘制一个精灵批次，你应该右键单击 SpriteBatch 并查看它的定义以获取更多想法。如果你仔细观察，你会发现我们可以用很多不同的方式来调用 Draw()。

# 中级示例

最后，如果你需要更多手把手的指导，请查看示例模组的 `ExampleCoinUI`，研究他们如何实现一些UI元素（[点击这里查看](https://github.com/tModLoader/tModLoader/tree/stable/ExampleMod/Common/UI/ExampleCoinsUI)）。请记住我们上面讨论的三个类，你应该能够理解它们的实现方式。你会注意到他们使用其他类来代替 `UIElement`，这些类有助于实现某些UI组件（`UIPanel` 和 `UIImageButton`）。

#

感谢 Scalie 和 absoluteAquarian 帮助我弄清楚这一点。
