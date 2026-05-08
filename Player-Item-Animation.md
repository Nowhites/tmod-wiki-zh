# 概述

玩家物品动画乍一看很简单，但有几个问题。具体来说，齿轮步枪是如何工作的？为什么在使用某些武器后短时间内无法切换物品？所有这些问题将在下面解答。

简单概述术语和系统：
- `动画` 掌管视觉效果。
  - 对于近战武器，碰撞箱随动画移动，命中率也随动画重置，因此这会影响dps
- `使用` 掌管武器的效果（射击、采矿、建造、消费等）
- `速度` 指的是统一影响动画和使用的乘数
  - 这就是为什么默认情况下，`攻击速度`也会增加使用速度（射速、采矿速度等）
- 增加速度的武器前缀直接修改`物品.使用时间`和`物品.动画时间`，从这个意义上说它们是'速度'修改器，但由于它们直接影响物品属性，而不是创建一个单独的乘数，所以它们与`使用速度`钩子和玩家`攻击速度`属性不同
- 对于某些物品（如工具），可能希望`攻击速度`和`使用速度`只影响武器的动画。tML提供了`物品.攻击速度仅影响武器动画`来实现此目的。
  - 注意，由于某种历史原因，原版在大多数近战弹射武器上设置了这个（如光束剑）。在1.4.4的'真近战'重做之前，这更为普遍。

## itemTime、itemAnimation和reuseDelay
为了跟踪玩家何时使用物品，游戏在`玩家`中使用三个变量：`itemTime`、`itemAnimation`和`reuseDelay`。

### itemTime
`玩家.itemTime`决定玩家当前持有物品的"使用"将持续多长时间。当此值达到零且`玩家.itemAnimation`尚未达到零时，物品的使用代码将重复。

原版中的大多数代码，包括放置方块，首先检查`玩家.itemTime == 0`（以及其他条件），然后设置`玩家.itemTime = 玩家.使用时间;`（加上或不调用`玩家钩子.总使用时间()`或使用速度修改器，如`玩家.墙壁速度`）（如果是真的话）。
之后，物品的实际"使用代码"将运行，游戏将等待直到`玩家.itemTime == 0 && 玩家.itemAnimation > 0`（以及其他取决于物品的条件）为真。

示例（`物品.射击 > 0`代码）：
```cs
if (item.type == 2223)
    shoot = 357;

itemTime = PlayerHooks.TotalUseTime(item.useTime, this, item);
Vector2 vector = RotatedRelativePoint(MountedCenter);
bool flag9 = false;
```

### itemAnimation
`玩家.itemAnimation`与`玩家.itemAnimationMax`一起用于确定玩家在使用动画中的进度。
任何物品使用都包裹在`玩家.itemAnimation > 0`的检查中，因此此字段驱动使用。

**值得注意的情况：**
- 如果玩家持有物品的`使用时间`**等于**其`使用动画`，则没有什么特别的事情发生。
- 如果玩家持有物品的`使用时间`**小于**其`使用动画`，由于前面小节中提到的原因，物品使用代码将在动画期间被调用多次。
- 如果玩家持有物品的`使用时间`**大于**其`使用动画`，则玩家将无法切换物品或再次使用物品，直到`玩家.itemTime`达到零，这将在使用动画结束*之后*发生。
  - 但是，如果`玩家.itemAnimation`达到0时`玩家.reuseDelay`大于0，则`玩家.itemTime`中的剩余计时器将被覆盖。

示例（`物品.射击 > 0`检查）：
```cs
flag8 = flag8 && ItemLoader.CheckProjOnSwing(this, item);
if (item.shoot > 0 && itemAnimation > 0 && itemTime == 0 && flag8) {
    int shoot = item.shoot;
    // ...
```

作为参考，这是`ItemLoader.CheckProjOnSwing(玩家, 物品)`的作用：
```cs
public static bool CheckProjOnSwing(Player player, Item item){
    return item.modItem == null || !item.modItem.OnlyShootOnSwing || player.itemAnimation == player.itemAnimationMax - 1;
}
```
基本上，它确保玩家刚刚开始物品使用动画。
使用`物品.使用样式 = 5;`的物品绕过上述方法调用。

### reuseDelay
`玩家.reuseDelay`是强制玩家在使用物品后多等一会儿的预期方式（通过`物品.重用延迟`）。
但是，对于`物品.近战`、`物品.创建瓦片 > 0`或`物品.创建墙 > 0`为真的任何物品，此字段**被忽略**。

如果玩家持有物品的`物品.重用延迟 > 0`为真，则在检查玩家是否刚刚点击"使用物品"按钮（通常为鼠标左键）之前，会运行以下代码：
```cs
// 在 Player.ItemCheck(int) 中找到
if (itemAnimation == 0 && reuseDelay > 0){
    itemAnimation = reuseDelay;
    itemTime = reuseDelay;
    reuseDelay = 0;
}
```

因此，玩家必须等待这个额外时间，并且不会发生额外的物品使用，因为在上述代码片段运行后`玩家.itemAnimation > 0 && 玩家.itemTime == 0`永远不会为真。

### itemTime与itemAnimation
物品动画（`itemAnimation`）不一定与物品使用时间（`itemTime`）匹配或同步。例如，铁镐的`使用动画 = 20`和`使用时间 = 13`。这意味着在铁镐的2次视觉挥动（2 * 20，或40帧）期间，铁镐将完成3次使用（3 * 13，或39帧），并开始在第40帧的第4次使用。

物品被动画化并不一定意味着物品正在被"使用"。例如，当挥动镐时，如果用户在空位置点击，物品将动画化但`itemTime`不会受到影响。如果用户在可采矿的方块上点击，两者将同时设置。如果用户在挥动过程中将鼠标从空位置移动到可采矿的方块，使用甚至可能与动画不同步。

有些类型的物品需要手动触发"物品使用"。弹射武器、瓦片、墙、工具、药水和一些其他物品在用户点击时都会自动触发物品使用，但不属于正常类别的物品可能不会触发"物品使用"。

例如，`ExampleManaCrystal`和`ExampleLifeFruit`在玩家点击且`可以使用物品`为`真`时都会播放动画，但`使用物品`的结果决定游戏是否将物品视为已被使用，最终设置`itemTime`。`ExampleMagicMirror`是另一个例子，它在`使用样式`方法中调用`玩家.应用物品时间(物品);`来开始物品使用。

### 手动设置itemTime或itemAnimation
有时直接赋值`itemTime`或`itemAnimation`很有用。这通常与持有弹射物一起使用，以告诉游戏玩家当前正在使用物品，应该显示为这样。`玩家.设置伪物品时间`方法方便地同时设置两者。

### 案例研究 - 齿轮步枪
为了更好地说明`itemTime`、`itemAnimation`和`reuseDelay`如何协同工作，请展开并阅读以下部分：

<details><summary>详情</summary><blockquote>

在下面的电子表格中，我们可以看到当玩家使用齿轮步枪物品时，`itemTime`、`itemAnimation`、`reuseDelay`等值随时间（"游戏更新"列）变化。请注意，显示的值是事件发生前的游戏更新开始时的值。

在这个例子中，我们看到当玩家在游戏更新34时点击使用物品，`itemTime`和`itemAnimation`值被设置为其最大值并从中倒数。每次`itemTime`在`itemAnimation`之前达到0时，它都会重置为`itemTimeMax`，从而产生3发连发。一旦`itemTime`和`itemAnimation`在游戏更新46结束时都达到0，`reuseDelay`值就被应用于两者。在更新60期间`reuseDelay`到期后，物品使用再次开始，展开新的连发。

![ClockworkAssualtRifleSpreadsheet](https://github.com/user-attachments/assets/bac681ec-bb3d-4b1e-94dd-8b8fc0c43fb9)

</blockquote></details>

## 有用的属性
tModLoader提供了几个封装`玩家.itemTime`和`玩家.itemAnimation`的属性，以允许mod制作者编写更容易理解和更具表现力的代码。尽可能使用这些。这将帮助您避免拼写错误和逻辑错误。

### 玩家.物品动画刚刚开始
如果物品动画处于第一帧，则计算为`真`。等效于`itemAnimation == itemAnimationMax && itemAnimation > 0`。

### 玩家.物品时间为零
如果物品使用处于第一帧，则计算为`真`。等效于`itemTime == 0`。请注意，此特定属性仅在`使用物品`和`使用动画`钩子中有效。[ExamplePickaxe](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Tools/ExamplePickaxe.cs)使用它来每挥动一次运行一次代码。

### 玩家.物品动画进行中
如果物品动画当前正在运行，则计算为`真`。等效于`itemAnimation > 0`。

### 玩家.物品动画结束或已结束
如果物品动画处于或超过其最后一帧，则返回`真`。这意味着如果玩家点击等）可以在下一帧再次开始。原版用它来取消召唤矛，但不推荐，因为它会在多人游戏中脱节（远程玩家可能在完成挥动时恰好收到新弹射物的数据包）。建议改用ai计数器来控制动画绑定弹射物的生命周期。等效于`itemAnimation <= 1`。[ExampleJoustingLanceProjectile](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleJoustingLanceProjectile.cs)使用了这个。

### 玩家.此动画中物品使用次数
物品在此动画（挥动）中已被使用/发射的次数。[ExampleSpecificAmmoGun](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Weapons/ExampleSpecificAmmoGun.cs#L82)使用它来改变多发连发中每次射击的逻辑。

### 物品.攻击速度仅影响武器动画
决定此武器上的攻击速度修改器是否实际影响其使用时间。默认为`假`，允许攻击速度修改器影响使用时间。设置为`真`以阻止这种情况。在原版中，所有发射弹射物且`noMelee`设置为假的近战武器都使用这个。[ExampleHamaxe](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Tools/ExampleHamaxe.cs)和[ExamplePickaxe](https://github.com/tModLoader/tModLoader/blame/stable/ExampleMod/Content/Items/Tools/ExampleHamaxe.cs)使用它来匹配近战速度不应影响采矿速度的行为。

### 物品.每次使用都射击
如果为`真`，此物品将在武器动画每次播放时发射其弹射物，而不是按使用时间。默认为`假`。[ExampleSwingingEnergySword](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Weapons/ExampleSwingingEnergySword.cs)使用了这个，还有真圣剑、骑士之刃、泰拉之刃等。

### 物品.每次动画使用限制
武器每次动画（挥动、点击等）可以使用（射击等）的次数。默认为`null`。原版中以下物品使用了这个：无限智慧之书、夜辉、 Eventide

### 物品.仅在第一次射击时消耗弹药
决定此物品是否应仅在其每次使用的第一次射击时消耗弹药。默认为`假`。原版中以下物品使用了这个：火焰喷射器、精灵熔化器。

### 物品.仅在最后一次射击时消耗弹药
决定此物品是否应仅在其每次使用的最后一次射击时消耗弹药。默认为`假`。原版中以下物品使用了这个：齿轮步枪、药剂师、Eventide

### 玩家.持续释放和物品.持续释放
"持续释放"的物品是只要用户按住使用按钮就处于使用状态的物品。这些武器通常生成一个"持有弹射物"，它充当武器物品并自行处理生成子弹或其他次级弹射物。持有弹射物中的逻辑由其自身决定是否需要正确计算使用时间和使用动画缩放。例如，星云强化武器生成星云强化弹射物。星云强化弹射物有计时器和逻辑来控制子弹的生成方式。

# 调整使用速度和动画速度
切勿直接动态修改`物品.使用时间`和`物品.使用动画`。tModLoader提供了钩子来协作调整使用时间和动画时间。对于动态效果和配饰，请按设计使用钩子以获得最大兼容性。

### (ModPlayer|ModItem|GlobalItem).使用时间倍增器
允许您更改物品的有效`使用时间`。

### (ModPlayer|ModItem|GlobalItem).使用动画倍增器
允许您更改物品的有效`使用动画`。

### 玩家.获取武器攻击速度
获取玩家对该武器的总攻击速度。考虑物品伤害类的`获取总攻击速度`。

### 玩家.获取总攻击速度
获取玩家对该伤害类的总攻击速度。

### 玩家.获取攻击速度
获取此玩家在此伤害类型上的攻击速度修改器，供编辑使用。[ExampleStatBonusAccessory](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Accessories/ExampleStatBonusAccessory.cs)使用它来增加远程攻击速度。

### 伤害类.近战无速度
这是各种原版仅弹射近战武器使用的伤害类。攻击速度对此伤害类的物品没有影响。

# 其他信息
> 齿轮步枪是如何工作的？

**齿轮步枪**物品的默认值中有以下代码：
```cs
useAnimation = 12;
useTime = 4;
reuseDelay = 14;
```
由于`使用时间`小于`使用动画`，`玩家.itemTime`最终将在强制物品动画继续14帧之前循环`12 / 4 = 3`次。

> 为什么在使用某些武器后短时间内无法切换物品？

在`玩家.更新(int)`中，必须在为10个热键槽检查`玩家输入.触发器.当前`中的触发器之前使`玩家.itemAnimation == 0 && 玩家.itemTime == 0 && 玩家.reuseDelay == 0`为真。
```cs
if (PlayerInput.Triggers.Current.Hotbar1) {
    selectedItem = 0;
    flag7 = true;
}

if (PlayerInput.Triggers.Current.Hotbar2) {
    selectedItem = 1;
    flag7 = true;
}

if (PlayerInput.Triggers.Current.Hotbar3) {
    selectedItem = 2;
    flag7 = true;
}

if (PlayerInput.Triggers.Current.Hotbar4) {
    selectedItem = 3;
    flag7 = true;
}

if (PlayerInput.Triggers.Current.Hotbar5) {
    selectedItem = 4;
    flag7 = true;
}

if (PlayerInput.Triggers.Current.Hotbar6) {
    selectedItem = 5;
    flag7 = true;
}

if (PlayerInput.Triggers.Current.Hotbar7) {
    selectedItem = 6;
    flag7 = true;
}

if (PlayerInput.Triggers.Current.Hotbar8) {
    selectedItem = 7;
    flag7 = true;
}

if (PlayerInput.Triggers.Current.Hotbar9) {
    selectedItem = 8;
    flag7 = true;
}

if (PlayerInput.Triggers.Current.Hotbar10) {
    selectedItem = 9;
    flag7 = true;
}
```

另一方面，如果该条件为假，游戏会检查主键盘上的数字键（`D0`到`D9`）而不是上面设置的触发器。
```cs
if (Main.keyState.IsKeyDown(D1)) {
    selectedItem = 0;
    flag10 = true;
}

if (Main.keyState.IsKeyDown(D2)) {
    selectedItem = 1;
    flag10 = true;
}

if (Main.keyState.IsKeyDown(D3)) {
    selectedItem = 2;
    flag10 = true;
}

if (Main.keyState.IsKeyDown(D4)) {
    selectedItem = 3;
    flag10 = true;
}

if (Main.keyState.IsKeyDown(D5)) {
    selectedItem = 4;
    flag10 = true;
}

if (Main.keyState.IsKeyDown(D6)) {
    selectedItem = 5;
    flag10 = true;
}

if (Main.keyState.IsKeyDown(D7)) {
    selectedItem = 6;
    flag10 = true;
}

if (Main.keyState.IsKeyDown(D8)) {
    selectedItem = 7;
    flag10 = true;
}

if (Main.keyState.IsKeyDown(D9)) {
    selectedItem = 8;
    flag10 = true;
}

if (Main.keyState.IsKeyDown(D0)) {
    selectedItem = 9;
    flag10 = true;
}
```
