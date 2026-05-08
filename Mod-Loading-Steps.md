以下页面列出了tModLoader加载模组及其内容的顺序。

如果tModLoader更新，此页面可能会过时。因此，本页的每个部分都将包含一个指向信息来源文件的链接。
此外，本页并不全面。您对特定加载时问题的最佳资源是自己查看tModLoader的源代码，特别是[`ModContent::Load()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/ModContent.cs#L289)。

## tl;dr
最重要的加载顺序步骤。

1. 调用`Mod`子类构造函数。
2. 加载模组配置（如果启用了内容自动加载）。
3. 加载模组内容（如果启用了内容自动加载）。这也会对所有自动加载的内容调用`ILoadable::Load()`。
4. 加载gores、音乐、背景、云（如果启用了各自的自动加载）。
5. 调用`Mod::Load()`，然后调用`ModSystem::OnModLoad()`。
6. 调用`ModType::SetupContent()`（并扩展为`ModType::SetStaticDefaults()`）。
7. 初始化`ContentSamples`。
8. 调用`Mod::PostSetupContent()`，然后调用`ModSystem::PostSetupContent()`。
9. 设置生物图鉴。
10. 设置NPC和物品掉落表。
11. 设置配方组。
12. 添加配方。
13. 调用`ModSystem::PostAddRecipes()`。
14. 调用`ModSystem::PostSetupRecipes()`。

## 模组加载顺序
来源：
- [`Terraria.ModLoader.Core.ModOrganizer::SelectAndSortMods()`]( https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Core/ModOrganizer.cs#L341)
- [`Terraria.ModLoader.Core.BuildProperties::ReadBuildFile()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Core/BuildProperties.cs#L96)

另请参阅：
- [build.txt](https://github.com/tModLoader/tModLoader/wiki/build.txt)

当tModLoader加载模组时，它按内部名称排序，然后重新排序以满足以下每个模组MOD的条件：
- MOD将加载在任何包含在MOD的`build.txt`的`sortBefore`字段中的模组*之前*。
- MOD将加载在任何包含在MOD的`build.txt`的`sortAfter`字段中的模组*之后*。
- MOD将加载在任何包含在MOD的`build.txt`的`modReferences`或`weakReferences`字段中的模组*之后*，*只要这些模组未包含在`sortBefore`中*。

在这些条件满足之前，模组按内部名称排序。不要依赖基于初始名称的排序：模组可能会被任意移动以满足上述条件。如果您需要您的模组在另一个模组之前/之后加载，请使用`sortBefore`/`sortAfter`。

例如，对于模组MOD，以下是`build.txt`片段：
```
modReferences = ModA
weakReferences = ModB
sortBefore = ModB, ModC
sortAfter = ModD
```
- MOD将加载在`ModC`之前，因为它包含在`sortBefore`中。
- MOD将加载在`ModB`之前，因为它包含在`sortBefore`中，即使`ModB`也包含在`weakReferences`中。
- MOD将加载在`ModA`之后，因为它包含在`modReferences`中，且不在`sortBefore`中。
- MOD将加载在`ModD`之后，因为它包含在`sortAfter`中。

作为一个特殊例外，tModLoader的内部`ModLoaderMod`（处理卸载内容等）始终首先加载，并在模组[实例化](#1-实例化)后插入模组加载顺序。

## 内容加载顺序
模组排序后，所有模组都被加载。

几个步骤使用[`Terraria.ModLoader.ModContent::LoadModContent()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/ModContent.cs#L398)原子地发生：所有子步骤在为 一个模组完成后才移动到下一个模组。以这种方式发生的步骤将被标记。

### 可加载类型
来源：
- [`Terraria.ModLoader.Core.AssemblyManager::IsLoadable()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Core/AssemblyManager.cs#L344)

下面有几个部分提到"可加载"类型。
如果类型通过以下检查，则认为它是*可加载的*：
1. 如果类型具有[`ExtendsFromMod`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/ExtendsFromModAttribute.cs)属性，如果该属性中列出的任何模组未加载，则认为该类型*不可*加载。
2. 如果类型扩展自*不可*加载的类型，则它也被认为*不可*加载。
3. 如果类型嵌套在*不可*加载的类型中，或由*不可*加载的泛型类型构造，则它也被认为*不可*加载。
4. 如果类型实现任何*不可*加载的接口，则它也被认为*不可*加载。
5. 通过所有这些检查后，该类型被认为是可加载的。

### 1. 实例化
来源：
- [`Terraria.ModLoader.Core.AssemblyManager::InstantiateMods()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Core/AssemblyManager.cs#L243)
- [`Terraria.ModLoader.Core.AssemblyManager::Instantiate()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Core/AssemblyManager.cs#L201)
- [`Terraria.ModLoader.Core.AssemblyManager::VerifyMod()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Core/AssemblyManager.cs#L224)

第一步是**实例化**，其中所有启用的模组的程序集被加载到内存中。这一步原子地发生。

按顺序：
1. 模组被*验证*：模组的程序集名称必须与其内部名称匹配，模组必须至少有一个命名空间以内部名称开头的可加载类型，且模组必须最多有一个可加载的[`Terraria.ModLoader.Mod`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Mod.cs)子类。
2. 创建模组的`Mod`子类实例（如果没有子类则创建`Mod`实例）。
3. 填充`Mod`上的以下字段：`File`；`Code`；`Logger`；`Side`；`DisplayName`；`TModLoaderVersion`；`TranslationForMods`。

这一步是调用您的`Mod`子类的构造函数的地方，也是将字段分配给`Mod`的好地方，该字段不在子步骤3中自动填充（例如[`PreJITFilter`](#jitting）、`ContentAutoLoadingEnabled`等）。

### 2. 内容加载和JITting
下一步是**内容加载**，其中模组的所有内容和系统加载。
这也是模组的程序集被**JITted**的步骤。这两个步骤同时发生：JITting在第一个模组开始加载之前开始，必须在进入预设置之前完成。

#### 内容加载
来源：
- [`Terraria.ModLoader.ModContent::Load()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/ModContent.cs#L289)
- [`Terraria.ModLoader.ModType::Load()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/ModType.cs#L27)

这一步骤原子地发生。

按顺序：
1. `Mod::loading`设置为`true`，允许内容添加到模组。
2. 模组自动加载所有返回[`ModConfig::Autoload()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Config/ModConfig.cs#L38)中为`true`的可加载的[`Terraria.ModLoader.Config::ModConfig`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Config/ModConfig.cs)子类（按内部名称排序）。
3. 模组通过调用[`Mod::CreateDefaultContentSource()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Mod.Hooks.cs#L11)准备加载资产。
4. 模组尝试自动加载所有内容：
	1. 模组加载所有本地化文件并注册所有找到的本地化键。
	2. 所有以`Mods.MODNAME.GameTips.`开头的本地化键都被添加到游戏提示列表。
	3. `Mod::ModSourceBestiaryInfoElement`被自动分配。
	4. 如果`Mod::ContentAutoloadingEnabled`为`true`：通过在新内容类型的实例上调用[`Mod::AddContent()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Mod.cs#L152)来加载所有[可自动加载](#可自动加载内容)内容。
		- 这就是调用`ILoadable::IsLoadingEnabled()`和`Load()`的地方！
		- 扩展来说，这 就是调用`ModType::ValidateType()`、`InitTemplateInstance()`、`Load()`和`Register()`的地方（按该顺序）。
	5. 如果不在专用服务器上：如果`Mod::GoreAutoloadingEnabled`、`MusicAutoLoadingEnabled`、`BackgroundAutoloadingEnabled`和`CloudAutoloadingEnabled`（分别为）都为`true`，则加载仅资产gores、音乐、背景和云。
5. 调用[`Mod::Load()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Mod.Hooks.cs#L20)。
6. 对模组中每个已知的`ModSystem`调用[`ModSystem::OnModLoad()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/ModSystem.cs#L48)。**如果您在此步骤中尝试添加新的`ModSystem`，tModLoader不会在其中调用`OnModLoad()`！**
7. `Mod::loading`设置为`false`。**在这一点上，不能再向模组添加内容。**

#### 可自动加载内容
来源：
- [`Terraria.ModLoader.Mod::Autoload()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Mod.Internals.cs#L45)

如果满足以下条件，则类型是*可自动加载的*：
- 该类型是可加载的。
- 该类型实现`ILoadable`接口（`ModType`这样做，所以任何子类也这样做）。
- 该类型不是抽象的。
- 该类型不包含未绑定的泛型参数（例如`public class MyItem<T> : ModItem`是*不可*自动加载的）。
- 该类型具有无参数构造函数（即使是私有的）。
- 该类型要么没有[`Autoload`属性](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/AutoloadAttribute.cs)，要么该属性允许加载。

#### JITting
来源：
- [`Terraria.ModLoader.Core.AssemblyManager::JITModAsync()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Core/AssemblyManager.cs#L369)
- [`Terraria.ModLoader.JITFilters`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/JITFilters.cs)

另请参阅：
- [JIT异常 - 弱引用](https://github.com/tModLoader/tModLoader/wiki/JIT-Exception#weak-references)
- [专家级跨模组内容 - JIT注意事项](https://github.com/tModLoader/tModLoader/wiki/Expert-Cross-Mod-Content#jit-considerations)

JITting在所有模组的可加载类型的方法集上执行。具体来说，如果满足以下所有条件，则方法会被JITted：
1. 方法的包含类型是[可加载的](#可加载类型)。
2. 方法的包含类型通过模组的[`PreJITFilter`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Mod.cs#L104)。
3. 如果方法的包含类型是嵌套的或泛型的，父类型/泛型类型定义也必须通过模组的`PreJITFilter`。
4. 方法不能是抽象的，不能包含泛型参数，且必须在正在JITted的类型上声明（即，不要JIT一个在子类上未被重写的非重写虚拟方法）。

默认情况下，如果该类型/方法上的所有`MemberJit`属性在`MemberJitAttribute::ShouldJIT()`中返回`true`，或者该类型/方法没有`MemberJit`属性，则该类型或方法通过模组的`PreJITFilter`。tModLoader提供以下`MemberJITAttribute`子类供使用：
- `NoJITAttribute`：成员永远不会被JITted。
- `JITWhenModsEnabledAttribute`：仅当指定模组也已启用时，成员才会被JITted。

请注意，如果您需要更多控制，可以制作自己的`PreJITFilter`和`MemberJITAttribute`子类。

### 3. 设置前
来源：
- [`Terraria.ModLoader.ModContent::ResizeArrays()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/ModContent.cs#L560)

在加载内容和设置内容之间采取了一些步骤。即：
1. 所有内容数组都被调整大小。这包括所有ID集。
2. 如果玩家使用了模组化的资源集，则在此处恢复。

### 4. 设置内容
来源：
- [`Terraria.ModLoader.Mod::SetupContent()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Mod.Internals.cs#L23)

下一步是**设置**。这是一个非常简单的步骤：为模组中每个加载的`ModType`调用`ModType::SetupContent()`。在大多数情况下，`ModType`子类将在此处调用`ModType::SetStaticDefaults()`，以及一些特定于每个`ModType`的额外工作。如果您需要查看每个`ModType`所做的特定额外工作，请查看它们。

这一步原子地发生。

### 5. 额外设置
在调用`SetupContent()`之后但在调用`PostSetupContent()`之前执行的一些额外工作。按顺序：
1. `ContentSamples`被重新初始化。在此步骤之后，它现在将包含所有模组化数据，以及在`SetDefaults()`中对原版内容所做的任何更改。
	- `ContentSamples`首先加载NPC实例，然后是弹射物，然后是物品，然后是NPC生物图鉴稀有星。
2. 设置了一些瓷砖合并数据（请参阅`Terraria.Main::SetupAllBlockMerge()`和`ModTile::PostSetupTileMerge()`）。
3. 设置了基于坐骑的Buff数据（请参阅`Terraria.Main::Initialize_BuffDataFromMountData()`）。

### 6. 后设置内容
对所有模组执行的最后一步是**后设置内容**。这是常用于跨模组兼容性的步骤。这一步原子地发生。

按顺序：
1. 调用[`Mod::PostSetupContent()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Mod.Hooks.cs#L27)。
2. 对模组中每个已知的`ModSystem`调用`ModSystem::PostSetupContent()`。
3. 所有请求的模组资产（`ModContent::Request<T>()`）被加载（请参阅[`Mod::TransferAllAssets()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/Mod.Internals.cs#L88)）。

### 7. 最终设置
来源：
- [`Terraria.ModLoader.ModContent::SetupBestiary()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/ModContent.cs#L420)
- [`Terraria.ModLoader.ModContent::SetupRecipes()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/ModContent.cs#L445)
- [`Terraria.ModLoader.RecipeLoader::AddRecipes()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/RecipeLoader.cs#L39)

在调用`PostSetupContent()`之后会发生很多事情，包括（按顺序）：
1. 许多`ModType`有一个"最终设置"阶段在此处调用。这包括缓存本地化文本、设置全局变量、设置模组添加的钩子等。
2. 调用`ModSystem::ModifyGameTipVisibility()`。
3. 设置生物图鉴。这就是调用`NPCLoader::SetBestiary()`的地方。
4. 设置物品掉落规则。这就是调用`NPCLoader::ModifyGlobalLoot()`、`NPCLoader::ModifyNPCLoot()`和`ItemLoader::ModifyItemLoot()`的地方。
5. 设置配方。这是加载中的"添加配方..."步骤，就是按顺序调用以下方法的地方：`Mod::AddRecipeGroups()`、`ModSystem::AddRecipeGroups()`、`Mod::AddRecipes()`、`ModSystem::AddRecipes()`、`ModItem::AddRecipes()`、`GlobalItem::AddRecipes()`、`Mod::PostAddRecipes()`、`ModSystem::PostAddRecipes()`、`ModSystem::PostSetupRecipes()`。
6. 如果玩家使用的是模组化菜单或Boss条样式，则在此处恢复。

上面的一些步骤只是亮点；请参阅`ModContent::Load()`末尾查看完整列表。

## 最终笔记
再一次，本指南可能会随着tModLoader更新而过时；检查加载顺序的最佳方式是自己阅读[`ModContent::Load()`](https://github.com/tModLoader/tModLoader/blob/1.4.4/patches/tModLoader/Terraria/ModLoader/ModContent.cs#L289)（最好在允许跳转到方法定义的环境中）。
