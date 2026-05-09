本wiki页面作为`Terraria.Player::Update(int)`方法的大多数技术方面的参考。
如果您需要完整参考，请通过`setup.bat`工具反编译tModLoader（参见：[tModLoader贡献者指南](https://github.com/tModLoader/tModLoader/wiki/tModLoader-guide-for-contributors)）。

更新"任务"按逻辑顺序列出，使用编号列表来表示相关性。

1. 如果玩家是此游戏的客户端（`i == Main.myPlayer`）且不是服务器玩家（`Main.netMode != 2`），则调用`LockOnHelper::Update()`
    * [说明](#lockonhelperupdate)
1. 如果玩家是此游戏的客户端（`i == Main.myPlayer`）且世界是The Constant（`Main.dontStarveWorld`），则调用`DontStarveDarknessDamageDealer::Update(Player)`
    * [说明](#dontstarvedarknessdamagedealerupdateplayer)
1. 地面/空中速度统计重置
    * `Player::maxFallSpeed`设置为`10`
    * `Player::gravity`设置为`Player::defaultGravity`（`0.4`）
    * `Player::jumpHeight`设置为`15`
    * `Player::jumpSpeed`设置为`5.01`
    * `Player::maxRunSpeed`和`Player::accRunSpeed`设置为`3`
    * `Player::runAcceleration`设置为`0.08`
    * `Player::runSlowdown`设置为`0.2`
1. 如果玩家的坐骑处于非活跃状态或不是矿车，则清除`Player::onWrongGround`（负责阻止不在矿车轨道上的矿车移动的变量）
1. `Player::heldProj`设置为`-1`
1. `Player::instantMovementAccumulatedThisFrame`（用于侧向滑轮移动的变量）设置为`Vector2.Zero`
1. 如果玩家持有传送门枪或最近退出过传送门枪传送门（`Player::PortalPhysicsEnabled`），则`Player::maxFallSpeed`设置为`35`
1. 湿润移动检查
    1. 如果设置了`Player::wet`
        1. 如果设置了`Player::honeyWet`：
            * `Player::gravity`降低到`0.1`
            * `Player::maxFallSpeed`降低到`3`
        1. 否则，如果设置了`Player::merman`：
            * `Player::gravity`降低到`0.3`
            * `Player::maxFallSpeed`降低到`7`
        1. 否则，如果设置了`Player::trident`（当玩家持有三叉戟且不在坐骑/矿车中时设置为`true`的变量）：
            * `Player::gravity`降低到`0.25`
            * `Player::maxFallSpeed`降低到`6`
            * `Player::jumpHeight`增加到`25`
            * `Player::jumpSpeed`增加到`5.51`
            * 如果玩家按住向上移动键（`Player::controlUp`）：
                * `Player::gravity`降低到`0.1`
                * `Player::maxFallSpeed`降低到`2`
        1. 否则：
            * `Player::gravity`降低到`0.2`
            * `Player::maxFallSpeed`降低到`5`
            * `Player::jumpHeight`增加到`30`
            * `Player::jumpSpeed`增加到`6.01`
1. 如果玩家有扭曲debuff：
    * `Player::gravity`设置为`0`
1. `Player::maxFallSpeed`增加`0.01`
1. 如果玩家是此游戏的客户端（`Main.myPlayer == i`）...
    1. 处理游戏手柄的快速抓取计时器
    1. 重置放置瓦片前显示的预览（`TileObject.objectPreview.Reset()`）
    1. 如果设置了`DD2Event::DownedInvasionAnyDifficulty`，则设置`Player::downedDD2EventAnyDifficulty`（用于跟踪玩家是否可以在活动外部使用DD2哨兵的变量）
1. `NPC::freeCake`处理
    * 如果玩家在自然发生的派对期间与她交谈，则从派对女孩那里获得一块蛋糕
1. `Player::emoteTime`计时器递减
1. `Player::ghostDmg`（用于跟踪玩家何时可以生成幽灵弹射物的变量，来自Spectre套装奖励）递减`6.6666665`
1. `Player::lifeSteal`处理
    * 如果世界处于正常模式且`Player::lifeSteal`小于`80`，则增加`0.6`。然后上限为`80`
    * 否则，如果`Player::lifeSteal`小于`70`，则增加`0.5`。然后上限为`70`
1. 调用`Player::ResizeHitbox()`
    * [说明](#playerresizehitbox)
1. 如果玩家在坐骑上且该坐骑是鲁道夫坐骑（驯鹿铃铛），则在玩家中心生成光
1. 其他客户端检查
    1. 清除`Player::outOfRange`
    1. 如果玩家不是此游戏的客户端（`whoAmI != Main.myPlayer`）且他们在世界边界之外，则设置`Player::outOfRange`
        * 注意：由于1.4 tModLoader中`Terraria.Tile`成为结构，因此对玩家是否在未加载的世界区域中的额外检查失败。
    1. 如果玩家被认为是"超出范围"：
        * `Player::numMinions`、`Player::slotsMinions`和`Player::itemAnimation`设置为`0`
        * 调用`Player::UpdateBuffs(int)`
            * [说明](#playerupdatebuffsint)
        * 调用`Player::PlayerFrame()`
            * [说明](#playerupdateframe)

// TODO: 完成文档

### LockOnHelper::Update()
锁定系统是Terraria中一个隐藏的机制，通常在游戏手柄使用背后进行。
激活系统并选择目标NPC会导致有效光标位置捕捉到目标NPC中心以进行武器瞄准。

此方法处理检查系统是否可以使用、更新箭头视觉的计时器以及寻找有效目标。

### DontStarveDarknessDamageDealer::Update(Player)
在完全黑暗至少5秒后，玩家开始每秒受到50点伤害。

为了被认为是"在黑暗中"，评估以下内容：
1. 检索玩家中心的亮度值
1. 将亮度值转换为3D向量，其中每个分量的范围在`0`到`1`之间（含）。为比较，颜色值`255`转换为`1`
1. 如果该向量的长度小于`0.15`，则玩家被认为是"不安全"，即在黑暗中。

### Player::ResizeHitbox()
此方法更新玩家的Y位置以考虑`Player::HeightOffsetBoost`，该属性在玩家在坐骑上时使用`Mount::HeightBoost`，在玩家在阶梯凳上时使用`PortableStoolUsage::HeightBoost`，如果都不是则使用`0`。

### Player::UpdateBuffs(int)
此方法负责更新所有玩家活动buff的剩余时间以及应用其效果。

1. 如果玩家当前正在用生命汲取物品击中NPC（`soulDrain > 0`）且他们是此客户端的玩家（`whoAmI == Main.myPlayer`），
    * 授予**生命汲取**（ID 151）buff持续2个游戏刻
1. 如果世界是The Constant（`Main.dontStarveWorld`），
    * 调用`Player::UpdateStarvingState(bool)`
        * [说明](#playerupdatestarvingstatebool)
1. 对于玩家身上的每个活动buff（buff索引 = `j`）：
    1. 如果玩家是此客户端的玩家（`whoAmI == Main.myPlayer`）且buff的剩余时间可以减少（`!BuffID.Sets.TimeLeftDoesNotDecrease[buffType[j]]`）
        * `buffTime[j]`递减`1`
    1. 按顺序检查以下buff。如果当前迭代的buff匹配任何以下，其效果将被应用：
        1. **黑曜石皮肤**（ID 1）
            * 设置`Player::lavaImmune`（防止熔岩伤害）
            * 设置`Player::fireWalk`（防止收到**燃烧**debuff）
            * 设置`Player::buffImmune[24]`（ID 24 = **着火！**debuff）
        1. 任何坐骑buff（`BuffID.Sets.BasicMountData[buffType[j]] != null`）
            * 用buff的坐骑数据初始化`Player::mount`
            * `Player::buffTime[j]`设置为`10`
        1. **瓶中星**（ID 158）
            * `Player::manaRegenBonus`增加`2`
        1. **磨锐**（ID 159）
            * `Player::GetArmorPenetration(DamageClass.Melee)`增加`12`
        1. **糖潮**（ID 192）
            * `Player::pickSpeed`减少`0.2`
            * `Player::moveSpeed`增加`0.2`
        1. **大脑欺骗**（ID 321）
            * `Player::allCrit`增加`10`
            * `Player::minionDamage`增加`0.1`
        1. **再生**（ID 2）
            * `Player::lifeRegen`增加`4`
// TODO: 完成说明

### Player::UpdateFrame()
// TODO

### Player::UpdateStarvingState(bool)
// TODO
