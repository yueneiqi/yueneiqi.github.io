---
layout: post
title: 2021Q1 计算机课程学习总结
date: 2021-04-02
author: 玉树
categories: [学习, 课程]
tags: [Study]
comments: true
toc: true
---

去年因为胡神关注了这些国外课程，但因为各种原因今年才刚刚开始，感觉如果英语阅读和听力不错，听听这些课程是很好的选择。记录一下自己学习的过程吧。
![](https://s2.loli.net/2022/04/03/HF6qswvCIM42XDY.png)
<!-- more -->
## The Missing Semester of Your CS Education

听了很多次，每次都有新收获：
第一次的时候刚想入门，听得懵懵的，写里面的作业，卡在bash脚本那里半天想不通就放弃了。后面用了linux和vim，再看这个课觉得帮助很大。
对我来说，这个课更像是有着详细介绍的索引，想要现在吃透它是很难得，每次吸收一些，效率都会有提升，讲义里各种链接极大得丰富了这个课程，让我有了很多工具去装（工具控福音）。

### 对于新手（我）来说，有用的点

**linux入门**：讲义是很好的新手入门快查表。

**oh-my-zsh及shell脚本**：看到zsh的自动补全就很想用，后来发现了[oh-my-zsh](https://www.wolai.com/pbYR8podr2SmdcxW7Rswuw)，太香了，自带git状态显示，很多[自带的插件](https://zhuanlan.zhihu.com/p/61447507)，两下esc自动补sudo之类的。工具控狂喜，用它写zsh脚本也很方便，在都在一个.zsh文件里写个函数就行了。很多重复的工作，我现在都会写个命令来搞定。写脚本的时候我会用这个[教程](https://zhuanlan.zhihu.com/p/39748065)参考shell的语法，同时用[shellcheck](https://www.shellcheck.net/#)检查，这份shell的[cheetsheet](https://github.com/skywind3000/awesome-cheatsheets/blob/master/languages/bash.sh)也挺有用的。一些[示例](https://www.wolai.com/81PzcE6tnYpwie5VrDde3Z)。

**dotfile**：第一次不理解，想搞但不知道是做啥的，到后面折腾vim和zsh的配置文件，再加上我在mac上装了linux虚拟机，怎么样同步配置是我一个很难受的点，这时候再看到dotfile就豁然开朗了，也走了很多坑，最后用了参考案例中的[一个解决方案](https://github.com/anishathalye/dotbot)（[我的笔记](https://www.wolai.com/w98scjAG3yYtqpLmHFK4ED)），中间我的linux崩了一次重装了，正好有了dotfile省了我很多事。

**git**：配合oh-my-zsh的集成提示，很香。

**vim**：第一次看的时候就只是看看，觉得挺厉害的但然并软，不练等于白看。后面开始使用vim，由此为契机再此看了这个课，觉得很有用，比起听教程学vim，不如做一下vimtutorial，查一下讲义，或者google一下，上手会快很多。入门之后，我也用了vimplus和vscode+vim插件，觉得后者更香，除了对纯键盘操作不感冒外，在安装vimplus本身和一些插件的各种错误也是给我留下了很深的怨念，但这不意味着vscode的上手配置就很轻松，也是挺费劲的。整体感觉下来，vscode更对我的口味吧。可惜了花在`.vimrc`的时间。vscode自带的终端虽然很方便，但性能有点拉垮，用tmux刚好可以弥补这个缺陷。

**正则表达式**：推荐的练习不错，我挺爱用正则表达式的，`grep -E`。

**valgrind**：正好我的入门语言是c和c++，这个工具在内存泄露和变量未初始化的问题上帮助很大。

## cs50x

从2月中旬开始到4月2日结束，耗时：（至少）60小时+

    +作业质量高，有阶梯，介绍详细，还有很多提示和帮助，适合自学完成作业
    +有助于熟悉vscode和c/c++的调试流程
    +TODO等注释标签思路写代码的习惯养成
    +覆盖内容广c python sql 前端三大件html css js

非常好的入门导论课程，因为有基础，所以前半部分的课程跳着听着玩了，老师的课讲得很好，节奏很紧凑。题外话就是这个课的包装和影视制作都太强了，看到最后发现这是一个很大的团队在做就了然了，想制作出这种课程背后的付出却是很大

## CSE 251 Programming in C

就我能做的作业而言（一半的lab和proj1），是一个难度简单，教程详细的c语言入门课程

    +详细的手把手教学
      +Makefile的简易入门
    -后半部分的proj和lab依赖较老的库，很折腾了半天之后也没写成

因为有基础，所以里面的一些作业和内容相对而言就太简单了。坑的一点就是，太想做proj2装了一天的环境也没装好，后面看见需要依赖图形库的作业就果断放弃，发现也就没啥能做的了，还是这门课太老了，想去装老的环境反而很繁琐，折腾了大半天也没成功，如果能做上的话，觉得会对这门课的评价会高更多（看起来很有意思），真的很想做电梯、车库和机器人啊....印象最深的该就是`wumpus`了吧
