---
title: 发布blog内容到服务器上
date: 2024-4-12 21:38:37
---

> 参考博客：https://www.wuyinghao.cn/2021/05/02/Hexo-Github-VPS-%E5%90%8C%E6%AD%A5%E6%96%B9%E6%A1%88/
> hexo官网：https://hexo.io/zh-cn/docs/one-command-deployment#Git

## 服务器安装GIT

```
Windows：下载并安装 git。
Mac：使用 Homebrew, MacPorts 或者下载 安装程序。
Linux (Ubuntu, Debian)：sudo apt-get install git-core
Linux (Fedora, Red Hat, CentOS)：sudo yum install git-core
```

## 安装node.js

1. 更新系统包列表
```
apt-get update
```

2. 安装依赖包
```
apt-get install -y curl gnupg
```

3. 安装Node.js
```
apt-get install -y nodejs
```
4. 验证安装
```
node -v
```