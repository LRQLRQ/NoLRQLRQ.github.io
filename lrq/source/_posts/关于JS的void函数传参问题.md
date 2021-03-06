---
title: 前端开发笔记--JS的void函数传参问题
tags: javascript
---

### 背景
这周要修复一个系统的BUG，在某个页面有查询功能，点击查询按钮后，前端发送查询信息到后端，后端返回给查询的数据。但是在前台翻页的时候，没有保留查询条件。
### 解决方案
原来系统的结构是正常翻页有一个函数，查询有一个函数。**（如果现在让我构造的话我要把他们构造到一个函数，在传参的时候增加一个'search=false'的默认值，但是系统太复杂了，耦合太多，进行这些改动影响太大。）**在点击搜索按钮之后进行查询函数。我的思路是设置一个全局标志位，在查询按钮点击之后触发标志位改变为true，然后在正常的翻页的查询函数中进行判断，如果点击过一次搜索按钮，那么以后都要进行搜索条件的查询。这个问题就解决了。

### 新发现的问题
查询条件保留的问题解决了，但是发现了一个新的问题。**在已经查询翻页的条件下，假如我点击了查询，已经翻到了第三页，此时再点击查询，发现查询条件改变了，但还是在第三页。**

### 新问题的尝试
解决思路是，在第一次点击按钮的时候，将查询的发送请求当前页设置为1.之后的请求照常运行。
因为系统框架使用的vue，最初想在data设置一个标志位，在html的按钮中对这个标志位进行v-onclick="isClick=!isClick",然后再watch监听这个变量，当这个变量改变的时候，就说明进行了点击操作，新增一个标志位置true，此时在发送的请求中设置请求页码为1，在返回处理信息中将标志位置false。
思路我觉得没毛病，虽然逻辑有点冗余复杂，尝试了之后发现不行，因为watch监听中的操作，在函数执行结束之后才会进行。也就是说，我的true标志位总是比我发送请求慢一步。因此也无法达到要求。
**抽空看一下vue中watch的生命周期，以后补上**

### 最终的解决思路
突然想起arguments这个东西，在JAVA、C里面默认都有这个东西，C语言虽然没有显式声明，但是可以通过argu数组这个来调用传进来的参数。就在javascript中进行了尝试，果然成功了。
**虽然我的函数是一个void函数，不需要传递任何参数，但是还是可以在arguments这个数组中调用查询的参数。**
这样就简单了啊，在html中绑定函数的时候在函数内传递'1',在js文件中进行判断，如果传递了'1'，就把当前页设置为第1页，进行查询。否则还是照常运行。哈哈，完美解决！



