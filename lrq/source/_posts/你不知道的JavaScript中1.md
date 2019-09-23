---
title: 你不知道的JavaScript(中卷)1
tag: javascript
---

## 第一部分 类型和语法

### 第一章 类型

这些工具库之所以功能强大，正是因为它们的开发者理解这门语言的本质和优点，并将它们运用到了极致。
JavaScript 有 7 中内置类型：

- 空值（null）
- 未定义（undefined）
- 布尔值（boolean）
- 数字（number）
- 字符串（string）
- 对象（object）
- 符号（symbol ES6 新增）
  **除了对象之外，其他类型被称为基本类型**
  使用符合条件来检验 null 值的类型:

```
(!a && typeof a === "object");  //true
```

还有一种情况，`typeof function a(){ /*...*/} === "function"; //true` 这么看来 function 也是 JavaScript 的内置类型之一，但实际上它是 object 的一个子类型。

#### 值和类型

JavaScript 中的变量是没有类型的，只有值才有。变量可以随时持有任何类型的值。JavaScript 不做“类型强制”，即一个变量可以现在被赋值为字符串类型值，随后又被赋值为数字类型的值。
polyfill:衬垫代码，用来为旧浏览器提供它没有原生支持的较新的功能。
与 undeclared 变量不同，访问不存在的对象属性，甚至是在全局对象 windos 上，也不会产生 ReferenceError 错误。

### 第二章 值

和其他强类型语言不同，在 JavaScript 中，数组可以容纳任何类型的值，可以是字符串、数字、对象、甚至是其他数组。使用 delete 运算符可以将单元从数组中删除，但是，单元删除后，数组的 length 属性不会发生变化。
数组通过数字进行索引，但它们也是对象，所以也可以包含字符串键值和属性（但这些并不计算在数组长度内），但是有个问题需要注意，如果字符串键值能够被强制类型转换为十进制数字的话，它就会被当作数字索引来处理。

```
var a = [];
a[0] = 1;
a["test"] = 1;
a.length;   //1

var b = [];
b["13"] = 1;
b.length;   //14
```

JavaScript 中字符串是不可变的，而数组是可变的。字符串不可变是指字符串的成员函数不会改变其原始值，而是创建并返回一个新的字符串。而数组的成员函数都是在其原始值上进行操作。
将字符串反转的方法：先将字符串转换为数组，待处理完后再将结果转换回字符串。

```
var str_reverse = str
                .split("")  //将值转换为字符串数组
                .reverse()  //将数组中的值进行反转
                .join("");  将数组中的字符拼接为字符串
```

JavaScript 只有一种数值类型，number(数字),包括"整数"和带小数的十进制数。
二进制浮点数最大的问题是会出现这种情况: `0.1+0.2 === 0.3 //false`，因为二进制浮点数中的 0.1 和 0.2 并不是很精确，相加结果是 0.30000000000004。
有时 JavaScript 程序需要处理一些比较大的数字，如数据库中的 64 位 ID 等。由于 JavaScript 的数字类型无法精确呈现 64 位数值，所以必须将它们保存(转换)为字符串。

可以使用`Number.isSafeInter(..)`来检测一个数是不是整数。
a|0 可以将变量中的数值转换为 32 位有符号整数，因为数位运算符|只适用于 32 位整数(它只关心 32 位以内的数，其他的数位将被忽略)，因此和 0 做|运算即可截取 a 中的 32 位数位。

**void 运算符**
按照惯例我们使用`void 0`来获得 undefined，使用其他表达式也可以，`void 0`、`void true`、`undefined`没有本质的区别。

**-0**
`var a = -1/3;` `// -0`
有些应用程序中的数据需要以级数的形式来表示(比如动画帧的移动速度)，数字的符号位用来代表其他数据，(比如移动的方向)。如果一个值为 0 的变量失去了他的符号位，它的方向信息就会丢失。

**特殊等式**
ES6 加入了一个工具方法`Object.is(..)`，可以判断两个值是否绝对相等。

```
var a = 2 / "foo";
var b = -3 / 0;

Object.is(a, NaN);  //true
Object.is(b, -0);   //true
Object.is(b, 0);    //false
```

**值和引用**

```
var a = 2;
var b = a;  //b是a值的一个副本
b++;
a;  //2
b;  //3

var c = [1, 2, 3];
var d = c;  //d是[1, 2, 3] 的一个引用
d.push(4);
c;  //[1, 2, 3, 4]
d;  //[1, 2, 3, 4]
```

简单值，即标量基本类型，总是通过值复制的方式来赋值/传递的。包括 null、undefined、字符串、数字、布尔值和 ES6 的 symbol。
复合值——对象(包括数组和封装对象)和函数，则总是通过引用复制的方式来赋值/传递的。
**清空数组的一个快捷方法**

```
x = [1, 2, 3, 4];
x.length = 0;
x;  //[]
```

这样，数组就被清空了。
如果通过值复制的方式来传递复合值(如数组)，就需要为其创建一个副本，这样传递的值就不是原始值。如:`foo( a.slice() )`，slice()不带参数会返回当前数组的一个浅副本。

### 第 3 章 原生函数

**内部属性 class**
所有 typeof 返回值为"object"的对象，都包含一个内部属性，[[Class]]，(我们可以把它看一个内部的分类，而非传统的面向对象意义上的类)，这个属性无法直接访问，一般通过`Object.prototype.toString(..)`来查看。如：

```
Object.prototype.toString( [1,2,3] );   //"[object Array]"
Object.prototype.toString( /regex-literal/i );   //"[object RegExp]"
```

如数组的内部[[Class]]属性值是"Array",正则表达式的是"RegExp"。基本类型的值，如 string，number，boolean，会被各自的封装对象自动装箱。由于基本类型值没有.length 和/toString() 这样的属性和方法，需要通过封装对象才能访问，此时 JavaScript 会自动为基本类型值包装一个封装对象。
想要得到封装后的对象的值，可以使用 valueOf()方法。
Array 构造函数只带一个数字参数的时候，该参数会被作为数组的预设长度(length)，而非只充当数组中的一个数组。
JavaScript 为基本数据类型提供了封装对象，成为原生函数，(如 String、Number、Boolean)，他们为基本数据类型值提供了该子类型所特有的方法和属性。(如`String.prototype.trim()` 、`Array.prototype.concat()`等。) 对于简单标量基本类型值，比如"abc"，如果要访问它的 length 属性或者`String.prototype`方法，JavaScript 引擎会自动对该值进行封装。

### 第 4 章 类型强制转换

#### 值类型转换

将值从一种类型转换为另一种类型通常称为类型转换，这是显式的情况，隐式的情况被称为“强制类型转换”。JavaScript 中的强制类型转换总是返回标量基本类型，如字符串、数字和布尔值，不会返回对象和函数。类型转换发生在静态类型语言的编译阶段，而强制类型转换则发生在动态类型语言的运行时。
不安全的 JSON 值：undefined、function、symbol 和包含循环引用(对象之间互相引用，形成一个无限循环)的对象。
如果要对含有非法 JSON 值的对象做字符串化，或者对象中的某些值无法被序列化时，就需要定义 toJSON()方法，来返回一个安全的 JSON 值。

```js
var o = {};
var a = {
  b: 42,
  c: o,
  d: function() {}
};
o.e = a; //在a中创建一个循环引用
//  JSON.stringify( a );    //循环引用会在这里产生错误

a.toJSON = function() {
  //自定义的JSON序列化
  return { b: this.b };
};

JSON.stringify(a); // "{ "b" : 42}"
```

toJSON()并不是返回一个 JSON 字符串化后的值，其实 toJSON()返回的应该是一个适当的值，可以是任何类型，然后由 JSON.stringify() 对其进行字符串化。
toJSON()应该返回一个能够被字符串化的安全的 JSON 值，而不是返回一个 JSON 字符串。
还可以向 JSON.stringify(..) 传递一个可选参数 replacer，它可以是数组或者函数，用来指定对象序列化过程中哪些属性应该被处理，哪些应该被排除，和 toJSON()很像。

```
var a = {
    b: 42,
    c: "42";
    d: [1,2,3]
};
JSON.stringify( a, ["b", "c"] );    // "{ "b": 42, "c": "42"}"

JSON.stringify( a, function(k,v) {
    if( k!== "c") return v;
});     // "{ "b":42, "d":[1,2,3]}"
```

JSON.string 还有一个可选参数 space，用来指定输出的缩进格式。space 为正整数时是指定每一级缩进的字符数，它还可以是字符串，此时最前面的字符被用于每一级的缩进。

```js
var a = {
    b: 42,
    c: "42",
    d: [1,2,3]
}

JSON.stringify( a, null, "-----");
输出结果为
{
-----"b": 42,
-----"c": "42",
-----"d": [
----------1,
----------2,
----------3
-----]
}
```

**有关 JSON.stringify()两个要点：**

1. 字符串、数字、布尔值和 null 的 JSON.stringify()规则与 toString()基本相同。
2. 如果传递给 JSON.stringify() 的对象中定义了 toJSON()方法，那么该方法会在字符串化前调用，以便将对象转换为安全的 JSON 值。

**数字 number**
从 ES5 开始，使用`Object.create(null)`创建的对象[[Prototype]]属性为 null，并且没有 valueOf()方法和 toString()方法，因此无法进行强制类型转换。

```
var a = {
    valueOf: function() {
        return "42";
    }
};

var b = {
    toString: function() {
        return "42";
    }
};

var c = [4,2];
c.toString = function() {
    return this.join( "" );
};
Number( a );    //42
Number( b );    //42
Number( c );    //42
Number( "" );    //0
Number( [] );    //0
Number( ["abc"] );    //NaN
```

**布尔值**
JavaScript 中的值可以分为两类：

- 可以被强制类型转换为 false 的值
- 其他(被强制类型转换为 true 的值)

会被转化为假值的有:

- undefined
- null
- false
- +0 、-0 和 NaN
- ""

`Boolean( document.all )` 结果是 false

**字符串和数字之间的显式转换**

```js
var a = 12;
var b = a.toString();

var c = "3.14";
var d = +c;
```

a.toString() 是显式的，不过其中涉及隐式转换。直接输入 12.toString()会报错，所以 JavaScript 引擎会自动为 12 创建一个封装对象，然后对该对象调用 toString(),这里显式转换中含有隐式转换。`+c`操作是 + 运算符的一元形式(即只有一个操作数)。+运算符显式的将 c 转换为数字，而非数字加法运算。

**获得时间戳的方法**

```
var a = +new Date();
var a = Date.now();
var a = (new Date()).getTime();
```

**~操作符**
~相当于补码，`~x = -(x+1)`

```js
var a = "Hello World";
if (a.indexOf("lo") >= 0) {
  //true
  //找到匹配
}
if (a.indexOf("lo") != -1) {
  //true
  //找到匹配
}
if (a.indexOf("ol") < 0) {
  //true
  //没有找到匹配
}
```

`>=0` 和`==-1`这样的写法不是很好，成为“抽象渗漏”，意思是在代码中暴露了底层的实现细节，这里是指用-1 作为失败时的返回值，这些细节应该被屏蔽掉。使用~就可以和 indexOf()一起，将结果强制类型转换为真/假值。如果 indexOf() 返回-1，~将其转换为假值 0，其他情况一律转换为真值。
`~~`操作符可以截取数字值的小数部分。~~中的第一个`~`执行 ToInt32 并翻转字位，然后第二个`~`再进行一次字位翻转，即将所有字位翻转回原值，最后得到的仍然是 ToInt32 的结果。对负数的小数处理会将小数点后的去掉。

#### 显式解析数字字符串

```
var a = "42";
var b = "42px";

Number( a );    //42
parseInt( a );  //42
Number( b );    //NaN
parseInt( b );  //42
```

解析字符串中的数字和将字符串强制类型转换为数字的返回结果都是数字。解析允许字符串中含有非数字字符，解析按照从左到右的顺序，如果遇到非数字字符就停止。而转换不允许出现非数字字符，否则会失败并返回 NaN。

#### 显式转换为布尔值

一元运算符`!`显式的将值强制类型转换为布尔值，但是值会翻转。所以最常用的显式强制类型转换为布尔值最的方法是`!!`，第二个！会将结果翻转为原值。

#### 隐式强制类型转化

```
var a = [1, 2];
var b = [3, 4];
a + b;      //"1,23,4"
```

a 和 b 都不是字符串，但是它们都被强制转换为字符串然后进行拼接。

```
var a = {
    valueOf: function() { return 42; },
    toString: function() { return 4; }
};
a + "";     //"42"
String( a );    //"4"
```

a+""会对 a 调用 valueOf 方法，然后通过 toString()抽象操作将返回值转换为字符串。而 String(a) 则是直接调用 toString()方法。

**字符串强制转换为数字的情况**

```
var a = "3.14";
var b = a - 0;
b;  //3.14
```

`-`是减法运算符，因此 a-0 会将 a 强制类型转换为数字。也可以使用 a \* 1 和 a / 1 ，因为这两个运算符也只适用于数字。

```
var a = [3];
var b = [1];

a - b;  //2
```

为了执行减法运算，a 和 b 都需要被转换为数字，它们首先被转换为字符串(通过 toString()),然后再转换为数字。

**布尔值到数字的转换**

```
//显式类型转换
function onlyOne() {
    var sum = 0;
    for(var i=0; i<arguments.length; i++) {
        sum += Number( !!arguments[i] );
    }
    return sum === 1;
}

//隐式类型转换
function onlyOne() {
    var sum = 0;
    for(var i=0; i<arguments.length; i++) {
        if( arguments[i] ) {
            sum += arguments[i];
        }
    }
    return sum === 1;
}
```

`!!arguments[i]`首先将参数转换为 true 或者 false，转换为布尔值之后，再通过 Number(..)显式强制类型转换为 0 或 1。无论是显式还是隐式，想要实现`onlyTwo()`、或者`onlyFour()`,只需要把判断条件改为`sum===2`、或者 `sum === 4`即可。

**逻辑运算的 || 和 &&**
将它们称为“逻辑运算符”不太准确，称他们为“选择器运算符”或者“操作数选择器运算符”更恰当些。和其他语言不同，在 JavaScript 中它们返回的并不是布尔值，而是两个操作数中的一个。

```
var a = 42;
var b = "abc";
var c = null;

a||b;       //42
a&&b;       //"abc"
c||b;       //"abc"
c&&b;       //null
```

在 C 和 PHP 中，上例的结果是 true 或者是 false，在 JavaScript(以及 Python 和 Ruby)中却是某个操作数的值。
||和&&首先会对第一个操作数进行判断，如果不是布尔值，就先进行 toBoolean 强制类型转换，然后再执行条件判断。
**对于||来说**：如果条件判断结果为 true，就返回第一个操作数的值，如果为 false，就返回第二个操作数的值。
**对于&&来说**：如果第一个判断结果为 true，则返回第二个操作数。如果第一个判断结果为 false，就直接返回第一个操作数的值。
从另一个角度看：

`a || b;` //大概就相当于
`a ? a : b`

`a && b;` //大概就相当于
`a ? b : a`

**守护运算符**：

```js
if (a) {
  foo();
}
//等价于
a && foo();
```

foo()只有在条件判断 a 通过时才会被调用。如果条件判断未通过， a && foo()就会悄然终止，也叫做（短路）。

**宽松相等和严格相等**
常见的误区是"`==`检查值是否相等，`===`检查值和类型是否相等"，听起来蛮有道理，然而不够准确。正确的解释应该是"`==`允许在相等中进行强制类型转换，而`===`不允许"。
==`==`和`===`都会检查操作数的类型，区别在于操作数类型不同时，它们的处理方式不同。==

**字符串和数字之间的相等比较**：
是数字转化为字符串还是字符串转化为数字?ES5 的规范这样定义：
(1)如果 Type(x)是数字，Type(y)是字符串，则返回 x==ToNumber(y) 的结果。
(2)如果 Type(x)是字符串，Type(y)是数字，则返回 ToNumber(x)==y 的结果。

**其他类型和布尔类型之间的相等比较**：

```
var a = "42";
var b = true;
a == b; //false
a == false; //false  这个字符串既不==true 也不==false

a = "1";
a == b; //true
```

ES5 中的规范：
(1)如果 Type(x) 是布尔类型，则返回 ToNumber(x) == y 的结果。
(2)如果 Type(y) 是布尔类型，则返回 x == ToNumber(y) 的结果。
引擎会把布尔类型的变量强制转换为数字值，即转换为 0 或 1，然后再与另一个变量进行比较。
尽量避免使用 ==true 和 ==false

```js
var a = "42";
if (a == true) {
  //这样用条件不成立
  //..
}
if (a === true) {
  //这样用条件也不成立
  //..
}
if (a) {
} //这样的显式用法没问题

if (!!a) {
} //这样的显式用法更好

if (Boolean(a)) {
} //这样的显式用法也很好
```

**null 和 undefined 的比较**：
(1)如果 x 为 null，y 为 undefined，则结果为 true。
(2)如果 x 为 undefined，y 为 null，则结果为 true。

```
var a = null;
var b;

a == b;     //true
a == null;  //true
b == null;  //true

a == false; //false
b == false; //false
a == "";    //false
b == "";    //false
a == 0;      //false
b == 0;     //false
```

**对象和非对象之间的相等比较**：
关于对象(对象、函数、数组)和标量基本类型(字符串、数字、布尔值)之间的相等比较，ES5 有如下规范：
(1)如果 Type(x)是字符串或数字，Type(y)是对象，则返回 x == ToPrimitive(y)的结果。
(2)如果 Type(x) 是对象，Type(y)是字符串或数字，则返回 ToPromitive(x) == y 的结果。

```
var a = 42;
var b = [42];

a==b;   //true
```

[42]首先调用 ToPromitive 抽象操作，返回"42",变成`"42"==42`，`然后又变成42==42`，最后二者相等。

**少见的情况**：

```js
var i = 2;
Number.prototype.valueOf = function() {
  return i++;
};

var a = new Number(42); //无论初初始值给的多少，返回的都一样

//这种情况可能发生吗？？
if (a == 2 && a == 3) {
  console.log("还真发生了！");
}
```

**极端情况**

```
[] == ![];  //true
```

根据 ToBoolean 规则，它会进行布尔值的显式强制转换(同时反转奇偶校验位)。所以`[]==![]`变成了`[]==false`。

一些奇怪的地方

```
"0" == false;   //true
false == 0;     //true
false == "";    //true
false == [];    //true
"" == 0;        //true
"" == [];       //true
0 == [];        //true
```

**安全运用隐式强制类型转换**

- 如果两边的值中有 true 或者 false，千万不要用 false
- 如果两边的值有[]、""、或者 0，尽量不要使用==。

这时最好用===来避免不经意的强制类型转换。这两个原则可以让我们避开几乎所有强制类型转换的坑。这种情况下强制类型转换越显式越好。所以选择`==`还是`===`取决于是否允许在相等比较中发生强制类型转换。

**抽象关系比较**：

```js
var a = { b: 42 };
var b = { b: 42 };
a < b; //fasle
a == b; //false
a > b; //false
a <= b; //true
a >= b; //true
```

看了上面的代码是不是觉得 R 了狗了。。a==b 进行比较，因为两个都是对象，需要引用同一个对象才会判断为 true。判断 a<b 时，因为此时调用方法将两边转换为字符串，a 结果是[object Object],b 也是[object Object],所以按照字母顺序 a<b 并不成立。但是 a<=b 和 a>=b 怎么就成立了？**因为根据规范，a<=b 被处理为 b<a ，然后将结果翻转。因为 b<a 的结果是 false，所以 a<=b 的结果是 true**，我们可能认为<=是“小于或者等于” 的意思，实际上 JavaScript 中<=是“不大于”的意思。

### 第五章 语法

`++a++`会产生 ReferenceError 错误，因为运算符需要将产生的副作用赋值给一个变量。它首先执行 a++(根据运算符优先级)，返回 42，然后执行`++42`，这时会产生 ReferenceError 错误，因为`++`无法直接在 42 这样的值上产生副作用。

组合赋值运算符，如`a = b += 2`首先执行`b+=2`,然后结果再被赋值给 a。

JavaScript 通过标签跳转能够实现 goto 的部分功能，continue 和 break 语句都可以带一个标签，因此能够像 goto 那样进行跳转。

```js
foo: for (var i = 0; i < 4; i++) {
  for (var j = 0; j < 4; j++) {
    if (j == i) {
      continue foo;
    }

    if ((j * i) % 2 == 1) {
      continue;
    }
    console.log(i, j);
  }
}
```

continue foo 并不是指“跳转到标签 foo 所在位置继续执行”而是“执行 foo 循环的下一轮循环”。所以这里的 foo 并非 goto

---

**代码块** ：
有一个坑，涉及强制类型转换。

```
[] + {};    //"[object Object]"
{} + [];    // 0
```

表面上看，+运算符根据第一个操作数([]或{})的不同会产生不同的结果，实际则不是。
第一行代码中，{}出现在+运算符表达式中，因此它被当做一个值(空对象)来处理。[]会被强制转换为"",而{}会被强制转换为 "[object Object]".
第二行代码中，{}被当做一个独立的空代码块(不执行任何操作)。代码块结尾不需要分号。最后 +[]将 []显式强制类型转换为 0。

---

**对象解构**

```
function getDate() {
    return {
        a: 42,
        b: "foo"
    };
}

var { a, b} = getDate();
console.log( a , b);
```

{ a, b} = getDate()就是 ES6 中的解构赋值。相当于如下代码：

```
var res = getDate();
var a = res.a;
var b = res.b;
```

此外，{..}还可以用作函数命名参数的对象解构，方便隐式地运用对象属性赋值:

```js
function foo({ a, b, c }) {
  //之前可能是传进来一个对象obj
  //然后 var a = obj.a; var b = obj.b; var c = obj.c;
  console.log(a, b, c);
}
foo({
  c: [1, 2, 3],
  a: 42,
  b: "foo"
}); // 42 "foo" [1,2,3]
```

---

#### 运算符优先级

用`,`连接一系列语句的时候，它的优先级最低，其他操作数的优先级都比它高。
`if(str && (matches = str.match( /[aeiou]/g) ) ){ .. }`，这里对赋值语句加上括号是十分必要的，因为&&运算符的优先级高于=，如果没有()对其中的表达式进行绑定的话，就会执行为 `(str && matches)=str.match..`这样就会报错，因为(str && matches)的结果不是一个变量，而是一个 undefined 的值，出现在=左边会出错！

**&&的优先级比||高**
(a && b || c)执行的是 (a && b) || c 还是 a && (b || c)?

```js
(false && true) || true; //true
false && (true || true); //false

(false && true) || true; //true
```

顺序不同结果还是有区别的，结果表示&&的优先级比||的优先级要高。

**短路**
对于&&和||来说，如果从左边的操作数得到结果，就可以忽略右边的操作数。我们将这种现象称为“短路”。(即执行最短路径)
“短路很方便也很常用”，如：

```js
function doSomething(opts) {
  if (opts && opts.cool) {
    //..
  }
}
```

opts && opts.cool 中的 opts 条件判断如同一道安全保护，因为如果 opts 未赋值，(或者不是一个对象)，表达式 opts.cool 就会出错。通过使用短路特性，opts 条件判断未通过时，opts.cool 就不会执行，就不会产生错误！~

```js
function doSomething(opts) {
  if (opts.cache || primeCache()) {
    //..
  }
}
```

这里首先判断 opts.cache 是否存在，如果存在就不需要在调用 primeCache() 函数，这样可以避免执行不必要的代码。

**&&和||和?:的优先级**:
a && b || c ? c || b ? a : c && b : a
其中的 ?: 运算符的优先级比&&和||的高还是低呢？
结果应该是这样: `(a && b || c) ? (c || b) ? a : (c && b) : a`

---

**右关联**
a ? b : c ? d : e; 因为`?:`是右关联，所以它的组合顺序是 `a ? b : (c ? d : e)`,有些情况下可能返回结果是相同的，但是过程却有着微妙的变化。
另一个右关联的组合是`=`运算符。下面是一个超级叼的表达式:

```js
var a = 42;
var b = "foo";
var c = false;

var d = (a && b) || c ? (c || b ? a : c && b) : a;
d; //42
```

来分析一下，&&的优先级最高，然后是||的优先级，最后是?:,所以上面的表达式可以分为这样`((a && b) || c) ? ((c || b) ? a :(c && b)) : a`,这样结果就很清晰了。慢慢推导，结果是 42.

**函数参数**

```js
function foo(a = 42, b = a + 1) {
  console.log(arguments.length, a, b, arguments[0], arguments[1]);
}

foo(); //0 42 43 undefined undefined
foo(10); // 1 10 11 10 undefined
foo(10, undefined); //2 10 11 10 undefined
foo(10, null); //2 10 null 10 null
```

#### try...finally

finally 中的代码总是会在 try 之后执行，如果有 catch 的话，则在 catch 之后执行。也可以将 finally 中的代码看做一个回调函数，即无论出现什么情况最后一定会被调用。
如果 finally 中抛出了异常(无论是有意还是无意)，函数就会在此终止。如果此前 try 中已经有 return 设置了返回值，则该值会被丢弃：

```
function foo() {
    try {
        return 32;
    }
    finally {
        throws "stop!";
    }
    console.log("永远执行不到这里");
}
console.log( foo() );
// Uncaught Exception: stop!
```

此外，finally 中的 return 会覆盖 try 和 catch 中 return 中的返回值：

```js
function foo() {
  try {
    return 42;
  } finally {
    //没有返回语句，不会覆盖
  }
}

function bar() {
  try {
    return 42;
  } finally {
    return "hello"; //会覆盖掉try中return的值
  }
}

foo(); //42
bar(); //"hello"
```

#### switch

```
switch (a) {
    case 2:
        //dosomething();
        break;
    case 3:
        //dosomething2();
        break;
    default:
        //默认执行代码
        break;
}
```

switch,刚开始确实觉得没什么好做比较的，但是看到两个小点，记一下。一个是 switch 的那个表达式，上式是 a，和 case 中每一项比较，都是`===`来进行比较，严格相等的，不能进行隐式强制转换的。还有一个看到一个可以用到强制转换的写法，感觉挺厉害的。

```js
var a = "42";
switch (true) {
  case a == 10:
    console.log("是10还是'10'");
    break;
  case a == 42:
    console.log("是42还是'42'");
    break;
  default:
    break;
}
//"是42还是'42'"
```

赶脚用这种方式还挺巧妙的，能用到隐式转换。

{ }在不同情况下的意思不尽相同，可以是语句块、对象常量、解构赋值(ES6)或者命名函数参数(ES6).

JavaScript 语言本身有一个统一的标准，在所有浏览器/引擎中的实现也是可靠的。但是 JavaScript 很少独立运行。通常运行环境中还有第三方代码，有时代码甚至会运行在浏览器之外的引擎环境中。
