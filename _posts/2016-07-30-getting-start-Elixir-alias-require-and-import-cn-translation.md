---
layout: post
title: "[文档翻译] Elixir语言学习: Alias, Require 和 Import"
description: "中文翻译"
category:
tags: elixir chinese-translation
---
{% include JB/setup %}

## 13. alias, require 和 import

原文: [http://elixir-lang.org/getting-started/alias-require-and-import.html](http://elixir-lang.org/getting-started/alias-require-and-import.html)

1. [alias 别名](#alias)
2. [require 需要(这翻译的好恶心)](#require)
3. [import 引入](#impoer)
4. [use 使用](#use)
5. [理解别名](#understanding-aliases)
6. [模块嵌套](#nested-modules)
7. [同一个 alias/import/require/use 定义中的多个嵌套模块名](#multi)


为了让软件复用更容易，Elixir提供了三种[编译指令](https://zh.wikipedia.org/zh-cn/%E7%B7%A8%E8%AD%AF%E7%A8%8B%E5%BC%8F%E5%AE%9A%E5%90%91) (`alias`, `require` 和 `import`)，加上一个[宏指令](https://zh.wikipedia.org/wiki/%E5%B7%A8%E9%9B%86) `use`，如下概述:

```elixir
# 模块别名，模块可以直接用Bar来调用，而不是Foo.Bar
alias Foo.Bar, as: Bar

# 确保模块被编译并且可以使用(通常用于macro)
require Foo

# 从Foo中引入所有的函数，这样的话，这些被引入的函数，不需要使用`Foo.`前缀
import Foo

# 作为一个扩展点来调用Foo中的自定义代码
use Foo
```

接下来是细节部分。前三个之所以被称为编译指令是因为它们有**[词法作用域](https://www.zhihu.com/question/20032419)**，`use`只是一个通用的扩展点。


### <a id="alias"></a> alias

`alias`可以给指定的模块名设置别名。现在假设`Math`模块有一个叫做List的指定数学运算的[实现](https://en.wikipedia.org/wiki/Implementation):

```elixir
defmodule Math do
  alias Math.List, as: List
end
```

现在，任何`List`的调用都会自动扩展到`Math.List`。你可以使用`Elixir.`前缀，来使用原来的`List`模块:

```elixir
List.flatten             #=> uses Math.List.flatten
Elixir.List.flatten      #=> uses List.flatten
Elixir.Math.List.flatten #=> uses Math.List.flatten
```

> 注释: 所有Elixir中定义的模块，都在同一个Elixir命名空间中。在实际调用中，为了方便可以省略`Elixir.`前缀。

别名通常是为了简写。如果调用`alias`的时候不使用`:as`选项，被引用模块会的别名会自动的被设置为模块名的最后一部分:

```elixir
alias Math.List
```

等同于:

```elixir
alias Math.List, as: List
```

注意`alias`是**词法作用域的**，你可以在某个函数内部设置别名:

```elixir
defmodule Math do
  def plus(a, b) do
    alias Math.List
    # ...
  end

  def minus(a, b) do
    # ...
  end
end
```
上面的例子中，我们在`plus/2`函数内部调用了`alias`，这个别名只会在`plus/2`中有效，`minus/2`不会受影响。


### <a id="require"></a> require

Elixir[元编程](https://zh.wikipedia.org/wiki/%E5%85%83%E7%BC%96%E7%A8%8B)(利用代码生成代码)提供宏指令机制，

宏指令指的是在编译时，某一段代码被执行并且被扩展。这就意味着，当使用宏指令时，需要确保宏指令的模块和相关实现是可用的。我们可以用`require`这个编译指令来确保宏指令的模块和相关实现的可用性。

```iex
iex> Integer.is_odd(3)
** (CompileError) iex:1: you must require Integer before invoking the macro Integer.is_odd/1
iex> require Integer
Integer
iex> Integer.is_odd(3)
true
```

在Elixir中，`Integer.is_odd/1`宏指令可以作为一个[`卫士`](https://en.wikipedia.org/wiki/Guard_(computer_science))来使用。可以理解为，我们必须首先require(不知道怎么翻译这个require)`Integer`模块。

一般来说，一个模块在被使用前不需要被require，除非我们想使用该模块中的某条宏指令。尝试调用一个没有被加载的宏指令会抛出(raise)一个错误。和`alias`编译指令一样，`require`也是***词法作用域的***。接下来的某个章节会提到宏指令。

### <a id="import"></a> import

当我们需要便捷访问到其他模块中的函数或者宏指令，却又不想使用[完全限定名](https://msdn.microsoft.com/zh-cn/library/aa287553(v=vs.71).aspx)的时候，`import`可以实现这个便捷访问。 例如，为了使用`List`模块中的`duplicate/2`方法，我们可以简单的引入它:

```iex
iex> import List, only: [duplicate: 2]
List
iex> duplicate :ok, 3
[:ok, :ok, :ok]
```

上面的例子中，我们只从`List`中引入了`duplicate`函数([元数](https://zh.wikipedia.org/wiki/%E5%85%83%E6%95%B0)为2)。 推荐使用可选的`:only`选项，这样不会造成引入命名空间下给定模块的所有函数。 `:except`选项和`：only`用途恰恰相反，引入给定模块下*除*函数之外的所有函数。

`import`也支持`:macros`和`:functions` 作为`:only`的选项。 例如，引入所有的宏，可以这样写:

```elixir
import Integer, only: :macros
```

或者引入所有的函数:

```elixir
import Integer, only: :functions
```

注意`import`也是**词法作用域的**，意味着可以在函数定义中引入宏或者函数。

```elixir
defmodule Math do
  def some_function do
    import List, only: [duplicate: 2]
    duplicate(:ok, 10)
  end
end
```

上面的例子中，被引入的`List.duplicate/2`函数只在some_function函数中有效。`duplicate/2`在该模块中的其他函数中无效，其他模块中也无效

注意`import`编译指令引入一个模块时会自动的`require`这个模块。


### <a id="use"></a> use

尽管不是一个编译指令，宏指令`use`紧密的和`require`编译指令关联，它可以允许你在当前上下文中文使用某个模块。 开发者经常使用`use`将一个外部功能(通常为模块)带入当前词法作用域。

例如，当时需要使用ExUnit框架来写测试的时候，我们需要使用(use)`ExUnit.Case`模块:

```elixir
defmodule AssertionTest do
  use ExUnit.Case, async: true

  test "always pass" do
    assert true
  end
end
```

`use`需要(requires)给定模块后，会触发`__using__/1`回调，允许给定模块注入代码到当前上下文中。一般情况下，下面的模块:

```elixir
defmodule Example do
  use Feature, option: :value
end
```

会被编译为

```elixir
defmodule Example do
  require Feature
  Feature.__using__(option: :value)
end
```

到此，Elixir模块部分快要结束了。最后一部分是讲模块属性(attributes)。


### <a id="understanding-aliases"></a> 理解别名

此时你应该有疑问: Elixir别名到底是什么，它是怎么体现出来的？

一个Elixir别名，其实就是一个大写的标识符(identifier)，编译的时候它会被转换为一个原子值，例如`String`, `Keyword`, 等)。例如，`String`别名会被默认转换为`:"Elixir.String"`原子值:

```iex
iex> is_atom(String)
true
iex> to_string(String)
"Elixir.String"
iex> :"Elixir.String" == String
true
```

`alias/2`编译指令允许我们改变别名扩展后的原子值。


在Erlang <abbr title=“虚拟机”>VM</abbr>中，模块以原子的类型表示，这也是为什么在Elixir中，别名扩展为原子类型。 例如，下面举例说明在Elixir中调用Erlang的模块:

```iex
iex> :lists.flatten([1, [2], 3])
[1, 2, 3]
```

也是这个原理，我们可以动态的调用任意模块中的函数:

```iex
iex> mod = :lists
:lists
iex> mod.flatten([1, [2], 3])
[1, 2, 3]
```

上面的例子说明，我们可以在原子`:lists`上调用`flatten`函数。

### <a id="nested-modules"></a> 模块嵌套

我们已经学习了别名的使用，现在我们了解一下Elixir中模块嵌套和使用方法。参考下面的例子:

```elixir
defmodule Foo do
  defmodule Bar do
  end
end
```

上面的例子定义了两个模块：`Foo` 和 `Foo.Bar`。 在同一词法作用域内，第二个模块可以在`Foo`模块内部被直接作为`Bar`模块访问。上面的例子和接下来的代码一样:

```elixir
defmodule Elixir.Foo do
  defmodule Elixir.Foo.Bar do
  end
  alias Elixir.Foo.Bar, as: Bar
end
```

如果`Bar`模块被移出`Foo`模块，我们只能使用它完整的模块名称`Foo.Bar`或者一个使用`alias`编译指令指定的别名。

**注意**：在Elixir中，我们没有必要在定义`Foo.Bar`模块前预先定义`Foo`，因为最后所有的模块名都会被Elixir转换为原子类型值。 因此，定义任何嵌套的模块，你可以不用预先定义某个模块的上一层模块(例如: 定义`Foo.Bar.Baz`我们不用先定义`Foo`或者`Foo.Bar`)。

在随后的章节中，别名在宏指令中扮演着重要的角色，因为别名可以保证[宏的健全性](https://en.wikipedia.org/wiki/Hygienic_macro)。

### <a id=“multi”></a> 同一个 alias/import/require/use 定义中的多个嵌套模块名

从Elixir v1.2开始，我们可以别名(alias)/引入(import)/需要(require)某个模块多次。在编写Elixir代码时，别名/引入/需要某个模块多次可以让我们便捷的操作嵌套模块。例如，假设你的app中所有的模块都嵌套于`MyApp`中，你可以只别名`MyApp.Foo`, `MyApp.Bar`和`MyApp.Baz`一次：

```elixir
alias MyApp.{Foo, Bar, Baz}
```


