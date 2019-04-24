---
title: 使用Table组件时，报should have a unique key
date: 2019-04-24 20:22:21
tags:
 - JavaScript
 - Ant Design
categories: JavaScript
---
## 现象
使用StandardTable组件时，设置了rowKey，但还是报should have a unique key。

## 分析
- 排查rowKey设置，没发现问题
- debug StandardTable组件，查看prop设置，没发现问题
- 再次分析console报错，发现第一个报错时ColGroup报出的，再次断点，发现里面设置的columns，第一个元素没有key值，其他都有。
- 对比其他页面，发现Table的columns设置，都是只有最后的元素没设置dataIndex。而报错的页面，第一个元素没有设置dataIndex

## 解决
给第一个column元素设置上dataIndex，问题解决。