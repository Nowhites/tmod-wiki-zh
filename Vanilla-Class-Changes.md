# 物品（Item）

## 属性

### public ModItem modItem

控制该物品行为的 ModItem 实例。如果这不是一个模组物品，则此属性为 null。

## 方法

### public ItemInfo GetModInfo(Mod mod, string name)

获取与该物品实例关联的 ItemInfo 实例（使用给定名称并由给定模组添加）。

### public T GetModInfo\<T\>(Mod mod) where T : ItemInfo

与其他 GetModInfo 相同，但假定类名和内部名称相同。

### public void CloneDefaults(int type)

允许您复制不同类型物品的默认属性。

# 弹射物（Projectile）

## 属性

### public ModProjectile modProjectile

控制该弹射物行为的 ModProjectile 实例。如果这不是一个模组弹射物，则此属性为 null。

## 方法

### public ProjectileInfo GetModInfo(Mod mod, string name)

获取与该弹射物实例关联的 ProjectileInfo 实例（使用给定名称并由给定模组添加）。

### public T GetModInfo\<T\>(Mod mod) where T : ProjectileInfo

与其他 GetModInfo 相同，但假定类名和内部名称相同。

### public void CloneDefaults(int type)

允许您复制另一个类型弹射物的默认属性。

# NPC

## 属性

### public ModNPC modNPC

控制该 NPC 行为的 ModNPC 实例。如果这不是一个模组 NPC，则此属性为 null。

## 方法

### public NPCInfo GetModInfo(Mod mod, string name)

获取与该 NPC 实例关联的 NPCInfo 实例（使用给定名称并由给定模组添加）。

### public T GetModInfo\<T\>(Mod mod) where T : NPCInfo

与其他 GetModInfo 相同，但假定类名和内部名称相同。

### public void CloneDefaults(int type)

允许您复制另一个类型 NPC 的默认属性。

# 玩家（Player）

## 方法

### public ModPlayer GetModPlayer(Mod mod, string name)

获取与该玩家实例关联的 ModPlayer 实例（使用给定名称并由给定模组添加）。

### public T GetModPlayer\<T\>(Mod mod) where T : ModPlayer

与其他 GetModPlayer 相同，但假定类名和内部名称相同。

### public void VanillaUpdateInventory(Item item)

将物品的效果给予玩家，就像它在玩家物品栏中一样。这适用于原版和模组物品。

### public void VanillaUpdateEquip(Item item)

将物品的效果给予玩家，就像玩家已装备它一样。注意：对于配饰，您必须同时调用 VanillaUpdateEquip 和 VanillaUpdateAccessory。这适用于原版和模组物品。

### public void VanillaUpdateAccessory(Item item, bool hideVisual, ref bool flag, ref bool flag2, ref bool flag3)

将物品的效果给予玩家，就像玩家已在配饰槽中装备它一样。注意：对于配饰，您必须同时调用 VanillaUpdateEquip 和 VanillaUpdateAccessory。这适用于原版和模组物品。

### public void VanillaUpdateVanityAccessory(Item item)

将物品的效果给予玩家，就像玩家已在时装配饰槽中装备它一样。
