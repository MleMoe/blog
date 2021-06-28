---
title: webAssembly
date: 2021-06-14 18:26:14
layout: slide
slide:
  separator: ---
  separator_vertical: '~~'
  data_separator_notes: '^Note:'
  theme: white
  config:
    history: true
    mouseWheel: true
---

WebAssembly

---

定义

~~

webassembly.org

a **binary instruction format** for a stack-based virtual machine.

a portable **compilation target** for programming languages, enabling deployment on the web for client and server applications.

~~

一种字节码，一个编译目标

Note: 所以，对于 Web 而言，WebAssembly 具有很大的意义——它提供了一条途径，以使得以各种语言编写的代码都可以以接近原生的速度在 Web 中运行。

~~
![format](/images/wasm/wasm.webp)

---

为什么会有 Wasm ？

~~

在 web 端提高性能

~~

JS 在引擎中的执行过程，以 V8 为例

![v8-js](/images/wasm/v8-js.png)

Note: 源码 --> AST --> 字节码 --> 机器码 - 解析器 Parser 把 JS 代码转化成抽象语法树 AST - 解释器 Ignition 将 AST 转化成字节码 ByteCode - 执行 ByteCode 及优化

~~

JS 执行优化

![v8-js](/images/wasm/optimization.png)

Note: 在解释执行字节码的过程中，标记重复执行的热点代码，然后把该代码发送给编译器 TurboFan，编译为更高效的机器码储存起来，等到下次再执行到这段代码时，就会用现在的机器码替换原来的字节码进行执行，这样大大提升了代码的执行效率。另外，当 TurboFan 判断一段代码不再为热点代码的时候，会执行去优化的过程，把优化的机器码丢掉，然后执行过程回到 Ignition。

~~

JavaScript: 动态类型语言

变量上一秒可能是 Array，下一秒就变成了 Number。
上一次引擎所做的优化，就失去了作用

为了解决这个问题，带有类型的 TypeScript 出现了。

~~

TS 对类型的问题做的再好，也始终逃不过要经过 解析器 和 解释器，而 Wasm 不用经过这两步

~~

这就是为什么 Wasm 比 TS 快 + 为什么还会有 Wasm 的原因。

---

编译

C/C++/Rust/Go/Ts -> Wasm

~~

Emscripten: C/C++ -> Wasm

需要： cmake + Emscripten

~~

AssemblyScript: TS -> Wasm

TS 写法需要有一定限制原则：

- 不能用联合类型
- 不能用 any 和 undefined、枚举类型
- ...

---

WASI

Wasm -> outside the web

---

运行效率比较(1,000,000,000 次计算)

js: 1020 ms
ts: 975 ms
emscripten(c/c++): 1 ms
assemblyScript(ts): 2149 ms

~~

辣鸡代码
https://github.com/MleMoe/wasm-start
