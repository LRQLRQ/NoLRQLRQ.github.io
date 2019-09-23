---
title: 你不知道的JavaScript(3)
tags: 前端
---

**不变性**
1. 对象常量
结合writable:false 和configurable:false就可以创建一个真正的常量属性(不可修改、重定义或删除)
```
var myObject = {};
Object.defineProperty( myObject, "FAVORITE_NUMBER", {
    value:42,
    writable: false,
    configurable: false
})
```
2. 禁止扩展
如果想禁止一个对象添加新属性并且保留已有属性，可以使用Object.preventExtensions(..)
```
var myObject = {
    a: 2
};
Object.preventExtensions( myObject );
myObject.b = 3;
myObject.b  //undefined
```
3. 密封
Object.seal(..)会创建一个"密封"的对象，这个方法实际上会在一个现有对象上调用Object.preventExtensions(..) 并把所有现有属性标记为 `configurable:false`  
密封后不仅不能添加新的属性，也不能重新配置或者删除任何现有的属性  
4. 冻结
Object.freeze(..)会创建一个冻结对象，这个方法实际上会在一个现有对象上调用 Object.seal(..) 并把所有"数据访问"属性标记为`writable:false`,这样就无法修改它们的值。  
这个方法是你可以应用在对象上的级别最高的不可变性，它会禁止对于对象本身及其任意直接属性的修改。  
你可以“深度冻结” 一个对象，具体方法为，首先在这个对象上调用Object.freeze(..)，然后遍历它引用的所有对象并在这些对象上调用Object.freeze(..)。但是一定要小心，因为这样做有可能会在无意中冻结其他（共享） 对象。

**Getter和Setter**
对象默认的 [[Put]] 和[[Get]]操作分别可以控制属性值的设置和获取。
getter是一个隐藏函数，会在获取属性值时调用。setter也是一个隐藏函数，会在设置属性值时调用。  
**存在性**
可以在不访问属性值的情况下判断对象中是否存在某个属性:
```
var myObject = {
    a: 2
};

("a" in myObject);  //true
("b" in myObject);  //false
myObject.hasOwnProperty( "a" ); //true
myObject.hasOwnProperty( "b" ); //false
```
in操作符会检查属性是否在对象及其 [[Prototype]] 原型链中，相比之下，hasOwnProperty(..) 只会检查属性是否在myObject对象中，不会检查[[Prototype]]链。

in操作符只会检查容器内是否有某个值，但是它实际上检查的是某个属性名是否存在。对于数组来说这个区别特别重要。比如： 4 in [ 2, 4, 6] 的结果并不是true，因为 [2, 4, 6] 这个数组的属性名是 0、1、2  

**可枚举性**
在数组上应用`for ... in ...`循环时可能会产生出乎意料的结果，因为这种枚举不仅会包含所有数值索引，还会包含所有可枚举属性。最好只在对象上应用 `for .. in`循环，如果要遍历数组就使用传统的for循环来遍历数值索引。  
```
var myObject = {};
Object.defineProperty(
    myObject,
    "a",
    { enumerable: true, value:2 }
);
Object.defineProperty(
    myObject,
    "b",
    { enumerable: false, value: 3 }
);
myObject.propertyIsEnumerable("a"); //true
myObject.propertyIsEnumerable("b"); //false

Object.keys( myObject );    // ["a"]
Object.getOwnPropertyNames( myObject ); //[ "a", "b"]
```
`propertyIfEnumerable(..)`会检查给定的属性名是否直接存在于对象中(而不是阿紫原型链上)，并且满足`enumerable: true`  
`Object.keys(..)` 会返回一个数组，包含所有可枚举属性，`Object.getOwnPropertyNames(..)`会返回一个数组，包含所有属性，无论它们是否可枚举。  
`in` 和 `hasOwnProperty(..)`的区别在于是否查找 `[[property]]` 链，然而`Object.keys(..)`和`Object.getOwnPropertyNames(..)`都只会查找对象直接包含的属性。  

**遍历**
下面是一个标准的for循环遍历程序
```
var myArray = [1, 2, 3];
for(var i=0; i<myArray.length; i++) {
    console.log( myArray[i] );
}
```
这实际上并不是在遍历值，而是遍历下标来指向值。  
ES5中增加了一些数组的辅助迭代器，包括`forEach(..)` 、`every(..)` 和`some(..)`。每种辅助迭代器都可以接受一个回调函数，并把它应用到数组的每个元素上，唯一的区别是他们对于回调函数返回值的处理方式不同。  
`forEach(..)`会遍历数组中的所有值并忽略回调函数的返回值。`every(..)`会一直运行直到回调函数返回false，`some(..)`会一直运行直到回调函数返回true。  
可以用`for ..of..`遍历数组的值。  
```
var myArray = [1,2,3];
for(var v of myArray) {
    console.log(v);
}
```
**小结**  
JavaScript 中的对象有字面形式（比如 var a = { .. }） 和构造形式（比如 var a = newArray(..)）。字面形式更常用，不过有时候构造形式可以提供更多选项。许多人都以为“JavaScript中万物都是对象”， 这是错误的对象是 6 个（或者是7个，取决于你的观点）基础类型之一。  

对象有包括function在内的子类型，不同子类型具有不同的行为，比如内部标签[objectArray]表示这是对象的子类型数组。对象就是键 / 值对的集合。可以通过 .propName 或者 ["propName"] 语法来获取属性值。  

访问属性时，引擎实际上会调用内部的默认[[Get]]操作（在设置属性值时是[[Put]]），[[Get]]操作会检查对象本身是否包含这个属性，如果没找到的话还会查找[[Prototype]]链属性的特性可以通过属性描述符来控制，比如`writable`和`configurable`。此外，可以使用`Object.preventExtensions(..)、Object.seal(..)和Object.freeze(..)`来设置对象（及其属性）的不可变性级别。  

属性不一定包含值——它们可能是具备getter/setter的“访问描述符”。此外，属性可以是可枚举或者不可枚举的，这决定了它们是否会出现在 for..in 循环中。


### 原型
JavaScript中的对象有一个特殊的[[Prototype]]内置属性，其实就是对于其他对象的引用。几乎所有的对象在创建的时候[[prototype]]  属性都会被赋予一个非空的值。  
```
var anotherObject = {
    a: 2
};
var myObject = Object.create( anotherObject );  //Object.create会创建一个对象并把这个对象的[[Prototype]]关联到指定的对象
myObject.a  // 2
```
使用`for...in`遍历对象时原理和查找[[Prototype]]链类似，任何可以通过原型链访问到的(并且是enumerable)的属性都会被枚举。使用in操作符来检查属性在对象中是否存在时，同样会查找对象的整条原型链(无论属性是否可枚举)  
到哪里才是[[Prototype]]的 “尽头”呢？所有普通的[[Prototype]]链最终都会指向内置的`Object.prototype`。由于所有的“普通”对象都“源于”(或者说把[[Prototype]]链的顶端设置为)这个Object.prototype对象，所以它包含JavaScript中许多通用的功能，比如你熟悉的`.toString()  .valueOf()  .hasOwnProperty(..)`  

#### 属性设置和屏蔽
给一个对象设置属性并不仅仅是添加一个新属性或者修改已有的属性值。如：`myObject.foo = "bar" `   
如果myObject对象中包含名为foo的普通数据访问属性，这条赋值语句只会修改已有的属性值。  
如果foo不是直接存在于myObject中，[[Prototype]]链就会被遍历，类似[[Get]]操作。如果原型链上找不到foo，foo就会被直接添加到myObject上。  
如果属性名foo既出现在myObject中也出现自myObject的[[Prototype]]链上层，那么就会发生屏蔽。myObject中包含的foo属性会屏蔽原型链上层的所有foo属性，因为myObject.foo 总是会选择原型链中最底层的foo属性。  

如果foo不直接存在于myObject而是存在于原型链上层时 myObject.foo = "bar"可能出现三种情况：
1. 如果在[[Prototype]]链上层存在名为foo的普通数据访问属性，并且没有被标记为只读(writable: false)，那就会直接在myObject中添加一个名为foo的新属性，它是**屏蔽属性**。
2. 如果在[[Prototype]]链上层存在foo，但是它被标记为只读(writable: false)，那么无法修改已有属性或者在myObject上创建屏蔽属性，如果运行在严格模式下，代码会抛出一个错误。否则，这条赋值语句会被忽略。  总之不会发生屏蔽
3. 如果在[[Prototype]] 链上层存在foo并且它是一个setter，那就一定会调用这个setter。foo不会被添加到myObject，也不会重新定义这个setter。

**隐式屏蔽**
```
var anotherObject = {
    a: 2
};
var myObject = Object.create( another );
another.a;       //2
myObject.a;     //2
anotherObject.hasOwnProperty("a");  //true
myObject.hasOwwnProperty( "a" );    //false

myObject.a++;   //隐式屏蔽

anotherObject.a     // 2
myObject.a;         // 3
myObject.hasOwnProperty( "a" ); //true
```
尽管myObject.a++看起来应该查找并增加anotherObject.a属性，但是别忘了 ++操作相当于`myObject.a = myObject.a + 1` 因此++操作首先会通过[[Prototype]] 查找a属性并从 anotherObject.a获取当前属性值2，然后给这个值加1，接着用[[ Put ]]将值3赋给 myObject中新建的屏蔽属性a。  

#### 技术
```
function Foo(name) {
    this.name = name;
}

Foo.prototype.myName = function() {
    return this.name;
}

var a = new Foo("a");
var b = new Foo("b");
a.myName();     // "a"
b.myName();     // "b"
```
`Foo.prototype.myName = ...` 有个有趣的技巧，他会给`Foo.prototype`对象添加一个属性(函数),现在a.myName()可以正常工作，看起来似乎创建a和b时会把Foo.prototype对象复制到这两个对象中，但事实并非如此。在创建过程中，a和b的内部 [[Prototype]] 都会关联到Foo.prototype上。当a和b中无法找到myName时，它会(通过委托)在Foo.prototype上找到。  

思考以下代码
```
function Foo {
    //...
}

Foo.prototype = {   //创建一个新的原型对象
    //...
};

var a1 = new Foo();
a1.constructor ===Foo;      //false
a1.constructor ===Object;   //true
```
a1并没有 `.constructor` 属性，所以它会委托 [[Prototype]] 链上的 `Foo.prototype`。但是这个对象也没有 `.constructor`属性(默认的Foo.prototype对象有这个属性，只是刚刚我们改了)，所以它会继续委托，这次会委托给委托链顶端的 `Object.prototype`，这个对象有 `.constructor`属性，指向内置的 `Object(..)`函数。  

**典型的原型风格**
```
function Foo(name) {
    this.name = name;
}
Foo.prototype.myName = function() {
    return this.name;
}
function Bar(name, label) {
    Foo.call( this, name);
    this.label = label;
}

//创建了一个新的Bar.prototype 对象并关联到Foo.prototype
Bar.prototype = Object.create( Foo.prototype );

//现在没有 Bar.prototype.constructor 了

Bar.prototype.myLabel = function() {
    return this.label;
}

var test = new Bar("a", "obj a");
test.myName();     // "a"
test.myLabel();    // "obj a"
```
这段代码的核心部分就是语句 `Bar.prototype = Object.create( Foo.prototype )` 调用 `Object.create(..)` 会凭空创建一个“新”对象，并把新对象内部的[[prototype]] 关联到你指定的对象。（本例中是Foo.prototype），换句话说，这句话的意思就是创建一个新的Bar.prototype对象，并把它关联到Foo.prototype 。  
声明 function Bar() {..}时，和其他函数一样，Bar会有一个 .prototype 关联到默认的对象，但是这个对象并不是我们想要的Foo.prototype。因此我们创建了一个新对象并把原来的对象抛弃掉。  
有两种常见的错误做法：
1. `Bar.prototype = Foo.prototype`  //和想要的机制不一样
并不会创建一个关联到 `Bar.prototype`的新对象，他只是让 `Bar.prototype`直接引用`Foo.prototype`对象。因此当执行类似`Bar.prototype.myLabel = ...`的赋值语句会直接修改`Foo.prototype`对象本身，显然这不是想要的结果，否则你根本不需要Bar对象，直接使用Foo就可以了，这样代码也会简单一些。  

2. `Bar.prototype = new Foo();`     //基本上满足需求，但是可能会产生一些副作用
这样确实会创建一个关联到 `Bar.prototype`的新对象，但是使用了`Foo(..)`的 “构造函数调用”，如果函数Foo有一些副作用(应该是在constructor写了什么东西)，比如写日志、修改状态、注册到其他对象等等。。这样就会影响到Bar()的 “后代”，后果很严重。  

两种把Bar.prototypr 关联到 Foo.prototype 的方法:
```
//ES6之前需要抛弃默认的Bar.prototype  
Bar.prototype = Object.create( Foo.prototype );

//ES6开始可以直接修改现有的Bar.prototype
Object.setPrototypeOf( Bar.prototype, Foo.prototype );
```
#### 检查”类“关系
检查一个实例（JavaScript中的对象）的继承祖先（JavaScript中的委托关联）通常被称为内省，或者反射。  
```
function Foo() {
    // ...
}
Foo.prototype.blah = ...;
var a = new Foo();
```
1. 站在“类”的角度来判断：  
`a instanceoof Foo   //true`  
instanceof操作符的左操作数是一个普通的对象，右操作数是一个函数。instanceof回答的问题是：在a的整条[[Prototype]]链中是否有指向Foo.prototype的对象？   
这个方法只能处理对象(a)和函数(带 .prototype引用的Foo)之间的关系。如果你想判断两个对象，比如a和b之间是否通过[[prototype]]链关联，只用instanceof无法实现。  
2. 另一种判断[[Prototype]]反射的方法，更加简洁。  
`Foo.prototype.isPrototypeOf( a )`  //true  
在本例中，我们实际上并不关心甚至不需要Foo，我们只需要一个可以用来判断的对象（本例中是Foo.prototype）就行，isPrototypeOf(..)回答的问题是：在a的整条 [[Prototype]]链中是否出现过Foo.prototype?  
我们只需要两个对象就可以判断它们之间的关系。  
```
b.isPrototypeOf( c )        //b是否出现在c的[[Prototype]]链中？
```
有一种方法能引用某个对象的prototype，如 `a.__proto__ ===Foo.prototype   //true` ，和constructor一样， `.__proto__`实际上并不存在于正在使用的对象中，它存在于内置的 `Object.prototype`中。  

#### 对象关联
ok，fine。[[Prototype]]机制就是存在于对象中的一个内部链接，它会引用其他对象。这个链接的作用是：**如果在对象上没有找到需要的属性或者方法引用，引擎就会继续在 [[Prototype]] 关联的对象上进行查找。同理，如果后者也没有找到需要的引用，就会继续查找它的 [[Prototype]]，以此类推**。这一系列对象的链接被称为 “原型链”。  

```
var foo = {
    something: function() {
        console.log("Tell me something good...");
    }
};

var bar = Object.create(foo);
bar.something();    //Tell me something
```
Object.create(..)会创建一个新对象(bar)并把它关联到我们指定的对象(foo)，这样我们就可以充分发挥[[Prototype]]的机制的威力了，(委托)并且避免不必要的麻烦(比如使用new的构造函数调用会生成 .prototype 和 .constructor引用)   
我们不需要类来创建两个对象之间的关系，只需要通过委托来关联对象就足够了。Object.create(..)可以完美的创建我们想要的关联关系。  

**小结（精华）**  
1. 如果要访问对象中并不存在的一个属性，[[Get]]操作就会查找对象内部[[Prototype]]关联的对象。这个关联关系实际上定义了一条“原型链”（很像嵌套作用域链），在查找属性时会对它进行遍历。  
2. 所有普通对象都有内置的Object.prototype，指向原型链的顶端（比如说全局作用域），如果在原型链中找不到指定的属性就会停止。toString()、 valueOf()和其他一些通用的功能都存在于Object.prototype对象上，因此语言中所有的对象都可以使用它们。  
3. 关联两个对象最常用的方法是使用new关键字进行函数调用，在调用的4个步骤中会创建一个关联其他对象的新对象。  
4. 使用new调用函数时会把新对象的 `.prototype`属性关联到“其他对象”。带new 的函数调用通常被称为“构造函数调用”，尽管他们实际上和传统面向类语言中的**类构造函数**不一样。
5. 虽然这些JavaScript机制和传统面向类语言中的“类初始化”和“类继承”很相似，但是JavaScript中的机制有一个核心区别，那就是不会进行复制，对象之间是通过内部的[[Prototype]]链进行关联。
6.  处于各种原因，以“继承”为结尾的术语（包含“原型继承”）和其他面向对象的术语都无法帮助你理解JavaScript的**真实机制**（不仅仅是限制我们的思维模式）。相比之下，“委托”是一个更合适的术语，因为对象之间的关系不是**复制**，而是委托。
