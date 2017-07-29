---
layout: post
title: 如何在github上搭建博客
category: 杂货铺
tags: [Github, Jekyll, Blog]
description: None
---

> 之前在CSDN上写过一些博客，但总觉得页面不够美观、简洁。今年学了一些前端（Front End）的知识（HTML、CSS、JavaScript），也了解了Bootstrap前端框架，看到原来界面可以做得这么漂亮，这些工具使用起来也不复杂。于是就决定自己搭建一个独立的博客，一来可以记录平日的所学所思，二来也可以藉由这个过程多学习掌握前端的知识，也算是学以致用了。


# 一、为什么想到在github上搭建博客
平日里工作会使用git工具管理代码，git实在是太强大而且github也足够好用，正好它也提供静态网页的功能 - [Github Pages](https://pages.github.com/)。更重要的是，网页托管在github上面，一切都是免费的，空间免费，也不用花钱买域名，省了不少事情。当然，搭建博客之后的主要工作还是撰写和维护，如果这些事情处理起来不方便，搭建独立博客的意义就不大了。在github上撰写博客，使用了Jekyll这个工具，当然如果你直接看它的官网内容可能不知道它在说什么，简单来讲，Jekyll就是一个博客撰写的工具，只要遵照YAML的语法写好文本文件，Jekyll就会解析这个文本文件并自动将它转换为网页HTML文件。还好，YAML的语法比HTML简单得多。至于维护，就不用多说了，github本身就是托管源码的平台，你只要把博客的文本文件当作源码管理就好了，只要github账户不注销，里面的内容永远不会丢。


# 二、github上博客管理的两种方式
[Github Pages](https://pages.github.com/)提供了可以展现网页站点的方式：项目页面、个人页面。这两种方式在网上都有人提及并使用，这里简单说明一下。

1. 项目页面
Github本身是用来管理项目源码的，每个项目都可以有一个展现功能的页面，可以藉由这个特点来搭建个人的博客。具体的搭建过程样例，可以参考[《搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门》](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)。

2. 个人页面
这个就是纯粹的网站了，和托管的项目没有关系。这种方式要求一个Github账号只能搭建一个网站，要想搭建多个站点的话，只能再申请新的账号了。

> 两种方式都会有一定的规则和要求，看自己情况进行选择就可以。我选择第二种方式，可以当作一个项目来维护，git操作更简单一些。


# 三、搭建博客

[Github Pages](https://pages.github.com/)已经提供了搭建的步骤，不过它只提供了一个demo。我想要的一个博客的框架而不仅仅是一个毫无生气的纯文字页面，既然github的项目是可以自由下载的，那么把别人好看的网站源码拿过来修改是最快也是最保险的方法。

1.首先需要创建一个项目仓库，需要注意的是，项目仓库的名称必须是username.github.io，下图是官方的示例。下文就以该用户为例来搭建博客。
![](/assets/img/blog/20151206-1.jpg)

2.将新创建的项目克隆到本地

    ~ $ git clone https://github.com/tobiasahlin/tobiasahlin.github.io.git

3.挑选模板，[jekyllthemes.org](http://jekyllthemes.org/)这个网站提供了一些模板，也可以在网上找一些别人用github搭建的博客直接clone过来使用。这里以clone我的为例，我的博客项目仓库名称是mumingv.github.io。

4.克隆模板

    ~ $ git clone https://github.com/mumingv/mumingv.github.io.git
    ~ $ ls -1
    mumingv.github.io
    tobiasahlin.github.io

5.去掉模板目录里的.git目录，并将其他内容移到tobiasahlin.github.io目录下去

    ~ $ rm -rf mumingv.github.io/.git/
    ~ $ ls -1a tobiasahlin.github.io/   
    .
    ..
    assets
    _config.yml
    feed.xml
    .git
    _includes
    index.html
    _layouts
    pages
    _posts
    README.md
    sitemap.txt

6.修改配置&删除原有文章

编辑配置文件_config.yml，修改其中的“作者信息”和“站点信息”。_posts目录下存放博客文章，保留一两篇做测试即可，其余的都可以删掉。

7.将博客提交到Github仓库

    ~ $ cd tobiasahlin.github.io
    ~ $ git add --all
    ~ $ git commit -m "Initial commit"
    ~ $ git push -u origin master

8.到这一步，框架就算搭建好了，浏览器中输入http://tobiasahlin.github.io就可以看到效果了。

9.如果要修改页面的风格和布局，可以在_layouts和_includes目录下找对应的文件修改，不过这个就需要一些前端开发的知识了。

**--END**
