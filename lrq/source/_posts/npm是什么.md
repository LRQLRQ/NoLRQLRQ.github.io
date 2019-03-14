---
title: npm是干什么的
tags: npm 前端
---
## npm,一个和pip一样的东西
一直在别人的博客里看到npm，今天查了一下，终于知道这是什么了,npm全名是Node Package Manager，是一个包管理器。因为前端js的文件每天都有无数个贡献者贡献代码，如果没有这么一个npm，包管理工具的话，那我们想要找到需要的代码，那得费一百牛二百虎之力。在社区中，大家把自己开发好的前端模块发给npm的作者，然后npm的作者把接收到的代码放到服务器上。当我们下好的npm之后，只需要输入命令，就可以方便快捷的下载好需要的代码，下载到node_modules目录中。

在这里抄一下npm的实现思路：
1. 买一个服务器当做代码仓库，把所有需要共享的代码放到服务器里。
2. 发邮件给jQuery，Bootstrap作者，使用npm publish把代码提交到registry上，分别取名jquery、bootstrap。
3. 社区里的其他人如果想使用这些代码，就吧jquery、bootstrap写到package.json里，然后运行npm install，npm就会帮他们自动下载
4. 下载完之后的代码就在node_modules里面，可以使用了。

### 这些可以使用的代码叫做“包”，（package）这就是NPM名字的由来，包管理器。


都是从这里学习的↓↓↓↓↓
原文地址：
https://blog.csdn.net/qq_37696120/article/details/80507178