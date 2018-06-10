---
title: "建站笔记 - 使用 hugo 建立我的博客"
date: 2018-05-21T14:01:10+08:00
authors: ["somax"]
slug: building-soblog-with-hugo
tags:
  - 建站
  - hugo
---

> 这是 [开篇](/post/first/) 之后的第一篇博客，记录一下建立这个博客网站的过程应该是个不错的选择。

### 前言
其实很久之前就有看到别人使用 Github Pages + jekyll 来免费搭建自己的博客网站，不过最近由于工作的原因接触 go 语言的项目越来越多，所以特意查了一下 Go 语言相关的建站工具，发现了 [hugo](http://gohugo.io) 这个项目。 就毫不犹豫的（其实还是纠结了一下）选择了 hugo。原因主要是 **开源，跨平台，安装简单，渲染速度快**！官方自称："**The world’s fastest framework for building websites**"

### 使用 Hugo 建立网站
过程中参考了[官方文档](http://gohugo.io/documentation/)，还有个老兄建立了中文版的网站 http://gohugo.org 也给了很大帮助。

不过作为学习总结，还是记录一下自己的操作过程。

#### 安装 Hugo

hugo 官网访问很慢，还是直接 [github release](https://github.com/gohugoio/hugo/releases) 下载比较快。根据操作系统选择合适版本下载即可（由于 Go语言的特性，天生跨平台，可以选择 ARM 版本来安装到树莓派上玩耍），具体过程就略了。


#### 新建站点

按照 [快速手册](https://gohugo.io/getting-started/quick-start/) 一步步下来，基本上没什么问题。

> hugo 安装完成一定要选择一个 Theme（或者自建--如果觉得自己可以的话），并且根据 Theme 的说明配置好 config.toml 文件，否则可能只能看到不完整的网站。

https://themes.gohugo.io/minimo/ 看上去不错，比较简洁，很适合做日志型网站。

[文档](https://minimo.netlify.com/docs/installation/) **一定**要先看一遍，避免走弯路（因为我已经走了🕺）。

#### 修改配置
1. 从 minimo theme 的 exampleSite 目录中复制 `config.toml` 并修改，主要修改的部分如下：

    ```toml
    baseURL = "https://somax.me"
    title = "So'Blog"
    
    theme = "minimo"
    
    Paginate = 10
    
    defaultContentLanguage = "zh"
    
    [params.info]
    description = "知识就像空气，学习如同呼吸。"
    
    [params.copyright]
    holder = "Somax"
    startYear = "2018"
    
    [params.settings]
    dateFormat = "2006-01-02"
    listDateFormat = "2006-01-02"
    archiveDateFormat = "01-02"
    ```


    [params.social]
    codepen = "somax"
    email = "somaxj@163.com"
    github = "somax"


    [languages]
    [languages.zh]
    lang = "zh"
    languageName = "Chinese"
    
    # 使用 filename.html 的方式，这样才能保证文档内图片相对路径的正确
    uglyURLs=true
    
    ```

1. 修改 `static/css/custom.css`，添加自定义样式
    ```css
    /* Custom CSS */
    .logo img {
        border-radius: 100%;
    }
    
    .menu li.current a {
        border-bottom: 2px solid #ffcd00;
    }
    
    #sidebar .widget-recent_posts .item-title{
        font-size: 0.9em;
    }
    
    @media screen and (min-width: 920px){
        .main {
            width: 100%;
        }
    }
    
    /* fix gitment width issue */
    #git-comments {
        max-width: 720px;
    }
    
    /* kbd style */
    kbd{
        box-shadow: inset 0 -2px 0 #c6cbd1;
        background-color: white;
    }
    ```

1. 修改 `content/_index.md` , 这些内容会显示在首页

    ```markdown
    ---
    title: 起点
    menu: main
    weight: -270
    ---
    > Stay hungry, stay foolish.
    > — Steve Jobs

    ```

#### 添加文章

接下来就是添加文章了。

上面看到特定名称 `_index.md`，该文件会自动渲染成文章目录，例如需要列出 post 子目录下的所有文章，则运行命令 `hugo new post/_index.md`，然后编辑：
```md
---
title: 闲言碎语
linkTitle:  碎语
menu: main
weight: -250
---

```
> `menu: main` 是为了使文章或者目录出现在主菜单中，`linkTitle` 则是在菜单中显示的名称

添加正式文章：`hugo new post/first.md`，本地编辑时开着 `hugo -D server` 可以实时预览，完成后用 `hugo` 命令生成静态网站文件。

要注意一下每篇文章默认都是草稿模式，正常发布时要把 `draft: true` 删除。

---
好了，如何将网站托管到 Github 留到下一篇再写吧。