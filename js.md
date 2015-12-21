Revision 2.93
https://github.com/google/styleguide/blob/gh-pages/javascriptguide.xml

#语言规范

##var
声明变量必须加上 var 关键字。
当你没有写 var, 变量就会暴露在全局上下文中, 这样很可能会和现有变量冲突. 另外, 如果没有加上, 很难明确该变量的作用域是什么, 变量也很可能像在局部作用域中, 很轻易地泄漏到 Document 或者 Window 中, 所以务必用 var 去声明变量.

##常量
常量的形式如: NAMES_LIKE_THIS, 即使用大写字符, 并用下划线分隔. 你也可用 @const 标记来指明它是一个常量. 但请永远不要使用 const 关键词。

###常量值
如果一个值是恒定的，它命名中的字母要全部大写（如CONSTANT_VALUE_CASE）。字母全部大写意味着这个值不可以被改写。
原始类型（ number 、 string 、 boolean ）是常量值。
对象的表现会更主观一些，当它们没有暴露出变化的时候，应该认为它们是常量。但是这个不是由编译器决定的。

###常量指针（变量和属性）
用 @const 注释的变量和属性意味着它是不能更改的。使用const关键字可以保证在编译的时候保持一致。使用 const 效果相同，但是由于IE的兼容问题，我们不使用const关键字。
另外，不应该修改用 @const 注释的方法。

###例子
注意，@const不一定是常量值，但命名类似CONSTANT_VALUE_CASE的**一定**是常量指针。

```js
/**
* 以毫秒为单位的超时时长
* @type {number}
*/
goog.example.TIMEOUT_IN_MILLISECONDS = 60;
```
1分钟60秒永远也不会改变，这是个常量。全部大写的命名意味其为常量值，所以它不能被重写。开源的编译器允许这个符号被重写，这是因为没有@const标记。

```js
/**
* Map of URL to response string.
* @const
*/
MyClass.fetchedUrlCache_ = new goog.structs.Map();
/**
 * Class that cannot be subclassed.
 * @const
 * @constructor
 */
sloth.MyFinalClass = function() {};
```
在这个例子中，指针没有变过，但是值却是可以变化的，所以这里用了驼峰式的命名，而不是全部大写的命名。

##分号
始终使用分号。
如果仅依靠语句间的隐式分隔，有时会很麻烦。
你自己更能清楚哪里是语句的起止。
而且有些情况下，漏掉分号会很危险:

```js
// 1.
MyClass.prototype.myMethod = function() {
  return 42;
}  // 没有分号.
(function() {
    // 一些局部作用域中的初始化代码
})();

// 2.
var x = {
  'i': 1,
  'j': 2
}  //没有分号.
//试着在IE和firefox下做一样的事情
[normalVersion, ffVersion][isIE]();

// 3.
var THINGS_TO_EAT = [apples, oysters, sprayOnCheese] //没有分号
-1 == resultOfOperation() || die();
```

###这段代码会发生些什么诡异事呢?
1. 报 JavaScript 错误 - 例子1上的语句会解释成，一个函数带一匿名函数作为参数而被调用，返回42后, 又一次被"调用"，这就导致了错误。
2. 例子2中，你很可能会在运行时遇到 'no such property in undefined' 错误，原因是代码试图这样 `x[ffVersion][isIE]()` 执行。
3. 当 `resultOfOperation()` 返回非 `NaN` 时, 就会调用die, 其结果也会赋给 `THINGS_TO_EAT。`

###为什么？
JavaScript 的语句以分号作为结束符，除非可以非常准确推断某结束位置才会省略分号。上面的几个例子产出错误, 均是在语句中声明了函数/对象/数组直接量，但 闭括号('}'或']')并不足以表示该语句的结束。在 JavaScript 中，只有当语句后的下一个符号是后缀或括号运算符时，才会认为该语句的结束。
遗漏分号有时会出现很奇怪的结果，所以确保语句以分号结束。

###区分：分号和函数
函数表达式后面要分号结束，但是函数声明就不需要。例如：

```js
var foo = function() {
  return true;
};  // 要分号

function foo() {
  return true;
}  // 不用
```

##嵌套函数
嵌套函数很有用，减少重复代码，隐藏帮助函数等。什么其他需要注意的地方，随意使用。

##块内函数声明
不要在块内声明一个函数
不要写成：

```js
if (x) {
  function foo() {}
}
```
虽然很多JS引擎都支持块内声明函数, 但它不属于 ECMAScript 规范。各个浏览器糟糕的实现相互不兼容，有些也与未来 ECMAScript 草案相违背。ECMAScript 只允许在脚本的根语句或函数中声明函数。 如果确实需要在块中定义函数，建议使用函数表达式来初始化变量：

```js
if (x) {
  var foo = function() {}
}
```

##异常
你在写一个比较复杂的应用时，不可能完全避免不会发生任何异常，大胆去用吧。

##自定义异常
有时发生异常了，但返回的错误信息比较奇怪，也不易读. 虽然可以将含错误信息的引用对象或者可能产生错误的完整对象传递过来, 但这样做都不是很好，最好还是自定义异常类，其实这些基本上都是最原始的异常处理技巧。所以在适当的时候使用自定义异常。

##标准特性
总是优于非标准特性。
最大化可移植性和兼容性，尽量使用标准方法而不是用非标准方法，(比如，优先用`string.charAt(3)`而不用`string[3]`，通过DOM原生函数访问元素，而不是使用应用封装好的快速接口。

##包装原始类型对象
不要用。
没有任何理由去封装基本类型，另外还存在一些风险：

```js
// 不要这样做！
var x = new Boolean(false);
if (x) {
  alert('hi');  // Shows 'hi'.
}
```
然而类型转换是可以的：

```js
var x = Boolean(0);
if (x) {
  alert('hi');  //永远都不显示。
}
typeof Boolean(0) == 'boolean';
typeof new Boolean(0) == 'object';
```
这样可以方便地转换成`number`、`string`和`boolean`。

##多级原型结构
不可取。
多级原型结构是指 JavaScript 中的继承关系。当你自定义一个D类，且把B类作为其原型，那么这就获得了一个多级原型结构。这些原型结构会变得越来越复杂！
使用[the Closure 库](http://code.google.com/closure/library/)中的`goog.inherits()`或其他类似的用于继承的函数，会是更好的选择。

```js
function D() {
  goog.base(this)
}
goog.inherits(D, B);
D.prototype.method = function() {
  ...
};
```

##方法和属性定义

```js
/**构造函数*/
function SomeConstructor() {
  this.someProperty = 1;
}
Foo.prototype.someMethod = function() {
  ...
};
```
有很多方法可以给构造器添加方法或成员，我们更倾向于使用如下的形式：

```js
Foo.prototype.bar = function() {
  /* ... */
};
```
其他特性的首选创建方式是在构造函数中初始化字段：

```js
/** @constructor */
function Foo() {
  this.bar = value;
}
```

###为什么？
当前的JavaScript引擎优化基于一个对象的“形状”， 给对象添加一个属性（包括覆盖原型设置的值）改变了“形状”，会降低性能 。

##删除
请使用`this.foo = null`。

```js
o.prototype.dispose = function() {
  this.property_ = null;
};
```
而不是：
```js
Foo.prototype.dispose = function() {
  delete his.property_;
};
```
在现代的JavaScript引擎中，改变一个对象属性的数量比重新分配值慢得多。应该避免删除关键字，除非有必要从一个对象的迭代的关键字列表删除一个属性，或改变`if (key in obj)`结果。

##闭包
可以使用，但是要小心。
闭包也许是 JS 中最有用的特性了. 有一份比较好的介绍闭包原理的[文档](http://jibbering.com/faq/faq_notes/closures.html).
有一点需要牢记，闭包保留了一个指向它封闭作用域的指针，所以，在给 DOM 元素附加闭包时，很可能会产生循环引用，进一步导致内存泄漏。比如下面的代码：

```js
function foo(element, a, b) {
  element.onclick = function() { /* 使用 a 和 b */ };
}
```
这里，即使没有使用 element，闭包也保留了element、a、b的引用。由于 element 也保留了对闭包的引用，这就产生了循环引用，这就不能被垃圾回收器回收。这种情况下，可将代码重构为：

```js
function foo(element, a, b) {
  element.onclick = bar(a, b);
}
function bar(a, b) {
  return function() { /* 使用 a 和 b */ }
}
```

##eval()
只用在代码加载与REPL(Read–eval–print loop)。
若eval() 会让程序执行的比较混乱，当 eval() 里面包含用户输入的话就更加危险。可以用其他更佳的，更清晰，更安全的方式写你的代码，所以一般情况下请不要使用 eval()。
对于RPC你可以用JSON格式，然后通过`JSON.parse()`来读取，而不是`eval()`。
假设服务器端返回了如下内容：

```js
{
  "name": "Alice",
  "id": 31502,
  "email": "looking_glass@example.com"
}
// 不建议
var userInfo = eval(feed);
var email = userInfo['email'];
```
万一里面含有恶意的js代码，使用eval的话，恶意代码就会被执行。

```js
// 建议
var userInfo = JSON.parse(feed);
var email = userInfo['email'];
```
用`JSON.parse()`，碰到违法的JSON会抛出一个错误。

##with(){}
使用 with 让你的代码在语义上变得不清晰。因为 with 的对象，可能会与局部变量产生冲突，从而改变你程序原本的用义。下面的代码是干嘛的？

```js
with (foo) {
  var x = 3;
  return x;
}
```
答案: 任何事。局部变量 x 可能被 foo 的属性覆盖，当它定义一个 setter 时，在赋值 3 后会执行很多其他代码。所以不要使用 with 语句。

##this
仅在对象构造器、方法、闭包中使用。
this 的语义很特别。有时它引用一个全局对象(大多数情况下)，调用者的作用域(使用 eval时)，DOM 树中的节点(添加事件处理函数时)，新创建的对象(使用一个构造器)，或者其他对象(如果函数被 call() 或 apply())。
使用时很容易出错，所以只有在下面两个情况时才能使用：

- 在构造器中
- 对象的方法(包括创建的闭包)中

##for-in循环
只用于 object/map/hash 的遍历。
对 Array 用 for-in 循环有时会出错。因为它并不是从 0 到 length - 1 进行遍历，而是所有出现在对象及其原型链的键值。 下面就是一些失败的使用案例：

```js
function printArray(arr) {
  for (var key in arr) {
    print(arr[key]);
  }
}
printArray([0,1,2,3]);  // 这样可以

var a = new Array(10);
printArray(a);  // 这样不行

a = document.getElementsByTagName('*');
printArray(a);  // 这样不行

a = [0,1,2,3];
a.buhu = 'wine';
printArray(a);  // 这样不行

a = new Array;
a[3] = 3;
printArray(a);  // 这样不行
```
而遍历数组通常用最普通的 for 循环：

```js
function printArray(arr) {
  var l = arr.length;
  for (var i = 0; i < l; i++) {
    print(arr[i]);
  }
}
```

##关联数组
永远不要使用 Array 作为 map/hash/associative 数组。
数组中不允许使用非整型作为索引值，所以也就不允许用关联数组。
而取代它使用 Object 来表示 map/hash 对象。Array 仅仅是扩展自 Object (类似于其他 JS 中的对象，就像 Date, RegExp 和 String)一样来使用。

##多行字符串
不要这样写长字符串：

```js
var myString = 'A rather long string of English text, an error message \
                actually that just keeps going and going -- an error \
                message to make the Energizer bunny blush (right through \
                those Schwarzenegger shades)! Where was I? Oh yes, \
                you\'ve got an error and all the extraneous whitespace is \
                just gravy.  Have a nice day.';
```
在编译时，不能忽略行起始位置的空白字符；"\"后的空白字符会产生奇怪的错误；虽然大多数脚本引擎支持这种写法，但它不是 ECMAScript 的标准规范。
使用字符串拼接方式来代替：

```js
var myString = 'A rather long string of English text, an error message ' +
   'actually that just keeps going and going -- an error ' +
   'message to make the Energizer bunny blush (right through ' +
   'those Schwarzenegger shades)! Where was I? Oh yes, ' +
   'you\'ve got an error and all the extraneous whitespace is ' +
   'just gravy.  Have a nice day.';
```

##数组和对象字面量
使用 Array 和 Object 语法，而不使用 Array 和 Object 构造器。
使用 Array 构造器很容易因为传参不恰当导致错误。

```js
// 长度3
var a1 = new Array(x1, x2, x3);

// 长度2
var a2 = new Array(x1, x2);

// 如果x1是个数字，并且是自然数，那么长度就是x1
// 如果x1是个数字，但不是自然数，会抛出异常
// 不然数组只包含x1一个元素，即它的值
var a3 = new Array(x1);

// 长度0
var a4 = new Array();
```
如果传入一个参数而不是2个参数，数组的长度很有可能就不是你期望的数值了。
为了避免这些歧义，我们应该使用更易读的直接量来声明。

```js
var a  = [x1, x2, x3];
var a2 = [x1, x2];
var a3 = [x1];
var a4 = [];
```
虽然 Object 构造器没有上述类似的问题，但鉴于可读性和一致性考虑，最好还是在字面上更清晰地指明。

```js
// 不建议
var o = new Object();
var o2 = new Object();
o2.a = 0;
o2.b = 1;
o2.c = 2;
o2['strange key'] = 3;
```
应该写成：

```js
var o = {};
var o2 = {
  a: 0,
  b: 1,
  c: 2,
  'strange key': 3
};
```

##修改内置对象原型
千万不要修改内置对象，如 Object.prototype 和 Array.prototype 的原型。而修改内置对象，如 Function.prototype 的原型，虽然少危险些，但仍会导致调试时的诡异现象。所以也要避免修改其原型。

##Internet Explorer条件注释
不要这样写：

```js
var f = function () {
  /*@cc_on if (@_jscript) { return 2* @*/  3; /*@ } @*/
};
```
条件注释妨碍自动化工具的执行，因为在运行时，它们会改变 JavaScript 语法树。


#Javascript编码风格
Revision 2.93

##命名
通常，使用 `functionNamesLikeThis`，`variableNamesLikeThis`， `ClassNamesLikeThis``，EnumNamesLikeThis``，methodNamesLikeThis`， `CONSTANT_VALUES_LIKE_THIS`，`foo.namespaceNamesLikeThis.bar`和`filenameslikethis.js`。

###属性和方法

- 文件或类中的 _私有_ 属性，变量和方法名应该以下划线 "_" 开头
- _保护_ 属性，变量和方法名不需要下划线开头，和公共变量名一样

###方法和函数参数
选函数参数以`opt_`开头。
函数的参数个数不固定时, 应该添加最后一个参数`var_args`为参数的个数. 你也可以不设置`var_args`而取代使用`arguments`。
可选和可变参数应该在`@param`标记中说明清楚。虽然这两个规定对编译器没有任何影响, 但还是请尽量遵守。

###Getters和Setters
EcmaScript5不鼓励使用属性的getter和setter。然而，如果使用它们，那么getter就不要改变属性的可见状态。

```js
/**
*错误--不要这样做.
*/
var foo = { get next() { return this.nextId++; } };
```

###存取函数
属Getters 和 setters 并不是必要的。但只要使用它们了，就请将 getters 命名成`getFoo()`形式，将 setters 命名成`setFoo(value)`形式。(对于布尔类型的 getters，使用`isFoo()`也可。)

###命名空间
JavaScript 不支持包和命名空间。
不容易发现和调试全局命名的冲突，多个系统集成时还可能因为命名冲突导致很严重的问题。为了提高 JavaScript 代码复用率，我们遵循下面的约定以避免冲突。

####为全局代码使用命名空间
在全局范围内**总是**在全局作用域上，使用一个唯一的，与工程/库相关的名字作为前缀标识。比如，你的工程是 "Project Sloth", 那么命名空间前缀可取为 sloth.*。

```js
var sloth = {};
sloth.sleep = function() {
  ...
};
```
很多JavaScript库，包括 [the Closure Library](https://developers.google.com/closure/library/?csw=1) 和 [Dojo toolkit](http://dojotoolkit.org/) 给为你提供了声明你自己的命名空间的函数。比如：

```js
goog.provide('sloth');
sloth.sleep = function() {
  ...
};
```

####明确命名空间所有权
当选择了一个子命名空间, 请确保父命名空间的负责人知道你在用哪个子命名空间，比如说，你为工程 'sloths' 创建一个 'hats' 子命名空间，那确保 Sloth 团队人员知道你在使用`sloth.hats`。

####外部代码和内部代码使用不同的命名空间
“外部代码”是指来自于你代码体系的外部，可以独立编译。内外部命名应该严格保持独立。如果你使用了外部库，他的所有对象都在`foo.hats.*`下， 那么你自己的代码不能在`foo.hats.*`下命名，因为很有可能其他团队也在其中命名。

```js
foo.require('foo.hats');
/**
*错误--不要这样做。
* @constructor
* @extends {foo.hats.RoundHat}
*/
foo.hats.BowlerHat = function() {
};
```
如果你需要在外部命名空间中定义新的 API，那么你应该直接导出一份外部库，然后在这份代码中修改。在你的内部代码中，应该通过他们的内部名字来调用内部 API，这样保持一致性可让编译器更好的优化你的代码。

```js
foo.provide('googleyhats.BowlerHat');
foo.require('foo.hats');
/**
* @constructor
* @extends {foo.hats.RoundHat}
*/
googleyhats.BowlerHat = function() {
  ...
};
goog.exportSymbol('foo.hats.BowlerHat', googleyhats.BowlerHat);
```

####重命名那些名字很长的变量, 提高可读性
主要是为了提高可读性。局部空间中的变量别名只需要取原名字的最后部分。

```js
/**
 * @constructor
 */
some.long.namespace.MyClass = function() {
};
/**
 * @param {some.long.namespace.MyClass} a
 */
some.long.namespace.MyClass.staticHelper = function(a) {
  ...
};
myapp.main = function() {
  var MyClass = some.long.namespace.MyClass;
  var staticHelper = some.long.namespace.MyClass.staticHelper;
  staticHelper(new MyClass());
};
```
不要为命名空间起本地别名：

```js
// 不建议
myapp.main = function() {
  var namespace = some.long.namespace;
  namespace.MyClass.staticHelper(new namespace.MyClass());
};
```
除非是枚举类型，不然不要访问别名变量的属性：

```js
/** @enum {string} */
some.long.namespace.Fruit = {
  APPLE: 'a',
  BANANA: 'b'
};
myapp.main = function() {
  var Fruit = some.long.namespace.Fruit;
  switch (fruit) {
    case Fruit.APPLE:
      ...
    case Fruit.BANANA:
      ...
  }
};
// 不建议
myapp.main = function() {
  var MyClass = some.long.namespace.MyClass;
  MyClass.staticHelper(null);
};
```
不要在全局范围内创建别名，而仅在函数块作用域中使用。

###文件名
文件名应该使用小写字符，以避免在有些系统平台上不识别大小写的命名方式。 文件名以.js结尾，不要包含除 - 和 _ 外的标点符号(使用 - 优于 _)。

##自定义toString()方法
应该总是成功调用且不要抛异常。
可自定义 `toString()` 方法，但确保你的实现方法满足：(1)总是成功 (2)没有其他负面影响。如果不满足这两个条件，那么可能会导致严重的问题，比如，如果 `toString()` 调用了包含 assert 的函数，assert 输出导致失败的对象，这在 `toString()` 也会被调用。

##延时初始化
没必要在每次声明变量时就将其初始化。

##明确作用域
任何时候都要明确作用域 - 提高可移植性和清晰度。例如, 不要依赖于作用域链中的 `window` 对象。可能在其他应用中，你函数中的 `window` 不是指之前的那个窗口对象。

##代码格式化
主要依照[C++格式规范](https://google.github.io/styleguide/cppguide.xml#Formatting)，针对JavaScript，还有下面一些附加说明。

###大括号
分号会被隐式插入到代码中，所以你务必在同一行上插入大括号。例如：

```js
if (something) {
  // ...
} else {
  // ...
}
```

###数组与对象的初始化
如果初始值不是很长，就保持写在单行上：

```js
var arr = [1, 2, 3];  // [号后、]号前，无空格
var obj = {a: 1, b: 2, c: 3};  // {号后、}号前，无空格
```
初始值占用多行时, 缩进2个空格：

```js
// 对象初始化
var inset = {
  top: 10,
  right: 20,
  bottom: 15,
  left: 12
};
// 数组初始化
this.rows_ = [
  '"Slartibartfast" <fjordmaster@magrathea.com>',
  '"Zaphod Beeblebrox" <theprez@universe.gov>',
  '"Ford Prefect" <ford@theguide.com>',
  '"Arthur Dent" <has.no.tea@gmail.com>',
  '"Marvin the Paranoid Android" <marv@googlemail.com>',
  'the.mice@magrathea.com'
];
// 在方法调用中的对象初始化
goog.dom.createDom(goog.dom.TagName.DIV, {
  id: 'foo',
  className: 'some-css-class',
  style: 'display:none'
}, 'Hello, world!');
```
比较长的标识符或者数值，不要为了让代码好看些而手工对齐。如：

```js
CORRECT_Object.prototype = {
  a: 0,
  b: 1,
  lengthyName: 2
};
// 错误的方式
WRONG_Object.prototype = {
  a          : 0,
  b          : 1,
  lengthyName: 2
};
```

###函数参数
尽量让函数参数在同一行上。如果一行超过 80 字符，每个参数独占一行，并以4个空格缩进，或者与括号对齐，以提高可读性。尽可能不要让每行超过80个字符。比如下面这样：

```js
// 四个空格，每行包括80个字符。适用于非常长的函数名，
// 重命名不需要重新缩进，占用空间小。
goog.foo.bar.doThingThatIsVeryDifficultToExplain = function(
    veryDescriptiveArgumentNumberOne, veryDescriptiveArgumentTwo,
    tableModelEventHandlerProxy, artichokeDescriptorAdapterIterator) {
  // ...
};

// 四个空格，每行一个参数。适用于长函数名，
// 允许重命名，并且突出每个参数。
goog.foo.bar.doThingThatIsVeryDifficultToExplain = function(
    veryDescriptiveArgumentNumberOne,
    veryDescriptiveArgumentTwo,
    tableModelEventHandlerProxy,
    artichokeDescriptorAdapterIterator) {
  // ...
};
// 缩进和括号对齐，每行80字符。 看上去是分组的参数，
// 占用空间小。
function foo(veryDescriptiveArgumentNumberOne, veryDescriptiveArgumentTwo,
             tableModelEventHandlerProxy, artichokeDescriptorAdapterIterator) {
  // ...
}
// 和括号对齐，每行一个参数。看上去是分组的并且
// 突出每个参数。
function bar(veryDescriptiveArgumentNumberOne,
             veryDescriptiveArgumentTwo,
             tableModelEventHandlerProxy,
             artichokeDescriptorAdapterIterator) {
  // ...
}
```
当函数调用本身缩进，你可以自由地开始相对于原始声明的开头或者相对于当前函数调用的开头，进行4个空格的缩进。以下都是可接受的缩进风格。

```js
if (veryLongFunctionNameA(
        veryLongArgumentName) ||
    veryLongFunctionNameB(
    veryLongArgumentName)) {
  veryLongFunctionNameC(veryLongFunctionNameD(
      veryLongFunctioNameE(
          veryLongFunctionNameF)));
}
```

###匿名函数传递
如果参数中有匿名函数，函数体从调用该函数的左边开始缩进2个空格，而不是从 function 这个关键字开始。这让匿名函数更加易读 (不要增加很多没必要的缩进让函数体显示在屏幕的右侧)。

```js
prefix.something.reallyLongFunctionName('whatever', function(a1, a2) {
  if (a1.equals(a2)) {
    someOtherLongFunctionName(a1);
  } else {
    andNowForSomethingCompletelyDifferent(a2.parrot);
  }
});

var names = prefix.something.myExcellentMapFunction(
    verboselyNamedCollectionOfItems,
    function(item) {
      return item.name;
    });
```

###换行缩进
除了数字字面量、对象字面量、匿名函数，所有换行都应跟上面的同级表达式左对齐，或者，比父级表达式再缩进4个空格（不是2个）。

```js
someWonderfulHtml = '' +
                    getEvenMoreHtml(someReallyInterestingValues, moreValues,
                                    evenMoreParams, 'a duck', true, 72,
                                    slightlyMoreMonkeys(0xfff)) +
                    '';

thisIsAVeryLongVariableName =
    hereIsAnEvenLongerOtherFunctionNameThatWillNotFitOnPrevLine();

thisIsAVeryLongVariableName = siblingOne + siblingTwo + siblingThree +
    siblingFour + siblingFive + siblingSix + siblingSeven +
    moreSiblingExpressions + allAtTheSameIndentationLevel;

thisIsAVeryLongVariableName = operandOne + operandTwo + operandThree +
    operandFour + operandFive * (
        aNestedChildExpression + shouldBeIndentedMore);

someValue = this.foo(
    shortArg,
    'Some really long string arg - this is a pretty common case, actually.',
    shorty2,
    this.bar());

if (searchableCollection(allYourStuff).contains(theStuffYouWant) &&
    !ambientNotification.isActive() && (client.isAmbientSupported() ||
                                        client.alwaysTryAmbientAnyways())) {
  ambientNotification.activate();
}
```

###空行
利用空行来划分逻辑上关联的代码。

```js
doSomethingTo(x);
doSomethingElseTo(x);
andThen(x);

nowDoSomethingWith(y);

andNowWith(z);
```

###二元和三元操作符
操作符始终跟随着前行，这样就不用顾虑分号的隐式插入问题。 如果一行实在放不下，还是按照上述的缩进风格来换行。

```js
// 放得下的话就放一行
var x = a ? b : c;
// 缩进4个空格
var y = a ?
    longButSimpleOperandB : longButSimpleOperandC;
// 按照第一个操作数缩进
var z = a ?
        moreComplicatedB :
        moreComplicatedC;
// 也适用于点运算符
var x = foo.bar().
    doSomething().
    doSomethingElse();
```

##括号
只在需要的时候使用。
不要滥用括号，只在必要的时候使用它。
对于一元操作符(如delete, typeof 和 void )，或是在某些关键词(如 return、throw、case、new)之后，不要使用括号。

##字符串
使用单引号`'`代替双引号`"`。
单引号(')优于双引号(")。当你创建一个包含HTML代码的字符串时就知道它的好处了。

```js
var msg = 'This is some HTML';
```

##可见性（私有和保护字段）
推荐使用 JSDoc 中的两个标记 `@private` 和 `@protected` 来指明类, 函数, 属性的可见性域。
标记为 `@private` 的全局变量和函数，表示它们只能在当前文件中访问。
标记为 `@private` 的构造器，表示该类只能在当前文件或是其静态/普通成员中实例化；私有构造器的公共静态属性在当前文件的任何地方都可访问，通过 `instanceof` 操作符也可。
永远不要为 全局变量、函数、构造器加 `@protected` 标记。

```js
// 文件1
// AA_PrivateClass_ 和 AA_init_ 是全局的并且在同一个文件中所以能被访问

/**
 * @private
 * @constructor
 */
AA_PrivateClass_ = function() {
};

/** @private */
function AA_init_() {
  return new AA_PrivateClass_();
}

AA_init_();
```

标记为 `@private` 的属性，在当前文件中可访问它； 如果是类属性私有，"拥有"该属性的类的所有静态/普通成员也可访问，但它们不能被不同文件中的子类访问或覆盖。
标记为 `@protected` 的属性，在当前文件中可访问它，如果是类属性保护，那么"拥有"该属性的类及其子类中的所有静态/普通成员也可访问。
注意：这与 C++, Java 中的私有和保护不同，它们是在当前文件中，检查是否具有访问私有/保护属性的权限，有权限即可访问，而不是只能在同一个类或类层次上。而 C++ 中的私有属性不能被子类覆盖。(C++/Java 中的私有/保护是指作用域上的可访问性，在可访问性上的限制。JS 中是在限制在作用域上。PS：可见性是与作用域对应)

```js
// 文件1

/** @constructor */
AA_PublicClass = function() {
  /** @private */
  this.privateProp_ = 2;
  /** @protected */
  this.protectedProp = 4;
};
/** @private */
AA_PublicClass.staticPrivateProp_ = 1;
/** @protected */
AA_PublicClass.staticProtectedProp = 31;
/** @private */
AA_PublicClass.prototype.privateMethod_ = function() {};
/** @protected */
AA_PublicClass.prototype.protectedMethod = function() {};

// 文件2

/**
* @return {number} The number of ducks we've arranged in a row.
*/
AA_PublicClass.prototype.method = function() {
  // Legal accesses of these two properties.
  return this.privateProp_ + AA_PublicClass.staticPrivateProp_;
};

// File 3.

/**
* @constructor
* @extends {AA_PublicClass}
*/
AA_SubClass = function() {
  // 合法访问了一个保护的静态属性
  AA_PublicClass.staticProtectedProp = this.method();
};
goog.inherits(AA_SubClass, AA_PublicClass);

/**
* @return {number} The number of ducks we've arranged in a row.
*/
AA_SubClass.prototype.method = function() {
  // 合法访问了一个合法实例属性
  return this.protectedProp;
};
```
注意在Javascript中，一个类（如 `AA_PrivateClass_` ）和其构造函数类型是没有区别的。没办法确定一种类型是public而它的构造函数是private。（因为构造函数很容易重命名从而躲避隐私检查）。

##JavaScript类型
强烈建议你去使用编译器。
如果使用 JSDoc，那么尽量具体地，准确地根据它的规则来书写类型说明。我们支持的类型是基于 [EcmaScript 4规范](http://wiki.ecmascript.org/doku.php?id=spec:spec)。

###JavaScript类型语言
ES4提议中包含了一种描述 JavaScript 类型的规范语法，这里我们在 JSDoc 中采用其来描述函数参数和返回值的类型。
JSDoc 的类型语言, 按照 ES4 规范，也进行了适当改变，但编译器仍然支持旧语法。

###类型转换
在类型检测不准确的情况下，有可能需要添加类型的注释，并且把类型转换的表达式写在括号里，括号是必须的。如：

```js
/** @type {number} */ (x)
```

###可空、可选的参数和属性
JavaScript 是一种弱类型语言，明白可选，非空和未定义参数或属性之间的细微差别还是很重要的。
对象类型(引用类型)默认非空。注意：函数类型默认不能为空。除了字符串、 整型、布尔、undefined和null外，对象可以是任何类型：

```js
/**
* 传入值初始化的类
* @param {Object} value某个值
* @constructor
*/
function MyClass(value) {
  /**
   * Some value.
   * @type {Object}
   * @private
   */
  this.myValue_ = value;
}
```
告诉编译器 `myValue_` 属性为一对象或 null. 如果 `myValue_` 永远都不会为 null, 就应该如下声明：

```js
/**
* 传入非null值初始化的类
* @param {!Object} value某个值
* @constructor
*/
function MyClass(value) {
  /**
   * Some value.
   * @type {!Object}
   * @private
   */
  this.myValue_ = value;
}
```
这样，当编译器在代码中碰到 MyClass 为 null 时，就会给出警告。
函数的可选参数可能在运行时没有定义，所以如果他们又被赋给类属性，需要声明成：

```js
/**
* 传入可选值初始化的类
* @param {Object=} opt_value某个值（可选）
* @constructor
*/
function MyClass(opt_value) {
  /**
   * Some value.
   * @type {Object|undefined}
   * @private
   */
  this.myValue_ = opt_value;
}
```
这告诉编译器 myValue_ 可能是一个对象，或 null ，或 undefined 。
注意: 可选参数`opt_value`被声明成`{Object=}`，而不是`{Object|undefined}`。这是因为可选参数可能是undefined。虽然直接写undefined也并无害处，但鉴于可阅读性还是写成上述的样子。
最后，属性的可为空和可选并不矛盾，下面的四种声明各不相同：

```js
/**
* 接受四个参数，两个可为空，两个可选
* @param {!Object} nonNull 必不为null
* @param {Object} mayBeNull 可为null
* @param {!Object=} opt_nonNull 可选但必不为null
* @param {Object=} opt_mayBeNull 可选可为null
*/
function strangeButTrue(nonNull, mayBeNull, opt_nonNull, opt_mayBeNull) {
  // ...
};
```

###类型定义
有时类型可以变得复杂。一个函数，它接受一个元素的内容可能看起来像：

```js
/**
* @param {string} tagName
* @param {(string|Element|Text|Array.<Element>|Array.<Text>)} contents
* @return {!Element}
*/
goog.createElement = function(tagName, contents) {
  ...
};
```
你可以定义带 @typedef 标记的常用类型表达式。例如：

```js
/** @typedef {(string|Element|Text|Array.<Element>|Array.<Text>)} */
goog.ElementContent;

/**
* @param {string} tagName
* @param {goog.ElementContent} contents
* @return {!Element}
*/
goog.createElement = function(tagName, contents) {
...
};
```

###类型模板
编译器对模板类型提供有限支持。它只能从字面上通过 this 参数的类型和 this 参数是否丢失推断匿名函数的 this 类型。

```js
/**
* @param {function(this:T, ...)} fn
* @param {T} thisObj
* @param {...*} var_args
* @template T
*/
goog.bind = function(fn, thisObj, var_args) {
...
};
//可能出现属性丢失警告
goog.bind(function() { this.someProperty; }, new SomeClass());
//出现this未定义警告
goog.bind(function() { this.someProperty; });
```

##注释
使用JSDoc。
使用 [c++的注释风格](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml#Comments)。 所有的文件、类、方法和属性都应该用合适的 [JSDoc](https://code.google.com/p/jsdoc-toolkit/) 的 标签 和 类型 注释。除了直观的方法名称和参数名称外，方法的描述、方法的参数以及方法的返回值也要包含进去。
行内注释应该使用 // 的形式。
为了避免出现语句片段，要使用正确的大写单词开头，并使用正确的标点符号作为结束。
建议使用DocBlockr插件，自动插入，然后手动完善。

##编译
建议您去使用 JS 编译器，如[Closure Compiler](https://developers.google.com/closure/compiler/?csw=1)。

##Tips and Tricks

###True和False布尔表达式
下边的布尔表达式都返回false：

- null
- undefined
- ''空字符串
- 数字0

但小心下面的, 可都返回true：

- 字符串"0"
- []空数组
- {}空对象

下面这段比较糟糕的代码：

```js
while (x != null) {}
```
你可以直接写成下面的形式(只要你希望x不是 0、空字符串、false)：

```js
while (x) {}
```
如果你想检查字符串是否为null或空：

```js
if (y != null && y != '') {}
```
但这样更好：

```js
if (y) {}
```
注意：还有很多不直观的关于布尔表达式的例子：

```js
Boolean('0') == true
'0' != true

0 != null
0 == []
0 == false

Boolean(null) == false
null != true
null != false

Boolean(undefined) == false
undefined != true
undefined != false

Boolean([]) == true
[] != true
[] == false

Boolean({}) == true
{} != true
{} != false
```

###条件（三元）操作符x?y:z
三元操作符用于替代下面的代码：

```js
if (val != 0) {
  return foo();
} else {
  return bar();
}
```
可以这样：

```js
return val ? foo() : bar();
```
生成HTML代码时很有用的：

```js
var html = '<input type="checkbox"' +
    (isChecked ? ' checked' : '') +
    (isEnabled ? '' : ' disabled') +
    ' name="foo">';
```

###&& 和 ||
二元布尔操作符是可短路的，只有在必要时才会计算到最后一项。
"||" 被称作为 'default' 操作符，因为可以这样：

```js
/** @param {*=} opt_win */
function foo(opt_win) {
  var win;
  if (opt_win) {
    win = opt_win;
  } else {
    win = window;
  }
  // ...
}
```
你可以这样简化：

```js
/** @param {*=} opt_win */
function foo(opt_win) {
  var win = opt_win || window;
  // ...
}
```
"&&" 也可以用来缩减代码。例如：

```js
if (node) {
  if (node.kids) {
    if (node.kids[index]) {
      foo(node.kids[index]);
    }
  }
}
```
可以这样：

```js
if (node && node.kids && node.kids[index]) {
  foo(node.kids[index]);
}
```
或这样：

```js
var kid = node && node.kids && node.kids[index];
  if (kid) {
    foo(kid);
}
```
这样就有点过头了：

```js
node && node.kids && node.kids[index] && foo(node.kids[index]);
```

###遍历Node list
Node lists是通过给节点迭代器加一个过滤器来实现的。这表示获取他的属性，如length的时间复杂度为O(n)，通过length来遍历整个列表需要O(n^2)。

```js
var paragraphs = document.getElementsByTagName('p');
for (var i = 0; i < paragraphs.length; i++) {
  doSomething(paragraphs[i]);
}
```
这样写更好：

```js
var paragraphs = document.getElementsByTagName('p');
for (var i = 0, paragraph; paragraph = paragraphs[i]; i++) {
  doSomething(paragraph);
}
```
这种方法对所有的collections和数组(只要数组不包含false值) 都适用。
在上面的例子中，也可以通过firstChild和nextSibling来遍历子节点。

```js
var parentNode = document.getElementById('foo');
for (var child = parentNode.firstChild; child; child = child.nextSibling) {
  doSomething(child);
}
```
