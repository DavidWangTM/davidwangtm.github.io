---
layout:     post
title:      "Markdown 简明语法"
subtitle:   "Markdown语言的基本语法讲解和展示"
date:       2014-10-14 14:00:00
author:     "DavidWang"
header-img: "img/post-bg-universe.jpg"
catalog: true
tags:
    - Markdown
---

> “Markdown语言是现在编写博客最为常用的语言。”

### 1.简介

Markdown的目标是实现「易读易写」，成为一种适用于网络的「书写语言」。

一份使用Markdown格式撰写的文件可以直接以纯文本发布，它的最大灵感来源其实是纯文本电子邮件的格式。

Markdown的语法由一些符号所组成，其作用一目了然。比如：在文字两旁加上星号，看起来就像**强调**。

Markdown兼容HTML语法并且会将`<`和`&`等符号进行自动转换，这项特性可以让我们很容易地用Markdown写HTML code。

### 2.区块元素

##### 2.1.段落和换行

* 空白行表示单一段落，相当于`<p/>`。
* 连续两个空格表示换行，相当于`<br/>`。
* 连续三个`*`或`+`或`-`,然后空白行，表示hr横线

##### 2.2.标题

标题是在首行插入1到6个`#`,对应的是标题1至标题6，例如：

```
# 这是 H1

## 这是 H2

...

###### 这是 H6
```

同时，也可以选择性地「闭合」标题，在行尾加上`#`，这么做只是为了美观：

```
# 这是 H1 #

## 这是 H2 ##

### 这是 H3 ###
```

##### 2.3.区块引用

Markdown标记区块引用是使用类似 email 中用`>`加`space`的引用方式:

```
> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.

> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
id sem consectetuer libero luctus adipiscing.
```

效果为：

> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.  

> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
id sem consectetuer libero luctus adipiscing.

区块引用可以嵌套，只要根据层次加上不同数量的`>`：

```
> This is the first level of quoting.
>
> > This is nested blockquote.
>
> Back to the first level.
```

效果为：

> This is the first level of quoting.
>
> > This is nested blockquote.
>
> Back to the first level.

引用的区块内也可以使用其他的Markdown语法，包括标题、列表、代码区块等：

>
> 1.   这是第一行列表项。
> 2.   这是第二行列表项。
>
> 给出一些例子代码：
>
>     return "Hello world!";

##### 2.4.列表

Markdown 支持有序列表和无序列表。

无序列表使用`*`、`+`或是`-`作为列表标记：

```
# 三种符号效果相同
*   Red
*   Green
*   Blue
+   Red
+   Green
+   Blue
-   Red
-   Green
-   Blue
```

效果均为：

*   Red
*   Green
*   Blue

有序列表则使用数字接着一个英文句点`.`：

```
1.  Bird
2.  McHale
3.  Parish
```

效果为：

1.  Bird
2.  McHale
3.  Parish

##### 2.5.代码区块

要在Markdown中建立代码区块很简单，只要简单地缩进4个`space`或是1个`tab`就可以，例如，下面的输入：

```
这是一个普通段落：

    这是一个代码区块。
```

效果为：

这是一个普通段落：

    这是一个代码区块。

代码区块中，一般的Markdown语法不会被转换，像是`*`便只是`*`。

此外，还可以使用一对
' \`\`\` '（连续三个数字键1左边的反引号）将段落包围起来使其成为代码区块。

##### 2.6.分割线

你可以在一行中用三个以上的`*`、`-`、`_`来建立一个分隔线，行内不能有其他东西。你也可以在`*`或是`-`中间插入`space`。下面每种写法都可以建立分隔线：

```
* * *

***

*****

- - -

---------------------------------------
```

### 3.区段元素

##### 3.1.链接

Markdown支持两种形式的链接语法： 行内式和参考式两种形式。

不管是哪一种，链接文字都是用`[]`来标记。

*3.1.1.行内式链接*

要建立一个行内式的链接，只要在`[]`后面紧接着`()`并插入网址链接即可，如果你还想要加上链接的`title`文字，只要在网址后面，用`""`把`title`文字包起来即可，例如：

```
这是[百度](http://www.baidu.com"百度首页")的行内式链接.

这个[百度](http://www.baidu.com)不包含`title`参数.
```

效果为：

这是[百度](http://www.baidu.com"百度首页")的行内式链接.

这个[百度](http://www.baidu.com)不包含`title`参数.

*3.1.2.参考式链接*

参考式的链接是在链接文字的`[]`后面再接上另一个`[]`，而在第二个`[]`里面要填入用以辨识链接的标记，接着，在文件的任意处，你可以把这个标记的链接内容定义出来：

<pre><code>
这是[百度][id]参考式链接.

[id]: http://www.baidu.com  "百度首页"
</code></pre>

效果为：

这是[百度][id]参考式链接.

[id]: http://www.baidu.com  (百度首页)

链接内容定义的形式为：

* `[]`（前面可以选择性地加上至多三个空格来缩进），里面输入链接文字
* 紧跟着一个`:`
* 接着是一个以上的`space`或`tab`
* 然后是链接的网址
* 最后可以选择性地输入`title`内容，可以用`''`、`""`或是`()`包着

##### 3.2.强调

Markdown使用星号`*`和下划线`_`作为标记强调字词的符号，被`*`或`_`包围的字词会被转成用`<em>`标签包围，用两个`*`或`_`包起来的话，则会被转成`<strong>`，例如：

```
*单个星号*

_单个下划线_

**双星号**

__双下划线__
```

效果为：

*单个星号*

_单个下划线_

**双星号**

__双下划线__

强调样式的限制是，样式符号必须成对出现，如果你的`*`和`_`两边都有空白的话，它们就只会被当成普通的符号。

强调也可以直接插在文字中间：

```
这是一个*强调*。
```

效果为：

这是一个*强调*。

##### 3.3.代码

如果要标记一小段行内代码，你可以用<code>`</code>把它包起来，例如：

```
Use the `printf()` function.
```

效果为：

Use the `printf()` function.

##### 3.4.图片

Markdown使用一种和链接很相似的语法来标记图片，同样也允许两种样式： 行内式和参考式。

*3.4.1.行内式图片*

行内式的图片语法看起来像是：

```
![测试图片](/image/test.jpg)

![测试图片](/image/test.jpg "你有多爱我？这么多！")
```

详细叙述如下：

* 一个`!`
* 接着一个`[]`，里面放上图片的替代文字
* 接着一个`()`，里面放上图片的网址，最后还可以用`""`包住,并加上选择性的`title`文字。

效果为：

![测试图片](/blog/image/test.jpg)

![测试图片](/blog/image/test.jpg "你有多爱我？这么多！")

*3.4.2.参考式图片*

参考式的图片语法则如下：

<pre><code>
![Alt text][id]

[id]: /blog/image/test.jpg "你有多爱我？这么多！"
</code></pre>

效果为：

![Alt text][id]

[id]: /blog/image/test.jpg "你有多爱我？这么多！"

到目前为止，Markdown还没有办法指定图片的宽高，如果你需要的话，你可以使用普通的`<img>`标签。

<br/>

### 4.其他

##### 4.1.自动链接

Markdown支持以比较简短的自动链接形式来处理网址和电子邮件信箱，只要是用`<>`包起来，Markdown就会自动把它转成链接。一般网址的链接文字就和链接地址一样，例如：

```
<http://www.baidu.com>

<baidu@163.com>
```

效果为：

<http://www.baidu.com>

<baidu@163.com>

##### 4.2.反斜杠

Markdown可以利用反斜杠来插入一些在语法中有其它意义的符号:

```
\\   反斜线
\`   反引号
\*   星号
\_   底线
\{}  花括号
\[]  方括号
\()  括弧
\#   井字号
\+   加号
\-   减号
\.   英文句点
\!   惊叹号
```

效果为：

\\   反斜线

\`   反引号

\*   星号

\_   底线

\{}  花括号

\[]  方括号

\()  括弧

\#   井字号

\+   加号

\-   减号

\.   英文句点

\!   惊叹号
