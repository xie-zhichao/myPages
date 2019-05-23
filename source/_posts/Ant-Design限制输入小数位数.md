---
title: '# Ant Design限制输入小数位数'
date: 2019-05-23 14:06:04
tags:
  - JavaScript
  - React
  - Ant Design
categories: JavaScript
---
# Ant Design限制输入小数位数

## 背景和需求
使用Ant Design组件的管理后台，有一个表单金额输入，使用InputNumber组件，需要限制输入的小数位数。比如，只能输入2位小数，不能多输。

## 设计
通过分析Ant Design组件文档，有两个配置项候选：
- onChange，表单值改变时调用，考虑每次改变时，判断新输入的值是否符合要求，如果不符合，重置为原来的值
- parser，表单值改变时调用，区别是，这个方法会把输入的值格式化，然后设置为组件的值，不需要手动重置值

## 实现
实现时发现，onChange方案不行，在onChange里面重置表单值无效，应该是被本身的赋值覆盖掉了（为了验证这个猜测，我加了一个timeout，延迟执行，就可以了，但是体验不太好，会闪一下）。

所以最终，使用了parser方案。

JavaScript
```javascript
/**
 * ant pro inputNumber组件，金额限制小数两位
 * @param {ant form} form 
 * @param {string} fieldName 
 * @param {number | string} value 
 * @param {number} num default:2 
 */
function amountFixed(form, fieldName, value, num = 2) {
  const valueStr = `${value || ''}`;
  const valueSplit = valueStr.split('.');
  
  if (valueSplit.length > 1 && (valueSplit[1].length > num || valueSplit.length > 2)) {
    return form.getFieldValue(fieldName);;
  } 
  return value;
}
```
Jsx，这里用的是parser
```javascript
<InputNumber
    min={0}
    precision={2}
    parser={value => amountFixed(form, 'list[1].amount', value)}
    style={{ width: '100%' }}
    placeholder="请输入金额"
  />
```