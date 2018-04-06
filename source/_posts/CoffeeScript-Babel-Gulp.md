---
title: CoffeeScript+Babel+Gulp
date: 2018-02-15 19:32:37
tags:
  - CoffeeScript
  - Babel
  - Gulp
categories: CoffeeScript
---
## 为什么是这个组合
如果你喜欢CoffeeScript，那么CoffeeScript+Babel+Gulp的组合可以提供一个CoffeeScript的开发环境，支持ES6的语法标准，使用node命令编译打包。
## 相关技术介绍
### CoffeeScript
CoffeeScript是一套JavaScript的转译语言，提供了许多借鉴于Ruby的语法糖，好处可以总结几点：
- 提供语法糖，代码结构更简洁、优雅，语法更严格，代码量更少
- 转译时运用了最佳实践，转译出来的代码在效率上较优
### Babel
JavaScript的语言标准，已经更新到ES6了，语法上有不少的改进，有些改进还是借鉴于CoffeeScript。为了兼容旧标准，Babel可以提供一个运行环境，让我们可以运行ES6标准的js代码。CoffeeScript最近也更了新版本到2.0版，添加了部分ES6的支持。
### Gulp
Gulp是node环境下的一个流式构建工具，为JavaScript工程提供了工程化构建解决方案。

## CoffeeScript+Babel+Gulp组合的相关配置
**package.json**
``` javascript
{
  "devDependencies": {
    "babel-core": "^6.26.0",
    "babel-preset-env": "^1.6.1",
    "gulp": "^3.9.1",
    "gulp-babel": "^7.0.0",
    "gulp-coffee": "^3.0.2",
    "gulp-filter": "^5.1.0",
    "gulp-sourcemaps": "^2.6.4"
  }, "scripts": {
    "build": "npm install && gulp build"
  }
}
```
**.babelrc**
``` javascript
{  
    "presets": [  
        ["env", { "modules": false }] 
    ],  
    "plugins": [],
    "ignore": []  
}
```
**gulpfile.js**
``` javascript
const gulp = require('gulp');
const coffee = require('gulp-coffee');
const sourcemaps = require('gulp-sourcemaps');
const filter = require('gulp-filter');
const babel = require("gulp-babel");

gulp.task('build', function() {
  const f = filter(['*.js']);
  gulp.src('./src/*.coffee')
    .pipe(sourcemaps.init())
    .pipe(coffee())
    .pipe(babel())
    .pipe(sourcemaps.write('./maps'))
    .pipe(gulp.dest('./lib/'));
});
```
> 打包： npm run build
