---
layout: post
title: "linux-system()函数笔记"
date: 2023-11-01
author: 玉树
categories: [Linux]
tags: [Linux]
comments: true
toc: true
---

linux-system()函数 小结：

- system执行命令的期间主进程收到的SIGINT和SIGQUIT会被忽略（posix.1规定），原因是posix不希望主进程响应system子进程的SIGINT和SIGQUIT信号
- 非阻塞版本：`popen()`
- Do not use **system**() from a program with set-user-ID or set-group-ID privileges
- 返回值多，小心处理
- 执行过程：fork ()->exec ()->waitpid ()
- 子进程是父进程的副本，可能会增加内存消耗

使用建议：

1、建议 system () 函数只用来执行 shell 命令，因为一般来讲，system () 返回值不是 0 就说明出错了；

2、建议监控一下 system () 函数的执行完毕后的 errno 值，争取出错时给出更多有用信息；

3、建议考虑一下 system () 函数的替代函数 popen ()；

## 风险

### **安全问题**

在编写具有SUID/SGID权限的程序时请勿使用system函数，system函数会继承环境变量，通过环境变量可能会造成系统安全的问题。

> Do not use **system**() from a program with set-user-ID or set-group-ID privileges, because strange values for some environment variables might be used to subvert system integrity. Use the [***exec***](https://linux.die.net/man/3/exec)*(3)* family of functions instead, but not [***execlp***](https://linux.die.net/man/3/execlp)*(3)* or [***execvp***](https://linux.die.net/man/3/execvp)*(3)*. **system**() will not, in fact, work properly from programs with set-user-ID or set-group-ID privileges on systems on which */bin/sh* is bash version 2, since bash 2 drops privileges on startup. (Debian uses a modified bash which does not do this when invoked as **sh**.)

**详解：**

SUID 是 Set User ID，SGID 是 Set Group ID的意思。在 Linux 系统中，这两种权限主要用于控制文件访问。当一个程序被设置了 SUID 或 SGID 权限后，用户在执行该程序时，会临时获得该程序文件的所有者或所属组的权限¹²⁷⁸。

SUID/SGID 权限主要应用在二进制可执行文件上¹²。例如，当普通用户需要修改自己的密码时，他们需要访问 `/etc/shadow` 文件，但这个文件通常只有 root 用户才有读写权限。此时，如果 `passwd` 命令（用于修改密码）被设置了 SUID 权限，并且该命令的所有者是 root 用户，那么普通用户在执行 `passwd` 命令时就会临时获得 root 用户的权限，从而可以修改 `/etc/shadow` 文件¹²。

然而，在编写具有 SUID/SGID 权限的程序时使用 `system` 函数可能会带来安全问题。因为 `system` 函数会继承环境变量，攻击者可能通过修改环境变量来执行恶意命令。例如，攻击者可以通过修改 `PATH` 环境变量，使得具有 SUID/SGID 权限的程序在调用 `system` 函数执行某个系统命令时，实际上执行的是攻击者提供的恶意程序。因此，为了避免这种潜在的安全风险，我们通常建议在编写具有 SUID/SGID 权限的程序时避免使用 `system` 函数。⁹

参考Bing 2023/11/1：

(2) linux：SUID、SGID详解 - 知乎. https://zhuanlan.zhihu.com/p/166468556. (3) Linux中文件特殊权限suid、sgid、sticky（有图详细讲解 .... https://blog.csdn.net/MssGuo/article/details/120754324. (4) Linux特殊权限之SUID - 简书. https://www.jianshu.com/p/4ea1e6aa2aa7. (5) Linux SetGID（SGID）文件特殊权限用法详解 - C语言中文网. https://c.biancheng.net/view/870.html. (6) 具有 SUID/SGID 权限的命令运行状态-CSDN博客. https://blog.csdn.net/wangzengdi/article/details/32173353. (7) LINUX中suid/guid指什么？-CSDN博客. [https://bing.com/search?q=SUID%2FSGID权限是什么意思](https://bing.com/search?q=SUID/SGID权限是什么意思). (8) Linux 特殊权限 SUID,SGID,SBIT - sparkdev - 博客园. https://www.cnblogs.com/sparkdev/p/9651622.html. (9) LINUX中suid/guid指什么？-CSDN博客. https://blog.csdn.net/chimu9074/article/details/100605347. (10) LINUX中suid,sgid是用来干嘛的？怎么使用？ - 百度知道. https://zhidao.baidu.com/question/189879510.html.

### 案例-SIGCHLD 信号处理方式变更导致`"No child processes"`

[【C/C++】Linux下system()函数引发的错误 - 恋恋美食的个人空间 - OSCHINA - 中文开源技术交流社区](https://my.oschina.net/renhc/blog/54582)

## 相关知识

### **为何会阻塞SIGCHLD 信号**

在Linux中`fork`的子进程结束后，内核会向其父进程发送`SIGCHLD`信号，即`system`函数的调用者，`system`源码中父进程是调用`waitpid`函数来为变成僵尸的子进程进行回收，获得其结束状态，然后将这个结束状态返回给`system`函数的调用者。

所以我们在调用`system`之前不能去注册`SIGCHLD` 信号的处理函数，如在信号处理函数中进行`wait`等操作，`system`函数中的 `waitpid`将可能获取不到子进程正确返回状态，因为子进程可能被信号处理函数抢先回收；

以下是在实际项目中封装好的`system`模板函数：

```c
typedef void (*sighandler_t)(int);
int os_system(const char *cmd)
{
   int ret = 0;
   sighandler_t old_handler;

   old_handler = signal(SIGCHLD, SIG_DFL);
   ret = system(cmd);
   signal(SIGCHLD, old_handler);

   return ret;
}
```

`old_handler =signal(SIGCHLD, SIG_DFL);` 在调用`system`前将`SIGCHLD`信号的处理函数注册为`SIG_DFL`，在Linux 中`SIG_DFL`的作用就是忽略的意思;

`signal(SIGCHLD, old_handler);`在调用完后将 SIGCHLD的处理函数恢复成调用前的处理函数。

### SIGCHLD

SIGCHLD 是一个在 Unix-like 系统中的信号，当一个进程结束或者停止时，会发送 SIGCHLD 信号给其父进程。默认情况下，这个信号会被忽略。但是，如果父进程希望被告知其子进程的状态变化，那么它应该捕获这个信号¹。

"SIGCHLD 将被阻塞"是指 SIGCHLD 信号被暂时挂起，不会立即送达到父进程。在 Unix-like 系统中，可以通过信号掩码来阻塞特定的信号。当一个信号被阻塞时，即使该信号产生了，也不会立即送达到进程，而是处于未决状态。只有当该信号被解除阻塞后，才会送达到进程⁴。

例如，在某些情况下，我们可能希望在父进程完成对 SIGCHLD 信号处理函数的设置之前阻塞 SIGCHLD 信号。这样可以确保在所有子进程结束之前，父进程能够完成对 SIGCHLD 信号的处理函数的设置。否则，如果所有子进程都结束了，而父进程还没有完成设置，则此时的 SIGCHLD 信号都会被忽略，从而导致子进程不能被父进程回收¹。

参考Bing 2023/11/1： (1) SIGCHLD信号-CSDN博客. https://blog.csdn.net/qq_33883085/article/details/89325396. (2) system阻塞SIGCHLD信号原因_sigchld system-CSDN博客. https://blog.csdn.net/wwlhsgs/article/details/50721681. (3) SIGCHLD信号详解 - 掘金. https://juejin.cn/post/7245145631181996090. (4) linux下wait/waitpid处理僵死进程详解(SIGCHLD信号) - 知乎. https://zhuanlan.zhihu.com/p/272700092.

### fork不意味着子进程会消耗和父进程一样多的物理内存

在 Unix-like 系统中，`fork()` 函数会创建一个新的进程，这个新进程是父进程的一个副本。但是，这并不意味着子进程会消耗和父进程一样多的物理内存¹²³⁴。

实际上，大多数现代的 Unix-like 系统（包括 Linux）在实现 `fork()` 函数时，采用了一种称为写时复制（Copy-On-Write，简称 COW）的技术来优化内存使用⁵。当调用 `fork()` 函数创建子进程时，子进程的用户级虚拟地址空间会和父进程共享相同的物理页帧¹²³⁴。也就是说，父进程和子进程在内存中的代码段、数据段、堆段和栈段都指向相同的物理内存⁵。

只有当父进程或子进程试图修改某个共享页面时，系统才会为修改操作的进程创建该页面的一个副本⁵。这样，每个进程都有自己独立的地址空间，而且只有在必要时才会复制内存页面，从而有效地节省了物理内存⁵。

所以，虽然 `fork()` 会创建一个新的进程，但并不会立即消耗和父进程一样多的物理内存¹²³⁴⁵。

参考Bing 2023/11/1： (1) 进程系统调用——fork函数深入理解(代码演示) - 知乎. https://zhuanlan.zhihu.com/p/422928238. (2) fork（）函数 - 知乎. https://zhuanlan.zhihu.com/p/422099192. (3) 【Linux】关于理解fork()函数的简单例子 - 知乎. https://zhuanlan.zhihu.com/p/91616048. (4) Linux中fork函数的作用及用法-CSDN博客. https://blog.csdn.net/weixin_40853073/article/details/81873398. (5) fork之后父子进程的内存关系_父进程 fork子进程 内存-CSDN博客. https://blog.csdn.net/shreck66/article/details/47039937.

## 参考

1. Bing AI
2. [Linux下的system函数应该这样用 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/563194375)
3. [🌟][宏伟精讲·linux system()函数完全解密 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/457019360)
4. [linux：SUID、SGID详解 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/166468556)
