***
本指南已更新至1.4版本。如果您需要查看此wiki页面的1.3旧版本，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Basic-NPC/0883f94540ceb6a3350a5d8d8094c31a749f1c02)
***

# 什么是NPC

NPC是"非玩家角色"的缩写，在Terraria中，这些是敌人、生物、城镇居民，以及很少见的以NPC形式出现但你可以伤害的弹幕（如恶魔火焰球、暗影法师的水球）。

**####**

**此页面非常不完整；有关其他NPC相关页面，请访问wiki搜索栏**

**####**

# 生成弹幕

如果您想让您的敌对`NPC`生成弹幕，首先确保您生成的弹幕是敌对的而非友方的，这样它不会伤害其他敌对NPC，也不会伤害玩家和友方生物。
此代码通常放在[AI](https://docs.tmodloader.net/docs/stable/class_mod_n_p_c.html#ae8d0a987b5ccdb2c583e789af32c9287) hook中。它展示了最常见的情况：从NPC直接向目标玩家（通常是最近的）发射一个弹幕：

```csharp
NPC.TargetClosest();
if (NPC.HasValidTarget && Main.netMode != NetmodeID.MultiplayerClient)
{
    var source = NPC.GetSource_FromAI();
    Vector2 position = NPC.Center;
    Vector2 targetPosition = Main.player[NPC.target].Center;
    Vector2 direction = targetPosition - position;
    direction.Normalize();
    float speed = 10f;
    int type = ProjectileID.PinkLaser;
    int damage = NPC.damage; //如果弹幕是敌对的，传入NewProjectile的伤害值将被应用翻倍，在专家模式下再翻倍，所以在缩放弹幕时请记住这一点，如果您使用NPC.damage（专家/大师模式下也会增加）
    Projectile.NewProjectile(source, position, direction * speed, type, damage, 0f, Main.myPlayer);
}
```

如果您的`AI`中已经有`NPC.TargetClosest();`代码，您可以省略它。您通常希望将其与[计时器](https://github.com/tModLoader/tModLoader/wiki/Time-and-Timers)结合使用，如果您想以其他方式生成，[ExampleGun](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Weapons/ExampleGun.cs)展示了一些您可以转移使用的变体（逻辑上，不要将代码复制到NPC中，它不起作用）。访问[NewProjectile](https://github.com/tModLoader/tModLoader/wiki/Projectile-Class-Documentation#public-static-int-newprojectileientitysource-spawnsource-float-x-float-y-float-speedx-float-speedy-int-type-int-damage-float-knockback-int-owner--255-float-ai0--0f-float-ai1--0f-)文档获取更多信息。

# 常见问题
### NPC从不生成或手动生成后立即消失
如果您在`SetDefaults`中错误地写了`NPC.life = value`而不是`NPC.lifeMax = value`，就会发生这种情况。

### NPC在专家模式中属性高很多
如果基础属性如伤害、防御和lifeMax相对较小（例如相当于蓝色史莱姆），就会发生这种情况。在`SetStaticDefaults`中添加`NPCID.Sets.DontDoHardmodeScaling[Type] = true`。

### 生成的弹幕没有伤害/立即消失
确保`Projectile.NewProjectile`中的`Owner`参数设置为`Main.myPlayer`。一个常见的误解是将其设置为`NPC.whoAmI`，但这不是游戏所期望的。所有者存在是为了确定在哪一方/客户端执行各种逻辑，如造成伤害或处理弹幕消失。

### 生成的弹幕伤害太高
在专家模式中，NPC.damage会按2倍缩放（默认）。各种NPC在达到专家模式时也会再次增加伤害缩放（如果您使用NPC.damage生成弹幕，请注意这一点）。除了所有这些之外，敌对弹幕获得自动的x2伤害，在专家模式中再增加x2。如果您想抵消这一点，在生成弹幕前将伤害减半，并在专家模式中再次减半。