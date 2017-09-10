---
layout: post
title: 读书笔记：head first javascript 第一章
category: js
tags: [javascript]
description: None
---

一直想深入学习一下javascript，至今才开始正式读一本javascript的书。

**head first javascript**

![](/assets/img/blog/head_first_javascript.gif)

不得不说，读一本英文书对我来说还是略吃力的。时间方面也是非常的紧张。

但相信只要开始去做，会越来越简单的。

正式读此书差不多一周了。同时也开始做笔记写博客。希望可以快速提高对js的认识和理解。



> a quick dip into javascript

第一章还是比较简单的，初步介绍了一下js。（话说，刚开始读，觉得序言很长，此书也比较有意思。英文吃力就只能慢慢来了）



本书内容面向的读者是有一些html和css基础的。（当然如果这点基础都没有，也谈不上web开发了）。

这本书并不像一本参考书一样，大量无聊地介绍一门语言地语法。更多的是引导读者去学习使用js。

首先，第一步我们需要了解js的作用及展示在浏览器上，并学会使用<script>标签

> The way javascript works
>
> How you're going to write javascript
>
> how to get javascript into your page

紧接着，会开始学习 js 中的变量，如何声明一个变量，并了解其含义和数据类型

````javascript
var name;
var age = 24;
var total = price - (price * discount / 100);
````

掌握了变量之后，我们可以尝试使用变量和一些关键词做更多事情，比如`while`循环：

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

最后，我们还会学习到如何使用 console，不仅仅是代码中的`console.log`,也包括开发工具中的`console`。

（不得不提的是，浏览器的cosole无疑是入门js的最快途径，作为一个浏览器自带的轻便，简单的工具，不用考虑编辑器等其他因素，你就可以通过它完成很多有意思的尝试和代码了！）。



### 总结

作为一本入门书籍来说，前边的章节并不会有很多太复杂的概念，了解一些基础的知识（同时认识一些基础的易犯的错误点），正式开始 读一本英文原著和 js 的学习之路吧。
