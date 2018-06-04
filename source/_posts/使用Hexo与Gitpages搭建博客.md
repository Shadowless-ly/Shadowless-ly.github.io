---
title: 使用Hexo与Gitpages搭建博客
comments: true
date: 2018-06-03 22:02:48
tag: hexo
categories: hexo
top:
---

# 使用Hexo与Gitpages搭建博客

一直使用为知笔记这款笔记软件来整理并记录自己学习的和感兴趣的东西。但是近段时间为知笔记的会员过期了，也不想每年花钱用这些笔记软件。忽然萌生出写博客这个想法。因为单纯想作为个人笔记使用，想要自己折腾折腾，所以不考虑使用博客园，CSDN这类的技术博客。经过一番搜索，调查，觉得用gitpages来托管静态页面这个方式还是挺适合的，不需要买云空间，而且也有现成的框架。

各种度娘，谷歌，折腾了一番，这个Blog也差不多能用了，趁热打铁，整理一下搭建过程。

## 搭建本地Hexo运行环境

我们需要先在本地搭建Hexo运行环境，安装Hexo并初始化我们的博客，然后进行一些配置修改，使其可以本地正常访问。

### 了解Hexo

> Hexo是高效的静态站点生成框架，她基于Node.js。 通过 Hexo 你可以轻松地使用 Markdown 编写文章，除了 Markdown 本身的语法之外，还可以使用 Hexo 提供的标签插件来快速的插入特定形式的内容，而且相对于其他框架，Hexo在速度上也有很大优势。

{% exturl Hexo https://hexo.io/zh-cn/ Hexo官网%}

### Git版本管理工具安装

Git是目前世界上最先进的分布式版本控制系统。我们可以使用它对代码，文档等文件进行版本管理。在本次博客搭建过程中，它的主要作用是帮助我们从github远程仓库获取各种工具，以及将Hexo生成的静态页面同步到远程仓库。而且对Windows来说可以使用其minitty作为命令行的替代。

* Windows：下载并安装 [git](https://git-scm.com/download/win "下载git")。
* Mac：使用 [Homebrew](http://mxcl.github.com/homebrew/), [MacPorts](http://www.macports.org/) ：`brew install git`；或下载 [安装程序](http://sourceforge.net/projects/git-osx-installer/) 安装。
* Linux (Ubuntu, Debian)：`sudo apt-get install git-core`
* Linux (Fedora, Red Hat, CentOS)：`sudo yum install git-core`

{% note warning　%}
** Windows用户　**
由于天朝众所周知的原因，下载git会非常缓慢，建议使用代理。
或者可以参考这里[Git for Windows 国内下载站](https://github.com/waylau/git-for-win "git for windows下载")。
{% endnote %}

如果想学习Git的使用，推荐一下廖老师的教程　{% exturl 廖雪峰的官方网站 https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000 %}

### Node.js环境搭建
由于Hexo是基于Node.js，所以我们必须先准备好Node.js环境。

#### 了解Node.js

> Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。 
> Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。 
> Node.js 的包管理器 npm，是全球最大的开源库生态系统。

{% exturl Node http://nodejs.cn/ Node.js官网 %}

#### Node.js安装
使用[nvm](https://github.com/creationix/nvm)来安装Node.js
{% tabs node.js, 1%}
<!-- tab cURL@download -->
使用curl安装
`$ curl https://raw.github.com/creationix/nvm/master/install.sh | sh`
<!-- endtab -->
<!-- tab Wget@download -->
使用Wget安装
`$ wget -qO- https://raw.github.com/creationix/nvm/master/install.sh | sh`
<!-- endtab -->
{% endtabs %}

安装完成后重启终端，执行以下命令安装Node.js
```bash
nvm install stable
```

{% note info %}
也可以直接下载[安装程序](https://nodejs.org/en/download/)执行安装
{% endnote %}

### 安装hexo
使用Node.js的包管理工具npm安装hexo
```bash
npm install -g hexo-cli
```
安装完成后，可以在命令行终端试一下hexo是否可以使用
![hexo](http://p9lal5uqx.bkt.clouddn.com/使用Hexo与Gitpages搭建博客/20180604111622738.png)

若不能使用，可以检查以下几方面：
* npm install过程是否有异常
* 检查环境变量是否正常，安装时会在/usr/bin/中创建hexo可执行程序的软链接
* 若链接不存在，检查/usr/lib/node_modules/hexo/bin/hexo是否存在，手工在/usr/bin/下创建链接`ln -s /usr/lib/node_modules/hexo/bin/hexo /usr/bin/hexo`

### 使用Hexo建站

#### 初始化
使用如下命令，hexo可以在一个指定文件夹(blog)中创建所需文件

```bash
$ hexo init blog
$ cd blog
$ npm install
```

完成后使用`tree blog`可以得到目录树如下

```raw
.
├── _config.yml    # 网站的配置信息，您可以在此配置大部分的参数。
├── package.json    # 应用程序的信息。
├── scaffolds    # 模版文件夹。新建文章时，Hexo会根据 scaffold 来建立文件。
├── source    # 资源文件夹是存放用户资源的地方
|   ├── _drafts
|   └── _posts
└── themes    # 主题文件夹。Hexo会根据主题来生成静态页面。
```

#### 配置

在blog目录下，_config.yml中为用户可以自定义的配置，我们可以从[Hexo官网](https://hexo.io/zh-cn/docs/configuration.html)了解各个参数的意义。
下面列出我搭建时的修改项。

```raw
# Site
title: Shadowless
subtitle: 备忘录
description: 求知若饥，虚心若愚
keywords: blog
author: Shadowless
language: zh-CN
timezone: Asia/Shanghai
```

|参数|描述|
|:-|:-|
|title|网站标题|
|subtitle|网站副标题|
|description|网站描述|
|author|您的名字|
|language|网站使用的语言，如：zh-CN|
|timezone|网站时区。Hexo 默认使用您电脑的时区。如：Asia/Shanghai|

```raw
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://shadowless.top
root: /
# permalink: :year/:month/:day/:title/
permalink: :category/:title/
permalink_defaults:
```

|参数|描述|默认值|
|:-|:-|:-|
|url|网址|　|	
|root|网站根目录|　|	
|permalink|文章的永久链接格式|:year/:month/:day/:title/|
|permalink_defaults|永久链接中各部分的默认值|　|
 


{% quote liyang, shadowless http://shadowless.top help %}
s
{% endquote %}

{% quote liyang, shadowless %}
a
{% endquote %}

{% label success@test %}{% label primary@Text %}


 {% note  info%}
 Any content (support inline tags too).
 {% endnote %}
 
 
 
  * {% exturl text url "title" %}
      * {% extlink what? http://shadowless.top "shadowless" %}

 {% centerquote %}Something{% endcenterquote %}
 {% cq %}Something{% endcq %}
 
 {% button http://shadowless.top, text, icon button, title %}
