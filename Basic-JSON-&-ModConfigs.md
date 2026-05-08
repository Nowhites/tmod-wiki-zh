## 简介
在我们开始之前，了解什么是config以及'ModConfig'如何处理很重要。首先，config代表'configuration'，我们通常用它来表示'配置文件'。配置文件有许多格式，在tModLoader中，我们使用一种名为JSON的语言来处理ModConfigs。

## 什么是JSON？
JSON代表JavaScript-Object-Notation，我们将深入探讨这个名称的含义和继承。JSON是一种用于以特定格式存储数据的语言，用于数据交换。JSON通常优于其他语言，因为它轻量、灵活且无结构（意味着它支持_任何_结构！）尽管某些其他语言如XML（.xml）或YAML（.yml）提供更多功能，但它们不如JSON轻量。JSON源自JavaScript，尽管它现在已经适应到许多语言。事实上，JSON真正的就是JavaScript对象。JSON是官方互联网媒体类型（`application/json`），文件使用`.json`文件扩展名。

## JSON如何工作？
JSON由由属性-值对和数组数据类型组成。如果您知道[Javascript对象](https://www.w3schools.com/js/js_objects.asp)是如何构建的，JSON文件对您来说非常简单。让我们直接开始，这是一个展示其基本数据类型的JSON示例：
```json
{
	"key": "value",
	"number": 420.7335,
	"string": "this is a string!",
	"boolean": true,
	
	"array": [
		10.20,
		"an array can hold many data types",
		false, 
		[
			"even arrays inside arrays!",
			42
		]
	],
	
	"object": {
		"name": "another object",
		"description": "we can also make objects within objects, the sky is the limit",
		"remarks": "note the JSON notation: key: value",
		"anotherObject": {
			"array": [
				"arrays inside an object inside an object? this is madness!",
				null
			]
		}
	},
	
	"arrayWithObjects": [
		{
			"name": "01001000 01100101 01101100",
			"description": "01101100 01101111" 
		},
		{
			"name": "01110111 01101111 01110010",
			"description": "01101100 01100100 00100001"
		}
	]
}
```
如您所见，天空才是极限！
在JSON中您将存储基本数据类型：字符串、数字、布尔值、数组、布尔值以及空值。以上示例展示了所有基本数据类型以及它们可以组合的方式。接下来我们将讨论重要备注。

### 重要备注
仔细查看上面的示例。JSON最重要的方面是它的键-值（或：名称-值）对。它可以归结为：特定键匹配某个值。（符号：`"key": value`）与C#等使用编译器的语言不同，您不需要指定数据类型，它会自动解析。同样重要的是每个元素应用逗号分隔，除了最后一个元素。您可以为最后一个元素包含逗号，但不是必需的。为了熟悉JSON，建议使用[JSONLint](https://jsonlint.com/)对其进行一些调整，这是一个JSON验证器。JSONLint会告诉您您的JSON是否有效，如果不有效，它会告诉您哪里出错了以及为什么出错。

## 模组制作者如何在Terraria模组制作中使用JSON？
对于tModLoader模组，模组制作者不直接使用JSON。模组制作者可以制作使用JSON存储配置信息的ModConfig类。请参阅ExampleConfig.cs获取有关ModConfig的更多信息。如果您想检查模组添加的ModConfig的JSON文件，可以在`\Terraria\ModLoader\Mod Configs`（Windows：`%userprofile%\Documents\My Games\Terraria\ModLoader\Mod Configs`）文件夹中找到它们作为`<ModName>_<ConfigName>.json`文件。此文件将仅显示与默认值的偏差，因此它可能只是一个包含`{}`作为其内容的空文件。通常直接查看文件没有用，但您可以从中获取一些见解。
