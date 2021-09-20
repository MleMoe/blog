---
title: linux 前端软装方案
date: 2021-09-20 19:49:52
tags:
  - linux
---

## 前言

半年没管过我的小机器，今天突然打开一看，？？？，不能登录了。

腾讯云：“乞丐版配置处于高负载状态，可能影响正常登录。”

“您是否要调整配置，需收费 XXX ”。

氪金是不可能氪金的。

尝试了没啥用的各种建议，不行。

于是采用重启大法，不得行。。。

那么就启动终极重置计划——重装系统吧！

反正里面除了配了一套开发环境也没啥，网站部署都白嫖 github page 了。

这次对着光杆 linux，就只装些前端开发必备环境了。

## git

```zsh
yum install git
```

搞一个 ssh 密钥

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
```

然后到 github setting 里 new ssh key 就好了。

## terminal

配置一个合心意的终端

### zsh

```bash
sudo yum install zsh
```

### ohmyzsh

```zsh
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

特别提醒：使用本地 ssh 连接 remote，该行命令无法执行成功，因为无法与 github 有效连接。查看 dns ，发现 github dns 解析是对的，没有出现污染问题。

换用 腾讯云 web 控制台打开 remote 终端，第一次 git clone ohmyzsh repo 失败，第二次成功。

网络问题就很迷。

安装常用插件：

```zsh
vi ~/.zshrc
# 在 ohmyzsh 的 plugins 加入
zsh-syntax-highlighting
zsh-autosuggestions
z
extract
history
```

其中 `zsh-syntax-highlighting` 和 `zsh-autosuggestions` 需要自己从 git clone

```zsh
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
```

然后 `source ~/.zshrc`，插件就可以使用了。

## node

为了方便，使用 `yum` 安装 node

```zsh
yum install nodejs
```

但是 yum 源安装的 node 版本是 v6.17.1，npm 版本为 3.10.10，不符合全球各国人民日渐增常的开发需求。

这里使用 `n` 这个 npm 包来管理 node 版本。

```zsh
npm install -g n
```

使用 `n` 安装 node 的长期支持版 v14.17.6

```zsh
n 14.17.6
```

输入：`PATH="$PATH"` ，更新 PATH 路径。

此时 node v14.17.6 安装完成， npm 版本也 到了 6.14.15，🎉。
