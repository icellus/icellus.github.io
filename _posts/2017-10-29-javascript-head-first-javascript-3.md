---
layout: post
title: 读书笔记：head first javascript 第三章
category: js
tags: [javascript]
description: None
---

简单地了解认识过js之后，我们就要开始接触更多更精彩的部分了。

> introducing functions

这一章，我们开始认识函数。



##### 声明函数

一开始，引入一个问题，当我们不得不写大量重复逻辑的代码的时候，有没有什么好的方法去实现代码的复用？

函数就是帮我们解决这个问题的。

````javascript
function speak (name,word) {
	console.log(name + "said:" + word +"!");
	return true;
}
speak('mike','hello');
speak('jerry','hi,my friend');
````

我们声明函数，并可以随意地在函数中实现我们想复用的代码和逻辑。

函数是可以使用 `return ` 关键字来返回一个值的。（如果未`return`任何东西，函数的返回值为`underfined`）

同时，我们需要理解一个概念----`pass-by-value`，即`javascript`是值传递的。

和其他语言中的 `pass-by-value`是一样的，当传递给变量参数的时候，只是copy 了一份变量的值，并传递给函数的参数，对于这个参数在函数内的操作，是并不会影响到原变量的。



##### 函数的作用域

当我们开始接触到函数的时候，自然就会接触到 作用域`scope`。

`javascript`中的两种作用域： `global`和`local`(和php中不同的是，php函数中无法引用外部变量，js是可以直接使用的)

````javascript
var name = 'dog';		// global
function test(){
  	document.write(name);
  	var age = 'cat';			//local
  	document.write(age);
}
test();

//output
dogcat
````

在函数中声明的变量的作用域是`local`，在函数外声明的变量的作用域是`global`。

而我们是无法在函数外部去访问一个作用域为`local`的变量的。



这里会有一个有意思的事情。就是声明一个变量是否使用`var`:

````javascript
var name = 'dog';
function test() {
  	name = 'cat';			// 未使用 var 关键字，使用了全部变量 name,并重新对 name 赋值
  	document.write(name);
}
test();
doucment.write(name);
var name;				// 如果没有赋值，并不会改变变量
document.write(name);

//output
catcatcat
````

注：其实在函数外声明变量是否使用 var 也是不同的。 



最后，还需要意识到一点。函数是可以定义在任何位置的。

````javascript
var name = test();
function test() {
  	return 'dog';
}
````

这样看起来可能是很奇怪的。看起来，浏览器加载一个页面的时候，是从上往下加载的。但事实上js `make two passes over your page`，即js会先浏览所有定义的函数，然后才开始执行你的代码。



#### 总结

本章也只是初步简单地了解了一下js 中的函数，及其特性。更加深入的了解和使用，还需要继续地深入。



