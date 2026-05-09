这是存储在 `ModSystem.ModifyInterfaceLayers(List<GameInterfaceLayer> layers)` 的 layers 参数中的原版界面层列表，按它们出现的顺序排列。

为了使用这些，写入您的代码如下：
```cs
int mouseItemIndex = layers.FindIndex(layer => layer.Name == "Vanilla: Mouse Item / NPC Head");
if (mouseItemIndex != -1) layers.Insert(mouseItemIndex, ...);
```

| 界面层 | 描述 |
| :--- | :--- |
| Vanilla: Interface Logic 1 | 处理在使用持有在光标上的物品时与鼠标输入相关的逻辑。 |
| Vanilla: MP Player Names | 绘制其他玩家的名称、距离、健康文本和头像图标。 |
| Vanilla: Emote Bubbles | 为 NPC 和玩家绘制表情气泡，以及悬停在 NPC 上时绘制 NPC 聊天气泡。 |
| Vanilla: Entity Markers | 为手动指定的 NPC 绘制召唤物目标标记。 |
| Vanilla: Smart Cursor Targets | 在使用智能光标放置或破坏时绘制目标瓷砖高亮。 |
| Vanilla: Laser Ruler | 绘制机械尺子瓷砖网格。 |
| Vanilla: Ruler | 绘制尺子线条并设置尺子文本值，但不绘制文本本身。 |
| Vanilla: Gamepad Lock On | 绘制 NPC 锁定指示器。 |
| Vanilla: Tile Grid Option | 为瓷砖网格选项绘制径向瓷砖网格覆盖。 |
| Vanilla: Town NPC House Banners | 绘制 NPC 房屋旗帜并处理从家中手动驱逐 NPC 的逻辑。 |
| Vanilla: Hide UI Toggle | 基于隐藏 UI 切换处理隐藏 UI 的逻辑。 |
| Vanilla: Wire Selection | 绘制并处理线选择径向菜单和线模式光标预览的逻辑。还绘制高尔夫挥杆功率仪表和高尔夫球指示箭头。 |
| Vanilla: Capture Manager Check | 绘制并处理相机模式的逻辑。 |
| Vanilla: Ingame Options | 绘制并处理游戏内选项菜单的逻辑。 |
| Vanilla: Fancy UI | 绘制装饰性 UI，如成就 UI、虚拟键盘 UI 和模组配置 UI。 |
| Vanilla: Achievement Complete Popups | 绘制成就和加入请求的游戏内通知。 |
| Vanilla: Entity Health Bars | 为 NPC 和其他玩家绘制血条。 |
| Vanilla: Invasion Progress Bars | 绘制入侵进度条和Boss血条。 |
| Vanilla: Map / Minimap | 绘制游戏内小地图。（不绘制全屏地图或覆盖地图） |
| Vanilla: Diagnose Net | 绘制网络诊断。 |
| Vanilla: Diagnose Video | 绘制渲染诊断。 |
| Vanilla: Sign Tile Bubble | 悬停在意告示牌上时绘制告示牌聊天气泡。 |
| Vanilla: Hair Window | 绘制造型师发型菜单。 |
| Vanilla: Dresser Window | 绘制衣柜菜单。 |
| Vanilla: NPC / Sign Dialog | 为 NPC 和告示牌绘制对话菜单。 |
| Vanilla: Interface Logic 2 | 处理与物品栏颜色相关的逻辑。 |
| Vanilla: Resource Bars | 绘制生命、法力和呼吸条，以及增益图标。 |
| Vanilla: Interface Logic 3 | 处理玩家物品栏关闭时的逻辑。 |
| Vanilla: Inventory | 绘制并处理与物品栏相关的所有逻辑。 |
| Vanilla: Info Accessories Bar | 绘制并处理 PDA 信息逻辑。 |
| Vanilla: Settings Button | 绘制并处理设置按钮的逻辑。 |
| Vanilla: Hotbar | 绘制并处理热键栏的逻辑。 |
| Vanilla: Builder Accessories Bar | 绘制并处理建筑配件切换的逻辑。 |
| Vanilla: Radial Hotbars | 绘制并处理径向热键栏和径向快速栏的逻辑。 |
| Vanilla: Mouse Text | 绘制尺子测量文本并处理设置鼠标文本的逻辑。 |
| Vanilla: Player Chat | 绘制聊天。 |
| Vanilla: Death Text | 绘制死亡文本覆盖。 |
| Vanilla: Cursor | 绘制光标。 |
| Vanilla: Debug Stuff | 什么都不做。 |
| Vanilla: Mouse Item / NPC Head | 绘制持有在光标上的物品，也绘制并处理手动移动 NPC 房屋的逻辑。 |
