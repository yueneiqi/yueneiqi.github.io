---
layout: post
title: "折腾记录：type stub problem on vscode for python"
date: 2023-08-09
author: 玉树
categories: [Tool]
tags: [Type Stub, Python]
comments: true
toc: true
---

## 问题

### Env

- OS: MacOS Monterey(intel) & Ubuntn22.04 LTS
- IDE: Vscode 1.80.2 x64
- Language: Python 3.11.3
- Language server: pylance 0.6.0
- Third-party library: Pillow 10.0.0

### 问题描述

代码能跑，但如何让type inferring识别`width`， 从而方便自动补全等功能。

![image.png](https://s2.loli.net/2024/02/25/QUxiXsEzWHlGL3b.png)

## 解决办法：不纠结

#### TL;DR:

库本身的问题 [`importing packages without proper stubs/hints`](https://github.com/microsoft/pylance-release/issues/3494#issuecomment-1522044553)

五个方法：

1. 换pycharm + 插件（vim/vscode keymap）（大部分支持，也有不能补全的时候但少一些）
2. 关闭pylance，岁月静好（解决不了问题，就不去出现问题的地方）
3. [type assert](https://github.com/microsoft/pylance-release/issues/3494#issuecomment-1654716138) (有时能解决问题)
4. [关闭报错信息](https://github.com/microsoft/pylance-release/issues/3494#issuecomment-1396630855)(不看到恼人的波浪号)
5. [手动增加](https://github.com/microsoft/python-type-stubs#our-use-of-type-stubs)[`.pyi`](https://github.com/microsoft/python-type-stubs#our-use-of-type-stubs)[stub files](https://github.com/microsoft/python-type-stubs#our-use-of-type-stubs)（理论上能解决问题，目测工作量大，相当于成为库作者）

在vscode上没有开箱即用的解决方案，所以目前的办法（2023.8.9）——**不纠结**，或者换pycharm。

------

key word: python, pylance, type stub

完整的讨论：

[Cannot access member "to" for type "None" Member "to" is unknownPylance(reportGeneralTypeIssues) · Issue #3494 · microsoft/pylance-release (github.com)](https://github.com/microsoft/pylance-release/issues/3494)

具体的例子：

[Pyspark: cannot access member "config" for type "classproperty" · Issue #4577 · microsoft/pylance-release (github.com)](https://github.com/microsoft/pylance-release/issues/4577)

## 做过的尝试

- 换IDE: pycharm
- 换 language server: jedi
- 试不同的库（pillow & python-redmine）
- 在两个机子上复现（mac & linux）
- 换不同的python版本(python3.11 & python 3.8)

## related concept

#### type stub problem

Python type stubs are a set of type stubs for popular Python packages. These are works in progress from the Microsoft Python team and others, with the intent that they are contributed to typeshed or to the associated packages once sufficiently complete. They are used to ensure that packages have high-quality type annotations. In cases where this must be done through type stubs, they are contributing stubs to typeshed. This repository contains their “work in progress”. [Once the stubs for a package meet the requirements of typeshed, they will contribute them to typeshed and delete them from this repository1](https://github.com/microsoft/python-type-stubs).

In case you are facing an issue with Pylance, it is possible that it is a type stub problem. [The type Pylance is using for the class may be coming from a type stub2](https://github.com/microsoft/pylance-release/issues/3494).

#### typeshed

Typeshed is a collection of library stubs for Python, with static types. [It contains external type annotations for the Python standard library and Python built-ins, as well as third-party packages contributed by people external to those projects1](https://github.com/python/typeshed). [This data can be used for static analysis, type checking, or type inference](about:blank#)[2](https://github.com/python/typeshed/blob/main/README.md). [If you’re using a type checker (such as mypy, pyright, pytype, PyCharm, etc.), you don’t need to interact with the typeshed repository at all: a copy of the standard library part of typeshed is bundled with type checkers](about:blank#)[2](https://github.com/python/typeshed/blob/main/README.md).

