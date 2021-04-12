---
title: 前端基础：我已经是个js程序员了
date: 2021-04-11 00:04:22
tags: 基础
---

## 浏览器与网络

### 地址栏输入url，到页面呈现，的过程

- 将url解析成ip地址，先查找缓存，没有则进行dns解析，向域名服务器处获取ip
- 建立tcp连接
- 发送http请求
- 收到服务器响应的资源文档
- 构建dom树和cssom树，布局，再渲染
- 画面呈现

### http与https

#### http

http：超文本传输协议，位于应用层，基于TCP/IP协议，使用无状态连接。

##### 状态码

1：消息，临时响应，代表请求已被接受，需要继续处理

2：成功，代表请求已成功被服务器接收、理解、并接受。

3：重定向，后续的请求地址（重定向目标）在本次响应的Location域中指明。

- [301 Moved Permanently](https://zh.wikipedia.org/wiki/HTTP_301)

  被请求的资源已**永久移动**到新位置，并且将来任何对此资源的引用都应该使用本响应返回的若干个URI之一。

- [302 Found](https://zh.wikipedia.org/wiki/HTTP_302)

  要求客户端执行**临时重定向**（原始描述短语为“Moved Temporarily”）

- [303 See Other](https://zh.wikipedia.org/wiki/HTTP_303)

  对应当前请求的响应可以在另一个URI上被找到

- **304 Not Modified**

  未修改。所请求的资源未修改，服务器返回此状态码时，不会返回任何资源。

  表示资源在由请求头中的If-Modified-Since或If-None-Match参数指定的这一版本之后，未曾被修改。在这种情况下，由于客户端仍然具有以前下载的副本，因此不需要重新传输资源。

  注：与协商缓存有关

- 305 Use Proxy

  被请求的资源必须通过指定的**代理**才能被访问。Location域中将给出指定的代理所在的URI信息，接收者需要重复发送一个单独的请求，通过这个代理才能访问相应资源。

  ##### 强缓存与协商缓存

  参考：https://juejin.cn/post/6844903838768431118

  ###### 缓存

  缓存是一种保存资源副本并在下次请求时直接使用该副本的技术。当 web 缓存发现请求的资源已经被存储，它会拦截请求，返回该资源的拷贝，而不会去源服务器重新下载。

  好处：缓解服务器端压力，提升性能(获取资源的耗时更短了)

  

  告诉浏览器在约定的这个时间前，可以直接从缓存中获取资源，而无需跑到服务器去获取。

  http缓存机制主要在http响应头中设定，响应头中相关字段为**Expires、Cache-Control、Last-Modified、Etag**。

  

  ###### 强缓存（过期时间）

  浏览器不会像服务器发送任何请求，直接从本地缓存中读取文件并返回

  **Cache-Control**（优先级更高）：当值设为max-age=300时，则代表在这个请求正确返回时间的5分钟内再次加载资源，就会命中强缓存。

  **Expires**：过期时间，如果设置了时间，则浏览器会在设置的时间内直接读取缓存，不再请求

  

  ###### 协商缓存（修改时间）

  当资源过期时，向服务器发送请求，服务器根据请求头参数来判断是否命中协商缓存，如果命中，则返回304状态码并告诉浏览器从缓存中读取资源，否则返回200状态码和新资源。

  - 协商缓存标识1，加上时间戳的hash值：**etag**/if-none-match（时间精度毫秒，优先级更高）

  当资源过期时，浏览器发现响应头里有Etag,则再次像服务器请求时带上请求头if-none-match(值是Etag的值)。服务器收到请求进行比对，决定返回新页面200或**304**

  https://user-gold-cdn.xitu.io/2019/5/6/16a8c60fb0ef49f0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1

  -  标识2，最新修改时间：**Last-modified**/if-modify-since（秒）

  当资源过期时（浏览器判断Cache-Control标识的max-age过期），发现响应头具有Last-Modified声明，则再次向服务器请求时带上头if-modified-since，表示请求时间。服务器收到请求后发现有if-modified-since则与被请求资源的最后修改时间进行对比（Last-Modified）,若最后修改时间较新（大），说明资源又被改过，则返回最新资源，HTTP 200 OK;若最后修改时间较旧（小），说明资源无新修改，响应HTTP 304 走缓存。

4：客户端错误

- 400 Bad Request

由于明显的客户端错误（例如，格式错误的请求语法，太大的大小，无效的请求消息或欺骗性路由请求），服务器不能或不会处理该请求。

- 401 Unauthorized

未认证，类似于403 Forbidden

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

  作为网关或者代理工作的服务器尝试执行请求时，未能及时从上游服务器（URI标识出的服务器，例如[HTTP](https://zh.wikipedia.org/wiki/HTTP)、[FTP](https://zh.wikipedia.org/wiki/FTP)、[LDAP](https://zh.wikipedia.org/wiki/LDAP)）或者辅助服务器（例如[DNS](https://zh.wikipedia.org/wiki/DNS)）收到响应。

#### https

在http的基础上加了ssl协议，是http的安全版本。

#### http2.0的改进

- 基于http3，安全性更有保证
- 使用二进制格式，比以前基于文本的传输更具普适性
- 多路复用，是以前长连接的增强

### Get、Post、Put、Delete、Options、Trace、Connect

#### 请求类型

- get：请求资源，可以使用缓存
- Post：提交数据，POST产生两个TCP数据包，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据），不能使用缓存
- Put：更新资源
- Delete：删除资源
- Options：返回某资源所支持的HTTP请求方法
- Trace：回显服务器收到的请求，主要用于测试或诊断
- Connect：HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器

#### Restful规范

用url来指代资源，用请求类型来指代动作。

### 跨网络异步获取资源

#### Ajax

使用 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 实现

#### Fetch

Fetch是XHR的替代，ES6出现的，使用了ES6中的promise对象，由js原生提供，没有使用XMLHttpRequest对象

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

#####  fetch发送2次请求的原因

用fetch的post请求时，第一次发送了一个Options请求，询问服务器是否支持该类型的请求，如果服务器支持，则在第二次中发送真正的请求。

### axios

axios 是一个基于Promise 用于浏览器和 nodejs 的 HTTP 客户端

#### Fetch与Ajax的不同

- 当接收到一个代表错误的 HTTP 状态码时，从 `fetch()` 返回的 Promise 不会被标记为 reject，而是标记为 resolve （返回值的 `ok` 属性设置为 false ），仅当网络故障时或请求被阻止时，才会标记为 reject。
- `fetch()` **不会接受跨域 cookies；**不能使用 `fetch()` 建立起跨域会话。其他网站的 `Set-Cookie` 头部字段将会被无视。
- `fetch` **不会发送 cookies**。

### ajax、axios、fetch之间优缺点重点对比

#### Ajax

- 会遇到XSS、CSRF攻击
- 本身是针对MVC的编程,不符合现在前端MVVM的浪潮。
- 基于原生的XHR开发，XHR本身的架构不清晰，已经有了fetch的替代方案。

#### fetch

- 符合关注分离，没有将输入、输出和用事件来跟踪的状态混杂在一个对象里
- 更好更方便的写法
- 更加底层，提供的API丰富（request, response）
- 脱离了XHR，是ES规范里新的实现方式
- fetchtch只对网络请求报错，对400，500都当做成功的请求，需要封装去处理
- fetch默认不会带cookie，需要添加配置项
- fetch不支持abort，不支持超时控制，使用setTimeout及Promise.reject的实现的超时控制并不能阻止请求过程继续在后台运行，造成了量的浪费
- fetch没有办法原生监测请求的进度，而XHR可以。

#### axios 

基于promise用于浏览器和node.js的http客户端。

- 支持 Promise API。
- 提供了一些并发请求的接口（重要，方便了很多的操作）。
- 在浏览器中创建 XMLHttpRequests。在 node.js 则创建 http 请求。（自动性强）
- 支持拦截请求和响应。
- 自动转换 JSON 数据。
- 客户端支持防御CSRF、 XSRF。

### Cookie

#### 字段

- name
- value
- size
- domain
- path
- expires/max-age：过期时间
- http：httponly，无法通过document.cookie来获取cookie
- secure：设置是否只能由https来传递此cookie

### Cookie与Session

作用：由于http的无状态性（这一次请求和上一次请求是没有任何关系的，互不认识的，没有关联的），为了使某个域名下的所有网页能够共享某些数据，session和cookie出现了。

Cookie是用户通行证，存储在客户端，在发送请求时附带。session 有如用户信息档案表, 里面包含了用户的认证信息和登录状态等信息，保存在服务器端。

#### Cookie和Session共同使用

- 客户端发一个http请求给服务器
- 服务器接受请求后，建立一个session，发回http响应给客户端，其中的set-cookie头部带有sessionid
- 客户端收到响应后，后续会自动在请求头中加cookie
- 服务器接收请求，分解cookie，验证成功后将请求返回给客户端。

用session只需在客户端保存一个id，大量数据都是保存在服务端，对服务器有压力。

用cookie不用session，那么账户信息全部保存在客户端，一旦被劫持，全部信息都会泄露。并且客户端数据量变大，网络传输的数据量也会变大。

### Token

是一种服务端无状态的认证方式。Token类似一个令牌，无状态，用户信息都被加密到token中，服务器收到token后解密就可知道是哪个用户。需要开发者手动添加。

token在客户端一般存放于localStorage，cookie，或sessionStorage中。在服务器一般存于数据库中

token 的认证流程与cookie很相似

- 用户登录，成功后服务器返回Token给客户端。
- 客户端收到数据后保存在客户端
- 客户端再次访问服务器，将token放入headers中
- 服务器端采用filter过滤器校验。校验成功则返回请求数据，校验失败则返回错误码

#### 用Token，不用Cookie+Session的优点

- 避免 CSRF 攻击

用户登陆银行网页，同时登陆了危险网页，攻击者在网页放一个表单，该表单提交src为`http://www.bank.com/api/transfer`，body为`count=1000&to=Tom`。倘若是session+cookie，表单发起的 POST 请求不受到浏览器同源策略限制，可以使用其它域的 Cookie 向其它域发送 POST 请求，形成 CSRF 攻击。在post请求的瞬间，cookie会被浏览器自动添加到请求头中。但token不同，token是开发者为了防范csrf而特别设计的令牌，浏览器不会自动添加到headers里，攻击者也无法访问用户的token，所以提交的表单无法通过服务器过滤，也就无法形成攻击。

- Token 是一种服务端无状态的认证方式，而cookie+session是有状态的。（ 所谓无状态就是服务端并不会保存身份认证相关的数据。）

#### JWT

头部 +负载+签名

```javascript
// javascript
var encodedString = base64UrlEncode(header) + '.' + base64UrlEncode(payload);

var signature = HMACSHA256(encodedString, 'secret'); // TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ,加盐secret组合加密
// 将这三部分用.连接成一个完整的字符串,构成了最终的jwt:
```



在客户端应用：一般是在请求头里加入`Authorization`，并加上`Bearer`标注

流程和token的一样

缺点：一旦 JWT 签发了，在到期之前就会始终有效，除非服务器部署额外的逻辑。JWT 本身包含了认证信息，一旦泄露，任何人都可以获得该令牌的所有权限。

### 浏览器存储方式：Cookie、SessionStorage、LocalStorage

共同点：都是存储在浏览器端

不同点：

- cookie可以在浏览器和服务器中来回传递的，而余下两者是只在本地保存的。

- 可存储数据的大小不一样，cookie数据不能超过4k，剩下的就比较大了，大概是在5M左右。

- 数据有效期不一样，sessionStorage当关闭浏览器的时候就失效了，localStorage一直有效，需要手动清

  除，而cookie只在有效期之内一直有效。

- 作用域不一样，localStorage、cookie在所有同源窗口中都是共享的，sessionStorage是某个窗口私有

  的。

  

  #### 跨页面通信

  同源页面（不在同一个tab）：可以使用LocalStorage

  非同源页面：iframe、postmessage

  H5之后为window新增了window.postMessage()方法，第一个参数是要发送的数据，第二个参数是域名。



### Doctype

声明于文档最前面，告诉服务器以什么方式（例如h5）来渲染页面。

运行模式分：严格模式（浏览器支持的最高版本）、混杂模式（向后兼容）



### XSS

跨站脚本攻击，指攻击者在网页中注入恶意代码，在用户浏览网页的时候进行攻击。

类型：

- 反射型：将攻击代码放在url地址的请求参数中
- 存储型：攻击者输入一些数据并且存储到了数据库中，其他浏览者看到的时候进行攻击。
- DOM型：攻击者通过各种手段将恶意脚本注入用户的页面中



处理：

- set-cookie：httponly，禁止javascript脚本来访问cookie，secure - 这个属性告诉浏览器仅在请求为https的时候发送cookie。
- 输入检查：对于用户的任何输入要进行检查、过滤和转义
- 输出检查

### CSRF

Cross Site Request Forgery，跨站请求伪造

在讲token时讲了

防御CSRF 攻击主要有三种策略：

- 验证 HTTP Referer 字段；
- 在请求地址中添加 token 并验证；
- 在 HTTP 头中自定义属性并验证。



### addEventListener

addEventListener(event, function, useCapture)

其中，event指定事件名；function指定要事件触发时执行的函数；useCapture指定事件是否在捕获或冒泡阶段执行。

### 跨域

- postMessage跨域：可以跨域操作的window属性之一。
- CORS：服务端设置Access-Control-Allow-Origin即可，前端无须设置，若要带cookie请求，前后端都需要设置。
  - Access-Control-Allow-Origin *;
  - Access-Control-Allow-Methods "POST, GET, OPTIONS";
  - Access-Control-Allow-Headers "Origin, Authorization, Accept";
  - Access-Control-Allow-Credentials true;
- 代理跨域：启一个代理服务器，实现数据的转发
- JSONP：script标签src属性中的链接却可以访问跨域的js脚本，利用这个特性，服务端不再返回JSON格式的数据，而是返回一段调用某个函数的js代码，在src中进行了调用，这样实现了跨域。

### 查看网站性能

检测页面加载时间

- 被动检测：在页面设置检测脚本，当用户访问网页时，记录数据，传回数据库分析

- 主动监测：搭建分布式环境，模拟用户访问页面，主动采集数据并分析

### 前端优化

- 降低请求量：合并资源，减少http请求数
- 加快请求速度：预解析DNS、CDN分发
- 使用缓存
- 加快渲染：加载顺序（js放最后），SSR，使用GPU

### 屏幕卡顿

原因：

- 内存溢出
- 资源过大
- 资源加载
- canvas绘制帧率

办法：

- 在物体离开屏幕可视区域即回收销毁
- 选用体积较小的资源，例如图片，可以在近处放清晰度高的，远处放清晰度低的
- 预加载
- 大部分显示器刷新频率为60次/s,因此游戏的每一帧绘制间隔时间需要小于1000/60=16.7ms，才能让用户觉得不卡顿



### js加载过程阻塞，解决方法

- 制定script标签的async属性，异步执行
- 设置defer属性，脚本将在页面完成解析时执行



### BOM

Brower object model，浏览器对象模型，它使 JavaScript 有能力与浏览器进行“对话”。window对象 所有浏览器都支持 window 对象。它表示浏览器窗口。



### 顶层对象

- 浏览器：window/self
- web worker：self
- node：global
- ES5：顶层对象的属性与全局变量等价
- ES6：globalThis

### This

同一段代码为了能够在各种环境，都能取到顶层对象，现在一般是使用`this`变量，但是有局限性。

-  全局环境中，this会返回等层对象。但是在node的模块中，返回的是当前模块，ES6中返回的是undifined。

- 函数里的this，作为对象方法运行返回的是对象，或者是调用方，单纯作为函数运行会指向顶层对象。
- ES2020引入globalThis对象，在任何环境下都指向全局环境下的this





## CSS与html

### CSS选择器

id选择器>class选择器>标签选择器，伪元素选择器、伪类选择器

带有!important 标记的样式属性的优先级最高；

样式表的来源不同时，优先级顺序为：内联样式> 内部样式 > 外部样式 > 浏览器用户自定义样式 > 浏览器默认样式

### 盒子模型

#### content-padding-border-margin

- 标准（W3C）盒子模型：width=content
- IE盒子模型：width = content+padding+border

#### 使用box-sizing 设置

box-sizing规定两个并排的带边框的框，语法为box-sizing：content-box/border-box/inherit

content-box：宽度和高度分别应用到元素的内容框，在宽度和高度之外绘制元素的内边距和边框

border-box：为元素设定的宽度和高度决定了元素的边框盒，

inherit：继承父元素的box-sizing

### CSS3新特性

- box-sizing属性，box-sizing:content-box;表示标准的盒子模型，box-sizing:border-box表示的是IE盒子模型
- 边框：border-radius，box-shadow等；

- 背景如background-size，background-origin等；

- 2D，3D转换如transform等；

- 动画如animation等。

### 画一个三角形

```html
<div id="demo"></div>
#demo{
width:0px;  
height:0px;  
border:40px solid transparent;
border-bottom:80px solid red;
}
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
```

### js设置轮播

图片轮播的原理就是图片排成一行，然后准备一个只有一张图片大小的容器，对这个容器设置超出部分隐藏，在控制定时器来让这些图片整体左移或右移，这样呈现出来的效果就是图片在轮播了。

如果有两个轮播，可封装一个轮播组件，供两处调用

### link和@import的区别

- link是html标签，没有兼容性问题，@import是css提供的，IE5以上才能识别
- link样式的权重高于@import
- link和页面同时加载，@import在页面加载结束后才加载

### transition和animation的区别

Animation和transition大部分属性是相同的，他们都是随时间改变元素的属性值，他们的主要区别是transition需要触发一个事件才能改变属性，而animation不需要触发任何事件的情况下才会随时间改变属性值，并且transition为2帧，从from .... to，而animation可以一帧一帧的。

### Flex布局

Flex是弹性布局，用来为盒状模型提供最大的灵活性，传统布局方式依赖position、float和display属性，对特殊布局不方便。

- 容器属性
  - flex-direction：决定主轴的方向（即子item的排列方法）
  - flex-wrap：决定换行规则
  - justify-content：对其方式，水平主轴对齐方式
  - align-items：对齐方式，竖直轴线方向
- 元素属性
  - align-self：允许单个项目与其他项目不一样的对齐方式，可以覆盖align-items，默认属性为auto，表示继承父元素的align-items

### 双边距重叠问题（外边距折叠）

多个相邻（兄弟或者父子关系）普通流的块元素垂直方向marigin会重叠

折叠的结果为：

两个相邻的外边距都是正数时，折叠结果是它们两者之间较大的值。
两个相邻的外边距都是负数时，折叠结果是两者绝对值的较大值。
两个外边距一正一负时，折叠结果是两者的相加的和。

### BFC

作用：用于清除浮动，防止margin重叠等

块级格式化上下文，是一个独立的渲染区域，并且有一定的布局规则。BFC是页面上的一个独立容器，子元素不会影响到外面

BFC区域不会与float box重叠，计算BFC的高度时，浮动元素也会参与计算

那些元素会生成BFC：

根元素

float不为none的元素

position为fixed和absolute的元素

display为inline-block、table-cell、table-caption，flex，inline-flex的元素

overflow不为visible的元素

### 元素消失

visibility=hidden, opacity=0，display:none

- opacity=0，元素隐藏，不改变页面布局，可触发绑定事件

- visibility=hidden，元素隐藏，不改变页面布局，不会触发绑定事件
- display=none，元素隐藏，改变页面布局，可以理解成在页面中把该元素删除掉一样。



### position属性

- fixed：相对浏览器窗口固定，即使窗口滚动它也不懂，不在文档流中，fixed元素可与其余元素重叠
- relative：相对于自身原本位置偏移，元素在文档流中仍占据原来的空间
- absolute：相对于最近的父元素定位，不在文档流中
- static：默认值，没有定位，出现在正常的文档流中，忽略任何top,buttom,left,right声明
- sticky：元素先按照普通文档流定位，然后相对于该元素在流中的flow root（BFC）和 containing block（最近的块级祖先元素）定位。而后，元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。

### 浮动清除

在非IE浏览器（如Firefox）下，当容器的高度为auto，且容器的内容中有浮动（float为left或right）的元素，在这种情况下，容器的高度不能自动伸长以适应内容的高度，使得内容溢出到容器外面而影响（甚至破坏）布局的现象。这个现象叫浮动溢出，为了防止这个现象的出现而进行的CSS处理，就叫CSS清除浮动。

- 设置css的overflow为auto
- 使用空元素，器css 的clear属性为both

###  block、inline、inline-block的区别

- block块元素独占一行，宽度自动填充为父元素宽度。可以设置width,height属性。即使设置了宽度,仍然是独占一行。
- inline元素，多个该元素排列在一行，直到炒出，才会换新一行，其宽度岁元素内容而变化。margin和padding属性，水平方向有效，竖直方向无效。设置width,height属性无效
- inline-block：既具有block的宽度高度特性又具有inline的同行特性。

### CSS预处理器

预先编译处理`CSS`。扩展 `CSS` ，增加了变量、Mixin、函数等特性，供开发者编写源代码，随后经过专门的编译工具将源码转化为`CSS`语法。

```
•    嵌套；
•    变量；
•    mixin/继承；
•    运算；
•    模块化；
```

- 2007， sass-scss（两套语法规则：一个依旧是用缩进作为分隔符来区分代码块的；另一套规则和CSS一样采用了大括号（｛｝）作为分隔符。后一种语法规则又名`SCSS`，在Sass3之后的版本都支持这种语法规则。）
- 2009，less（使用`CSS`的语法）
- 2010，Stylus（同时支持缩进和CSS常规样式书写规则）

### HTML5 

#### 新增的元素

语义化：header, footer,  nav, aside, article, section

存储：sessionStorage, localStorage

多媒体：audio, video

绘制：svg, canvas, webgl

通信：websocket

多线程：web worker

#### 删除的元素

```html
<big>，<center>，<font>，<tt>，<strike> <acronym>，<applet>
```

#### 块元素和行内元素

块元素：独占一行，自动填满父元素，，可以设置margin和pading以及高度和宽度

行元素：不会独占一行，width和height会失效，并且在垂直方向的padding和margin会失效。

## 操作系统

### 进程和线程

#### 进程

系统调用资源的最小单位

#### 线程

CPU调度的最小单位

##### **进程切换开销**

1. 切换虚拟地址空间
2. 切换CPU上下文
3. 切换内核栈

##### **线程切换开销**

1. 切换CPU上下文
2. 切换内核栈





## js

### 数据类型

- 6种原始类型，可一使用typeof判断
  - undefined
  - Number
  - Boolean
  - String
  - Symbol
  - BigInt：BigInt是通过在整数末尾附加 `n `或调用构造函数来创建的。
- null，和其上六种属于属于原始值
- Object
  - Array
  - Map
  - WeakMap
  - Set
  - WeakSet
  - Data



### 数组方法

- push、unshift，原地修改
- pop、shift，原地修改
- splice，原地修改
- sort，原地修改
- slice

### ES6新特性

- 在变量声明方面有：let, const
- 面向对象编程有 语法糖 class
- 在模块导入方面是 import export
- 新的数据类型：map、set
- 新的数组方法：map、reduce
- 异步：promise
- 箭头函数
- 解构赋值和三点表达式
- 等等……

### this

函数里的this是函数的调用者；

严格模式下，在全局顶层是undefined。

### 箭头函数

作用：更简短的函数并且不绑定`this`。

不能用作构造函数，无自己的原型属性；

无arguments，但是可以用三点解析式，用…rest参数获取

### Symbol

每个从`Symbol()`返回的symbol值都是唯一的。一个symbol值能作为对象属性的标识符；这是该数据类型仅有的目的。

不支持语法："`new Symbol()`"。

[`Object.getOwnPropertySymbols()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols) 方法让你在查找一个给定对象的符号属性时返回一个symbol类型的数组。

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



WeakSet 结构与 Set 类似，也是不重复的值的集合。WeakSet 的成员只能是对象，而不能是其他类型的值。其次，WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用。

### Map

只有对同一个对象的引用，Map 结构才将其视为同一个键。但是对于数值和字符串没有影响
 Map 的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键。这就解决了同名属性碰撞（clash）的问题
  // 如果 Map 的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值严格相等，Map 将其视为一个键，比如0和-0就是一个键，布尔值true和字符串true则是两个不同的键。

另外，undefined和null也是两个不同的键。虽然NaN不严格相等于自身，但 Map 将其视为同一个键。

```
Map 结构的实例有以下属性和操作方法。

（1）size 属性

size属性返回 Map 结构的成员总数。

const map = new Map();
map.set('foo', true);
map.set('bar', false);

map.size // 2
（2）Map.prototype.set(key, value)

set方法设置键名key对应的键值为value，然后返回整个 Map 结构。如果key已经有值，则键值会被更新，否则就新生成该键。

const m = new Map();

m.set('edition', 6)        // 键是字符串
m.set(262, 'standard')     // 键是数值
m.set(undefined, 'nah')    // 键是 undefined
set方法返回的是当前的Map对象，因此可以采用链式写法。

let map = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c');
（3）Map.prototype.get(key)

get方法读取key对应的键值，如果找不到key，返回undefined。

const m = new Map();

const hello = function() {console.log('hello');};
m.set(hello, 'Hello ES6!') // 键是函数

m.get(hello)  // Hello ES6!
（4）Map.prototype.has(key)

has方法返回一个布尔值，表示某个键是否在当前 Map 对象之中。

const m = new Map();

m.set('edition', 6);
m.set(262, 'standard');
m.set(undefined, 'nah');

m.has('edition')     // true
m.has('years')       // false
m.has(262)           // true
m.has(undefined)     // true
（5）Map.prototype.delete(key)

delete方法删除某个键，返回true。如果删除失败，返回false。

const m = new Map();
m.set(undefined, 'nah');
m.has(undefined)     // true

m.delete(undefined)
m.has(undefined)       // false
（6）Map.prototype.clear()

clear方法清除所有成员，没有返回值。

let map = new Map();
map.set('foo', true);
map.set('bar', false);

map.size // 2
map.clear()
map.size // 0

```



Map 结构原生提供三个遍历器生成函数和一个遍历方法。

- `Map.prototype.keys()`：返回键名的遍历器。
- `Map.prototype.values()`：返回键值的遍历器。
- `Map.prototype.entries()`：返回所有成员的遍历器。
- `Map.prototype.forEach()`：遍历 Map 的所有成员。



### Proxy

Proxy 在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，在这层拦截中可以对外界的访问进行过滤和改写。

```javascript
// new Proxy()表示生成一个Proxy实例，target参数表示所要拦截的目标对象，handler参数是一个配置对象，用来定制拦截行为，对于每一个被代理的操作，需要提供一个对应的处理函数，该函数将拦截对应的操作
// var proxy = new Proxy(target, handler);

var proxy = new Proxy({}, {
  get: function(target, propKey) { // get方法的两个参数分别是目标对象和所要访问的属性。
    return 35; // 由于拦截函数总是返回35，所以访问任何属性都得到35。
  }
});

proxy.time // 35
proxy.name // 35
proxy.title // 35

// 如果 handler === {} 
```

可继承，即Proxy 实例可以作为其他对象的原型对象

```javascript
var proxy = new Proxy({}, {
  get: function(target, propKey) {
    return 35;
  }
});

let obj = Object.create(proxy);
obj.time // 35
```



### 作用域

#### script、function、{}

- 在 script声明的var、let、const为全局变量

- 在function声明的var、let、const为局部变量，只在本函数和下层函数中使用（可以使用闭包在外部使用输出）

- 在{}中声明的var仍为全局变量，但let、const为局部变量，其之外不可以使用

注意：for （var i=0; i<n ; i++）和 for( let i=0; i< n; i++)的区别，这里var定义了一个在for内的全局变量，let定义了n个for内的局部变量。

如何使var在for中的异步函数使用不同的值，使用闭包，即在for内新声明函数（可以不带相关参数），并使用

#### 全局作用域

一个（var、let、const）变量在函数外面或者大括号 {} 外申明，那么就是定义了一个全局作用域的变量。一旦你申明了全局变量，那么你可以在任何地方使用它，甚至在函数中也行。

#### 局部作用域

在 JavaScript 中，有两种局部作用于：**函数作用域和块作用域**。

##### 函数作用域

当你在函数中申明一个（var、let、const）变量，你就只能够在这个函数范围内使用它。在范围之外你不能使用。

##### 块作用域

当你在一个大括号中 {} 使用 *const* 或者 *let* 申明变量，那么这个变量只能够在这个大括号范围内使用。

##### 嵌套/词法 作用域

当一个函数在另一个函数内定义，**内部的函数能够访问外部函数的变量**（闭包作用的原理）。我们称之为**词法作用域**。然而，外部的函数不能够访问内部函数的变量。



### 闭包

[参考](https://zhuanlan.zhihu.com/p/28921273)

[参考2](https://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)

#### 定义

在函数内声明内部函数，并将之返回（或作为返回对象的属性）。

闭包就是能够读取其他函数内部变量的函数，或者子函数在外调用，子函数所在的父函数的作用域不会被释放。

#### 原理

内层函数可访问外层函数的变量，将闭包返回就将上层函数的变量暴露

#### 作用

##### 解决副作用

在你想要的时候通过创建一个函数来激活内部的闭包。

```javascript
function prepareCake (flavor) {
  return function () {
    setTimeout(_ => console.log(`Made a ${flavor} cake!`), 1000)
  }
}

const makeCakeLater = prepareCake('banana')

// And later in your code...
makeCakeLater()
// Made a banana cake!
```



##### 使用闭包中的私有变量

```javascript
function secret (secretCode) {
  return {
    saySecretCode () {
      console.log(secretCode)
    }
  }
}

const theSecret = secret('CSS Tricks is amazing')
theSecret.saySecretCode()
// 'CSS Tricks is amazing'
```

##### 解决var的全局变量在异步函数调用中的变化

```javascript
for (let i = 0; i < 5; i++) {
  setTimeout(() => {
    console.log(i)
  }, 1000 * i)
}
//输出：0 1 2 3 4
for (var i=0; i<5; i++){
  setTimeout(()=>{
    console.log(i)
  }, 1000*i)
}
// 输出： 5 5 5 5 5，5是因为最后i++跳出来的是5
// 使用闭包实现类似let的结果
for (var i = 0; i < 5; i++) {
  function f(i: number) {
    setTimeout(() => {
      console.log(i)
    }, 1000 * i)
  }
  f(i)
}
//输出：0 1 2 3 4
```



### [原型](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Object_prototypes)

#### 定义

[参考](https://www.zhihu.com/question/34183746)

1.对象有属性__ proto  __ ， 指向该对象的构造函数的原型对象。
2.方法除了有属性__ proto __,还有属性prototype，prototype指向该方法的原型对象。

在JS里，万物皆对象。方法（Function）是对象，方法的原型(Function.prototype)是对象。因此，它们都会具有对象共有的特点。

即：**对象具有属性__ proto__，可称为隐式原型**，一个对象的隐式原型指向构造该对象的**构造函数的原型**，这也保证了实例能够访问在构造函数原型中定义的属性和方法。

方法这个特殊的对象，除了和其他对象一样有上述_proto_属性之外，还有自己特有的属性——原型属性（prototype），这个属性是一个指针，指向一个对象，这个对象的用途就是包含所有实例共享的属性和方法（我们把这个对象叫做原型对象）。原型对象也有一个属性，叫做constructor，这个属性包含了一个指针，指回原构造函数。



JavaScript 常被描述为一种**基于原型的语言 (prototype-based language)**——每个对象拥有一个**原型对象**，对象以其原型为模板、从原型继承方法和属性。

原型对象也可能拥有原型，并从中继承方法和属性，一层一层、以此类推。这种关系常被称为**原型链 (prototype chain)**，它解释了为何一个对象会拥有定义在其他对象中的属性和方法。原型对象是一个内部对象，应当使用` __proto__` 访问，

准确地说，这些属性和方法定义在<u>Object的构造器函数(constructor functions)之上的`prototype`属性上</u>，而非对象实例本身。

> 在传统的 OOP 中，首先定义“类”，此后创建对象实例时，类中定义的所有属性和方法都被复制到实例中。
>
> 在 JavaScript 中并不如此复制——而是在对象实例和它的构造器之间建立一个**链接**（它是__ proto__属性，是从构造函数的`prototype`属性派生的），之后通过上溯原型链，在构造器中找到这些属性和方法。

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

原型对象是一个内部对象，应当使用` __proto__` 访问， `doSomeInstancing` 的 `__proto__` 属性就是`doSomething.prototype`. 

当你访问 `doSomeInstancing` 的一个属性, 浏览器首先查找 `doSomeInstancing` 是否有这个属性. 如果 `doSomeInstancing` 没有这个属性, 然后浏览器就会在 `doSomeInstancing` 的 `__proto__` 中查找这个属性(也就是 doSomething.prototype). 如果 doSomeInstancing 的 `__proto__` 有这个属性, 那么 doSomeInstancing 的 `__proto__` 上的这个属性就会被使用. 

否则, 如果 doSomeInstancing 的 `__proto__` 没有这个属性, 浏览器就会去查找 doSomeInstancing 的 `__proto__` 的 `__proto__` ，看它是否有这个属性.

 默认情况下, 所有函数的原型属性的 `__proto__` 就是 `window.Object.prototype`. 所以 doSomeInstancing 的 `__proto__` 的 `__proto__` (也就是 doSomething.prototype 的 `__proto__` (也就是 `Object.prototype`)) 会被查找是否有这个属性. 如果没有在它里面找到这个属性, 然后就会在 doSomeInstancing 的 `__proto__` 的 `__proto__` 的 `__proto__` 里面查找. 然而这有一个问题: doSomeInstancing 的 `__proto__` 的 `__proto__` 的 `__proto__` 不存在. 

最后, 原型链上面的所有的 `__proto__` 都被找完了, 浏览器所有已经声明了的 `__proto__` 上都不存在这个属性，然后就得出结论，这个属性是 `undefined`.

// 层层向上直到一个对象的原型对象为 null 。

### [继承](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Inheritance)

JavaScript继承的对象函数并不是通过复制而来，而是通过原型链继承（通常被称为 **原型式继承 ——** **prototypal inheritance）**

通过`function.call`来调用父类的构造函数，但是这样无法自动指定`Teacher.prototype`的值，这样`Teacher.prototype`就只能包含在构造函数里构造的属性，而没有方法。因此我们利用`Object.create()`方法将`Person.prototype`作为`Teacher.prototype`的原型对象，并改变其构造器指向，使之与`Teacher`关联。

现在`子类`的`prototype`的`constructor`属性指向的是`父类`，需将之改回子类

```javascript
function Parent(x1) {
  this.x1 = x1;
  this.f1 = function () {
    console.log('parent!')
  }
}
// 新的构造器
function Child(y1, x1) {
  Parent.call(this, x1)
  this.y1 = y1
  this.f1 = function () {
    console.log('Child!')
  }
}
// 默认有一个空的原型属性，让Child()从Parent()的原型对象里继承方法
Child.prototype = Object.create(Parent.prototype)
// 现在Child()的prototype的constructor属性指向的是Parent()， 改回 Child
Child.prototype.constructor = Child;


let cObj = new Child(2, 1)
```



#### es6

```javascript
class c2 extends c1 {

}

class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}

typeof Point // "function"
Point === Point.prototype.constructor // true，类的数据类型就是函数，类本身就指向构造函数。使用的时候，也是直接对类使用new命令，跟构造函数的用法完全一致。


class ColorPoint extends Point {
  constructor(x, y, color) {
    this.color = color; // ReferenceError
    super(x, y);
    this.color = color; // 正确
  }
}
```

### 事件循环

任务队列中，在每一次事件循环中，macrotask只会提取一个执行，而microtask会一直提取，直到microsoft队列为空为止。

也就是说如果某个microtask任务被推入到执行中，那么当主线程任务执行完成后，会循环调用该队列任务中的下一个任务来执行，直到该任务队列到最后一个任务为止。

而事件循环每次只会入栈一个macrotask，主线程执行完成该任务后又会检查microtasks队列并完成里面的所有任务后再执行macrotask的任务。

- macrotasks: setTimeout, setInterval, setImmediate, I/O, UI rendering
- microtasks: process.nextTick, Promise, MutationObserver

### 事件委托

不在事件的发生地（直接dom）上设置监听函数，而是在其父元素上设置监听函数，通过**事件冒泡**，父元素可以监听到子元素上事件的触发，通过判断事件发生元素DOM的类型，来做出不同的响应。

### 事件监听

Element.addEventListener(event, func, useCapture)

第一个参数是事件的类型(如 "click" 或 "mousedown").

第二个参数是事件触发后调用的函数。

第三个参数是个布尔值用于描述事件是冒泡还是捕获。该参数是可选的。

事件传递有两种方式，冒泡和捕获

事件传递定义了元素事件触发的顺序，如果你将P元素插入到div元素中，用户点击P元素，

在冒泡中，内部元素先被触发，然后再触发外部元素，

捕获中，外部元素先被触发，在触发内部元素.

### 图片的懒加载和预加载

预加载：提前加载图片，当用户需要查看时可直接从本地缓存中渲染。

懒加载：懒加载的主要目的是作为服务器前端的优化，减少请求数或延迟请求数。

### mouseover和mouseenter的区别

mouseover：当鼠标移入元素或其子元素都会触发事件，所以有一个重复触发，冒泡的过程。对应的移除事件是mouseout

mouseenter：当鼠标移除元素本身（不包含元素的子元素）会触发事件，也就是不会冒泡，对应的移除事件是mouseleave

### new做的事

- new 操作符新建了一个空对象，这个对象的__ proto__指向构造函数的prototype

- 执行构造函数后返回这个对象。

### 拖拽功能

三个事件，分别是mousedown，mousemove，mouseup

两个坐标，clientX，clientY标识的是鼠标的坐标，分别标识横坐标和纵坐标，并且我们用offsetX和offsetY来表示元素的元素的初始坐标

拖拽的同时是绝对定位，我们改变的是绝对定位条件下的left以及top等等值。

### 防抖

规定在一定时间内事件触发的次数，把多个顺序调用合并为一次，直到最后一次调用，过了延迟的事件才调用。

```javascript
function debunce(fn, wait) {
	var timeout;
	return function() {
    var context = this, args = arguments;
		clearTimeout(timeout);
		timeout = setTimeout(function(){
      fn.apply(context, args);
    }, wait);
	}
}
function realFn(){
    console.log("Success");
}
window.addEventListener('scroll', debounce(realFn, 500));
```

### 节流

只允许一个函数在 X 毫秒内执行一次。与防抖相比，节流函数最主要的不同在于它保证在 X 毫秒内至少执行一次我们希望触发的事件 handler。

```javascript
// 使用定时器实现
function throttle(func, wait) {
    var timeout;
    return function() {
        var context = this;
        var args = arguments;
        if (!timeout) {
            timeout = setTimeout(function(){
                timeout = null;
                func.apply(context, args)
            }, wait)
        }

    }
}
```

### setTimeout(fn,100);100毫秒是如何权衡的

setTimeout()函数只是将事件插入了任务列表，必须等到当前代码执行完，主线程才会去执行它指定的回调函数，有可能要等很久，所以没有办法保证回调函数一定会在setTimeout指定的时间内执行，100毫秒是插入队列的时间+等待的时间

### 浅拷贝和深拷贝

#### 浅拷贝

如果是数组，可以利用slice，concat方法返回一个新数组来实现拷贝，假如数组嵌套了对象或者数组的话，使用concat方法克隆并不完整，如果数组元素是基本类型，就会拷贝一份，互不影响，而如果是对象或数组，就会只拷贝对象和数组的引用，这样我们无论在新旧数组进行了修改，两者都会发生变化，我们把这种复制引用的拷贝方法称为浅拷贝，

深拷贝就是指完全的拷贝一个对象，即使嵌套了对象，两者也互相分离，修改一个对象的属性，不会影响另一个

### 深度复制

```javascript
function deepCopy(obj) {
  // 判断是数组还是字典类对象
  let newObj = obj instanceof Array ? []:{};
  for (let i in obj){
    // 根据是否是对象来决定是否递归
    let s = typeof obj[i] == 'object' ? deepCopy(obj[i]) : obj[i];
    newObj[i] = s;
  }
  return newObj;
}

// 2
var s = JSON.parse(JSON.stringify(obj));
//原理是JOSN对象中的stringify可以把一个js对象序列化为一个JSON字符串，parse可以把JSON字符串反序列化为一个js对象，通过这两个方法，也可以实现对象的深复制。
```

### 实现bind

```javascript
Function.prototype.myBind = function(context) {
  var fn = this; // 原环境，谁调用的
  return function(...args){	// 提供后续调用
    return fn.call(context, args);
  }
}
var fun = function (x) {
  return x ** 2;
}
var s = fun.myBind([1, 2, 3])(3);
console.log(s);
```

### Promise

三种状态：pending、fulfilled、rejected

代码实现 promise

```javascript
// 定义三种状态
const PENDING = 'PENDING';      // 进行中
const FULFILLED = 'FULFILLED';  // 已成功
const REJECTED = 'REJECTED';    // 已失败

class Promise {
  constructor(exector) {
    // 初始化状态
    this.status = PENDING;
    // 将成功、失败结果放在this上，便于then、catch访问
    this.value = undefined;
    this.reason = undefined;

    const resolve = value => {
      // 只有进行中状态才能更改状态
      if (this.status === PENDING) {
        this.status = FULFILLED;
        this.value = value;
      }
    }
    const reject = reason => {
      // 只有进行中状态才能更改状态
      if (this.status === PENDING) {
        this.status = REJECTED;
        this.reason = reason;
      }
    }
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
  })
}

}
```



### ajax用promise封装

- 创建xmlhttprequest对象。
- 使用open方法设置和服务器的交换信息。
- 发送数据进行交互
- 注册onreadystatechange事件
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



### Proxy和Object.defineProperty监听对象属性改变

- Object.defineProperty：vue2和es5使用

  vue2结合订阅发布者模式实现双向绑定

```javascript
Object.defineProperty(user,'name',{
		set：function(key,value){}
})
```

- Proxy：vue3和es6使用

```javascript
var  user = new Proxy({}，{
	set：function(target,key,value,receiver){}
})
```

### setInterval

固定时间间隔执行一次

#### 使用setTimeout实现setInterval

```javascript
function myFun(){
  // do something
  setTimeout(myFun, 200);
}
```

### 实现sleep函数

```javascript
function mySleep(ms) {
  return new Promise((resolve)=>{
    setTimeout(resolve, ms);
  })
}
mySleep(500).then(()=>{
  // do something
})
```

### 转换-_命名到驼峰命名

```javascript
function cssStyle2DomStyle(sName) {
    let newStr = sName.replace(/([\d\D])-([\d\D])/g, (match, $1, $2) => $1 + $2.toUpperCase());
    return newStr[0] === '-' ? newStr.slice(1) : newStr;
}
```

### Flat

```javascript
function flat(arr){
  return arr.reduce((a, b)=> a.concat(b instanceof Array? flat(b):b), []);
}
```

### node服务器

```javascript
const http = require('http');

const hostname = '127.0.0.1';
const port = 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```



### 垃圾回收

- 引用计数

  - 当声明了一个变量并将某个对象赋予它时，这个对象的引用数加一，反之减一。 

  - 对引用数为0的变量清除。 

    // 可以即刻回收垃圾，但是计数过程过于复杂，并且循环引用无法回收。

- 标记清除

  - 标记阶段，垃圾回收器由根对象开始遍历，所有根对象能访问到的对象会被标记为可到达对象。

  - 清除阶段，对内存从头到尾线性遍历，没有标记的对象一律回收。

    // 此方法实现简单，但是会造成大量的内存碎片。

### C++、Java、JavaScript这三种语言的区别

- 静态类型/动态类型

  编译还是运行的时候知道变量的类型

- 编译型/解释型

  - c++，编译型
  - js，解释型
  - java，编译成字节码再用解释器

## git常用命令

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

## 框架相关

### Virtual dom

用js对象的结构模拟表示dom树的结构，用这棵虚拟树构造一颗真的dom树，插入到文档中。当状态改变时，重新构造虚拟树，新树旧树比较，把差异更新到真正的dom树上，来更新视图。

### webpack

#### 定义

本质上，**webpack** 是一个用于现代 JavaScript 应用程序的*静态模块打包工具*。当 webpack 处理应用程序时，它会在内部构建一个 [依赖图(dependency graph)](https://webpack.docschina.org/concepts/dependency-graph/)，此依赖图对应映射到项目所需的每个模块，并生成一个或多个 *bundle*。

#### 核心概念

- 入口，entry

  指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

- 输出(output) 

  在哪里输出它所创建的 *bundle*，以及如何命名这些文件。

  主要输出文件的默认值是 `./dist/main.js`，其他生成文件默认放置在 `./dist` 文件夹中。

- loader 

  webpack 只能理解 JavaScript 和 JSON 文件，**loader** 让 webpack 能够去处理其他类型的文件，并将它们转换为有效 [模块](https://webpack.docschina.org/concepts/modules)，以供应用程序使用，以及被添加到依赖图中。

  **loader** 有两个属性：

  1. `test` 属性，识别出哪些文件会被转换。
  2. `use` 属性，定义出在进行转换时，应该使用哪个 loader。

  注：webpack打包的文件默认是不支持ES6的，我们需要用babel转译。在loader中使用babel。

- 插件(plugin) 

  loader 用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。

  想要使用一个插件，你只需要 `require()` 它，然后把它添加到 `plugins` 数组中。多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 `new` 操作符来创建一个插件实例。

- 模式(mode) 

  通过选择 `development`, `production` 或 `none` 之中的一个，来设置 `mode` 参数，你可以启用 webpack 内置在相应环境下的优化。其默认值为 `production`。

### 组合式API和配置式API

在vue2中，我们需要将代码划分写到mouted、watch、computed，这个一定程度上规范了代码的整洁性，但是当组件内容过于复杂时，代码将变得复杂难懂。一个细小的逻辑任务，其相关代码可能要分散到多个选项中。而setup中可以调用computes watch等，是之能够像函数般在同一处复用。

### 生命周期

Vue实例有一个完整的生命周期，从开始创建、初始化数据、编译模板、挂载Dom、渲染→更新→渲染、销毁等一系列过程。

#### beforeCreate

数据对象data = undefined；实例的挂载元素el=undefined

#### created

data有了（可以访问数据和方法），但未挂载；el还无

#### beforeMount

data、el都有了，但没有挂载

#### mounted

vue实例已经挂载到了真实dom上，可以获取dom节点

#### beforeUpdate

#### updated

避免操作数据，防止死循环

#### beforeDestory

#### destoried



### PWA

PWA全称Progressive Web App，即渐进式WEB应用。一个 PWA 应用首先是一个网页, 可以通过 Web 技术编写出一个网页应用. 随后添加上 App Manifest 和 Service Worker 来实现 PWA 的安装和离线等功能



### gulp

gulp强调的是前端开发的工作流程，我们可以通过配置一系列的task，定义task处理的事务，然后定义执行顺序，来让gulp执行这些task，从而构建项目的整个前端开发流程。



### redux

在组件化的应用中，会有着大量的组件层级关系，深嵌套的组件与浅层父组件进行数据交互，变得十分繁琐困难。而redux，站在一个服务级别的角度，可以毫无阻碍地将应用的状态传递到每一个层级的组件中。redux就相当于整个应用的管家。

###  vue双向绑定

vue2 数据双向绑定是通过数据劫持，结合发布者-订阅者模式的方式来实现的。利用了 Object.defineProperty() 这个方法重新定义了对象获取属性值(get)和设置属性值(set)。

Vue3 使用的是proxy，这个可以自动检测到对象深层的变化。


### VUE和REACT有什么区别？

react整体是函数式的思想，把组件设计成纯组件，状态和逻辑通过参数传入，所以在react中，是单向数据流；

vue的思想是响应式的，也就是基于是数据可变的，通过对每一个属性建立Watcher来监听，当属性变化的时候，响应式的更新对应的虚拟dom。

### vuex有哪几种状态和属性

- modules：模块化vuex

- state中保存着共有数据，数据是响应式的

- getter可以对state进行计算操作，主要用来过滤一些数据，可以在多组件之间复用

- mutations定义的方法动态修改state中的数据，通过commit提交方法，方法必须是同步的

- actions将mutations里面处理数据的方法变成异步的，就是异步操作数据，通store.dispatch来分发actions，把异步的方法写在actions中，通过commit提交mutations，进行修改数据。


### vuex的流程

页面通过mapAction异步提交事件到action。action通过commit把对应参数同步提交到mutation。
mutation会修改state中对于的值。 最后通过getter把对应值跑出去，在页面的计算属性中通过mapGetter来动态获取state中的值



### vue路由的两种模式

- hash ——即地址栏URL中的#符号（此hsah 不是密码学里的散列运算） hash 虽然出现URL中，但不会被包含在HTTP请求中，对后端完全没有影响，因此改变hash不会重新加载页面。

- history ——利用了HTML5 History Interface 中新增的pushState() 和replaceState() 方法

这两个方法应用于浏览器的历史记录站，在当前已有的back、forward、go 的基础之上，它们提供了对历史记录进行修改的功能。只是当它们执行修改是，虽然改变了当前的URL，但你浏览器不会立即向后端发送请求。



### 观察者模式/订阅发布模式

当对象间存在一对多关系时，则使用观察者模式（Observer Pattern）。比如，当一个对象被修改时，则会自动通知依赖它的对象。观察者模式属于行为型模式。



### CI/CD

CI/CD 其实就是一个流程，用于实现应用开发中的高度持续自动化和持续监控。

CI/CD 是一种通过在应用开发阶段引入自动化来频繁向客户交付应用的方法。

CI/CD 的核心概念是持续集成、持续交付和持续部署。作为一个面向开发和运营团队的解决方案，CI/CD 主要针对在集成新代码时所引发的问题。

- “CI”始终指持续集成，它属于开发人员的自动化流程。成功的 CI 意味着应用代码的新更改会定期构建、测试并合并到共享存储库中。
- “CD”指的是持续交付，或持续部署
  - 持续交付，指开发人员对应用的更改会自动进行错误测试并上传到存储库
  - 持续部署，指自动将开发人员的更改从存储库发布到生产环境，以供客户使用。

[参考](https://juejin.cn/post/6844903976693940231#heading-55)

## 数据结构

### 查找

#### 二分查找

```javascript
var find = (arr, target) => {
  let l = 0, h = arr.length - 1;
  let m = null;
  while (l <= h) {
    m = Math.floor((l + h) / 2);
    if (target === arr[m]) {
      return m;
    }
    else if (target < arr[m]) {
      h = m - 1;
    }
    else {
      l = m + 1;
    }
  }
  return -1;
}
console.log(find([
  0, 1, 1, 2,
  6, 6, 6, 8
], 6));

// 查找重复元素里index最小的那个，也可以说是没有那值，返回稍大的值的index
var find2 = (arr, target) => {
  let l = 0, h = arr.length - 1;
  let m = null;
  while (l <= h) {
    m = Math.floor((l + h) / 2);
    if (target <= arr[m]) {
      h = m - 1;
    }
    else {
      l = m + 1;
    }
  }
  return l;
}
```



### 排序

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
}
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
}
var qSort = (arr, low, high) => {
  if (low < high) {
    // 随机快排，取值为[low, high]
    let i = Math.round(Math.random() * (high - low) + low);
    [arr[i], arr[low]] = [arr[low], arr[i]];
    pLoc = partition(arr, low, high);
    qSort(arr, low, pLoc - 1);
    qSort(arr, pLoc + 1, high);
  }
}

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
}
var merge = (l, r) => {
  let res = [];
  while (l.length > 0 && r.length > 0) {
    if (l[0] <= r[0]) {
      res.push(l.shift());
    }
    else {
      res.push(r.shift());
    }
  }
  return res.concat(l.length > 0 ? l : r);
}
var s = mSort([0, 1, 2, 1, 3, 8, 7, 6]);
console.log(s);
```

### 二叉树

```javascript
//  Definition for a binary tree node.
class TreeNode {
  constructor(val, left, right) {
    this.val = (val === undefined ? 0 : val)
    this.left = (left === undefined ? null : left)
    this.right = (right === undefined ? null : right)
  }
}
var tree = new TreeNode('A', new TreeNode('B', new TreeNode('D'), new TreeNode('E')), new TreeNode('C', new TreeNode('F'), new TreeNode('G')));

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
    }
    else {
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
}

var preOrder3 = (root) => {
  var stack = [];
  var res = [];
  var p = root;
  while (stack.length > 0 || p) {
    if (p) {
      stack.push(p);
      res.push(p.val);
      p = p.left;
    }
    else {
      p = stack.pop();
      p = p.right;
    }
  }
  return res;
}

console.log('preOrder2: ', preOrder2(tree));
console.log('preOrder3: ', preOrder3(tree));

var inRes = [];
var inOrderRecur = (root) => {
  if (!root) {
    return null;
  }
  inOrderRecur(root.left);
  inRes.push(root.val);
  inOrderRecur(root.right);
}
inOrderRecur(tree);
console.log('inOrderRecur: ', inRes);

var inOrder2 = (root) => {
  let res = [];
  let stack = [];
  let p = root;
  while (stack.length > 0 || p) {
    if (p) {
      stack.push(p);
      p = p.left;
    }
    else {
      p = stack.pop();
      res.push(p.val);
      p = p.right;
    }
  }
  return res;
}
console.log('inOrder2: ', inOrder2(tree));


var postRes = [];
var postOrderRecur = (root) => {
  if (!root) {
    return;
  }
  postOrderRecur(root.left);
  postOrderRecur(root.right);
  postRes.push(root.val);
}
postOrderRecur(tree);
console.log('postOrderRecur: ', postRes);


var postOrder2 = (root) => {
  let res = [];
  let stack = [];
  let p = root;
  let last = null;
  while (stack.length > 0 || p) {
    if (p) {
      stack.push(p);
      p = p.left;
    }
    else {
      p = stack.pop();
      // 如果右子树为空或者右子树已经遍历
      if (!p.right || p.right == last) {
        res.push(p.val)
        last = p;
        p = null;
      }
      else {
        stack.push(p);
        p = p.right;
      }
    }
  }
  return res;
}
console.log('postOrder2: ', postOrder2(tree));

```

