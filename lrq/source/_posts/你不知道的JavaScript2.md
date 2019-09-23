---
title: 你不知道的JavaScript(2)
tags: 前端
---

##### 块级作用域

先说一段代码
```
var foo = true;
if(foo) {
    var bar = foo * 2;
    console.log(bar);
}
console.log(bar);
```
上述代码会输出什么呢？我一直以为第一个console输出2，到第二个会输出undefined，但是两个都输出了2.就是因为定义的时候用的是var！！使用var声明变量时，bar在哪里都是一样的，最终都会属于外部作用域。  
const 和 let有块级作用域，var出来的变量没有块级作用域。

##### 小结
1. 函数是JavaScript中最常见的作用域单元。本质上，声明在一个函数内部的变量或函数会在所处的作用域中“隐藏”起来，这是有意为之的良好软件的设计原则。
2. 函数不是唯一的作用域单元。块作用域指的是变量和函数不仅可以属于所处的作用域，也可以属于某个代码块(通常是指{ .. }内部)
3. 从ES3开始，try/catch结构在 catch分句中具有块作用域。
4. 在ES6中引入了`let`关键字(`var`关键字的表亲),用来在任意代码块中声明变量。if( .. ){ let a = 2; }会声明一个劫持了if的 { .. }块的变量，并且将变量添加到这个块中。

### 提升
编译：引擎会在解释JavaScript代码之前首先对其进行编译。编译阶段的一部分工作就是找到所有的声明，并用合适的作用域将他们关联起来。  
**只有声明本身会被提升，而赋值操作或其他运行逻辑会留在原地。如果提升改变了代码执行的顺序，会造成非常严重的后果**  
函数声明会被提升，但是函数表达式却不会被提升。  
```
foo();  
var foo = function bar() {
    //
}
```
上面这种就是函数表达式，  变量标识符`foo()`被提升并分配给所在作用域，因此`foo()`不会导致ReferenceError。但是foo此时没有赋值，(如果他是一个函数声明而不是函数表达式，就会被赋值),程序运行到foo()时，foo的内容还是undefined，对undefined进行调用导致非法操作，因此会抛出TypeError异常。  
此外，即使是具名的函数表达式，名称标识符在赋值之前也无法再所在作用域中使用：
```
foo();  //TypeError
bar();  //ReferenceError
var foo = function bar() {
    //...
};
```
上段代码提升后，实际上会理解为以下形式:
```
var foo;
foo();  //TypeError
bar();  //ReferenceError
foo = function() {
    var bar = ...self...;   //其实我也不太懂
    
}
```
#### 函数优先
函数声明和变量声明都会被提升，但是两者同时呢？答案是函数会首先被提升，然后才是变量。  
```
foo();
var foo;
function foo() {
    console.log("1");
}

foo = function() {
    console.log("2");
}
```
输出的结果是1，而不是2. `var foo`尽管出现在`function foo()...`的声明之前，但它是重复的声明，被忽略了，因为函数的声明会被提升到普通变量之前。  


### 作用域闭包
闭包是基于词法作用域书写代码时所产生的自然结果，甚至不需要为了利用它们而有意识地创建闭包。闭包的创建和使用值代码中随处可见，**缺少的是根据自己的意愿来识别、拥抱、和影响闭包的思维环境。**  
闭包定义：**当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行**  
引擎有垃圾回收器来释放不再使用的内存空间，但是闭包可以阻止这件事的发生。内部作用域依然存在，闭包的哪个引用，仍然在使用内部作用域的某个变量。  
某个函数在定义时的词法作用域以外的地方被调用。闭包使得函数可以继续访问定义时的词法作用域。无论通过何种手段将内部函数传递到所在的词法作用域以外，**它都会持有对原始定义作用域的引用**，无论在何处执行这个函数都会使用闭包。  

#### 模块
先看一块代码：
```
function CoolModule() {
    var something = "cool";
    var another = [1, 2, 3];
    
    function doSomething() {
        console.log( something );
    }
    function doAnother() {
        console.log( another.join("!") );
    }
    
    return {
        doSomething: doSomething,
        doAnother: doAnother
    }
}
var foo = CoolModule();
foo.doSomething();      //cool
foo.doAnother();        //1!2!3!
```
模块模式需要具备两个必要条件：
1. 必须有外部的封闭函数，该函数至少被调用一次（每次调用都会创建一个新的模块实例）
2. 封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以返回或者修改私有的状态。  

一个具有函数属性的对象本身并不是真正的模块，从方便观察的角度看，一个从函数调用所返回的，只有数据属性而没有闭包函数的对象并不是真正的模块。  
模块内部的函数也可以改动，如下代码：
```
var foo = (function CoolModule(id) {
    function change() {
        // 修改公共API
        publicAPI.identify = identify2;
    }
    function identify1() {
        console.log( id );
    }
    function identify2() {
        console.log( id.toUpperCase() );
    }
    
    var publicAPI = {
        change: change,
        identify: identify1
    };
    
    return publicAPI;
})( "foo module" );

foo.identify(); // foo module
foo.change();
foo.identify(); //FOO MODULE 
```
通过在模块实例的内部保留对公共API对象的内部引用，可以从内部对模块实例进行修改，包括添加或删除方法和属性，以及修改他们的值。

贴一个模块管理器的代码：
```
var MyModules = (function Manager() {
    var modules = {};
    function define(name, deps, impl) {
        for(var i=0; i<deps.length; i++) {
            deps[i] = module[deps[i]];
        }
        modules[name] = impl.apply( impl, deps );  //这句话很关键
    }
    
    function get(name) {
        return modules[name];
    }
    
    return {
        define: define,
        get: get
    };
})();
```
那句关键的话，为了模块的定义引入了包装函数，(可以传入任何依赖)，并且将返回值，也就是模块的API，存储在一个根据名字来管理的列表中。  
模块管理器没有任何的"魔力"，他们符合前面说的模块模式的两个特点：**为函数定义引入包装函数，并保证它的返回值和模块的API保持一致。**  

词法作用域和动态作用域的区别：  
1. 词法作用域是在写代码或者说定义时确定的，而动态作用域是在运行时确定的。(this 也是)
2. 词法作用域关注函数在何处声明，而动态作用域关注函数从何处调用。

## 第二章 this和原型对象
#### 误解
##### 1.指向自身
##### 2.指向它的作用域
在某种情况下它是正确的，但是在其他情况下它确实错误的。 `this`在任何情况下都不指向函数的词法作用域。在JavaScript内部，作用域确实和对象类似，可见的标识符都是它的属性。但是作用域"对象"无法通过JavaScript代码访问，他存在于JavaScript引擎内部。  
##### this到底是什么
this是在运行时进行绑定的，并不是在编写时绑定的，它的上下文取决于函数调用时的各种条件。this的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。  
当一个函数被调用时，会创建一个活动记录(也称为执行上下文)。这个记录会记录函数在哪里被调用(调用栈)、函数的调用方法、传入的参数等信息。this就是记录的其中一个属性，会在函数执行的过程中用到。  
**this既不指向函数自身，也不指向函数的词法作用域，this实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用**  

#### 调用位置
调用位置是函数在代码中被调用的位置（而不是声明的位置）。要分析调用栈(就是为了到达当前的执行位置所调用的所有函数)。我们关心的调用位置就是当前正在执行的函数的前一个调用中。
#### 调用规则
##### 1. 默认绑定
独立函数调用。
```
function foo() {
    console.log( this.a );
}
var a = 2;
foo();
```
**tip**：声明在全局作用域中的变量(比如var a = 2)就是全局对象的一个同名属性。当调用foo()时，this.a被解析成了全局变量a。因为在本例中，函数调用时应用了this的`默认绑定`，因此this指向全局对象。
如果使用严格模式(strict mode)，那么全局对象将无法使用默认绑定，因此this会绑定到undefined。有时候写代码可能会用到第三方的库，其严格程度和你写的代码有所不同，要注意这类兼容性细节。
##### 2. 隐式绑定
需要考虑调用位置是否有上下文对象，或者说是否被某个对象拥有或者包含。
```
function foo() {
    console.log( this.a );
}

var obj = {
    a: 2,
    foo: foo
};

var bar = obj.foo;  //函数别名
var a = "global";   //a是全局对象的属性
bar();               //"global"
```
参数传递其实是一种隐式赋值。回调函数丢失this绑定是非常常见的。除此之外，还有一种情况this也会令人出乎意料：调用回调函数的函数可能会修改this

##### 3. 显式绑定
在**隐式绑定**中，我们必须在一个对象内部包含一个指向函数的属性，并通过这个属性简介引用函数，从而把`this`间接(隐式)绑定到这个对象上。  
```
function foo() {
    console.log( this.a );
}
var obj = {
    a: 2
};
foo.call( obj )     //2
```
通过`foo.call(...)`，我们可以在调用foo时强制把它的this绑定到obj上。  
如果传入的是一个原始值(字符串类型、布尔类型或者数字类型)来当做this的绑定对象，这个原始值会被转换成它的对象形式。（也就是 new String(..) 、 new Boolean(..) 或者 new Number(..) ）。这通常被称为"装箱"  .
###### 硬绑定
```
function foo() {
    console.log( this.a );
}
var obj = {
    a: 2
}
var bar = function() {
    foo.call(obj);
}
bar();  // 2
setTimeout( bar, 100)   // 2
```
创建了函数bar(),并在它的内部手动调用了foo.call(obj), 因此强制把foo的this绑定到了obj， 无论之后如何调用函数bar，他们总会手动在obj上调用foo，这种绑定是一种显示的强制绑定，因此我们称为硬绑定。  
`硬绑定`是一种非常常用的模式，在ES5中提供了内置的方法`Function.prototype.bind`,它的用法如下：
```
function foo(something) {
    console.log( this.a , something);
    return this.a + something;
}
var obj = {
    a: 2
};

var bar = foo.bind( obj );
var b = bar(3);     // 2 3
console.log( b );   //5
```
bind(..)会返回一个硬编码的新函数，它会把参数设置为this的上下文并调用原始函数。  

##### 4. new绑定
使用new来调用函数，或者说发生都早函数调用时，会自动执行下面的操作：
1. 创建（或者说构造）一个全新的对象
2. 这个新对象会被执行 [[原型 ]]连接。
3. 这个新对象会绑定到函数调用的this
4. 如果哈数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象。

```
function foo(a) {
    this.a = a;
}

var bar = new foo(2);
console.log( bar.a );   //2
```
#### 优先级
有些地方多条规则都可以用，就得讨论一下优先级了。"默认绑定"这个弟弟肯定是优先级最低的了。

##### 显式绑定和隐式绑定优先级比较
```
function foo() {
    console.log( this.a );
}
var obj1 = {
    a: 2,
    foo: foo
};
var obj2 = {
    a: 3,
    foo: foo
};

obj1.foo();             //2
obj2.foo();             //3
obj1.foo.call( obj2 );  //3
obj2.foo.call( obj1 );  //2
```
**显示绑定**优先级更高，也就是说在判断时应当优先考虑是否可以应用**显示绑定**。

##### 判断this
可以根据优先级来判断函数在某个调用位置应用的是哪条规则。可以按照以下顺序判断：
1. 函数是否在new中调用(new 绑定)？如果是的话，this绑定的对象是新创建的对象。  
`var bar = new foo();`
2. 函数是否通过call、apply(显式绑定)或者硬绑定调用？如果是的话，this绑定的是指定的对象。  
`var bar = foo.call( obj2 );`
3. 函数是否在某个上下文对象中调用(隐式绑定)？ 如果是的话，this绑定的是那个上下文对象。  
`var bar = obj1.foo();`
4. 如果都不是，使用默认绑定。如果在严格模式下，就绑定到undefined，否则绑定到全局对象。  
`var bar = foo();`

##### 被忽略的this
如果你把null或者undefined作为this的绑定对象传入call。apply或者bind，这些值在调用时会被忽略，实际使用的是默认绑定规则。  

##### this语法
ES6中有一种无法使用这些规则的特殊函数类型：箭头函数。
箭头函数不是用function关键字定义的，而是使用被称为"胖箭头"的操作符 =>定义的。箭头函数不使用this的四种标准规则，而是根据外层(函数或全局)作用域来决定this。  

#### 小结
如果要判断一个运行中的函数的this绑定，就需要找到这个函数的直接调用位置。找到之后就可以顺序应用下面的这四条规则来判断this的绑定对象。  
1. 由new 调用？  则绑定到新创建的对象。
2. 由call 或者 apply (或者bind)调用？  绑定到指定的对象。
3. 由上下文对象调用？  绑定到上下文对象
4. 默认： 在严格模式下绑定到undefined，  否则绑定到全局对象。

**ATTENTION**：有些调用可能再无意中使用默认绑定规则。如果想"更安全"地忽略this绑定，可以使用一个DMZ对象，比如φ = Object.create(null),以保护全局对象。  
ES6中的箭头函数并不会使用四条标准的绑定规则，而是根据当前的词法作用域来决定this。具体来说，箭头函数会继承外层函数调用的this绑定，(无论this绑定到什么)。这其实和ES6之前的代码中的self = this机制一样。  

## 对象

#### 类型
对象是JavaScript的基础。在JavaScript中一共有六种主要类型(术语是"语言类型"):  
string 、number 、boolean 、null 、undefined 、object 
简单基本类型(string 、boolean 、number 、null 、undefined )本身并不是对象。null有时会被当做一种对象类型，但是这其实只是语言本身的一个bug，即对null执行 `typeof null` 会返回 `object`，实际上，null本身是基本类型。  
JavaScript中海油一些对象子类型，通常被称为内置对象，如： Strig、Number、Boolean、Object、Function、Array、Date、RegExp、Error  
```
var str = "I am a string" ;
console.log( str.length );      //13
console.log( str.charAt(3));    //"m"
```
原始值 `I am a string`并不是一个对象，他只是一个字面量，并且是一个不可变的值。如果要在这个字面量上执行一些操作，比如获取长度、访问其中某个字符等，那需要将其转换为String对象。  
使用以上的方法，我们可以直接在字符串字面量上访问属性或者方法，之所以可以这样做，是因为引擎自动把字面量转换成String对象，所以可以访问属性和方法。  

#### 内容
对象的内容是由一些存储在特定明明位置的(任意类型的)值组成的，称之为属性。存储在对象容器内部的是这些属性的名称，它们就像指针，(从技术角度来说就是引用)，指向这些值真正存储的位置。
```
var myObject = {
    a: 2
};
myObject.a;     //2
myObject["a"]   //2
```
如果要访问myObject中a位置上的值，我们需要.操作符或者[]操作符。 .a 语法通常被称为"属性访问"，["a"]语法通常被称为"键访问"。实际上访问的是同一个位置。这两种语法的主要区别在于.操作符要求属性名满足标识符的命名规范，而[".."]语法可以接受任何UTF-8字符串作为属性名。比如 myObject["Super-Fun!"]  

##### 属性与方法
从技术角度来说，函数永远不会"属于"一个对象。每次访问对象的属性就是属性访问，如果属性访问返回的是一个函数，那它也并不是一个"方法"。属性访问返回的函数和其他的函数没有任何区别。除了可能发生隐式绑定：**有些函数有this引用，有时候这些this确实会指向调用位置的对象引用，但是这种用法从本质上来说并没有把一个函数变成一个“方法”，因为this是在运行时根据调用位置动态绑定的，所以函数和对象的关系最多也只能说是间接关系。**  

**复制对象**  
JSON安全的对象：也就是说可以被序列化为一个JSON字符串并且可以根据这个字符串解析出一个结构和值完全一样的对象
```
function anotherFunction() {
    //..
}
var anotherObject = {
    c: true
}
var anotherArray = [];
var myObject = {
    a: 2,
    b: anotherObject,
    c: anotherArray,
    d: anotherFunction
}

var newObj = Object.assign( {}, myObject );
```
**属性描述符**  
```
var myObject = {
    a: 2
}
Object.getOwnPropertyDescriptor( myObject, "a");
//{
//  value:2,
//  writable: true,
//  enumerable: true,
//  configurable: true
//}
```

