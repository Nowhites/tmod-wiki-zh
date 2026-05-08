# 欢迎来到 tModLoader Wiki！

本页面作为与 tModLoader 相关的指南中心。这些指南帮助教授关于 tModLoader 模组制作的基本概念，并可作为模组开发者的有用资源。欢迎贡献尚未涵盖的主题的指南，但请勿在未事先咨询开发者的情况下编辑已建立的指南。

**[自动生成的文档](http://tmodloader.github.io/tModLoader/)** 记录了 Terraria 和 tModLoader API 的类和成员。它可用于了解特定方法和字段，但模组开发者应从本 wiki 和 ExampleMod 中的学习资源开始。文档有与各种可用版本的 tModLoader 对应的单独页面。

您可以通过右侧侧边栏中的链接浏览页面。

如果您有建议、提示，或发现拼写错误或语法错误，请在 **[我们的 discord 服务器](https://discord.gg/tmodloader)**、**[论坛主题](https://forums.terraria.org/index.php?threads/1-3-tmodloader-a-modding-api.23726/)** 或 **[开启一个问题主题](https://github.com/tModLoader/tModLoader/issues/new/choose)** 来通知我们。

有用的资源列表可以在 **[这里](Useful-Resources)** 找到。

以下是 wiki 页面的结构化列表，您可以使用它来了解更多关于 API 的信息。

如果您想做出贡献，请事先查看我们的 **[贡献指南](https://github.com/tModLoader/tModLoader/blob/stable/.github/CONTRIBUTING.md)**。

## 信息页面
这些页面描述某些文件或文件夹。
- [关于 build.txt 的信息](build.txt)
- [关于 description.txt 的信息](description.txt)
- [tModLoader 开发流程](The-tModLoader-development-pipeline)（了解 tModLoader 如何构建）
- [为模组或 tML 贡献本地化](Contributing-Localization)
- [关于 Workshop 的信息](Workshop)（如何发布和更新您的模组）
- [许可信息](Licensing-information)（包括：如何许可您的模组，关于 tML 的许可证）

## 入门指南
- [面向玩家的 tModLoader 指南](tModLoader-guide-for-players) -- 如果您想玩模组，请学习 tModLoader
- [面向开发者的 tModLoader 指南](tModLoader-guide-for-developers) -- 学习如何使用 tModLoader 创建模组
- [面向贡献者的 tModLoader 指南](tModLoader-guide-for-contributors) -- 学习如何为 tModLoader 做贡献
- [tModLoader 基础使用指南](Basic-tModLoader-Usage-Guide) -- 学习使用 tML 的基础知识
- [tModLoader 基础使用常见问题](Basic-tModLoader-Usage-FAQ) -- 查找常见使用问题和错误的修复方法
- [tModLoader 模组制作入门指南](Basic-tModLoader-Modding-Guide) - 如果您想制作模组，请从这里开始。
- [tModLoader 模组制作常见问题](Basic-tModLoader-Modding-FAQ)
- [运行模组服务器](Starting-a-modded-server) -- 学习如何设置模组服务器
- [调试多人游戏使用问题](Debugging-Multiplayer-Usage-Issues)

## 开发指南
这些页面旨在帮助您学习如何正确开发模组、如何设置开发环境等。
- [使用 Visual Studio 开发](Developing-with-Visual-Studio) -- 推荐，但仅限 Windows。
- [使用 Visual Studio Code 开发](Developing-with-Visual-Studio-Code) -- 轻量级、跨平台，功能较少。
- [使用 Rider 开发](Developing-with-Rider) -- 跨平台。
- [为什么使用 IDE](Why-Use-an-IDE) -- 了解 Visual Studio 和其他 IDE 如何帮助模组开发者更轻松地制作模组。
- [学习如何调试](Learn-How-To-Debug) -- 调试是我们查找和修复错误和 bug 的方式。
- [更新迁移指南](Update-Migration-Guide) -- 包含更新（移植）模组到每个新 tModLoader 版本所需的代码更新信息。

## 简单指南
这些指南很简单，如果您是 tModLoader API 的新手，应该使用它们。它们的目的是让您熟悉 tModLoader，并帮助您学习如何制作各种类型的内容。
- [基础指南前置条件](Basic-Prerequisites)
- [精灵制作](Spriting)
- [基础弹药指南](Basic-Ammo)
- [基础自动加载指南](Basic-Autoload)
- [基础坐标指南](Coordinates)
- [几何学](Geometry)
- [基础发光面具指南](Basic-glowmask-guide)
- [实体来源](IEntitySource)
- [本地化](Localization)
  - [贡献本地化](Contributing-Localization)
- [基础日志指南](Logging)
- [基础召唤物指南](Basic-Minion-Guide)
- [基础 ModConfig 指南](Basic-JSON-&-ModConfigs)
- [基础 ModDust 指南](Basic-Dust)
- [基础 ModItem 指南](Basic-Item)
  - [护甲和装备纹理](Armor-Texture-Migration-Guide)
- [基础 ModProjectile 指南](Basic-Projectile)
- [ModPlayer 指南](ModPlayer)
- [基础配方指南](Basic-Recipes)
- [基础 ModTile 指南](Basic-Tile)
- [基础 ModTileEntity 指南](Basic-Tile-Entity)
- [基础网络代码指南](Basic-Netcode)
- [基础 NPC 掉落和战利品指南](Basic-NPC-Drops-and-Loot-1.4)
- [基础 NPC 生成指南](Basic-NPC-Spawning)
- [基础声音指南](Basic-Sounds)
- [基础时间和计时器指南](Time-and-Timers)
- [基础 UI 元素指南](Basic-UI-Element)
- [墙壁指南](Wall)
- [条件](Conditions)

## 中级指南
以下指南比简单指南更高级，但不是高级或专家级别。
- [中级指南前置条件](Intermediate-Prerequisites)
- [中级 Git 和模组管理指南](Intermediate-Git-&-mod-management)
- [中级 C#6 模组制作指南](Intermediate-modding-with-c%236)
- [中级 C#7 模组制作指南](Intermediate-modding-with-c%237)
- [中级网络代码指南](Intermediate-netcode)
- [中级配方指南](Intermediate-Recipes)：配方组、自定义条件、闪光、排序、编辑
- [中级保存和加载指南](Saving-and-loading-using-TagCompound)：TagCompound
- [资源](Assets) -- 访问和加载纹理和着色器

## 高级指南
以下指南被认为是高级的，只有在掌握了中级水平后才能尝试。
- [高级指南前置条件](Advanced-Prerequisites)
- [高级角速度和线速度指南](Advanced-Angular-velocity-and-Linear-velocity)
- [高级自定义 UI 指南](Advanced-guide-to-custom-UI)
- [高级修复模组未完全卸载指南](https://github.com/tModLoader/tModLoader/wiki/Fixing-Mod-Not-Fully-Unloaded)
- [高级原版代码适配指南](Advanced-Vanilla-Code-Adaption)
- [高级 Detouring 指南](https://github.com/tModLoader/tModLoader/wiki/Advanced-Detouring-Guide)
- [世界生成](World-Generation)
- [反射](Reflection) -- 访问和修改 tModLoader 和其他模组的私有和内部成员。

## 专家指南
专家级指南不一定比高级指南更难，但它们更常涵盖特定（困难）主题。如果您有兴趣学习复杂主题或提高模组质量，应该遵循专家指南。
- [专家指南前置条件](Expert-Prerequisites)
- [专家级跨模组内容指南](Expert-Cross-Mod-Content) -- 调用、弱引用、模组引用、配方
- [专家级 IL 编辑](Expert-IL-Editing) -- 使用 MonoMod 修补游戏
- [专家级 IL 编辑其他模组](Patching-Other-Mods-Using-MonoMod)
- [使用 HookEndpointManager 的专家级 IL 编辑](Detouring-and-IL-Editing-using-HookEndpointManager)
- [专家级着色器指南](Expert-Shader-Guide) -- 护甲染料和屏幕着色器
- [tModCodeAssist](tModCodeAssist) -- 关于为 tModLoader 的自定义代码分析器配置和实现新代码修复的信息。

## 原版参考
#### 指南/值参考/更改
- [旗帜到 NPC 转换](BannerToNPC-Conversions)
- [玩家物品动画](Player-Item-Animation)
- [有用的原版字段](Useful-Vanilla-Fields)
- [有用的原版方法](Useful-Vanilla-Methods)
- [原版内容 ID](Vanilla-Content-IDs) -- ItemID、ProjectileID、TileID、NPCID、TileID、WallID、BuffID、SoundID 等。
- [原版游戏 Wiki 数据 ID](https://terraria.wiki.gg/wiki/Data_IDs)
- [原版界面更改](Vanilla-Class-Changes)
- [原版界面层值](Vanilla-Interface-layers-values)
- [原版物品字段值](Vanilla-Item-Field-Values)
- [原版 NPC 字段值](Vanilla-NPC-Field-Values)
- [原版弹射物字段值](Vanilla-Projectile-Field-Values)
- [原版配方](https://terraria.wiki.gg/wiki/Recipes)
- [原版世界生成步骤](Vanilla-World-Generation-Steps)
#### ID/名称
- [原版弹药 ID](Vanilla-Ammo-IDs)
- [原版弹射物 AI](Vanilla-Projectile-AIs)
#### 类文档
- [物品类文档](Item-Class-Documentation)
- [NetMessage 类文档](NetMessage-Class-Documentation)
- [NPC 类文档](NPC-Class-Documentation)
- [弹射物类文档](Projectile-Class-Documentation)

## 其他页面
这些是我们目前未能分类或根本不属于任何一类的页面。
- [坐骑数据参数](MountDataParameters)
- [开源模组](Open-Source-Mods)
- [命令行参数和服务器配置](Command-Line)
- [.tmod 文件格式](https://github.com/tModLoader/tModLoader/wiki/.tmod-File-format)
