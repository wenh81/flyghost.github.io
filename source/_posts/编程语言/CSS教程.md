# CSS 教程

**CSS 是一种描述 HTML 文档样式的语言。**

CSS 描述应该如何显示 HTML 元素。

本教程将从零起点的基础教程开始，一直到 CSS3 高级教程，为您提供全面系统地讲解。

## 每一章中的实例

本 CSS 教程包含数百个 CSS 实例。

通过使用我们的在线编辑器（W3Schoo TIY），您可以编辑 CSS，然后单击运行按钮来查看结果。

### CSS 实例

```
body {
  background-color: lightblue;
}

h1 {
  color: white;
  text-align: center;
}

p {
  font-family: verdana;
  font-size: 20px;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_index)

请点击“亲自试一试”按钮来查看它如何运行。

## CSS 实例

从 300 多个实例中学习！使用我们的在线编辑器，您可以编辑 CSS，然后单击运行按钮来查看结果。

[访问 CSS 实例！](https://www.w3school.com.cn/css/css_examples.asp)

## CSS 习题和测验

在 W3School 测试您的 CSS 技能！

[开始 CSS 测验！](https://www.w3school.com.cn/quiz/quiz.asp?f=css)

## CSS 参考手册

在 W3School，您将找到所有属性和选择器的完整 CSS 参考手册，包括语法、示例、浏览器支持等。

- [CSS 参考手册](https://www.w3school.com.cn/cssref/index.asp)
- [CSS 浏览器支持参考手册](https://www.w3school.com.cn/cssref/css_browsersupport.asp)
- [CSS 选择器参考手册](https://www.w3school.com.cn/cssref/css_selectors.asp)
- [CSS 函数参考手册](https://www.w3school.com.cn/cssref/css_functions.asp)
- [CSS 动画相关属性](https://www.w3school.com.cn/cssref/css_animatable.asp)
- [CSS 网络安全字体](https://www.w3school.com.cn/cssref/css_websafe_fonts.asp)
- [CSS 字体回退](https://www.w3school.com.cn/cssref/css_fonts_fallbacks.asp)
- [CSS 单位](https://www.w3school.com.cn/cssref/css_units.asp)
- [CSS 颜色](https://www.w3school.com.cn/cssref/css_colors.asp)
- [CSS 颜色值](https://www.w3school.com.cn/cssref/css_colors_legal.asp)
- [CSS 默认值](https://www.w3school.com.cn/cssref/css_default_values.asp)
- [CSS 实体](https://www.w3school.com.cn/cssref/css_entities.asp)
- [CSS 听觉参考手册](https://www.w3school.com.cn/cssref/css_ref_aural.asp)

# CSS 简介

## 什么是 CSS？

- *CSS* 指的是层叠样式表* (*C*ascading *S*tyle *S*heets)
- CSS 描述了*如何在屏幕、纸张或其他媒体上显示 HTML 元素*
- CSS *节省了大量工作*。它可以同时控制多张网页的布局
- 外部样式表存储在 *CSS 文件*中

***：**也称级联样式表。

## CSS 演示 - 一张 HTML 页面 - 多个样式！

下面是一张提供了四个不同样式表的 HTML 页面。请单击下面的样式表链接，来查看不同的样式：

<iframe src="https://www.w3school.com.cn/demo/css/intro.html" style="margin: 0px; padding: 0px; border: none; width: 810px; height: 700px; background: rgb(255, 255, 255);"></iframe>

## 为何使用 CSS？

CSS 用于定义网页的样式，包括针对不同设备和屏幕尺寸的设计和布局。

### CSS 实例

```
body {
  background-color: lightblue;
}

h1 {
  color: white;
  text-align: center;
}

p {
  font-family: verdana;
  font-size: 20px;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_index)

## CSS 解决了一个大问题

HTML 从未打算包含用于格式化网页的标签！

创建 HTML 的目的是*描述网页*的内容，例如：

```
<h1>这是一个标题。</h1>

<p>这是一个段落。</p>
```

将 <font> 之类的标签和 color 属性添加到 HTML 3.2 规范后，Web 开发人员的噩梦开始了。大型网站的开发将字体和颜色信息添加到每个页面中，这演变为一个漫长而昂贵的过程。

为了解决这个问题，万维网联盟（W3C）创建了 CSS。

CSS 从 HTML 页面中删除了样式格式！

如果您不知道 HTML 是什么，建议您阅读 [HTML 教程](https://www.w3school.com.cn/html/index.asp)。

## CSS 节省了大量工作！

样式定义通常保存在外部 .css 文件中。

通过使用外部样式表文件，您只需更改一个文件即可更改整个网站的外观！

# CSS 语法

## CSS 语法

CSS 规则集（rule-set）由选择器和声明块组成：

![CSS 选择器](https://www.w3school.com.cn/i/css/selector.gif)

选择器指向您需要设置样式的 HTML 元素。

声明块包含一条或多条用分号分隔的声明。

每条声明都包含一个 CSS 属性名称和一个值，以冒号分隔。

多条 CSS 声明用分号分隔，声明块用花括号括起来。

### 实例

在此例中，所有 <p> 元素都将居中对齐，并带有红色文本颜色：

```
p {
  color: red;
  text-align: center;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_syntax_1)



### 例子解释

- p 是 CSS 中的*选择器*（它指向要设置样式的 HTML 元素：<p>）。
- color 是属性，red 是属性值
- text-align 是属性，center 是属性值

在下一章中，您将学到更多关于 CSS 选择器和 CSS 属性的知识。

# CSS 选择器

## CSS 选择器

CSS 选择器用于“查找”（或选取）要设置样式的 HTML 元素。

我们可以将 CSS 选择器分为五类：

- 简单选择器（根据名称、id、类来选取元素）
- [组合器选择器](https://www.w3school.com.cn/css/css_combinators.asp)（根据它们之间的特定关系来选取元素）
- [伪类选择器](https://www.w3school.com.cn/css/css_pseudo_classes.asp)（根据特定状态选取元素）
- [伪元素选择器](https://www.w3school.com.cn/css/css_pseudo_elements.asp)（选取元素的一部分并设置其样式）
- [属性选择器](https://www.w3school.com.cn/css/css_attribute_selectors.asp)（根据属性或属性值来选取元素）

此页会讲解最基本的 CSS 选择器。

## CSS 元素选择器

元素选择器根据元素名称来选择 HTML 元素。

### 实例

在这里，页面上的所有 <p> 元素都将居中对齐，并带有红色文本颜色：

```
p {
  text-align: center;
  color: red;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_syntax_element)

## CSS id 选择器

id 选择器使用 HTML 元素的 id 属性来选择特定元素。

元素的 id 在页面中是唯一的，因此 id 选择器用于选择一个唯一的元素！

要选择具有特定 id 的元素，请写一个井号（＃），后跟该元素的 id。

### 实例

这条 CSS 规则将应用于 id="para1" 的 HTML 元素：

```
#para1 {
  text-align: center;
  color: red;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_syntax_id)

**注意：**id 名称不能以数字开头。

## CSS 类选择器

类选择器选择有特定 class 属性的 HTML 元素。

如需选择拥有特定 class 的元素，请写一个句点（.）字符，后面跟类名。

### 实例

在此例中，所有带有 class="center" 的 HTML 元素将为红色且居中对齐：

```
.center {
  text-align: center;
  color: red;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_syntax_class)

您还可以指定只有特定的 HTML 元素会受类的影响。

### 实例

在这个例子中，只有具有 class="center" 的 <p> 元素会居中对齐：

```
p.center {
  text-align: center;
  color: red;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_syntax_element_class_1)

HTML 元素也可以引用多个类。

### 实例

在这个例子中，<p> 元素将根据 class="center" 和 class="large" 进行样式设置：

```
<p class="center large">这个段落引用两个类。</p>
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_syntax_element_class_2)

**注意：**类名不能以数字开头！

## CSS 通用选择器

通用选择器（*）选择页面上的所有的 HTML 元素。

### 实例

下面的 CSS 规则会影响页面上的每个 HTML 元素：

```
* {
  text-align: center;
  color: blue;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_syntax_universal)

## CSS 分组选择器

分组选择器选取所有具有相同样式定义的 HTML 元素。

请看下面的 CSS 代码（h1、h2 和 p 元素具有相同的样式定义）：

```
h1 {
  text-align: center;
  color: red;
}

h2 {
  text-align: center;
  color: red;
}

p {
  text-align: center;
  color: red;
}
```

最好对选择器进行分组，以最大程度地缩减代码。

如需对选择器进行分组，请用逗号来分隔每个选择器。

### 实例

在这个例子中，我们对上述代码中的选择器进行分组：

```
h1, h2, p {
  text-align: center;
  color: red;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_grouping)

## 所有简单的 CSS 选择器

| 选择器                                                       | 例子       | 例子描述                             |
| :----------------------------------------------------------- | :--------- | :----------------------------------- |
| [.*class*](https://www.w3school.com.cn/css/css_selectors.asp) | .intro     | 选取所有 class="intro" 的元素。      |
| [#*id*](https://www.w3school.com.cn/css/css_selectors.asp)   | #firstname | 选取 id="firstname" 的那个元素。     |
| [*](https://www.w3school.com.cn/css/css_selectors.asp)       | *          | 选取所有元素。                       |
| [*element*](https://www.w3school.com.cn/css/css_selectors.asp) | p          | 选取所有 <p> 元素。                  |
| [*element*,*element*,..](https://www.w3school.com.cn/css/css_selectors.asp) | div, p     | 选取所有 <div> 元素和所有 <p> 元素。 |

## 延伸阅读

课外书：[CSS 元素选择器](https://www.w3school.com.cn/css/css_selector_type.asp)

课外书：[CSS 选择器分组](https://www.w3school.com.cn/css/css_selector_grouping.asp)

课外书：[CSS 类选择器详解](https://www.w3school.com.cn/css/css_selector_class.asp)

课外书：[CSS ID 选择器详解](https://www.w3school.com.cn/css/css_selector_id.asp)

课外书：[CSS 属性选择器详解](https://www.w3school.com.cn/css/css_selector_attribute.asp)

课外书：[CSS 后代选择器](https://www.w3school.com.cn/css/css_selector_descendant.asp)

课外书：[CSS 子元素选择器](https://www.w3school.com.cn/css/css_selector_child.asp)

课外书：[CSS 相邻兄弟选择器](https://www.w3school.com.cn/css/css_selector_adjacent_sibling.asp)

# 如何添加 CSS

**当浏览器读到样式表时，它将根据样式表中的信息来格式化 HTML 文档。**

## 三种使用 CSS 的方法

有三种插入样式表的方法：

- 外部 CSS
- 内部 CSS
- 行内 CSS

## 外部 CSS

通过使用外部样式表，您只需修改一个文件即可改变整个网站的外观！

每张 HTML 页面必须在 head 部分的 <link> 元素内包含对外部样式表文件的引用。

### 实例

外部样式在 HTML 页面 <head> 部分内的 <link> 元素中进行定义：

```
<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
<body>

<h1>This is a heading</h1>
<p>This is a paragraph.</p>

</body>
</html>
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_howto_external)

外部样式表可以在任何文本编辑器中编写，并且必须以 .css 扩展名保存。

外部 .css 文件不应包含任何 HTML 标签。

"mystyle.css" 是这样的：

### "mystyle.css"

```
body {
  background-color: lightblue;
}

h1 {
  color: navy;
  margin-left: 20px;
}
```

**注意：**请勿在属性值和单位之间添加空格（例如 **margin-left: 20 px;**）。正确的写法是：**margin-left: 20px;**

## 内部 CSS

如果一张 HTML 页面拥有唯一的样式，那么可以使用内部样式表。

内部样式是在 head 部分的 <style> 元素中进行定义。

### 实例

内部样式在 HTML 页面的 <head> 部分内的 <style> 元素中进行定义：

```
<!DOCTYPE html>
<html>
<head>
<style>
body {
  background-color: linen;
}

h1 {
  color: maroon;
  margin-left: 40px;
} 
</style>
</head>
<body>

<h1>This is a heading</h1>
<p>This is a paragraph.</p>

</body>
</html>
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_howto_internal)

## 行内 CSS

行内样式（也称内联样式）可用于为单个元素应用唯一的样式。

如需使用行内样式，请将 style 属性添加到相关元素。style 属性可包含任何 CSS 属性。

### 实例

行内样式在相关元素的 "style" 属性中定义：

```
<!DOCTYPE html>
<html>
<body>

<h1 style="color:blue;text-align:center;">This is a heading</h1>
<p style="color:red;">This is a paragraph.</p>

</body>
</html>
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_howto_inline)

**提示：**行内样式失去了样式表的许多优点（通过将内容与呈现混合在一起）。请谨慎使用此方法。

## 多个样式表

如果在不同样式表中为同一选择器（元素）定义了一些属性，则将使用最后读取的样式表中的值。

假设某个*外部样式表*为 <h1> 元素设定的如下样式：

```
h1 {
  color: navy;
}
```

然后，假设某个*内部样式表*也为 <h1> 元素设置了如下样式：

```
h1 {
  color: orange;    
}
```

### 实例

如果内部样式是在链接到外部样式表*之后*定义的，则 <h1> 元素将是橙色：

```
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css">
<style>
h1 {
  color: orange;
}
</style>
</head>
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_howto_multiple_1)

### 实例

不过，如果在链接到外部样式表*之前*定义了内部样式，则 <h1> 元素将是深蓝色：

```
<head>
<style>
h1 {
  color: orange;
}
</style>
<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_howto_multiple_2)

## 层叠顺序

当为某个 HTML 元素指定了多个样式时，会使用哪种样式呢？

页面中的所有样式将按照以下规则“层叠”为新的“虚拟”样式表，其中第一优先级最高：

1. 行内样式（在 HTML 元素中）
2. 外部和内部样式表（在 head 部分）
3. 浏览器默认样式

因此，行内样式具有最高优先级，并且将覆盖外部和内部样式以及浏览器默认样式。

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_howto_cascade)

# CSS 注释

## CSS 注释

注释用于解释代码，以后在您编辑源代码时可能会有所帮助。

浏览器会忽略注释。

位于 <style> 元素内的 CSS 注释，以 /* 开始，以 */ 结束：

### 实例

```
/* 这是一条单行注释 */
p {
  color: red;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_comments_1)

您可以在代码中的任何位置添加注释：

### 实例

```
p {
  color: red;  /* 把文本设置为红色 */
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_comments_2)

注释能横跨多行：

### 实例

```
/* 这是
一条多行的
注释 */ 

p {
  color: red;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_comments_3)

## HTML 和 CSS 注释

从 HTML 教程中，您学习到可以使用 <!--...--> 语法在 HTML 源代码中添加注释。

在下面的例子中，我们结合使用了 HTML 和 CSS 注释：

### 实例

```
<!DOCTYPE html>
<html>
<head>
<style>
p {
  color: red; /* 将文字颜色设置为红色 */
} 
</style>
</head>
<body>

<h2>My Heading</h2>

<!-- 这些段落将是红色的 -->
<p>Hello World!</p>
<p>这段文本由 CSS 设置样式。</p>
<p>CSS 注释不会在输出中显示。</p>

</body>
</html>
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_comments_4)

# CSS 颜色

**指定颜色是通过使用预定义的颜色名称，或 RGB、HEX、HSL、RGBA、HSLA 值。**

## CSS 颜色名

在 CSS 中，可以使用颜色名称来指定颜色：

Tomato

Orange

DodgerBlue

MediumSeaGreen

Gray

SlateBlue

Violet

LightGray

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_names)

CSS/HTML 支持 [140 种标准颜色名](https://www.w3school.com.cn/css/css_colors.asp)。

## CSS 背景色

您可以为 HTML 元素设置背景色：

Welcome to China

China is a great country!

### 实例

```
<h1 style="background-color:DodgerBlue;">China</h1>
<p style="background-color:Tomato;">China is a great country!</p>
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_background)

## CSS 文本颜色

您可以设置文本的颜色：

### China

China is a great country!

China, officially the People's Republic of China, is a country in East Asia.

### 实例

```
<h1 style="color:Tomato;">China</h1>
<p style="color:DodgerBlue;">China is a great country!</p>
<p style="color:MediumSeaGreen;">China, officially the People's Republic of China...</p>
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_text)

## CSS 边框颜色

您可以设置边框的颜色：

Hello World

Hello World

Hello World

### 实例

```
<h1 style="border:2px solid Tomato;">Hello World</h1>
<h1 style="border:2px solid DodgerBlue;">Hello World</h1>
<h1 style="border:2px solid Violet;">Hello World</h1>
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_border)

## CSS 颜色值

在 CSS 中，还可以使用 RGB 值、HEX 值、HSL 值、RGBA 值或者 HSLA 值来指定颜色：

与颜色名 "Tomato" 等效：

rgb(255, 99, 71)

\#ff6347

hsl(9, 100%, 64%)

与颜色名 "Tomato" 等效，但是透明度为 50%：

rgba(255, 99, 71, 0.5)

hsla(9, 100%, 64%, 0.5)

<h3>实例</h3>

```
<h1 style="background-color:rgb(255, 99, 71);">...</h1>
<h1 style="background-color:#ff6347;">...</h1>
<h1 style="background-color:hsl(9, 100%, 64%);">...</h1>

<h1 style="background-color:rgba(255, 99, 71, 0.5);">...</h1>
<h1 style="background-color:hsla(9, 100%, 64%, 0.5);">...</h1>
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_values)

了解有关颜色值的更多信息

在下一章中，您将学习有关 [RGB](https://www.w3school.com.cn/css/css_colors.asp)、[HEX](https://www.w3school.com.cn/css/css_colors.asp) 和 [HSL](https://www.w3school.com.cn/css/css_colors.asp) 的更多知识。

# CSS RGB 颜色

## RGB 值

在 CSS 中，可以使用下面的公式将颜色指定为 RGB 值：

### rgb(*red*, *green*, *blue*)

每个参数 (*red*、*green* 以及 *blue*) 定义了 0 到 255 之间的颜色强度。

例如，rgb(255, 0, 0) 显示为红色，因为红色设置为最大值（255），其他设置为 0。

要显示黑色，请将所有颜色参数设置为 0，如下所示：rgb(0, 0, 0)。

要显示白色，请将所有颜色参数设置为 255，如下所示：rgb(255, 255, 255)。

请通过混合以下 RGB 值来进行实验：

rgb(255, 99, 71)

### RED

255

### GREEN

99

### BLUE

71

### 实例

rgb(255, 0, 0)

rgb(0, 0, 255)

rgb(60, 179, 113)

rgb(238, 130, 238)

rgb(255, 165, 0)

rgb(106, 90, 205)

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_rgb)

通常为所有 3 个光源使用相等的值来定义灰色阴影：

### 实例

rgb(0, 0, 0)

rgb(60, 60, 60)

rgb(120, 120, 120)

rgb(180, 180, 180)

rgb(240, 240, 240)

rgb(255, 255, 255)

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_rgb_gray)

## RGBA 值

RGBA 颜色值是具有 alpha 通道的 RGB 颜色值的扩展 - 它指定了颜色的不透明度。

RGBA 颜色值指定为：

### rgba(*red*, *green*, *blue*, *alpha*)

*alpha* 参数是介于 0.0（完全透明）和 1.0（完全不透明）之间的数字：

请通过混合以下 RGBA 值来进行实验：

rgba(255, 99, 71, 0.5)

### RED

255

### GREEN

99

### BLUE

71

### ALPHA

0.5

### 实例

rgba(255, 99, 71, 0)

rgba(255, 99, 71, 0.2)

rgba(255, 99, 71, 0.4)

rgba(255, 99, 71, 0.6)

rgba(255, 99, 71, 0.8)

rgba(255, 99, 71, 1)

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_rgba)

# CSS HEX 颜色

## HEX 值

在 CSS 中，可以使用以下格式的十六进制值指定颜色：

### #*rrggbb*

其中 rr（红色）、gg（绿色）和 bb（蓝色）是介于 00 和 ff 之间的十六进制值（与十进制 0-255 相同）。

例如，#ff0000 显示为红色，因为红色设置为最大值（ff），其他设置为最小值（00）。

请通过混合以下十六进制值来进行实验：

\#ff6347

### RED

ff

### GREEN

63

### BLUE

47

### 实例

\#ff0000

\#0000ff

\#3cb371

\#ee82ee

\#ffa500

\#6a5acd

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_hex)



通常为所有 3 个光源使用相等的值来定义灰色阴影：

### 实例

\#000000

\#3c3c3c

\#787878

\#b4b4b4

\#f0f0f0

\#ffffff

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_hex_gray)

# CSS HSL 颜色

## HSL 值

在 CSS 中，可以使用色相、饱和度和明度（HSL）来指定颜色，格式如下：

### hsla(*hue*, *saturation*, *lightness*)

色相（*hue*）是色轮上从 0 到 360 的度数。0 是红色，120 是绿色，240 是蓝色。

饱和度（*saturation*）是一个百分比值，0％ 表示灰色阴影，而 100％ 是全色。

亮度（*lightness*）也是百分比，0％ 是黑色，50％ 是既不明也不暗，100％是白色。

请通过混合以下 HSL 值来进行实验：

hsl(0, 100%, 50%)

### HUE

0

### SATURATION

100%

### LIGHTNESS

50%

### 实例

hsl(0, 100%, 50%)

hsl(240, 100%, 50%)

hsl(147, 50%, 47%)

hsl(300, 76%, 72%)

hsl(39, 100%, 50%)

hsl(248, 53%, 58%)

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_hsl)

## 饱和度

饱和度可以描述为颜色的强度。

100％ 是纯色，没有灰色阴影

50％ 是 50％ 灰色，但是您仍然可以看到颜色。

0％ 是完全灰色，您无法再看到颜色。

### 实例

hsl(0, 100%, 50%)

hsl(0, 80%, 50%)

hsl(0, 60%, 50%)

hsl(0, 40%, 50%)

hsl(0, 20%, 50%)

hsl(0, 0%, 50%)

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_hsl_saturation)

## 亮度

颜色的明暗度可以描述为要赋予颜色多少光，其中 0％ 表示不亮（黑色），50％ 表示 50％ 亮（既不暗也不亮），100％ 表示全明（白）。

### 实例

hsl(0, 100%, 0%)

hsl(0, 100%, 25%)

hsl(0, 100%, 50%)

hsl(0, 100%, 75%)

hsl(0, 100%, 90%)

hsl(0, 100%, 100%)

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_hsl_lightness)

通常通过将色调和饱和度设置为 0 来定义灰色阴影，并将亮度从 0％ 到 100％ 进行调整可以得到更深/更浅的阴影：

### 实例

hsl(0, 0%, 0%)

hsl(0, 0%, 24%)

hsl(0, 0%, 47%)

hsl(0, 0%, 71%)

hsl(0, 0%, 94%)

hsl(0, 0%, 100%)

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_hsl_gray)

## HSLA 值

HSLA 颜色值是带有 Alpha 通道的 HSL 颜色值的扩展 - 它指定了颜色的不透明度。

HSLA 颜色值指定为：

### hsla(*hue*, *saturation*, *lightness*, *alpha*)

*alpha* 参数是介于 0.0（完全透明）和 1.0（完全不透明）之间的数字：

请通过混合以下 HSLA 值进行实验：

hsla(0, 100%, 50%, 0.5)

### HUE

0

### SATURATION

100%

### LIGHTNESS

50%

### ALPHA

0.5

### 实例

hsla(9, 100%, 64%, 0)

hsla(9, 100%, 64%, 0.2)

hsla(9, 100%, 64%, 0.4)

hsla(9, 100%, 64%, 0.6)

hsla(9, 100%, 64%, 0.8)

hsla(9, 100%, 64%, 1)

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_color_hsla)

# CSS 背景

CSS 背景属性用于定义元素的背景效果。

在这些章节中，您将学习如下 CSS 背景属性：

- background-color
- background-image
- background-repeat
- background-attachment
- background-position

## CSS background-color

background-color 属性指定元素的背景色。

### 实例

页面的背景色设置如下：

```
body {
  background-color: lightblue;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_background-color_body)

通过 CSS，颜色通常由以下方式指定：

- 有效的颜色名称 - 比如 "red"
- 十六进制值 - 比如 "#ff0000"
- RGB 值 - 比如 "rgb(255,0,0)"

请查看 [CSS 颜色值](https://www.w3school.com.cn/cssref/css_colors_legal.asp)，获取可能颜色值的完整列表。

## 其他元素

您可以为任何 HTML 元素设置背景颜色：

### 实例

在这里，<h1>、<p> 和 <div> 元素将拥有不同的背景色：

```
h1 {
  background-color: green;
}

div {
  background-color: lightblue;
}

p {
  background-color: yellow;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_background-color_elements)

## 不透明度 / 透明度

opacity 属性指定元素的不透明度/透明度。取值范围为 0.0 - 1.0。值越低，越透明：

opacity 1

opacity 0.6

opacity 0.3

opacity 0.1

### 实例

```
div {
  background-color: green;
  opacity: 0.3;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_background_opacity_1)

**注意：**使用 **opacity** 属性为元素的背景添加透明度时，其所有子元素都继承相同的透明度。这可能会使完全透明的元素内的文本难以阅读。

## 使用 RGBA 的透明度

如果您不希望对子元素应用不透明度，例如上面的例子，请使用 *RGBA* 颜色值。下面的例子设置背景色而不是文本的不透明度：

100% opacity

60% opacity

30% opacity

10% opacity

您从我们的 [CSS 颜色](https://www.w3school.com.cn/css/css_background.asp) 章节中学到了可以将 RGB 用作颜色值。除 RGB 外，还可以将 RGB 颜色值与 *alpha* 通道一起使用（RGB*A*） - 该通道指定颜色的不透明度。

RGBA 颜色值指定为：rgba(*red*, *green*, *blue*, *alpha*)。*alpha* 参数是介于 0.0（完全透明）和 1.0（完全不透明）之间的数字。

**提示：**您可在我们的 [CSS 颜色](https://www.w3school.com.cn/css/css_background.asp) 一章中学到有关 RGBA 颜色的更多知识。

### 实例

```
div {
  background: rgba(0, 128, 0, 0.3) /* 30% 不透明度的绿色背景 */
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_background_opacity_2)

# CSS 背景图像

## CSS 背景图像

background-image 属性指定用作元素背景的图像。

默认情况下，图像会重复，以覆盖整个元素。

### 实例

页面的背景图像可以像这样设置：

```
body {
  background-image: url("paper.gif");
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_background-image)



### 实例

本例展示了文本和背景图像的*错误组合*。文字难以阅读：

```
body {
  background-image: url("bgdesert.jpg");
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_background-image_bad)



**注意：**使用背景图像时，请使用不会干扰文本的图像。

还可以为特定元素设置背景图像，例如 <p> 元素：

### 实例

```
p {
  background-image: url("paper.gif");
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_background-image_p)

# CSS 背景重复

## CSS background-repeat

默认情况下，background-image 属性在水平和垂直方向上都重复图像。

某些图像应只适合水平或垂直方向上重复，否则它们看起来会很奇怪，如下所示：

### 实例

```
body {
  background-image: url("gradient_bg.png");
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_background-image_gradient_1)

如果上面的图像仅在水平方向重复 (background-repeat: repeat-x;)，则背景看起来会更好：

### 实例

```
body {
  background-image: url("gradient_bg.png");
  background-repeat: repeat-x;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_background-image_gradient_2)

**提示：**如需垂直重复图像，请设置 background-repeat: repeat-y;

## CSS background-repeat: no-repeat

background-repeat 属性还可指定只显示一次背景图像：

### 实例

背景图像仅显示一次：

```
body {
  background-image: url("tree.png");
  background-repeat: no-repeat;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_background-image_norepeat)

在上例中，背景图像与文本放置在同一位置。我们想要更改图像的位置，以免图像过多干扰文本。

## CSS background-position

background-position 属性用于指定背景图像的位置。

### 实例

把背景图片放在右上角：

```
body {
  background-image: url("tree.png");
  background-repeat: no-repeat;
  background-position: right top;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_background-image_position)

# CSS 背景附着

## CSS background-attachment

background-attachment 属性指定背景图像是应该滚动还是固定的（不会随页面的其余部分一起滚动）：

### 实例

指定应该固定背景图像：

```
body {
  background-image: url("tree.png");
  background-repeat: no-repeat;
  background-position: right top;
  background-attachment: fixed;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_background-image_attachment_1)



### 实例

指定背景图像应随页面的其余部分一起滚动：

```
body {
  background-image: url("tree.png");
  background-repeat: no-repeat;
  background-position: right top;
  background-attachment: scroll;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_background-image_attachment_2)

# CSS 背景简写

## CSS background - 简写属性

如需缩短代码，也可以在一个属性中指定所有背景属性。它被称为简写属性。

而不是这样写：

```
body {
  background-color: #ffffff;
  background-image: url("tree.png");
  background-repeat: no-repeat;
  background-position: right top;
}
```

您能够使用简写属性 `background`：

### 实例

使用简写属性在一条声明中设置背景属性：

```
body {
  background: #ffffff url("tree.png") no-repeat right top;
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_background_shorthand)

在使用简写属性时，属性值的顺序为：

- background-color
- background-image
- background-repeat
- background-attachment
- background-position

属性值之一缺失并不要紧，只要按照此顺序设置其他值即可。请注意，在上面的例子中，我们没有使用 background-attachment 属性，因为它没有值。

## 所有 CSS 背景属性

| 属性                                                         | 描述                                               |
| :----------------------------------------------------------- | :------------------------------------------------- |
| [background](https://www.w3school.com.cn/cssref/pr_background.asp) | 在一条声明中设置所有背景属性的简写属性。           |
| [background-attachment](https://www.w3school.com.cn/cssref/pr_background-attachment.asp) | 设置背景图像是固定的还是与页面的其余部分一起滚动。 |
| [background-clip](https://www.w3school.com.cn/cssref/pr_background-clip.asp) | 规定背景的绘制区域。                               |
| [background-color](https://www.w3school.com.cn/cssref/pr_background-color.asp) | 设置元素的背景色。                                 |
| [background-image](https://www.w3school.com.cn/cssref/pr_background-image.asp) | 设置元素的背景图像。                               |
| [background-origin](https://www.w3school.com.cn/cssref/pr_background-origin.asp) | 规定在何处放置背景图像。                           |
| [background-position](https://www.w3school.com.cn/cssref/pr_background-position.asp) | 设置背景图像的开始位置。                           |
| [background-repeat](https://www.w3school.com.cn/cssref/pr_background-repeat.asp) | 设置背景图像是否及如何重复。                       |
| [background-size](https://www.w3school.com.cn/cssref/pr_background-size.asp) | 规定背景图像的尺寸。                               |

# CSS 边框

## CSS 边框属性

CSS border 属性允许您指定元素边框的样式、宽度和颜色。

我的所有边都有边框。

我有一条红色的下边框。

我有圆角边框。

我有一条蓝色的左边框。

## CSS 边框样式

border-style 属性指定要显示的边框类型。

允许以下值：

- dotted - 定义点线边框
- dashed - 定义虚线边框
- solid - 定义实线边框
- double - 定义双边框
- groove - 定义 3D 坡口边框。效果取决于 border-color 值
- ridge - 定义 3D 脊线边框。效果取决于 border-color 值
- inset - 定义 3D inset 边框。效果取决于 border-color 值
- outset - 定义 3D outset 边框。效果取决于 border-color 值
- none - 定义无边框
- hidden - 定义隐藏边框

border-style 属性可以设置一到四个值（用于上边框、右边框、下边框和左边框）。

### 实例

演示不同的边框样式：

```
p.dotted {border-style: dotted;}
p.dashed {border-style: dashed;}
p.solid {border-style: solid;}
p.double {border-style: double;}
p.groove {border-style: groove;}
p.ridge {border-style: ridge;}
p.inset {border-style: inset;}
p.outset {border-style: outset;}
p.none {border-style: none;}
p.hidden {border-style: hidden;}
p.mix {border-style: dotted dashed solid double;}
```

结果：

点状边框。

虚线边框。

实线边框。

双线边框。

凹槽边框。其效果取决于 border-color 的值。

垄状边框。其效果取决于 border-color 的值。

3D inset 边框。其效果取决于 border-color 的值。

3D outset 边框。其效果取决于 border-color 的值。

无边框。

隐藏边框。

混合边框。

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_border-style)

**注意：**除非设置了 **border-style** 属性，否则其他 CSS 边框属性（将在下一章中详细讲解）都不会有任何作用！

# CSS 边框宽度

## CSS 边框宽度

border-width 属性指定四个边框的宽度。

可以将宽度设置为特定大小（以 px、pt、cm、em 计），也可以使用以下三个预定义值之一：thin、medium 或 thick：

### 实例

演示不同的边框宽度：

```
p.one {
  border-style: solid;
  border-width: 5px;
}

p.two {
  border-style: solid;
  border-width: medium;
}

p.three {
  border-style: dotted;
  border-width: 2px;
} 

p.four {
  border-style: dotted;
  border-width: thick;
}
```

结果：

5px border-width

medium border-width

2px border-width

thick border-width

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_border-width_1)

## 特定边的宽度

border-width 属性可以设置一到四个值（用于上边框、右边框、下边框和左边框）：

### 实例

```
p.one {
  border-style: solid;
  border-width: 5px 20px; /* 上边框和下边框为 5px，其他边为 20px */
}

p.two {
  border-style: solid;
  border-width: 20px 5px; /* 上边框和下边框为 20px，其他边为 5px */
}

p.three {
  border-style: solid;
  border-width: 25px 10px 4px 35px; /* 上边框 25px，右边框 10px，下边框 4px，左边框 35px */
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_border-width_2)

# CSS 边框颜色

## CSS 边框颜色

border-color 属性用于设置四个边框的颜色。

可以通过以下方式设置颜色：

- name - 指定颜色名，比如 "red"
- HEX - 指定十六进制值，比如 "#ff0000"
- RGB - 指定 RGB 值，比如 "rgb(255,0,0)"
- HSL - 指定 HSL 值，比如 "hsl(0, 100%, 50%)"
- transparent

**注释：**如果未设置 `border-color`，则它将继承元素的颜色。

### 实例

演示不同的边框颜色：

```
p.one {
  border-style: solid;
  border-color: red;
}

p.two {
  border-style: solid;
  border-color: green;
}

p.three {
  border-style: dotted;
  border-color: blue;
}
```

结果：

红色实线边框

绿色实线边框

蓝色点状边框

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_border-color_1)

## 特定边框的颜色

border-color 属性可以设置一到四个值（用于上边框、右边框、下边框和左边框）。

### 实例

```
p.one {
  border-style: solid;
  border-color: red green blue yellow; /* 上红、右绿、下蓝、左黄 */
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_border-color_2)

## HEX 值

边框的颜色也可以使用十六进制值（HEX）来指定：

### 实例

```
p.one {
  border-style: solid;
  border-color: #ff0000; /* 红色 */
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_border-color-hex)

## RGB 值

或者使用 RGB 值：

### 实例

```
p.one {
  border-style: solid;
  border-color: rgb(255, 0, 0); /* 红色 */
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_border-color-rgb)

## HSL 值

也可以使用 HSL 值：

### 实例

```
p.one {
  border-style: solid;
  border-color: hsl(0, 100%, 50%); /* 红色 */
}
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=css_border-color-hsl)

您可以在我们的 [CSS 颜色](https://www.w3school.com.cn/css/css_colors.asp) 章节中学到有关 HEX、RGB 和 HSL 值的更多知识。

