---
title: React
date: 2021-04-26 09:41:03
tags:
  - react
  - basic
---

## 原因

其实我一开始搞的是 vue ，但是实习面试问技术栈都说用的 react 。
那就开始吧！

## 开始

先来看下 [MDN-React_getting_started](https://developer.mozilla.org/zh-CN/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_getting_started) 相关文档入个门。

先看基础概念

- JSX
  JSX 是 javascript 的一个语言扩展，简单来说，就是 js+html，js 变量嵌在 html 里，用以变量值显示或逻辑控制。类似模版语言，但它具有 js 的全部功能。

  JSX 可以生成 React “元素”，可将这些元素渲染为 DOM。
  在`{}`内可以放置任意有效的 js 表达式。例如，2 + 2，user.firstName 或 formatName(user) 都可。
  注：可将 JSX 拆分为多行，建议将内容包裹在括号中，可以避免遇到自动插入分号陷阱。

```javascript
const name = "Josh Perez";
const element = <h1>Hello, {name}</h1>;
```

```js
function formatName(user) {
  return user.firstName + " " + user.lastName;
}

const user = {
  firstName: "Harper",
  lastName: "Perez",
};

const element = <h1>Hello, {formatName(user)}!</h1>;
```
