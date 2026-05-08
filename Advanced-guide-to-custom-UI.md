# 简介
本指南旨在教你如何利用泰拉瑞亚中存在的各种UI类来制作自己的UI。在学习本指南之前，你应该了解以下主题：

* 类层次结构和继承
* 抽象/虚方法和重写
* 面向对象编程（与类继承相关）

# 简要说明
泰拉瑞亚的UI由各种类组成，其中最重要的是：UIElement、UIState和UserInterface。我们将逐一介绍每个类以及在创建UI过程中各自的重要性。

# 对象关系
自定义UI不过是一个自定义的`UserInterface`，其状态被设置为一个自定义的`UIState`。通常，一个UserInterface可以有一个_活动_的UIState（它可以保存状态历史记录，最多达到上限，之后你可以返回），该状态随后在该UserInterface中被显示。

# UserInterface
没有理由创建一个继承自`UserInterface`的自定义类。（虽然你可以，因为这个类不是密封的）在大多数情况下，只需在你的ModSystem类中声明一个UserInterface类型的新字段。我们稍后会回到这个话题。

# UIState
`UIState`代表一个界面所处的状态。这个类本身非常简单：它实际上是一个跨越整个屏幕宽度和高度的`UIElement`，允许你在屏幕上的任何位置添加元素。

# UIElement
`UIElement`代表一个可以是界面一部分的任何类型的UIElement。原版示例有`UIPanel`、`UIImage`和`UIImageButton`类。每一个都有特定的功能。（分别显示背景面板、显示图像或显示可点击的按钮图像）你可以通过让你的类继承自UIElement来制作你自己的自定义元素。我们稍后会回到这个话题。

# 初始设置
首先，你需要创建一个继承自`ModSystem`的类，你可以使用现有的或创建一个新的：

```cs
class MyUISystem : ModSystem { }
```

然后你需要创建一个UserInterface和UIState。你可以在你的ModSystem类中定义这些新字段：

```cs
internal UserInterface MyInterface;
```

对于你的UIState，你应该创建一个新的自定义类：

```cs
class TheUI : UIState { }
```

然后在你的ModSystem类中为它创建一个字段：

```cs
internal TheUI MyUI;
```

## 初始化UI
因为UI只有玩家才能看到，你不应该在服务器上初始化UI。这会浪费资源，而且服务器不玩游戏也不看任何图形。只在客户端初始化。在你的`ModSystem.Load()`中，初始化你的UI：

```cs
if (!Main.dedServ) {
    MyInterface = new UserInterface();

    MyUI = new TheUI();
    MyUI.Activate(); // Activate调用UIState上的Initialize()（如果尚未初始化），然后调用OnActivate，并在每个子元素上调用Activate。
}
```

在你的`ModSystem.Unload()`中，你可以调用你的UI可能需要的任何卸载操作，然后将其设置为null：

```cs
MyUI?.SomeKindOfUnload(); // 如果你的UI持有需要卸载的数据，以面向对象的方式调用它
MyUI = null;
```

如上所示的UI上的Unload不被推荐，因为让UI负责数据通常是不好的做法。但是，如果你的UI持有静态引用（如纹理），这很有用。


## 更新和绘制UI
设置你的界面状态很好，但UI不会神奇地调用自己更新或绘制。要做到这一点，你必须重写`ModSystem.UpdateUI(Gametime gameTime)`和`ModSystem.ModifyInterfaceLayers(List<GameInterfaceLayer> layers)`：

```cs
private GameTime _lastUpdateUiGameTime;

public override void UpdateUI(GameTime gameTime) {
    _lastUpdateUiGameTime = gameTime;
    if (MyInterface?.CurrentState != null) {
        MyInterface.Update(gameTime);
    }
}
```
上面的代码片段将对你的界面调用.Update，并将其传播到其状态和底层元素。

```cs
public override void ModifyInterfaceLayers(List<GameInterfaceLayer> layers) {
    int mouseTextIndex = layers.FindIndex(layer => layer.Name.Equals("Vanilla: Mouse Text"));
    if (mouseTextIndex != -1) {
        layers.Insert(mouseTextIndex, new LegacyGameInterfaceLayer(
            "MyMod: MyInterface",
    	    delegate
            {
                if ( _lastUpdateUiGameTime != null && MyInterface?.CurrentState != null) {
                    MyInterface.Draw(Main.spriteBatch, _lastUpdateUiGameTime);
                }
                return true;
            },
            InterfaceScaleType.UI));
    }
}
```
上面的代码片段添加了一个自定义层到原版层列表中，如果界面有状态，将对你的UI调用.Draw。这将使你的UI实际绘制并显示在屏幕上。将InterfaceScaleType设置为UI以进行适当的UI缩放。

- 注意，在这种情况下，你是在"Mouse Text"层下方添加你的UI层，这意味着你的UI将在该层之前绘制。你可以通过查找具有其他名称的层来将你的自定义层添加到其他层（无论是原版还是非原版）旁边。原版界面层的列表可以在这里找到。

## 显示UI
出于测试目的，建议添加一个热键或其他易于访问的方式来切换UI。要显示你的UI，你需要将界面的状态设置为你的UI实例。你应该通过访问你的ModSystem实例并调用.SetState来实现：

```cs
MyUISystem uiSystemInstance = ModContent.GetInstance<MyUISystem>();
uiSystemInstance.MyInterface.SetState(uiSystemInstance.MyUI);
```

这个代码片段将把状态设置为你的UI，导致它显示。如果你想隐藏UI，只需将null传入方法调用即可。

你可以理解为什么使用专用类来管理你的UI是有用的。制作这样的辅助方法很有用：

```cs
internal void ShowMyUI() {
    MyInterface?.SetState(MyUI);
}

internal void HideMyUI() {
    MyInterface?.SetState(null);
}
```

然后简单地调用它们：

```cs
ModContent.GetInstance<MyUISystem>().ShowMyUI();
ModContent.GetInstance<MyUISystem>().HideMyUI();
```


## 状态历史
界面会跟踪状态历史。如果你想返回上一个状态，可以调用`MyInterface.GoBack()`，如果历史记录中有的话，这将激活上一个状态。请记住，它会从历史记录中移除激活的状态，所以你需要调用.SetState或.AddToHistory来将其放回。历史记录最多保存32个状态，如果你已经添加了32个新状态到历史记录，最旧的4个状态会被移除。如果你有一个渐进式UI（例如分页或标签），状态历史会很有用，因为你的界面的状态不断变化。

# 添加元素
如果你现在激活你的UI，你可能会感到困惑，因为什么都没有显示。这是因为你的UI是空的。
你可以向UIState添加任何你想要的元素，它们会显示出来。

在你的MyUI类中，重写`OnInitialize`方法。例如，添加一个新的UIPanel到状态：

```cs
public override void OnInitialize() { // 1
    UIPanel panel = new UIPanel();    // 2
    panel.Width.Set(300, 0);          // 3
    panel.Height.Set(300, 0);         // 3
    Append(panel);                    // 4
}
```
1) 重写OnInitialize方法。当我们的模组加载时，这会被调用来初始化UI。
2) 创建一个新的UIPanel实例。这是一个原版类，将在这个元素上绘制一个原版样式的背景。
3) 设置宽度和高度为大一点的值，以便我们能看到它。
4) 将面板追加到我们的UIState。Append是UIElement类上的一个方法，它允许你向那个元素添加子元素。子元素的放置是相对于那个元素的。因为UIState覆盖整个屏幕，我们的UIPanel将显示在屏幕的左上角原点。


## 添加一些文本
让我们通过向UIPanel添加一些文本来增添趣味。我们可以以相同的方式进行，但这次将元素追加到我们的面板变量：

```cs
public override void OnInitialize() {
    UIPanel panel = new UIPanel();
    panel.Width.Set(300, 0);
    panel.Height.Set(300, 0);
    Append(panel);

    UIText text = new UIText("Hello world!"); // 1
    panel.Append(text);                       // 2
}
```
1) 初始化UIText。
2) UIText元素现在是UIPanel元素的子元素。文本应该显示在UIPanel中。

## 居中元素
你大部分时间将花在将元素放置在UI中的完美位置。你将使用UIElement类上的各种字段：

```cs
public StyleDimension Top;
public StyleDimension Left;
public StyleDimension Width;
public StyleDimension Height;
public StyleDimension MaxWidth = StyleDimension.Fill;
public StyleDimension MaxHeight = StyleDimension.Fill;
public StyleDimension MinWidth = StyleDimension.Empty;
public StyleDimension MinHeight = StyleDimension.Empty;
public bool OverflowHidden;
public float PaddingTop;
public float PaddingLeft;
public float PaddingRight;
public float PaddingBottom;
public float MarginTop;
public float MarginLeft;
public float MarginRight;
public float MarginBottom;
public float HAlign;
public float VAlign;
```

居中元素是模组开发者常见的用例。相对于其父元素居中一个元素很容易。要将文本在我们的UIPanel中居中，我们可以使用HAlign和VAlign，并将两者都设置为0.5f：

```cs
public override void OnInitialize() {
    UIPanel panel = new UIPanel();
    panel.Width.Set(300, 0);
    panel.Height.Set(300, 0);
    Append(panel);

    UIText text = new UIText("Hello world!");
    text.HAlign = 0.5f; // 1
    text.VAlign = 0.5f; // 1
    panel.Append(text);
}
```
这基本上是将我们的水平和垂直对齐设置为50%，居中我们的元素。

### 类似标题的文本
我们可以用这些对齐技巧来完美地将我们的文本对齐得像一个标题，如果我们想要的话。我们可以将HAlign设置为50%来水平居中我们的文本，我们可以将VAlign设置为一个低值，或者通过设置Top位置设置一个固定的绝对值。后者是推荐的：

```cs
public override void OnInitialize() {
    UIPanel panel = new UIPanel();
    panel.Width.Set(300, 0);
    panel.Height.Set(300, 0);
    Append(panel);

    UIText header = new UIText("My UI Header");
    header.HAlign = 0.5f;  // 1
    header.Top.Set(15, 0); // 2
    panel.Append(header);
}
```
1) 将水平对齐设置为50%
2) 将Top位置设置为15像素。文本应该相对于UIPanel向下15像素。请记住，你可以为此使用VAlign，但它会随着你的UIPanel大小变化而缩放。当你改变UIPanel的大小时，这可能会搞乱东西。


### 将UIPanel居中
你的UI可能仍然显示在UIState的左上角。大多数模组开发者希望他们的UI显示在屏幕中央。因为UIState覆盖整个屏幕，我们可以使用HAlign和VAlign技巧来居中我们的UIPanel：

```cs
public override void OnInitialize() {
    UIPanel panel = new UIPanel();
    panel.Width.Set(300, 0);
    panel.Height.Set(300, 0);
    panel.HAlign = panel.VAlign = 0.5f; // 1
    Append(panel);
}
```

1) 这是一个设置两个字段为相同值的简洁技巧。

但是，如果你的UIState改变了覆盖屏幕的方式，或者如果你的UIPanel本身没有附加到UIState上，这个技巧将不起作用。注意你的UIPanel的父元素并相应地对齐它。


# 与UIElement的交互
模组开发者最常见的交互是当点击某个物品时做些什么，比如一个按钮。
让我们向带有交互的UI添加一个按钮：

```cs
public override void OnInitialize() {
    UIPanel panel = new UIPanel();
    panel.Width.Set(300, 0);
    panel.Height.Set(300, 0);
    Append(panel);

    UIText header = new UIText("My UI Header");
    header.HAlign = 0.5f;
    header.Top.Set(15, 0);
    panel.Append(header);

    UIPanel button = new UIPanel();   // 1
    button.Width.Set(100, 0);
    button.Height.Set(50, 0);
    button.HAlign = 0.5f;
    button.Top.Set(25, 0);            // 2
    button.OnLeftClick += OnButtonClick;  // 3
    panel.Append(button);

    UIText text = new UIText("Click me!");
    text.HAlign = text.VAlign = 0.5f; // 4
    button.Append(text);              // 5
}

private void OnButtonClick(UIMouseEvent evt, UIElement listeningElement) {
    // 我们可以在这里做事情！
}
```
1) 初始化一个新的UIPanel。因为没有UIButton类（只有UIImageButton），我们将使用UIPanel和UIText来代替。
2) 将top位置设置在标题下方一点。
3) 添加新的OnClick事件。请注意，事件会通过父子链传播。这意味着如果我们点击UIText，点击事件最终也会落在我们的UIPanel（按钮）上，因为它是一个父元素。因此，我们只需要向UIPanel添加处理程序。
4) 设置文本对齐以便它在按钮面板中居中。
5) 将文本追加到我们的按钮。按钮被追加到我们的背景面板。

OnButtonClick目前什么都不做。让我们改变我们的文本。为了做到这一点，我们必须使按钮文本成为一个类级别的字段，而不是仅限定在OnInitialize方法中：

```cs
private UIText text; // 稍后初始化

public override void OnInitialize() {
    // ... code
    text = new UIText("Click me!");
    // ... other code
}
```
现在我们可以在OnButtonClick方法中访问text：

```cs
private void OnButtonClick(UIMouseEvent evt, UIElement listeningElement) {
    text.SetText("I was clicked!");
}
```

### 事件
有许多像OnLeftClick这样的事件，列表如下：

```cs
public event UIElement.MouseEvent OnLeftMouseDown;
public event UIElement.MouseEvent OnLeftMouseUp;
public event UIElement.MouseEvent OnLeftClick;
public event UIElement.MouseEvent OnLeftDoubleClick;
public event UIElement.MouseEvent OnRightMouseDown;
public event UIElement.MouseEvent OnRightMouseUp;
public event UIElement.MouseEvent OnRightClick;
public event UIElement.MouseEvent OnRightDoubleClick;
public event UIElement.MouseEvent OnMouseOver;
public event UIElement.MouseEvent OnMouseOut;
public event UIElement.ElementEvent OnUpdate;
public event UIElement.ScrollWheelEvent OnScrollWheel;
public event UIElement.MouseEvent OnMiddleMouseDown;
public event UIElement.MouseEvent OnMiddleMouseUp;
public event UIElement.MouseEvent OnMiddleClick;
public event UIElement.MouseEvent OnMiddleDoubleClick;
public event UIElement.MouseEvent OnXButton1MouseDown;
public event UIElement.MouseEvent OnXButton1MouseUp;
public event UIElement.MouseEvent OnXButton1Click;
public event UIElement.MouseEvent OnXButton1DoubleClick;
public event UIElement.MouseEvent OnXButton2MouseDown;
public event UIElement.MouseEvent OnXButton2MouseUp;
public event UIElement.MouseEvent OnXButton2Click;
public event UIElement.MouseEvent OnXButton2DoubleClick;
```

### 防止鼠标点击使用选中的物品
默认情况下，UI不会阻止鼠标点击导致玩家使用他们当前选择的物品。要实现此行为，必须将`Player.mouseInterface`布尔值设置为true。这应该在`DrawSelf`方法中完成。逻辑应该应用到有意义的最低父元素。不要将其应用到UIState本身，因为UIState覆盖整个屏幕，而是将其应用到添加到UIState的面板或容器。

```cs
protected override void DrawSelf(SpriteBatch spriteBatch) {
    base.DrawSelf(spriteBatch);
    // 如果这段代码在面板或容器元素中，直接检查它
    if (ContainsPoint(Main.MouseScreen)) {
        Main.LocalPlayer.mouseInterface = true;
    }
    // 否则，我们可以检查一个子元素
    if (panel.ContainsPoint(Main.MouseScreen)) {
        Main.LocalPlayer.mouseInterface = true;
    }
}
```

### 防止滚轮切换选中的热栏物品
默认情况下，UI不会阻止滚轮切换选中的热栏物品。要实现此行为，应该调用`Terraria.GameInput.PlayerInput.LockVanillaMouseScroll`方法。这应该在`DrawSelf`方法中完成。逻辑应该应用到有意义的最低父元素。不要将其应用到UIState本身，因为UIState覆盖整个屏幕，而是将其应用到添加到UIState或其他容器的可滚动列表或面板。

```cs
protected override void DrawSelf(SpriteBatch spriteBatch) {
    base.DrawSelf(spriteBatch);
    // 如果这段代码在可滚动元素中，直接检查它
    if (IsMouseHovering) {
        PlayerInput.LockVanillaMouseScroll("MyMod/ScrollListA"); // 传入的字符串可以是任何内容。
    }
    // 否则，我们可以检查一个子元素
    if (list.IsMouseHovering) {
        PlayerInput.LockVanillaMouseScroll("MyMod/ScrollListB"); // 传入的字符串可以是任何内容。
    }
}
```

# 悬停时显示工具提示
模组开发者常用的另一个有用的交互是悬停元素时显示工具提示。
这可以通过重写DrawSelf方法并检查鼠标是否悬停在元素上来实现（请注意，如果你重写了DrawSelf，你也需要在其中调用base.DrawSelf），然后更改Main.hoverItemName。让我们在悬停按钮时显示这个工具提示：（你需要像我们之前对文本做的那样，使button成为类级别的字段）

```cs
 protected override void DrawSelf(SpriteBatch spriteBatch) {
    base.DrawSelf(spriteBatch); // 这确保Draw调用被传播到子元素
    if (text.IsMouseHovering || button.isMouseHovering) {
        Main.hoverItemName = "Click to see what happens";
    }
}
```
请注意，以这种方式修改鼠标文本仅在`Main.hoverItemName`在实际渲染`"Vanilla: Mouse Text"`GameInterfaceLayer之前设置时才有效。如果你没有修改本wiki页面开头提出的`ModifyInterfaceLayers`实现，情况就是这样。如果你在该层上方显示UI，`Main.hoverItemName = [...]`应该移动到更早被调用的地方，但如果可能的话，在所有更新方法调用之后再移动，因为原版可能会删除你的文本内容。

# UI显示或隐藏时的交互
重写方法.OnActivate()和.OnDeactivate()分别做UI激活或停用时的事情。例如，activate可用于检索最新数据来填充UI，deactivate可重置存储此数据的变量。使用deactivate使你的UI为下一次激活做好准备，因此它在不使用时占用更少的内存。（理想情况下将内容设置为null以释放内存）

# 自定义UIElement
制作你自己的元素很容易。你必须制作一个继承`UIElement`类的自定义类。OnInitialize、OnActivate、OnDeactivate、DrawSelf和Update方法将是主要重写的方法。

一个简单的例子是制作我们自己的自定义按钮类来方便我们上面做的事情。
让我们从基础开始：首先定义我们的类是什么以及应该做什么。
按钮类必须显示一些文本并且可点击，当点击时可以执行分配的动作。
很好，现在我们知道了类的用例，我们可以建模它：

```cs
public class UIClickableButton : UIElement {

    // 1
    private object _text;
    private UIElement.MouseEvent _clickAction;
    private UIPanel _uiPanel;
    private UIText _uiText;

    // 2
    public string Text 
    {
        get => _uiText?.Text ?? string.Empty; // 3
        set => _text = value;
    }

    public UIClickableButton(object text, UIElement.MouseEvent clickAction) : base() { // 4
        _text = text?.toString() ?? string.Empty;
        _clickAction = clickAction;
    }

    public override void OnInitialize() { 
        _uiPanel = new UIPanel();               // 5
        _uiPanel.Width = StyleDimension.Fill;   // 5
        _uiPanel.Height = StyleDimension.Fill;  // 5
        Append(_uiPanel);

        _uiText = new UIText("");               // 6
        _uiText.VAlign = _uiText.HAlign = 0.5f; // 6
        _uiPanel.Append(_uiText);

        _uiPanel.OnLeftClick += _clickAction;       // 7
    }

    public override void Update(GameTime gameTime) {
        base.Update(gameTime); // 将更新传播到子元素。
        if (_text != null) {                    // 8
            _uiText.SetText(_text.ToString());
            _text = null;
            Recalculate();                      // 9
            base.MinWidth = _uiText.MinWidth;   // 9
            base.MinHeight = _uiText.MinHeight; // 9
        }
    }
}
```
有很多事情在进行，让我们看看：
1) 我们定义自定义按钮需要的变量。
2) 一个公共属性，我们可以用它来获取和设置文本后备字段。在第8点有更多相关内容。
3) 简单返回_uiText的文本，如果它为null则返回空字符串。（尚未初始化）
4) 我们的构造函数。我们必须传入一个文本和一个点击动作。文本是object类型，这是模仿UIText类的建模。
5) 创建一个新的UIPanel作为基础背景。我们将大小设置为Fill，这等于调用Set(0, 1f); 1f代表100%，所以在这种情况下，我们会将UIPanel拉伸到我们制作这个元素的大小。
6) 创建一个新的UIText，我们将其在我们的UIPanel中居中对齐。
7) 将点击动作注册到UIPanel。请记住，子元素上的点击事件会通过父子链传播，所以点击事件最终会落在我们的UIPanel OnClick处理程序上。
8) 通过在Update期间更新UIText的文本，我们可以使我们的文本更改是线程安全的，并避免在绘制文本时编辑文本时出错。
9) Recalculate强制此元素及其子元素重新计算大小、填充等。如果内容发生变化，例如在这种情况下，你应该这样做。我们可以在Recalculate期间复制计算的最小宽度和最小高度，因为UIText类在这种情况下会计算它们。

现在我们可以使用这个类，而不是我们之前做的：

```cs
private UIClickableButton _button;

public override void OnInitialize() {
    UIPanel panel = new UIPanel();
    panel.Width.Set(300, 0);
    panel.Height.Set(300, 0);
    Append(panel);

    UIText header = new UIText("My UI Header");
    header.HAlign = 0.5f;
    header.Top.Set(15, 0);
    panel.Append(header);

    _button = new UIClickableButton("Click me!", OnButtonClick);
    _button.Width.Set(100, 0);  
    _button.Height.Set(50, 0);
    _button.HAlign = 0.5f;
    _button.Top.Set(25, 0);
     panel.Append(_button);
}

private void OnButtonClick(UIMouseEvent evt, UIElement listeningElement) {
    _button.Text = "I was clicked!";
}
```
