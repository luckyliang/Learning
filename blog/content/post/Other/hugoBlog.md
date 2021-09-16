---
layout:     post
title:      "Hugo 搭建个人博客"
subtitle:   ""
description: "hugo 搭建静态博客"
excerpt: ""
date:       2018-02-11 09:32:00
author:     "Cheng"
image: "https://img.zhaohuabing.com/in-post/2018-04-11-service-mesh-vs-api-gateway/background.jpg"
published: true
tags:
    - blog
URL: "/2018/02/11/hugoblog/"
categories: [ other ]

---



Hugo是由Go语言实现的静态网站生成器。简单、易用、高效、易扩展、快速部署。使用的是go语言的模板语法

## 创建

hugo的安装参考[hugo中文文档](https://www.gohugo.org/)，[官方文档](https://gohugo.io/documentation/)

新建站点，执行命令

```bash
$ hugo new site blog
```

站点目录：

```
▸ archetypes/
▸ content/
▸ data/
▸ layouts/
▸ static/
▸ themes/
config.toml

```

### archetypes 目录

默认，通过 `hugo new` 创建的内容会根据`archetypes`中的`default.md`的内容格式生成新的文章

新建的.md文件会保存到content目录中，也可以在content目录中创建字目录，例如创建一个about目录，然后执行

```bash
$ hugo new about/about.md
```

 就会在`content/about`目录中生成该文件

### content 目录

所有内容页面存放目录，content 下的一级子目录看作一个对应的 **section** 内容分类区 content section。比如，为博客设置一个 `blog` 目录，为文章设置一个 `articles` 目录，为教程设置一个 `tutorials` 目录等，Hugo 使用内容分类区分作为默认**内容类型** content type，如果在扉页 front matter 设置了 `type` 则以具体设置的类型为准。

### layouts 目录

布局模板文件目录，存放 `.html` 布局模板文件，对应不同的内容，模板有多种，data-templates、homepage、lists、menu-templates、partials、section-templates 等等。

站点的首页模板在主题目录中 **layouts/index.html**，除首页外，Hugo 有两类基本页面：

- Single page 单体页面，如 **hugo new demo.md** 创建的 Post 页面；
- List page 列表页面，如 tags 或 categories 页面；

###  static 目录

静态资源存放目录，比如想使用 Marmarid 画作模块，或者 jQuery 工具库，或者其它脚本、图像、CSS 等等，就可以将文件放到这里，在 Hugo 编译生成时会自动原样复制到 **public** 目录。注意，可以有多个静态资源目录。

### resources 目录

资源缓冲目录，非默认创建，用于加速 Hugo 的生成过程，也可以用给模板作者分发构建好的 SASS 文件，因此不必安装预处理器。

### public 目录

生成静态站点的文件输出目录。

### assets 目录

不是默认创建的资源目录，保存所有需要通过 **Hugo Pipes** 处理的资源，只有那些 `.Permalink` 和 `.RelPermalink` 引用的文件会发布到 `public` 目录中，参考 Hugo 管道处理。

### config 目录

配置目录，非默认创建，Hugo 有大量的配置指令，此目录用于保存 JSON, YAML, TOML 等配置文件。最简单的项目只需要一个根目录下的配置文件 `config.toml`。 Every root setting object can stand as its own file and structured by environments.

内容目录结构与 URL 对应关系参考：

```bash
.
└── content
    └── about
    |   └── index.md       // <- https://example.com/about/
    ├── posts
    |   ├── _index.md      // <- https://example.com/posts/
    |   ├── index.md       // <- https://example.com/posts/
    |   ├── firstpost.md   // <- https://example.com/posts/firstpost/
    |   ├── happy
    |   |   └── ness.md    // <- https://example.com/posts/happy/ness/
    |   └── secondpost.md  // <- https://example.com/posts/secondpost/
    └── quote
        ├── first.md       // <- https://example.com/quote/first/
        └── second.md      // <- https://example.com/quote/second/
```

每个目录下的 **_index.md** 和 **index.md** 是特殊的索引页面，二选一使用。可以在其扉页 front matter 为模板提供元数据，如 list templates, section templates, taxonomy templates, taxonomy terms templates, homepage template 等等。

### data 目录

数据模板目录，Hugo 静态网站不会连接像 MySQL 这样的数据库，而此目录保存的数据相当于 Hugo 使用的数据库，生成过程用到的配置数据，可以用 YAML, JSON, TOML 等格式文件。数据模板除了在此的文件定义，还可以从动态内容中生成。通过 **getJSON** 和 **getCSV** 两个函数是模板函数加载外部数据，或者访问数据接口，在外部数据加载完成以前，Hugo 会暂停渲染模板文件。

## 安装主题

这里主题使用的是 [hugo-theme-cleanwhite](https://github.com/zhaohuabing/hugo-theme-cleanwhite)

在themes文件中执行

```bash
git clone https://github.com/zhaohuabing/hugo-theme-cleanwhite.git
```

该主题提供了例子安装github上教程可以轻松集成， 也可以根据`exampleSite`中的配置在自己的`config.toml`中配置

```bash
$ mkdir test		//创建测试站点
$ cd test
$ mkdir themes
$ cd themes
$ git clone https://github.com/zhaohuabing/hugo-theme-cleanwhite.git
$ cp -r hugo-theme-cleanwhite/exampleSite/** ../			//复制示例站点配置
$ cd ..
$ hugo serve			
```

hugo生成静态文件

```bash
$ hugo -D
或者更详细的
$ hugo --theme=hugo-theme-cleanwhite --buildDrafts --baseUrl="https://xxx.github.io/"
```

会生成存放静态页面`public`目录

将`public`静态文件上传到github上，这里不是用github来部署博客网站，只是将静态文件上传到github上，然后在服务端clone github上的静态文件再进行部署

```bash
$ cd public
$ git git init --separate-git-dir ../blogGit	//实现源码和git厂库分离blogGit为public的同级目录
$ git add .
$ git commit -m "first commit"
$ git branch -M main
$ git remote add origin github远程仓库
$ git push -u origin main //注意在推送前查看config文件是否配置了user
```

config文件中配置git用户

```
[user]
	name = gitusername			//git用户名
	email = xxxx@xx.com			//用户邮箱
	password = xxxxxx				//密码，现在密码改为token了
```

[github 生成token的方法](https://www.cnblogs.com/leon-2016/p/9284837.html)

## 部署

部署到自己的云服务器上，首先准备好服务器和域名，我这里用的是阿里的ES服务器

### nginx配置

nginx安装就不说了网上一大堆

nginx配置

```shell
server {
    listen    8080 default_server;		配置监听端口
    listen    [::] default_server;
    server_name   xxx.xx.xx.xx;				//服务器的ip地址
    location / {
           root /var/www/Blog;				//根目录
           index index.html index.htm;		//入口
        }
}
```

修改配置后刷新

```bash
nginx -c /etc/nginx/nginx.conf
nginx -s reload
```

在`/var/www/`路径下`pull`之前推送到`github`上的仓库，最终静态文件存放在`/var/www/Blog`中

nginx其他可能用到的命令

重启服务： `service nginx restart`

快速停止或关闭Nginx：`nginx -s stop`

配置文件修改重装载命令：`nginx -s reload`

### 配置安全组

配置安全组主要是对外开放端口，外界能访问，在阿里云安全组配置如下

![image-20210914190012656](/img/image-20210914190012656.png)



参考链接

[Hugo不完美系列教程](https://www.jianshu.com/p/c5297a8bb1e7)

