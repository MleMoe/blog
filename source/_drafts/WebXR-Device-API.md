---
title: WebXR Device API
date: 2021-01-13 16:52:25
tags: 
- WebXR
- AR
---


## 程序流程

使用WebXR Device API的应用程序遵循类似的使用模式：

- 调用`navigator.xr.isSessionSupported()`以确定硬件和UA是否支持该XR内容类型（传参确定类型）。
- 若支持，则将XR内容发布给用户。
- 等待窗口进行[transient-activation](https://html.spec.whatwg.org/multipage/interaction.html#transient-activation)开始查看XR内容。//由用户单击页面上的开始按钮实现。
- 在用户激活事件（即点击按钮）中使用`navigator.xr.requestSession()` 来请求`XRSession`，
- 如果请求`XRSession`成功，使用它运行[帧循环](https://immersive-web.github.io/webxr/#frame)以响应XR输入，并生成图像以作为响应显示在[XR设备](https://immersive-web.github.io/webxr/#xrsession-xr-device)上。
- 继续运行[帧循环，](https://immersive-web.github.io/webxr/#frame)直到UA[关闭会话](https://immersive-web.github.io/webxr/#shut-down-the-session)或用户指示他们要退出XR内容为止。

## 支持设备

安卓手机，支持AR Core

安卓版 chrome浏览器（版本81+），或  chrome canary。在地址栏输入 chrome://flags，搜索 webxr，找到WebXR incubations，设置为启用状态

## 示例

网址：<https://ar.mle.moe/>

图片：

![测试结果](/images/flower.jpeg)

## 参考

- [WebXR API](https://immersive-web.github.io/webxr/)，W3C工作组
- [W3C标准](https://zh.wikipedia.org/wiki/W3C%E6%8E%A8%E8%8D%90%E6%A0%87%E5%87%86)，WebXR Device API属于最早的 `工作草案WD `标准
- [Augmented reality: You may already know it](https://web.dev/web-ar/)
- [Virtual reality comes to the web](https://web.dev/vr-comes-to-the-web/)
- [Virtual reality comes to the web, part II](https://web.dev/vr-comes-to-the-web-pt-ii/)
- [Positioning virtual objects in real-world views](https://web.dev/ar-hit-test/)

