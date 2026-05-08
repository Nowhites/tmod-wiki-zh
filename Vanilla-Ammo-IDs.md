以下是 `Terraria.ID.AmmoID` 类中的常量列表。

`AmmoID` 用于定义弹药类别，即所有与相同武器配合使用的物品组。`AmmoID` 基于该类别中第一个弹药物品的 `ItemID`。例如，`ItemID` `MusketBall` 的值为 97，而 `AmmoID` for Bullet 的值也为 97。游戏中所有其他子弹将其 `item.ammo` 设置为 `AmmoID.Bullet`，所有使用子弹弹药的武器将其 `item.useAmmo` 设置为 `AmmoID.Bullet`。对于 ModItems，请也遵循此编号约定以避免 ID 重叠。

| 弹药 | ID/类型 |
| :-- | :-- |
| None（无） | 0 |
| Gel（凝胶） | 23 |
| Arrow（箭） | 40 |
| Coin（硬币） | 71 |
| FallenStar（陨星） | 75 |
| Bullet（子弹） | 97 |
| Sand（沙子） | 169 |
| Dart（飞镖） | 283 |
| Rocket（火箭） | 771 |
| Solution（溶液） | 780 |
| Flare（信号弹） | 931 |
| Snowball（雪球） | 949 |
| StyngerBolt（毒刺弹） | 1261 |
| CandyCorn（糖果玉米） | 1783 |
| JackOLantern（南瓜灯） | 1785 |
| Stake（木桩） | 1836 |
| NailFriendly（钉子） | 3108 |
