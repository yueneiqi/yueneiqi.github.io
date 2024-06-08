---
layout: post
title: "Latex编写跨页长表格报告的踩坑经历"
date: 2024-06-08
author: 玉树
categories: [Latex]
tags: [Latex]
comments: true
toc: true
---

具体latex项目示例见github仓库[ECNU-Thesis-Proposal](https://github.com/yueneiqi/ECNU-Thesis-Proposal)，下面是一些踩坑的总结。

## 简单跨页长表格

使用[`mdframed`包](https://ctan.org/pkg/mdframed)适合实现下图可跨页的简单表格样式，且在单元格内依旧像正文一样，方便得调整样式和正常使用 `figure`,`enumerate`等功能，不需要额外的调整样式去匹配。这些是普通的表格 [`tabularx`](https://www.ctan.org/pkg/tabularx)和 跨页表格（[`longtable`](https://www.ctan.org/pkg/longtable)、 [`xltabular`](https://www.ctan.org/pkg/xltabular)）很难方便做到的。

![](https://s2.loli.net/2024/06/08/pect9Ck7XVxomzE.png)

示例

```LaTeX
\mdfdefinestyle{mymdfstyle}{
    everyline=true,
    linewidth=0.5pt,
    innerleftmargin=4pt,
    innerrightmargin=4pt,
    innertopmargin=-15pt,
    innerbottommargin=-10pt
}
\begin{mdframed}[
    style=mymdfstyle,
]

% 第一个单元格的内容

% 使用 \mdfsubtitle 添加横行，区分单元格
\mdfsubtitle[
    subtitleaboveline=true,
    subtitleabovelinewidth=0.8pt, % 经验值，肉眼看跟上面 linewidth = 0.5pt 粗细差不多
    subtitlebelowskip=0.2\baselineskip,
    subtitleinneraboveskip=0pt,
    subtitleinnerbelowskip=0pt,
]{}

% 第二个单元格的内容

\end{mdframed}
```

## 图片

### tikz画图初探

- [手册](https://www.bu.edu/math/files/2013/08/tikzpgfmanual.pdf)
- [mathcha](https://www.mathcha.io) - 在线画图工具
- [TikZ and PGF examples (texample.net)](https://texample.net/tikz/examples/)
- literature survey taxonomy tree：
  - [LaTreeX (lautgesetz.com)](https://lautgesetz.com/latreex/)-在线生成tree图片或latex源码网站
  - [How to Draw a Literature Survey Taxonomy Tree in Latex - Roy’s Blog](https://shantoroy.com/latex/Draw-literature-survey-tree-in-latex/)

### 强制图片位置

```LaTeX
\usepackage{float}

\begin{figure}[H]
...
\end{figure}
```

## 列表

简单列表

```text
一、第一项
二、第二项
三、第三项
```

```LaTeX
\begin{enumerate}[label=\chinese*、]
    \item 第一项
    \item 第二项
    \item 第三项
\end{enumerate}
```

多级列表

```text
- 开发组
  - 数据层 --- 前期2位同学，后期1位同学
      - 主要负责数据的采集、抽取以及入库等（需要了解neo4j）。
  - 前端 --- 1位同学
      - 主要负责功能界面的可视化（需要了解node.js）。
  - 后端 --- 1位同学
      - 主要负责业务逻辑。
- 研究组
```

```LaTeX
\begin{enumerate}
    \item 开发组
    \begin{enumerate}
        \item 数据层 --- 前期2位同学，后期1位同学
        \begin{itemize}
            \item 主要负责数据的采集、抽取以及入库等（需要了解neo4j）。
        \end{itemize}
        \item 前端 --- 1位同学
        \begin{itemize}
            \item 主要负责功能界面的可视化（需要了解node.js）。
        \end{itemize}
        \item 后端 --- 1位同学
        \begin{itemize}
            \item 主要负责业务逻辑。
        \end{itemize}
    \end{enumerate}
    \item 研究组
\end{enumerate}
```

每一级不同样式的多级列表

```text
第一章 第一级项目
  1.1 第二级项目
    1.1.1 第三级项目
    1.1.2 第三级项目
第二章 第一级项目
```

```LaTeX
\setlist[enumerate]{itemsep=0pt}
\setlist[enumerate,1]{label=第\chinese*章,itemindent=20pt}
\setlist[enumerate,2]{label=\arabic{enumi}.\arabic*,itemindent=-5pt}
\setlist[enumerate,3]{label=\arabic{enumi}.\arabic{enumii}.\arabic*}

\begin{enumerate}
    \item 第一级项目
    \begin{enumerate}
        \item 第二级项目
        \begin{enumerate}
                \item 第三级项目
                \item 第三级项目
        \end{enumerate}
    \item 第一级项目
    \end{enumerate}
\end{enumerate}

% 恢复默认
\setlist[enumerate]{}
\setlist[enumerate,1]{}
\setlist[enumerate,2]{}
\setlist[enumerate,3]{}
```



## 工具

- [overleaf](https://www.overleaf.com) - 在线编写latex网站，有很多[现成的模版](https://www.overleaf.com/latex/templates)，如[华师大毕业论文模版和ppt模版](https://www.overleaf.com/latex/templates?q=ecnu)；
- [Detexify](https://detexify.kirelabs.org/classify.html) - 根据鼠标画出来的形状去搜索相近的符号及其对应的latex代码。

## 后记

官方开题报告使用了一个跨页长表格来填充内容，也是开坑的起点。一开始基于[上海交大开题报告模板](https://github.com/NemoYuan2008/SJTU-Thesis-Proposal)，修改制作latex模版时，用了错误的[`xltabular`包](https://www.ctan.org/pkg/xltabular)，导致写的时候失去了latex自动化的优势，反而耗费了很大的功夫去全手动的写东西。四分之一时间在写报告，剩下的时间全在调格式debug。急急忙忙交完了报告后，回过头看，发现了非常合适的[`mdframed`包](https://ctan.org/pkg/mdframed)，重构了报告的正文部分，因此有了本文和[github仓库](https://github.com/yueneiqi/ECNU-Thesis-Proposal)。

## 参考

- [SJTU Thesis Proposal 上海交大开题报告模板](https://github.com/NemoYuan2008/SJTU-Thesis-Proposal)
