# 欢迎来到 tModLoader Wiki！

本页面作为与 tModLoader 相关的指南中心。这些指南帮助教授关于 tModLoader 模组制作的基本概念，并可作为模组开发者的有用资源。欢迎贡献尚未涵盖的主题的指南，但请勿在未事先咨询开发者的情况下编辑已建立的指南。

**[自动生成的文档](http://tmodloader.github.io/tModLoader/)** 记录了 Terraria 和 tModLoader API 的类和成员。它可用于了解特定方法和字段，但模组开发者应从本 wiki 和 ExampleMod 中的学习资源开始。文档有与各种可用版本的 tModLoader 对应的单独页面。

您可以通过右侧侧边栏中的链接浏览页面。

如果您有建议、提示，或发现拼写错误或语法错误，请在 **[我们的 discord 服务器](https://discord.gg/tmodloader)**、**[论坛主题](https://forums.terraria.org/index.php?threads/1-3-tmodloader-a-modding-api.23726/)** 或 **[开启一个问题主题](https://github.com/tModLoader/tModLoader/issues/new/choose)** 来通知我们。

有用的资源列表可以在 **[这里](n1-有用资源.md)** 找到。

以下是 wiki 页面的结构化列表，您可以使用它来了解更多关于 API 的信息。

如果您想做出贡献，请事先查看我们的 **[贡献指南](https://github.com/tModLoader/tModLoader/blob/stable/.github/CONTRIBUTING.md)**。

## 信息页面
这些页面描述某些文件或文件夹。
- [关于 build.txt 的信息](n23-build配置.md)
- [关于 description.txt 的信息](n24-description配置.md)
- [tModLoader 开发流程](n18-开发流程.md)（了解 tModLoader 如何构建）
- [为模组或 tML 贡献本地化](m2-参与本地化.md)
- [关于 Workshop 的信息](j3-Steam工作坊.md)（如何发布和更新您的模组）
- [许可信息](m1-许可证信息.md)（包括：如何许可您的模组，关于 tML 的许可证）

## 入门指南
- [面向玩家的 tModLoader 指南](b1-玩家安装指南.md) -- 如果您想玩模组，请学习 tModLoader
- [面向开发者的 tModLoader 指南](b2-开发者安装指南.md) -- 学习如何使用 tModLoader 创建模组
- [面向贡献者的 tModLoader 指南](b3-贡献者安装指南.md) -- 学习如何为 tModLoader 做贡献
- [tModLoader 基础使用指南](a2-基础使用指南.md) -- 学习使用 tML 的基础知识
- [tModLoader 基础使用常见问题](a5-使用常见问题.md) -- 查找常见使用问题和错误的修复方法
- [tModLoader 模组制作入门指南](a3-基础模组开发指南.md) - 如果您想制作模组，请从这里开始。
- [tModLoader 模组制作常见问题](a4-模组开发常见问题.md)
- [运行模组服务器](l1-架设模组服务器.md) -- 学习如何设置模组服务器
- [调试多人游戏使用问题](i2-多人游戏调试.md)

## 开发指南
这些页面旨在帮助您学习如何正确开发模组、如何设置开发环境等。
- [使用 Visual Studio 开发](b4-使用Visual-Studio开发.md) -- 推荐，但仅限 Windows。
- [使用 Visual Studio Code 开发](b5-使用VS-Code开发.md) -- 轻量级、跨平台，功能较少。
- [使用 Rider 开发](b6-使用Rider开发.md) -- 跨平台。
- [为什么使用 IDE](a6-为什么使用IDE.md) -- 了解 Visual Studio 和其他 IDE 如何帮助模组开发者更轻松地制作模组。
- [学习如何调试](i1-调试教程.md) -- 调试是我们查找和修复错误和 bug 的方式。
- [更新迁移指南](k1-版本迁移指南.md) -- 包含更新（移植）模组到每个新 tModLoader 版本所需的代码更新信息。

## 简单指南
这些指南很简单，如果您是 tModLoader API 的新手，应该使用它们。它们的目的是让您熟悉 tModLoader，并帮助您学习如何制作各种类型的内容。
- [基础指南前置条件](c1-开发前置要求.md)
- [精灵制作](n19-精灵图制作.md)
- [基础弹药指南](c12-弹药系统.md)
- [基础自动加载指南](c2-自动加载机制.md)
- [基础坐标指南](n4-坐标系统.md)
- [几何学](n6-几何学.md)
- [基础发光面具指南](c16-发光遮罩指南.md)
- [实体来源](g7-实体来源接口.md)
- [本地化](o1-本地化指南.md)
  - [贡献本地化](m2-参与本地化.md)
- [基础日志指南](i3-日志记录.md)
- [基础召唤物指南](c13-召唤物指南.md)
- [基础 ModConfig 指南](c15-JSON配置.md)
- [基础 ModDust 指南](c9-尘埃粒子.md)
- [基础 ModItem 指南](c3-物品系统.md)
  - [护甲和装备纹理](n11-护甲纹理迁移.md)
- [基础 ModProjectile 指南](c4-投射物系统.md)
- [ModPlayer 指南](g4-ModPlayer文档.md)
- [基础配方指南](c11-合成系统.md)
- [基础 ModTile 指南](c6-瓦片系统.md)
- [基础 ModTileEntity 指南](c7-瓦片实体.md)
- [基础网络代码指南](c14-网络代码基础.md)
- [基础 NPC 掉落和战利品指南](c20-14版NPC掉落.md)
- [基础 NPC 生成指南](c21-NPC生成机制.md)
- [基础声音指南](c10-声音系统.md)
- [基础时间和计时器指南](n5-时间和计时器.md)
- [基础 UI 元素指南](c17-UI元素基础.md)
- [墙壁指南](c8-墙壁系统.md)
- [条件](n7-条件系统.md)

## 中级指南
以下指南比简单指南更高级，但不是高级或专家级别。
- [中级指南前置条件](d1-中级前置要求.md)
- [中级 Git 和模组管理指南](d2-Git与模组管理.md)
- [中级 C#6 模组制作指南](d3-CSharp6语法.md)
- [中级 C#7 模组制作指南](d4-CSharp7语法.md)
- [中级网络代码指南](d7-高级网络编程.md)
- [中级配方指南](d6-高级合成.md)：配方组、自定义条件、闪光、排序、编辑
- [中级保存和加载指南](j1-数据保存与加载.md)：TagCompound
- [资源](n20-资源文件.md) -- 访问和加载纹理和着色器

## 高级指南
以下指南被认为是高级的，只有在掌握了中级水平后才能尝试。
- [高级指南前置条件](e1-高级前置要求.md)
- [高级角速度和线速度指南](e5-角速度与线速度.md)
- [高级自定义 UI 指南](e4-自定义UI高级指南.md)
- [高级修复模组未完全卸载指南](n14-修复模组卸载问题.md)
- [高级原版代码适配指南](e2-原版代码适配.md)
- [高级 Detouring 指南](e3-方法重定向指南.md)
- [世界生成](c18-世界生成.md)
- [反射](n16-反射.md) -- 访问和修改 tModLoader 和其他模组的私有和内部成员。

## 专家指南
专家级指南不一定比高级指南更难，但它们更常涵盖特定（困难）主题。如果您有兴趣学习复杂主题或提高模组质量，应该遵循专家指南。
- [专家指南前置条件](f1-专家前置要求.md)
- [专家级跨模组内容指南](f4-跨模组内容.md) -- 调用、弱引用、模组引用、配方
- [专家级 IL 编辑](f2-IL代码编辑.md) -- 使用 MonoMod 修补游戏
- [专家级 IL 编辑其他模组](n13-使用MonoMod修补模组.md)
- [使用 HookEndpointManager 的专家级 IL 编辑](f5-使用HookEndpointManager重定向和IL编辑.md)
- [专家级着色器指南](f3-着色器指南.md) -- 护甲染料和屏幕着色器
- [tModCodeAssist](i4-代码辅助插件.md) -- 关于为 tModLoader 的自定义代码分析器配置和实现新代码修复的信息。

## 原版参考
#### 指南/值参考/更改
- [旗帜到 NPC 转换](n10-旗帜NPC转换.md)
- [玩家物品动画](n9-玩家物品动画.md)
- [有用的原版字段](n2-有用的原版字段.md)
- [有用的原版方法](n3-有用的原版方法.md)
- [原版内容 ID](h2-内容ID.md) -- ItemID、ProjectileID、TileID、NPCID、TileID、WallID、BuffID、SoundID 等。
- [原版游戏 Wiki 数据 ID](https://terraria.wiki.gg/wiki/Data_IDs)
- [原版界面更改](h1-原版类变更.md)
- [原版界面层值](h10-界面层值.md)
- [原版物品字段值](h6-物品字段值.md)
- [原版 NPC 字段值](h4-NPC字段值.md)
- [原版弹射物字段值](h5-投射物字段值.md)
- [原版配方](https://terraria.wiki.gg/wiki/Recipes)
- [原版世界生成步骤](h8-世界生成步骤.md)
#### ID/名称
- [原版弹药 ID](h7-弹药ID.md)
- [原版弹射物 AI](h9-投射物AI.md)
#### 类文档
- [物品类文档](g1-物品类API.md)
- [NetMessage 类文档](g5-网络消息API.md)
- [NPC 类文档](g2-NPC类API.md)
- [弹射物类文档](g3-投射物类API.md)
- [原版类文档](G-API参考文档/g8-瓦片类API.md)

## 其他页面
这些是我们目前未能分类或根本不属于任何一类的页面。
- [坐骑数据参数](n12-坐骑数据参数.md)
- [开源模组](j4-开源模组.md)
- [命令行参数和服务器配置](l2-命令行参数.md)
- [.tmod 文件格式](n22-tmod文件格式.md)
