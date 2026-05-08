1.4 版本支持新的简化躯干护甲纹理格式。这会影响 `EquipType.HandsOn/HandsOff/Body`。旧的样式必须转换为新样式才能正确绘制。

# 躯干护甲纹理
之前的 `Body`、`Arms` 和 `FemaleBody` 纹理已合并为一个新纹理。新纹理包含"扩展"的手臂精灵。这些扩展手臂精灵用于：
* 吃食物（`ItemUseStyleID.EatFood`）
* 打高尔夫球（`ItemUseStyleID.GolfPlay` 和 `ItemHoldStyleID.HoldGolfClub`）
* 喝药水（`ItemUseStyleID.DrinkLiquid`）
* 使用割草机（`ItemUseStyleID.MowTheLawn`）
* 弹吉他（`ItemUseStyleID.Guitar` 和 `ItemHoldStyleID.HoldGuitar`）
* 挥动短剑（`ItemUseStyleID.Rapier`）
* 使用夜辉（`ItemUseStyleID.RaiseLamp` 和 `ItemHoldStyleID.HoldLamp`）
* 抚摸城镇宠物。

您可以在 [泰拉瑞亚 Wiki](https://terraria.wiki.gg/wiki/Use_Style_IDs) 上查看这些使用样式的示例。

# 纹理模板和工具

* [Sprite Transformer](https://forums.terraria.org/index.php?threads/96210/)：一个可以快速将旧的 Body、Arms 和 FemaleBody 纹理转换为新格式的工具。正如论坛页面所述，由于许多新纹理是由 Re-Logic 手工绘制的，该工具并非 100% 准确。您需要进行一些额外的编辑。

使用这些模板作为创建新纹理的参考。
* [工作表参考（由 Re-Logic 提供）](https://imgur.com/ZbAsfkn.png)
<img width="180" height="112" alt="Body Armor Reference - Relogic" src="https://github.com/user-attachments/assets/c3fd1952-76dc-47c4-bae5-c9ec730f65ad" />

* [工作表参考（完整玩家）](https://imgur.com/8dzJAr3.png)
<img width="360" height="224" alt="Body Armor Reference - Full Player" src="https://github.com/user-attachments/assets/34a41e7c-6abd-4027-a67c-a8050a3083a5" />

* [工作表参考（穿着铜盔甲的完整玩家）](https://imgur.com/0iw2Tw2.png)
<img width="360" height="224" alt="Body Armor Reference - Copper Armor" src="https://github.com/user-attachments/assets/6713b02a-ccb0-4a35-9e8a-dcdae569e89c" />

* [工作表参考（穿着稻草人衬衫的完整玩家）](https://imgur.com/H5P8qN4.png)
<img width="360" height="224" alt="Body Armor Reference - Scarecrow Shirt" src="https://github.com/user-attachments/assets/560c1ee1-08d6-49bd-b5e8-bd60cf2d1643" />

* [工作表参考（完整玩家，暗色）](https://imgur.com/UIyCVN2.png)
<img width="360" height="224" alt="Body Armor Reference - Full Player Dim" src="https://github.com/user-attachments/assets/cda23755-86aa-45bc-a24a-eb5739b38005" />

* [着色区域参考](https://imgur.com/Xsxt4EF.png)
<img width="360" height="224" alt="Body Armor Reference - Colored Areas" src="https://github.com/user-attachments/assets/6491d08e-43ac-4393-97d8-dc8d982cf23e" />

* [着色区域参考（带标签）](https://imgur.com/3nhay8x.png)
<img width="360" height="224" alt="Body Armor Reference - Colored Areas Labeled" src="https://github.com/user-attachments/assets/5e0b2bd1-5f28-4b9f-ad39-601a951bc29c" />

* 头部护甲参考
<img width="20" height="560" alt="Head Armor Reference" src="https://github.com/user-attachments/assets/8bd0439d-dfc7-47a3-a96b-233663941e85" />

* 腿部护甲参考
<img width="20" height="560" alt="Leg Armor Reference" src="https://github.com/user-attachments/assets/878952fb-b49e-4854-9995-5f323bdf6d77" />

# 完整迁移示例

## 躯干护甲

这里我们将把 Solar Flare 盔甲从 1.3 纹理迁移到 1.4 纹理。

1. 我们有 1.3 样式的 `Body`、`Arms` 和 `FemaleBody` 纹理。

 ![](https://imgur.com/dn1fror.png) ![](https://imgur.com/qsJK939.png) ![](https://imgur.com/yomz856.png)

2. 我们将把它们输入 Sprite Transformer，它会生成我们的新纹理。如果您的护甲套装没有 `FemaleBody`，请在 Sprite Transformer 中勾选"Use Body sprite sheet for Female equivalent"选项。

 ![](https://imgur.com/BKALEWo.png)

 虽然 Sprite Transformer 正在用有限的信息尽力处理，但它的输出纹理并不完美。注意缺少肩部区域。我们必须手动编辑此纹理来修复问题。使用上面的参考作为指导来了解各部分的位置。需要注意的是：除了在挥动和跳跃时，肩部区域始终绘制在手臂上方。

3. 我们将编辑 Sprite Transformer 生成的纹理来修复问题。这是完成的 Solar Flare 胸甲的样子：（来自 1.4）

 ![](https://imgur.com/hRPTGA3.png)

 注意肩膀现在在正确的位置，手臂更完整，身体部分也更完整了。

## 配饰

HandsOn 和 HandsOff 配饰也需要遵循这个新格式。这里我们将把 Fire Gauntlets 从 1.3 样式迁移到 1.4 样式。

1. 我们有 1.3 样式的 `HandsOn` 和 `HandsOff` 纹理。

 ![](https://imgur.com/xmlNoOn.png) ![](https://imgur.com/Jw5E7WS.png)

2. 我们也可以使用 Sprite Transformer 来处理这些，但有点棘手。Sprite Transformer 要求我们选择 Body 纹理。我们可以使用一个[空纹理](https://imgur.com/saRgNdG.png)（40 × 1120）作为 Body 纹理，然后勾选"Use Body sprite sheet for Female equivalent"选项。Sprite Transformer 的输出如下，分别是 HandsOn 和 HandsOff 纹理。

 ![](https://imgur.com/NyT3exB.png) ![](https://imgur.com/2iv76Wb.png)

3. HandsOn 纹理看起来非常好。我们只需要删除最右边的额外扩展部分。然而 HandsOff 纹理不太好。Sprite Transformer 不知道它应该是背面的手。我们必须自己把它移下来，并为它创建扩展精灵。这是我们完成的纹理的样子：（来自 1.4）

 ![](https://imgur.com/btDMar7.png) ![](https://imgur.com/Dmiq7PZ.png)

# 额外信息和陷阱

有关纹理的更多信息，请参阅 [Terraria Workshop 论坛帖子](https://forums.terraria.org/index.php?threads/the-ultimate-guide-to-content-creation-and-use-for-the-terraria-workshop.100652/#advancedtexturepack)

### 纹理大小

最终的躯干纹理应该是 360 × 224

### 原版纹理

要自己查看原版纹理，请使用 [TConvert](https://forums.terraria.org/index.php?threads/61706/) 等工具提取原版资源。确保特别查看 `Images/Armor` 文件夹中的护甲和 `Images/Accessories` 文件夹中的配饰。

### "当我提取原版 `Acc_HandsOn_#` 或 `Acc_HandsOff_#` 配饰时，我仍然得到旧格式的纹理！"

请在 `Images/Accessories` 文件夹中查找新样式的纹理。只需位于 `Images` 文件夹中的 `Acc_HandsOn_#` 和 `Acc_HandsOff_#` 纹理是旧格式。

### "当我使用 Sprite Transformer 时，我的输出纹理只是黑白！"

将源图像设置为 RGB 颜色而不是索引颜色。

### "当我使用 Sprite Transformer 时，点击 Transform 没有反应！"

将源图像设置为 RGB 颜色而不是灰度颜色。

### "当我使用 Sprite Transformer 时，我的输出纹理有奇怪的抖动和黑色背景！"

将源图像设置为 32 位深度。
