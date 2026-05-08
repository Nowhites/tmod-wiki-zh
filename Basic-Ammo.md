***
本指南已更新至1.4版本。如果您需要查看此wiki页面的1.3旧版本，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Basic-Ammo/3ac8255f8a511bc25d15090d52fcd045febccb4f)
***

# 什么是弹药？
弹药是一个将武器物品、弹药物品和弹射物连接在一起的系统。基本思路是这样的：武器物品有`Item.useAmmo`设置为AmmoID，武器应该使用的弹药物品有`Item.ammo`设置为相同的AmmoID，而该弹药物品有`Item.shoot`设置为使用该弹药时武器将发射的特定弹射物。

举个例子，让我们看看`木弓`和`燃烧箭`。`木弓`有`Item.useAmmo = AmmoID.Arrow;`而`燃烧箭`有`Item.shoot = ProjectileID.FireArrow;`和`Item.ammo = AmmoID.Arrow;`。当玩家射箭时，泰拉瑞亚会在玩家的物品栏中搜索`Item.ammo`与`木弓`的`Item.useAmmo`匹配的物品。如果找到弹药物品，则生成该弹药物品的`Item.shoot`弹射物，并消耗该弹药。

作为一个约定，AmmoID与第一个弹药物品的ItemID匹配。例如，AmmoID.Arrow和ItemID.WoodenArrow都等于40。所有其他箭有`Item.ammo = AmmoID.Arrow;`。

# 如何为我的武器使用原版弹药？
要使用原版弹药，只需将Item.useAmmo设置为正确的AmmoID。例如，ExampleMod中的`Example Gun`有`Item.useAmmo = AmmoID.Bullet;`。

# 如何制作一个原版弹药？
要制作属于原版弹药类的弹药，只需将Item.ammo设置为正确的AmmoID。例如，`Example Bullet`在其SetDefaults方法中有`Item.ammo = AmmoID.Bullet;`设置。此外，弹药物品还必须定义弹药发射的弹射物。`Example Bullet`展示了这个例子：`Item.shoot = ModContent.ProjectileType<Projectiles.ExampleBullet>();`

# 如何创建一个新的弹药类？
可以通过将你的一个弹药物品指定为AmmoID来创建新的弹药类。例如，Example Mod向我们展示了一个新的"ExampleCustomAmmo"弹药类。`ExampleCustomAmmo`物品有`Item.ammo = Item.type;`来将其指定为弹药类的定义弹药。`ExampleCustomAmmoGun`有`Item.useAmmo = ModContent.ItemType<ExampleCustomAmmo>();`来匹配我们与原版弹药物品建立的模式。
任何其他弹药将有`Item.ammo = ModContent.ItemType<ExampleCustomAmmo>()`，任何使用该弹药的其他武器将有`Item.useAmmo = ModContent.ItemType<ExampleCustomAmmo>()`。

# 如何用原版物品创建新的弹药类？
使用GlobalItem类在`SetDefaults`中设置`Item.ammo`，并在`PickAmmo`中将`type`设置为你制作的新弹射物。如果物品默认放置墙壁或方块，你必须手动重新添加"弹药"提示，因为原版逻辑默认阻止了这一点。

```cs
public class AmmoModificationsGlobalItem : GlobalItem
{
	public override void SetDefaults(Item entity)
	{
		if (entity.type == ItemID.Rope)
		{
			entity.ammo = ItemID.Rope;
		}
		if (entity.type == ItemID.VineRope)
		{
			entity.ammo = ItemID.Rope;
		}
		// 以此类推，SilkRope和WebRope也一样
	}

	public override void PickAmmo(Item weapon, Item ammo, Player player, ref int type, ref float speed, ref StatModifier damage, ref float knockback)
	{
		if (ammo.type == ItemID.Rope)
		{
			type = ModContent.ProjectileType<RopeShot>();
		}
		if (ammo.type == ItemID.VineRope)
		{
			type = ModContent.ProjectileType<VineRopeShot>();
			// 弹药物品通常有分配给它的shootSpeed，这会加到武器的shootSpeed上，我们可以这样复制这种行为。
			// 对于damage和knockback也可以做类似的事情，不过你要在物品提示中包含这些（见下文）
			speed += 2f;
		}
	}

	public override void ModifyTooltips(Item item, List<TooltipLine> tooltips)
	{
		// 添加弹药提示，因为它没有，因为放置型（"可放置"）提示替换了它
		// 只对放置型物品（墙壁/方块）需要这样做
		if (item.type == ItemID.Rope || item.type == ItemID.VineRope)
		{
			int index = tooltips.FindLastIndex(tt => tt.Mod.Equals("Terraria") && tt.Name.Equals("Placeable"));
			if (index != -1)
			{
				tooltips.Insert(index + 1, new TooltipLine(Mod, "Ammo", Language.GetTextValue("LegacyTooltip.34")));
			}
		}
	}
}
```

请注意，使用原版物品制作新物品有与其他模组冲突的风险，所以请谨慎使用以保持兼容性并防止玩家困惑。

# 使用多种弹药类型或忽略特定弹药物品的武器
[ModItem.CanChooseAmmo](https://docs.tmodloader.net/docs/stable/class_mod_item.html#a20ec23ea02f6acf3a68fa63aabb14917)钩子允许武器替代正常的弹药选择逻辑。一个常见用途是允许武器使用多种弹药类型。关于这方面的示例和额外说明，请参见[ExampleSpecificAmmoGun.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Weapons/ExampleSpecificAmmoGun.cs#L68)。

# 手动消耗弹药
弹药通常通过游戏逻辑被武器消耗，但有些情况下模组开发者可能需要手动选择和消耗弹药。最典型的例子是持续弹射物武器。这些包括涡流冲击枪、幻影、庆典MK2和凤凰。

**TODO**
