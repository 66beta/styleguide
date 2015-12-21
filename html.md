#整体样式规则
Revision 2.23

##协议
嵌入式资源省略协议头。
省略图片、媒体文件、样式表以及脚本的URL协议头部分（http:、https:），不使用这两个协议的文件则不省略。省略协议头，即让URL变成相对地址，可以避免协议混合及小文件重复下载。

```html
<!-- 不建议 -->
<script src="http://www.google.com/js/gweb/analytics/autotrack.js"></script>

<!-- 建议 -->
<script src="//www.google.com/js/gweb/analytics/autotrack.js"></script>
```
```css
/* 不建议 */
.example {
  background: url(http://www.google.com/images/example);
}

/* 建议 */
.example {
  background: url(//www.google.com/images/example);
}
```

#整体排版规则

##缩进
每次缩进使用两个空格。
不使用TAB键或者混合使用TAB键和空格进行缩进。
（SublimeText提供了原生功能，设定为2个空格后，可执行一个命令重新排版）
```html
<ul>
  <li>Fantastic
  <li>Great
</ul>
```
```css
.example {
  color: blue;
}
```

##大小写
只使用小写字母。
所有的代码都使用小写字母：适用于HTML元素、属性、属性值（除了text/CDATA）、CSS选择器、属性名以及属性值（字符串除外）。

```html
<!-- 不建议 -->
<A HREF="/">Home</A>
<!-- 建议 -->
<img src="google.png" alt="Google">
```
```css
/* 不建议 */
color: #E5E5E5;
/* 建议 */
color: #e5e5e5;
```

##尾部空格
删除尾部的空格。
尾部的空格是多余的，不删除则形成无意义的文件差异（git、svn）。
（SublimeText中有插件TrailingSpaces）
```html
<!-- 不建议 -->
<p>What?_
<!-- 建议 -->
<p>Yes please.
```

#整体元数据规则

##编码
使用UTF-8无BOM编码。
让你的编辑器使用无字节顺序标记的UTF-8编码。在HTML模板和文档中使用`<meta charset="utf-8">`指定编码。不需要为样式表指定编码，它默认是UTF-8。
（SublimeText提供了原生转换功能）

##注释
在需要时尽可能去解释你的代码。
用注释去解释你的代码，包括它的应用范围、用途、此方案的选择理由等。
（SublimeText中有辅助插件 DocBlockr）

##待处理内容
用TODO标记待办事宜和处理内容。
只用TODO来标记待办事宜，不要使用其他格式，例如@@。
在括号里添加联系方式（姓名或邮箱），格式为 TODO（联系方式）。
在冒号后面添加处理内容，格式为 TODO：处理内容。

```html
{# TODO(张三): 重新处理水平居中 #}
<center>Test</center>

<!-- TODO: 移除可选的元素 -->
<ul>
  <li>Apples</li>
  <li>Oranges</li>
</ul>
```

#HTML样式规则
##文档类型
HTML5（HTML语法）是所有HTML文档的首选：`<!DOCTYPE html>`。

##HTML合法性
使用合法的HTML代码，除非由于文件大小导致的不可达到的性能目标而不能使用。
（现代化编辑器都提供了相应验证功能，SublimeText中安装SublimeLinter和SublimeLinter-html-tidy插件即可）
```html
<!-- 不推荐 -->
<title>Test</title>
<article>This is only a test.

<!-- 推荐 -->
<!DOCTYPE html>
<meta charset="utf-8">
<title>Test</title>
<article>This is only a test.</article>
```

##语义化
在需要的地方，使用正确的html。
根据元素（有时被错误的叫做“标签”）被创造的初衷来使用他们。比如，对标题使用标题元素，对段落使用`<p>`元素，对锚点使用`<a>`元素等。
语义化的使用HTML对于可访问性、复用性和代码的高效性等因素非常重要。
```html
<!-- 不推荐 -->
<div onclick="goToRecommendations();">All recommendations</div>

<!-- 推荐 -->
<a href="recommendations/">All recommendations</a>
```

##多媒体降级
为多媒体提供替代内容。
对于图片、视频、通过canvas实现的动画等多媒体来说，确保提供可访问的替代内容。对于图片，可提供有意义的替代文本alt；对于视频和音频，如有条件可提供对白和字幕。
提供替代内容对辅助功能很重要：没有alt，一位盲人用户很难知道一张图片的内容，其他用户可能不能了解视频和音频的内容。（对于alt属性会引起冗余的图片和你不打算添加CSS的纯粹装饰性的图片，不用添加替代文本，写成 alt="" 即可。）
```html
<!-- 不推荐 -->
<img src="spreadsheet.png">
<!-- 推荐 -->
<img src="spreadsheet.png" alt="Spreadsheet screenshot.">
```

##关注点分离
将结构、表现、行为分离。
严格保持结构（标识），表现（样式），行为（脚本）分离，尽量使三者之间的相互影响最小。
就是说，确保文档和模板只包含HTML，并且HTML只用来表现结构。把任何表现性的东西都移到样式表，任何行为性的东西都移到脚本中。
此外，尽可能少的从文档和模板中引用样式表和脚本来减少三者的相互影响。
结构、表现、行为分离对维护非常重要。更改HTML文档和模板总是比更新样式表和脚本成本更高。
```html
<!-- 不推荐 -->
<!DOCTYPE html>
<title>HTML sucks</title>
<link rel="stylesheet" href="base.css" media="screen">
<link rel="stylesheet" href="grid.css" media="screen">
<link rel="stylesheet" href="print.css" media="print">
<h1 style="font-size: 1em;">HTML sucks</h1>
<p>I’ve read about this on a few sites but now I’m sure:
  <u>HTML is stupid!!1</u>
<center>I can’t believe there’s no way to control the styling of
  my website without doing everything all over again!</center>
<!-- 推荐 -->
<!DOCTYPE html>
<title>My first CSS-only redesign</title>
<link rel="stylesheet" href="default.css">
<h1>My first CSS-only redesign</h1>
<p>I’ve read about this on a few sites but today I’m actually
  doing it: separating concerns and avoiding anything in the HTML of
  my website that is presentational.
<p>It’s awesome!
```
##实体引用
不要使用实体引用。
假设文件、编辑器和团队之间使用相同的编码（UTF-8），则没有必要使用例如 &mdash; 、 &rdquo; 或 &#x263a; 这样的实体引用。
唯一的例外适用于HTML中具有特殊意义的字符（比如<和&），和控制或者隐藏的字符（比如不换行空格）。
```html
<!-- 不推荐 -->
The currency symbol for the Euro is &ldquo;&eur;&rdquo;.
<!-- 推荐 -->
The currency symbol for the Euro is "€".
```

##可选标签
可以考虑省略可选的标签。
为了优化文件大小和可扫描，考虑省略可选标签。 [HTML5规范](http://www.whatwg.org/specs/web-apps/current-work/multipage/syntax.html#syntax-tag-omission)定义了哪些标签可以被省略。
```html
<!-- 不推荐 -->
<!DOCTYPE html>
<html>
  <head>
    <title>Spending money, spending bytes</title>
  </head>
  <body>
    <p>Sic.</p>
  </body>
</html>
<!-- 推荐 -->
<!DOCTYPE html>
<title>Saving money, saving bytes</title>
<p>Qed.
```

##type属性
为样式表和脚本省略 type 属性。
引用样式表（除非不是使用CSS）和脚本（除非不是使用JavaScript）不要使用type属性。
HTML5将`text/css`和`text/javascript`设置为默认值，在这种情况下指定type属性并不必要。甚至同样兼容老版本的浏览器。

#HTML格式规则

##常规格式化
对每个块、列表、表格元素都另起一行，每个子元素都缩进。
每个块元素、列表元素或表格元素另起一行，而不必考虑元素的样式（因CSS可以改变元素的display属性）。
同样的，如果他们是块、列表或者表格元素的子元素，则将之缩进。
（如果你遇到列表项之间有空白的问题，可以把所有li元素放到一行。Linter鼓励抛出警告而不是错误。）
```html
<blockquote>
  <p><em>Space</em>, the final frontier.</p>
</blockquote>

<ul>
  <li>Moe
  <li>Larry
  <li>Curly
</ul>

<table>
  <thead>
    <tr>
      <th scope="col">Income
      <th scope="col">Taxes
  <tbody>
    <tr>
      <td>$ 5.00
      <td>$ 4.50
</table>
```

##HTML代码中的引号
当引用属性值时，使用双引号。
使用双引号而不是单引号来包裹属性值。
```html
<!-- 不推荐 -->
<a class='maia-button maia-button-secondary'>Sign in</a>
<!-- 推荐 -->
<a class="maia-button maia-button-secondary">Sign in</a>
```
