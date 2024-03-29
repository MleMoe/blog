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

5 分钟分享：WebAssembly

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

1995 年，Javascript 语言创建，速度非常缓慢
![js-time](/images/wasm/js-time.webp)

~~

2008 年，谷歌 Chrome，V8 引擎和 JIT 即时编译
![js-time](/images/wasm/jit-time.webp)

~~

2015 年，WebAssembly 发布
![js-time](/images/wasm/wasm-time.webp)

~~

JS 在引擎中的执行过程，以 V8 为例

源码 --> AST --> 字节码 --> 机器码

![v8-js](/images/wasm/v8-js.png)

~~

JS 执行优化

![v8-js](/images/wasm/optimization.png)

~~

- 在解释执行字节码的过程中，标记重复执行的热点代码，然后把该代码发送给编译器 TurboFan，编译为更高效的机器码储存起来
- 等到下次再执行到这段代码时，就会用现在的机器码替换原来的字节码进行执行，这样大大提升了代码的执行效率。
- 另外，当 TurboFan 判断一段代码不再为热点代码的时候，会执行去优化的过程，把优化的机器码丢掉，然后执行过程回到 解释执行。

~~

JavaScript: 动态类型语言

变量上一秒可能是 Array，下一秒就变成了 Number。
上一次引擎所做的优化，就失去了作用

为了解决这个问题，带有类型的 TypeScript 出现了。

~~

TS 对类型的问题做的再好，也要经过 解析器 和 解释器，而 Wasm 不用经过这两步

~~

这就是为什么 Wasm 比 TS 快 + 为什么还会有 Wasm 的原因。

---

WASM 的使用

C/C++/Rust/Go/Ts -> Wasm

以 C/C++为例

~~

Emscripten: C/C++ -> Wasm

准备工具： cmake + Emscripten

~~

- cmake

  - 方法 1: [官网](https://cmake.org/)下载
  - 方法 2: `mac brew cmake`

~~

- Emscripten

Emscripten 是使用 LLVM 构建的工具链，可将源代码编译为 WebAssembly。

```bash
git clone https://github.com/juj/emsdk.git
cd emsdk
./emsdk install latest
./emsdk activate latest

# 环境变量配置（终端会提示）
# on Linux or Mac OS X，在当前终端窗口短暂配置 PATH 等变量
source ./emsdk_env.sh
# 永久配置则将环境变量添加至 .bashrc 或 .zshrc
echo 'source "略/emsdk/emsdk_env.sh"' >> $HOME/.zshrc
```

~~

#### 准备 C 源文件

`EMSCRIPTEN_KEEPALIVE` 表示这个函数需要导出

```C
#include <emscripten/emscripten.h>

#ifdef __cplusplus
extern "C"
{
#endif

int EMSCRIPTEN_KEEPALIVE add(int num) {
  int s = 0;
  for (int i = 1; i <= num; i++) {
      s = s+1;
  }
  return s;
}

#ifdef __cplusplus
}
#endif

```

~~

#### build

CMakeLists.txt

```txt
cmake_minimum_required(VERSION 2.8)
project(WASM)

set( CMAKE_CXX_STANDARD 11 )

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif(NOT CMAKE_BUILD_TYPE)

set(EMSCRIPTEN_LINK_FLAGS "${EMSCRIPTEN_LINK_FLAGS} -s EXTRA_EXPORTED_RUNTIME_METHODS=['ccall','cwrap']")

add_executable(add src/add.cc)
set_target_properties( add PROPERTIES LINK_FLAGS "${EMSCRIPTEN_LINK_FLAGS} --bind" )

```

~~

编译脚本
运行 即可编译出 wasm 文件了。

emcmake 和 emmake 是 Emscripten 的编译工具，类似于 cmake 和 make

```bash

if [ ! -d "build" ]; then
  mkdir build
fi
cd build
emcmake cmake ..
emmake make

```

~~

#### 使用

```js
// 在引入 assemblyCpp/build/add.js 后，在 脚本中执行
const num = 1000000000;
Module.onRuntimeInitialized = function () {
  // const start = new Date().getTime();
  const resultEM = Module._add(num);
  // const result = Module.ccall('add', 'number', ['number'], [num]);
  // const end = new Date().getTime();
  // console.log('em 用时：', end - start, 'ms');
  // console.log('em 结果：', resultEM);
};
```

~~

#### 效率比较

运行效率比较(1,000,000,000 次计算)

- js: 1020 ms
- ts: 975 ms
- emscripten(c/c++): 1 ms

~~

#### 代码

[github repo: wasm-start](https://github.com/MleMoe/wasm-start)

---

## 其它

~~

## Web 标准

在 2019 年，W3C 使 WebAssembly 成为了新的 Web 标准。

~~

### assemblyScript

ts -> wasm

~~

Wasm 优点：

- 安全：在沙盒中安全运行不受信任的代码
- 开放：以一种通用的方式将程序与环境互通
- 可移植：与架构无关
- 多语言：由于能够从多种语言中编译出来

~~
Wasm 是中间表示，不同语言编写的代码编译成 Wasm 后可以在各种平台上运行

Wasm -> outside the web

WASI: the WebAssembly system interface
