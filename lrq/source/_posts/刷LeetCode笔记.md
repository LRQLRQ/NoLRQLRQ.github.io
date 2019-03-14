---
title: 刷LeetCode的笔记
tags: algorithm
---
**刷LeetCode心得体会**

11.23日，第一次刷LeetCode，有一个题我写了28行，看了讨论区别人只写了4行，看了他写的代码，是用了javascript的一些方法，我不知道这些方法，还是像C语言一样实现了一下。
总结一下javascript的一些方法

有个题目是返回输入的数字的倒序
代码如下

```
var reverse = function(x){
	result = parseInt(x.toString().split('').reverse().join(''));
	if(result > Math.pow(2,31)-1 || -result < Math.pow(-2,31)-1) return 0;
	return x > 0 ? result : -result;
}
```
`x.toString` 是把输入的数字转化为字符串
`x.split('')`是把生成的字符串根据''切割，转化为数组
`x.reverse()` 是把数组进行倒序
`x.join('')` 是将数组组合成字符串

---

11.27 Roman to Integer
        今天做了一道将罗马数字转化为数字的题，罗马数字中I为1，V为5，X为10，L为50，C为100，D为500，M为1000.但是表示4的时候不是表示IIII，而是IV，I在V左边表示5-1=4，像这样的都有 IV=4  IX=9  XL=40  XC=90  CD=400 CM=900.
然后根据罗马数字的输入来得到整数的输出，我的代码是这样：
```
/**
 * @param {string} s
 * @return {number}
 */
var romanToInt = function(s) {
    var num_IV = s.split('IV').length-1;    //-1
    var num_IX = s.split('IX').length-1;    //-1    
    var num_XL = s.split('XL').length-1;    //-10
    var num_XC = s.split('XC').length-1;    //-10
    var num_CD = s.split('CD').length-1;    //-100
    var num_CM = s.split('CM').length-1;    //-100
    var num_I = s.split('I').length-1;
    var num_V = s.split('V').length-1;
    var num_X = s.split('X').length-1;
    var num_L = s.split('L').length-1;
    var num_C = s.split('C').length-1;
    var num_D = s.split('D').length-1;
    var num_M = s.split('M').length-1;
    var result = 0;

    result = num_I + num_V*5 + num_X*10 + num_L*50 + num_C*100 + num_D*500 + num_M*1000 
    - num_IV*2 - num_IX*2 - num_XL*20 - num_XC*20 - num_CD*200 - num_CM*200;
    
    return result;
    
};

```
这样写代码很冗杂，才超过了百分之30多的人。。。也算是初步会用了js的一些方法吧。
如果想求某个字符串temp，在字符串s中出现的次数，可以用s.split('temp').length-1，来获得。s.split('temp')后得到一个数组，再求出这个数组的长度，再-1，因为split操作后，数组中会出现''元素。

看了看discussion，发现另一种用slice实现的方法，虽然也有点暴力，没有为之一振的感觉，但是初学者，能吸收一点是一点，放在这里刚好可以对比着学习split和slice这两个方法了

```
var romanToInt = function(s) {
    var roman = {		//定义存储的数组
        'I': 1,
        'V': 5,
        'X': 10,
        'L': 50,
        'C': 100,
        'D': 500,
        'M': 1000,
        'IV': 4,
        'IX': 9,
        'XL': 40,
        'XC': 90,
        'CD': 400,
        'CM': 900,
    };
    var ret = 0;
    for (var i = 0; i < s.length; ) {
        var j = i + 1;	//每次j只比i大1，是当前元素的下一个元素
        if (j === s.length) {	//如果j为字符串的长度，就说明到了最后，进行最后一次运算
            ret += roman[s.slice(i, j)];
            return ret;
        }
        if (s.slice(i, j + 1) in roman) {		//如果连着2个字符出现在之前存储的数组中，即出现了IV、IX这些
            ret += roman[s.slice(i, j + 1)];
            i = j + 1;
        } else {
            ret += roman[s.slice(i, j)];
            i = j;
        }
    }
    return ret;
};
```

`if (s.slice(i, j + 1) in roman) ` 我觉得这个知识点有点东西，得记一下。
依据键来取声明式对象中的值

此外，slice方法，是输入两个index，返回的是该字符串中，两个index之间的字符串。
而split方法，是把字符串进行切分，分割成若干个数组。

---

11.28 求一个数组中，前面相同的部分
如输入 strs = ['flower','flow','fly']  就输出'fl'

做这道题我自己的方法是
1.最多循环数组中第一个元素的长度
2.以第一个元素为基准，以此和数组中别的元素的相同位置的字符进行比较
3.如果相同，则将该字符添加到ret后面


做这道题，掌握到的**新知识点**：
1.对于一些乱七八糟的输入，比如空数组[] ,比如['']，可以这样来判断输入值是否合法
`if(!strs || strs.length)`,如果是undefined的话，加一个取反，逻辑就为true。如果是长度为0，也会返回true。
2.对于输入的strs，可以用这种方式来取值`strs[i][j]`
即对于string类型的数据，可以直接通过[]的方式取值
3.string类型的一个新方法，截取字符串 `string.substring(i,j)`，则返回字符串string中的i到j坐标中的字符串

---

11.29 判定一个括号是不是成对出现，符合规则`{[()]}`,比如说是这样

我的思路：
1.重复将输入的字符串根据`() [] {}` 进行split拆分成数组，再合并起来，一直到最后结果变为 `''`,这就说明成功了。具体代码如下：
```
var temp = s;
while(1){
    temp = s.split("()").join("").split("[]").join("").split("{}").join("");
    if(temp=="")
        return true;
    if(temp==s){
        return false;
    }
    s = temp;
}
```
但是这样运行效率太差劲了，只超过了10%的人，菜的要死。
然后在dission看到了超过100%的人。。学习一下
思路：
1.将括号的左边，和括号的右边存在两个Set对象中，将成对的括号制成map表，以左边括号为key，以右边括号为value
2.用一个栈，随时储存和进行操作
3.对传入的字符串进行遍历，如果是一个左括号的类型的值，则push到栈顶。如果是一个右括号类型的值，就pop弹出栈顶的值，此时栈顶是左括号的最后一个的值，弹出之后就变成了前一个。拿弹出的值对应的map和此时的右括号的某个值进行比较，看相不相等。相等就继续判断下一个，不等就直接输出false

优点：
1.遍历的过程一次只判断一个字母，速度快一点。
2.提前制好了表，即两个set对象和一个map，查找更快。
3.用到了栈，巧妙的用后进先出的特点，解决了括号要按同种类型嵌套的问题
代码如下：
```
var isValid = function(s) {
    let stack = [];
    let openSet = new Set(['(', '{', '[']);
    let closeSet = new Set(['}', ']', ')']);
    let bracketMap = new Map([['(', ')'], ['[', ']'], ['{', '}']]); 
    for (let c of s) {
        if (openSet.has(c))  {
            stack.push(c);   
        }
        else if (closeSet.has(c)) {
            let temp = stack.pop();
            if (temp === undefined || c !== bracketMap.get(temp)) return false;
        } 
    }
    return stack.length === 0;
};
```

新学的知识点：
1.Set对象，和map类似，是一组key的集合，但是不存储value，因为key不能重复，所以在Set中没有重复的元素。
2.map,是一组键值对的结构，具有极快的查找速度，通过传入的数组的数组建立。通过set(key,value)方法添加元素，通过get(key)来取值。
3.栈的push和pop，栈是一种后进先出的数据结构，栈中项的推入和弹出都发生在栈顶。javascript中为数组提供了push()和pop()方法，push()可以接受任意数量的参数，逐个添加到数组的末尾，并返回修改后数组的长度。pop()方法则从数组的末尾弹出最后一项，减少数组的length值，返回弹出的项










