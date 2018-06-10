---
title: "建站笔记 - 将网站托管到 Github"
date: 2018-05-21T16:41:58+08:00
authors: ["somax"]
slug: host-on-github
tags:
  - git
  - 建站
---

有很多使用 github pages 功能免费发布网站的教程，也可以直接看 [官方文档](https://pages.github.com/)，就不再赘述了。

## github 设定

有一些注意事项这里提一下，根据 github 的规定，使用 `<用户名>.github.io` 作为项目名称，就可以通过 `<用户名>.github.io` 这个域名访问到网站，所以我的是就是 [somax.github.io](https://somax.github.io)。

hugo 渲染页面默认会输出到 public 目录，想了个方案来管理源文件和最终静态网站文件：

- 在项目中建立 `master` 及 `src` 两个分支，`master` 分支用来放最终静态网站文件，而 `src` 分支存放源文件
- 在 `src` 分支上，添加 `public` 目录到 `.gitignore` 以便提交源码时忽略它
- 然后在 `public` 目录中重新 `git init` 并存放 `master` 分支

这样就很方便了，源码在主目录操作，hugo 命令渲染好在进入 public 提交静态页面代码。

## 域名设置

想要 `.IO` 或者 `.ME` 的域名，找了一下国内域名供应商视乎没有提供。最后在 [godaddy.com](https://godaddy.com) 购买了 `somax.me`，第一年 6 折，2年共 ￥150.99。

购买成功后，进入 DNS 管理界面进行 DNS 配置：

| 类型  | 名称 | 值              | TTL    |
| ----- | ---- | --------------- | ------ |
| A     | @    | 185.199.110.153 | 600 秒 |
| CNAME | www  | somax.github.io | 1 小时 |

然后回到项目 master 分支（也就是 public 目录），添加 CNAME 文件：
```bash
echo somax.me > CNAME
```

提交代码后就可以使用新域名 [somax.me](https://somax.me) 访问我的博客了🏖。