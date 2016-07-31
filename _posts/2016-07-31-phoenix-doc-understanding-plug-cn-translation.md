---
layout: post
title: "[文档翻译] Phoenix框架学习: Understanding Plug 插头"
description: "中文翻译"
category:
tags: elixir phoenixframework chinese-translation
---
{% include JB/setup %}

## Plug 插头

原文: [http://www.phoenixframework.org/docs/understanding-plug](http://www.phoenixframework.org/docs/understanding-plug)

Plug在Phoenix框架中HTTP层里，是一个很重要的概念，它处于靠前(例如处理HTTP请求)和靠中的位置(例如Controller中预处理)。 一个HTTP连接的生命周期中的每一步，我们都会用到Plug， 一些Phoenix框架中的组建，例如Endpoints, Routers, 和Controllers，他们内部其实都是一个Plug。 继续往下看，一探究竟。

对于web app来说， [Plug](https://github.com/elixir-lang/plug)是可组合模块的一个规范。 同时，它给不同web服务器的连接适配器提供了一个抽象层接口。 可以简单把Plug理解成它将我们使用的“连接”(connection)组装在一起。这和别的HTTP中间件(例如Rack)不同，Plug的请求和响应在中间件层是完全独立分开的。

### Plug 规范
简单来说，Plug规范有两种, 函数插头(function plugs)和模块插头(module plugs)。

#### Function Plugs 函数插头

为了把一个函数变成一个插头， 这个函数只需要简单的接受两个参数: 连接结构(connection struct `%Plug.Conn{}`)和其他选项。返回值为这个连接结构。任意函数符合这个要求，即为一个函数插头。来看一个例子:

```elixir
def put_headers(conn, key_values) do
  Enum.reduce key_values, conn, fn {k, v}, conn ->
    Plug.Conn.put_resp_header(conn, to_string(k), v)
  end
end
```
很简单，对吧。


下面的代码示例展示在一个Phoenix连接中，如何使用多个插头完成一系列的变换。
```elixir
defmodule HelloPhoenix.MessageController do
  use HelloPhoenix.Web, :controller

  plug :put_headers, %{content_encoding: "gzip", cache_control: "max-age=3600"}
  plug :put_layout, "bare.html"

  ...
end
```

依从插头的规范，`put_headers/2`, `put_layout/2`, 和 `action/2`将app的请求转化为一系列的显示变换。这些变换还没有结束。现在我们模拟一个场景，来感受一下插头的设计所带来的高效：当我们需要检查多重条件，然后，如果条件失败，则跳转或者终止。在没有插头的情况下，我们可能会这样写：
```elixir
defmodule HelloPhoenix.MessageController do
  use HelloPhoenix.Web, :controller

  def show(conn, params) do
    case authenticate(conn) do
      {:ok, user} ->
        case find_message(params["id"]) do
          nil ->
            conn |> put_flash(:info, "That message wasn't found") |> redirect(to: "/")
          message ->
            case authorize_message(conn, params["id"]) do
              :ok ->
                render conn, :show, page: find_message(params["id"])
              :error ->
                conn |> put_flash(:info, "You can't access that page") |> redirect(to: "/")
            end
        end
      :error ->
        conn |> put_flash(:info, "You must be logged in") |> redirect(to: "/")
    end
  end
end
```

几步验证和授权需要这么复杂的内嵌和代码重复？下面我们来优化一下，仅用几个插头:
```elixir
defmodule HelloPhoenix.MessageController do
  use HelloPhoenix.Web, :controller

  plug :authenticate
  plug :find_message
  plug :authorize_message

  def show(conn, params) do
    render conn, :show, page: find_message(params["id"])
  end

  defp authenticate(conn, _) do
    case Authenticator.find_user(conn) do
      {:ok, user} ->
        assign(conn, :user, user)
      :error ->
        conn |> put_flash(:info, "You must be logged in") |> redirect(to: "/") |> halt
    end
  end

  defp find_message(conn, _) do
    case find_message(conn.params["id"]) do
      nil ->
        conn |> put_flash(:info, "That message wasn't found") |> redirect(to: "/") |> halt
      message ->
        assign(conn, :message, message)
    end
  end

  defp authorize_message(conn, _) do
    if Authorizer.can_access?(conn.assigns[:user], conn.assigns[:message]) do
      conn
    else
      conn |> put_flash(:info, "You can't access that page") |> redirect(to: "/") |> halt
    end
  end
end
```

使用扁平的一系列插头转换，可以替换掉例子中的内嵌代码块。同样的功能，插头可以让代码更具有组合性，更简洁，更具有复用性。

下面来看另一种插头: 模块插头。

#### Module Plugs 模块插头

模块插头允许我们在模块内定义一个连接转换(connection transformation)。定义一个模块插头，只需要实现两个函数：
- 函数`init/1`初始化传递给`call/2`的参数(arguments)或者选项(options)
- 函数`call/2`执行连接转换(connection transformation)。这里的`call/2`其实是一个前文中的函数插头。

为了理解这个功能，我们下面来实现一个模块插头。这个模块插头在连接赋值(connection assign)中设置`:local`键值对，以便后续的其他插头，controller action或者views中使用。

```elixir
# lib/hello_phoenix/plugs/locale.ex
defmodule HelloPhoenix.Plugs.Locale do
  import Plug.Conn

  @locales ["en", "fr", "de"]

  def init(default), do: default

  def call(%Plug.Conn{params: %{"locale" => loc}} = conn, _default) when loc in @locales do
    assign(conn, :locale, loc)
  end
  def call(conn, default), do: assign(conn, :locale, default)
end

defmodule HelloPhoenix.Router do
  use HelloPhoenix.Web, :router

  pipeline :browser do
    plug :accepts, ["html"]
    plug :fetch_session
    plug :fetch_flash
    plug :protect_from_forgery
    plug :put_secure_browser_headers
    plug HelloPhoenix.Plugs.Locale, "en"
  end
  ...
```

我们可以简单在浏览器管道(router.ex中的browser pipeline)中使用这个模块插头 `plug HelloPhoenix.Plugs.Locale, "en”`。 在`init/1`回调中，如果locale在params中不存在，我们传递一个默认的locale。 利用模式匹配，我们定义了两个`call/2`函数，验证locale是否在params中，如果匹配失败，则返回”en”。

Plug插头的部分就结束了。Phoenix框架到处可见可组合的转换的插头设计。不过这里只是开始。实际编码中，我们随时可以自问: ”这里可以使用插头么？“，大部分时候答案是肯定的。