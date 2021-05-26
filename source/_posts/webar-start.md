---
title: WebAR 起步
date: 2021-05-11 21:20:04
tags:
  - webar
  - vite
  - vue
  - gh-pages
  - 教程
---

这是一个简单的 WebAR 新手教程，它将指导如何使用 WebXR Device API + Vite + Vue 创建自己的第一个 WebAR demo，并通过 github 的 gh-pages 进行部署。

## 最终纪念

跟随教程，你最终会得到类似以下物品：

- 代码：[github repo](https://github.com/MleMoe/webar-vue-demo)
- 展示：[demo](https://mlemoe.github.io/webar-vue-demo/)

~~作用可以是，像我在社区打疫苗排队时，给阳光暴晒的路面种向日葵~~
![🌻](/images/sunflower.jpeg)

## 开发准备

- bash 或 zsh
- Node.js
- npm 或 yarn 或 pnpm
- vue
- vite
- vscode
- git
- 一个 github 账户

## 基础知识

### 什么是 WebAR ？

AR 是介于 VR 和现实之间的视觉技术，VR 全是虚拟，AR 则为实+虚。
所以 AR 被定义为一种将虚拟对象与 3D 环境实时集成并支持交互的技术。
简单来说就是在真实场景内放置虚拟的物体，用户通过眼镜或其它显示设备观看该结合场景，或进行一些交互。

WebAR 则是在 Web 上实现 AR 的一种方式。

### 目前状况

只考虑单目相机（也就是普通相机）

- 老前辈 AR.js

  - ARToolKit，1999 年，第一个 AR SDK，由[华盛顿大学](http://hitl.washington.edu/artoolkit/)开发。依照图形的仿射不变性原理，仅支持基准标记，因为它通过识别特定平面 marker 对相机位姿追踪，仅涉及简单的矩阵运算，所以速度极快。
  - JSARToolKit5，基于 ARToolKit 实现
  - AR.js，2017 年
    AR.js 是一个基于 Three.js 和 JSARToolKit5 的 Web AR 解决方案，它可以在所有平台上以及具有 WebRTC 和 WebGL 的任何浏览器上运行。
    若有兴趣，可看 [AR.js-Examples](https://stemkoski.github.io/AR-Examples/)
  - 目前除实现特定 marker 外，还支持自然特征（即图像）和 location，但对 markerless 的支持不够，不过这也是它的基本特性（支持基准标记）决定的。

- three.ar.js
  基于 [WebARonARKit](https://github.com/google-ar/WebARonARKit)（ios ARKit） 和 [WebARonARCore](https://github.com/google-ar/WebARonARCore)（Android ARCore），分别为 google 在 ARKit 和 ARCore 基础上实现的 web 封装。
  就是谷歌+手机 AR 套件+three.js
  项目地址：[google-ar/three.ar.js](https://github.com/google-ar/three.ar.js)
  最新的更新是在三年前，很明显谷歌鸽了大家

  > 有老哥在 three.ar.js 仓库提 issue [#127](https://github.com/google-ar/three.ar.js/issues/127)
  > is it depreacated?
  > I guess it is

- 正在进行中的 [W3C WebXR Device API 工作组](https://www.w3.org/immersive-web/)
  WebXR，即 WebVR + WebAR。
  该工作组志在描述访问 Web 上的 VR 和 AR 设备的支持规范。即使用 Web 接口，用 Web 编程的方式，让在 Web 的 WebXR 设备运行 XR。
  目前支持设备：
  支持 AR Core 的安卓手机，安卓版 chrome 浏览器（版本 81+），或 chrome canary。
  需在地址栏输入 chrome://flags，搜索 webxr，找到 WebXR incubations，设置为启用状态

我们这里选用第三种，因为是 markerless，且是面向未来的 Web 规范。

## 开发

### 项目结构

vite + vue 项目起步具体介绍可参考云游君同学的 [vue-d3-demo](https://www.yunyoujun.cn/posts/vue-d3-demo/)，这里只按步执行命令。

首先，初始化 vite 项目。

```bash
npm init vite-app webar-vue-demo
# or
# yarn create vite-app webar-vue-demo

cd webar-vue-demo

# 安装依赖
npm install
# or
# yarn

# 启动项目，默认地址为 http://localhost:3000
npm run dev
# or
# yarn dev

```

如此，项目框架就搭起来了。

### 开发范式

先来介绍一下使用 WebXR Device API 的应用程序遵循类似的使用模式：

- 调用`navigator.xr.isSessionSupported(mode)`以确定设备是否支持该 XR 内容类型，并告知用户结果

  ```js
  const supported = await navigator.xr.isSessionSupported("immersive-ar");
  if (supported) {
    // 'immersive-ar' sessions may be supported.
    // Page should advertise support to the user.
  } else {
    // 'immersive-ar' sessions are not supported.
  }
  ```

- 用户点击开始按钮（即激活事件），使用`navigator.xr.requestSession(mode, options)` 请求 `XRSession`，
- 使用 `XRSession` 运行帧循环以响应 XR 输入，并生成图像以作为响应显示在 XR 设备上。
- 继续运行帧循环。直到浏览器关闭会话，或用户指示要退出 XR 内容为止。

### 辅助资源

在 src 路径下新建 webar 文件夹，放置由 [Immersive Web at W3C](https://github.com/immersive-web) 提供的 webxr 的辅助工具，可以省去我们的一些功夫。
在 componets 路径下新建 BasicAR.vue 组件，在 App.vue 中使用该组件。

在 public 下新建 gltf 文件夹，放置用到的标线和向日葵模型。

其上文件可在文档开始放出的 github 仓库里获取。

### code

components/BasicAR.vue

```js
<template>
  <header>
    <h1>Web AR 测试</h1>
    <p>需往四周移动摄像头，等待数秒，以学习周围环境知识。</p>
    <p>
      【支持设备】：安卓 chrome 浏览器（版本81+）或 chrome canary。 搜索
      chrome://flags，将 WebXR incubations，设为启用
    </p>
    <div id="btn-container"></div>
  </header>
</template>
<script>
import { WebXRButton } from "../webar/js/util/webxr-button.js";
import { Scene } from "../webar/js/render/scenes/scene.js";
import {
  Renderer,
  createWebGLContext,
} from "../webar/js/render/core/renderer.js";
import { Node } from "../webar/js/render/core/node.js";
import { Gltf2Node } from "../webar/js/render/nodes/gltf2.js";
import { DropShadowNode } from "../webar/js/render/nodes/drop-shadow.js";
import { vec3 } from "../webar/js/render/math/gl-matrix.js";
export default {
  name: "BasicAR",
  mounted() {
    let xrButton = null; // 提示按钮
    let xrRefSpace = null; // 用于绘制的参考空间，the XRReferenceSpace can be used to establish a spatial relationship with the user’s physical environment.
    let xrViewerSpace = null; // 用于命中测试的参考空间
    let xrHitTestSource = null; // 命中测试控制句柄

    let gl = null;
    let renderer = null;
    let scene = new Scene();
    scene.enableStats(false);

    let arObject = new Node(); //node，用于放置花节点
    arObject.visible = false;
    scene.addNode(arObject);
    let flower = new Gltf2Node({ url: "gltf/sunflower/sunflower.gltf" }); // 花
    arObject.addNode(flower);
    let shadow = new DropShadowNode(); // 给花group创造阴影
    vec3.set(shadow.scale, 0.15, 0.15, 0.15);
    arObject.addNode(shadow);

    const MAX_FLOWERS = 30; // 最大花数量
    let flowers = []; // node数组，用来暂存花对象的引用

    let reticle = new Gltf2Node({ url: "gltf/reticle/reticle.gltf" }); // 标线图像
    reticle.visible = false;
    scene.addNode(reticle);

    scene.clear = false; // 背景透明，AR需要实现3D物体与现实场景结合

    /**
     * 初始化 XR 内容
     */
    function initXR() {
      // 提示按钮
      xrButton = new WebXRButton({
        onRequestSession: onRequestSession, // 请求会话
        onEndSession: onEndSession, //终止会话
        textEnterXRTitle: "开始 AR",
        textXRNotFoundTitle: "此设备不支持AR",
        textExitXRTitle: "退出 AR",
      });

      document.querySelector("#btn-container").appendChild(xrButton.domElement); // 将提示按钮加入dom中
      // 若浏览器支持 navigator.xr，即拥有 the XRSystem object
      if (navigator.xr) {
        // 判断设备和浏览器是否支持该 XR 类型，这里是 immersive-ar
        navigator.xr.isSessionSupported("immersive-ar").then((supported) => {
          xrButton.enabled = supported; // 根据检测结果设定是否支持
        });
      }
    }

    /**
     * 请求会话
     */
    function onRequestSession() {
      return navigator.xr
        .requestSession("immersive-ar", {
          requiredFeatures: ["local", "hit-test"],
        })
        .then((session) => {
          xrButton.setSession(session); // 设置 AR Session
          onSessionStarted(session);
        });
    }

    /**
     * 开始会话
     */
    function onSessionStarted(session) {
      session.addEventListener("end", onSessionEnded);
      session.addEventListener("select", onSelect); // 添加select事件监听器。当用户点击屏幕时，将基于标线的位置将花朵放置在相机视图中。

      if (!gl) {
        gl = createWebGLContext({
          xrCompatible: true,
        });
        // 设置渲染器
        renderer = new Renderer(gl);
        scene.setRenderer(renderer);
      }
      session.updateRenderState({ baseLayer: new XRWebGLLayer(session, gl) });
      // 该参考空间用于命中测试，要测量与控制器的距离，可以使用以控制器为中心的参考系
      // 其原始位置跟踪查看者的位置和方向。它用于用户可以在其中进行物理移动的环境
      session.requestReferenceSpace("viewer").then((refSpace) => {
        xrViewerSpace = refSpace;
        session
          .requestHitTestSource({ space: xrViewerSpace })
          .then((hitTestSource) => {
            xrHitTestSource = hitTestSource;
          });
      });
      // 在屏幕上绘制某些内容，需使用以用户为中心的坐标
      // The requestReferenceSpace(type) method constructs a new XRReferenceSpace of a given type
      // local 类型，创建会话时其原始位置位于查看者位置附近的跟踪空间。确切位置取决于基础平台和实现。用户不会移动太多，甚至不会超出他们的起始位置。所以用来绘制模型
      session.requestReferenceSpace("local").then((refSpace) => {
        xrRefSpace = refSpace;
        // 开始帧循环
        session.requestAnimationFrame(onXRFrame);
      });
    }

    /**
     * 结束会话
     */
    function onEndSession(session) {
      xrHitTestSource.cancel();
      xrHitTestSource = null;
      session.end();
    }
    function onSessionEnded(event) {
      xrButton.setSession(null);
    }

    /**
     * 选择事件回调函数
     */
    function onSelect(event) {
      if (reticle.visible) {
        addARObjectAt(reticle.matrix);
      }
    }
    /**
     * 将一朵新花加入场景
     */
    function addARObjectAt(matrix) {
      let newFlower = arObject.clone();
      newFlower.visible = true;
      newFlower.matrix = matrix;
      scene.addNode(newFlower);
      flowers.push(newFlower);
      if (flowers.length > MAX_FLOWERS) {
        let oldFlower = flowers.shift();
        scene.removeNode(oldFlower);
      }
    }

    /**
     * 每次绘制一帧时调用
     */
    function onXRFrame(t, frame) {
      // 获取AR会话
      let session = frame.session;
      // 获取渲染所需位姿
      let pose = frame.getViewerPose(xrRefSpace);
      // 将上一个标线隐去
      reticle.visible = false;
      // 如果命中测试源都存在，则用摄像机位姿信息在场景中显示标线。要在AR中绘制任何内容，需要知道观看者在哪里以及其观察方向
      if (xrHitTestSource && pose) {
        // 获取命中测试结果，返回平面数据数组
        // 数组中的第一个是距离相机最近的那个。如果没有返回命中结果，则继续在下一帧中再试一次。
        let hitTestResults = frame.getHitTestResults(xrHitTestSource);
        if (hitTestResults.length > 0) {
          // 从最近的命中测试结果中获取姿势，将标线图像转换（移动）到命中测试位置，然后将其visible属性设置为true。
          // 姿势表示表面上点的姿势。
          let pose = hitTestResults[0].getPose(xrRefSpace);
          reticle.visible = true;
          reticle.matrix = pose.transform.matrix;
        }
      }
      scene.startFrame();
      session.requestAnimationFrame(onXRFrame);
      scene.drawXRFrame(frame, pose);
      scene.endFrame();
    }
    initXR();
  },
};
</script>
<style scoped>
header {
  z-index: 2;
  display: flex;
  justify-content: center;
  flex-direction: column;
  position: relative;
  max-width: 500px;
  margin: 0 auto;
}

header h1 {
  margin-top: 0px;
}

header div {
  align-items: center;
}

canvas {
  position: absolute;
  z-index: 0;
  width: 100%;
  height: 100%;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
  margin: 0;
  touch-action: none;
}
</style>
```

在 App.vue 中使用 BasicAR 组件

```js
<template>
  <BasicAR />
</template>

<script>
import BasicAR from "./components/BasicAR.vue";

export default {
  name: "App",
  components: {
    BasicAR,
  },
};
</script>
```

## 部署

运行 WebXR 有额外的安全要求，以下是官方描述，即只能使用 http://localhost/ 和 https 链接加载 WebXR 内容。

> A WebXR compatible environment starts with a securely-loaded document. Your document needs to either have been loaded from the local drive (such as by using an URL such as http://localhost/...), or using HTTPS when loading the page. The JavaScript code must, likewise, have been loaded securely.

因为我们是在 pc 上开发，pc 没有可供测试用的 WebAR 设备，使用 http://localhost/ 无法满足需求。

有两种比较方便的解决办法，分别是

- 测试：手机 chrome 浏览器中把我们的 http://ip:port 设为安全来源
- 部署：使用 gh-pages 部署拿到一个 https 链接。

### 测试

将 pc 与手机置于同一个局域网下，执行 `npm run dev` 命令启动项目后，Terminal 会出现如下信息：

```bash
 Dev server running at:
  > Local:    http://localhost:3000/
  > Network:  http://your-ipv4-address:3000/
```

在手机 chrome 中搜索打开 chrome://flags，搜索 Insecure origins treated as secure，设置成 Enabled 状态，并在其上的文本框内输入 `http://your-ipv4-address:3000/`，relaunch 一下 chrome，就能使用该测试链接查看 AR 内容了。

### 通过 gh-pages 部署

我们可以将项目部署到 github 的 gh-pages，这样虽然有点麻烦，但是从新建项目到部署项目是一个完备的流程，学习一下也是有用的。

先在 github 新建一个 webar-vue-demo 仓库，依照提示把本地仓库与远程仓库连接起来。
然后生成需部署的静态文件。如果你正在嵌套的公共路径下部署项目（例如要部署在 `https://USERNAME.github.io/REPO/`，即某个仓库的 gh-pages 分支下），需要修改资源路径。

- 可以简单指定一个 build.base 配置项，然后所有资源的路径都将据此重写。

  ```json
  // vite.config.js
  export default {
    // 配置选项
    base: "/webar-vue-demo/",
  };
  ```

- 这个选项也可以通过命令行参数指定，例如 `vite build --base=/my/public/path/`，或者直接修改 packages.json，`"build": "vite build --base=/my/public/path/"`

在项目中（通常是在根目录下）创建一个 deploy.sh 脚本，包含以下内容，运行它（使用 `sh deploy.sh`）来部署站点

```bash
#!/usr/bin/env sh

# 发生任何错误时终止
set -e

# 构建
npm run build

# 进入输出产物文件夹
cd dist

# 禁用 jekyll
touch .nojekyll

# 如果你要部署到自定义域名
# echo 'www.example.com' > CNAME

git init
git add -A
git commit -m 'deploy'

# 如果你要部署在 https://<USERNAME>.github.io。视情况 master 改为 main
# git push -f git@github.com:<USERNAME>/<USERNAME>.github.io.git master

# 如果你要部署在 https://<USERNAME>.github.io/<REPO>
# 把本地master分支的内容推送到远程的gh-pages。如果远程没有gh-pages分支的话，会自动创建该分支。
# git push -f git@github.com:<USERNAME>/<REPO>.git master:gh-pages

cd -
```

注意事项：
资源文件夹前面会带着下划线，本地使用没有问题，提交到 github 上面，想使用 github pages 的时候提示 404，原因为 github pages 默认使用的 jekyll 模版会忽略下划线开头的文件，所以要禁用 jekyll 。禁用方法就是在项目 gh-pages 根目录下添加一个空白文件，命名为：.nojekyll。可以在 main 分支 public 文件夹下放置一个.nojekyll，这样 build 的时候就会自动生成，或者修改一下 deploy.sh，在 `cd dist` 之后 `touch .nojekyll`

具体可看 [github docs: about-github-pages](https://docs.github.com/cn/pages/getting-started-with-github-pages/about-github-pages)

## 参考

- [WebXR 文档](https://immersive-web.github.io/webxr/)
- WebXR 入门博客
  - [Augmented reality: You may already know it](https://web.dev/web-ar/)
  - [Virtual reality comes to the web](https://web.dev/vr-comes-to-the-web/)
  - [Virtual reality comes to the web, part II](https://web.dev/vr-comes-to-the-web-pt-ii/)
  - [Positioning virtual objects in real-world views](https://web.dev/ar-hit-test/)
- [WebXR 示例](https://github.com/immersive-web/webxr-samples)
- [WebXR_Device_API/Startup_and_shutdown](https://developer.mozilla.org/en-US/docs/Web/API/WebXR_Device_API/Startup_and_shutdown)
- [vite 部署](https://vitejs.bootcss.com/guide/static-deploy.html#github-pages)
