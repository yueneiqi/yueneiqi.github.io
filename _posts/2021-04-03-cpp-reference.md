---
layout: post
title: 【持续更新】C++学习资料
date: 2021-04-15
author: 玉树
categories: [C++]
tags: [Study,C++]
comments: true
toc: true
---

c++的资料非常得多，经典书单不再赘述，这里补充一些自己的体会。

**必看：**
  - [hacking C++](https://hackingcpp.com/index.html)系统全面的c++学习网站，整合了很多资源，新手友好（[Beginner's Guide](https://hackingcpp.com/cpp/beginners_guide.html#intro)）

**能抓住modern C++(C++17)主干，方便入门的：**
  - [cs106L](https://web.stanford.edu/class/cs106l/)【[视频](https://www.bilibili.com/video/BV1yJ411m7CE)】斯坦福modern C++(C++17)课程
  - 【[快速学习C和C++，基础语法和优化策略(南科大计算机系)](https://www.bilibili.com/video/BV1Vf4y1P7pq?p=63)】
  - [《现代 C++ 教程: 高速上手 C++ 11/14/17/20》](https://changkun.de/modern-cpp/zh-cn/00-preface/)》正如书名所说，精简的笔记

**进一步的学习资料：**
  - [【录播】现代C++中的高性能并行编程与优化（持续更新中）](https://www.bilibili.com/video/BV1fa411r7zp?vd_source=8fbe66e7c96f76c12929e16b0eed07a6)
  - [《C++ Core Guidelines》(2022)](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)
  - [cpp-fundamentals-for-professionals](https://www.educative.io/courses/cpp-fundamentals-for-professionals) （C++17）很好的讲义，很多可以在网页上运行的代码例子和小练习

**日常学习**

  - [cppcon](https://www.youtube.com/user/CppCon) 大牛们的讲座合集; Back to Basics系列适合作为入门学习的补充课程
  - [C++ Weekly](https://www.youtube.com/c/lefticus1)

---
### 推荐文章合集
- [RAII](https://hackingcpp.com/cpp/std/unique_ownership.html)
- [move semantic](https://hackingcpp.com/cpp/lang/move_semantics.html#hfold0a)
    - [Advantages of pass-by-value and std::move over pass-by-reference ](https://stackoverflow.com/questions/51705967/advantages-of-pass-by-value-and-stdmove-over-pass-by-reference)
    - [什么是move？理解C++ Value categories，move， move in Rust](https://zhuanlan.zhihu.com/p/374392832)
    - [C++: 再谈move，右值引用的由来 ](https://zhuanlan.zhihu.com/p/515808385)

---
### 关于modern C++（C++11/14/17/20）
  - 在modern c++中，`singleton`设计模式不推荐，应该尽量使用`Dependency injection`来替代，如果想用`singleton`，c++11之后的实现贴在[这里](https://www.zhihu.com/question/333710483/answer/2297774354)
  - （2022.5）在clang和gcc上使用c++20 `module`特性很麻烦，`intellisense` 没有很好的支持，建议跳过这个部分
