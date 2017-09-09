---
layout: post
title: laravel 之验证器(1)
category: php
tags: [PHP]
description: None
---

作为web开发，参数验证一直都很重要的一点。

当我们作为初学者的时候，代码中可能充斥着大量的 if else 来进行参数的验证。这不论是对着写代码的人，还是对于其他读代码的人，以及后期的维护，都是一件很痛苦的事情。

laravel 作为公认的优雅的php框架，validator 让参数验证不再痛苦。

使用Laravel 已经有一段时间了，这次来深入理解研究以下laravel 的 validator。

----

谈起laravel,不得不说其官方文档已经非常好了，但是如果真的想理解其功能，更需要一些php的基础知识和对框架的深入了解。



首先我们介绍一下 laravel validator 的实现方式。官方文档上已经说明了，总共提供三种方式。在此，我们对其实现进行简单的总结和分析。

<ul>
<li>
基于controller 的实现

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

    ​   从 Controller 类中我们可以看到，该类引用了 验证（validatesRequests）trait，即，所有的验证方法都是在该trait 中实现的。

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

    即可完成参数的校验。同时我们可以重写`throwValidationException`方法来实现自定义异常。
</li>

<li>
手动创建验证器

    ​   使用 Controller 的方法，固然轻松简洁。但是又时候可能会不能完全满足我们的业务需求。毕竟 `validatesRequests`只实现了简单的验证。

    我们可以通过以下的方式来使用创建验证器。

    ````php
    use Illuminate\Http\Request;
    use Validator; // 注意引入命名空间

    class TestController extends Controller {
        public function myValidate(Request $request) {
           $validator = Validator::make($request->all(),$rules, $message, $attributes);
           
           $validator->after(function($validator) {
               if ($this->somethingElseIsInvalid()) {
                   $validator->errors()->add('field', 'Something is wrong with this field!');   
               }
           });

           if ($validator->fails()) {
               //
           }
        }
    }
    ````

    ​   当我们手动创建验证器的时候，就需要通过 `fails()`方法来手动判断并抛出异常。同时，手动创建的验证器我们可以使用钩子来实现更复杂的验证以及添加更丰富的错误信息。

    ​   在validator实例使用`after`方法，该方法支持传入一个闭包。在闭包中你可以随意的实现你想要完成的验证逻辑。
</li>


<li>
创建表单验证类

    ​   不可避免的，当我们在控制器中写下大量的验证逻辑，就会使用我们的业务代码和验证逻辑产生耦合。这在后期的维护上很可能给我们带来许多的困难和麻烦。

    ​   这个时候，laravel 提供了一种更好的方式来帮我们解决问题。

    ````php
    php artisan make:request MyFormRequest
    ````

    ​   是的，直接使用 `artisan` 命令创建一个新的验证类，完全分离验证逻辑和代码。这个时候必须要提的一点是，该`Request`和请求对象`Illuminate\Http\Request`是不同的。两者都是继承于`SymfonyRequest`，但各自的实现是完全不同的。

    ​   `Illuminate\Http\Request`的任务和作用更多是封装了关于本次请求的属性和方法。

    而我们创建的只是表单请求类，从下面这块代码中：

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
    ````
</li>

</ul>











### 总结：

laravel 提供了非常便捷，使用简单的表单验证。

关于更多的验证器用法和实现，回头我们再来分析。


