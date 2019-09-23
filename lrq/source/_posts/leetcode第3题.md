---
title: LeetCode第3题
tags: algorithm
---

2018.2.26
## 题目要求：输入一个字符串，输出其中连续的不重复的最长的子字符串的长度。
#### **第一次用了滑动窗口的思想**
解题思路：将输入的字符串使用split方法分割成数组，然后使用pop方法将数组尾的字符弹出。再构造一个移动窗口的数组--temp，每弹出一个字符先用变量tail存着，跟窗口--temp对比一下，如果重复了就记下当前temp的长度，和历史的比较留下大的，如果不重复就用push方法，推进窗口数组中。

**代码如下：**

---
```
var lengthOfLongestSubstring = function(s) {
    var sArray = s.split('');   //将所给的字符串分成数组
    var sTemp = [];             //缓存移动窗口
    var count = 0;              //返回的数字
    var tail;                   //每次循环数组队尾的字母   
    while(sArray.length>0){     //当字符串数组有值时进行循环
        tail = sArray.pop();     //取出sArray末尾的元素
        for(let i=0;i<sTemp.length;i++){    //取出字符串尾的元素与缓存窗口进行比较
            if(sTemp[i]===tail){            //如果字符重复则进行操作
                count = count>sTemp.length?count:sTemp.length;  //将历史中最大的count与窗口缓存的数组长度进行比较，取其大者
                for(let j=0;j<i+1;j++){     
                    sTemp.shift();          //将重复字符以前的字符弹出数组
                }
            }
        }
        sTemp.push(tail);                   //缓存移动窗口推入末尾的元素
    }
    count = count>sTemp.length?count:sTemp.length;  //有时传入的字符串本身就是最大的字符串，没有进入循环中的判断，因此最后还要进行赋值
    console.log(count);
    return count;
};
```
---

然后看了一下别人的代码，感觉能写好一点。不需要将传入的s字符串转换为数组，也不需要对s进行操作，只需要每次取出s中的元素进行比较就行了。**s[i]**就可以取出字符串s中第i位的元素。思路是一样的，用滑动窗口进行判断，如果用**temp.indexOf(s[i])**进行判断后，发现这个字符在缓冲区的话，就使用slice方法，给temp重新赋值，从重复的位置的后一个到队尾，重新赋给temp。并记录下最大的长度和历史比较。如果不在缓冲区的话，直接push到缓冲区就好。
**代码如下：**

---
```
var lengthOfLongestSubstring = function(s) {
    var temp = [];
    var maxLength = 0; 
    for(var i = 0; i < s.length; i++){
        if(temp.indexOf(s[i]) === -1){
            temp.push(s[i]);
            if(temp.length > maxLength)
                maxLength = temp.length;
        }else{
            temp = temp.slice(temp.indexOf(s[i]) + 1,temp.length);
            temp.push(s[i]);
        }
    }
    
    return maxLength;
};
```

---
