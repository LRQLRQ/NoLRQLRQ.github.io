---
title: 学习JavaScript
tags: javascript 
---


# 1.这是我第一天学习JavaScript #

*斜体*
**粗体**
~~好吗？~~
>这是引用
sss


```
print("hello!");
return 0;
```

***

##1.为什么学习 JavaScript?##
JavaScript web 开发人员必须学习的 3 门语言中的一门：

HTML 定义了网页的内容
CSS 描述了网页的布局
JavaScript 网页的行为

##2.学习中的要点##




alert() 函数在 JavaScript 中并不常用，但它对于代码测试非常方便。

您会经常看到 document.getElementById("some id")。这个方法是 HTML DOM 中定义的。
DOM (Document Object Model)（文档对象模型）是用于访问 HTML 元素的正式 W3C 标准。



###JavaScript 用法###
HTML 中的脚本必须位于 <script> 与 </script> 标签之间。
脚本可被放置在 HTML 页面的 <body> 和 <head> 部分中。



那些老旧的实例可能会在 script 标签中使用 type="text/javascript"。现在已经不必这样做了。JavaScript 是所有现代浏览器以及 HTML5 中的默认脚本语言

### 在 head 或者 body 的JavaScript ###
您可以在 HTML 文档中放入不限数量的脚本。
脚本可位于 HTML 的 body 或 head 部分中，或者同时存在于两个部分中。

通常的做法是把函数放入 head 部分中，或者放在页面底部。这样就可以把它们安置到同一处位置，不会干扰页面的内容。

###外部的 JavaScript###
也可以把脚本保存到外部文件中。外部文件通常包含被多个网页使用的代码。

外部 JavaScript 文件的文件扩展名是 .js。

如需使用外部文件，请在 script 标签的 "src" 属性中设置该 .js 文件：

##JavaScript 输出##
###JavaScript 没有任何打印或者输出的函数。###

JavaScript 可以通过不同的方式来输出数据：

1. 使用 window.alert() 弹出警告框。
2. 使用 document.write() 方法将内容写到 HTML 文档中。
3. 使用 innerHTML 写入到 HTML 元素。
4. 使用 console.log() 写入到浏览器的控制台。

2中的操作 HTML 元素
如需从 JavaScript 访问某个 HTML 元素，您可以使用 document.getElementById(id) 方法。

请使用 "id" 属性来标识 HTML 元素，并 innerHTML 来获取或插入元素内容：


请使用 document.write() 仅仅向文档输出写内容。
如果在文档已完成加载后执行 document.write，整个 HTML 页面将被覆盖

###JavaScript 注释###
不是所有的 JavaScript 语句都是"命令"。双斜杠 // 后的内容将会被浏览器忽略：

###JavaScript 语句###
JavaScript 语句是发给浏览器的命令。

这些命令的作用是告诉浏览器要做的事情。

###重新声明 JavaScript 变量###
如果重新声明 JavaScript 变量，该变量的值不会丢失：

在以下两条语句执行后，变量 carname 的值依然是 "Volvo"
var carname="Volvo";        
var carname;

#JavaScript变量学习图#
![avatar](https://7n.w3cschool.cn/attachments/image/20160809/1470709822216279.gif)

###JavaScript 对象###
对象由花括号分隔。在括号内部，对象的属性以名称和值对的形式 (name : value) 来定义。属性由逗号分隔：

    var person={firstname:"John", lastname:"Doe", id:5566};
 等价于
```javascript
 var person={
    firstname : "John",
    lastname  : "Doe",
    id        :  5566
 };
```

对象属性有两种寻址方式：
1. name=person.lastname;
2. name=person["lastname"];

###提示：JavaScript具有隐含的全局概念，意味着你不声明的任何变量都会成为一个全局对象属性。###

#JavaScript数据类型学习脑图：#
![avatar](https://7n.w3cschool.cn/attachments/image/20160809/1470710140220491.gif)


#JavaScript函数学习脑图#
![avatar](https://7n.w3cschool.cn/attachments/image/20160809/1470709911288582.gif)




##JavaScript 全局变量##

变量在函数外定义，即为全局变量。
全局变量有 全局作用域: 网页中所有脚本和函数均可使用。

如果变量在函数内没有声明（没有使用 var 关键字），该变量为全局变量。
以下实例中 carName 在函数内，但是为全局变量。
// 此处可调用 carName 变量 

function myFunction() { 
    carName = "Volvo"; 
    // 此处可调用 carName 变量 
}

##HTML 事件##
HTML 事件可以是浏览器行为，也可以是用户行为。

HTML 网页中的每个元素都可以产生某些可以触发 JavaScript 函数的事件。

以下是 HTML 事件的实例：

1. HTML 页面完成加载
2. HTML input 字段改变时
3. HTML 按钮被点击
通常，当事件发生时，你可以做些事情。

在事件触发时 JavaScript 可以执行一些代码。


常见的HTML事件
下面是一些常见的HTML事件的列表:

事件----------------描述
onchange---------HTML 元素改变
onclick	------------用户点击 HTML 元素
onmouseover-----用户在一个HTML元素上移动鼠标
onmouseout------用户从一个HTML元素上移开鼠标
onkeydown-------用户按下键盘按键
onload------------浏览器已完成页面的加载

#JavaScript运算符学习脑图#
![avatar](https://7n.w3cschool.cn/attachments/image/20170921/1505989160659350.gif)


#JavaScript流程语句学习脑图#

![avatar](https://7n.w3cschool.cn/attachments/image/20160809/1470710031660821.gif)

##Throw 语句##
throw 语句允许我们创建自定义错误。

正确的技术术语是：创建或抛出异常（exception）。
如果把 throw 与 try 和 catch 一起使用，那么您能够控制程序流，并生成自定义的错误消息。

```javascript
function myFunction(){
	try{ 
		var x=document.getElementById("demo").value;
		if(x=="")    throw "值为空";
		if(isNaN(x)) throw "不是数字";
		if(x>10)     throw "太大";
		if(x<5)      throw "太小";
	}
	catch(err){
		var y=document.getElementById("mess");
		y.innerHTML="错误：" + err + "。";
	}
}
```

##JavaScript 调试工具##
在程序代码中寻找错误叫做代码调试。

调试很难，但幸运的是，很多浏览器都内置了调试工具。

内置的调试工具可以开始或关闭，严重的错误信息会发送给用户。

有了调试工具，我们就可以设置断点 (代码停止执行的位置), 且可以在代码执行时检测变量。

浏览器启用调试工具一般是按下 F12 键，并在调试菜单中选择 "Console" 。

1. console.log() 方法
2. 设置断点
在调试窗口中，你可以设置 JavaScript 代码的断点。
在每个断点上，都会停止执行 JavaScript 代码，以便于我们检查 JavaScript 变量的值。
在检查完毕后，可以重新执行代码（如播放按钮）。
3. debugger 关键字
debugger 关键字用于停止执行 JavaScript，并调用调试函数。
这个关键字与在调试工具中设置断点的效果是一样的。
如果没有调试可用，debugger 语句将无法工作。
开启 debugger ，代码在第三行前停止执行。

##JavaScript 表单验证##

###JavaScript 可用来在数据被送往服务器前对 HTML 表单中的这些输入数据进行验证###
###表单数据经常需要使用 JavaScript 来验证其正确性：###

1. 验证表单数据是否为空？
2. 验证输入是否是一个正确的email地址？
3. 验证日期是否输入正确？
4. 验证表单输入内容是否为数字型？



##JavaScript JSON##
JSON 是用于存储和传输数据的格式。

JSON 通常用于服务端向网页传递数据 。

###什么是 JSON?###
1. JSON 英文全称 JavaScript Object Notation
2. JSON 是一种轻量级的数据交换格式。
3. JSON是独立的语言 
4. JSON 易于理解。








