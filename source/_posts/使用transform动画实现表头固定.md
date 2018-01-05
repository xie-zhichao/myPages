---
title: 使用transform动画实现表头固定
date: 2018-01-05 13:15:07
tags:
---

## 需求场景
  用Excel做数据表格时，我们常常用到一个功能：冻结。冻结可以实现表头固定，这样，下拉查看数据时，依然可以对应上表头的标题。
Web页面的Table数据表，同样会有类似的需求，目前解决方案有几种：
  ### 双层table
  比较常见的是：使用两个table，上面一个table只做表头，下面一个table做数据展示。两个table的列宽需要匹配，包括滚动条的列宽，常见的是用<colgroup>来处理（用div也是可行的，样式处理复杂一点），最后一列是滚动条宽度。
. table1
    ``` html
    <colgroup>
        <col width="50">
        <col width="100">
        <col width="150">
        <col width="17">
    </colgroup> 
    ```	

. table2
    ``` html
    <colgroup>
        <col width="50">
        <col width="100">
        <col width="150">
        <col width="17">
    </colgroup> 
    ```
	
  这方式的缺点是：
    - 宽度必须固定，动态适应的话，需用js去处理
    - 历史代码改造工作量会比较大
  
  ### transform动画
  这里介绍一个更为灵活一点的实现方案：transform动画，监控滚动事件，根据滚动距离，移动表头。
  参考项目：https://github.com/KenyeeC/eleFixed
 
  ``` javascript
    trigger.addEventListener('scroll', function(event){
        var event_ = event || window.event;
        var evtTarget = event_.target || event_.srcElement;
        
        var offsetTop = evtTarget.scrollTop;
        var translate;
        if(offsetTop > offsetTop_){
            translate = 'translate(0px, '+(offsetTop - offsetTop_)+'px)';
        } else {
            translate = 'translate(0px, 0px)';                    
        }
        var translateCss = {
            '-webkit-transform': translate,
            '-moz-transform': translate,
            '-ms-transform': translate,
            '-o-transform': translate
        };
        timerHandler && clearTimeout(timerHandler);
        timerHandler = setTimeout(function() {
            $(target).find('tr th').css(translateCss);
        }, 100);
    });
  ```
  这里有几点需要关注：
    - transform兼容性，兼容chrome，firefox比较简单，带前缀。ie给<thead>设置动画时无效，必须需要给<th>设置
    - firefox，ie会有闪烁，可以设置一个时延