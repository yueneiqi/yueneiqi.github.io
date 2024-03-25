---
layout: post
title: "解决MacOS上zsh在git add时tab补全卡住的问题"
date: 2024-03-25
author: 玉树
categories: [MacOS]
tags: [MacOS]
comments: true
toc: true
---

## 问题描述

在MacOS上，通过zsh在git仓库中使用`git add`命令并用tab键补全路径时卡住。

尝试[Git tab completion not working in zsh on mac - stackoverflow](https://stackoverflow.com/questions/24513873/git-tab-completion-not-working-in-zsh-on-mac)排名靠前的方法无效。之前使用[Warp](https://www.warp.dev/)新的终端工具来绕过这个问题。

## 解决方案

根据[stackoverflow](https://stackoverflow.com/a/77774933/17777720)上2024最新的回答，在`~/.zshrc`中添加：

```bash
# Init Homebrew to get `HOMEBREW_PREFIX`
eval "$(brew shellenv)"

# Delete brew's objectively worse git completion
remove_conflicting_git_completions() {
    local git_completion_bash="$HOMEBREW_PREFIX/share/zsh/site-functions/git-completion.bash"
    local git_completion_zsh="$HOMEBREW_PREFIX/share/zsh/site-functions/_git"

    [ -e "$git_completion_bash" ] && rm "$git_completion_bash"
    [ -e "$git_completion_zsh" ] && rm "$git_completion_zsh"
}

# This needs to run every time since brew sometimes brings those files back
remove_conflicting_git_completions

# Add Homebrew's site functions to fpath (minus git, because that causes conflicts)
# This will give you autocomplete for _other_ things you installed
# from brew (like `just`, or `exa`, or `k6`)
fpath=($HOMEBREW_PREFIX/share/zsh/site-functions $fpath)
```

执行一次后即可修复，即使执行后删去上文中的代码也没事,依旧可正常补全，不会再卡住。
