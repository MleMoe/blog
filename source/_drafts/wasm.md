---
title: Wasm
date: 2021-06-14 19:38:46
tags:
---

## Wasm 是什么

Wasm 是 WebAssembly 的缩写，WebAssembly 定义如下：

webassembly.org：

> WebAssembly (abbreviated Wasm) is a binary instruction format for a stack-based virtual machine. Wasm is designed as a portable compilation target for programming languages, enabling deployment on the web for client and server applications.

MDN：

> WebAssembly 是一种运行在现代网络浏览器中的新型代码，并且提供新的性能特性和效果。它设计的目的不是为了手写代码而是为诸如 C、C++和 Rust 等低级源语言提供一个高效的编译目标。
> 对于网络平台而言，这具有巨大的意义——这为客户端 app 提供了一种在网络平台以接近本地速度的方式运行多种语言编写的代码的方式；在这之前，客户端 app 是不可能做到的。

## Wasm 由来

JS 在引擎中的执行过程，以 Chrome 和 Node 都在用的 V8 引擎为例。
![v8-js](/images/wasm/v8-js.png)

JavaScript 文件在浏览器中被下载下来后，在 V8 引擎中经历了什么呢？简单来说 V8 引擎通常会经过一下流程：源码 --> AST --> 字节码 --> 二进制代码

- 解析器 Parser 把 JS 代码转化成抽象语法树 AST
- 解释器 Ignition 将 AST 转化成字节码 ByteCode
- 执行 ByteCode 及优化
  在解释执行字节码的过程中，标记重复执行的热点代码，然后把该代码发送给编译器 TurboFan，编译为更高效的机器码储存起来，等到下次再执行到这段代码时，就会用现在的机器码替换原来的字节码进行执行，这样大大提升了代码的执行效率。另外，当 TurboFan 判断一段代码不再为热点代码的时候，会执行去优化的过程，把优化的机器码丢掉，然后执行过程回到 Ignition。

![optimization](/images/wasm/optimization.png)

但是由于 JavaScript 是一种动态类型语言，变量上一秒可能是 Array，下一秒就变成了 Number。那么上一次引擎所做的优化，就失去了作用，此时又要再一次进行优化。

为了解决这个问题，带有类型的 TypeScript 出现了。
可是 TS 对类型的问题做的再好，也始终逃不过要经过 Parser 和 Ignition，这两步是 JavaScript 代码在引擎执行过程当中消耗时间最多的两步。而 Wasm 不用经过这两步，这就是为什么 Wasm 比 TS 快 + 为什么还会有 Wasm 的原因。

WASI
[Standardizing WASI: A system interface to run WebAssembly outside the web](https://hacks.mozilla.org/2019/03/standardizing-wasi-a-webassembly-system-interface/)

参考：
https://zhuanlan.zhihu.com/p/158586853

[ts->wasm](https://hacks.mozilla.org/2019/03/standardizing-wasi-a-webassembly-system-interface/)
