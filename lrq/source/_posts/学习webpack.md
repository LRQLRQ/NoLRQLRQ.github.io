---
title: 学习Webpack
tags: 前端
---

# 核心概念
* Entry:入口
* Output:输出
* Loaders:处理其他类型（不是js）的文件
* Plugins:插件，其他的功能


* Entyr:**代码的入口**，说自己import了什么，require了什么。可以在这里直接或者间接找到依赖。**打包的入口**,可以是一个或者是多个。如下代码所示：
```
module.export = {
    entry: {
        index: ['index.js', '<a href=""></a>p.js'],
        vender: 'vender.js'
    }
}
```
* Output:打包的输出。webpack会提供给我们一下变量，比如name、hash ，代码中的name就分别代表entry中的index和vendor,hash是打包中出现的独一无二的码
```
modules.exports = {
    entry: {
        index: 'index.js',
        vendor: 'vendor.js'
    },
    output: {
        filename: '[name].min.[hash:5].js'
    },
}
```
* Loaders，用来处理其他文件，将文件转化为模块。可以将css文件变为js中的一个模块，变为依赖引入进来。如下代码，在module中加入rules，rules是一个数组，也就是可以设置很多loaders，设置它的loader是css-loader。
常用的loader：**编译相关**：babel-loader、ts-loader。**样式相关**：style-loader、css-loader...**文件相关**：file-loader、url-loader
```
module.exports = {
    module: {
        rules: [
            {
                test: /\.css$/,
                use: 'css-loader'
            }
        ]
    }
}
```
* Plugins的作用**参与打包整个过程，打包优化和压缩，配置编译时的变量。**
常用的Plugins有**优化相关的**：CommonsChunkPlugin，UglifyJsPlugin，**功能相关的**：HtmlWebpackPlugin、HotModuleReplacementPlugin
```
const webpack = require('webpack');
module.exports = {
    plugins: [
        new webpack.optimize.UglifyJsPlugin()
    ]
}
```

* 名词
Chunk：代码块
Bundle：已经被打包后的代码
Module：模块。通过loaders处理后，把文件转变为模块，把图片、css处理，变为模块。




