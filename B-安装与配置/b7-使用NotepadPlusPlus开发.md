# 使用Notepad++开发

* **警告：本文档已过时，与当前tModLoader无关。我们推荐使用[Visual Studio Code](Developing-with-Visual-Studio-Code)或[Rider](Developing-with-Rider)。**
* **Notepad++不支持语法高亮或自动完成。我们不推荐使用此编辑器，但本指南保留用于记录。**

---

# Notepad++设置

## 语法高亮
要将Notepad++与C#一起使用，您需要为每种语言配置语法高亮。首先，在菜单中导航到"语言"→"定义语言..."。这将打开"用户定义语言"窗口。

### 设置C#语法高亮
1. 在"Notepad++"菜单中，点击"语言"→"定义语言..."
2. 在"用户定义语言"窗口中，在"语言名称"文本框中输入"C#"
3. 在"扩展名"文本框中输入"cs"
4. 现在您需要为各种关键字着色。由于您已经在这里，我将跳过说明并让您自己选择颜色，但为了获得最佳效果，我建议使用以下关键字：
   * **注释和字符串：** 评论、字符串
   * **数字：** 数字
   * **操作符：** 操作符
   * **关键字：** 关键词1
   * **类型：** 关键词2、关键词3、关键词4

## 自动完成
自动完成功能允许Notepad++自动补全代码，减少输入量。要启用此功能，请按照以下步骤操作：

1. 在菜单中导航到"设置"→"首选项..."
2. 在"首选项"窗口中，点击"自动完成"选项卡
3. 勾选"启用自动完成"和"每个字符输入后更新"复选框
4. 在"单词补全"部分，勾选"输入时显示"
5. 点击"关闭"

## 设置自定义自动完成
要为C#设置自定义自动完成，您需要创建一个XML文件，其中包含您希望自动完成的单词列表。以下是创建基本自动完成文件的方法：

1. 在Notepad++中创建一个新文件
2. 添加以下内容作为文件头：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<NotepadPlus>
    <AutoComplete language="C#">
```
3. 添加您希望自动完成的单词，例如：
```xml
        <Item keyword="public" />
        <Item keyword="private" />
        <Item keyword="protected" />
        <Item keyword="override" />
        <Item keyword="virtual" />
        <Item keyword="class" />
        <Item keyword="interface" />
        <Item keyword="namespace" />
        <Item keyword="using" />
        <Item keyword="return" />
        <Item keyword="if" />
        <Item keyword="else" />
        <Item keyword="for" />
        <Item keyword="foreach" />
        <Item keyword="while" />
        <Item keyword="switch" />
        <Item keyword="case" />
        <Item keyword="break" />
        <Item keyword="continue" />
        <Item keyword="try" />
        <Item keyword="catch" />
        <Item keyword="finally" />
        <Item keyword="throw" />
        <Item keyword="new" />
        <Item keyword="this" />
        <Item keyword="base" />
        <Item keyword="null" />
        <Item keyword="true" />
        <Item keyword="false" />
        <Item keyword="static" />
        <Item keyword="readonly" />
        <Item keyword="const" />
        <Item keyword="ref" />
        <Item keyword="out" />
        <Item keyword="in" />
        <Item keyword="params" />
        <Item keyword="async" />
        <Item keyword="await" />
        <Item keyword="yield" />
        <Item keyword="var" />
        <Item keyword="dynamic" />
        <Item keyword="lock" />
        <Item keyword="checked" />
        <Item keyword="unchecked" />
        <Item keyword="unsafe" />
        <Item keyword="fixed" />
        <Item keyword="extern" />
        <Item keyword="volatile" />
        <Item keyword="sealed" />
        <Item keyword="abstract" />
        <Item keyword="partial" />
        <Item keyword="delegate" />
        <Item keyword="event" />
        <Item keyword="typeof" />
        <Item keyword="nameof" />
        <Item keyword="is" />
        <Item keyword="as" />
        <Item keyword="where" />
        <Item keyword="select" />
        <Item keyword="from" />
        <Item keyword="orderby" />
        <Item keyword="group" />
        <Item keyword="join" />
        <Item keyword="let" />
        <Item keyword="into" />
        <Item keyword="ascending" />
        <Item keyword="descending" />
        <Item keyword="equals" />
        <Item keyword="by" />
        <Item keyword="on" />
```
4. 添加tModLoader特定的关键词，例如：
```xml
        <Item keyword="Mod" />
        <Item keyword="ModItem" />
        <Item keyword="ModProjectile" />
        <Item keyword="ModNPC" />
        <Item keyword="ModBuff" />
        <Item keyword="ModMount" />
        <Item keyword="ModWorld" />
        <Item keyword="ModPlayer" />
        <Item keyword="ModSystem" />
        <Item keyword="ModTile" />
        <Item keyword="ModWall" />
        <Item keyword="ModDust" />
        <Item keyword="ModPrefix" />
        <Item keyword="ModSoundStyle" />
        <Item keyword="ModGore" />
        <Item keyword="ModData" />
        <Item keyword="Content" />
        <Item keyword="Assets" />
        <Item keyword="Loader" />
        <Item keyword="ModContent" />
        <Item keyword="ItemID" />
        <Item keyword="NPCID" />
        <Item keyword="BuffID" />
        <Item keyword="TileID" />
        <Item keyword="WallID" />
        <Item keyword="SoundID" />
        <Item keyword="Recipe" />
        <Item keyword="RecipeGroup" />
        <Item keyword="Main" />
        <Item keyword="WorldGen" />
        <Item keyword="Chat" />
        <Item keyword="Dust" />
        <Item keyword="Projectile" />
        <Item keyword="NPC" />
        <Item keyword="Player" />
        <Item keyword="Item" />
        <Item keyword="Color" />
        <Item keyword="Vector2" />
        <Item keyword="Vector3" />
        <Item keyword="Rectangle" />
        <Item keyword="Point" />
        <Item keyword="Texture2D" />
        <Item keyword="SpriteBatch" />
        <Item keyword="GameTime" />
        <Item keyword="SpriteContext" />
        <Item keyword="DrawData" />
        <Item keyword="ModTranslation" />
```
5. 添加ModContent方法：
```xml
        <Item keyword="ModContent.Request&lt;T&gt;" />
        <Item keyword="ModContent.GetInstance&lt;T&gt;" />
        <Item keyword="ModContent.Find&lt;T&gt;" />
        <Item keyword="ModContent.BuffType&lt;T&gt;" />
        <Item keyword="ModContent.ProjectileType&lt;T&gt;" />
        <Item keyword="ModContent.NPCType&lt;T&gt;" />
        <Item keyword="ModContent.ItemType&lt;T&gt;" />
        <Item keyword="ModContent.TileType&lt;T&gt;" />
        <Item keyword="ModContent.WallType&lt;T&gt;" />
        <Item keyword="ModContent.DustType&lt;T&gt;" />
        <Item keyword="ModContent.GoreType&lt;T&gt;" />
        <Item keyword="ModContent.PrefixType&lt;T&gt;" />
        <Item keyword="ModContent.Load&lt;T&gt;" />
```
6. 添加事件和方法：
```xml
        <Item keyword="SetDefaults" />
        <Item keyword="SetStaticDefaults" />
        <Item keyword="Load" />
        <Item keyword="Unload" />
        <Item keyword="UpdateInventory" />
        <Item keyword="UpdateEquip" />
        <Item keyword="UpdateAccessory" />
        <Item keyword="PreUpdate" />
        <Item keyword="PostUpdate" />
        <Item keyword="ResetEffects" />
        <Item keyword="PreKill" />
        <Item keyword="OnKill" />
        <Item keyword="PreNPCLoot" />
        <Item keyword="NPCLoot" />
        <Item keyword="OnCatchNPC" />
        <Item keyword="PreChatButtonClicked" />
        <Item keyword="ChatButtonClicked" />
        <Item keyword="PreDraw" />
        <Item keyword="PostDraw" />
        <Item keyword="DrawEffects" />
        <Item keyword="ModifyShootStat" />
        <Item keyword="Shoot" />
        <Item keyword="UseItemHitbox" />
        <Item keyword="HoldItem" />
        <Item keyword="HoldStyle" />
        <Item keyword="AltFunctionUse" />
        <Item keyword="UseStyle" />
        <Item keyword="UseAnimation" />
        <Item keyword="UseTime" />
        <Item keyword="ConsumeItem" />
        <Item keyword="PreConsumeItem" />
        <Item keyword="NewProjectile" />
        <Item keyword="FindFrame" />
        <Item keyword="AI" />
        <Item keyword="SendExtraAI" />
        <Item keyword="ReceiveExtraAI" />
        <Item keyword="AIMethod" />
        <Item keyword="ModifyHitNPC" />
        <Item keyword="OnHitNPC" />
        <Item keyword="ModifyHitPVP" />
        <Item keyword="OnHitPVP" />
        <Item keyword="ModifyHitByNPC" />
        <Item keyword="OnHitByNPC" />
        <Item keyword="ModifyHitByProjectile" />
        <Item keyword="OnHitByProjectile" />
        <Item keyword="CanHitNPC" />
        <Item keyword="CanHitPVP" />
        <Item keyword="CanBeHitByNPC" />
        <Item keyword="CanBeHitByProjectile" />
        <Item keyword="HitEffect" />
        <Item keyword="CheckDead" />
        <Item keyword="PreKill" />
        <Item keyword="OnKill" />
        <Item keyword="TownNPCType" />
        <Item keyword="GlobalNPC" />
        <Item keyword="GlobalItem" />
        <Item keyword="GlobalProjectile" />
        <Item keyword="GlobalBuff" />
        <Item keyword="IL" />
        <Item keyword="On" />
```
7. 关闭标签：
```xml
    </AutoComplete>
</NotepadPlus>
```
8. 将文件保存为`CSharp.xml`（或您偏好的名称）
9. 将此文件复制到Notepad++的自动完成文件夹中（通常位于`%APPDATA%\Notepad++\plugins\APIs\`）
10. 重新启动Notepad++，自动完成功能现在应该可以工作了

## 附加提示
- **多光标编辑：** 按住`Ctrl`并点击可以在多个位置设置光标，实现同时编辑多行
- **宏录制：** 使用"宏"菜单录制和播放重复性任务
- **搜索和替换：** 使用正则表达式进行强大的搜索和替换操作
- **代码折叠：** 在"视图"菜单中启用代码折叠以更轻松地浏览大型文件
- **缩进指南：** 在"视图"菜单中启用缩进指南以更好地可视化代码结构
