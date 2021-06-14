---
title: 关于证明我是一名 JS 程序员这件事
date: 2021-04-11 00:04:22
tags: 面试
---

## 浏览器与网络

### 浏览器内核

#### 渲染引擎

用来解释网页语法并渲染到网页上。负责显示请求的内容。如果请求的内容是 HTML，它就负责解析 HTML 和 CSS 内容，并将解析后的内容显示在屏幕上。

常见的渲染引擎可以分这四种：Trident、Gecko、Blink、Webkit。

#### js 引擎

用于解析和执行 JavaScript 代码。chrome 的 javascript 解释器是 V8。

### 地址栏输入 url，到页面呈现，的过程

[参考](https://developer.mozilla.org/zh-CN/docs/Web/Performance/How_browsers_work)

- 将 url 解析成 ip 地址，先查找缓存，没有则进行 dns 解析，向域名服务器处获取 ip
- 建立 tcp 连接
- 发送 http 请求
- 收到服务器响应的资源文档
- 构建 dom 树和 cssom 树，布局，再渲染
- 画面呈现

### http 与 https

#### http

http：超文本传输协议，位于应用层，基于 TCP/IP 协议，使用无状态连接。

##### 请求字段

- User-agent，浏览器标识
- Authorization，认证信息，可以放 token
- Origin，后端可以判断，access-control-allow-origin
- cookie
- Cache-control：里面有 max-age，判断是否命中强缓存
- If-Modified-Since，304，协商缓存
- If-None-Match，304

##### 响应字段

- set-cookie，`Set-Cookie: UserID=JohnDoe; Max-Age=3600; Version=1`
- ETag，对于某个资源的某个特定版本的一个标识符，通常是一个 消息散列 `ETag: "737060cd8c284d8af7ad3082f209582d"`
- Expires，指定一个日期/时间，超过该时间则认为此回应已经过期，`Expires: Thu, 01 Dec 1994 16:00:00 GMT`
- Last-Modified，所请求的对象的最后修改日期，`Last-Modified: Tue, 15 Nov 1994 12:45:26 GMT`

##### 状态码

1：消息，临时响应，代表请求已被接受，需要继续处理

2：成功，代表请求已成功被服务器接收、理解、并接受。

3：重定向，后续的请求地址（重定向目标）在本次响应的 Location 域中指明。

- [301 Moved Permanently](https://zh.wikipedia.org/wiki/HTTP_301)

  被请求的资源已**永久移动**到新位置，并且将来任何对此资源的引用都应该使用本响应返回的若干个 URI 之一。

- [302 Found](https://zh.wikipedia.org/wiki/HTTP_302)

  要求客户端执行**临时重定向**（原始描述短语为“Moved Temporarily”）

- [303 See Other](https://zh.wikipedia.org/wiki/HTTP_303)

  对应当前请求的响应可以在另一个 URI 上被找到

- **304 Not Modified**

  未修改。所请求的资源未修改，服务器返回此状态码时，不会返回任何资源。

  表示资源在由请求头中的`If-Modified-Since`或`If-None-Match`参数指定的这一版本之后，未曾被修改。在这种情况下，由于客户端仍然具有以前下载的副本，因此不需要重新传输资源。

  注：与协商缓存有关

- 305 Use Proxy

  被请求的资源必须通过指定的**代理**才能被访问。Location 域中将给出指定的代理所在的 URI 信息，接收者需要重复发送一个单独的请求，通过这个代理才能访问相应资源。

4：客户端错误

- 400 Bad Request

由于明显的客户端错误（例如，格式错误的请求语法，太大的大小，无效的请求消息或欺骗性路由请求），服务器不能或不会处理该请求。

- 401 Unauthorized

未认证，类似于 403 Forbidden

- **404 Not Found**
- **405 Method Not Allowed**
- **418 I'm a teapot**

5：服务器错误

- 500 Internal Server Error

通用错误消息，服务器遇到了一个未曾预料的状况，导致了它无法完成对请求的处理。没有给出具体错误信息。

- 502 Bad Gateway

作为[网关](https://zh.wikipedia.org/wiki/网关)或者[代理](https://zh.wikipedia.org/wiki/代理服务器)工作的服务器尝试执行请求时，从上游服务器接收到无效的响应。

- 503 Service Unavailable

  由于临时的服务器维护或者[过载](https://zh.wikipedia.org/wiki/過載)，服务器当前无法处理请求。这个状况是暂时的，并且将在一段时间以后恢复。

- 504 Gateway Timeout

  作为网关或者代理工作的服务器尝试执行请求时，未能及时从上游服务器（URI 标识出的服务器，例如[HTTP](https://zh.wikipedia.org/wiki/HTTP)、[FTP](https://zh.wikipedia.org/wiki/FTP)、[LDAP](https://zh.wikipedia.org/wiki/LDAP)）或者辅助服务器（例如[DNS](https://zh.wikipedia.org/wiki/DNS)）收到响应。

#### https

在 http 的基础上加了 ssl 协议，是 http 的安全版本。

#### http2.0 的改进

- 基于 https，安全性更有保证
- 使用二进制格式，比以前基于文本的传输更具普适性
- 多路复用，是以前长连接的增强

### Get、Post、Put、Delete、Options、Trace、Connect

#### 请求类型

- get：请求资源，可以使用缓存
- Post：提交数据，POST 产生两个 TCP 数据包，浏览器先发送 header，服务器响应 100 continue，浏览器再发送 data，服务器响应 200 ok（返回数据），不能使用缓存
- Put：更新资源
- Delete：删除资源
- Options：返回某资源所支持的 HTTP 请求方法
- Trace：回显服务器收到的请求，主要用于测试或诊断
- Connect：HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器

### 幂等

参考：[mdn](https://developer.mozilla.org/zh-CN/docs/Glossary/Idempotent)

一个 HTTP 方法是**幂等**的，指的是同样的请求被执行一次与连续执行多次的效果是一样的，服务器的状态也是一样的。换句话说就是，幂等方法不应该具有副作用（统计用途除外）。在正确实现的条件下， [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) ， [`HEAD`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/HEAD) ， [`PUT`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/PUT) 和 [`DELETE`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/DELETE) 等方法都是**幂等**的，而 [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST) 方法不是。所有的 [safe](https://developer.mozilla.org/en-US/docs/Glossary/Safe) 方法也都是幂等的。

幂等性只与后端服务器的实际状态有关，而每一次请求接收到的状态码不一定相同。例如，第一次调用 [`DELETE`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/DELETE) 方法有可能返回 [`200`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/200) ，但是后续的请求可能会返回 [`404`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/404) 。 [`DELETE`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/DELETE) 的言外之意是，开发者不应该使用 `DELETE` 法实现具有删除最后条目功能的 RESTful API。

#### Restful 规范

用 url 来指代资源，用请求类型来指代动作。

### Ajax、Fetch 与 axios

#### Ajax

使用 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 实现
特点：

- 会遇到 XSS、CSRF 攻击
- 本身是针对 MVC 的编程，不符合现在前端 MVVM 的浪潮。
- 基于原生的 XHR 开发，XHR 本身的架构不清晰，已经有了 fetch 的替代方案。

#### Fetch

Fetch 是 XHR 的替代，ES6 出现的，使用了 ES6 中的 promise 对象，由 js 原生提供，没有使用 XMLHttpRequest 对象
特点：

- 符合关注分离，没有将输入、输出和用事件来跟踪的状态混杂在一个对象里
- 更好更方便的写法
- 更加底层，提供的 API 丰富（request, response）
- 脱离了 XHR，是 ES 规范里新的实现方式
- fetchtch 只对网络请求报错，对 400，500 都当做成功的请求，需要封装去处理
- fetch 默认不会带 cookie，需要添加配置项
- fetch 不支持 abort，不支持超时控制，使用 setTimeout 及 Promise.reject 的实现的超时控制并不能阻止请求过程继续在后台运行，造成了量的浪费
- fetch 没有办法原生监测请求的进度，而 XHR 可以。

```javascript
fetch(url, {
    body: JSON.stringify(data), // must match 'Content-Type' header
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, same-origin, *omit
    headers: {
      'user-agent': 'Mozilla/4.0 MDN Example',
      'content-type': 'application/json'
    },
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, cors, *same-origin
    redirect: 'follow', // manual, *follow, error
    referrer: 'no-referrer', // *client, no-referrer
  })
  .then(response => response.json()) // parses response to JSON
}
```

#### axios

axios 是一个基于 Promise 的用于浏览器和 nodejs 的 HTTP 客户端，本质上也是对原生 XHR 的封装，只不过它是 Promise 的实现版本，符合最新的 ES 规范。

- 支持 Promise API。
- 提供了一些并发请求的接口（重要，方便了很多的操作）。
- 在浏览器中创建 XMLHttpRequests。在 node.js 则创建 http 请求。（自动性强）
- 支持拦截请求和响应。
- 自动转换 JSON 数据。
- 客户端支持防御 CSRF、 XSRF。

#### Fetch 与 Ajax 的不同

- 当接收到一个代表错误的 HTTP 状态码时，从 `fetch()` 返回的 Promise 不会被标记为 reject，而是标记为 resolve （返回值的 `ok` 属性设置为 false ），仅当网络故障时或请求被阻止时，才会标记为 reject。
- `fetch()` **可以接受跨域 cookies；**你也可以使用 `fetch()` 建立起跨域会话。
- `fetch` **不会发送 cookies**。除非你使用了*credentials* 的[初始化选项](https://developer.mozilla.org/zh-CN/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。

#### fetch 发送 2 次请求的原因

用 fetch 的 post 请求时，第一次发送了一个 Options 请求，询问服务器是否支持该类型的请求，如果服务器支持，则在第二次中发送真正的请求。

### 强缓存与协商缓存

#### 流程图

![强缓存与协商缓存](/images/cache.jpg)

#### 缓存

缓存是一种保存资源副本并在下次请求时直接使用该副本的技术。当 web 缓存发现请求的资源已经被存储，它会拦截请求，返回该资源的拷贝，而不会去源服务器重新下载。

好处：缓解服务器端压力，提升性能(获取资源的耗时更短了)

告诉浏览器在约定的这个时间前，可以直接从缓存中获取资源，而无需跑到服务器去获取。

http 缓存机制主要在 http 响应头中设定，响应头中相关字段为**Expires、Cache-Control、Last-Modified、Etag**。

#### 强缓存（过期时间）

浏览器不会像服务器发送任何请求，直接从本地缓存中读取文件并返回

**Cache-Control**（优先级更高）：当值设为 max-age=300 时，则代表在这个请求正确返回时间的 5 分钟内再次加载资源，就会命中强缓存。

**Expires**：过期时间，如果设置了时间，则浏览器会在设置的时间内直接读取缓存，不再请求

#### 协商缓存（修改时间）

当资源过期时，向服务器发送请求，服务器根据请求头参数来判断是否命中协商缓存，如果命中，则返回 304 状态码并告诉浏览器从缓存中读取资源，否则返回 200 状态码和新资源。

- 协商缓存标识 1，加上时间戳的 hash 值：**etag**/if-none-match

当资源过期时，浏览器发现响应头里有 Etag,则再次像服务器请求时带上请求头 if-none-match(值是 Etag 的值)。服务器收到请求进行比对，决定返回新页面 200 或 304

- 标识 2，最新修改时间：**Last-modified**/if-modify-since

当资源过期时（浏览器判断 Cache-Control 标识的 max-age 过期），发现响应头具有 Last-Modified 声明，则再次向服务器请求时带上头 if-modified-since，表示请求时间。服务器收到请求后发现有 if-modified-since 则与被请求资源的最后修改时间进行对比（Last-Modified）,若最后修改时间较新（大），说明资源又被改过，则返回最新资源，HTTP 200 OK;若最后修改时间较旧（小），说明资源无新修改，响应 HTTP 304 走缓存。

#### Cache-control

- max-age：最大有效时间
- no-cache：不使用强缓存，需要与服务器验证缓存是否新鲜
- no-store：不使用缓存，包括强缓存和协商缓存
- public：所有的内容冬雨可以被缓存，包括客户端和代理服务器，比如 CDN
- provate：所有的内容只有单个用户即客户端才可以缓存，代理服务器不能缓存。是默认值。

### Cookie

#### 字段

- name
- value
- size
- domain
- path
- expires/max-age：过期时间
- http：httponly，无法通过 document.cookie 来获取 cookie
- secure：设置是否只能由 https 来传递此 cookie

### Cookie 与 Session

作用：由于 http 的无状态性（这一次请求和上一次请求是没有任何关系的，互不认识的，没有关联的），为了使某个域名下的所有网页能够共享某些数据，session 和 cookie 出现了。

Cookie 是用户通行证，存储在客户端，在发送请求时附带。session 有如用户信息档案表, 里面包含了用户的认证信息和登录状态等信息，保存在服务器端。

#### Cookie 和 Session 共同使用

- 客户端发一个 http 请求给服务器
- 服务器接受请求后，建立一个 session，发回 http 响应给客户端，其中的 set-cookie 头部带有 sessionid
- 客户端收到响应后，后续会自动在请求头中加 cookie
- 服务器接收请求，分解 cookie，验证成功后将请求返回给客户端。

用 session 只需在客户端保存一个 id，大量数据都是保存在服务端，对服务器有压力。

用 cookie 不用 session，那么账户信息全部保存在客户端，一旦被劫持，全部信息都会泄露。并且客户端数据量变大，网络传输的数据量也会变大。

### Token

是一种服务端无状态的认证方式。Token 类似一个令牌，无状态，用户信息都被加密到 token 中，服务器收到 token 后解密就可知道是哪个用户。需要开发者手动添加。

token 在客户端一般存放于 localStorage，cookie，或 sessionStorage 中。在服务器一般存于数据库中

token 的认证流程与 cookie 很相似

- 用户登录，成功后服务器返回 Token 给客户端。
- 客户端收到数据后保存在客户端
- 客户端再次访问服务器，将 token 放入 headers 中
- 服务器端采用 filter 过滤器校验。校验成功则返回请求数据，校验失败则返回错误码

#### 用 Token，不用 Cookie+Session 的优点

- 避免 CSRF 攻击

用户登陆银行网页，同时登陆了危险网页，攻击者在网页放一个表单，该表单提交 src 为`http://www.bank.com/api/transfer`，body 为`count=1000&to=Tom`。倘若是 session+cookie，表单发起的 POST 请求不受到浏览器同源策略限制，可以使用其它域的 Cookie 向其它域发送 POST 请求，形成 CSRF 攻击。在 post 请求的瞬间，cookie 会被浏览器自动添加到请求头中。但 token 不同，token 是开发者为了防范 csrf 而特别设计的令牌，浏览器不会自动添加到 headers 里，攻击者也无法访问用户的 token，所以提交的表单无法通过服务器过滤，也就无法形成攻击。

- Token 是一种服务端无状态的认证方式，而 cookie+session 是有状态的。（ 所谓无状态就是服务端并不会保存身份认证相关的数据。）

#### JWT

头部 +负载+签名

```javascript
// javascript
var encodedString = base64UrlEncode(header) + "." + base64UrlEncode(payload);

var signature = HMACSHA256(encodedString, "secret"); // TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ,加盐secret组合加密
// 将这三部分用.连接成一个完整的字符串,构成了最终的jwt:
```

在客户端应用：一般是在请求头里加入`Authorization`，并加上`Bearer`标注

流程和 token 的一样

缺点：一旦 JWT 签发了，在到期之前就会始终有效，除非服务器部署额外的逻辑。JWT 本身包含了认证信息，一旦泄露，任何人都可以获得该令牌的所有权限。

### cookie 和 token 都存放在 header 中，为什么不会劫持 token？

- Cookie

攻击者通过 xss 拿到用户的 cookie 然后就可以伪造 cookie 了。
通过 csrf 在同个浏览器下面通过浏览器会自动带上 cookie 的特性
在通过 用户网站-攻击者网站-攻击者请求用户网站的方式 浏览器会自动带上 cookie

- token

不会被浏览器带上 问题 2 解决
token 是放在 jwt 里面下发给客户端的 而且不一定存储在哪里 不能通过 document.cookie 直接拿到，通过 jwt+ip 的方式 可以防止 被劫持 即使被劫持 也是无效的 jwt

### 浏览器存储方式：Cookie、SessionStorage、LocalStorage

共同点：都是存储在浏览器端

不同点：

- cookie 可以在浏览器和服务器中来回传递的，而余下两者是只在本地保存的。

- 可存储数据的大小不一样，cookie 数据不能超过 4k，剩下的就比较大了，大概是在 5M 左右。

- 数据有效期不一样，sessionStorage 当关闭浏览器的时候就失效了，localStorage 一直有效，需要手动清

  除，而 cookie 只在有效期之内一直有效。

- 作用域不一样，localStorage、cookie 在所有同源窗口中都是共享的，sessionStorage 是某个窗口私有

  的。

### 跨页面通信

同源页面（不在同一个 tab）：可以使用 LocalStorage

非同源页面：iframe、postmessage

H5 之后为 window 新增了 window.postMessage()方法，第一个参数是要发送的数据，第二个参数是域名。

### Doctype

声明于文档最前面，告诉服务器以什么方式（例如 h5）来渲染页面。

运行模式分：严格模式（浏览器支持的最高版本）、混杂模式（向后兼容）

### XSS

跨站脚本攻击，指攻击者在网页中注入恶意代码，在用户浏览网页的时候进行攻击。

类型：

- 反射型：将攻击代码放在 url 地址的请求参数中
- 存储型：攻击者输入一些数据并且存储到了数据库中，其他浏览者看到的时候进行攻击。
- DOM 型：攻击者通过各种手段将恶意脚本注入用户的页面中

处理：

- set-cookie：httponly，禁止 javascript 脚本来访问 cookie，secure - 这个属性告诉浏览器仅在请求为 https 的时候发送 cookie。
- 输入检查：对于用户的任何输入要进行检查、过滤和转义
- 输出检查

### CSRF

Cross Site Request Forgery，跨站请求伪造

在讲 token 时讲了

防御 CSRF 攻击主要有三种策略：

- 验证 HTTP Referer 字段；
- 在请求地址中添加 token 并验证；
- 在 HTTP 头中自定义属性并验证。

### addEventListener

addEventListener(event, function, useCapture)

其中，event 指定事件名；function 指定要事件触发时执行的函数；useCapture 指定事件是否在捕获或冒泡阶段执行。

### 跨域

可看：[参考](https://zh.javascript.info/fetch-crossorigin)

- postMessage 跨域：可以跨域操作的 window 属性之一。
- CORS：服务端设置 Access-Control-Allow-Origin 即可，前端无须设置，若要带 cookie 请求，前后端都需要设置。
  - `Access-Control-Allow-Origin *;`
  - `Access-Control-Allow-Methods "POST, GET, OPTIONS";`
  - `Access-Control-Allow-Headers "Origin, Authorization, Accept";`
  - `Access-Control-Allow-Credentials true;`
- 代理跨域：启一个代理服务器，实现数据的转发
- JSONP：script 标签 src 属性中的链接却可以访问跨域的 js 脚本，利用这个特性，服务端不再返回 JSON 格式的数据，而是返回一段调用某个函数的 js 代码，在 src 中进行了调用，这样实现了跨域。例如：`script.src = http://another.com/weather.json?callback=gotWeather;`，需要后端配合。

### 查看网站性能

检测页面加载时间

- 被动检测：在页面设置检测脚本，当用户访问网页时，记录数据，传回数据库分析

- 主动监测：搭建分布式环境，模拟用户访问页面，主动采集数据并分析

### 前端优化

- 降低请求量：合并资源，减少 http 请求数
- 加快请求速度：预解析 DNS、CDN 分发
- 使用缓存
- 加快渲染：加载顺序（js 放最后），SSR，使用 GPU

### 屏幕卡顿

原因：

- 内存溢出
- 资源过大
- 资源加载
- canvas 绘制帧率

办法：

- 在物体离开屏幕可视区域即回收销毁
- 选用体积较小的资源，例如图片，可以在近处放清晰度高的，远处放清晰度低的
- 预加载
- 大部分显示器刷新频率为 60 次/s，因此游戏的每一帧绘制间隔时间需要小于 1000/60=16.7ms，才能让用户觉得不卡顿

### js 加载过程阻塞，解决方法

- 制定 script 标签的 async 属性，异步执行
- 设置 defer 属性，脚本将在页面完成解析时执行

### BOM

Brower object model，浏览器对象模型，它使 JavaScript 有能力与浏览器进行“对话”。window 对象 所有浏览器都支持 window 对象。它表示浏览器窗口。

### 顶层对象

- 浏览器：window/self
- web worker：self
- node：global
- ES5：顶层对象的属性与全局变量等价
- ES6：globalThis

### This

同一段代码为了能够在各种环境，都能取到顶层对象，现在一般是使用`this`变量，但是有局限性。

- 全局环境中，this 会返回等层对象。但是在 node 的模块中，返回的是当前模块，ES6 中返回的是 undifined。

- 函数里的 this，作为对象方法运行返回的是对象，或者是调用方，单纯作为函数运行会指向顶层对象。
- ES2020 引入 globalThis 对象，在任何环境下都指向全局环境下的 this

## CSS 与 html

### DOM

DOM（Document Object Model——文档对象模型）

### Document

#### 查找

- `document.getElementById(id)`

- `document.getElementByClassName(class)`

- `document.getElementByTagName(tag)`

- `document.querySelector(selectors)`

  表示文档中与指定的一组 CSS 选择器匹配的第一个元素,一个 [`HTMLElement`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement)对象。如果没有匹配到，则返回 null。

  `var el = document.querySelector("div.user-panel.main input[name='login']");`

  这里, 一个 class 属性为"user-panel main"的 div 元素`<div class="user-panel main">`内包含一个 name 属性为"login"的 input 元素`<input name="login"/>`

- [`querySelectorAll()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/querySelectorAll) ，与指定选择器匹配的所有元素的列表
- `ParentNode.firstElementChild`，返回第一个子元素或者 null
- `ParentNode.lastElementChild`
- `node.parentNode`

#### 创建

- [`Document.createElement()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/createElement)

  用给定标签名 tagName 创建一个新的元素。

- `node.cloneNode(deep)`

  返回调用该方法的节点的一个副本。deep，可选参数，是否采用深度克隆`,如果为true,`则该节点的所有后代节点也都会被克隆,如果为`false,则只克隆该节点本身.`

#### 加入

- `parentNode.append(node|str)`

在 `ParentNode`的最后一个子节点之后插入一组 [`Node`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node) 对象或 [`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString) 对象。被插入的 [`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString) 对象等价为 [`Text`](https://developer.mozilla.org/zh-CN/docs/Web/API/Text) 节点。

- `ParentNode.prepend`方法可以在父节点的第一个子节点之前插入一系列[`Node`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node)对象或者[`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString)对象。

- `node.appendChild(node)`

  将一个节点附加到指定父节点的子节点列表的末尾处。

  如果将被插入的节点已经存在于当前文档的文档树中，那么 `appendChild()` 只会将它从原先的位置移动到新的位置（不需要事先移除要移动的节点）。这意味着，一个节点不可能同时出现在文档的不同位置。

  如果某个节点已经拥有父节点，在被传递给此方法后，它首先会被移除，再被插入到新的位置。

  若要保留已在文档中的节点，可以先使用 [`Node.cloneNode()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node/cloneNode) 方法来为它创建一个副本，再将副本附加到目标父节点下。请注意，用 `cloneNode` 制作的副本不会自动保持同步。

【与 [`Node.appendChild()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node/appendChild) 的差异】

- `ParentNode.append()`允许追加 [`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString) 对象，而 `Node.appendChild()` 只接受 [`Node`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node) 对象。
- `ParentNode.append()` [没有返回值](https://repl.it/FgPh/1)，而 `Node.appendChild()` 返回追加的 [`Node`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node) 对象。
- `ParentNode.append()` 可以追加多个节点和字符串，而 `Node.appendChild()` 只能追加一个节点。

#### 删除

- `parent.removeChild(child)`

  根据父母节点来删除节点。删除后的节点虽然不在文档树中了，但其实它还在内存中，可以随时再次被添加到别的位置。

#### 获取子节点

- `node.children()`，返回 一个 Node 的子[`elements`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element) ，是一个动态更新的 [`HTMLCollection`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLCollection)。

### CSS 选择器

#### 基本选择器

- 通用选择器：`* {}`
- 元素(类型)选择器：`p {}`
- 类选择器：`.class {}`
- ID 选择器：`#id {}`
- 属性选择器：`[attr=value] {}`

#### 分组选择器

- , 的选择器列表：`div, span {}`

#### 组合器（关系选择符）

- 后代组合器：`div span {}`
- 直接子代组合器：`div>span {}`
- 一般兄弟组合器：`A~B {}`
- 紧邻兄弟组合器：`A+B {}`
- 列选择器：`col || td`

#### 伪选择器

- 伪类：`a:visited {}`，匹配所有曾被访问过的`<a>`元素。支持按照状态信息来选择元素。
- 伪元素：`p::first-line`，匹配所有`<p>`元素的第一行。伪选择器用于表示无法用 HTML 语义表达的实体。

### CSS 优先级

递增的：

1. [类型选择器](https://developer.mozilla.org/en-US/docs/Web/CSS/Type_selectors)（例如，`h1`）和**伪元素**（例如，`::before`）
2. [类选择器](https://developer.mozilla.org/en-US/docs/Web/CSS/Class_selectors) (例如，`.example`)，**属性选择器**（例如，`[type="radio"]`）和**伪类**（例如，`:hover`）
3. [ID 选择器](https://developer.mozilla.org/en-US/docs/Web/CSS/ID_selectors)（例如，`#example`）。

带有 `!important` 标记的样式属性的优先级最高；

**通配选择符**（universal selector）（[`*`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Universal_selectors)）**关系选择符**（combinators）（[`+`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Adjacent_sibling_combinator), [`>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Child_combinator), [`~`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/General_sibling_combinator), [`' '`](https://developer.mozilla.org/en-US/docs/Web/CSS/Descendant_combinator), [`||`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Column_combinator)）和 **否定伪类**（negation pseudo-class）（[`:not()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:not)）对优先级没有影响。

样式表的来源不同时，优先级顺序为：内联样式> 内部样式 > 外部样式 > 浏览器用户自定义样式 > 浏览器默认样式

### 盒子模型

#### content-padding-border-margin

- 标准（W3C）盒子模型：width = content
- IE 盒子模型：width = content+padding+border

content 大小自适应

#### 使用 box-sizing 设置

`box-sizing`：规定两个并排的带边框的框，语法为`box-sizing：content-box/border-box/inherit`

`content-box`：宽度和高度分别应用到元素的内容框，在宽度和高度之外绘制元素的内边距和边框

`border-box`：为元素设定的宽度和高度决定了元素的边框盒，

`inherit`：继承父元素的 box-sizing

### overflow

CSS 属性 **overflow** 定义当一个元素的内容太大而无法适应 [块级格式化上下文](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context) 时候该做什么。

- visible：默认值。内容不会被修剪，会呈现在元素框之外
- hidden：内容会被修剪，并且其余内容不可见
- scroll：内容会被修剪，浏览器会显示滚动条以便查看其余内容
- auto：由浏览器定夺，如果内容被修剪，就会显示滚动条

为使 `overflow`有效果，块级容器必须有一个指定的高度。除了 visible 外，其它都触发 BFC

### background-size

用于调整背景图片的宽和高，可以放大和缩小。（因为默认图片布局是根据其尺寸平铺）

```css
background-size: 300px 150px; // 宽和长，强制转换
background-size: contain; // 确保两个尺寸(图片的宽和高)都小于或等于容器的相应尺寸。
background-size: cover; // 确保两个尺寸(图片的宽和高)都大于或等于容器的相应尺寸。
```

### Background-origin

### animation

从一个 CSS 样式配置转换到另一个 CSS 样式配置

两个部分：描述动画的**样式规则**，用于制定动画开始、结束以及中间点样式的**关键帧**

```css
p {
  animation-duration: 3s; // 持续时间
  animation-name: slidein; // 动画名字
  background: red;
  animation-iteration-count: 2; // 设置运行次数，无穷是默认值，或许直接写infinite
  animation-direction: alternate; // 来回播放，而不是从头播放
}

@keyframes slidein {
  from {
    // 代表0%
    margin-left: 100%;
    width: 300%;
  }
  50% {
    font-size: 300%;
  }
  to {
    // 代表 100%
    margin-left: 0%;
    width: 100%;
  }
}
```

### transform

### box-sizing

content 内容自适应。

- box-sizing: content-box;

  表示标准的 W3C 盒子模型，width = content;

- box-sizing: border-box

  表示的是 IE 盒子模型，width = border + padding + content

### Border-radius

- 边框：`border-radius`，`box-shadow`等；

### 画一个三角形

```html
<div id="demo"></div>
#demo{ width:0px; height:0px; border:40px solid transparent; border-bottom:80px
solid red; }
```

### 垂直居中

```css
#box {
    width: 300px;
    height: 300px;
    background: #ddd;
}
#child {
    background: orange;
    position: absolute;
    top: 50%;
    transform: translate(0, -50%);
}

// 2

#box {
    width: 300px;
    background: #ddd;
    padding: 100px 0;
}
#child {
    width: 200px;
    height: 100px;
    background: orange;
}

// 3
#box {
    width: 300px;
    height: 300px;
    background: #ddd;
    display: flex;
    align-items: center; // 垂直居中
    justify-content: center;// 水平居中
}

<div id="box">
    <div id="child">test vertical align</div>
</div>
```

### 三列布局

```css
1: parent的div设置display：grid，设置grid-template-columns属性，固定第一列第二列宽度，第三列auto

2: 给div设置float：left，再给right的div设置overflow:hidden。这样子两个盒子浮动，另一个盒子触发bfc达到自适应

<div class="div1"> 1</div>
<div class="div2">2</div>
<div class= "div3"> 3 </div>

.div1,
.div2 {
  float: left;
  height: 100px;
  width: 100px;
  background: blue;
}
.div3 {
  overflow: auto;
  height: 100px;
  width: 100px;
  background: red;
}
```

### js 设置轮播

图片轮播的原理就是图片排成一行，然后准备一个只有一张图片大小的容器，对这个容器设置超出部分隐藏，在控制定时器来让这些图片整体左移或右移，这样呈现出来的效果就是图片在轮播了。

如果有两个轮播，可封装一个轮播组件，供两处调用。

### link 和@import 的区别

- link 是 html 标签，没有兼容性问题，@import 是 css 提供的，IE5 以上才能识别
- link 样式的权重高于@import
- link 和页面同时加载，@import 在页面加载结束后才加载

### transition 和 animation 的区别

Animation 和 transition 大部分属性是相同的，他们都是随时间改变元素的属性值，他们的主要区别是 transition 需要触发一个事件才能改变属性，而 animation 不需要触发任何事件的情况下才会随时间改变属性值，并且 transition 为 2 帧，从 from .... to，而 animation 可以一帧一帧的。

### Flex 布局

Flex 是弹性布局，用来为盒状模型提供最大的灵活性，传统布局方式依赖 position、float 和 display 属性，对特殊布局不方便。

- 容器属性
  - flex-direction：决定主轴的方向（即子 item 的排列方法）
  - flex-wrap：决定换行规则
  - justify-content：对其方式，水平主轴对齐方式
  - align-items：对齐方式，竖直轴线方向
- 元素属性
  - align-self：允许单个项目与其他项目不一样的对齐方式，可以覆盖 align-items，默认属性为 auto，表示继承父元素的 align-items

### 双边距重叠问题（外边距折叠）

多个相邻（兄弟或者父子关系）普通流的块元素垂直方向 marigin 会重叠

折叠的结果为：

两个相邻的外边距都是正数时，折叠结果是它们两者之间较大的值。
两个相邻的外边距都是负数时，折叠结果是两者绝对值的较大值。
两个外边距一正一负时，折叠结果是两者的相加的和。

```css
.div {
  display: flex; //display: flex;的元素生成BFC
  flex-direction: column;
}
// 分组选择器
.div1,
.div2 {
  height: 100px;
  width: 100px;
  border: 1px solid #000;
  margin: 100px;
  /* 显然，两个边框之间的距离不是400，而是200 */
}
```

### BFC

作用：用于清除浮动，防止 margin 重叠等

块级格式化上下文，是一个独立的渲染区域，并且有一定的布局规则。BFC 是页面上的一个独立容器，子元素不会影响到外面。

BFC 区域不会与 float box 重叠，计算 BFC 的高度时，浮动元素也会参与计算

那些元素会生成 BFC：

- 根元素

- float：不为 none

- position：fixed/absolute

- display：inline-block、table-cell、table-caption，flex，inline-flex

- overflow：不为 visible

### 元素消失

visibility=hidden, opacity=0，display:none

- opacity=0，元素隐藏，不改变页面布局，可触发绑定事件

- visibility=hidden，元素隐藏，不改变页面布局，不会触发绑定事件
- display=none，元素隐藏，改变页面布局，可以理解成在页面中把该元素删除掉一样。

### position 属性

- fixed：相对浏览器窗口固定，即使窗口滚动它也不懂，不在文档流中，fixed 元素可与其余元素重叠
- relative：相对于自身原本位置偏移，元素在文档流中仍占据原来的空间
- absolute：相对于最近的父元素定位，不在文档流中
- static：默认值，没有定位，出现在正常的文档流中，忽略任何 top,buttom,left,right 声明
- sticky：元素先按照普通文档流定位，然后相对于该元素在流中的 flow root（BFC）和 containing block（最近的块级祖先元素）定位。一个 sticky 元素会“固定”在离它最近的一个拥有“滚动机制”的祖先上。

### 浮动清除

在非 IE 浏览器（如 Firefox）下，当容器的高度为 auto，且容器的内容中有浮动（float 为 left 或 right）的元素，在这种情况下，容器的高度不能自动伸长以适应内容的高度，使得内容溢出到容器外面而影响（甚至破坏）布局的现象。这个现象叫浮动溢出，为了防止这个现象的出现而进行的 CSS 处理，就叫 CSS 清除浮动。

- 设置 css 的 overflow 为 auto
- 使用空元素，css 的 clear 属性为 both

### block、inline、inline-block 的区别

- block 块元素独占一行，宽度自动填充为父元素宽度。元素的高度、宽度、行高以及顶和底边距都可设置。即使设置了宽度,仍然是独占一行。例如，`div, p`
- inline 元素，多个该元素排列在一行，直到超出，才会换新一行，其宽度随元素内容而变化。width,height 属性无效，垂直方向的 padding 和 margin 会失效。例如，`a, span`
- inline-block：既具有 block 的宽度高度特性又具有 inline 的同行特性。例如，`img,input`

### CSS 预处理器

预先编译处理`CSS`。扩展 `CSS` ，增加了变量、Mixin、函数等特性，供开发者编写源代码，随后经过专门的编译工具将源码转化为`CSS`语法。

特点：
• 嵌套；
• 变量；
• mixin/继承；
• 运算；
• 模块化；

类别与发展：

- 2007， sass-scss（两套语法规则：一个依旧是用缩进作为分隔符来区分代码块的；另一套规则和 CSS 一样采用了大括号（｛｝）作为分隔符。后一种语法规则又名`SCSS`，在 Sass3 之后的版本都支持这种语法规则。）
- 2009，less（使用`CSS`的语法）
- 2010，Stylus（同时支持缩进和 CSS 常规样式书写规则）

### HTML5

#### 新增的元素

语义化：header, footer, nav, aside, article, section

存储：sessionStorage, localStorage

多媒体：audio, video

绘制：svg, canvas, webgl

通信：websocket

多线程：web worker

## 操作系统

### 进程和线程

#### 进程

系统调用资源的最小单位，也就是说是拥有资源且独立运行的最小单位。

#### 线程

CPU 调度的最小单位。一个进程可以拥有多个线程，线程是可以独立运行的最小单位。

##### **进程切换开销**

1. 切换虚拟地址空间
2. 切换 CPU 上下文
3. 切换内核栈

##### **线程切换开销**

1. 切换 CPU 上下文
2. 切换内核栈

### 进程通信

- 管道
- 消息队列
- 信号量
- 信号
- 套接字 socket
- 共享内存

## js

### 数据类型

- 6 种原始类型，可以使用 typeof 判断
  - undefined
  - Number
  - Boolean
  - String
  - Symbol
  - BigInt：BigInt 是通过在整数末尾附加 `n` 或调用构造函数来创建的。
- null，和其上六种属于属于原始值
- Object
  - Array
  - Map
  - WeakMap
  - Set
  - WeakSet
  - Date

#### 基本数据类型与引用类型的区别

- 基本数据类型

> 字符串（String）、数值（Number）、布尔值（Boolean）、Null、Undefined
>
> 1.占用空间固定，保存在栈中
>
> 2、保存与复制的是值本身
>
> 3、使用 typeof 检测数据的类型

- 引用类型

> 对象（Object）、数组（Array）、函数（Function）
>
> 1、占用空间不固定，保存在堆中
>
> 2、保存与复制的是指向对象的一个指针
>
> 3、使用 instanceof 检测数据类型

### Object

- Object.keys()，获取**自身属性**

- for in 遍历**原型链上扩展的属性**

- `Object.create()`方法创建一个新对象，使用现有的对象来提供新创建的对象的`__proto__`

- `Object.assign()` 方法用于将所有可枚举属性的值从一个或多个源对象分配到目标对象。它将返回目标对象。

  `Object.assign(target, ...sources)=>target`

  ```javascript
  const target = { a: 1, b: 2 };
  const source = { b: 4, c: 5 };

  const returnedTarget = Object.assign(target, source);

  console.log(target);
  // expected output: Object { a: 1, b: 4, c: 5 }

  console.log(returnedTarget);
  // expected output: Object { a: 1, b: 4, c: 5 }
  ```

- `Object.defineProperties()` 方法直接在一个对象上定义新的属性或修改现有属性，并返回该对象。

  `Object.defineProperties(obj, props)`

### 数组方法

#### 修改

- push、unshift，原地修改
- pop、shift，原地修改
- splice，原地修改，(index, num, val1, val2, ...)

#### 排序

- sort，原地修改

#### 截取子数组

- slice，返回新数组

#### 合并数组

- concat，合并两个或**多个数组**。此方法不会更改现有数组，而是返回一个新数组。

  `var new_array = old_array.concat(value1[, value2[, ...[, valueN]]])`

#### Flat

- `flat()` 方法会按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。

#### Join

- `join()` 方法将一个数组（或一个[类数组对象](https://developer.mozilla.org/zh-CN_docs/Web/JavaScript/Guide/Indexed_collections#working_with_array-like_objects)）的所有元素连接成一个字符串并返回这个字符串。如果数组只有一个项目，那么将返回该项目而不使用分隔符。

#### 遍历

- forEach，遍历数组，不返回新数组，且无法提前跳出

- map，根据元素做处理，返回各个处理结果的新数组
- reduce，返回新数组

#### 测试

- `filter()` 方法创建一个新数组, 其包含通过所提供函数实现的测试的所有元素。

  ```js
  var newArray = arr.filter(callback(element[, index[, array]])[, thisArg])
  ```

- every，测试一个数组内的所有元素是否都能通过某个指定函数的测试。它返回一个布尔值。若收到一个空数组，此方法在一切情况下都会返回 `true`。

  `arr.every(callback(element[, index[, array]])[, thisArg])`

- `some()` 方法测试数组中是不是至少有 1 个元素通过了被提供的函数测试。它返回的是一个 Boolean 类型的值。

- `find`方法对数组中的每一项元素执行一次 `callback` 函数，直至有一个 callback 返回 `true`。当找到了这样一个元素后，该方法会立即返回这个元素的值，否则返回 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)。

- `findIndex()`方法返回数组中满足提供的测试函数的第一个元素的**索引**。若没有找到对应元素则返回-1。

- `indexOf()`方法返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回-1。

- `includes()` 方法用来判断一个数组是否包含一个指定的值，根据情况，如果包含则返回 true，否则返回 false。

### ES6 新特性

- 在变量声明方面有：let, const
- 面向对象编程有 语法糖 class
- 在模块导入方面是 import export
- 新的数据结构：map、set
- 新的数组方法：map、reduce
- 异步：promise
- 箭头函数
- 解构赋值和三点表达式
- 等等……

### 异步

#### Async，promise 和 generator，区别是什么

[async](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function)

- Generator 函数是将函数分步骤阻塞 ，只有主动调用 next() 才能进行下一步

- `async`和`await`关键字让我们可以用一种更简洁的方式写出基于[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)的异步行为，而无需刻意地链式调用`promise`。

  async 函数可能包含 0 个或者多个[`await`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/await)表达式。await 表达式会暂停整个 async 函数的执行进程并出让其控制权，只有当其等待的基于 promise 的异步操作被兑现或被拒绝之后才会恢复进程。promise 的解决值会被当作该 await 表达式的返回值。使用`async` / `await`关键字就可以在异步代码中使用普通的`try` / `catch`代码块。

  async 函数一定会返回一个 promise 对象。如果一个 async 函数的返回值看起来不是 promise，那么它将会被隐式地包装在一个 promise 中。

  `async`/`await`的行为就好像搭配使用了生成器和 promise。

### Promise

#### 三种状态

pending、fulfilled、rejected

#### 静态方法

##### Promise.all( iterable)

@ para：promise 数组，或者 iterable

@ return：成功则返回，所有 promise 返回值的数组；失败，则把第一个触发失败的 promise 对象的错误信息作为它的失败错误信息。

相比于 allSettled ，all 更适合彼此相互依赖或者在其中任何一个`reject`时立即结束

##### Promise.allSettled(iterable)

方法返回一个在所有给定的 promise 都已经`fulfilled`或`rejected`后的 promise，并带有一个对象数组，每个对象表示对应的 promise 结果。

当您有多个彼此不依赖的异步任务成功完成时，或者您总是想知道每个`promise`的结果时，通常使用它。

##### Promise.any(iterable)

接收一个 Promise 对象的集合，当其中的一个 promise **成功**，就返回那个成功的 promise 的值。最快成功的那一个。

##### Promise.race(iterable)

返回一个 promise，一旦迭代器中的**某个**promise 解决或拒绝，返回的 promise 就会解决或拒绝。也就是最快处理的那个，无论是成功还是失败。

##### Promise.reject(reason)

返回一个状态为失败的 Promise 对象，并将给定的失败信息传递给对应的处理方法

##### Promise.resolve(value)

返回一个以给定值解析后的[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 对象。

如果这个值是一个 promise ，那么将返回执行后的这个 promise ；

如果这个值是 thenable（即带有[then](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/then)方法），返回的 promise 会“跟随”这个 thenable 的对象，采用它的最终状态；否则返回的 promise 将以此值完成。

此函数将类 promise 对象的多层嵌套展平。

#### 实现 promise

```javascript
// 定义三种状态
const PENDING = "PENDING"; // 进行中
const FULFILLED = "FULFILLED"; // 已成功
const REJECTED = "REJECTED"; // 已失败

class Promise {
  constructor(exector) {
    // 初始化状态
    this.status = PENDING;
    // 将成功、失败结果放在this上，便于then、catch访问
    this.value = undefined;
    this.reason = undefined;

    const resolve = (value) => {
      // 只有进行中状态才能更改状态
      if (this.status === PENDING) {
        this.status = FULFILLED;
        this.value = value;
      }
    };
    const reject = (reason) => {
      // 只有进行中状态才能更改状态
      if (this.status === PENDING) {
        this.status = REJECTED;
        this.reason = reason;
      }
    };
    // 立即执行exector
    // 把内部的resolve和reject传入executor，用户可调用resolve和reject
    exector(resolve, reject);
  }
  then(onFulfilled, onRejected) {
    // then是微任务，这里用setTimeout模拟
    setTimeout(() => {
      if (this.status === FULFILLED) {
        // FULFILLED状态下才执行
        onFulfilled(this.value);
      } else if (this.status === REJECTED) {
        // REJECTED状态下才执行
        onRejected(this.reason);
      }
    });
  }
}
```

#### 实现 all

```javascript
var all = (promises) => {
  if (!Array.isArray(promises)) {
    return reject(new TypeError("arguments must be an array"));
  }
  var l = promises.length;
  var result = new Array(l);
  var num = 0;
  return new Promise((resolve) => {
    for (let i = 0; i < l; i++) {
      Promise.resolve(promises[i]).then(
        (res) => {
          result[i] = res;
          num += 1;
          if (num === l) {
            return resolve(result);
          }
        },
        (reason) => {
          return reject(reason);
        }
      );
    }
  });
};
```

### this

函数里的 this 是函数的调用者；

严格模式下，在全局顶层是 undefined。

### 箭头函数

作用：更简短的函数并且不绑定`this`。

不能用作构造函数，无自己的原型属性；

无 arguments ，但是可以用三点解析式，用…rest 参数获取

### Symbol

每个从`Symbol()`返回的 symbol 值都是唯一的。一个 symbol 值能作为对象属性的标识符；这是该数据类型仅有的目的。

不支持语法："`new Symbol()`"。

[`Object.getOwnPropertySymbols()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols) 方法让你在查找一个给定对象的符号属性时返回一个 symbol 类型的数组。

### Set

Set：成员值唯一的数组（就是集合）

```javascript
// Set 结构的实例有以下属性。

Set.prototype.constructor：构造函数，默认就是Set函数。
Set.prototype.size：返回Set实例的成员总数。
// Set 实例的方法分为两大类：操作方法（用于操作数据）和遍历方法（用于遍历成员）。下面先介绍四个操作方法。

Set.prototype.add(value)：添加某个值，返回 Set 结构本身。
Set.prototype.delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
Set.prototype.has(value)：返回一个布尔值，表示该值是否为Set的成员。
Set.prototype.clear()：清除所有成员，没有返回值。
```

WeakSet 结构与 Set 类似，也是不重复的值的集合。

WeakSet 的成员只能是对象，而不能是其他类型的值。

其次，WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用。

### Map

只有对同一个对象的引用，Map 结构才将其视为同一个键。但是对于数值和字符串没有影响
Map 的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键。这就解决了同名属性碰撞（clash）的问题

注意：如果 Map 的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值严格相等，Map 将其视为一个键，比如 0 和-0 就是一个键，布尔值 true 和字符串 true 则是两个不同的键。

另外，undefined 和 null 也是两个不同的键。虽然 NaN 不严格相等于自身，但 Map 将其视为同一个键。

可以按照数据插入时的顺序遍历所有的元素。

- new Map()，或者以这样的方式初始化，`new Map([['foo', 3], ['bar', {}], ['baz', undefined]])`
- Map.prototype.set(k, v)
- Map.prototype.delete(k)，删除
- Map.prototype.clear()，清空 map
- Map.prototype.get(k)，对于 v 是引用类型，可以直接在返回值上原地修改（但是不能直接赋值），假如是基础类型，还是得用 set 修改。
- Map.prototype.has(k)
- Map.prototype.size，获取大小
- for (var [key, value] of mapObj)，遍历
- Map.prototype.forEach((v, k, map)=>{})，按插入顺序遍历，对每个元素做一次操作
- Map.prototype.keys()
- Map.prototype.values()
- Map.prototype.entries()

### Proxy

Proxy 在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，在这层拦截中可以对外界的访问进行过滤和改写。

```javascript
// new Proxy()表示生成一个Proxy实例，target参数表示所要拦截的目标对象，handler参数是一个配置对象，用来定制拦截行为，对于每一个被代理的操作，需要提供一个对应的处理函数，该函数将拦截对应的操作
// var proxy = new Proxy(target, handler);

var proxy = new Proxy(
  {},
  {
    get: function (target, propKey) {
      // get方法的两个参数分别是目标对象和所要访问的属性。
      return 35; // 由于拦截函数总是返回35，所以访问任何属性都得到35。
    },
  }
);

proxy.time; // 35
proxy.name; // 35
proxy.title; // 35

// 如果 handler === {}
```

可继承，即 Proxy 实例可以作为其他对象的原型对象

```javascript
var proxy = new Proxy(
  {},
  {
    get: function (target, propKey) {
      return 35;
    },
  }
);

let obj = Object.create(proxy);
obj.time; // 35
```

### 作用域

#### script、function、{}

- 在 script 声明的 var、let、const 为全局变量

- 在 function 声明的 var、let、const 为局部变量，只在本函数和下层函数中使用（可以使用闭包在外部使用输出）

- 在{}中声明的 var 仍为全局变量，但 let、const 为局部变量，其之外不可以使用

注意：for （var i=0; i<n ; i++）和 for( let i=0; i< n; i++)的区别，这里 var 定义了一个在 for 内的全局变量，let 定义了 n 个 for 内的局部变量。

如何使 var 在 for 中的异步函数使用不同的值，使用闭包，即在 for 内新声明函数（可以不带相关参数），并使用

#### 全局作用域

一个（var、let、const）变量在函数外面或者大括号 {} 外申明，那么就是定义了一个全局作用域的变量。一旦你申明了全局变量，那么你可以在任何地方使用它，甚至在函数中也行。

#### 局部作用域

在 JavaScript 中，有两种局部作用于：**函数作用域和块作用域**。

##### 函数作用域

当你在函数中申明一个（var、let、const）变量，你就只能够在这个函数范围内使用它。在范围之外你不能使用。

##### 块作用域

当你在一个大括号中 {} 使用 _const_ 或者 _let_ 申明变量，那么这个变量只能够在这个大括号范围内使用。

##### 嵌套/词法 作用域

当一个函数在另一个函数内定义，**内部的函数能够访问外部函数的变量**（闭包作用的原理）。我们称之为**词法作用域**。然而，外部的函数不能够访问内部函数的变量。

### 闭包

[参考](https://zhuanlan.zhihu.com/p/28921273)

[参考 2](https://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)

#### 定义

一个函数和对其周围状态（词法环境）的引用捆绑在一起（或者说函数被引用包围），这样的组合就是闭包（closure）。

**也就是说，闭包让你可以在一个内层函数中访问到其外层函数的作用域**。在 JavaScript 中，每当创建一个函数，闭包就会在函数创建的同时被创建出来。

在函数内声明内部函数，并将之返回（或作为返回对象的属性）。

闭包就是能够读取其他函数内部变量的函数，或者子函数在外调用，子函数所在的父函数的作用域不会被释放。

#### 原理

内层函数可访问外层函数的变量，将闭包返回就将上层函数的变量暴露

#### 作用

##### 解决副作用

在你想要的时候通过创建一个函数来激活内部的闭包。

```javascript
function prepareCake(flavor) {
  return function () {
    setTimeout((_) => console.log(`Made a ${flavor} cake!`), 1000);
  };
}

const makeCakeLater = prepareCake("banana");

// And later in your code...
makeCakeLater();
// Made a banana cake!
```

##### 使用闭包中的私有变量

```javascript
function secret(secretCode) {
  return {
    saySecretCode() {
      console.log(secretCode);
    },
  };
}

const theSecret = secret("CSS Tricks is amazing");
theSecret.saySecretCode();
// 'CSS Tricks is amazing'
```

##### 解决 var 的全局变量在异步函数调用中的变化

```javascript
for (let i = 0; i < 5; i++) {
  setTimeout(() => {
    console.log(i);
  }, 1000 * i);
}
//输出：0 1 2 3 4
for (var i = 0; i < 5; i++) {
  setTimeout(() => {
    console.log(i);
  }, 1000 * i);
}
// 输出： 5 5 5 5 5，5是因为最后i++跳出来的是5
// 使用闭包实现类似let的结果
for (var i = 0; i < 5; i++) {
  function f(i: number) {
    setTimeout(() => {
      console.log(i);
    }, 1000 * i);
  }
  f(i);
}
//输出：0 1 2 3 4
```

### [原型](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Object_prototypes)

#### 原型定义

[参考](https://www.zhihu.com/question/34183746)

1.对象有属性`__proto__` ， 指向该对象的构造函数的原型对象。 2.方法除了有属性`__proto__`,还有属性`prototype`，`prototype`指向该方法的原型对象。

在 JS 里，万物皆对象。方法（Function）是对象，方法的原型(Function.prototype)是对象。因此，它们都会具有对象共有的特点。

即：**对象具有属性** proto**，可称为隐式原型**，一个对象的隐式原型指向构造该对象的**构造函数的原型**，这也保证了实例能够访问在构造函数原型中定义的属性和方法。

方法这个特殊的对象，除了和其他对象一样有上述*proto*属性之外，还有自己特有的属性——原型属性（prototype），这个属性是一个指针，指向一个对象，这个对象的用途就是包含所有实例共享的属性和方法（我们把这个对象叫做原型对象）。原型对象也有一个属性，叫做 constructor，这个属性包含了一个指针，指回原构造函数。

JavaScript 常被描述为一种**基于原型的语言 (prototype-based language)**——每个对象拥有一个**原型对象**，对象以其原型为模板、从原型继承方法和属性。

原型对象也可能拥有原型，并从中继承方法和属性，一层一层、以此类推。这种关系常被称为**原型链 (prototype chain)**，它解释了为何一个对象会拥有定义在其他对象中的属性和方法。原型对象是一个内部对象，应当使用 `__proto__` 访问，

准确地说，这些属性和方法定义在 Object 的构造器函数(constructor functions)之上的`prototype`属性上，而非对象实例本身。

> 在传统的 OOP 中，首先定义“类”，此后创建对象实例时，类中定义的所有属性和方法都被复制到实例中。
>
> 在 JavaScript 中并不如此复制——而是在对象实例和它的构造器之间建立一个**链接**（它是**proto**属性，是从构造函数的`prototype`属性派生的），之后通过上溯原型链，在构造器中找到这些属性和方法。

对象的原型：可以通过`Object.getPrototypeOf(obj)`获得，是每个实例上都有的属性

`prototype`：构造函数的属性，`prototype` 属性包含（指向）一个对象，你在这个对象中定义需要被继承的成员。

```javascript
// 函数
function doSomething(){}
console.log( doSomething.prototype );
// 输出：
{
    constructor: ƒ doSomething(),
    __proto__: {
        constructor: ƒ Object(),
        hasOwnProperty: ƒ hasOwnProperty(),
        isPrototypeOf: ƒ isPrototypeOf(),
        propertyIsEnumerable: ƒ propertyIsEnumerable(),
        toLocaleString: ƒ toLocaleString(),
        toString: ƒ toString(),
        valueOf: ƒ valueOf()
    }
}

doSomething.prototype.foo = "bar";

{
    foo: "bar",
    constructor: ƒ doSomething(),
    __proto__: {
        constructor: ƒ Object(),
        hasOwnProperty: ƒ hasOwnProperty(),
        isPrototypeOf: ƒ isPrototypeOf(),
        propertyIsEnumerable: ƒ propertyIsEnumerable(),
        toLocaleString: ƒ toLocaleString(),
        toString: ƒ toString(),
        valueOf: ƒ valueOf()
    }
}


var doSomeInstancing = new doSomething();
doSomeInstancing.prop = "some value"; // add a property onto the object

{
    prop: "some value",
    __proto__: {
        foo: "bar",
        constructor: ƒ doSomething(),
        __proto__: {
            constructor: ƒ Object(),
            hasOwnProperty: ƒ hasOwnProperty(),
            isPrototypeOf: ƒ isPrototypeOf(),
            propertyIsEnumerable: ƒ propertyIsEnumerable(),
            toLocaleString: ƒ toLocaleString(),
            toString: ƒ toString(),
            valueOf: ƒ valueOf()
        }
    }
}
```

原型对象是一个内部对象，应当使用`__proto__` 访问， `doSomeInstancing` 的 `__proto__` 属性就是`doSomething.prototype`.

当你访问 `doSomeInstancing` 的一个属性, 浏览器首先查找 `doSomeInstancing` 是否有这个属性. 如果 `doSomeInstancing` 没有这个属性, 然后浏览器就会在 `doSomeInstancing` 的 `__proto__` 中查找这个属性(也就是 doSomething.prototype). 如果 doSomeInstancing 的 `__proto__` 有这个属性, 那么 doSomeInstancing 的 `__proto__` 上的这个属性就会被使用.

否则, 如果 doSomeInstancing 的 `__proto__` 没有这个属性, 浏览器就会去查找 doSomeInstancing 的 `__proto__` 的 `__proto__` ，看它是否有这个属性.

默认情况下, 所有函数的原型属性的 `__proto__` 就是 `window.Object.prototype`. 所以 doSomeInstancing 的 `__proto__` 的 `__proto__` (也就是 doSomething.prototype 的 `__proto__` (也就是 `Object.prototype`)) 会被查找是否有这个属性. 如果没有在它里面找到这个属性, 然后就会在 doSomeInstancing 的 `__proto__` 的 `__proto__` 的 `__proto__` 里面查找. 然而这有一个问题: doSomeInstancing 的 `__proto__` 的 `__proto__` 的 `__proto__` 不存在.

最后, 原型链上面的所有的 `__proto__` 都被找完了, 浏览器所有已经声明了的 `__proto__` 上都不存在这个属性，然后就得出结论，这个属性是 `undefined`.

// 层层向上直到一个对象的原型对象为 null 。

### [继承](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Inheritance)

JavaScript 继承的对象函数并不是通过复制而来，而是通过原型链继承（通常被称为 **原型式继承 ——** **prototypal inheritance）**

通过`function.call`来调用父类的构造函数，但是这样无法自动指定`Child.prototype`的值，这样`Child.prototype`就只能包含在构造函数里构造的属性，而没有方法。

因此我们利用`Object.create()`方法将`Parent.prototype`作为`Child.prototype`的原型对象，并改变其构造器指向，使之与`Child`关联。

现在`子类`的`prototype`的`constructor`属性指向的是`父类`，需将之改回子类

```javascript
function Parent(x1) {
  this.x1 = x1;
  this.f1 = function () {
    console.log("parent!");
  };
}
// 新的构造器
function Child(y1, x1) {
  Parent.call(this, x1);
  this.y1 = y1;
  this.f1 = function () {
    console.log("Child!");
  };
}
// 默认有一个空的原型属性，让Child()从Parent()的原型对象里继承方法
Child.prototype = Object.create(Parent.prototype);
// 现在Child()的prototype的constructor属性指向的是Parent()， 改回 Child
Child.prototype.constructor = Child;

let cObj = new Child(2, 1);
```

#### es6

```javascript
class c2 extends c1 {}

class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return "(" + this.x + ", " + this.y + ")";
  }
}

typeof Point; // "function"
Point === Point.prototype.constructor; // true，类的数据类型就是函数，类本身就指向构造函数。使用的时候，也是直接对类使用new命令，跟构造函数的用法完全一致。

class ColorPoint extends Point {
  constructor(x, y, color) {
    this.color = color; // ReferenceError
    super(x, y);
    this.color = color; // 正确
  }
}
```

### 事件循环

任务队列中，在每一次事件循环中，macrotask 只会提取一个执行，而 microtask 会一直提取，直到 microsoft 队列为空为止。

也就是说如果某个 microtask 任务被推入到执行中，那么当主线程任务执行完成后，会循环调用该队列任务中的下一个任务来执行，直到该任务队列到最后一个任务为止。

而事件循环每次只会入栈一个 macrotask，主线程执行完成该任务后又会检查 microtasks 队列并完成里面的所有任务后再执行 macrotask 的任务。

- macrotasks: setTimeout, setInterval, setImmediate, I/O, UI rendering
- microtasks: process.nextTick, Promise, MutationObserver

### 事件委托

不在事件的发生地（直接 dom）上设置监听函数，而是在其父元素上设置监听函数，通过**事件冒泡**，父元素可以监听到子元素上事件的触发，通过判断事件发生元素 DOM 的类型，来做出不同的响应。

### 事件监听

`Element.addEventListener(event, func, useCapture)`

第一个参数是事件的类型(如 "click" 或 "mousedown").

第二个参数是事件触发后调用的函数。

第三个参数是个布尔值用于描述事件是冒泡还是捕获。该参数是可选的。

事件传递有两种方式，冒泡和捕获

事件传递定义了元素事件触发的顺序，如果你将 P 元素插入到 div 元素中，用户点击 P 元素，

在冒泡中，内部元素先被触发，然后再触发外部元素，

捕获中，外部元素先被触发，在触发内部元素.

### 图片的懒加载和预加载

预加载：提前加载图片，当用户需要查看时可直接从本地缓存中渲染。

懒加载：懒加载的主要目的是作为服务器前端的优化，减少请求数或延迟请求数。

### mouseover 和 mouseenter 的区别

`mouseover`：当鼠标移入元素或其子元素都会触发事件，所以有一个重复触发，冒泡的过程。对应的移除事件是`mouseout`

`mouseenter`：当鼠标移除元素本身（不包含元素的子元素）会触发事件，也就是不会冒泡，对应的移除事件是`mouseleave`

### new 做的事

- new 操作符新建了一个空对象，这个对象的`__proto__`指向构造函数的`prototype`

- 执行构造函数后返回这个对象。

### 拖拽功能

三个事件，分别是`mousedown`，`mousemove`，`mouseup`

两个坐标，`clientX`，`clientY`标识的是鼠标的坐标，分别标识横坐标和纵坐标，并且我们用`offsetX`和`offsetY`来表示元素的元素的初始坐标

拖拽的同时是绝对定位，我们改变的是绝对定位条件下的`left`以及`top`等等值。

### 防抖

事件被触发 n 秒后再执行回调，如果在这 n 秒内又被触发，则重新计时。

使用场景：

- 按钮提交：只执行最后提交的一次，以防止多次提交。
- 搜索框联想：防止联想发送请求，只发送最后一次

```javascript
function debunce(fn, wait) {
  var timeout;
  return function () {
    var context = this,
      args = arguments;
    clearTimeout(timeout);
    timeout = setTimeout(function () {
      fn.apply(context, args);
    }, wait);
  };
}
function realFn() {
  console.log("Success");
}
window.addEventListener("scroll", debounce(realFn, 500));
```

### 节流

规定在一个单位时间内，只能触发一次事件。如果在这个时间段内多次触发，只有最早的一次生效。

只允许一个函数在 X 毫秒内执行一次。与防抖相比，节流函数最主要的不同在于它保证在 X 毫秒内至少执行一次我们希望触发的事件 handler。

```javascript
// 使用定时器实现
function throttle(func, wait) {
  var timeout;
  return function () {
    var context = this;
    var args = arguments;
    if (!timeout) {
      timeout = setTimeout(function () {
        timeout = null;
        func.apply(context, args);
      }, wait);
    }
  };
}
```

### setTimeout(fn,100);100 毫秒是如何权衡的

`setTimeout()`函数只是将事件插入了任务列表，必须等到当前代码执行完，主线程才会去执行它指定的回调函数，有可能要等很久，所以没有办法保证回调函数一定会在`setTimeout`指定的时间内执行，100 毫秒是插入队列的时间+等待的时间

### 浅拷贝和深拷贝

#### 浅拷贝

如果是数组，可以利用 slice，concat 方法返回一个新数组来实现拷贝，假如数组嵌套了对象或者数组的话，使用 concat 方法克隆并不完整，如果数组元素是基本类型，就会拷贝一份，互不影响，而如果是对象或数组，就会只拷贝对象和数组的引用，这样我们无论在新旧数组进行了修改，两者都会发生变化，我们把这种复制引用的拷贝方法称为浅拷贝，

深拷贝就是指完全的拷贝一个对象，即使嵌套了对象，两者也互相分离，修改一个对象的属性，不会影响另一个

### 深度复制

```javascript
function deepCopy(obj) {
  // 判断是数组还是字典类对象
  let newObj = obj instanceof Array ? [] : {};
  for (let i in obj) {
    // 根据是否是对象来决定是否递归
    let s = typeof obj[i] == "object" ? deepCopy(obj[i]) : obj[i];
    newObj[i] = s;
  }
  return newObj;
}

// 2
var s = JSON.parse(JSON.stringify(obj));
//原理是JOSN对象中的stringify可以把一个js对象序列化为一个JSON字符串，parse可以把JSON字符串反序列化为一个js对象，通过这两个方法，也可以实现对象的深复制。
```

### 实现 bind

```javascript
Function.prototype.myBind = function (context) {
  var fn = this; // 原环境，谁调用的
  return function (...args) {
    // 提供后续调用
    return fn.call(context, ...args);
  };
};
var fun = function (x) {
  return x ** 2;
};
var s = fun.myBind([1, 2, 3])(3);
console.log(s);
```

### 函数柯里化

柯里化（Currying），把接受多个参数的函数转换成接受一个单一参数的函数。[参考](https://segmentfault.com/a/1190000018265172)

### ajax 用 promise 封装

- 创建`XMLHttpRequest`对象。
- 使用`open`方法设置和服务器的交换信息，需要传入参数，例如请求类型、url、是否异步
- send() 发送数据进行交互
- 注册`onreadystatechange`事件
- 更新数据或界面

```javascript
var myAjax = function(action, url) {
  return new Promise((resolve, reject)=>{
    let xhr = new XMLHttpRequest();
    xhr.open(action, url, true);
    xhr.send();
    xhr.onreadystatechange = function (){
      if (xhr.status === 200) {
        let json = JSON.parse(xhr.responseText);
        resolve(json);
      }
      else {
        reject(xhr.status)
      }
   })
    .then(res=>{})
    .catch(err => {})

  }
}
```

### Proxy 和 Object.defineProperty 监听对象属性改变

- `Object.defineProperty`：vue2 和 es5 使用

  vue2 结合订阅发布者模式实现双向绑定

```javascript
Object.defineProperty(user,'name',{
  set：function(key,value){}
})
```

- `Proxy`：vue3 和 es6 使用

```javascript
var  user = new Proxy({}，{
 set：function(target,key,value,receiver){}
})
```

### setInterval

固定时间间隔执行一次

#### 使用 setTimeout 实现 setInterval

```javascript
function myFun() {
  // do something
  setTimeout(myFun, 200);
}
```

### 实现 sleep 函数

```javascript
function mySleep(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}
mySleep(500).then(() => {
  // do something
});
```

### 转换-\_命名到驼峰命名

```javascript
function cssStyle2DomStyle(sName) {
  let newStr = sName.replace(
    /([\d\D])-([\d\D])/g,
    (match, $1, $2) => $1 + $2.toUpperCase()
  );
  return newStr[0] === "-" ? newStr.slice(1) : newStr;
}
```

### 实现 Flat

```javascript
function flat(arr) {
  return arr.reduce((a, b) => a.concat(b instanceof Array ? flat(b) : b), []);
}
```

### 链式调用

调用完的方法将自身实例返回

### node 服务器

```javascript
const http = require("http");

const hostname = "127.0.0.1";
const port = 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader("Content-Type", "text/plain");
  res.end("Hello World");
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

### 垃圾回收

- 引用计数

  - 当声明了一个变量并将某个对象赋予它时，这个对象的引用数加一，反之减一。

  - 对引用数为 0 的变量清除。

    // 可以即刻回收垃圾，但是计数过程过于复杂，并且循环引用无法回收。

- 标记清除

  - 标记阶段，垃圾回收器由根对象开始遍历，所有根对象能访问到的对象会被标记为可到达对象。

  - 清除阶段，对内存从头到尾线性遍历，没有标记的对象一律回收。

    // 此方法实现简单，但是会造成大量的内存碎片。

### C++、Java、JavaScript 这三种语言的区别

- 静态类型/动态类型

  编译还是运行的时候知道变量的类型

- 编译型/解释型

  - c++，编译型
  - js，解释型
  - java，编译成字节码再用解释器

### 正则表达式

正则表达式是用于匹配字符串中字符组合的模式。在 JavaScript 中，正则表达式也是对象。这些模式被用于 [`RegExp`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp) 的 [`exec`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec) 和 [`test`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test) 方法, 以及 [`String`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String) 的 [`match`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/match)、[`matchAll`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/matchAll)、[`replace`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace)、[`search`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/search) 和 [`split`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/split) 方法。

#### 模式

`/g` ，使用全局，在整个字符串查找并返回所有匹配结果

`/i`，忽略大小写

#### 常用方法

- reg.test(str)

  测试是否含有该匹配，`/reg/.test(str)=>bool`，检测字符串是否满足正则规则。

  当使用`/g`时，reg 对象的`lastIndex`属性改变。

  ```js
  var regex = /foo/g;

  // regex.lastIndex is at 0
  regex.test("foo"); // true

  // regex.lastIndex is now at 3
  regex.test("foo"); // fals
  ```

- str.match(reg)

  - 如果使用 g 标志，则将返回与完整正则表达式匹配的所有结果，但不会返回捕获组。
  - 如果未使用 g 标志，则仅返回第一个完整匹配及其相关的捕获组（`Array`）。 在这种情况下，返回的项目将具有如下所述的其他属性。
    - `groups`: 一个捕获组数组 或 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)（如果没有定义命名捕获组）。
    - `index`: 匹配的结果的开始位置
    - `input`: 搜索的字符串.

- str.replace(reg)

  字符串的替换方法，第二个参数为自定义替换值

  `let newStr = sName.replace(/([\d\D])-([\d\D])/g, (match, $1, $2) => $1 + $2.toUpperCase());`，小写是`toLowerCase()`

#### 规则

`^`，匹配句首，`$`，匹配句尾；

`\d`，数字字符，`\D`，非数字字符；

`\w`，单字字符，匹配一个单字字符（字母、数字或者下划线），等价于 `[A-Za-z0-9_]`

`\s`，匹配一个空白字符，包括空格、制表符、换页符和换行符。

`{m, n}`，有[m, n]次匹配

`()`，后续可以使用`$n`

## TypeScript

### 联合类型

联合类型（Union Types）表示取值可以为多种类型中的一种。

```ts
let myFavoriteNumber: string | number;
myFavoriteNumber = "seven";
myFavoriteNumber = 7;
let myFavoriteNumber: string | number;
myFavoriteNumber = true;

// index.ts(2,1): error TS2322: Type 'boolean' is not assignable to type 'string | number'.
//   Type 'boolean' is not assignable to type 'number'.
```

联合类型使用 `|` 分隔每个类型。

这里的 `let myFavoriteNumber: string | number` 的含义是，允许 `myFavoriteNumber` 的类型是 `string` 或者 `number`，但是不能是其他类型。

当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们**只能访问此联合类型的所有类型里共有的属性或方法**。

联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型

### 泛型

泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

```javascript
function createArray(length: number, value: any): Array<any> {
  let result = [];
  for (let i = 0; i < length; i++) {
    result[i] = value;
  }
  return result;
}

createArray(3, "x"); // ['x', 'x', 'x']
```

```typescript
function createArray<T>(length: number, value: T): Array<T> {
  let result: T[] = [];
  for (let i = 0; i < length; i++) {
    result[i] = value;
  }
  return result;
}
// 当然，也可以不手动指定，而让类型推论自动推算出来，去掉<string>也可以
createArray<string>(3, "x"); // ['x', 'x', 'x']
```

#### 多个类型参数

定义泛型的时候，可以一次定义多个类型参数

```typescript
function swap<T, U>(tuple: [T, U]): [U, T] {
  return [tuple[1], tuple[0]];
}

swap([7, "seven"]); // ['seven', 7]
```

#### 泛型约束

在函数内部使用泛型变量的时候，由于事先不知道它是哪种类型，所以不能随意的操作它的属性或方法：

```ts
function loggingIdentity<T>(arg: T): T {
  console.log(arg.length);
  return arg;
}

// index.ts(2,19): error TS2339: Property 'length' does not exist on type 'T'.
```

上例中，泛型 `T` 不一定包含属性 `length`，所以编译的时候报错了。

这时，我们可以对泛型进行约束，只允许这个函数传入那些包含 `length` 属性的变量。这就是泛型约束：

```ts
interface Lengthwise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}
```

多个类型参数之间也可以互相约束：

```ts
function copyFields<T extends U, U>(target: T, source: U): T {
  for (let id in source) {
    target[id] = (<T>source)[id];
  }
  return target;
}

let x = { a: 1, b: 2, c: 3, d: 4 };

copyFields(x, { b: 10, d: 20 });
```

上例中，我们使用了两个类型参数，其中要求 `T` 继承 `U`，这样就保证了 `U` 上不会出现 `T` 中不存在的字段。

### 接口

[参考](https://ts.xcatliu.com/basics/type-of-object-interfaces.html)

在 TypeScript 中，我们使用接口（Interfaces）来定义对象的类型。类似 C++里的 struct 结构体

## git 常用命令

- Workspace：工作区
- Index / Stage：暂存区
- Repository：仓库区（或本地仓库）
- Remote：远程仓库

- ```bash
  # 恢复暂存区的所有文件到工作区
  $ git checkout .
  ```

- ```bash
  # 暂时将未提交的变化移除，稍后再移入
  $ git stash
  $ git stash pop
  ```

## 常用的 mysql

```mysql
select * from ta where a like '%a%' order by a DESC;
select ta.a, tb.b from ta left join tb on ta.id = tb.id;
```

## 框架相关

### MV-X

MV-X 的目的是把程序的数据、业务逻辑和界面三部分解耦，来分离关注点。

- Model

  数据和数据的处理方法

- View

  视图，负责对数据的展示

- Controller

  定义界面对用户输入的响应方式，连接模型和视图，用于控制应用程序的流程，处理用户的行为和数据上的改变

- Presenter

  作为 View 和 Model 的中间人，从 View 到 Model 和从 Model 到 View 的数据进行“手动同步”

- VM

  数据的双向绑定

#### MVC

在 Controller 中响应用户对 View 的事件，Controller 调用 Model 的接口对数据进行操作，一旦 Model 发生变化 Model 就通知相关视图进行更新。

#### MVP

手动绑定

#### MVVM

mvvm 把 view 和 model 的同步逻辑自动化，只需要告诉 view 的显示内容与 model 哪一部分对应即可。采用双向绑定。

### Virtual dom

virtual dom 是将真实的 dom 的数据抽取出来，以对象的形式模拟树形结构。当状态改变时，重新构造虚拟树，新树旧树比较，把差异更新到真正的 dom 树上，来更新视图。

diff 算法比较的也是 virtual dom。

diff，就是新旧虚拟 dom 的比较，如果有差异就以新的为准，然后再插入的真实的 dom 中，重新渲染

### computed 和 watch

#### 计算属性 computed

支持缓存，只有依赖数据发生变化时，才会重新进行计算函数；

计算属性的函数中都有一个 get(默认具有，获取计算属性)和 set(手动添加，设置计算属性)方法；

计算属性是自动监听依赖值的变化，从而动态返回内容。

#### 侦听属性 watch

不支持缓存，只要数据发生变化，就会执行侦听函数；

侦听属性内支持异步操作；

侦听属性的值可以是一个对象，接收 handler 回调，deep，immediate 三个属性；

监听是一个过程，在监听的值变化时，可以触发一个回调，并做一些其他事情。

### webpack

#### webpack 定义

本质上，**webpack** 是一个用于现代 JavaScript 应用程序的*静态模块打包工具*。当 webpack 处理应用程序时，它会在内部构建一个 [依赖图(dependency graph)](https://webpack.docschina.org/concepts/dependency-graph/)，此依赖图对应映射到项目所需的每个模块，并生成一个或多个 _bundle_。

#### 核心概念

- 入口，entry

  指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

- 输出，output

  在哪里输出它所创建的 _bundle_，以及如何命名这些文件。

  主要输出文件的默认值是 `./dist/main.js`，其他生成文件默认放置在 `./dist` 文件夹中。

- loader

  webpack 只能理解 JavaScript 和 JSON 文件，**loader** 让 webpack 能够去处理其他类型的文件，并将它们转换为有效 [模块](https://webpack.docschina.org/concepts/modules)，以供应用程序使用，以及被添加到依赖图中。

  **loader** 有两个属性：

  1. `test` 属性，识别出哪些文件会被转换。
  2. `use` 属性，定义出在进行转换时，应该使用哪个 loader。

  注：webpack 打包的文件默认是不支持 ES6 的，我们需要用 babel 转译。在 loader 中使用 babel。

- 插件，plugin

  loader 用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。

  想要使用一个插件，你只需要 `require()` 它，然后把它添加到 `plugins` 数组中。多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 `new` 操作符来创建一个插件实例。

- 模式，mode

  通过选择 `development`, `production` 或 `none` 之中的一个，来设置 `mode` 参数，你可以启用 webpack 内置在相应环境下的优化。其默认值为 `production`。

### 组合式 API 和配置式 API

在 vue2 中，我们需要将代码划分写到 mounted、watch、computed，这个一定程度上规范了代码的整洁性，但是当组件内容过于复杂时，代码将变得复杂难懂。一个细小的逻辑任务，其相关代码可能要分散到多个选项中。而 setup 中可以调用 computed、watch 等，使之能够像函数般在同一处复用。

### 生命周期

Vue 实例有一个完整的生命周期，从开始创建、初始化数据、编译模板、挂载 Dom、渲染 → 更新 → 渲染、销毁等一系列过程。

#### beforeCreate

数据对象 data = undefined；vue 实例的挂载元素 el=undefined

#### created

data 有了，可以访问数据和方法，但未挂载到 dom；el 还是 undefined

#### beforeMount

data、el 都有了，但没有挂载

#### mounted

vue 实例已经挂载到了真实 dom 上，可以获取 dom 节点。但是无法确定是否已经渲染好，于是需要用 nextTick，在回调函数里进行 dom 操作

#### beforeUpdate

响应式数据更新时调用，发生在虚拟 DOM 打补丁之前，适合在更新之前访问现有的 DOM

#### updated

虚拟 DOM 重新渲染和打补丁之后调用，组成新的 DOM 已经更新，避免在这个钩子函数中操作数据，防止死循环

#### beforeDestory

#### destoried

### PWA

PWA 全称 Progressive Web App ，即渐进式 WEB 应用。一个 PWA 应用首先是一个网页, 可以通过 Web 技术编写出一个网页应用. 随后添加上 App Manifest 和 Service Worker 来实现 PWA 的安装和离线等功能

### gulp

gulp 强调的是前端开发的工作流程，我们可以通过配置一系列的 task ，定义 task 处理的事务，然后定义执行顺序，来让 gulp 执行这些 task，从而构建项目的整个前端开发流程。

### redux

在组件化的应用中，会有着大量的组件层级关系，深嵌套的组件与浅层父组件进行数据交互，变得十分繁琐困难。而 redux，站在一个服务级别的角度，可以毫无阻碍地将应用的状态传递到每一个层级的组件中。redux 就相当于整个应用的管家。

### vue 双向绑定

vue2 数据双向绑定是通过数据劫持，结合发布者-订阅者模式的方式来实现的。利用了 Object.defineProperty() 这个方法重新定义了对象获取属性值(get)和设置属性值(set)。

Vue3 使用的是 proxy，这个可以自动检测到对象深层的变化。

[参考](https://juejin.cn/post/6844904088119853063)

递归遍历 data 中的数据，使用 [Object.defineProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)劫持 getter 和 setter，在 getter 中做数据依赖收集处理，在 setter 中 监听数据的变化，并通知订阅当前数据的地方。

检测不到对象属性的添加和删除：当你在对象上新加了一个属性`newProperty`，当前新加的这个属性并没有加入 vue 检测数据更新的机制(因为是在初始化之后添加的)。`vue.$set`是能让 vue 知道你添加了属性, 它会给你做处理，`$set`内部也是通过调用`Object.defineProperty()`去处理的

无法监控到数组下标的变化，导致直接通过数组的下标给数组设置值，不能实时响应。

当 data 中数据比较多且层级很深的时候，会有性能问题，因为要遍历 data 中所有的数据并给其设置成响应式的。

给对象新增一个属性，内部并没有监听到，新增的属性需要手动再次使用`Object.defineProperty()`进行监听。 这就是为什么 `vue 2.x`中 检测不到对象属性的添加和删除的原因。

新增的属性，proxy 并不需要重新添加响应式处理，因为 `Proxy` 是对对象的操作，只要你访问对象，就会走到 `Proxy` 的逻辑中。

**Object.defineProperty 只能劫持对象的属性（需要遍历对象的每个属性），而 Proxy 是直接代理对象。**

**Object.defineProperty 对新增属性需要手动进行 Observe。**

**使用 vue2 给 `data` 中的数组或对象新增属性时，需要使用 `vm.$set` 才能保证新增的属性也是响应式的**

在 `set` 方法中，对 `target` 是数组和对象做了分别的处理，`target` 是数组时，会调用重写过的 `splice` 方法进行手动 `Observe` 。

对于对象，如果 `key` 本来就是对象的属性，则直接修改值触发更新，否则调用 `defineReactive`方法重新定义响应式对象。

如果采用 `proxy` 实现，`Proxy` 通过 `set(target, propKey, value, receiver)` 拦截对象属性的设置，是可以拦截到对象的新增属性的。

不止如此，`Proxy` 对数组的方法也可以监测到，不需要像上面 vue2.x 源码中那样进行 `hack`。

Proxy 支持 13 种拦截操作，这是 defineProperty 所不具有的

1. vue2.x 中无法通过数组索引来实现响应式数据的自动更新是 vue 本身的设计导致的，不是 `defineProperty` 的锅。
2. `Object.defineProperty` 和 `Proxy` 本质差别是，`defineProperty` 只能对属性进行劫持，新增属性需要手动 `Observe` 的问题。

### vue2 vue3 监听数组

vue2 设置了 7 个变异数组（push、pop、shift、unshift、splice、sort、reverse）的 hack 方法来监听数组变化。vm.items[indexOfItem] = newValue 这种是无法检测的。

`Object.defineProperty` 在数组中的表现和在对象中的表现是一致的，数组的索引就可以看做是对象中的 `key`。

1. 通过索引访问或设置对应元素的值时，可以触发 `getter` 和 `setter` 方法
2. 通过 `push` 或 `unshift` 会增加索引，对于新增加的属性，需要再手动初始化才能被 `observe`。
3. 通过 `pop` 或 `shift` 删除元素，会删除并更新索引，也会触发 `setter` 和 `getter` 方法。

所以，`Object.defineProperty` 是有监控数组下标变化的能力的，只是 vue2.x 放弃了这个特性。

### vm.$nextTick([callback, context])

使用 nextTick 是为了可以获取更新后的 DOM。

触发时机：在同一事件循环中的数据变化后，DOM 完成更新，立即执行 nextTick 的回调函数，或者 Vue.nextTick().then()

### vue 和 react 有什么区别？

react 整体是函数式的思想，把组件设计成纯组件，状态和逻辑通过参数传入，所以在 react 中，是单向数据流；

vue 的思想是响应式的，也就是基于是数据可变的，通过对每一个属性建立 Watcher 来监听，当属性变化的时候，响应式的更新对应的虚拟 dom。

### vuex 有哪几种状态和属性

- modules：模块化 vuex

- state 中保存着共有数据，数据是响应式的

- // getter 可以对 state 进行计算操作，主要用来过滤一些数据，可以在多组件之间复用

- mutations 定义的方法动态修改 state 中的数据，通过 commit 提交方法，方法必须是同步的

- actions 将 mutations 里面处理数据的方法变成异步的，就是异步操作数据，通 store.dispatch 来分发 actions，把异步的方法写在 actions 中，通过 commit 提交 mutations，进行修改数据。

### vuex 的流程

页面通过 store.dispatch 异步提交事件到 action。action 通过 commit 把对应参数同步提交到 mutation。
mutation 会修改 state 中对于的值。 最后通过 getter 把对应值跑出去，在页面的计算属性中通过 mapGetter 来动态获取 state 中的值

### vue 路由

前端路由：更新视图，但不重新请求页面。目前在浏览器中主要有两种实现方式：hash、history

#### 两种模式

- hash ——即地址栏 URL 中的#符号（此 hsah 不是密码学里的散列运算） hash 虽然出现 URL 中，但不会被包含在 HTTP 请求中，对后端完全没有影响，因此改变 hash 不会重新加载页面。

- history ——利用了 HTML5 History Interface 中新增的 pushState() 和 replaceState() 方法

通过 location.pathname 获取到当前 url 的路由地址；

这两个方法应用于浏览器的历史记录站，在当前已有的 back、forward、go 的基础之上，它们提供了对历史记录进行修改的功能。

history 模式下，通过 pushState 和 replaceState 方法可以修改 url 地址，当调用它们修改浏览器历史栈后，虽然当前 url 改变了，但浏览器不会立即发送请求该 url。结合 popstate 方法监听 url 中路由的变化，来更新视图

history 模式的特点是实现更加方便，可读性更强，同时因为没有了#，url 也更加美观；

它的劣势也比较明显，当用户刷新或直接输入地址时会向服务器发送一个请求，所以 history 模式需要服务端同学进行支持，将路由都重定向到根路由

#### history 前端路由引起的问题

通常情况下，Vue 是单页应用，url 是拼接的假链，假如直接进入该 url，就会无法渲染页面，导致无法正确加载。

解决办法：Nuxt 有 generate 的功能配置，可实现特定 router 的页面预先渲染。

或者使用 ssr 模式。// [参考](https://ssr.vuejs.org/zh/#服务器端渲染-vs-预渲染-ssr-vs-prerendering)

为什么不选择 ssr，涉及构建设置和部署的更多要求，更多的服务器端负载，会比仅仅提供静态文件的 server 更加大量占用 CPU 资源

### 热重载

“热重载”不只是当你修改文件的时候简单重新加载页面。启用热重载后，当你修改 `.vue` 文件时，该组件的所有实例将在**不刷新页面**的情况下被替换。它甚至保持了应用程序和被替换组件的当前状态！当你调整模版或者修改样式时，这极大地提高了开发体验。

//感觉并不是面试该说的话。

### 观察者模式/订阅发布模式

当对象间存在一对多关系时，则使用观察者模式（Observer Pattern）。比如，当一个对象被修改时，则会自动通知依赖它的对象。观察者模式属于行为型模式。观察者模式比订阅发布模式多了一层中介，类似用户之间的微博 app。

### CI/CD

CI/CD 其实就是一个流程，用于实现应用开发中的高度持续自动化和持续监控。

CI/CD 是一种通过在应用开发阶段引入自动化来频繁向客户交付应用的方法。

CI/CD 的核心概念是**持续集成、持续交付和持续部署**。作为一个面向开发和运营团队的解决方案，CI/CD 主要针对在集成新代码时所引发的问题。

- “CI”始终指持续集成，它属于开发人员的自动化流程。成功的 CI 意味着应用代码的新更改会定期构建、测试并合并到共享存储库中。
- “CD”指的是持续交付，或持续部署
  - 持续交付，指开发人员对应用的更改会自动进行错误测试并上传到存储库
  - 持续部署，指自动将开发人员的更改从存储库发布到生产环境，以供客户使用。

[参考](https://juejin.cn/post/6844903976693940231#heading-55)

### vite

在浏览器里使用 [ES module](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) 是使用 http 请求拿到模块，所以 vite 必须提供一个 web server 去代理这些模块，上文中提到的 `koa` 就是负责这个事情，vite 通过对请求路径的劫持获取资源的内容返回给浏览器，不过 vite 对于模块导入做了特殊处理。

vite 对 `import` 都做了一层处理，其过程如下：

1. 在 koa 中间件里获取请求 body
2. 通过 [es-module-lexer](https://www.npmjs.com/package/es-module-lexer) 解析资源 ast 拿到 import 的内容
3. 判断 import 的资源是否是绝对路径，绝对视为 npm 模块
4. 返回处理后的资源路径：`"vue" => "/@modules/vue"`

#### 为什么需要 @modules？

如果我们在模块里写 `import vue from 'vue'`，浏览器中的 esm 并不能获取到导入的模块内容。

因为 `vue` 这个模块安装在 node_modules 里，以往使用 webpack 来获取。webpack 遇到上面的代码，会帮我们做以下几件事：

- 获取这段代码的内容
- 解析成 AST
- 遍历 AST 拿到 `import` 语句中的包的名称
- 使用 [enhanced-resolve](https://github.com/webpack/enhanced-resolve) 拿到包的实际地址进行打包。

但是浏览器中 ESM 无法直接访问项目下的 node_modules，所以 vite 对所有 import 都做了处理，用带有 **@modules** 的前缀重写它们。

目前社区中大部分模块或 npm 包都没有设置默认导出 esm，而是导出了 cjs 的包，既然 vue3.0 需要额外处理才能拿到 esm 的包内容。

不过 vite 在最近的更新中，加入了 `optimize` 命令，这个命令专门为解决模块引用的坑而开发，例如我们要在 vite 中使用 lodash，只需要在 **vite.config.js** （vite 配置文件）中，配置 `optimizeDeps` 对象，在 `include` 数组中添加 lodash。

这样 vite 在执行 `runOptimize` 的时候中会使用 roolup 对 lodash 包重新编译，将编译成符合 esm 模块规范的新的包放入 node_modules 下的 .**vite_opt_cache** 中，然后配合 resolver 对 `lodash` 的导入进行处理：使用编译后的包内容代替原来 lodash 的包的内容，这样就解决了 vite 中不能使用 cjs 包的问题，这部分代码在 [depOptimizer.ts](https://github.com/vitejs/vite/blob/master/src/node/depOptimizer.ts) 里。

## 数据结构

### 查找方法

#### 二分查找

```javascript
var find = (arr, target) => {
  let l = 0,
    h = arr.length - 1;
  let m = null;
  while (l <= h) {
    m = Math.floor((l + h) / 2);
    if (target === arr[m]) {
      return m;
    } else if (target < arr[m]) {
      h = m - 1;
    } else {
      l = m + 1;
    }
  }
  return -1;
};
console.log(find([0, 1, 1, 2, 6, 6, 6, 8], 6));

// 查找重复元素里index最小的那个，也可以说是没有那值，返回稍大的值的index
var find2 = (arr, target) => {
  let l = 0,
    h = arr.length - 1;
  let m = null;
  while (l <= h) {
    m = Math.floor((l + h) / 2);
    if (target <= arr[m]) {
      h = m - 1;
    } else {
      l = m + 1;
    }
  }
  return l;
};
```

### 排序方法

#### 冒泡排序

```javascript
var nums = [0, 1, 2, 1, 3, 8, 7, 6];
var popSort = (arr) => {
  var isSwap = false;
  for (let i = 0; i < arr.length; i++) {
    for (let j = 0; j < arr.length - i - 1; j++) {
      if (arr[j] > arr[j + 1]) {
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
        isSwap = true;
      }
    }
    if (!isSwap) {
      break;
    }
    isSwap = false;
  }
  return arr;
};
popSort(nums);
console.log(nums);
```

#### 快速排序

```javascript
var partition = (arr, l, h) => {
  let p = arr[l];
  while (l < h) {
    while (l < h && arr[h] >= p) {
      h -= 1;
    }
    arr[l] = arr[h];
    while (l < h && arr[l] <= p) {
      l += 1;
    }
  }
  arr[l] = p;
  return l;
};
var qSort = (arr, low, high) => {
  if (low < high) {
    // 随机快排，取值为[low, high]
    let i = Math.round(Math.random() * (high - low) + low);
    [arr[i], arr[low]] = [arr[low], arr[i]];
    pLoc = partition(arr, low, high);
    qSort(arr, low, pLoc - 1);
    qSort(arr, pLoc + 1, high);
  }
};

var numList = [0, 1, 2, 1, 3, 8];
qSort(numList, 0, numList.length - 1);
console.log(numList);
```

#### 归并排序

```javascript
var mSort = (arr) => {
  if (arr.length === 1) {
    return arr;
  }
  let mid = Math.floor(arr.length / 2);
  return merge(mSort(arr.slice(0, mid)), mSort(arr.slice(mid)));
};
var merge = (l, r) => {
  let res = [];
  while (l.length > 0 && r.length > 0) {
    if (l[0] <= r[0]) {
      res.push(l.shift());
    } else {
      res.push(r.shift());
    }
  }
  return res.concat(l.length > 0 ? l : r);
};
var s = mSort([0, 1, 2, 1, 3, 8, 7, 6]);
console.log(s);
```

### 二叉树

```javascript
//  Definition for a binary tree node.
class TreeNode {
  constructor(val, left, right) {
    this.val = val === undefined ? 0 : val;
    this.left = left === undefined ? null : left;
    this.right = right === undefined ? null : right;
  }
}
var tree = new TreeNode(
  "A",
  new TreeNode("B", new TreeNode("D"), new TreeNode("E")),
  new TreeNode("C", new TreeNode("F"), new TreeNode("G"))
);

/**
 * @param {TreeNode} root
 * @return {number[][]}
 */
var levelOrder = function (root) {
  if (!root) {
    return [];
  }
  root.row = 0;
  var queue = [root];
  var p = null;
  var res = [];
  while (queue.length > 0) {
    p = queue.shift();
    if (p.row >= res.length) {
      res.push([p.val]);
    } else {
      res[p.row].push(p.val);
    }
    p.left && (p.left.row = p.row + 1) && queue.push(p.left);
    p.right && (p.right.row = p.row + 1) && queue.push(p.right);
  }
  return res;
};
// console.log(levelOrder(tree));

/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var preRes = [];
var preorderTraversal = function (root) {
  if (!root) {
    return null;
  }
  preRes.push(root.val);
  preorderTraversal(root.left);
  preorderTraversal(root.right);
};
preorderTraversal(tree);
// console.log(preRes);

var preOrder2 = (root) => {
  if (!root) {
    return [];
  }
  var stack = [root];
  var res = [];
  var p = null;
  while (stack.length > 0) {
    p = stack.pop();
    res.push(p.val);
    p.right && stack.push(p.right);
    p.left && stack.push(p.left);
  }
  return res;
};

var preOrder3 = (root) => {
  var stack = [];
  var res = [];
  var p = root;
  while (stack.length > 0 || p) {
    if (p) {
      stack.push(p);
      res.push(p.val);
      p = p.left;
    } else {
      p = stack.pop();
      p = p.right;
    }
  }
  return res;
};

console.log("preOrder2: ", preOrder2(tree));
console.log("preOrder3: ", preOrder3(tree));

var inRes = [];
var inOrderRecur = (root) => {
  if (!root) {
    return null;
  }
  inOrderRecur(root.left);
  inRes.push(root.val);
  inOrderRecur(root.right);
};
inOrderRecur(tree);
console.log("inOrderRecur: ", inRes);

var inOrder2 = (root) => {
  let res = [];
  let stack = [];
  let p = root;
  while (stack.length > 0 || p) {
    if (p) {
      stack.push(p);
      p = p.left;
    } else {
      p = stack.pop();
      res.push(p.val);
      p = p.right;
    }
  }
  return res;
};
console.log("inOrder2: ", inOrder2(tree));

var postRes = [];
var postOrderRecur = (root) => {
  if (!root) {
    return;
  }
  postOrderRecur(root.left);
  postOrderRecur(root.right);
  postRes.push(root.val);
};
postOrderRecur(tree);
console.log("postOrderRecur: ", postRes);

var postOrder2 = (root) => {
  let res = [];
  let stack = [];
  let p = root;
  let last = null;
  while (stack.length > 0 || p) {
    if (p) {
      stack.push(p);
      p = p.left;
    } else {
      p = stack.pop();
      // 如果右子树为空或者右子树已经遍历
      if (!p.right || p.right == last) {
        res.push(p.val);
        last = p;
        p = null;
      } else {
        stack.push(p);
        p = p.right;
      }
    }
  }
  return res;
};
console.log("postOrder2: ", postOrder2(tree));
```

## 网络安全

### 对称加密

定义：有一个密钥，可以对一段内容加密，加密后只有用它才能看到原文。

如果通信双方各持有同一个密钥，且没有别人只带，那么这双方的通信安全可以保证，除非密钥被破解。

但是存在问题：这个密钥怎么让通信双方知道，同时不被别人知道。因为在服务器生成密钥传输给客户端的时候有可能被劫持。

这时候就需要非对称加密了。

### 非对称加密

定义：有两把密钥，一把叫公钥，一把叫私钥。用公钥加密的内容必须用私钥才能解开，用私钥加密的内容只有公钥能解开。

服务器把公钥明文传给客户端可能会被劫持，服务器向客户端传递的数据可能会被解析，只能保证客户端到服务器的通信安全。假如使用两对非对称密钥可以解决，但太过耗时。

非对称加密比较耗时，对称加密快很多。

所以 https 采用 非对称加密+对称加密，且非对称加密解密只使用一次就可以。

- 服务器用于**非对称加密**的公钥 A、私钥 AA
- 浏览器向服务器请求，服务器把公钥 A 明文发给浏览器
- 浏览器随机生成一个对称加密密钥 B，用公钥 A 加密后传给服务器
- 服务器用私钥 AA 解密得到密钥 B
- 这样双方都拥有对称密钥 B 了，且别人无法知道它

这样还是存在漏洞，例如中间人攻击

### 中间人攻击

- 服务器用于非对称公钥 A、私钥 AA，将公钥 A 明文发给浏览器
- 中间人劫持**公钥 A**，将自己的**公钥 X**替换数据包里面的 A 发给浏览器。当然，中间人有**私钥 XX**
- 浏览器随机生成一个对称加密密钥 B，用公钥 X 加密传回服务器
- 中间人劫持后用私钥 XX 解密得到**密钥 B**，再用公钥 A 加密后传给服务器。
- 服务器拿到后用私钥 AA 解密得到密钥 B

最后结果是：

- 服务器：公钥 A、私钥 AA、公钥 B
- 中间人：公钥 A、自己的公钥 X 私钥 XX、公钥 B
- 浏览器：中间人的公钥 X、自己生成的公钥 B

这样在服务器浏览器双方都不发现异常的情况下，中间人得到了密钥 B。

原因是：浏览器无法确定自己收到的公钥是不是网站自己的。需要给网站服务器颁发一个“身份证”，也就是 CA 证书。

### 数字证书

证书：证书持有者，证书持有者的公钥。

服务器把证书传输给浏览器，浏览器从证书里取公钥。

需要做的是，证书的传输过程中，如何防止被修改。使用数字签名。

把证书内容生成一份签名，比对证书内容和签名是否一致就能察觉是否被修改。

### Https 获取密钥过程

![https流程](/images/https.jpeg)

【注】：HTTPS 必须在每次请求中都要先在 SSL/TLS 层进行握手传输密钥吗？

可以通过一个会话标识符 session ID（在 TLS 握手中生成），服务器可以保存会话的相关信息，在服务器和服务器都保存了 session id 的情况下，就可以完成一次快速握手。

### SSL/STL 协议

SSL/TLS 协议的基本过程是这样的：

> （1） 客户端向服务器端索要并验证公钥。
> （2） 双方协商生成"对话密钥"。
> （3） 双方采用"对话密钥"进行加密通信。

上面过程的前两步，又称为"握手阶段"（handshake）。

"握手阶段"涉及四次通信，需要注意的是，"握手阶段"的所有通信都是明文的。

## 编码

[参考](http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)

### ASCII

来源：上个世纪 60 年代，美国制定了一套字符编码，对英语字符与二进制位之间的关系，做了统一规定。这被称为 ASCII 码，一直沿用至今。

**一个字节**，ASCII 码一共规定了 128 个字符的编码（包括 32 个不能打印出来的控制符号），只占用了后 7 位，第一位统一用 0。

### UNICODE

英语用 128 个符号编码就够了，但是用来表示其他语言，128 个符号是不够的。

如果有一种编码，将世界上所有的符号都纳入其中。每一个符号都给予一个独一无二的编码，那么乱码问题就会消失。这就是 Unicode，就像它的名字都表示的，这是一种所有符号的编码。

Unicode 只是一个**符号集**，它只规定了符号的二进制代码，却没有规定这个二进制代码应该如何存储。

于是 unicode 的实现方式 utf-8 出现了。

### UTF-8

**UTF-8 是 Unicode 的实现方式之一**，还包括 utf-16（两字节）和 utf-32（四字节），后两种基本不用

**可变长度编码**，使用**1-4 个字节**表示一个符号

- 对于单字节，第一位设为 0，后面 7 位是这个符号的 unicode 码

- 对于 n 字节，第一字节的前 n 位设为 1，第 n+1 位设为 0；后面自己的前两位设为 0；剩下其余二进制位全部为这个符号的 unicode 吗

注：utf-8 兼容 ascii 码

### 原码、补码、反码、移码

[参考](https://www.jianshu.com/p/abbdae4f3841)

对于一个十六进制的数：0x，八位存储

- 原码：正数是其二进制本身；**负数是符号位为 1**，数值部分取绝对值的二进制。

- 反码：正数的反码和原码相同；负数是符号位为 1，**其它位是原码取反**。

- 补码：正数的补码和原码，反码相同；**负数是反码未位加 1**

- 移码：**将符号位取反的补码（不区分正负）**

| 编码 | 10810（sbyte） | -10810（sbyte） |
| ---- | -------------- | --------------- |
| 原码 | **0**1101100   | **1**1101100    |
| 反码 | **0**1101100   | **1**0010011    |
| 补码 | **0**1101100   | **1**0010100    |
| 移码 | **1**1101100   | **0**0010100    |

来由：将符号位参与运算, 并且只保留加法的方法。

如果用原码表示, 让符号位也参与计算, 显然对于减法来说, 结果是不正确的.这也就是为何计算机内部不使用原码表示一个数.

`1 - 1 = 1 + (-1) = [00000001]原 + [10000001]原= [10000010]原 = -2`

为了解决原码做减法的问题, 出现了反码

发现用反码计算减法, 结果的真值部分是正确的. 而唯一的问题其实就出现在"0"这个特殊的数值上. 虽然人们理解上+0 和-0 是一样的, 但是 0 带符号是没有任何意义的. 而且会有[0000 0000]原和[1000 0000]原两个编码表示 0.

`1 - 1 = 1 + (-1) = [0000 0001]原 + [1000 0001]原= [0000 0001]反+ [1111 1110]反= [1111 1111]反= [1000 0000]原= -0`

于是补码的出现, 解决了 0 的符号以及两个编码的问题

`1-1 = 1 + (-1) = [0000 0001]原 + [1000 0001]原 = [0000 0001]补+ [1111 1111]补= [0000 0000]补=[0000 0000]原`

-1-127 的结果应该是-128, 在用补码运算的结果中, [1000 0000]补就是-128. 但是注意因为实际上是使用以前的-0 的补码来表示-128, 所以-128 并没有原码和反码表示.(对-128 的补码表示[1000 0000]补算出来的原码是[0000 0000]原, 这是不正确的)

使用补码, 不仅仅修复了 0 的符号以及存在两个编码的问题, 而且还能够多表示一个最低数. 这就是为什么 8 位二进制, 使用原码或反码表示的范围为[-127, +127], 而使用补码表示的范围为[-128, 127].

因为**机器使用补码**, 所以对于编程中常用到的 32 位 int 类型, 可以表示范围是: [-231, 231-1] 因为第一位表示的是符号位.而使用补码表示时又可以多保存一个最小值.

### javascript 数字表示

JavaScript 使用 Number 类型表示数字（整数和浮点数），遵循 [IEEE 754](https://zh.wikipedia.org/wiki/IEEE_754) 标准 通过**64 位**来表示一个数字。

- 第 0 位：符号位，0 表示正数，1 表示负数(s)
- 第 1 位到第 11 位：储存指数部分（e）
- 第 12 位到第 63 位：储存小数部分（即有效数字）f

JavaScript 能够准确表示的整数范围在`-2^53`到`2^53`之间（不含两个端点），超过这个范围，无法精确表示这个整数。

计算机无法直接对十进制的数字进行运算，这是硬件物理特性已经决定的。这样运算就分成了两个部分：**先按照 IEEE 754 转成相应的二进制，然后对阶运算**

0.1 和 0.2 转换成二进制后会无限循环

```js
0.1 -> 0.0001100110011001...(无限循环)
0.2 -> 0.0011001100110011...(无限循环)
```

但是由于 IEEE 754 尾数位数限制，需要将后面多余的位截掉，在进制之间的转换中精度已经损失

由于指数位数不相同，运算时需要对阶运算 这部分也可能产生精度损失

按照上面两步运算（包括两步的精度损失），最后的结果是

```js
0.01001100110011001100110011001100110011001100110011;
```

结果转换成十进制之后就是 0.30000000000000004，这样就有了前面的“秀”操作：0.1 + 0.2 != 0.3

精度损失可能出现在进制转化和对阶运算过程中

#### 怎么解决精度问题？

- 将数字转成整数

- BigInt

  `BigInt` 是一种内置对象，它提供了一种方法来表示大于 `253 - 1` 的整数。这原本是 Javascript 中可以用 [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number) 表示的最大数字。`BigInt` 可以表示任意大的整数。

  可以用在一个整数字面量后面加 `n` 的方式定义一个 `BigInt` ，如：`10n`，或者调用函数`BigInt()`。不需要加 new。

  使用 `typeof` 测试时， `BigInt` 对象返回 "bigint"

### 汉字编码

gb2312，标准字符集 ，6K+字

gbk 是 gb2312 的扩展规范，2W+字

现在统一用 utf-8
