---
title: hexo-theme
date: 2021-04-26 23:13:50
tags:
  - hexo
---

## 起步

### 选择工具

模版引擎：pug

CSS 预处理器：stylus

### 搭建 Hexo 工作目录

开发主题之前，需要搭建 Hexo 工作目录，有两种方式可供选择

- 使用 hexo-cli
- 克隆 Hexo 主题的单元测试项目

搭建完 Hexo 开发环境后，需要安装相关插件来支持所使用的渲染引擎。Hexo 默认安装的渲染引擎是 EJS 和 Stylus，并且 Hexo 内置了 Swig，因此，如果选用 EJS + Stylus 或 Swig + Stylus，那么可以忽略这段，如果选择了其它的渲染引擎，需要自行选择安装。如我选择了 PUG + Stylus，那么可以只安装 PUG。

```shell
yarn add hexo-renderer-pug
```

### 生成主题结构目录

上一步只是搭建好了 Hexo 工作目录，接下来是创建主题的文件目录，可以参考着已有的主题的文件目录手动创建，也可以使用 Yeoman 自动生成，使用 Yeoman 自动生成的步骤如下。

#### 安装 Yeoman

```shell
yarn global add yo
yarn global add generator-hexo-theme
```

#### 生成主题目录结构

进入 Hexo 的 themes 目录中（使用 test 模版创建方式无 themes 目录，需`mkdir themes`手动创建），新建一个文件夹作为主题目录（如`mkdir space`），然后进入该文件夹中（`cd space`），执行指令：`yo hexo-theme`，按照提示填写或选择相应的信息。

```shell
--=[ generator-hexo-theme ]=--
? What is the theme name? space
? Which template language to use? pug
? Which stylesheet language to use? styl
? Other technical features Hexo scripts directory (hexo plugins), EditorConfig file .editorconfig, node npm package.json
```

生成结构如下

```plainText
.
├── layout        # 布局文件夹
|   ├── includes
|   |   ├── layout.pug       # 页面总体布局
|   |   └── recent-posts.pug # 文章列表
|   ├── index.pug            # 首页
|   ├── archive.pug          # 归档页
|   ├── category.pug         # 分类页
|   ├── tag.pug              # 标签页
|   ├── post.pug             # 文章页
|   └── page.pug             # 除以上页面之外的页面
├── scripts       # 脚本文件夹
├── source        # 资源文件夹
|   ├── css
|   ├── js
|   └── favicon.ico
├── .editorconfig # 编辑器配置文件
├── _config.yml   # 主题配置文件
└── package.json
```

参考：[从 0 到 1 开发 Hexo 主题杂谈](https://liuyib.github.io/2019/08/20/develop-hexo-theme-from-0-to-1/)
