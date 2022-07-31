---
layout: post
title: "C++: 避免使用单例模式"
date: 2022-05-23
author: 玉树
categories: [C++]
tags: [C++, Singleton, Design Pattern]
comments: true
toc: true
---

### 避免使用单例模式：全局变量

避免传统单例模式的代码实现，但注意单例的抽象方法是可取的。

理由：

- 全局变量
- 不利于单元测试
- 单例模式的滥用

单例模式本质上是一个大号的全局变量。全局变量没有访问限制，因为它没有所有权，这导致程序员很难推断全局变量的状态，当你调用基于一个全局变量函数时，可能另一个实例在你没有意识到的情况下，也调用了另一个基于该全局变量的函数。没有所有权也带来了另一个问题——全局变量的构造顺序不被标准所决定。换句话说，在不同文件的非局部变量的初始化顺序和析构顺序是未定义的（undefined），这个问题也被称为Static initialization order fiasco。

单例模式也会掩盖依赖关系，使单元测试变得复杂，我们很难去`mock`一个单例模式产生的对象，夸张点说，使用了单例模式就没有单元可言。Mihai在博客中也提到一个例子，实际生产中，往往不止一个对象可以作为单例，比如日志和文件系统，他们之间还有依赖关系。日志需要文件系统打开文件去记录信息，而文件系统需要日志记录打开了哪些文件。在处理这些情况时，单例模式是很棘手的。

Rainer在书中提到了单例模式不好的声誉也来自于程序员对它的滥用，很多人仅仅是为想要运用设计模式而应用单例模式，以至于会出现整个程序都是单例模式的情况。

### 替代方法

1. 利用命名空间
2. 依赖注入模式
3. 异步回调

#### 1.利用命名空间

一个替代单例模式的思路是将类中所以的函数和数据成员都变成`static`，但这样有些过度设计了，可以直接用命名空间来代替类，这样从类的写法：

```cpp
class Manager
{
public:
    static int blimp_count();
    static void add_more_blimps(int);
    static void destroy_blimp(int);
private:
    static std::vector<Blimp> blimps;
    static void deploy_blimp();
};
```

转变成：

```cpp
namespace Manager
{
    int blimp_count();
    void add_more_blimps(int);
    void destroy_blimp(int);
}
```

#### 2.使用依赖注入模式

使用依赖注入（dependency injection）模式来替代，更详细的介绍看Marc（2021）在书中的代码，这个例子中`ILogger`的实例通过`Foo`的构造函数被注入到类中：

```cpp
class Foo
{
public:
    explicit Foo(ILogger& logger): m_logger { logger } {}
    void doSomething()
    {
        m_logger.log("Hello dependency injection!", ILogger::LogLevel::Info);
    }
private:
  ILogger& m_logger;
}
```

当`Foo`的实例被创建时，一个`ILogger`实例就会被注入到其中：

```cpp
Logger concreteLogger { "log.out"};
concreteLogger.setLogLevel(ILogger::LogLevel::Debug);

Foo f { concreteLogger };
f.doSomething();
```

#### 3.改进：利用异步回调的方式

[Mihai(2018)在博客中](https://www.fluentcpp.com/2018/03/06/issues-singletons-signals/)提到受boost和qt信号启动的异步回调的方法：

```cpp
class Logger
{
public:
    void Log(std::string const& message)
    {
        std::cout << message << '\n';
    }
};

class Debugger
{
public:
    void Update()
    {
        sigLog.emit("Debugger updating")
    }

    Signal<void(std::string)> sig_Log;
};

class Profiler
{
public:
    Profiler::Profiler()
    void Update()
    {
        sig_Update.emit()
        sig_Log.emit("Profiler updating")
    }

    Signal<void> sig_Update;
    Signal<void(std::string)> sig_Log;
};

class Game
{
public:
    Game()
        : m_logger()
        , m_debugger()
        , m_profiler()
        , m_debuggerLoggerConnection(m_debugger.sig_Log.connect(&Logger::Log, m_logger))
        , m_profilerLoggerConnection(m_profiler.sig_Log.connect(&Logger::Log, m_logger))
        , m_profilerDebuggerConnection(m_profiler.sig_Update.connect(&Debugger::Update, m_debugger))
    {}

    void Update()
    {
        m_profiler.Update();
    }

private:
    Logger m_logger;
    Debugger m_debugger;
    Profiler m_profiler;

    Connection m_debuggerLoggerConnection;
    Connection m_profilerLoggerConnection;
    Connection m_profilerDebuggerConnection;
};
```

### 需要单例的话：c++11的实现

如果需要使用单例模式的话，可以选择利用c++11 `magic statics`特性实现的`Meyers Singleton`：

```cpp
 #include <iostream>
 #include <cassert>
 ​
 class Singleton
 {
 public:
     static Singleton& Instance()
     {
         static Singleton instance;
         return instance;
     }
 public:
     Singleton(const Singleton&) = delete;
     Singleton(Singleton&&) = delete;
     Singleton& operator=(const Singleton&) = delete;
     Singleton& operator=(Singleton&&) = delete;
 private:
     Singleton() = default;
     ~Singleton() = default;
 };
 ​
 int main()
 {
     auto& a = Singleton::Instance();
     auto& b = Singleton::Instance();
     assert(&a == &b);
 ​
     return 0;
 }
```

magic statics的特性保证即使在多线程的情况下，`test`只会被初始化一次，然后这个实例会在`main()`函数返回之后被摧毁(在static destruction stage时)。

[Core Guidline 提到一个exception](http://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#i3-avoid-singletons)，就是将上文中的`Meyers Singleton`：

```cpp
X& myX()
{
    static X my_x {3};
    return my_x;
}
```

#### Less simple solution

Core Guidline 也提到当`X` 的析构涉及到需要同步行为时，我们必须使用一个复杂些的解决方案：

```cpp
X& myX()
{
    static auto p = new X {3};
    return *p;  // potential leak
}
```

这样做的话，我们需要使用线程安全的方法手动`delete`掉这个对象。因为容易出错，所以使用这个方法有几个前提条件：

- `myX`是多线程的代码
- `X`需要被摧毁（比如它需要释放资源），并且
- `X`的析构函数需要同步执行

TODO（不理解）:
If you, as many do, define a singleton as a class for which only one object is created, functions like `myX` are not singletons, and this useful technique is not an exception to the no-singleton rule.

<br>

【后注一】对于static member的初始化，C++标准保证，在同一个编译单元内的static member、global variable的静态初始化先于动态初始化。但是同为静态初始化或者同为动态初始化的时候，是按照其定义的顺序来进行的。 静态初始化是指zero initialization或者是用常量表达式初始化的情况，除此以外的都为动态初始化。  C++标准3.6.2节有相关描述。

<br>

**参考资料：**

1. [Modern C++ Singleton Template - Code Review Stack Exchange](https://codereview.stackexchange.com/questions/173929/modern-c-singleton-template)
2. [The Issues With Singletons and How to Fix Them - Fluent C++ (fluentcpp.com)](https://www.fluentcpp.com/2018/03/06/issues-singletons-signals/)
3. [C++ Core Guidelines: aovid singletons](http://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#i3-avoid-singletons)
4. [shared_ptr和unique_ptr可否用于单例模式？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/333710483/answer/2297774354)
5. Marc Gregoire - Professional C++ - Wiley (2021) 第1106 - 1108页
6. Meyers, S, 1998. Effective C++. Reading, MA: Addison-Wesley
7. [What are drawbacks or disadvantages of singleton pattern? - Stack Overflow](https://stackoverflow.com/questions/137975/what-are-drawbacks-or-disadvantages-of-singleton-pattern)
8. [C++中多线程与Singleton的那些事儿 - origins - 博客园 (cnblogs.com)](https://www.cnblogs.com/liyuan989/p/4264889.html)
9. Rainer Grimm - C++ Core Guidelines Explained_Best Practices for Modern C++ - Addison-Wesley (2022)
