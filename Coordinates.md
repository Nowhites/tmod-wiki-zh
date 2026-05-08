# 坐标系统

理解坐标系统对于tModLoader模组制作至关重要。泰拉瑞亚使用多个坐标系统，每个都有其特定的用途。

## 世界坐标 vs 屏幕坐标

### 世界坐标
世界坐标用于表示游戏中世界的实际位置：
- 原点(0, 0)在地图的左上角
- X轴向右增加（向右移动X增加）
- Y轴向下增加（向下移动Y增加）
- 这与数学中的标准笛卡尔坐标不同

### 屏幕坐标
屏幕坐标用于表示在玩家屏幕上的位置：
- 原点(0, 0)在屏幕的左上角
- X轴向右增加
- Y轴向下增加

## 像素坐标 vs 瓷砖坐标

### 像素坐标
像素坐标以像素为单位表示精确位置：
```csharp
Vector2 pixelPosition = new Vector2(100, 200); // 100像素向右，200像素向下
```

### 瓷砖坐标
瓷砖坐标以瓷砖为单位表示位置，每个瓷砖是16x16像素：
```csharp
Point tilePosition = new Point(10, 20); // 第10块瓷砖横向，第20块瓷砖纵向
```

## 坐标转换

### 世界坐标到屏幕坐标
```csharp
Vector2 worldPosition = new Vector2(500, 300);
Vector2 screenPosition = worldPosition - Main.screenPosition;
```

### 屏幕坐标到世界坐标
```csharp
Vector2 screenPosition = new Vector2(100, 100);
Vector2 worldPosition = screenPosition + Main.screenPosition;
```

### 像素坐标到瓷砖坐标
```csharp
Vector2 pixelPosition = new Vector2(160, 320);
Point tilePosition = (pixelPosition / 16).ToPoint();
// 或者
int tileX = (int)(pixelPosition.X / 16f);
int tileY = (int)(pixelPosition.Y / 16f);
```

### 瓷砖坐标到像素坐标
```csharp
Point tilePosition = new Point(10, 20);
Vector2 pixelPosition = tilePosition.ToVector2() * 16f;
// 或者
Vector2 pixelPosition = new Vector2(tilePosition.X * 16, tilePosition.Y * 16);
```

## Main.screenPosition

`Main.screenPosition`表示当前视野左上角的世界坐标。这对于确定玩家能看到什么以及相对于视野的位置非常重要。

```csharp
// 获取屏幕视野范围内的世界区域
Rectangle screenRect = new Rectangle(
    (int)Main.screenPosition.X,
    (int)Main.screenPosition.Y,
    Main.screenWidth,
    Main.screenHeight
);
```

## 实体位置

### 玩家位置
```csharp
// 玩家世界位置（像素）
Vector2 playerPosition = Main.LocalPlayer.position;

// 玩家世界位置（瓷砖）
Point playerTilePosition = (Main.LocalPlayer.position / 16f).ToPoint();

// 玩家屏幕位置
Vector2 playerScreenPosition = Main.LocalPlayer.position - Main.screenPosition;
```

### NPC和弹射物位置
```csharp
// NPC位置
Vector2 npcPosition = npc.position;
Vector2 npcCenter = npc.Center;

// 弹射物位置
Vector2 projectilePosition = projectile.position;
Vector2 projectileCenter = projectile.Center;
```

## 鼠标坐标

```csharp
// 鼠标世界坐标
Vector2 mouseWorld = Main.MouseWorld;

// 鼠标屏幕坐标
Vector2 mouseScreen = Main.mouseState.Position;

// 鼠标瓷砖坐标
Point mouseTile = (Main.MouseWorld / 16f).ToPoint();
```

## 常见用途

### 检测实体是否在屏幕上
```csharp
public static bool IsOnScreen(Vector2 position, int width, int height)
{
    Rectangle entityRect = new Rectangle((int)position.X, (int)position.Y, width, height);
    Rectangle screenRect = new Rectangle(
        (int)Main.screenPosition.X,
        (int)Main.screenPosition.Y,
        Main.screenWidth,
        Main.screenHeight
    );
    return entityRect.Intersects(screenRect);
}
```

### 绘制相对于屏幕的内容
```csharp
public override void PostDraw(SpriteBatch spriteBatch, Color lightColor)
{
    Vector2 screenPosition = npc.position - Main.screenPosition;
    spriteBatch.Draw(texture, screenPosition, Color.White);
}
```

## 重要提醒

1. **Y轴向下增加**: 在泰拉瑞亚中，Y轴向下增加，这与数学中的标准坐标系统相反
2. **单位一致**: 确保在计算中使用一致的单位（像素或瓷砖）
3. **考虑视野**: 在绘制或检测位置时要考虑`Main.screenPosition`
4. **使用Center**: 对于碰撞检测和距离计算，通常使用实体的Center而不是position
