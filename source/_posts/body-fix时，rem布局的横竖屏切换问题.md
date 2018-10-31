---
title: 使用body fix实现禁止蒙层底部页面跟随滚动时，rem布局的横竖屏切换在safari下的问题
date: 2018-10-26 09:13:41
tags: 
  - CSS
  - body
  - fixed
  - 蒙层
  - rem
  - 横竖屏
categories: CSS
---
## 场景：禁止蒙层底部页面跟随滚动
目前比较常用的有三种方案：
- overflow hidden，这个方案是pc端的不二选择，简单有效。移动端，效果不好或直接无效。
- 滑动事件，阻止默认行为，移动端的一般选择。
**不过碰到弹层也有滚动条时，这种方案就行不通了**
- 移动端，碰到第二种方案里面的特例时，我们就要考虑其他方案了：body fix，代码如下：
```javascript
componentDidMount() {
  const rootEl = document.documentElement;
  const bodyEl = document.body;
  this.position = window.getComputedStyle(bodyEl).position;
  this.scrollTop = rootEl.scrollTop || bodyEl.scrollTop;
  bodyEl.style.position = 'fixed';
  bodyEl.style.top = -this.scrollTop + 'px';
}
componentWillUnmount() {
  document.body.style.position = this.position;
  document.body.style.top = '';
  window.scrollTo(0, this.scrollTop);
}
```
## 场景：Rem响应式布局
在移动端，需要使用响应式布局，rem布局是个很不错的方案。样例如下：
```css
html { font-size: 62.5%; } /* font-size: 62.5% now means that 1.0 rem = 10px */
.container { background: #fff; font-family: arial; font-size: 1.4rem; line-height: 1.6rem; }
```
响应式实现：在页面**初始化**和**resize**时，按屏幕尺寸计算并设置html元素的font size。
```javascript
function setRem() {
  //...
}
setRem();
window.onresize = function() {
  setRem();
}
```
## safari下的问题
 在上述两个方案一起使用时，safari下出现一个奇怪的问题：
 **打开蒙层，再做横竖屏切换时，rem动态设置无效**
 >暂时还没找到原因和解决方案（如有同学找到原因或方案，欢迎指教）。