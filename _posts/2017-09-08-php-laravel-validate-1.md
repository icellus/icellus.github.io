---
layout: post
title: laravel 之验证器(1)
category: php
tags: [PHP]
description: None
---

在web开发中，参数验证一直都很重要的一点。

当我们作为初学者的时候，代码中可能充斥着大量的 `if else` 来进行参数的验证。这不论是对写代码的人，还是对于读代码的人，以及后期的维护，都是一件很痛苦的事情。

laravel 作为公认的优雅的php框架，`validator` 让参数验证不再痛苦。

使用Laravel 已经有一段时间了，这次来深入了解一下laravel 的 `validator`。

----

谈起laravel,不得不说其官方文档已经非常好了，但是如果真的想理解其功能，更需要一些php的基础知识和对框架的深入了解。



首先我们介绍一下 laravel `validator` 的实现方式。官方文档上已经说明了，总共提供三种方式。在此，我们对其实现进行简单的总结和分析。（关于验证规则、属性和错误信息，本文就不做概述了）


1. 基于`controller` 的实现

    ````php
    // app/Http/Controller
    <?php

    namespace App\Http\Controllers;

    use Illuminate\Foundation\Auth\Access\AuthorizesRequests;
    use Illuminate\Foundation\Bus\DispatchesJobs;
    use Illuminate\Foundation\Validation\ValidatesRequests;
    use Illuminate\Routing\Controller as BaseController;

    class Controller extends BaseController
    {
        use AuthorizesRequests, DispatchesJobs, ValidatesRequests;  // 引用trait
    }
    ````

    ​   从 `Controller` 类中我们可以看到，该类引用了 验证`validatesRequests`trait，即，所有的验证方法都是在该trait 中实现的。

    在该trait 中，主要实现的方法就是

    ````php
    public function validate(Request $request, array $rules, array $messages = [], array $customAttributes = [])
    {
        $validator = $this->getValidationFactory()->make($request->all(), $rules, $messages, $customAttributes); // 获得validator 实例，并对参数进行校验

        if ($validator->fails()) {
            $this->throwValidationException($request, $validator); // 验证失败，即抛出异常
        }
    }
    ````

    这段代码应该是相当的清晰简洁的。我们只需要在我们具体的业务代码中使用

    ````php
    $this->validate($request, $rules, $message, $attributes);
    ````

    即可完成参数的校验。同时我们可以通过重写`throwValidationException`方法来实现自定义异常。

2. 手动创建验证器

    ​   使用 `Controller` 的方法，固然轻松简洁。但是有时候可能会不能完全满足我们的业务需求。毕竟 `validatesRequests`只实现了简单的验证。

    这个时候，我们可以通过以下的方式来手动创建验证器。

    ````php
    use Illuminate\Http\Request;
    use Validator; // 注意引入命名空间

    class TestController extends Controller {
        public function myValidate(Request $request) {
           $validator = Validator::make($request->all(),$rules, $message, $attributes);// 手动创建验证器
           
           // 验证器钩子
           $validator->after(function($validator) {
               if ($this->somethingElseIsInvalid()) {
                   $validator->errors()->add('field', 'Something is wrong!');   
               }
           });

           //验证结果
           if ($validator->fails()) {
               //
           }
        }
    }
    ````

    ​   当我们手动创建验证器的时候，就需要通过 `fails()`方法来手动判断并抛出异常。同时，手动创建的验证器我们可以使用钩子来实现更复杂的验证以及添加更丰富的错误信息。

    ​   这里就需要提一下`validator`的钩子，即`after`方法，该方法支持传入一个闭包。在闭包中你可以随意的定义并实现你想要完成的验证逻辑。

3. 创建表单验证类

    ​   不可避免的，当我们在控制器中写下大量的验证逻辑，就会使得我们的业务代码和验证逻辑产生耦合。这在后期的维护上很可能给我们带来许多的困难和麻烦。

    ​   这个时候，laravel 提供了一种更好的方式来帮我们解决问题。

    ````php
    php artisan make:request MyFormRequest

    ````


    ​是的，直接使用 `artisan` 命令创建一个新的验证类，完全分离验证逻辑和代码。创建的验证类文件位于`app\Http\Request`,并继承于`app\Http\Request\Request`(抽象类)。

    我们再从抽象类`Request`的父类分析，其继承于`Illuminate\Foundation\Http\FormRequest`,主要的方法都在`FormRequest`中实现。

    而`FormRequest`继承于我们平时使用的请求对象`Illuminate\Http\Request`。所以表单验证类是可以完全调用请求对象`Illuminate\Http\Request`的方法的。

    ````php
    class FormRequest extends Request implements ValidatesWhenResolved
    {
       use ValidatesWhenResolvedTrait;
       
       ...  // 其他属性和方法
    }
    ````

    我们可以看到，FormRequest 主要是实现了`ValidatesWhenResolved`接口，并引入了`ValidatesWhenResolvedTrait`trait 。

    顾名思义，我们可以看出这个trait 主要实现了 `validate`什么时候会被解析。

    ````php
    // TestController
    public function myValidate(MyFormRequest, $request) {
        // 具体的业务逻辑代码
    }
    ````

    当我们想要使用该验证类的时候，只需要在控制器方法中利用类型提示传入请求即可，传入的请求会在控制器方法被调用前进行验证。

    当然，你需要在该请求类中书写对应的验证规则：

    ````php
    public function authorize()
    {
       return true; // 权限验证，如果你不使用它，或者想在其他地方实现，请实现该方法，并返回true;
    }

    // 对应的书写 rules,messages,attributes方法
    public function rules()
    {
       return [
           'name' => 'required|string'
       ];
    }

    // 表单验证类的钩子(laravel 5.5支持)
    public function withValidator($validator)
    {
        $validator->after(function ($validator) {
            if ($this->somethingElseIsInvalid()) {
                $validator->errors()->add('field', 'Something is wrong!');
            }
        });
    }
    ````


### 总结：

laravel 提供了非常便捷，使用简单的表单验证。

关于更多的验证器用法和实现，回头我们再来分析。


