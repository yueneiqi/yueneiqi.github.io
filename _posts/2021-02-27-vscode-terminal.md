---
layout: post
title: VSCode终端字体设置：Nerd Fonts
date: 2021-02-27
Author: 玉树
categories: [Tool, VSCode]
tags: [Configuration, VSCode]
comments: true
---
在vscode上设置适配vim和oh-my-zsh的字体，从而解决字体不能正确显示的问题

<img src="https://s2.loli.net/2022/02/26/buPTCkdM4SpVRqL.png" width="30%"/>

<!-- more -->

方法如下：

### 1.安装Powerline fonts

这是一个字体包的安装，也可以直接百度去下单个字体。

Debian/Ubuntu等基于Linux的环境：

```powershell
sudo apt-get install fonts-powerline
```

其他环境，如mac：

```powershell
# clone 下载
git clone https://github.com/powerline/fonts.git --depth=1
# install 安装
cd fonts
./install.sh
# clean-up a bit 删除安装包
cd ..
rm -rf fonts
```


（可选）查看字体库

```powershell
cd ~/Library/Fonts
```

可以看到有很多带`for powerline`的字体，感兴趣也可以自己尝试一下，在vscode的使用格式如下文。

### 2.设置

<img src="https://s2.loli.net/2022/02/26/FUSWY35znomyiEv.png" width="70%"/>

在设置栏中输入：

`DroidSansMono Nerd Font`





参考：

[VScode终端字体设置 - 简书 (jianshu.com)](https://www.jianshu.com/p/155314ee02e5)

[转载：Powerline fonts 字体安装 - 简书 (jianshu.com)](https://www.jianshu.com/p/d8330dfb7197)
