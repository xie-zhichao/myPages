---
title: 'PostCSS: 构建css更简单'
date: 2018-05-29 13:52:42
tags:
 - CSS
 - PostCSS
categories: CSS
---
## PostCSS介绍
> PostCSS is a tool for transforming CSS with JS plugins. These plugins can support variables and mixins, transpile future CSS syntax, inline images, and more.

这是官方的介绍，翻译一下就是：PostCSS是一个用JS插件实现的CSS转换工具。这些插件支持变量，混合，CSS语法转换，内联图片及更多功能。

## 使用场景
介绍一个简单又很有用的场景：浏览器兼容。这个是每个前端都头疼的问题，有了PostCSS插件，你就可以免除考虑大量兼容性的问题（虽说这也是前端开发的基本功之一，但对项目来说，确实是省事的，更工程化）。样例如下：

- 通过gulp加载插件

``` javascript
var autoprefixer = require('autoprefixer');
var color_rgba_fallback = require('postcss-color-rgba-fallback');
var opacity = require('postcss-opacity');
var pseudoelements = require('postcss-pseudoelements');
var vmin = require('postcss-vmin');
var pixrem = require('pixrem');
var will_change = require('postcss-will-change');
```

处理之前(autoprefixer)：

``` css
@keyframes animationExample {
    from {
        width: 0;
    }
    to {
        width: 100%;
    }
}
 
.animateThis {
    animation: animationExample 2s;
    display: flex;
}
```

处理之后：

``` css
@-webkit-keyframes animationExample {
    from {
        width: 0;
    }
    to {
        width: 100%;
    }
}
 
@keyframes animationExample {
    from {
        width: 0;
    }
    to {
        width: 100%;
    }
}
 
.animateThis {
    -webkit-animation: animationExample 2s;
            animation: animationExample 2s;
    display: -webkit-box;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
}
```

- 参考：[Using PostCSS for Cross Browser Compatibility](https://webdesign.tutsplus.com/tutorials/using-postcss-for-cross-browser-compatibility--cms-24567)

