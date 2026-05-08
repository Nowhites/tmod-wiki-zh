**当您掌握了模组制作的基础知识后，不断前往 ExampleMod 复制您懒得手动编写的代码可能会很烦人（相信我，我一直这样做）。幸运的是，Visual Studio 支持自定义代码片段的使用，允许您通过几次按键粘贴预制代码。**

***

# 第一个 VS 代码片段

要创建您的第一个代码片段，请创建一个带有 `snippet` 扩展名的文件。

![image](https://user-images.githubusercontent.com/68346263/156292238-8e6b0c53-d83f-4128-813c-adf346191dc7.png)

使用任何文本编辑器打开该文件并粘贴以下内容：
```xml
<?xml version="1.0" encoding="utf-8"?>
<CodeSnippets xmlns="http://schemas.microsoft.com/VisualStudio/2005/CodeSnippet">
    <CodeSnippet Format="1.0.0">
        <Header>
            <Title>Snippet Title</Title>
        </Header>
        <Snippet>
            <Code Language="CSharp">
                <![CDATA[]]>
            </Code>
            <Declarations></Declarations>
        </Snippet>
    </CodeSnippet>
</CodeSnippets>
```

如果您从未使用过 HTML 或 XML，这可能看起来令人生畏，因此以下是所有重要部分的简化分解：
- `Header` 标签中的所有内容都是代码片段的信息，显示在代码片段管理器中。现在，标题可以是您想要的任何内容。
- 此代码片段将提供的实际代码应放在 `Code` 标签内，位于 `CDATA` 括号对中。简而言之：`<![CDATA[您的代码放在这里]]>`。您可以按任何方式格式化代码，但当使用代码片段时，Visual Studio 的 C# 语言功能会自动格式化它。
- `Declarations` 标签是您放置参数的位置。这些是您在使用的代码片段中能够自定义的部分（稍后会详细介绍）。

***

# 代码片段参数

参数是为代码片段添加灵活性的好方法，允许您自定义代码片段提供的样板代码。参数可用于替换变量名、函数参数以及您想要包含自定义性的代码其他区域。例如，您可以在类名位置添加参数，以允许用户使用他/她自己的。

要创建参数，请在您选择的位置替换任何名称，并用 `$` 字符包围该名称（示例：`private void FindSqrt(16)` → `private void FindSqrt($Value$)`）。

这是我在 Mod Item 代码片段中使用的参数之一。请注意字符串的内容如何被移除并替换为 `Literal`：

![image](https://user-images.githubusercontent.com/68346263/156295293-0e317d1c-aecd-4924-a848-0fe9183c6874.png)

现在您设置了 `Literal`，您需要为其提供一些属性。还记得代码片段代码中的 `Declarations` 标签吗？在其中编写如下代码
```xml
<Declarations>
    <Literal>
        <ID>Value</ID>
        <ToolTip>Value of parameter</ToolTip>
        <Default>16</Default>
    </Literal>
</Declarations>
```

- `Literal` 标签定义一个新的可替换值
- `ID` 是代码中 `$` 内的内容
- `Tooltip` 是关于该值的有用信息。虽然不是必需的，但建议添加
- `Default` 标签定义此参数的默认值。这只是首次使用代码片段时显示的内容

以下是我的代码片段的工具提示参数的外观声明：

![image](https://user-images.githubusercontent.com/68346263/156295774-0ba8ce9f-9fca-469c-9de3-99ee9f327e46.png)

以下是该参数在使用代码片段时的外观。

![image](https://user-images.githubusercontent.com/68346263/156295840-03f66ffb-d586-41a6-b19e-25e7bef343d.png)

***

# 导入您的代码片段

完成编写代码片段的代码后，保存文件并打开 Visual Studio（如果尚未打开）。按 `Control K` 然后是 `Control B` 打开代码片段管理器；这是您可以管理由 Visual Studio 或您自己添加的代码片段的地方。在语言下拉列表中选择 `CSharp`，单击导入按钮，然后选择您的文件。您将看到一个新窗口，其中询问您要将代码片段文件导入到哪里。您应该在右侧看到一个名为 `My Code Snippets` 的文件夹；选择它。

现在您的代码片段已在 Visual Studio 中，您已准备好使用它。按 `Control K` 然后是 `Control X`，单击 `My Code Snippets` 并选择您的代码片段。您在代码片段文件的 `<![CDATA[]]>` 部分放入的代码应该会出现，如果出现了，恭喜！您已完成第一个 Visual Studio 代码片段。您可以重复此过程以满足您的所有样板代码需求。

以下是使用我的示例的代码片段演示 | [代码片段代码](https://gist.github.com/Arnimox/8c09aa22768f958333d15a089529d465)

![](https://cdn.discordapp.com/attachments/886315368027676702/948421370331689030/SnippetsShowcase.gif?size=4096)
