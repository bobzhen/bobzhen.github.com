---
layout: post
title: "[博客翻译] Phoenix v1.1.2和React.js"
description: "中文翻译"
category:
tags: phoenix react npm chinese-translation
---
{% include JB/setup %}

## Phoenix v1.1.2和React.js

原文链接: [Phoenix v1.1.2 and React.js
](https://medium.com/@diamondgfx/phoenix-v1-1-2-and-react-js-3dbd195a880a#.nadyhxiyx)


<figure>
    🎉这个logo有点像巧克力和花生黄油。
    <img src="https://cdn-images-1.medium.com/max/1600/1*qN1NFtAvST7tkBx5gDfmDw.png" style="max-width: 500px;margin: auto;"/>
</figure>


### 简介

在史前时代，给Phoenix项目中添加React.js是个很繁琐的工序，卸掉默认的brunch，使用Webpack，直到Phoenix v1.1.2使用兼容npm的新版brunch。下面我们一起来看看如何新建一个Phoenix v1.1.2项目，然后使用基本的React组件渲染主页。

当前版本

Phoenix: v.1.1.4

React: v0.14.6

ReactDOM: v0.14.6

NPM: 3.6.0 (一定要使用NPM3.x版本，2.x版本不兼容上面给出的Phoenix+React版本，[看这里](https://github.com/phoenixframework/phoenix/issues/1410))

NodeJS: v5.6.0

Brunch: v2.1.x, v2.2.x, v2.3.2+ (v2.3.0版本会崩溃，错误信息为"NODE_ENV"未定义，所以确定使用列出的版本), v2.4.x

### 创建Phoenix项目

首先得创建版本为v1.1.2+的Phoenix项目:

```bash
$ mix local.hex

$ mix archive.install https://github.com/phoenixframework/archives/raw/master/phoenix_new.ez
```

第二条命令会检测之前的Phoenix版本，如果有，会提示是否需要覆写。一路输入Y。接着，新建一个Phoenix应用:

```bash
$ mix phoenix.new reactpx
```

继续输入Y允许安装和获取依赖包，会在这个项目中用到它们。接着，进入项目文件夹，安装npm依赖:

```bash
$ cd reactpx
```

### 安装和配置NPM依赖

接下来安装react，react-dom和babel-preset-react依赖，React中，JSX模版需要babel-preset-react这个依赖。

```bash
$ npm install --save react react-dom babel-preset-react
``

安装结束后，我们需要修改Phoenix项目中root目录下的brunch-config.js文件，这样做的目的是为了让Phoenix项目使用刚刚安装的新模块。首先找到plugins对象的位置，在plugins里找到子对象babel。

```javascript
plugins: {
  babel: {
    presets: ["es2015", "react"],
    // Do not use ES6 compiler in vendor code
    ignore: [/web\/static\/vendor/]
  }
},
```

这里OK后，我们还需要在npm配置中添加已安装的npm模块白名单，让brunch知道"react"和"react-dom"将被使用:

```javascript
npm: {
    enabled: true,
    // Whitelist the npm deps to be pulled in as front-end assets.
    // All other deps in package.json will be excluded from the bundle.
    whitelist: ["phoenix", "phoenix_html", "react", "react-dom"]
}
```

接下来运行brunch构建命令，保证所有的js依赖都ok。(从技术上的角度，第一次启动Phoenix服务器的时候，brunch构建会被触发，但是如果服务器已经启动，构建则不会在每次修改后自动触发。):

```bash
$ brunch build
```

### 关于NPM版本的提示

这篇教程假设你使用NPM 3.x。如果是2.x，构建的时候会有问题，很多人都遇到了这个问题。请参考这个Phoenix Github [Issue](https://github.com/phoenixframework/phoenix/issues/1410#issuecomment-166001866)。在此特别感谢[Matt Widmann](https://medium.com/u/9abc4f3b3a89)和[Fabio Akita](https://medium.com/u/9a78859fae43)指出这个版本问题。

### 来一个组件例子

下面我们来设置Phoenix应用，在页面上添加一个简单的Hello World React组件。
打开web/templates/page/index.html.eex，删除所有的代码，添加下面这段代码:

```html
<div id="hello-world"></div>
```

接着在web/static/js/app.js文件的末尾添加下面React代码:

```javascript
import React from "react"
import ReactDOM from "react-dom"

class HelloWorld extends React.Component {
  render() {
    return (<h1>Hello World!</h1>)
  }
}

ReactDOM.render(
  <HelloWorld/>,
  document.getElementById("hello-world")
)
```

### Yo, 搞定!

启动服务器

```bash
$ iex -S mix phoenix.server
```

(译者注: 上面的为交互debug模式，可以简单的使用mix phoenix.server启动服务器)

打开http://localhost:4000，你会发现[这个](https://cdn-images-1.medium.com/max/1600/1*l76j9FFIBqyZBi2T2MIQcg.png)。

啊哈，简单的配置之后，现在你可以完美的在Phoenix应用中使用React了。源代码在此: [https://github.com/Diamond/reactpx](https://github.com/Diamond/reactpx)

原文作者简介:
[Brandon Richey](https://medium.com/@diamondgfx): 来自美国纽约的软件工程师，Elixir和Ruby程序员，偶尔凭兴趣做做游戏开发。

全文完。
翻译不妥之处，请见谅，如果有空来函指正，将感激不尽！

