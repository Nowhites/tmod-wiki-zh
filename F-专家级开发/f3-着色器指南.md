___
请先阅读[专家先决条件](Expert-Prerequisites)。<br>
不熟悉mod制作？那么您不应该遵循本教程<br>
_原文：_ https://forums.terraria.org/index.php?threads/a-beginners-guide-to-shaders.86128/
___

# 简介

着色器是非常强大的工具，如果您知道自己在做什么，它们是创建精美视觉效果的轻量级工具。然而，它们也相当复杂，实现门槛相当高。本指南的目的是帮助解决这个问题，并为编写您自己的泰拉瑞亚mod着色器提供一个入门课程。

[[https://i.imgur.com/GZ4aAqa.gif|inline]]

## 什么是着色器？

一般来说，着色器是一段创建或修改渲染输出的代码。用外行人的话来说，这基本上意味着"让事物变得美观的代码"。在游戏开发中，我们主要使用两种类型的着色器：顶点着色器和像素着色器（也称为片元着色器）。顶点着色器用于修改3D模型，例如移动或变形它们。像素着色器用于修改屏幕的实际像素输出。它们通常与顶点着色器协同工作，但在泰拉瑞亚中也可以独立运作。

理解着色器的一个重要点是它们不是只执行一次，而是"对每个X执行"。应用于3D模型的顶点着色器将对其每个顶点（点）运行其代码。应用于纹理的像素着色器将对该纹理覆盖的每个像素运行（是的，可以覆盖整个屏幕）。这一开始可能令人困惑，但在编写几个通道后您就会掌握它。

因为泰拉瑞亚不包含任何3D模型（可以说），它不使用任何顶点着色器。它确实使用像素着色器，可以大致分为三组：

1. 装甲着色器*：主要包括玩家染料，但也包含一些杂项效果，例如（一部分）柱力场和水着色器
2. 屏幕着色器：包括屏幕滤镜，如天体入侵和血月色调
3. 瓦片着色器：包括瓦片油漆

_* 在内部，这些被称为像素着色器，但为避免与包含集合混淆，我将这些称为装甲着色器而不是。_

实现您自己的瓦片着色器相当复杂，就我个人而言，除非您完全了解泰拉瑞亚的引擎（更重要的是及其限制），否则不应该这样做，因此您将限于制作装甲着色器和屏幕着色器。这两种着色器的工作方式及其参数存在根本差异，但它们的基本结构是相同的。

## 着色器结构

泰拉瑞亚着色器由三部分组成：参数、通道和技术。通道和技术没有真正被充分利用，所以对于为泰拉瑞亚编写着色器的目的，通道是修改像素输出的单个函数，所有通道都包含在一个技术中。如果我们以vanilla染料为例，每个染料都使用一个通道来改变装甲、配饰或坐骑的外观，所有这些通道都包含在单个技术中。

参数是从外部（即泰拉瑞亚）传入着色器的信息，例如着色器应使用的颜色。它允许您从单个通道创建多个效果（所有基本颜色染料使用相同的通道，只是使用不同的颜色参数）。当您创建自己的着色器并想使用泰拉瑞亚预先存在的着色器系统时（您应该这样做），您的着色器需要包含默认着色器的所有参数：如果没有，泰拉瑞亚将尝试设置不存在的参数，导致崩溃。装甲着色器和屏幕着色器不使用相同的参数集，因此将在各自的章节中讨论。

您可以在此帖子底部找到装甲着色器和屏幕着色器的空白示例。

## 装甲着色器

![Rising Dye](https://i.imgur.com/bCz7fFb.gif)

泰拉瑞亚的装甲着色器修改特定纹理的绘制方式。最明显的例子是可用于装甲、配饰和坐骑的各种染料，但它们也可用于将完全着色视觉效果应用于空白纹理（想想柱的力场：那完全是一个着色器）。

装甲着色器的参数如下：

```hlsl
sampler uImage0 : register(s0); // 您当前绘制的纹理
sampler uImage1 : register(s1); // 您可用于各种目的的辅助纹理。这通常是噪声贴图
float3 uColor;
float3 uSecondaryColor;
float uOpacity;
float uSaturation;
float uRotation;
float uTime;
float4 uSourceRect; // 当前绘制帧的位置和大小
float2 uWorldPosition;
float uDirection;
float3 uLightSource; // 用于反射染料
float2 uImageSize0;
float2 uImageSize1;
float2 uTargetPosition;
float4 uLegacyArmorSourceRect;
float2 uLegacyArmorSheetSize;
```

## 屏幕着色器

[[https://i.imgur.com/pjpwfxO.png|inline]]

泰拉瑞亚的屏幕着色器修改整个屏幕的内容，而不是特定纹理。屏幕着色器最常见的应用是为屏幕赋予特定色调，但它们也可用于其他效果，如扭曲效果、叠加，或者如果您出于某种原因想要颠倒屏幕。屏幕着色器不应与自定义天空混淆，例如在太阳柱背景中掉落的流星：这些与着色器无关，本指南不涉及。

屏幕着色器比像素着色器复杂得多，因此除非您尝试做像屏幕色调这样非常简单的事情，否则我建议您从装甲着色器开始。本指南不包含屏幕着色器的示例，因为大部分关于装甲着色器的信息是相通的。

屏幕着色器的参数如下：

```hlsl
sampler uImage0 : register(s0); // 屏幕内容
sampler uImage1 : register(s1); // 最多三个可用于各种目的的额外纹理（例如作为叠加）
sampler uImage2 : register(s2);
sampler uImage3 : register(s3);
float3 uColor;
float3 uSecondaryColor;
float2 uScreenResolution;
float2 uScreenPosition; // 相机位置
float2 uTargetPosition; // 着色器的"目标"，这实际上意味着什么往往因着色器而异
float2 uDirection;
float uOpacity;
float uTime;
float uIntensity;
float uProgress;
float2 uImageSize1;
float2 uImageSize2;
float2 uImageSize3;
float2 uImageOffset;
float uSaturation;
float4 uSourceRect; // 似乎没有使用，但包含以保持一致性
float2 uZoom;
```

# 如何编写着色器

现在这一切都很好，但实际上您如何编写着色器？在本章中，我将向您展示一些着色器示例及其作用。

## 基本着色器

首先，XNA中着色器文件的扩展名是`.fx`。除此之外，它就像任何其他文本文件一样工作，因此您不需要任何特殊软件来开始编写着色器。

让我们从一些装甲着色器开始。首先，让我们编写最小可行的着色器。

```hlsl
sampler uImage0 : register(s0);
sampler uImage1 : register(s1);
float3 uColor;
float3 uSecondaryColor;
float uOpacity;
float uSaturation;
float uRotation;
float uTime;
float4 uSourceRect;
float2 uWorldPosition;
float uDirection;
float3 uLightSource;
float2 uImageSize0;
float2 uImageSize1;
float2 uTargetPosition;
float4 uLegacyArmorSourceRect;
float2 uLegacyArmorSheetSize;
    
float4 ArmorBasic(float4 sampleColor : COLOR0) : COLOR0
{
    return sampleColor;
}
    
technique Technique1
{
    pass ArmorBasic
    {
        PixelShader = compile ps_2_0 ArmorBasic();
    }
}
```

这有很多内容需要解释。参数您已经知道了，让我们看看中间的部分。

```hlsl
float4 ArmorBasic(float4 sampleColor : COLOR0) : COLOR0
{
    return sampleColor;
}
```

这里我们有一个通道。现在，您可能认识到`ArmorBasic`前面的`float4`表示这是返回类型。`: COLOR0`表示这个返回值应该存储在哪里，即GPU的COLOR0寄存器。把两者放在一起，这意味着这个通道写入一个由四个浮点数（红、绿、蓝和alpha）组成的颜色值到GPU。

至于`float4 sampleColor : COLOR0`，这意味着您正在声明一个参数，等于COLOR0寄存器中的内容。所以这个通道所做的就是获取COLOR0的值并放入COLOR0。不太有用，您可能会想。

我们将继续介绍更有用的内容，但让我们先看看着色器的最后部分：

```hlsl
technique Technique1
{
    pass ArmorBasic
    {
        PixelShader = compile ps_2_0 ArmorBasic();
    }
}
```

所有这些所做的就是通过编译我们希望它包含的所有通道来编译技术。每次创建通道时，如果想在泰拉瑞亚中使用它，都必须将其添加到技术中。另外，确保使用`ps_2_0`，否则会出现兼容性问题。

为了保持代码片段简短，我将省略参数和技术，但显然如果要进行编译，您仍然需要它们。

好的，让我们做一些更有用的事情。现在，我们正在将寄存器的内容写入同一个寄存器，这完全没有用。我们首先想要做的就是绘制纹理。为此，我们必须进行一些调整。

```hlsl
float4 ArmorBasic(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    return color;
}
```

这里发生了两件事：一，我们添加了`float2 coords : TEXCOORD0`作为参数，二，我们添加了一个叫做`tex2D`的东西。要理解其中任何一个，您首先必须记住我在介绍中提到的：像素着色器对纹理覆盖的每个像素执行（不是纹理的每个像素，但如果以1的比例绘制精灵基本上是同一回事）。现在，您可能能说出`coords`像`sampleColor`一样获取TEXCOORD0寄存器的值，但实际值是什么意思？基本上，它表示当前像素相对于纹理的位置。这可能听起来很复杂，但希望这张图片能澄清：

![Coordinate illustration](https://i.imgur.com/A6Chqtu.png)

所以，想象着色器在这个纹理上运行。它将从左上角开始，在这种情况下`coords`将等于(0, 0)。在第一行的中途，它将是(0.5, 0)。在纹理的正中心，它将是(0.5, 0.5)，在右下角它将是(1, 1)。

那么`tex2D`做什么呢？简单地说，它在位置`coords`对`uImage0`的颜色数据进行采样。并且因为着色器对每个像素运行，而`coords`相应地更新，本质上它是逐像素追踪纹理。所以这个通道所做的就是简单地绘制您的纹理。

但是有一个问题，那就是您正在覆盖颜色寄存器的内容。有时候当它是故意的时候这很有用（屏幕着色器通常这样做），但也可能导致问题，特别是如果您覆盖透明度的话。所以通常，您希望在写入之前将结果乘以寄存器的内容：

```hlsl
float4 ArmorBasic(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    return color * sampleColor;
}
```
采样将成为几乎所有通道的基础，但本身没有太大作用。让我们引入一些参数。

## 参数和混合

现在我们可以绘制精灵，但这与游戏已经做的没有什么不同：我们想要一个自定义效果。比如说，我们想给精灵着色。如果我们不知道怎么做的更好，我们可能会尝试这样做：

```hlsl
float4 ArmorTint(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    color *= uColor;
    return color * sampleColor;
}
```

在这里，我们使用参数列表中的`uColor`参数，但有一个问题：`color`是`float4`，但`uColor`是`float3`，您不能将它们相互乘。我们想要做的是将纹理的红色、绿色和蓝色通道乘以参数的值，所以我们这样做：

```hlsl
float4 ArmorTint(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    color.rgb *= uColor;
    return color * sampleColor;
}
```

`color.rgb *= uColor;`这一行等于：

```hlsl
color.r *= uColor.r;
color.g *= uColor.g;
color.b *= uColor.b;
```

这种成员访问就是我们所说的混合。我们使用`rgba`来引用颜色数据，但有时您使用位置，在这种情况下`xyzw`更有意义。因此，您也可以这样做：

```hlsl
color.xyz *= uColor;
```

这与前一行完全相同，只是语法糖。您甚至可以在同一行中混合它们。但是，您不能在同一个混合中混合它们：

```hlsl
color1.rgb = color2.xyz; // 这是可以的
color1.xgb = color2.xyz; // 这是不可以的
```

无论如何，让我们回到原来的通道。让我们传入纯红色看看会发生什么：

![Red simple](https://i.imgur.com/2IhgYzX.png)

还不错。让我们看一个稍微复杂的例子：

```hlsl
float4 ArmorLuminosity(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    float luminosity = (color.r + color.g + color.b) / 3;
    color.rgb = luminosity * uColor;
    return color * sampleColor;
}
```

我们在这里做的是计算每个像素的发光度（亮度），然后用颜色乘以它。这可以防止在您的色调补充纹理颜色的地方出现大面积黑斑。

结果是：

![Red luminosity](https://i.imgur.com/Pj1ozO8.png)

## 创造性地使用坐标

虽然我们已经可以做一些相当不错的事情，但都很统一。为了增加一些变化，我们可以使用坐标来获得一些好东西。让我们做一个简单的渐变：

```hlsl
float4 ArmorGradient(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    color.rgb *= coords.x;
    return color * sampleColor;
}
```

如果您记得上面解释坐标如何工作的图片，您可能能够猜到这里发生了什么：最左边的像素将是黑色的，并且会变亮到它们的原始颜色。我不得稍微增强结果以保持清晰，但它看起来像这样：

![Gradient horizontal](https://i.imgur.com/hSNMYTl.png)

简单，对吧？您甚至可以将两个相反方向的渐变组合起来：

```hlsl
float4 ArmorColorGradient(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    float luminosity = (color.r + color.g + color.b) / 3;
    color.rgb *= ((coords.x * uColor) + ((1 - coords.x) * uSecondaryColor)) * luminosity;
    return color * sampleColor;
}
```
![Gradient horizontal multicolor](https://i.imgur.com/w3PTOr3.png)

好的，那是水平方向的。让我们尝试垂直的！

```hlsl
float4 ArmorVerticalGradient(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    color.rgb *= coords.y;
    return color * sampleColor;
}
```
![Gradient vertical bad](https://i.imgur.com/yIMcvQB.png)

嗯，看起来不对。如果您非常仔细地看，您可能能够看到长袍底部稍微变亮，但肯定应该在那个点完全亮？发生了什么？

这里的问题是坐标是相对于您正在绘制的纹理，而不是您正在绘制的帧。您可能知道，泰拉瑞亚中的每个装甲套装都是20帧长，所以在我们第一帧结束时y坐标只是0.05，而不是1。一旦您看到行走周期，这尤其明显：

![Gradient vertical bad anim](https://i.imgur.com/pZuvvF8.gif)

为了解决这个问题，我们希望y坐标在我们当前帧的开始时为0，在结束时为1。幸运的是，有一个（相对）简单的公式：

```hlsl
float frameY = (coords.y * uImageSize0.y - uSourceRect.y) / uSourceRect.w;
```

您可以自己仔细研究这到底做了什么，但它"缩小"坐标以只包含单个帧，然后将原点移动到活动帧。现在，让我们再试一次：

```hlsl
float4 ArmorVerticalGradient(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    float frameY = (coords.y * uImageSize0.y - uSourceRect.y) / uSourceRect.w;
    color.rgb *= frameY;
    return color * sampleColor;
}
```
![Gradient vertical good](https://i.imgur.com/TD8CvHI.png)

是的，好多了！

## 传递时间

（这可能是我做过的最糟糕的双关语）

现在，很有可能您来这里不是为了无聊的渐变东西，而是因为您想制作动画着色器，您也是对的！实际上，制作动画着色器非常简单：您只需使用`uTime`参数。然而，主要问题是如何正确使用它。

假设我要这样做：

```hlsl
float4 ArmorAnimated(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    color.rgb *= uTime;
    return color * sampleColor;
}
```

现在，这只会增加亮度直到无穷大（好吧，直到一个小时过去才会重置，但那仍然是很长的时间），而且真的没有帮助。相反，大多数染料使用正弦函数将原始时间转换为正弦波。这会给你一个漂亮且可预测的标量来使用：

```hlsl
float4 ArmorAnimatedSine(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    color.rgb *= sin(uTime);
    return color * sampleColor;
}
```
![Sine animation](https://i.imgur.com/xQAqvm1.gif)

您可能知道，正弦波在1和-1之间波动。在这种情况下，负范围真的没有用，因为负颜色只显示为黑色。如果我们想消除那个负范围，只在0和1之间弹跳，我们可以这样做：

```hlsl
float time = (sin(uTime) + 1) / 2;
float altTime = sin(uTime) * 0.5f + 0.5f; // 同样的事情。[/code]
```

除了正弦波，还有另一种不太有用但仍然不错的建立模式的方法，那就是使用`frac`，它获取数字的小数部分。当与`uTime`一起使用时，您会得到一个漂亮的锯齿图案，范围从0到1（不含）。

```hlsl
float4 ArmorAnimatedFrac(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    color.rgb *= frac(uTime);
    return color * sampleColor;
}
```
![Frac animation](https://i.imgur.com/djj1vSI.gif)

好的，还有一个。如果我们把坐标和时间结合起来会怎样？很酷的东西，那就是：

```hlsl
float4 ArmorRadar(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    float wave = 1 - frac(coords.x + uTime);
    color.rgb = color.rgb * wave;
    return color * sampleColor;
}
```
![Radar dye](https://i.imgur.com/XvnaDFY.gif)

因为我们将`uTime`加到`coords.x`，我们的'分割点'（`frac`的结果从~0.99跳回0的地方）不断向左移动，给我们一个漂亮的雷达般的效果。想要加快或放慢移位？只需将`uTime`乘以一个标量：

```hlsl
float wave = 1 - frac(coords.x + uTime * 0.5f); // 这会慢一半
float wave = 1 - frac(coords.x + uTime * 2); // 这会快一倍。或者慢一半。随你喜欢。
```

## 制作一些噪声

最后，我们将看看如何使用噪声来增强我们的染料。大多数染料有相当几何的感觉，有时这会非常漂亮。但是，如果您想添加一些随机性（感知或其他），您可以使用噪声贴图。几种染料已经这样做：暮光染料中的星星，沙中之沙染料和涡流染料中的...呃，涡流...都是通过使用噪声贴图创建的。

噪声的应用非常多样化，我不会涉及所有。但是，较常见的应用之一是将其用作叠加。为此，我们需要做相当多的工作。

首先，您必须解决不同像素比的问题：因为坐标从0到1范围，不管图像有多大，使用一组坐标对两个图像将不会覆盖相同数量的像素（如果图像大小不同），这会导致难看的拉伸：

```hlsl
float4 ArmorNoise(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    float4 noise = tex2D(uImage1, coords); // 相同坐标
    color.rgb = noise.rgb;
    return color * sampleColor * color.a; // 乘以color.a来遮盖不可见的像素
}
```
![Noise bad](https://i.imgur.com/Kuw015o.png)

要将纹理坐标转换为噪声坐标，您可以使用此公式：

```hlsl
float2 noiseCoords = (coords * uImageSize0 - uSourceRect.xy) / uImageSize1;
```

这会获取您想要的像素的笛卡尔坐标，然后确定将获得该像素的坐标。实际上，它看起来像这样：

```hlsl
float4 ArmorNoise(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    float2 noiseCoords = (coords * uImageSize0 - uSourceRect.xy) / uImageSize1;
    float4 noise = tex2D(uImage1, noiseCoords);
    color.rgb = noise.rgb;
    return color * sampleColor * color.a; // 乘以color.a来遮盖不可见的像素    
}
```
![Noise good](https://i.imgur.com/CjpZhBE.png)

然后您可以用它做各种事情（大部分是难看的）：

```hlsl
float4 ArmorNoise(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    float2 noiseCoords = (coords * uImageSize0 - uSourceRect.xy) / uImageSize1;
    float4 noise = tex2D(uImage1, noiseCoords);
    float luminosity = (color.r + color.g + color.b) / 3;
    color.rgb = luminosity * noise.b * 2;
    return color * sampleColor;
}
```
![Noise applied](https://i.imgur.com/WP9ld0c.png)

如果您只是想采样噪声贴图以获得伪随机标量，您可以省去这一切：只需使用正常坐标采样，它应该可以正常工作。

这些就是制作您自己的着色器所需的几乎所有构建模块。虽然您还可以使用一些更巧妙的技巧，但这些应该足以让您开始。

# 编译您的着色器

完成着色器编写后，您需要先编译才能使用。有两种方法可以做到这一点：最直接的是使用可以编译.fx文件到.xnb的应用程序，如[fxcompiler](https://github.com/tModLoader/tModLoader/raw/refs/heads/gh-pages/downloads/fxcompiler.zip)（[Reach版本](https://github.com/tModLoader/tModLoader/raw/refs/heads/gh-pages/downloads/fxcompiler_reach.exe)），或[EasyXnb](https://github.com/SuperAndyHero/EasyXnb/releases)（着色器模型3所需）。第二种方法是用XNA应用程序构建它们。

使用fxcompiler或EasyXnb非常简单：只需将您想要编译的文件放到与exe相同的文件夹中，运行它，就完成了。将.xnb文件移动到您的mod文件夹（_YourModName/Effects_是放置它们的好地方），就完成了。如果您使用XNA应用程序构建它们，它们将最终出现在您的bin文件夹中。

另一个选项是使用Modders Toolkit着色器工具。着色器工具允许在游戏中进行实时测试和开发。更多信息可以在[Modders Toolkit Wiki](https://github.com/JavidPack/ModdersToolkit/wiki/Shader-Tool)上找到。

# 使用您的着色器

如何使用您的着色器主要取决于它是什么类型的着色器，但首先您必须先加载它们。您可以在`Mod`的`Load`函数中执行此操作：

```cs
using Microsoft.Xna.Framework.Graphics;
using Terraria.Graphics.Effects;
using Terraria.Graphics.Shaders;
using Terraria.ID;
using Terraria.ModLoader;
using ReLogic.Content;
using Terraria;

public class MyMod : Mod
{
	// stuff...

	public override void Load() {

		// 所有这些加载都需要在客户端进行

		if (Main.netMode != NetmodeID.Server) {
			// 首先，加载您的着色器文件
			// 无论它是什么类型的着色器，您都必须这样做
			// 并且每个着色器文件都必须这样做
			// 此示例假设您同时有装甲和屏幕着色器

			Asset<Effect> dyeShader = this.Assets.Request<Effect>("Effects/MyDyes");
			Asset<Effect> specialShader = this.Assets.Request<Effect>("Effects/MySpecials");
			Asset<Effect> filterShader = this.Assets.Request<Effect>("Effects/MyFilters");

			// 要添加染料，只需为您想要添加的每个染料添加此内容
			// "PassName"应该对应于您技术内的通道名称
			// 如果您在此出错，请确保您在效果文件中正确拼写

			GameShaders.Armor.BindShader(ModContent.ItemType<MyDyeItem>(), new ArmorShaderData(dyeShader, "PassName"));

			// 如果您的染料需要特定参数（如颜色），您可以在绑定着色器后附加它们
			// IntelliSense应该能够在这里帮助您

			GameShaders.Armor.BindShader(ModContent.ItemType<MyColorDyeItem>(), new ArmorShaderData(dyeShader, "ColorPass")).UseColor(1.5f, 0.15f, 0f);
			GameShaders.Armor.BindShader(ModContent.ItemType<MyNoiseDyeItem>(), new ArmorShaderData(dyeShader, "NoisePass")).UseImage("Images/Misc/noise"); // 使用默认的泰拉瑞亚噪声贴图

			// 要绑定一个杂项的非滤镜效果，请使用此内容
			// 如果您实际使用这个，您可能已经知道自己在做什么
			// 这种类型的着色器需要一个额外参数：float4 uShaderSpecificData;
			GameShaders.Misc["MyMod:EffectName"] = new MiscShaderData(specialShader, "PassName");

			// 要绑定屏幕着色器，请使用此内容
			// EffectPriority应该设置为您认为合理的任何值

			Filters.Scene["MyMod:FilterName"] = new Filter(new ScreenShaderData(filterShader, "PassName"), EffectPriority.Medium);
		}
	}
}
```

您也可以在内容的加载方法或`ModSystem`类中加载着色器。例如，[ExampleDye.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/ExampleDye.cs)显示在染料的`ModItem`类中加载染料着色器并将其绑定到染料项目。它更倾向于将着色器加载代码保留在其将使用的类中。还要注意语法的细微差别，使用`Mod.Assets.Request`而不是`this.Assets.Request`。

对于染料，这就足够了：装备您的染料项目将显示染料。简单，对吧？对于滤镜，您需要在适当的时候激活它们。激活（和停用）滤镜非常简单：

```cs
if (Main.netMode != NetmodeID.Server) // 这一切都需要在客户端进行！
{
    Filters.Scene.Activate("FilterName");

    // 更新滤镜
    Filters.Scene["MyMod:FilterName"].GetShader().UseProgress(progress);

    Filters.Scene["MyMod:FilterName"].Deactivate();
}
```

有关如何使用滤镜的示例，请查看我的另一个冲击波滤镜指南：

https://forums.terraria.org/index.php?threads/tutorial-shockwave-effect-for-tmodloader.81685/

# 提示和技巧

我已经在这里分享了我大部分的秘诀，但还有一些可能能够帮助您的小技巧。

1. **必要时遮罩您的精灵** -
有时候您的着色器也会填充精灵透明部分的颜色。您通常可以通过将颜色乘以样本的alpha来补救（除非您已经覆盖了它，您真的不应该这样做）。参见上面噪声贴图的示例，了解...呃，示例。
2. **保持高效** -
着色器每帧执行多次。确保它们尽可能快，或者至少不要不必要的慢。就个人而言，我认为这适用于所有代码，但在这方面我似乎是少数。
3. **闪烁效果要小心** -
某些患有癫痫的人可能会因闪烁效果而受到癫痫发作。小心这一点，如果您的mod最终包含可能引发此类发作的内容，请给予适当的警告。
4. **实验！** -
我的许多染料都是胡乱折腾的结果。不要害怕突破界限（不要炸掉您的GPU），您可能会惊喜地惊讶。

这就是我要说的。如果您对本帖中的任何内容有任何问题，请随时在这里提问！

***

_Armor着色器模板_
```cs
sampler uImage0 : register(s0);
sampler uImage1 : register(s1);
float3 uColor;
float3 uSecondaryColor;
float uOpacity;
float uSaturation;
float uRotation;
float uTime;
float4 uSourceRect;
float2 uWorldPosition;
float uDirection;
float3 uLightSource;
float2 uImageSize0;
float2 uImageSize1;
float2 uTargetPosition;
float4 uLegacyArmorSourceRect;
float2 uLegacyArmorSheetSize;

float4 ArmorBasic(float4 sampleColor : COLOR0, float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    return color * sampleColor;
}

technique Technique1
{
    pass ArmorBasic
    {
        PixelShader = compile ps_2_0 ArmorBasic();
    }
}
```

_屏幕着色器模板_
```cs
sampler uImage0 : register(s0);
sampler uImage1 : register(s1);
sampler uImage2 : register(s2);
sampler uImage3 : register(s3);
float3 uColor;
float3 uSecondaryColor;
float2 uScreenResolution;
float2 uScreenPosition;
float2 uTargetPosition;
float2 uDirection;
float uOpacity;
float uTime;
float uIntensity;
float uProgress;
float2 uImageSize1;
float2 uImageSize2;
float2 uImageSize3;
float2 uImageOffset;
float uSaturation;
float4 uSourceRect;
float2 uZoom;

float4 FilterMyShader(float2 coords : TEXCOORD0) : COLOR0
{
    float4 color = tex2D(uImage0, coords);
    return color;
}

technique Technique1
{
    pass FilterMyShader
    {
        PixelShader = compile ps_2_0 FilterMyShader();
    }
}
```
