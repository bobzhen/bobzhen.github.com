---
layout: post
title: "[问答翻译] Javascript中的this"
description: "中文翻译"
category:
tags: javascript this chinese-translation
---
{% include JB/setup %}

## Javascript中的this

Javascript中的this是一个很神奇但又很恼人的东西。

我不是专业的js开发，平时都用coffeescript在ruby on rails项目中写jquery，从而实现server-side rendering，基本上不用担心js中的this的使用。

但是技不压身，多学点总是好的。

翻译一段stackoverflow中关于"[javascript中的this是如何工作的？](http://stackoverflow.com/questions/3127429/how-does-the-this-keyword-work)"的回答。以下为翻译正文:

====begin

[Mike West](https://mikewest.org/)写过一篇[Javascript中的作用域](http://www.digital-web.com/articles/scope_in_javascript/)，很简明的阐述了`this`的用法和javascript的作用域链(scope chains)。

当习惯了this，它的使用规则会一目了然。[ECMAScript](http://www.ecma-international.org/publications/standards/Ecma-262.htm)标准定义了`this`关键字是用来`对当前执行上下文中，ThisBinding的计算`(§11.1.1)。ThisBinding被Javascript解释器用来计算Javascript代码，可以理解为一个持有对某个对象的引用的特殊CPU寄存器。在下面三种不同情况中，当解释器创建一个执行环境(上下文)的时候，它会更新ThisBinding:

### 1. 初始化的全局执行环境(上下文)

这种情况下，当`<script>`元素在html中被触发时，Javascript代码将被计算(evaluate):

```html
<script type="text/javascript">//<![CDATA[
    alert("I'm evaluated in the initial global execution context!");

    setTimeout(function () {
        alert("I'm NOT evaluated in the initial global execution context.");
    }, 1);
//]]]></script>
```

在这种情况下，ThisBinding的值为全局对象: window.(§10.4.1.1)

### 2. 在一段被计算的代码(eval code)中

1) 直接调用eval()方法
  ThisBinding的值没有任何变化：它的值和当前调用的执行环境的ThisBinding的值一样。

2) 非直接调用eval()方法
  ThisBinding被赋值为全局对象，就像它在初始化的全局执行环境中一样($10.4.2(1))

EMACScript($15.1.2.1.1)中定义了什么是`直接调用eval()方法`。一般来说，`eval(…)`是一个直接调用，而相对应的，`(0, eval)(…)`或者`var indirectEval = eval; indirectEval()`是一个对eval()方法的间接调用。更多解释可以参考在[Javascript中(1,eval)(‘this’)和eval(‘this’)的比较](http://stackoverflow.com/q/9107240/196844)问题中，[chuckj](http://stackoverflow.com/questions/9107240/1-evalthis-vs-evalthis-in-javascript/9107491#9107491)的回答，另外如果你可能会用到间接eval()方法调用，参考这篇[博客](http://dmitrysoshnikov.com/ecmascript/es5-chapter-2-strict-mode/#indirect-eval-call)。

### 3. "进入"函数代码中

这种情况发生在调用一个函数的时候。当一个对象调用一个函数的时候，比如`obj.myMethod()`或者等同的调用`obj["myMethod"]()`，这种情况下，ThisBinding的值为这个对象(参考§13.2.1例子中的`对象`)。在其他大多数情况下，ThisBinding还是被设置为全局对象(§10.4.3)。

之所以称之为`其他大多数情况`，是因为ECMAScript 5中八种内置函数允许ThisBinding在参数列表中被指定。这些特殊函数接受所谓的thisArg参数，而参数在函数被调用的时候，变成了ThisBinding(赋值给ThisBInding)。

八种内置函数列表:

* Function.prototype.apply( thisArg, argArray )
* Function.prototype.call( thisArg [ , arg1 [ , arg2, ... ] ] )
* Function.prototype.bind( thisArg [ , arg1 [ , arg2, ... ] ] )
* Array.prototype.every( callbackfn [ , thisArg ] )
* Array.prototype.some( callbackfn [ , thisArg ] )
* Array.prototype.forEach( callbackfn [ , thisArg ] )
* Array.prototype.map( callbackfn [ , thisArg ] )
* Array.prototype.filter( callbackfn [ , thisArg ] )

对于Function.prototype的函数，它们被函数对象所调用，但是ThisBinding被赋值为thisArg而不是对应的函数对象。

对于Array.prototype的函数，在给定的回调函数callbackFn的执行环境里中，如果ThisBinding可以被赋值为thisArg，那么就赋值，如果不行，ThisBinding被赋值为全局对象。


上面说的都是纯Javascript中的规则。在Javascript的库中，比如jQuery，一些库函数会操作`this`的值。这些库的开发者们这样做的目的是为了支持一些通用的案例模版，对于这些库的使用者们来说，这样使用起来会很方便。当传入一个引用`this`的回调函数到库函数时，我们需要参考文档来确认当这个回调函数被调用的时候，`this`的值是什么。

`this`的值在Javascript库中是如何被操作的？这些库只是简单的使用Javascript中某个可以接收thisArg参数的内置方法。我们可以写一个自己的接受回调函数和thisArg为参数的函数:

```javascript
function doWork(callbackFn, thisArg) {
  // .  .  .
  if (callbackFn != null) callbackFn.call(thisArg);
}
```

更新:

俺忘记一个特殊情况。当通过`new`操作符构造一个新对象的时候，这个Javascript解释器建立一个新的空的对象，设置一些内置属性，接着对这个新的对象调用构造器函数。因此，在构造器环境中当一个函数被调用的时候，这个`this`的值就为解释器创建的新对象:

```javascript
function MyType() {
  this.someData = "a string";
}

var instance = new MyType();
// 下面的代码也可以，但是要写更多代码:
// var instance = {};
// MyType.call(instance);
```

小测试: Just for fun，测试你对下面几种情况的理解。
(译者注: 我的博客木有stackoverflow辣么高级，所以，直接把答案写在下面，而不是跟原文一样可以鼠标移上去看答案 :>)

1. 在A处的`this`值为甚么? 为什么?

```html
<script type="text/javascript">
  if(true){
      // A
  }
</script>
```

答案:

```
window,
因为A处于初始化的全局执行环境。
```

2. 当obj.staticFunction()被执行时，B处的`this`值为多少？为什么？

```html
<script type="text/javascript">
  var obj = {
    someData: "a string";
  };

  function myFun() {
    // B
  }

  obj.staticFunction = myFun;

  obj.staticFunction();
</script>
```

答案:

```
obj,
当对一个对象调用函数的时候，ThisBinding值为这个对象。

```

3. C处的`this`值为多少？为什么?

```html
<script type="text/javascript">
  var obj = {
    myMethod: function() {
      // C
    }
  };

  var myFun = obj.myMethod;
  myFun();
</script>
```
答案:

```
window,
这题中，Javascript解释器"进入"函数里，可是由于myFun/obj.myMethod没有被在某个对象中调用，ThisBinding被设置为`window`
```

4. D处的`this`值为多少？为什么?

```html
<script type="text/javascript">
  function myFun() {
    // Line D
  }

  var obj = {
    myMethod : function () {
      eval("myFun()");
    }
  };

  obj.myMethod();
</script>
```

答案:

```
window, 这题比较隐晦，当计算这个eval代码时，this的值为obj。但是在这个eval代码内部，myFun没有在对象上被调用，所以ThisBinding被设置为window。

```

5. E处的`this`值为多少？为什么?

```html
<script type="text/javascript">
  function myFun() {
    // Line E
  }

  var obj = {
    someData: "a string"
  };

  myFun.call(obj);
</script>
```

答案:

```
obj,
"myFun.call(obj);"调用特殊内置方法"Function.prototype.call()"，这个内置方法接受thisArg为第一个参数。

```
====end

全文完。
翻译不妥之处，请见谅，如果有空来函指正，将感激不尽！

