---
layout:post
title:读书笔记----head first javascript 第一章
category:javascript
tags:[javascript]
description:None
---

一直想尽量学习一下javascript，至今才开始正式读一本javascript的书。

**head first javascript**

![](/assets/img/blog/head_first_javascript.gif)

不得不说，读一本英文书对我来说还是略吃力的。时间方面也是非常的紧张。

但相信只要开始去做，会越来越简单的。

正式读此书差不多一周了。慢慢也开始做记录写博客。希望可以快速提高对js的认识和理解。



> a quick dip inot javascript

第一章还是比较简单的，初步介绍了一下js。（话说，刚开始读，觉得序言很长，此书也比较有意思。英文吃力就只能慢慢来了）



本书内容面向的读者是有一些html和css基础的。（当然如果这点基础都没有，也谈不上web开发了）。

这本书并不像一本参考书一样，大量无聊地介绍一门语言地语法。更多的是学习去使用js。

首先，第一步我们需要了解js的作用及展示在浏览器上，并学会使用<script>标签

> The way javascript works
>
> How you're going to write javascript
>
> how to get javascript into your page

接着，声明变量，并了解变量

````javascript
var name;
var age = 24;
var total = price - (price * discount / 100);
````

然后我们可以尝试做更多的：

````javascript
var score = 5;
while (score > 0) {
	if (score == 3){
      	alert("score is 3");
	}
  	score = score - 1;
}
document.write("Oh,this is my score");
````

最后，我们还可以学习一下使用 console.log，并且使用开发中工具中的console（这是一个非常轻便、使用简单的工具，不用考虑编辑器等其他各种东西就可以做很多事情）。



### 总结

作为一本入门书来说，前边的章节不会有太复杂的概念，了解一些基础的知识（当然，也要了解一些基础的易犯的错误点），并学习读英文书。
