# 瓷砖类文档

此页面列出了与 `Tile` 类相关的方法和字段。如果您直接使用 `Tile`，可以查阅此页面以了解各种内容。如果您想在模组中创建瓷砖，这是不相关的，请参阅[基础瓷砖指南](https://github.com/tModLoader/tModLoader/wiki/Basic-Tile)。另请参阅 [Main.tile[]](https://github.com/tModLoader/tModLoader/wiki/Main-Class-Documentation#tile)。最好记住 `Main.tile[]` 可以包含 null 值，null 值为空/空气瓷砖。非 null 值也可能是空/空气瓷砖。

索引|
-----|
[字段](#字段和属性)|
[方法](#方法)|
[位字段数据](#位字段数据)|

# 字段和属性

`Tile` 类的结构非常紧凑和高效。您通常不想直接修改这些字段。所有值默认为 0。

| 字段 | 类型 | 描述 |
|----------|------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [type](#type) | ushort | 对应此瓷砖的 TileID。由于泥土瓷砖的值为 0，您需要检查其他值以了解此瓷砖是否有瓷砖。`Tile.active()` 可用于检查 `Tile` 是否是空气。 |
| [wall](#wall) | ushort | 对应此瓷砖的 WallID。值为 0 表示没有墙。 |
| [liquid](#liquid) | byte | 表示瓷砖中有多少液体。范围从 0（无液体）到 255（充满液体）。 |
| [sTileHeader](#sTileHeader) | ushort | 位字段。不要直接触碰，请使用方法。存储：导线、致动器、斜坡、颜色和半砖数据。 |
| [bTileHeader](#bTileHeader) | byte | 位字段。不要直接触碰，请使用方法。存储：液体类型、墙壁颜色和 Wire4 数据。 |
| [bTileHeader2](#bTileHeader2) | byte | 位字段。不要直接触碰，请使用方法。存储：帧号、wallFrameX 和 WallFrameNumber 数据。 |
| [bTileHeader3](#bTileHeader3) | byte | 位字段。不要直接触碰，请使用方法。存储：SkipLiquid、CheckLiquid 和 wallFrameY 数据。 |
| [frameX](#frameX)<br>[frameY](#frameY) | short | 对应此瓷砖使用的精灵表中 16x16（通常）区域的坐标。对于框架瓷砖，此值在世界加载或附近放置或开采其他瓷砖时自动设置。请参阅[框架与框架重要](https://github.com/tModLoader/tModLoader/wiki/Basic-Tile#framed-vs-frameimportant-tiles)了解更多详情。对于 FrameImportant 瓷砖，此值不会更改，将会在多人游戏中保存和同步。在这两种情况下，`frameX` 和 `frameY` 对应此瓷砖应绘制的精灵表中区域的左上角坐标。某些瓷砖（如圣诞树和武器架）使用这些字段的高位来进行特定于瓷砖的行为。模组制作者不应该尝试类似的方法，而应该使用 `ModTileEntity` |
| [collisionType](#collisionType) | int | 仅获取属性。未使用。 |

# 方法

## public Tile()

构造函数，所有值默认为 0。

## public Tile(Tile copy)

克隆另一个 Tile 的构造函数

## public object Clone()

创建 Tile 的克隆。`Tile clone = (Tile)Main.tile[x,y].Clone()`

## public void CopyFrom(Tile from)

从另一个 Tile 复制字段。

## public void ClearEverything()

清除所有字段。

## public bool active()

## public void active(bool active)

设置或检查是否存在瓷砖。

## public bool nactive()

如果 Tile 处于活跃状态但未被致动，则返回 true。等同于 `tile.active() && !tile.inActive()`。

## public bool inActive()

## public void inActive(bool inActive)

设置或检查瓷砖是否被致动。

## public void liquidType(int liquidType)

## public byte liquidType()

设置或检查液体类型。0 水，1 岩浆，2 蜂蜜。

## public bool lava()

## public void lava(bool lava)

## public bool honey()

## public void honey(bool honey)

设置或检查液体类型。

## public bool wire()

## public void wire(bool wire)

## public bool wire2()

## public void wire2(bool wire2)

## public bool wire3()

## public void wire3(bool wire3)

## public bool wire4()

## public void wire4(bool wire4)

设置或检查各别导线是否存在。

## public bool actuator()

## public void actuator(bool actuator)

设置或检查瓷砖上是否存在致动器。

## public byte color()

## public void color(byte color)

设置或检查瓷砖颜色。

## public byte wallColor()

## public void wallColor(byte wallColor)

设置或检查墙壁颜色。

## public byte frameNumber()

## public void frameNumber(byte frameNumber)

框架瓷砖通常有 3 种选择（备用图像），这设置或检查该值。

## public int wallFrameX()

## public void wallFrameX(int wallFrameX)

## public byte wallFrameNumber()

## public void wallFrameNumber(byte wallFrameNumber)

## public int wallFrameY()

## public void wallFrameY(int wallFrameY)

各种墙壁框架相关方法。

## public bool checkingLiquid()

## public void checkingLiquid(bool checkingLiquid)

## public bool skipLiquid()

## public void skipLiquid(bool skipLiquid)

各种液体移动相关方法。

## public bool topSlope()

## public bool bottomSlope()

## public bool leftSlope()

## public bool rightSlope()

## public bool HasSameSlope(Tile tile)

## public bool halfBrick()

## public void halfBrick(bool halfBrick)

## public byte slope()

## public void slope(byte slope)

## public static void SmoothSlope(int x, int y, bool applyToNeighbors = true)

各种斜坡相关方法。

## public override string ToString()

打印关于 Tile 的调试信息。

## public void ClearTile()

## public bool isTheSameAs(Tile compTile)

## public int blockType()

## public void ResetToType(ushort type)

## internal void ClearMetadata()

## public Color actColor(Color oldColor)

各种方法。

# 位字段数据

为了高效存储数据，每个 `Tile` 使用多个"头部"字段作为**位字段**。

**位字段**是用于位序列而不是实际值的变量。访问和修改位通常通过按位运算符完成：`|`（按位 OR）、`&`（按位 AND）、`^`（按位 XOR）、`~`（按位 NOT）、`<<`（左移）和 `>>`（右移）。

下面显示每个运算符的用法示例：

| 运算符 | 值 1 | 值 2 | 表达式 | 表达式（二进制） | 结果 | 结果（二进制） |
|-----------|---------|---------|------------|--------------------------|--------|----------------|
| `\|` | 35 | 68 | `35 \| 68` | `0010 0011 \| 0100 0100` | 103 | `0110 0111` |
| `&` | 39 | 68 | `39 & 68` | `0010 0111 & 0100 0100` | 4 | `0000 0100` |
| `^` | 47 | 68 | `47 ^ 68` | `0010 1111 ^ 0100 0100` | 107 | `0110 1011` |
| `~` | 68 | n/a | `~ 68` | `~ 0100 0100` | 187 | `1011 1011` |
| `<<` | 35 | 2 | `35 << 2` | `0010 0011 << 0000 0010` | 210 | `1000 1100` |
| `>>` | 35 | 2 | `35 >> 2` | `0010 0011 >> 0000 0010` | 8 | `0000 1000` |

| 运算符 | 解释 |
|----------|------------|
| `\|` | 如果值 1 中的位或值 2 中的位被设置，则结果中的位也被设置。 |
| `&` | 仅当值 1 和值 2 中的位都被设置时，结果中的位才被设置。 |
| `^` | 仅当值 1 或值 2 中的位被设置但不是两者都被设置时，结果中的位才被设置。 |
| `~` | 每个 1 位变为 0 位，反之亦然。 |
| `<<` | 值 1 中的每位向左移动值 2 次。从右侧填充零，任何溢出最高有效位（第一位）的位被移除。 |
| `>>` | 值 1 中的每位向右移动值 2 次。从左侧填充最高有效位，任何溢出最低有效位（最后一位）的位被移除。 |

`Tile` 类有四个位字段：`sTileHeader`、`bTileHeader`、`bTileHeader2` 和 `bTileHeader3`。这些位字段**不应**直接修改。相反，请使用 `Tile` 中的各种方法。

作为参考，以下是位字段中每位代表的内容以及通常访问的位置：

## sTileHeader

`-SSS UHGB RTAC CCCC`

`-`：未使用。

`SSS`：存储此 `Tile` 的斜坡类型的位置。从 `tile.slope()` 和 `tile.slope(byte)` 访问。0 无斜坡，1 左下斜坡，2 右下斜坡，3 左上斜坡，4 右上斜坡。

`U`：此 `Tile` 上是否有致动器。从 `tile.actuator()` 和 `tile.actuator(bool)` 访问。

`H`：此 `Tile` 是否为半砖。从 `tile.halfBrick()` 和 `tile.halfBrick(bool)` 访问。

`G`：此 `Tile` 上是否有绿导线。从 `tile.wire3()` 和 `tile.wire3(bool)` 访问。

`B`：此 `Tile` 上是否有蓝导线。从 `tile.wire2()` 和 `tile.wire2(bool)` 访问。

`R`：此 `Tile` 上是否有红导线。从 `tile.wire()` 和 `tile.wire(bool)` 访问。

`T`：此 `Tile` 是否被致动。从 `tile.inActive()` 和 `tile.inActive(bool)` 访问。

`A`：此 `Tile` 是否活跃（非空气）。从 `tile.active()` 和 `tile.active(bool)` 访问。

`C CCCC`：存储此 `Tile` 的颜料颜色类型的位置。从 `tile.color()` 和 `tile.color(byte)` 访问。值范围从 0 到 30。

## bTileHeader

`YLLW WWWW`

`Y`：此 `Tile` 上是否有黄导线。从 `tile.wire4()` 和 `tile.wire4(bool)` 访问。

`LL`：存储此 `Tile` 的液体类型的位置。从 `tile.lava()`、`tile.lava(bool)`、`tile.honey()` 和 `tile.honey(bool)` 访问。0 是水，1 是岩浆，2 是蜂蜜。

`W WWWW`：存储此 `Tile` 墙壁的颜料颜色类型的位置。从 `tile.wallColor()` 和 `tile.wallColor(byte)` 访问。值范围从 0 到 30。

## bTileHeader2

`WWNN XXXX`

`WW`：与墙壁框架相关。从 `tile.wallFrameNumber()` 和 `tile.wallFrameNumber(byte)` 访问。值范围从 0 到 3。

`NN`：此 `Tile` 的备用帧号。从 `tile.frameNumber()` 和 `tile.frameNumber(byte)` 访问。值可以范围从 0 到 3，但原版只使用 0 到 2。

`XXXX`：存储此 `Tile` 墙壁在其精灵表中的 frameX 的位置。从 `tile.wallFrameX()` 和 `tile.wallFrameX(byte)` 访问。值范围从 0 到 15。`tile.wallFrameX()` 在调用时返回此值 * 36，而 `tile.wallFrameX(byte)` 将此值设置为参数 / 36。

## bTileHeader3

`---S CYYY`

`---`：未使用。

`S`：用于液体更新的标志。从 `tile.skipLiquid()` 和 `tile.skipLiquid(bool)` 访问。

`C`：用于液体更新的标志。从 `tile.checkingLiquid()` 和 `tile.checkingLiquid(bool)` 访问。

`YYY`：存储此 `Tile` 墙壁在其精灵表中的 frameY 的位置。从 `tile.wallFrameY()` 和 `tile.wallFrameY(byte)` 访问。值范围从 0 到 7。这些位的处理方式与 `bTileHeader2` 中的 `XXXX` 位相同。
