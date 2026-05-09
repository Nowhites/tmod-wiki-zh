协作开发的首要规则是_选择一种风格并坚持下去_。以下样式指南应适用于tModLoader的所有未来贡献。

请体谅过去的开发者，不要仅仅为了格式而重新格式化代码。重新格式化PR将仅在相关代码有active需要更改时才会被考虑。

以下是有关常规C#设计和样式的有用资源
* https://google.github.io/styleguide/csharp-style.html
* https://docs.microsoft.com/en-us/dotnet/standard/design-guidelines/
* https://github.com/ktaranov/naming-convention/blob/master/C%23%20Coding%20Standards%20and%20Naming%20Conventions.md

如果本指南中未列出某些内容，请使用最佳判断在匹配现有代码和上述建议之间进行选择。

## 标识符名称
对以下使用`camelCase`
* 局部变量
* 方法参数
* 私有字段

其他所有内容使用`PascalCase`。

> _**为什么：除了方法参数，camelCase表示不是任何公共API一部分的变量。**_

对属性'backing fields'使用`_camelCase`。私有字段也可以选择加`_`前缀。

> _**为什么：`_`表示该字段是特定成员的实现细节，对类的整体功能不重要。**_

对于包含逻辑的类（和结构），使用属性而不是字段。不包含逻辑且仅用作数据的结构应使用字段。

> _**为什么：API实现细节可能在遥远的将来发生变化。使用属性和自动属性允许修改或添加getter和setter，而不会强制依赖项重新编译。JIT在内联自动属性方面非常高效，因此在Release模式下几乎没有性能问题。由于属性不能与`ref`变量一起使用，简单的数据`struct`应该改为使用字段。Terraria本身很少使用属性。Microsoft指南建议完全避免公共字段。**_

## 大括号
使用[K&R Style](https://en.wikipedia.org/wiki/Indentation_style#K&R_style)。语句、方法和方法签名的声明在同一行使用大括号。所有其他声明使用新行。这主要由.editorconfig强制执行

在单行`if/else/using`语句上可以省略大括号。例如：
```cs
if (canRestoreFlag) {
    for (int k = 0; k < canRestore.Count; k++) {
        if (canRestore[k] > 0)
            infos[k] = null;
    }
}
```

以下是**禁止的**

```cs
if (canRestoreFlag) // Body spans multiple lines
    for (int k = 0; k < canRestore.Count; k++) // Braces required on for loops
        if (canRestore[k] > 0) // Ok
            infos[k] = null;
```

**不要**混用缺失的大括号
```cs
if (ConfigManager.AnyModNeedsReload()) // Forbidden. Must have braces to match `else`
	needsReload = true;
else {
	foreach (NetConfig pendingConfig in pendingConfigs)
		ConfigManager.GetConfig(pendingConfig).OnChanged();
}
```

**例外**：ExampleMod贡献；_单行_语句和方法体应加大括号。只有属性（即`bool SomeProperty => true;`）可以保持上述定义。

_**为什么：在Terraria充满小型控制流语句的情况下，平衡良好的视觉分离和保持代码在屏幕上。**_

### 文件作用域命名空间
[文件作用域命名空间](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/csharp-10.0/file-scoped-namespaces)是一个相对较新的C#语言特性。因此，我们将推迟在学习资源中使用它，直到它的使用变得更加普遍。我们在tModLoader中使用它，但在ExampleMod中**不**使用它。

## 注释
关于单行的短注释应放在同一行。
```cs
public override void SetDefaults() {
	item.damage = 12; // The damage for projectiles isn't actually 12, it actually is the damage combined with the projectile and the item together.
	item.DamageType = DamageClass.Ranged;
	...
}
```
**不要**使用块注释。改用多行注释
```cs
public override bool GetDefaultVisiblity(PlayerDrawSet drawInfo) {
	// The layer will be visible only if the player is holding an ExampleItem in their hands. Or if another modder forces this layer to be visible.
	return drawInfo.drawPlayer.HeldItem?.type == ModContent.ItemType<ExampleItem>();

	// If you'd like to reference another PlayerDrawLayer's visibility,
	// you can do so by getting its instance via ModContent.GetInstance<OtherDrawLayer>(), and calling GetDefaultVisiblity on it
}
```

## 成员顺序
遵循一般顺序：内部类、静态成员、字段、属性、构造函数、方法。按功能对字段和方法进行分组。从最大/最常用的功能开始，到最小/最少使用的结束。

```cs
public class TmodFile : IEnumerable<TmodFile.FileEntry>
{
	public class FileEntry
	{
		...
	}

	public const float CompressionTradeoff = 0.9f;

	private static string Sanitize(string path) => path.Replace('\\', '/');

	public readonly string path;

	private FileStream fileStream;
	private IDictionary<string, FileEntry> files = new Dictionary<string, FileEntry>();
	private FileEntry[] fileTable;

	...
	
	private bool? _validModBrowserSignature;
	internal bool ValidModBrowserSignature {
		get { ... }
	}

	internal TmodFile(string path, string name = null, Version version = null) { ... }

	public bool HasFile(string fileName) {...}
	public byte[] GetBytes(FileEntry entry) {...}
	public byte[] GetBytes(string fileName) {...}
	
	...
	
	
	internal void AddFile(string fileName, byte[] data) {...}
	internal void RemoveFile(string fileName) {...}
	
	...
}
```

Backing fields可以在对应属性之前声明。
```cs
private readonly List<PlayerDrawLayer> _childrenBefore = new List<PlayerDrawLayer>();
public IReadOnlyList<PlayerDrawLayer> ChildrenBefore => _childrenBefore;

private readonly List<PlayerDrawLayer> _childrenAfter = new List<PlayerDrawLayer>();
public IReadOnlyList<PlayerDrawLayer> ChildrenAfter => _childrenAfter;
```

支持'lookup fields'可以与它们服务的方法放在一起。
```cs
private static readonly char[] nameSplitters = new char[] { '/', ' ', ':' };
public static void SplitName(string name, out string domain, out string subName) {
	int slash = name.IndexOfAny(nameSplitters); // slash is the canonical splitter, but we'll accept space and colon for backwards compatability, just in case
	if (slash < 0)
		throw new MissingResourceException("Missing mod qualifier: " + name);

	domain = name.Substring(0, slash);
	subName = name.Substring(slash + 1);
}
```

## 方法长度
保持方法简短。当一个方法有多个'部分'时，考虑将它们拆分为多个较小的方法（如果可能，使它们`static`/pure）。

> _**为什么：好的方法名可以代替可能随时间腐烂的注释。方法参数和返回值清楚地显示了大方法各部分之间的数据流。较小的方法减少了读者的精神负担，并通过限制作用域内变量的数量减少了编程错误。**_

## 控制流嵌套
尝试避免方法中超过3层控制流嵌套。避免在长的`if`块末尾有`else`语句。尽早处理错误，快速失败，以及`return`而不是创建长的`else if`链。

> _**为什么：提高可读性。高度嵌套的控制流很容易迷失。守护语句通过消除错误和情况使方法更容易阅读。参见https://en.wikipedia.org/wiki/Guard_(computer_science)**_

## 视觉换行
使用空行将长方法分解为逻辑块。如果方法继续，在任何缩进语句之后总是放一个空行。不要在每个字段或每一行之间放空格。
```cs
internal void AddFile(string fileName, byte[] data) {
	fileName = Sanitize(fileName);
	int size = data.Length;

	if (size > MIN_COMPRESS_SIZE && ShouldCompress(fileName)) {
		using (var ms = new MemoryStream(data.Length)) {
			using (var ds = new DeflateStream(ms, CompressionMode.Compress))
				ds.Write(data, 0, data.Length);

			var compressed = ms.ToArray();
			if (compressed.Length < size * COMPRESSION_TRADEOFF)
				data = compressed;
		}
	}

	lock (files) {
		files[fileName] = new FileEntry(fileName, -1, size, data.Length, data);
	}

	fileTable = null;
}
```

## 转换而不是使用`as`

如果强制转换成功，`ClassCastException`比从`as`返回的`NullReferenceException`信息更丰富，将在更有用的行号抛出。

对于安全类型检查，使用模式匹配而不是使用`as`加`!= null`检查。

```cs
var exampleItem = (ExampleItem)item.ModItem;

// with pattern matching
if (item.ModItem is ExampleItem exampleItem) {
    ...
}
```

❌ 避免
```cs
var exampleItem = item.ModItem as ExampleItem;
if (exampleItem != null) {
    ...
}
```

## 注释掉原版代码，而不是删除它
使用`/*`和`*/`注释多行。它们应该各自单独成行。
```cs
else if (buffType[j] == 117) {
	allDamage += 0.1f;
	/*
	meleeDamage += 0.1f;
	rangedDamage += 0.1f;
	magicDamage += 0.1f;
	minionDamage += 0.1f;
	*/
}
```

修改单行时。只有当不容易看出tML添加了什么而什么是原版时，才保留原版代码的注释版本。

当多个更改属于同一修复/功能/重构时，也使用井号
```cs
//if (item.maxStack == 1 && item.Prefix(-3))
if (item.IsCandidateForReforge && item.Prefix(-3)) // TML: #StackablePrefixWeapons


//if (stack <= 0)
if (stack <= 0 && mouseItem.maxStack == 1) // TML: #StackablePrefixWeapons: Gameplay impact: stackable items will not get a prefix on craft


// TML attempts to make ApplyItemTime calls run on remote players, so this check is removed. #ItemTimeOnAllClients
// if (whoAmI == Main.myPlayer) {
if (true) {
	...
}
```

❌ 不必要的注释
```cs
//if (Main.mouseItem.IsTheSameAs(inv[slot])) {
if (Main.mouseItem.IsTheSameAs(inv[slot]) && ItemLoader.CanStack(inv[slot], Main.mouseItem)) {
```


## 保持补丁小
Terraria的源代码不存储在git上，相反tML的更改存储在patches/目录中的.patch文件中。尽可能小地保持补丁使处理Terraria更新和识别tML所需的确切更改变得更容易。

使用return/continue或goto来避免更改缩进。**仅当有5+行需要缩进时**

```cs
if (!WallLoader.PreDraw(j, i, wall, spriteBatch))
	goto PostDraw;

...

PostDraw:
WallLoader.PostDraw(j, i, wall, spriteBatch);
```

```cs
private bool ItemCheck_CheckCanUse(Item sItem) {
	if(!CombinedHooks.CanUseItem(this, sItem))
		return false;

	...
}
```

在具有多个return语句的方法末尾包装方法以插入hook
```cs
public void HitEffect(int hitDirection = 0, double dmg = 10.0) {
	VanillaHitEffect(hitDirection, dmg);
	NPCLoader.PostHitEffect(this, hitDirection, dmg);
}

public void VanillaHitEffect(int hitDirection = 0, double dmg = 10.0) {
	if (!active)
		return;
	
	if (...) {
		return;
	}
	
	...
}
```

提交时总是检查你的补丁，看看是否有办法最小化它们。

## 注意破坏性更改
对tModLoader源代码进行的某些更改一旦成为官方版本，可能会对模组制作者和玩家造成问题。例如，重命名模组使用的字段将导致tModLoader更新时这些模组中断。因此，我们有各种策略来维护兼容性。破坏性更改有两种：二进制不兼容和源代码不兼容。当更改导致二进制不兼容时，早期版本构建的模组将无法加载或无法正常工作。当更改导致源代码不兼容时，模组制作者下次构建模组时将不得不更改代码，但模组否则将继续正常运行。

我们为保持兼容性和维护功能而付出的努力取决于版本应该有多稳定。通常，这取决于对原版游戏的更新。在tModLoader仍在追赶原版更新时，破坏性更改的可能性更大。如果已经很长时间没有大的Terraria更新，tModLoader将在引入新功能或错误修复时尝试使先前构建的模组正常工作。

### 新Hook
添加新hook没有问题。

### 向hook添加参数
如果`public virtual void SomeHook()`变为`public virtual void SomeHook(int someParameter)`，使用旧方法的老模组将发现其模组功能受限，因为旧方法不再被调用。为了保持兼容性，我们有时会保留旧方法并将其标记为`Obsolete`

```cs
// New hook in ModItem.cs
public virtual void PickAmmo(Item weapon, Player player, ref int type, ref float speed, ref int damage, ref float knockback) {
}

// Old hook in ModItem.cs
[Obsolete("PickAmmo now has a weapon parameter that represents the item using the ammo.")]
public virtual void PickAmmo(Player player, ref int type, ref float speed, ref int damage, ref float knockback) {
}

// Calling site in ItemLoader.cs
public static void PickAmmo(Item weapon, Item ammo, Player player, ref int type, ref float speed, ref int damage, ref float knockback) {
	ammo.modItem?.PickAmmo(weapon, player, ref type, ref speed, ref damage, ref knockback);
	ammo.modItem?.PickAmmo(player, ref type, ref speed, ref damage, ref knockback); // deprecated

```

### 修改抽象方法
此方法和新方法需要改为`virtual`而不是`abstract`。上述所有说明仍然适用。

### 更改返回类型
更改返回类型会导致不兼容。TODO，更多信息。

### 向原版方法添加参数
我们通常不这样做，因为它会破坏兼容性。取而代之的是，添加一个新的重载并将当前方法标记为`Obsolete`。见下文。

### 向原版方法添加重载
当我们向原版方法添加重载时，通常是因为我们需要添加一个新参数来传入额外的上下文。如果意图是让所有模组制作者都使用新方法，我们将原始方法标记为`Obsolete`并使原始方法成为带有新参数某些默认值的新参数转发器。

如果一个方法以前只有一个重载，我们还需要确保用`[OriginalOverload]`标记原始方法以保持MonoMod兼容性。（这是因为现有事件的参数如果它们是唯一重载，则会从事件中省略，这不再是true。）[添加了PressurePlate设置、HitSwitch和SwitchTiles hooks](https://github.com/tModLoader/tModLoader/pull/4774/files#top)](https://github.com/tModLoader/tModLoader/pull/4774/files) PR是一个例子，请参阅原始`Collision.SwitchTiles`方法是如何被更改的。请参阅[Preserve backwards compatibility for MonoMod hooks](https://github.com/tModLoader/tModLoader/pull/4795) PR以获取有关此过程的信息。

### 示例
- [Rename ModProjectile.Kill to ModProjectile.OnKill](https://github.com/tModLoader/tModLoader/pull/3770) - 此示例显示典型的hook重命名情况。我们希望继续工作的模组构建，但希望立即将正在构建的模组移至新方法。旧hook设置为`Obsolete`，新hook以新名称添加。PR确保调用旧hook以保持现有行为。注意`ProjectileLoader.Kill_Obsolete`如何设置为`Obsolete`，而`ModProjectile.Kill`设置为`Obsolete`并标记为编译错误。`Obsolete`方法可以按设计调用标记为编译错误的`Obsolete`方法。也使用了tModPorter。代码使用`RenameMethod`移植到新方法名称，应用于ExampleMod，并有测试来验证其效果。
- [GlobalInfoDisplay.ModifyDisplayParameters refactor](https://github.com/tModLoader/tModLoader/commit/3ca9bf16a3722c35ea86377b7a28f4456b072743) - 此示例显示使多个方法过时并用新的组合方法替换。此示例展示使用tModPorter重命名方法和更改其签名：`RenameMethod`和`ChangeHookSignature`
- [Add parameter to InfoDisplay.DisplayValue method](https://github.com/tModLoader/tModLoader/pull/3604) - 此示例展示使用`tModPorter`的`ChangeHookSignature`更改hook签名。此示例不保持兼容性，在这种情况下没关系，因为hook的使用非常罕见。
