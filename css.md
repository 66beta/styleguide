Revision 2.23
https://github.com/google/styleguide/blob/gh-pages/htmlcssguide.xml

#css样式规则

##CSS合法性
尽可能使用有效的CSS。
使用有效的CSS代码，除非在处理css验证器bug或者是专有的语法时。
使用诸如[W3C CSS validator](http://jigsaw.w3.org/css-validator/)等工具验证测试。
使用有效的CSS代码是一个可衡量CSS代码质量的指标，可帮你找出不起作用可被删除的CSS代码，从而确保CSS的合理使用。

##id和class的命名
使用有意义的或者通用的id和class名称
用能反映出元素目的或者通用的id、class名称，代替那些很表象的、难懂的名称。
如果名称需要是易懂的，或不容易被修改，应该首选特定的或者能反映出元素目的的名称。
通用的名称适用于非特殊元素或与兄弟元素无区别的元素。他们常被称为“辅助元素”。
使用功能性或者通用的名称，可减少不必要的文档或者模板变化。

```css
/* 不推荐：无意义 */
#yee-1901 {}
/* 不推荐：表象 */
.button-green {}
.clear {}

/* 推荐：具体的 */
#gallery {}
#login {}
.video {}
/* 推荐：通用的 */
.aux {}
.alt {}
```

##id和class命名规范
ID和class命名要尽可能简短，但必要的话就别怕长。
尽可能简洁地传达id或者class名称的含义。
使用简洁的id或者class名称有助于提高可读性和代码效率。
(这部分请参见 BEM部分)

```css
/* 不推荐 */
#navigation {}
.atr {}

/* 推荐 */
#nav {}
.author {}
```

##类型选择器
省略类型选择器前的ID和class。

```css
/* 不推荐 */
ul#example {}
div.error {}
/* 推荐 */
#example {}
.error {}
```

##简写属性
尽可能使用简写的属性书写方式。
CSS提供了多种属性 简写 的方式（如font），即使只显式设置一个值，也应该尽可能地使用。
使用简写属性有助于提高代码效率及可读性。
(这部分可以借助工具，参考 前端工业化)

```css
/* 不推荐 */
border-top-style: none;
font-family: palatino, georgia, serif;
font-size: 100%;
line-height: 1.6;
padding-bottom: 2em;
padding-left: 1em;
padding-right: 1em;
padding-top: 0;
/* 推荐 */
border-top: 0;
font: 100%/1.6 palatino, georgia, serif;
padding: 0 1em 2em;
```

##0与单位
值为0的话，省略单位。

```css
margin: 0;
padding: 0;
```

##前缀0
省略值前面的0。

```css
font-size: .8em;
```

##十六进制
尽可能使用3位简写十六进制值。

```css
/* 不建议 */
color: #eebbcc;
/* 建议 */
color: #ebc;
```

##前缀
大型项目中以及嵌入在其它项目或外部网站上的代码需要给id和class添加前缀（命名空间）。使用短的、独特的标识符，并在其后跟一个破折号。 使用命名空间有助于防止命名冲突，可以让维护变得简单，例如在搜索和替换操作时。

```css
.adw-help {} /* AdWords */
#maia-note {} /* Maia */
```

##id与class名称分隔符
选择器中的词语和缩写中不要使用除了连字符以外的任何字符（包括空字符），以提高可理解性和可读性。

```css
/* 不推荐 */
.demoimage {}
.error_status {}

/* 推荐 */
#video-id {}
.ads-sample {}
```

##Hack兼容
请先尝试其他的方法，避免用户代理检测以及CSS的“hacks”。
进行用户代理检测或使用特殊的CSS选择器及hacks看起来是处理样式差异的捷径。但为了实现和保持高效性以及代码的可维护性，这两种方案应该放到最后考虑。换句话说，用户代理检测和使用hacks会增大项目推进的阻力，所以从项目的长远利益考虑应尽力避免。一旦允许并无顾忌地使用用户代理检测和hacks便很容易滥用，最终一发而不可收。


#CSS格式化规则

##声明顺序
按字母顺序排列声明。
css文件书写按字母顺序排列的方式，容易记忆和维护，以达到一致的代码。
在排序时忽略浏览器特定的前缀。但是，特定CSS属性的多个浏览器前缀应按字母顺序排列（如-moz书写在-webkit前面）。

```css
background: fuchsia;
border: 1px solid;
-moz-border-radius: 4px;
-webkit-border-radius: 4px;
border-radius: 4px;
color: black;
text-align: center;
text-indent: 2em;
```

##块内容的缩进
将包括嵌套及声明的块内容进行缩进，以体现层次并提高可读性。

```css
@media screen, projection {

  html {
    background: #fff;
    color: #444;
  }

}
```

##声明结束
每个属性后使用分号结束。
以分号结束每个属性，提高一致性和可扩展性。
（这部分可以参考 前端工程化）

```css
/* 不推荐 */
.test {
  display: block;
  height: 100px
}
/* 推荐 */
.test {
  display: block;
  height: 100px;
}
```

##CSS属性名结束
属性名称的冒号后有一个空格。
为保证一致性，在属性名与属性值之间添加一个空格（但是属性名和冒号间没有空格）。

```css
/* 不推荐 */
h3 {
  font-weight:bold;
}
/* 推荐 */
h3 {
  font-weight: bold;
}
```

##声明块间隔
在选择器和后面的声明块之间使用一个空格。
最后一个选择器与表示声名块开始的左大花括号在同行，中间有一个字符空格。
表示开始的左大花括号和选择器在同行。

```css
/* 不推荐：缺少空间 */
#video{
  margin-top: 1em;
}
/* 不推荐：不必要的换行符 */
#video
{
  margin-top: 1em;
}
/* 推荐 */
#video {
  margin-top: 1em;
}
```

##选择器及声明分离
每个选择器和声明独立成行。

```css
/* 不推荐 */
a:focus, a:active {
  position: relative; top: 1px;
}
/* 推荐 */
h1,
h2,
h3 {
  font-weight: normal;
  line-height: 1.2;
}
```

##CSS代码块分离
使用新空行分离规则。
始终把一个空行（两个换行符）放在代码块规则之间。

```css
html {
  background: #fff;
}

body {
  margin: auto;
  width: 50%;
}
```

##CSS引号
属性选择器和属性值中使用单引号。
在属性选择器及属性值中使用单引号（''）而不是双引号（""）。在 url（） 中不要使用引号。
特例：如果你确实需要定义@charset，由于不允许使用单引号，故请使用双引号。

```css
/* 不推荐 */
@import url("//www.google.com/css/maia.css");

html {
  font-family: "open sans", arial, sans-serif;
}

/* 推荐 */
@import url(//www.google.com/css/maia.css);

html {
  font-family: 'open sans', arial, sans-serif;
}
```

#CSS元规则

##分段注释
通过分段注释来区分段落。段落在注释后新起一行。

```css
/* Header */

#adw-header {}

/* Footer */

#adw-footer {}

/* Gallery */

.adw-gallery {}
```

