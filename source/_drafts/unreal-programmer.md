---
title: 成为一名 real 前端程序员
tags:
  - basic
---

## React

## Hexo

参考：

- [Hexo 文档](https://hexo.io/zh-cn/docs/)
- [从 0 到 1 开发 Hexo 主题杂谈](https://liuyib.github.io/2019/08/20/develop-hexo-theme-from-0-to-1/)
- [hexo-theme-stun](https://github.com/liuyib/hexo-theme-stun)

## Mix-in

原因： 在 JavaScript 中，我们只能继承单个对象。每个对象只能有一个 `Prototype`。并且每个类只可以扩展另外一个类。
这种单继承的设定会让人感受限制，mixin 被用来打破这种限制。
定义：mixin 是一个包含（可被其它类使用而无需继承的）方法的类。
换句话说，mixin 提供了实现特定行为的方法，但是我们不单独使用它，而是使用它来将这些行为添加到其它类中。

## styled-components

styled-components 是一个常用的 css in js 类库。和所有同类型的类库一样，通过 js 赋能解决了原生 css 所不具备的能力，比如变量、循环、函数等。诸如 sass&less 等预处理可以解决部分 css 的局限性，但还是要学习新的语法，而且需要对其编译，其复杂的 webpack 配置也总是让开发者抵触。而 styled-components 很好的解决了这些问题，很适合 React 技术栈的项目开发。

参考：

- [使用 styled-components 加速 React 开发](https://zhuanlan.zhihu.com/p/28876652)

## cli 脚手架

[打造前端团队的 React CLI 工具](https://segmentfault.com/a/1190000019069633)
[上文例子](https://github.com/pea-team/pea-cli)
[一步一步手写一个自己前端脚手架 cli 工具](https://juejin.cn/post/6844904045845577742)
