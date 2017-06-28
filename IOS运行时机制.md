---
title: IOS运行时机制
date: 2017-02-20 12:00:33
tags:
- IOS
- 面试
- Runtime
category: Swift
keywords:
- IOS
- 面试
- Runtime
---

Runtime，运行时机制，是面试时用到比较多的概念，在理解面向对象以及实际开发中也有重要的作用，这里用概念和代码的方式记录运行时机制的简单理解和应用。

<!-- more -->
<!-- toc -->

# Runtime简介
Runtime让Object-C和Swift成为了动态语言，实现了让编译和链接的工作放到了运行时，Runtime是一套C和汇编语言的API库，用结构体表示类和对象，用函数表示方法，对象与对象之间，对象与方法之间使用消息机制，这样让这两门语言有了面向对象能力。

# Runtime里装着什么？
为了深入的理解Runtime，有必要打开Runtime的源代码一看究竟，




