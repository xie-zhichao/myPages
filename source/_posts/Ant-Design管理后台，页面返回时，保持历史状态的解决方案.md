---
title: Ant Design管理后台，页面返回时，保持历史状态的解决方案
date: 2019-05-15 18:41:12
tags:
  - JavaScript
  - React
  - Ant Design
categories: JavaScript
---
## 背景
我们有一个管理后台，使用的Ant Design pro2框架。

## 需求
管理后台大部分页面都是分页查询列表 + 详情的结构，常见的业务场景是：添加查询条件，翻页，找到业务数据，点击查看详情，处理完成后返回列表页。

这个时候，为了使用便利，用户希望能保持之前的查询参数和分页参数，Ant Design Pro没有提供这个功能。

## 设计思路
分析一下，有两种方式：
- Redux存储查询参数和分页参数，返回时，读取之前的state数据，作为初始化查参数。
- History state，查询参数和分页参数都通过pushState放在Url里面，返回时，读取Url里面的参数，做出初始化查询参数

对比，选择History state，基于一下考虑：
- 写在Url里面更直观，更符合Web的Url和资源对的理念
- 页面强制刷新时，也可以保持参数
 
## 示例
- 首先，在渲染查询表单时，用Url里面的查询参数初始化表单的值
```javascript
// 根据query参数，初始化表单值
  const querying = Query.parse();
  ...
  <FormItem label="商户ID">
      {getFieldDecorator('id', {
        rules: [
          {
            validator: textMaxLength(20),
          },
        ],
        initialValue: querying.id // 用Url里面的查询参数初始化表单的值
      })(<Input maxLength="20" placeholder="输入商户ID" />)}
    </FormItem>
```

- 其次，search方法里面, 查询之前要先读取Url里面的查询参数，查询后，更新表单查询参数：
```javascript
  // 读取url里面的查询参数
  const querying = Query.parse();
  const { pageNumber = 1, pageSize = 20 } = querying;
  ...
  // 更新url查询参数
  Query.appendQuery(values);
```

- 最后，在分页列表渲染方法里面，更新分页参数
```javascript
    // 给页面Url添加分页参数，保持分页状态
    const { pageNumber, pageSize } = data;
    pageNumber && Query.appendQuery({
      pageNumber,
      pageSize
    });
```

## 注意事项
- 初始化表单时，有些控件的初始值需要转换，比如日期选择器
- 页面上有多个分页列表时，参数名要区分一下