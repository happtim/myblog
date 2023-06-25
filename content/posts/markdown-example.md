+++
date = "2023-06-21"
title = "markdown 语法"

+++

# 标题

要创建标题，请在单词或短语前面添加井号 (`#`) 。`#` 的数量代表了标题的级别。例如，添加三个 `#` 表示创建一个三级标题 (`<h3>`)

# Heading level 1
## Heading level 2
### Heading level 3

# 段落

要创建段落，请使用空白行将一行或多行文本进行分隔。

I really like using Markdown.  

I think I'll use it to format all of my documents from now on.

## 换行语法

在一行的末尾添加两个或多个空格，然后按回车键,即可创建一个换行(`<br>`)。

This is the first line.  
And this is the second line.


# 强调

通过将文本设置为粗体或斜体来强调其重要性。

## 粗体（Bold）

要加粗文本，请在单词或短语的前后各添加两个星号（asterisks）或下划线（underscores）。如需加粗一个单词或短语的中间部分用以表示强调的话，请在要加粗部分的两侧各添加两个星号（asterisks）。

I just love **bold text**.

Love**is**bold

## 斜体（Italic）

要用斜体显示文本，请在单词或短语前后添加一个星号（asterisk）或下划线（underscore）。要斜体突出单词的中间部分，请在字母前后各添加一个星号，中间不要带空格。

Italicized text is the *cat's meow*.

A*cat*meow

## 粗体（Bold）和斜体（Italic）

要同时用粗体和斜体突出显示文本，请在单词或短语的前后各添加三个星号或下划线。要加粗并用斜体显示单词或短语的中间部分，请在要突出显示的部分前后各添加三个星号，中间不要带空格。

This text is ***really important***.

This is really***very***important text.

## 删除线

您可以通过在单词中心放置一条水平线来删除单词。结果看起来~~像这样~~。此功能使您可以指示某些单词是一个错误，要从文档中删除。若要删除单词，请在单词前后使用两个波浪号`~~`。

~~世界是平坦的。~~ 我们现在知道世界是圆的。


# 引用

要创建块引用，请在段落前添加一个 `>` 符号。

> Dorothy followed her through many of the beautiful rooms in her castle.

## 多个段落的块引用

块引用可以包含多个段落。为段落之间的空白行添加一个 `>` 符号。

>Dorothy followed her through many of the beautiful rooms in her castle.
>
>The Witch bade her clean the pots and kettles and sweep the floor and keep the fire fed with wood.

## 嵌套块引用

块引用可以嵌套。在要嵌套的段落前添加一个 `>>` 符号。

>Dorothy followed her through many of the beautiful rooms in her castle.
>
>>The Witch bade her clean the pots and kettles and sweep the floor and keep the fire fed with wood.

## 带有其它元素的块引用

块引用可以包含其他 Markdown 格式的元素。并非所有元素都可以使用，你需要进行实验以查看哪些元素有效。

> - Revenue was off the chart.
> - Profits were higher than ever.
>
>  *Everything* is going according to **plan**.


# 列表

可以将多个条目组织成有序或无序列表。

## 有序列表

要创建有序列表，请在每个列表项前添加数字并紧跟一个英文句点。数字不必按数学顺序排列，但是列表应当以数字 1 起始。

1. First item  
2. Second item  
3. Third item  
4. Fourth item

## 无序列表

要创建无序列表，请在每个列表项前面添加破折号 (-)、星号 (\*) 或加号 (+) 。缩进一个或多个列表项可创建嵌套列表。

- First item  
- Second item  
- Third item  
- Fourth item

# 代码

要将单词或短语表示为代码，请将其包裹在反引号 (`` ` ``) 中。

At the command prompt, type `nano`.

## 转义反引号

如果你要表示为代码的单词或短语中包含一个或多个反引号，则可以通过将单词或短语包裹在双反引号(` `` `)中。

``Use `code` in your Markdown file.``

## 代码块

要创建代码块，请将代码块的每一行缩进至少四个空格或一个制表符。

```
<html>
  <head>
  </head>
</html>
```

## 语法高亮

许多Markdown处理器都支持受围栏代码块的语法突出显示。使用此功能，您可以为编写代码的任何语言添加颜色突出显示。要添加语法突出显示，请在受防护的代码块之前的反引号旁边指定一种语言。

```html
<html>
  <head>
  </head>
</html>
```


# 分隔线

要创建分隔线，请在单独一行上使用三个或多个星号 (`***`)、破折号 (`---`) 或下划线 (`___`) ，并且不能包含其他内容。

try to put a blank line before...  
  
---  
  
...and after a horizontal rule.


# 链接

链接文本放在中括号内，链接地址放在后面的括号中，链接title可选。

[我的博客地址](https://blog.happtim.com)

## 给链接增加 Title

链接title是当鼠标悬停在链接上时会出现的文字，这个title是可选的，它放在圆括号中链接地址后面，跟链接地址之间以空格分隔。

[我的博客地址](https://blog.happtim.com "my blog")

## 网址和Email地址

使用尖括号可以很方便地把URL或者email地址变成可点击的链接。

<https://blog.happtim.com>

## 带格式化的链接

强调 链接, 在链接语法前后增加星号。 要将链接表示为代码，请在方括号中添加反引号。

I love supporting the **[EFF](https://eff.org/)**.  
This is the _[Markdown Guide](https://www.markdownguide.org/)_.


# 图片

要添加图像，请使用感叹号 (`!`), 然后在方括号增加替代文本，图片链接放在圆括号里，括号里的链接后可以增加一个可选的图片标题文本。

![image.png](http://assets.happtim.com/image/n3dc/202306211443859.png)
## 链接图片

给图片增加链接，请将图像的Markdown 括在方括号中，然后将链接添加在圆括号中。

[![image.png](http://assets.happtim.com/image/n3dc/202306211443859.png)](https://blog.happtim.com/post/markdonw-example)


# 转义字符

要显示原本用于格式化 Markdown 文档的字符，请在字符前面添加反斜杠字符 \ 。

\* Without the backslash, this would be a bullet in an unordered list.


# 表格

要添加表，请使用三个或多个连字符（`---`）创建每列的标题，并使用管道（`|`）分隔每列。您可以选择在表的任一端添加管道。

| Syntax    | Description |
| --------- | ----------- |
| Header    | Title       |
| Paragraph | Text        |


## 对齐

您可以通过在标题行中的连字符的左侧，右侧或两侧添加冒号（`:`），将列中的文本对齐到左侧，右侧或中心。

| Syntax    | Description |   Test Text |
|:--------- |:-----------:| -----------:|
| Header    |    Title    | Here’s this |
| Paragraph |    Text     |    And more |

## 格式化表格中的文字

您可以在表格中设置文本格式。例如，您可以添加链接，代码（仅反引号（`` ` ``）中的单词或短语，而不是代码块）和强调。

您不能添加标题，块引用，列表，水平规则，图像或HTML标签。

# 任务列表

任务列表使您可以创建带有复选框的项目列表。在支持任务列表的Markdown应用程序中，复选框将显示在内容旁边。要创建任务列表，请在任务列表项之前添加破折号`-`和方括号`[ ]`，并在`[ ]`前面加上空格。要选择一个复选框，请在方括号`[x]`之间添加 x 。

- [x] Write the press release
- [ ] Update the website
- [ ] Contact the media

# 使用 Emoji 表情

有两种方法可以将表情符号添加到Markdown文件中：将表情符号复制并粘贴到Markdown格式的文本中，或者键入_emoji shortcodes_。

## 复制和粘贴表情符号

在大多数情况下，您可以简单地从[Emojipedia](https://emojipedia.org/) 等来源复制表情符号并将其粘贴到文档中。许多Markdown应用程序会自动以Markdown格式的文本显示表情符号。从Markdown应用程序导出的HTML和PDF文件应显示表情符号。

## 使用表情符号简码

一些Markdown应用程序允许您通过键入表情符号短代码来插入表情符号。这些以冒号开头和结尾，并包含表情符号的名称。

去露营了！ :tent: 很快回来。

真好笑！ :joy:

去露营了！⛺很快回来。

真好笑！😂