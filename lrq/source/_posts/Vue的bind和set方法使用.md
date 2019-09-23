---
title: Vue中v-bind动态改变样式及Vue.set()方法使用
tags: 前端 vue
---

### 需求及问题描述
现在用v-for将后端传来的图片显示在页面上了，想实现这样一个功能：
1. 点击某张图片后该图片添加边框 
2. 同时该图片右侧增加标记，提示已经点击。 
3. 边框是只有正在操作的图片才有，标记是只要点击过就有。

### 解决方案
这里因为是在for循环里面，每一张图片自带了index这个属性。

#### 第一个需求
对于第一个需求，首先在html中使用v-bind绑定class属性，然后设置点击事件，点击之后设置某标志位为当前点击的index，使用三目表达式来判断当前的class中的index是不是点击的index，进而实现显示边框的效果。
下面是html代码
```
<a :class="clickActive==index? 'active':'' "  @click="clickPic(index)">
```
下面是js代码
```
clickPic: function(index) {
    this.clickActive = index;     //将全局变量设置为当前点击的下标
}
```
其中clickActive是全局变量，用来表示当前点击图片的下标。active是一个类，需要在css中定义这个类的样式。
至此第一个需求完成。

#### 第二个需求
想要达到一个"记忆"的效果，肯定要设置一个全局的东西，将点击过的东西记录下来。本来是设置了一个数组，点击了某个图片后，在clickPic函数中给数组中设置true。代码如下

```
clickPic: function(index) {
    this.clickActive = index;     //将全局变量设置为当前点击的下标
    this.clickActives[index] = true;    //将点击过的图片index记录
}
```

然后在显示图片旁边加上标记,代码如下：

```
<span v-show="clickActives[index]==true">已点击</span>
```

逻辑上没什么问题，但是一直不能成功，在Chrome上看元素也是没有显示，display:none 这样。查了查，试了试别的方法，发现数组布尔值无法监听，看到有一种方法是将数组内设为对象数组，改动对象的属性之后就可以监听到变化。尝试以后发现确实可以，然后思考了一下。

#### 总结陈词
归根结底是没有监听到的原因，对于数组中直接放布尔值vue无法监听到，加入将数组内的元素变为对象，实质上还是监听的对象。想要实现监听数组，就需要使用**Vue.set(arr, index, item)**这个方法,代码如下:

```
clickPic: function(index) {
    this.clickActive = index;     //将全局变量设置为当前点击的下标
    Vue.set(this.clickActives, index, true);   //使用vue.set给设置布尔值并监听
}
```

至此，功能实现。

下面是效果图
![avatar](https://github.com/LRQLRQ/picture/blob/master/vueSet.png?raw=true)
