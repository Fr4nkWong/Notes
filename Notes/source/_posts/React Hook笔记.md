---
title: React Hook笔记
date: 2018-11-19
categories:
  - front-end
---

## 前言

## Hook
### 什么是Hook
### 怎么使用Hook
- 状态变量声明：
- 状态变量读取：
- 状态变量更新：
- 一方面它是直接用在function当中，而不是class；另一方面每一个hook都是相互独立的，不同组件调用同一个hook也能保证各自状态的独立性。这就是两者的本质区别了；
- react是根据useState出现的顺序来定的；因此必须把hooks写在函数的最外层，不能写在ifelse等条件语句当中，来确保hooks的执行顺序一致；
- useEffect钩子：等价于componentDidMount、componentDidUpdate、componentWillUnmount的集合体。
## Hook与其他写法的比较

## 小结
