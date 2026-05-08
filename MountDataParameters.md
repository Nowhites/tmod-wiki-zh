### 这些是您可以在ModMount的SetDefaults中使用的内容

| MountData | 描述 |
| :-- | :-- |
| mountData.spawnDust (int) | 骑乘或下马时生成的灰尘ID。 |
| mountData.buff (int) | 分配给骑乘的buff的ID号。 |
| mountData.abilityChargeMax (int) | 用于Scutlix骑乘，表示骑乘能力充电直到停止充电的最大时间（以帧为单位）。对于Scutlix骑乘，它影响骑乘眼睛照明颜色变化的速率。Scutlix骑乘的值越高，眼睛照明颜色值中蓝色值每帧升高的越慢。 |
| mountData.abilityCooldown (int) | 骑乘可以调用Mount.UseAbility或ModMount可以调用MountLoader.UseAbility之前的帧数。 |
| mountData.abilityDuration (int) | Scutlix骑乘的能力使用后，屏幕上准心存在的帧数。 |
| mountData.swimSpeed (float) | 此参数用于原版海龟骑乘、钻头骑乘和可爱猪笼草骑乘。它表示水平速度，即每帧可以在x轴上移动多少像素。 |
| mountData.heightBoost (int) | 骑乘时玩家在垂直轴上提升的像素数。 |
| mountData.fallDamage (float) | 掉落伤害乘数。 |
| mountData.runSpeed (float) | 骑乘可以左右移动的像素数，可能在应用于玩家位置更新前一帧之前被其他变量修改。 |
| mountData.dashSpeed (float) | 骑乘处于冲刺状态时的移动速度。 |
| mountData.flightTimeMax (int) | 骑乘处于飞行状态的帧数。 |
| mountData.jumpHeight (int) | 按下跳跃按钮时玩家和骑乘上升的高度（负y速度）。 |
| mountData.acceleration (float) | 骑乘在空气中由于重力在y轴上下落越来越快的速率。要找到在任何给定时间骑乘时的下落速度（以像素/帧为单位），将为您的当前Y速度 - 加速度 + 重力 + 0.001。重力的值将在0.25到1之间，这取决于您在世界表面位置方面的x位置除以6。 |
| mountData.jumpSpeed (float) | 按下跳跃按钮时玩家和骑乘向跳跃高度（负y速度）上升的速率。 |
| mountData.blockExtraJumps (boolean) | |
| mountData.totalFrames (int) | 骑乘在各种条件下移动时涉及的动画帧总数。骑乘可以具有的运动状态顺序如下：站立、跑步、自由下落、飞行、游泳、冲刺 |
| standingFrameStart (int) | 骑乘动画帧数组中起始帧的索引 |
| mountData.constantJump (boolean) | |
| mountData.MinecartUpgradeDashSpeed | 此矿车在矿车升级套件激活时的替代冲刺速度。默认为20f。 |
| mountData.MinecartUpgradeAcceleration | 此矿车在矿车升级套件激活时的替代加速度。默认为0.1f。 |
| mountData.MinecartUpgradeJumpHeight | 此矿车在矿车升级套件激活时的替代跳跃高度。默认为15f。 |
| mountData.MinecartUpgradeJumpSpeed | 此矿车在矿车升级套件激活时的替代跳跃速度。默认为5.15f。 |
| mountData.MinecartUpgradeRunSpeed | 此矿车在矿车升级套件激活时的替代运行速度。默认为20f。 |
